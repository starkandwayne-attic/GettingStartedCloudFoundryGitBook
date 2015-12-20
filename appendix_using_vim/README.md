# Appendix: Using Vi(m)

## Command Line Text Editor: Vi(m)


### Built in Text Editors

Unix and Unix-based operating systems (Mac, Linux) have a few text editors available via Terminal (the command line), such as:

* emacs
* vi(m)
* pico/nano

Familiarity with one or more of these tools is strongly recommended, primarily because this will provide you the freedom to work from any Unix-based computer or session (e.g. PuTTY session) without installing additional tools.

Of these editors, the most commonly preferred editors are vi(m) and emacs as they are the most powerful editors. Pico/nano can be useful in a pinch with the basic functions that you'd expect from a text editor.

### Quick History of Vi(m)

Vi was originally written in 1976 with the code released in 1979. It was written as a visual editor for a line editor known as `ex`, hence its name (<strong><u>vi</u></strong>sual).

Vim (<strong>vi im</strong>proved) is the successor of vi, initially released in 1991.

Although vim has additional functionality beyond vi, all the commands discussed below are available in both. The abbreviation vi(m) is used here to refer to both editors (you may also see this abbreviation elsewhere on the web).

### Vi(m) Basics: Getting started

**Open a file**

To open a file in vi or vim, you can simply enter either program name and the file name (and path, if not in your current directory). For example, if you are currently in the directory of the file "test.txt" you can open this file in vi using the following command:

```bash
vi test.txt
```

If you wish to open the file in vim, then the process is similar using "vim":

```bash
vim test.txt
```

Similarly in either case, you can specify a file in a different directory. So if the "test.txt" file is on your desktop, you could enter:

```bash
vi ~/Desktop/test.txt
```

OR

```bash
vim ~/Desktop/test.txt
```

This is actually the same way you can create a file: if the file does not already exist, vi(m) will create a blank file with the file name you specified.

**Insert mode vs command mode**

Vi(m) has two modes: insert mode and command mode. When you first open your file, you are in command mode. Command mode allows you to use various keyboard commands to move around file, delete characters/words/lines, replace text, etc. When you are ready to insert text, you can enter "insert mode". To enter insert mode, simply hit the `i` key on your keyboard. Once you are ready to exit insert mode, hit the `esc` key.

**Insert  mode basics**

When you are in insert mode, you can add and delete text similar to how you would with a GUI based text editor like Notepad, Notepad++, TextEdit, etc. You can move around the text file using arrow keys and delete text using your delete key.

**Command mode basics**

When you are in command mode there are two main "types" of commands:

1. Commands prefixed with a colon
1. Commands that are not prefixed with a colon

This is important to note as you will use both "types" of commands. When the syntax includes a colon at the beginning, please make sure that you also include the colon when using it.

**Save changes to a file**

You should always periodically save a file while you are editing it, to prevent losing any unsaved changes. To save changes to a file you will use the "write" command:

```
:w
```

**Close a file**

When you are ready to close the file you are editing (and quit vi(m)):

```
:q
```

If you find that you've made a change that you do not want to save, or you need to close a file that you do not wish to make changes to, you would use:

```
:q!
```

Note that this command includes the "!" after the "q". **You will not be prompted if you want to exit without saving changes if you use this command.**


### Vi(m) Basics: File Editing

**Moving around in your file**

The original keys to move around vi(m) were `h` (left)/`l` (right) and `k` (up)/`j` (down). Although these are still available, most people find it easier to use the left/right and up/down arrows.

If you want to move around a bit more quickly, you can use the `w`, `b`, `e`, `0`, and `$` keys. Both `w` and `e` will move from left to right. In the case of `w` you will move to the beginning of the next word, whereas `e` will take you to the end of the next word. `b` will take you to the beginning of the previous word. If you want to the beginning of an individual line you will use `0` and `$` will take you to the end of the line.

**Deleting text from your file**

The most common deletions are deleting individual characters, words, or entire lines. In the case of individual characters you will use `x`, but for deleting words and lines you will use `d` and a qualifier. For an entire line, you will use `dd` and for a single word you will use `dw`.

**A quick note about words**

Although most words seem intuitive, for example you can count "the cat in the hat" has five words, sometimes a line of text will have a special character such as "the cat's hat". We're not going to go too deep into this here, but this is something to be mindful of when moving around the file and deleting individual words - make sure you know your word boundaries! (e.g. If you use `dw` to try to delete "cat's", you will actually only delete "cat" and be left with "'s").

**Undoing changes**

If you have made a change you do not wish to keep, you can undo it using the `u` key. As far as vi(m) is concerned, making a change and undoing it is still "making a change". So if you open a file, inadvertently change it, undo the change, and then try to use `:q` to quit, you will still receive an error that changes were made - you will need to either save and quit or quit using `:q!`.

### Vi(m) Basics: Stacking commands

You may have noticed with the addition of numbers, that you can actually stack inputs for a complete command. When you combine commands, you can tell vi(m) more details about what you want to actually accomplish.

You may recall previously that `d` was used to delete and was combined with either another `d` or `w` to delete either a whole line or word respectively. You can also do this with other commands. For example, you could have the text:

```
The quick brown fox jumps over the lazy dog.
```

Let's say you want to delete " jumps over the lazy dog." From the start of the line, you would use: `4e` and then right arrow or `l` key so that the cursor is on the space between "fox" and "jumps". Then you can use `d$` to delete the text from your current cursor position to the end of the line.

Another common application of stacking commands is saving and quitting a file. In this case you would use `:wq` to write the file before quitting vi(m).

### Vi(m) Basics: Using numbers

You can also stack numbers onto vi(m) commands. For example, if you wanted to delete five words you would enter `5dw`. Using the previous example, deleting "cat's", you would need to use `3dw` since "cat", "'", and "s" are interpreted as separate words due to the special character.

Stacking numbers is definitely not limited to deleting words! You can also use `3w` to move forward three words, or `10` and the down arrow to move downward 10 lines (if there are 10 additional lines available below your current position in the file).

**Quick note about using numbers**

You may have noticed the caveat in the above example: if there were 10 additional lines left to move. How the command will handle numbers greater than the number available will differ. For example, if you use `10` and the down arrow and there are only 3 lines below you, it will move to the last line available. Some of the other commands, such as the search command (not covered), will simply not execute.

### Review of commands

The commands reviewed above are:

| command | purpose |
|---------|---------|
| `i` | enter insert mode |
| `esc` | enter command mode |
| `:w` | save a file |
| `:q` | quit vi(m) - will error (and not quit) if there are unsaved changes |
| `:q!` | quit vi(m) without saving changes - no confirmation prompt if changes have been made |
| `h`, `j`, `k`, `l` or arrow keys | left/right, up/down movements around file |
| `w` | move to first character of next word |
| `e` | move to last character (end) of next word |
| `b` | move to first character (beginning) of previous word |
| `0` | move to first character of line |
| `$` | move to last character of line |
| `u` | undo last edit |
| `x` | delete character under cursor |
| `dd` | delete line where cursor is positioned |
| `dw` | delete word where cursor is positioned |
| numbers | added at the beginning of command to indicate number of instances, e.g. `5dw` to delete 5 words |


### More about Vi(m)

* **Unix Editors from Stanford School of Sciences** <br />https://earthsci.stanford.edu/computing/unix/editing/editorchoices.php
* **Vim Adventures** <br />http://vim-adventures.com/<br />Learning VIM while playing a game
* **Vim Golf** <br />http://www.vimgolf.com/<br />Users work through a series of tests and try to complete them with the minimum number of keystrokes possible. While learning you may want to view other people's posted solutions; however, I strongly encourage you to try and make your own solution.
