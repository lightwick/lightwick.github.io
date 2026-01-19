---
layout: post
title: "ICD Parsing"
mathjax: true
---

### Parsing
```
// frame_parser.h
#pragma once

#include <QtCore>
#include <variant>
#include <unordered_map>
#include <functional>

// -------------------- CRC32 (IEEE 802.3) --------------------
class Crc32
{
public:
    static quint32 compute(QByteArrayView data)
    {
        initTableOnce();
        quint32 crc = 0xFFFFFFFFu;
        for (unsigned char b : data) {
            crc = (crc >> 8) ^ table[(crc ^ b) & 0xFFu];
        }
        return crc ^ 0xFFFFFFFFu;
    }

private:
    static void initTableOnce()
    {
        static std::once_flag flag;
        std::call_once(flag, [](){
            for (quint32 i = 0; i < 256; ++i) {
                quint32 c = i;
                for (int k = 0; k < 8; ++k) {
                    c = (c & 1u) ? (0xEDB88320u ^ (c >> 1)) : (c >> 1);
                }
                table[i] = c;
            }
        });
    }

    static inline quint32 table[256];
};

// -------------------- Helpers: safe big-endian reads --------------------
static inline bool readU8(QByteArrayView buf, qsizetype &off, quint8 &out)
{
    if (off + 1 > buf.size()) return false;
    out = static_cast<quint8>(buf[off]);
    off += 1;
    return true;
}

static inline bool readU16BE(QByteArrayView buf, qsizetype &off, quint16 &out)
{
    if (off + 2 > buf.size()) return false;
    out = (static_cast<quint16>(static_cast<quint8>(buf[off])) << 8) |
          (static_cast<quint16>(static_cast<quint8>(buf[off + 1])));
    off += 2;
    return true;
}

static inline bool readU32BE(QByteArrayView buf, qsizetype &off, quint32 &out)
{
    if (off + 4 > buf.size()) return false;
    out = (static_cast<quint32>(static_cast<quint8>(buf[off])) << 24) |
          (static_cast<quint32>(static_cast<quint8>(buf[off + 1])) << 16) |
          (static_cast<quint32>(static_cast<quint8>(buf[off + 2])) <<  8) |
          (static_cast<quint32>(static_cast<quint8>(buf[off + 3])));
    off += 4;
    return true;
}

// -------------------- Frame definitions --------------------
struct FrameHeader
{
    quint16 magic = 0;
    quint8  version = 0;
    quint16 msgCode = 0;
    quint8  src = 0;
    quint8  dst = 0;
    quint16 payloadLen = 0;
};

struct Frame
{
    FrameHeader header;
    QByteArrayView payload;  // view into original datagram (no copy)
};

// -------------------- Parse result --------------------
enum class ParseError
{
    TooShort,
    BadMagic,
    BadVersion,
    LengthMismatch,
    BadCrc
};

struct ParseResult
{
    bool ok = false;
    Frame frame{};
    ParseError error{};
    QString why;
};

// -------------------- Parser --------------------
class FrameParser
{
public:
    // Configure these to match your protocol
    static constexpr quint16 kMagic = 0xA55Au;
    static constexpr quint8  kVersion = 1;
    static constexpr qsizetype kHeaderSize = 2 + 1 + 2 + 1 + 1 + 2; // 9
    static constexpr qsizetype kTailSize   = 4; // crc32

    static ParseResult parse(QByteArrayView datagram)
    {
        ParseResult r;

        if (datagram.size() < kHeaderSize + kTailSize) {
            r.error = ParseError::TooShort;
            r.why = "Datagram too short";
            return r;
        }

        qsizetype off = 0;
        FrameHeader h;
        if (!readU16BE(datagram, off, h.magic) ||
            !readU8(datagram, off, h.version) ||
            !readU16BE(datagram, off, h.msgCode) ||
            !readU8(datagram, off, h.src) ||
            !readU8(datagram, off, h.dst) ||
            !readU16BE(datagram, off, h.payloadLen))
        {
            r.error = ParseError::TooShort;
            r.why = "Failed reading header";
            return r;
        }

        if (h.magic != kMagic) {
            r.error = ParseError::BadMagic;
            r.why = "Bad magic";
            return r;
        }

        if (h.version != kVersion) {
            r.error = ParseError::BadVersion;
            r.why = "Unsupported version";
            return r;
        }

        const qsizetype expectedSize = kHeaderSize + static_cast<qsizetype>(h.payloadLen) + kTailSize;
        if (datagram.size() != expectedSize) {
            r.error = ParseError::LengthMismatch;
            r.why = QString("Length mismatch: got %1, expected %2")
                        .arg(datagram.size()).arg(expectedSize);
            return r;
        }

        // Payload view
        const qsizetype payloadOff = kHeaderSize;
        r.frame.header = h;
        r.frame.payload = datagram.sliced(payloadOff, h.payloadLen);

        // Read crc from tail
        qsizetype crcOff = payloadOff + h.payloadLen;
        quint32 rxCrc = 0;
        if (!readU32BE(datagram, crcOff, rxCrc)) {
            r.error = ParseError::TooShort;
            r.why = "Failed reading CRC";
            return r;
        }

        // Compute CRC over header+payload (excluding crc field)
        const QByteArrayView crcRegion = datagram.sliced(0, kHeaderSize + h.payloadLen);
        const quint32 calc = Crc32::compute(crcRegion);

        if (calc != rxCrc) {
            r.error = ParseError::BadCrc;
            r.why = QString("CRC mismatch: rx=0x%1 calc=0x%2")
                        .arg(rxCrc, 8, 16, QLatin1Char('0'))
                        .arg(calc,  8, 16, QLatin1Char('0'));
            return r;
        }

        r.ok = true;
        return r;
    }
};

// -------------------- Example decoded messages --------------------
struct MsgA {
    quint16 x;
    quint16 y;
};

struct MsgB {
    float value;
    quint8 flags;
};

using Message = std::variant<MsgA, MsgB>;

// For float parsing safely:
static inline bool readFloatBE(QByteArrayView buf, qsizetype &off, float &out)
{
    quint32 u = 0;
    if (!readU32BE(buf, off, u)) return false;
    static_assert(sizeof(float) == sizeof(quint32));
    std::memcpy(&out, &u, sizeof(float));
    return true;
}

// -------------------- Decoder registry --------------------
class MessageDecoder
{
public:
    using DecodeFn = std::function<std::optional<Message>(const Frame&)>;

    MessageDecoder()
    {
        decoders_.emplace(0x0101, [](const Frame& f) -> std::optional<Message> {
            // MsgA expects payloadLen == 4
            if (f.header.payloadLen != 4) return std::nullopt;
            qsizetype off = 0;
            quint16 x=0, y=0;
            if (!readU16BE(f.payload, off, x) || !readU16BE(f.payload, off, y)) return std::nullopt;
            return MsgA{x,y};
        });

        decoders_.emplace(0x0202, [](const Frame& f) -> std::optional<Message> {
            // MsgB expects payloadLen == 5 (float + flags)
            if (f.header.payloadLen != 5) return std::nullopt;
            qsizetype off = 0;
            float value = 0.f;
            quint8 flags = 0;
            if (!readFloatBE(f.payload, off, value) || !readU8(f.payload, off, flags)) return std::nullopt;
            return MsgB{value, flags};
        });
    }

    std::optional<Message> decode(const Frame& frame) const
    {
        auto it = decoders_.find(frame.header.msgCode);
        if (it == decoders_.end()) return std::nullopt; // Unknown msg_code (drop or handle)
        return it->second(frame);
    }

private:
    std::unordered_map<quint16, DecodeFn> decoders_;
};
```

## Solution 2: Ground-up
```
// udp_receiver.cpp (snippet)
#include <QtNetwork>
#include "frame_parser.h"

class UdpReceiver : public QObject
{
    Q_OBJECT
public:
    explicit UdpReceiver(QObject* parent = nullptr)
        : QObject(parent)
    {
        connect(&sock_, &QUdpSocket::readyRead, this, &UdpReceiver::onReadyRead);
    }

    bool bind(quint16 port)
    {
        // ReuseAddressHint helps for some dev scenarios; in production you might skip it.
        return sock_.bind(QHostAddress::AnyIPv4, port, QUdpSocket::DefaultForPlatform);
    }

signals:
    void messageDecoded(const Message& msg);

private slots:
    void onReadyRead()
    {
        while (sock_.hasPendingDatagrams()) {
            QNetworkDatagram d = sock_.receiveDatagram();
            const QByteArray bytes = d.data(); // makes a copy; ok for most. Can optimize later.

            auto parsed = FrameParser::parse(QByteArrayView(bytes));
            if (!parsed.ok) {
                // Production: rate-limit logs
                // qWarning() << "Parse drop:" << parsed.why;
                continue;
            }

            auto msg = decoder_.decode(parsed.frame);
            if (!msg) {
                // Unknown msg_code or bad payload structure
                continue;
            }

            emit messageDecoded(*msg);
        }
    }

private:
    QUdpSocket sock_;
    MessageDecoder decoder_;
};
```
