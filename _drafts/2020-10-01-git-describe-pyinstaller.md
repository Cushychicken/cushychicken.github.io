---
bg: "pyinstaller-icon.jpg"
layout: post
title: "Getting git describe into a PyInstaller program"
summary: "haha version controlling snek program go brrr"
tags: ['programming']
date: 2020-10-01
---

I've written about [the joy of distributing scripts to less tech-savvy coworkers](http://cushychicken.github.io/python-guis-for-heretics/). Now that some of these simple GUIs have been out in the field for a few weeks, I'm getting a few reports of buggy or odd behavior from the end consumers. As a result, it's my turn to learn a hard programmer's lesson: it's *super* challenging to fix bigs without a corresponding version to test/repro against. After some putzing and poking online, I learned about `git describe`. Just the ticket for my versioning needs! And, even better, there's a great Python wrapper for it - I just have to `import git` in my Python scripts, and I can extract information from `git` seamlessly!

...or so I thought. 

The trouble, I've come to realize, is that PyInstaller and GitPython don't play well together. Just adding the line `import git` into a PyInstaller script causes your compiled Python .exe to fail with a pretty cryptic error ("Failed to execute script", and an OK button). This is [a known issue](https://github.com/pyinstaller/pyinstaller/issues/3509), but I don't see a clear line of sight to a fix (or even a diagnosis of the problem) in that GitHub issue.

So, yeah - workaround time. 

Instead of embedding `git describe` into the program itself via GitPython, I ended up moving into a distribution script. This is a really simple config wrapper that goes through all the manual steps of generating the final .exe, so I don't have to remember the build process each time. Now, in addition to all of that build work, the distribution script generates a `version.txt` file every time is run. The actual program reads in the flat `version.txt` file on startup, and uses that string as its version information. 

It's a start, but PyInstaller isn't smart enough to include this `version.txt` file in the installer without you telling it to include it. I've found the easiest way to do that is through editing PyInstaller's `.spec` files. This is pretty straightforward if you [follow their documentation](https://pyinstaller.readthedocs.io/en/stable/spec-files.html). I ended up following the documentation instructions, and modified the `Analysis` initilization with something like this: 

```python
added_files = [('version.txt', '.')]
a = Analysis(['example_script.py'],
             pathex=['C:\\Users\\nash.reilly\\Documents\\GitHub\\example_script'],
             binaries=[],
             datas=added_files,
             hiddenimports=[],
             hookspath=[],
             runtime_hooks=[],
             excludes=[],
             win_no_prefer_redirects=False,
             win_private_assemblies=False,
             cipher=block_cipher,
             noarchive=False)
```

...where all I needed to do was specify `added_files = [('version.txt', '.')]`, and pass that to the `Analysis.datas` field. 

Note, too, that once you've edited the `.spec` file, you'll also need to pass the `.spec` file to PyInstaller, rather than your actual script. Here's a pretty bare-bones version of what my distribution script looks like. See that PyInstaller is using `example_script.spec` rather than the input Python file. If you don't, PyInstaller will just generate a new `.spec` file with each call - which will overwrite any changes you've included locally in your spec file, FYI. 

```python
import os
import PyInstaller.__main__
import git

if __name__ == '__main__':
    # Deletes current version.txt file
    if os.path.exists('version.txt'):
        os.remove('version.txt')

    # Writes latest git version info to 'version.txt'
    r = git.repo.Repo(search_parent_directories=True)
    version_info = r.git.describe('--dirty', '--tags')
    with open('version.txt', 'w') as f:
        f.write(version_info)
        f.write('\n')
        f.close()

    # Builds production version (no debug console window)
    PyInstaller.__main__.run([
        '--name=%s' % 'Example Application',
        '--noconsole',
        '--onefile',
        '--noconfirm',
        'example_script.spec'
    ])  
```

Good news - if you're zipping and distributing the whole output of PyInstaller (i.e. lots of files in your dist directory), you're done!

However, I chose instead make the PyInstaller exe a single file application, for a few reasons:

1. Easier distribution to coworkers
2. No confusion about which system icon to click - this script is going to people who know nothing about Python or programming, so ease of use is *paramount*. 

This needs a little bonus effort to work right. The problem, as explained by [this StackOverflow post](https://stackoverflow.com/questions/51060894/adding-a-data-file-in-pyinstaller-using-the-onefile-option), is that `version.txt` lives in different places in the dev and deployed environments when you use the `--onefile` option. In your dev environment, this is the same directory as the build script. (Hence this: ".") When you hand the bundled script off to other people, PyInstaller unpacks that `version.txt` folder into the system's temp data folder. You can get access there by calling `sys._MEIPASS`. The helper function `resource_path()` is a quick sanity check that modifies the base path of `version.txt` based on some really simple exception logic, which allows you to check both locations gracefully for your versioning info:

```python
def resource_path(relative_path):
    """ Get absolute path to resource, works for dev and for PyInstaller """
    try:
        # PyInstaller creates a temp folder and stores path in _MEIPASS
        base_path = sys._MEIPASS
    except Exception:
        base_path = os.path.abspath(".")
    return os.path.join(base_path, relative_path)

# Git Repo information
version_info = ''
version_file_path = resource_path('version.txt')
with open(version_file_path) as f:
    version_info = f.readlines()[0].strip()
```

Note well: every config file that gets bundled with the .exe needs to be read in thru the `resource_path()` function. Otherwise, your program will crash, because it will look in the local directory for that file, and not find it! 