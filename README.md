# wxpython-actions

Test github actions and python 

options to pyinstaller
- `-F` is to create one file
- `--windowed` is to create a real mac bundle
- `--noconfirm` is to say yes to delete the existing files
- `-i FILE.icns`  specify an icon
  
Example:

    pyinstaller --windowed -F --noconfirm rubber_band_async.py

See also `bin/build` script.

## Mac

Without `--windowed`, you won't get an app bundle you can run. It will complain about needing a framework build of Python.  

> You *can* have a venv pointing to a `pyenv` Python framework install, and build a proper wxpython exe. 🎉  viz. `Pyinstaller` running out of the venv will build it just fine.  Proof is https://github.com/abulka/wxpython-actions/actions/runs/997316399 

[Doco](https://www.blog.pythonlibrary.org/2019/03/19/distributing-a-wxpython-application/) on Pyinstaller.


# github-actions-run-step-on-specific-os

https://stackoverflow.com/questions/57946173/github-actions-run-step-on-specific-os

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

## Spec files

Spec files are auto generated, but once generated, you can tweak them.  Looks like there is no option to pass `info.plist` via command line, so you *have* to resort to running from a spec file

    pyinstaller rubber_band_async_custom.spec

