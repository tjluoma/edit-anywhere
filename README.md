edit-anywhere
=============

**Summary:** A quick-and-dirty attempt at implementing [QuickCursor][1] using [BBEdit][2] and [Keyboard Maestro][3].

**For those who aren't familiar with QuickCursor:**
QuickCursor did one thing really well: anywhere you could edit text, it would send that text to [your favorite text editor][2] and then when you closed that file, it would send the text back to the original application.

So, for example, if you were using Google Chrome to write an email in GMail but wanted to write it in BBEdit, you would just press your keyboard shortcut for QuickCursor, it would select all of the text (or just part of it if you had some already selected), cut it, open it in BBEdit, wait for you to finish, and then switch back to Google Chrome and paste the text that you had written in BBEdit.

Got it? Good. (If not, watch [the YouTube video for QuickCursor][4].)

### If QuickCursor is so great, why are you using this? ###

In a word: [sandboxing][5]. QuickCursor was killed by sandboxing.

This annoyed me for several reasons. QuickCursor was the first app that I bought from the Mac App Store. But most of all I really missed being able to edit text in BBEdit from anywhere.

### How does this work? ###

Like so:

1. User presses a keyboard shortcut which has been defined in Keyboard Maestro which triggers a macro which I call 'edit anywhere' (or "EA" for short).

2. EA checks to see if the menu item 'cut' is enabled. 
	*	if yes, then EA assumes that you have some text selected, and that is the text you want to edit in BBEdit.
	*	if no, EA will do 'Select All' (using either the menu item or ⌘ + A depending on which is available)
	
3.	EA will then 'cut' the text from step 2 and save it to a file `~/.edit_anywhere`. That file is 'hidden' in your home directory, which means that:
	*	you can save it as often as you like
	*	if you reboot your computer (or, heaven forbid, it crashes) the file will still be there
	*	it is secure, at least in as much as no one else should be able to read it unless they already have access to your account.
	
4.	EA will then open the file in BBEdit using the command-line `bbedit` tool which comes with BBEdit. (Note: if you purchased BBEdit from the Mac App Store you will have to [download and install the command line tools from BareBones.com][6].)

5. When you close the `~/.edit_anywhere` file in BBEdit, it will re-activate the app that the text came from. (Note: this step is not foolproof. If you have quit the app or changed it in some way since editing the text, it might not be possible to send the text back to it. QuickCursor had the same problem.)

6. EA will then paste the contents of that file into the original app.

7. EA will then rename the `~/.edit_anywhere` file to something like `edit_anywhere.2013-08-29--16.30.15.txt` (representing the current date and 24h-time) and move it to the trash (~/.Trash/) in case you need to recover it.


### There are a few provisos, a couple of *quid pro quos*.

* This will only work in 'regular' applications. Specifically if will not work in applications which only exit in the menu bar.

* Theoretically you could re-work this to use Mac OS X's `open` command using something like `open -n -W -a YourApp ~/.edit_anywhere.txt`. However, if you did that you would have to store the name of the application where the text came from in a Keyboard Maestro variable and then add a command in your Keyboard Maestro macro to re-activate that app before pasting in the text. That isn't difficult to do, in fact the original version of my macro did that, but BBEdit's command-line `bbedit` tool has a `--resume` flag built right in which is designed to do just that, so I am using it instead of reinventing the functionality.

* It would also be possible to edit the Keyboard Maestro macro to check to see which app the text is coming from an use a different editor depending on that source application. (For example, if the front-most app is Mailplane, I want to edit text in MultiMarkdown Composer vs if the front-most app is Safari I want to edit text in BBEdit.) I just wanted something to give me the base functionality back. I'm not sure whether or not I'll extend it to add that level of customization, mostly because I almost always want to edit text in BBEdit.

### App-specific Macros ###

[Edit-Anywhere-BBEdit.kmmacros][] — 2014-02-04: This is the macro that I am now using with BBEdit which is the easiest app to use it with since the `bbedit` command-line tool has a `--resume` feature.

[Edit-Anywhere-MultiMarkdown.kmmacros][] -- 2014-02-04: A macro to use with [MultiMarkdown Composer](http://multimarkdown.com) which demonstrates how to adapt this to work with any application using OS X’s `open` command.


[1]: http://www.hogbaysoftware.com/products/quickcursor
[2]: http://barebones.com
[3]: http://www.keyboardmaestro.com/main/
[4]: http://www.youtube.com/watch?v=-bHwcyHrRGs
[5]: http://www.hogbaysoftware.com/products/quickcursor/faq
[6]: http://www.barebones.com/support/bbedit/cmd-line-tools.html