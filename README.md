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

# yml linux notes

## For pure binary builds

Ubuntu 20.04 only has python 3.8 wheels at the moment, 
for some reason py3.9 wheels are missing.

So might have to drop back to 3.8 for everything, unless can change python version
somehow depending on os? Thus this would allow 18.04 to have py3.9

## As for snaps

`core18` is py36 and it is hard to upgrade python - have a pending
question in forums. 

If we build using core20 then that might be easier and would give us py38
by default.
Alternatively, possibly a pyenv based solution could be achieved?

Note re linux installs

     pip install -U -f https://extras.wxpython.org/wxPython4/extras/linux/gtk3/ubuntu-20.04/wxPython
     pip install -U -f https://extras.wxpython.org/wxPython4/extras/linux/gtk3/ubuntu-18.04 wxPython

# Github Actions ENV vars

You can set environment variables using an `env:` entry, at a job or step level.

https://github.com/actions/starter-workflows/issues/68

> The pain is that when you set variables in a step in your workflow, further steps can NOT see the VAR 🙊.

> Each step runs in its own process in the virtual environment and has access to the workspace and filesystem. Because steps run in their own process, changes to environment variables are not preserved between steps.

> Officially you have to re-set VAR at each `step`. This sucks.

Then GitHub Actions finally allowed [job-wide](https://github.blog/changelog/2019-10-01-github-actions-new-workflow-syntax-features/#env-at-the-workflow-and-job-level) level environment variables (note, not workflow wide - just job wide). E.g.

## Example of setting job level ENV vars

```yml
jobs:
  buildfun:
    runs-on: ubuntu-18.04

    # job level env 
    env:
      MY_ENV_1: My environment variable 1
      MY_ENV_2: My environment variable 2

    steps:
    - name: Report env
      run: |
        echo $MY_ENV_1, $MY_ENV_2, ${{ env.MY_ENV_1 }}       
```

Note the env vars are visible at each step level.

## Conditional setting of env variables

Sometimes we want to set an env var conditionally, or at a step level, and let other steps see it.

Unfortunately job level env vars can't be done conditionally, because the `env:` syntax does not allow any conditonality syntax. E.g. Neither

```yml
if: ${{ github.ref == 'refs/heads/main' }}
```

nor the more bash oriented if used in `run`

```
run: |
  if [ "$RUNNER_OS" == "Linux" ]; then
  fi
```

will work inside an `env:`.

Whilst `if:` conditionality syntax works inside a jobs section (running the job under a condition) that turns the whole job on or off, which is too coarse grained since we usually want to define an intelligent single job and keep things DRY.

Luckily, since `if:` conditionality syntax works for steps (running the step under a condition), we can conditionally set job wide environment variables inside steps that are first in the array of steps in a job. But we need to use specific syntax that sets the environment variable permanently, because normally env vars are lost after each step completes.

## Techniques for setting job wide ENV vars inside steps

This [technique](https://github.com/actions/toolkit/issues/641) seems the simplest, and works OK

```yml
steps:
- name: Set env var 3.9 using technique 2
  run: echo "PYTHON_VERSION=3.7" >> $GITHUB_ENV

- name: Report env using technique 2
  run: echo "PYTHON_VERSION is ${{ env.PYTHON_VERSION }} aka $PYTHON_VERSION"
```

This is [another technique](https://github.com/Actions-R-Us/gh-actions-samples/blob/ffac4047d7d97409180efad5c503b4afb6c2a289/.github/workflows/step_param_from_env.yml#L21) but I have not tried it

```yml
- name: Export Script Parameter
  run: |
       echo "::set-env name=MY_ACTION_SCRIPT::.github/step_param_from_env.sh"
  shell: bash
```

