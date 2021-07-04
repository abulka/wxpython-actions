# wxpython-actions and pyinstaller

Test of github actions building wxPython python apps using Pyinstaller.

## Pyinstaller

Options to pyinstaller

- `-F` is to create one file
- `--windowed` is to create a real mac bundle
- `--noconfirm` is to say yes to delete the existing files
- `-i FILE.icns`  specify an icon
  
Example:

    pyinstaller --windowed -F --noconfirm rubber_band_async.py

See also `bin/build` script.

See [doco](https://www.blog.pythonlibrary.org/2019/03/19/distributing-a-wxpython-application/) on Pyinstaller.

## Pyinstaller building wxPython apps via virtual environments

You *can* use a virtual environment to build a wxPython app - as long as it points to a framework install. For example a `venv` pointing to a `pyenv` Python framework install will work and build a proper wxpython exe. 🎉 

`Pyinstaller` can also running out of the `venv`.  Proof of a successful build on Github Actions is https://github.com/abulka/wxpython-actions/actions/runs/997316399 

## Pyinstaller Spec files

Spec files are auto generated, but once generated, you can tweak them.  Looks like there is no option to pass `info.plist` via command line, so you *have* to resort to running from a spec file

    pyinstaller rubber_band_async_custom.spec

## Pyinstaller - building a Mac app

Without `--windowed`, you won't get an app bundle you can run. It will complain about needing a framework build of Python, even though you have one.  

# Github actions

## run step on specific os

https://stackoverflow.com/questions/57946173/github-actions-run-step-on-specific-os

Example:

```yml
- name:  Install
  run:   |
         if [ "$RUNNER_OS" == "Linux" ]; then
              apt install important_linux_software
         elif [ "$RUNNER_OS" == "Windows" ]; then
              choco install important_windows_software
         else
              echo "$RUNNER_OS not supported"
              exit 1
         fi
  shell: bash
```

# Building a ubuntu snap app

Use my `.github/workflows/wxpython-snap.yml` based on https://github.com/marketplace/actions/snapcraft-action plugn for github actions which can build snaps. It works, though you have to use LXD - rather than multipass.

