---
bg: "business_time.jpg"
layout: post
title: "Writing Simple Python GUIs for your Command-Line-Phobe Coworkers"
summary: "distributing scripts to unix eunuchs"
tags: ['programming']
date: 2020-06-30

---

If you have a modicum of coding skill, you've probably found some crappy little office task that you can automate away with a judicious hundred lines of Python. This typically takes the form of simple command line applications to reformat files: read in `input_format.csv`, spit out `output_format.csv`, yada yada. If you've got a modicum of empathy to accompany that coding skill, you've probably noticed your coworkers have to complete these crappy little office tasks, too. It's typically this point where most coders get a glimmer in their eye. You start to see a glorious future of automation ahead of you, starting with this single opportunity to remove some drudge work from your coworkers' plates.

"I'll be a hero," you think. "I'll save my coworkers so much time! I'll win the respect and adulation of my peers!" I imagine that this is the point where, if you're a software engineer, you post your little Python script on Slack, people see what it does, and then start using it. Problem solved! 

However, if your coworkers _aren't_ software engineers, this initial distribution is not some great victory met with fanfare. Instead, it's the point where the complaints start rolling in. 

> _"What's Python? How do I install it on Windows?"_
>
> _"How do I use a command line? Can't you make this thing a desktop app?"_
>
> _"What the hell is Cygwin?!"_
>
> _"This script doesn't do anything for me - it's just another thing I have to figure out!"_

Not exactly the glorious reception you'd envisioned, is it? You did all this work for your coworkers to help save them time, and all you got was a brutal pushback! And _nobody_ took the time to actually learn how to use your brilliant command line script!  (Or worse - your boss _loves_ the idea, but doesn't know how to use it, so he asks you to rewrite it as a Visual Basic plugin for Excel. _**shudder**_ )

This is the point where any sane script monkey starts thinking: "Well, fuck these clowns. If they can't appreciate the work I've done to help them, I won't bother helping them any more!" Net result: your coworkers keep struggling with boring, mundane tasks, and you aren't recognized for your genius bit of automation. 

It's a brutal reality, but if you're the sole person in your group who's figured out enough to:

* Install Python, and use it to run command line scripts,
* Run Cygwin or the Linux Subsystem for Windows on your work Windows machine, 
* Get your hands on a Linux machine/VM for writing scripts,

...then you're probably the most experienced software engineer in your group. If your coworkers aren't software engineers, it's a _huge_ lift to get all of your coworkers to understand command lines, Unix systems, and virtual machines. 

The good news is that there's a way to work around this phobia, eliminate some bullshit work for your coworkers, and get some recognition as being "the dude who automated that crappy task that used to take up a nonzero chunk of our days". The trick is to _make your script look like something they have used before_. This isn't nearly as big of a leap as you might think, but it's the difference between writing tools that nobody cares about, and writing tools that your colleagues embrace.

These are some of the lessons I've learned on how to package rudimentary Python scripts into simple Windows GUIs for your ~~heretic, Unperson, Linux-fearing, software-ignorant, oxygen thieving~~ coworkers to use. 

# Add a GUI 

Why add a GUI? Simple: your coworkers know GUIs, and they love GUIs. They love them, even if they've never heard the term "GUI" and don't know what "GUI" means. *Every single program* they use on a daily basis leverages a GUI: Chrome, Excel, Word, Outlook, whatever. Anything that uses a command line is foreign, scary "hacker shit." 

I put "hacker shit" in quotes, because, well, it's a quote from a former boss of mine. (Story time!) I wrote a very short, sloppy Perl script once that could transform a CSV database export into a nicely formatted report with some graphs that he and his fellow managers could use. At the time, I loved that he thought 20 lines of Perl qualified as "hacker shit". It made me feel like a badass, until I realized he couldn't see the value of the method - just the output. Because the method of generating those graphs was a command line program, it was "hacker shit" - not something he could understand. Rather, it was something that I would have to *perform for him*, rather than *a task he could do himself.* Net result: I had to spend fifteen minutes generating a report for my boss every Friday, because he couldn't figure out how to use my software.

Moral: _use GUIs_. GUIs feel safe and familiar to your coworkers, and _feeling safe and familiar leads to people using your software._

This is where I've seen a lot of disconncts between "software people" and "office people". Why? 

Writing command line scripts is _easy_. Writing GUIs is _hard_.

Anyone who has futzed with `tkinter`, Python's default GUI framework, knows it's as challenging to get right as it is powerful. You only need to write one `tkinter` application to know that it has a lot of "gotchas" around window formatting, passing variables between GUI elements, and all sorts of other backend crap that you don't necessarily want to worry about. You're not a software developer - you're just writing some software to help you and your coworkers get back to your _actual work_ faster. 

The `PySimpleGUI` module takes care of a _lot_ of the heavy lifting around building a simple GUI. 

* Instead of exhaustively declaring the position of each GUI element, you can just pass a list of lists, and let `PySimpleGUI` figure out how to format them nicely. 
* `PySimpleGUI` does all of the hard work of figuring out OS functions like file browsing and folder browsing by leveraging native OS tools for these tasks.
* It's super easy to add one of `PySimpleGUI`'s canned color schemes, so you don't have to struggle through making your GUI look like un-branded ass. 

Again - I'm assuming that you're writing a little script that will be used by a few people. This isn't a tutorial on how to make a professional looking desktop app like Spotify, or the Atom editor. The goal here is to make a little GUI that is "just good enough" to be used by you, and ten to twenty of your closest colleagues. 

## Accepting Text Input

It's really easy to feed text into a `PySimpleGUI` window. A simple TextBox is a fantastic way to dump in basic text data: 

```python
import PySimpleGUI as sg

sg.theme('BluePurple')

layout = [[sg.Text('Your typed chars appear here:'), sg.Text(size=(15,1), key='-OUTPUT-')],
          [sg.Input(key='-IN-')],
          [sg.Button('Show'), sg.Button('Exit')]]

window = sg.Window('Pattern 2B', layout)

while True:  # Event Loop
    event, values = window.read()
    print(event, values)
    if event == sg.WIN_CLOSED or event == 'Exit':
        break
    if event == 'Show':
        # Update the "output" text element to be the value of "input" element
        window['-OUTPUT-'].update(values['-IN-'])

window.close()
```

This gets even easier when you're trying to select an input file or folder. `PySimpleGUI` bakes in File Browser and Folder Browser functionality linked into the native OS. This is dope, because it looks *exactly* like the File Browser interface that ~~those chowderheads in marketing~~ your local Excel power users already use to suck in CSV data to their spreadsheets.

```python
import PySimpleGUI as sg

sg.theme('Light Blue 2')

layout = [[sg.Text('Enter 2 files to comare')],
          [sg.Text('File 1', size=(8, 1)), sg.Input(), sg.FileBrowse()],
          [sg.Text('File 2', size=(8, 1)), sg.Input(), sg.FileBrowse()],
          [sg.Submit(), sg.Cancel()]]

window = sg.Window('File Compare', layout)

event, values = window.read()
window.close()
print(f'You clicked {event}')
print(f'You chose filenames {values[0]} and {values[1]}')
```

Another nice detail of this script - you'll notice that the `FileBrowse` method is in the same line as `Input`. `PySimpleGUI` is smart enough to know that the `FileBrowse` field is associated with the `Input` field by virtue of being on the same line in the layout file. As a result, the `FileBrowse` method will drop the file your use browses to into the `Input` field. Neat! 

Using the contents of a whole folder is just as painless. Just substitute `FolderBrowse` for `FileBrowse`, and you can select a whole folder of content files to work with. Spicy! 

## Command Line Options

Sometimes a full blown text input is more than you really need in a script - you just need some functionality turned "on" or "off". This is where a command line user adds in flags to their command line interface: options like `-p` and `-h` that allow you to turn on optional functionality in your script's operation. 

Checkboxes are a really nice way to emulate command line switches in `PySimpleGUI`:

```python
import PySimpleGUI as sg

layout =  [ [sg.Input(key='input-file'), sg.FileBrowse("Select")],
			[sg.Button("Submit"), sg.Checkbox('Remove Lines That begin with "S"', key='s-checkbox')]]

window = sg.Window(layout)

while True:
	event, values = window.read()
	if event == 'Submit':
        if values['s-checkbox']:
            removeLinesBeginningWithS(values['input-file'])
        file_process(values['input-file'])
```

Checkboxes start to get a little out of hand when your script has a few alternate input or output modes, and you need to select one of these modes for the script to work properly. (Think scenarios where you use one input format form to spit out your choice of different output forms. A good example is something like using a form of name/address/phone numbers, and using that to print out your choice of a FedEx, UPS, or DHL shipping label.) Radio Buttons are a nice alternative to checkboxes in this instance:

```python
import PySimpleGUI as sg

r_keys = ['r-mode-1', 'r-mode-2', 'r-mode-3']

layout =  [ [sg.Input(key='input-file'), sg.FileBrowse("Select")],
            [sg.Radio('Option 1', key='r-mode-1'), 
             sg.Radio('Option 2', key='r-mode-2'),
             sg.Radio('Option 3', key='r-mode-3')]
			[sg.Button("Submit"), sg.Checkbox('Remove Lines That begin with "S"', key='s-checkbox')]]

window = sg.Window(layout)

while True:
	event, values = window.read()
	if event == 'Submit':
        mode = [ key for key in r_keys if values[key]][0]
        if mode == 'r-mode-1':
            do_function1(values['input-file'])
        elif mode == 'r-mode-2':
            do_function2(values['input-file'])
        elif mode == 'r-mode-3':
            do_function3(values['input-file'])
    
```

This example shows how you can specialize your program flow between one of three options. 



## Sane Error Checking

One other thing you will learn very quickly when you write software for your colleagues is that they will find _all_ of its limitations. They typically learn this by finding dumb, simple bugs that you've never thought to consider, because, well _you wrote the software_. You understand intuitively how it should work! Your coworkers, however, don't. This leads them directly to you for tech support. Observe:

> Coworker: "_Your script didn't work!_"
>
> You: "_Did you feed it a CSV file?_"
>
> Coworker: "_No, I gave it an Excel file, and it barfed!_"

The naive answer to this situation is to think: "My coworker is a fucking moron. Now I have to teach him how to use it the right way." So, you immediately and patiently start instructing him on the proper manner of usage. Half an hour later, your coworker knows how to export CSV files from Excel, the proper file format is generated, and everyone is happy. By next week, however, he's forgotten how to export CSV files again, so he'll come to you for a remedial lesson.

This naive answer is the wrong answer to this situation. Instead of solving your coworkers' problems, you've mortgaged a half hour to him every Friday to re-teach him how to use your tool. 

The right answer to this situation: take this opportunity to fix your simple little GUI. Take advantage of these moments to fix _this_ error, and also _all future errors like this_. 

Experience has shown me that this is what `try/except` blocks were born to do. 

You can enclose functions that do formatting dependent stuff in a `try:` block. If you're doing things like:

* expecting to read input from a specific file format (csv, txt, xlsx, etc)
* taking input from a spreadsheet that expects a certain column order
* reading in a text file where the contents are expected in a certain line order,

...an exception can get thrown when things go sideways. An `except:` statement is great for flashing up little `PySimpleGUI` popups that help correct bad user behavior, simply by *reminding them what the proper behavior is*. Pay close attention to the dumb mistakes you make while developing this little application. What happens when you feed the wrong kind of file to the script? Does it handle incorrectly formatted data gracefully? These moments are the ones you'll want to give your users a little nudge in the right direction. 

Here's a really ace example of what this looks like: 

```python
import PySimpleGUI as sg

class FileTypeError(Exception):
    """Dummy exception class to catch CSV file errors"""

layout = [[sg.Text('Select a CSV file'), sg.Input(), sg.FileBrowse()],
         	[sg.Button('Submit')]]

window = sg.Window(layout)

while True:  # Event Loop
    event, values = window.read()
    print(event, values)
    
    # Submit File
    if event == 'Submit':
        try:
            fname = values[0]
            if os.path.splitext(fname)[-1].lower() != '.csv':
                raise FileTypeError
            fp = open(fname)
        except FileError:
            sg.Popup('There was a problem with the input file.')
        except FileTypeError:
            sg.Popup('Input file type must be CSV.')
        else: 
            # do file actions here
```

Flashing a little message window with `sg.Popup()` is a quick, easy way of pointing out errors to your users. You can get substantially richer and more complex when it comes to these kinds of nudges, however. I've written little debugger routines that pre-screen the file input data, and give users line-by-line suggestions of how to get their input data in line with the program's expectations. This is basically free, built-in training for your users. The more time you spend doing this up front, the less time you'll need to spend getting your colleagues un-stuck. 

Remember: whenever your software doesn't work, _your coworkers are going to call you._ It's wise to get ahead of this wherever you possibly can!

# Distribute

Adding a GUI solves some issues with usability. Your program at least has a user interface that any Windows-loving doofus could use. You're not out of the woods yet: you still need a way to get your scripts in front of your coworkers. 

This is where the `pyinstaller` package comes to save your distribution bacon. `pyinstaller` takes the hassle out of making a simple binary package for other Windows users. This is typically dirt simple. You just need to run the following command:

```powershell
$> pyinstaller --noconsole your_script.py
```

And `your_script.py` becomes a Windows executable with a whole folder of supporting files in the `dist` directory! Distribution, at this point, is as simple as zipping the `dist` directory that `pyinstaller` spits out. Installation on your coworkers' machines is as simple as unzipping the `zip` file. Helpful hint: point out to your colleagues where the `.exe` file lives. 

However, you've probably read enough of this to realize that making this process *simple* really helps improve your adoption. It'd be a huge improvement if it were *obvious* to your coworker which file to click. `pyinstaller`, fortunately, foresaw this need, and allows you to bundle your program into a single .exe file:

```powershell
$> pyinstaller --noconsole --onefile your_script.py
```

This option makes it so that there is one file, `your_script.exe`, that needs distribution. No confusing extra files! Your coworkers just need to double click that one file, and it will open their slick new GUI program that saves them time and elevates you to Automation Godhood in their minds. 

Take note: this method of bundling to one file makes your program much slower to open. It takes somewhere between 5 and 10 seconds on my Windows machine. I've also read that there are some hand-waving steps that it takes that can cause problems when distributing to other systems. Instrument well, and keep a debug console version handy! 

You get extra super bonus points if you show them how to create a shortcut to the `.exe` to put on their desktop. That makes your program look even more like any other given GUI program that your coworkers use. (This would be a great thing to automate, but I haven't figured out how to do it. Please shoot me an email if you know an easy way to do this!)

## Script Consoles: A Debug Life Raft

Do yourself a favor, and build two versions of your script: one with the `--noconsole` option, and a second, debug version without the option. This allows you to have two versions of your script: a "production" version that has no console window, and a "debug" version that includes a console window with debug messages:

```powershell
# This is the "production" version
$> pyinstaller --noconsole your_script.py
# This is the "debug" version
$> pyinstaller your_script.py
```

You can zip the `--noconsole` version and send it out to your coworkers as the "production" version of your program, and zip the "debug" version to hold in reserve to fix unexpected issues. When your coworkers complain to you about behavior that you, yourself, can't explain, you're all teed up to send them the debug version, and request a screencap of the resulting console output. (Windows Snipping Tool is your friend here!)

Best of all: this is next to no overhead to maintain. You're using the same script as inputs for `pyinstaller`, but just feeding it different command line options. 

# An Example

One of my finest examples of this sort of GUI script automation is an application I wrote to reformat CSV export into the format expected by a web database. (If you're really curious - it's an engineering bill of materials file from OrCAD that needs to be stored in Arena PLM. Woohoo, line of business software!) It's pretty damn simple - it just implements a few functional rules to apply to the data, sorts it into a particular order, and yanks out a few line items that don't need to make their way into the database. The code is [available on GitHub](https://github.com/Cushychicken/arena_bom_formatter) for anyone to peruse - it has examples of just about every one of these actions in it.
