# wxpython-actions

Test github actions and python 

options to pyinstaller
- `-F` is to create one file
- `--windowed` is to create a real mac bundle
- `--noconfirm` is to say yes to delete the existing files

    pyinstaller --windowed -F --noconfirm rubber_band_async.py

Without `--windowed`, you won't get an app bundle you can run. It will complain about needing a framework build of Python.  

> You *can* have a venv pointing to a `pyenv` Python framework install, and build a proper wxpython exe. Pyinstaller running out of the venv will nuild it ok.

https://www.blog.pythonlibrary.org/2019/03/19/distributing-a-wxpython-application/

