---
layout: post
title: pyinstaller hiddenimports
---

`pyinstaller --hidden-import setuptools._distutils`

### Input
`pip show setuptools`
### output
```
~
Location: C;\users\<name>\appdata\local\programs\python\python310\lib\site-packages
```

manually add setuptools file.\
With the command above, setuptools dist version will be added\

TODO: create python setup file for auto configuration