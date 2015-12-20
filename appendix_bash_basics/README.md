# Appendix: BASH Basics

## What and Why of BASH

BASH, an acronym meaning "Bourne-Again SHell", was originally released in 1989 as an improvement on the Bourne shell (simply referred to as sh). sh was written by Stephen Bourne and his surname is incorporated into the names for both BASH and sh. BASH is the standard command line interface for most Unix and Unix based systems. (So if you are accessing a Unix or Unix based system via PuTTY, you are probably accessing a BASH session.)

Since the majority of BOSH, Cloud Foundry, and UAA functionality is handled using CLI commands it is important to have a basic understand of BASH since BASH is the environment where these commands "live". The commands and concepts below will be used (directly or indirectly) as part of the training.

## BASH Basics: Users

When you open Terminal or PuTTY, you are by default accessing the shell as a user. In the case of PuTTY (or ssh), you are connecting to a remote machine typically as a specific user. If you are running Terminal, for instance if you are using Mac OS X, a flavor of Linux, or Sun OS then usually you will be logged in as your current user account by default.

In the labs, you will have user names like `student1` or `student11`, but when you are using PuTTY/ssh in other contexts you may have different usernames such as `databaseadmin`, `projectqa`, or `jdoe`. Each user will have different permissions associated with their account, for example `jdoe` may be able to view table data and properties, but only the `databaseadmin` can create/delete/update tables or databases in that environment.

There are some cases where user privileges are not enough for certain actions. One user that will never have permissions problems is the `root` user. The `root` user is also sometimes known as a superuser: it can literally do anything on the system. This is incredibly dangerous, as it means there are no restrictions in place that would prevent the `root` user from executing potentially dangerous commands. One common example on the web is that if you are logged in as `root` you should ***never, ever*** **execute the following command**: `rm –rf /`

The reason this example is used so commonly on the internet is to help users understand that when they go for help on various forums/etc., they should always check over any commands they are given and fully understand their consequences before executing them - especially if they are `root`. As `root` user, there is nothing stopping you from doing something that will irreparably harm your system like the above command. (What precisely this command does is described in the _BASH Plus: Flags and Parameters_ section below.)

In order to avoid potentially damaging your system, there is another option in BASH for executing commands that require root permissions: `sudo`. `sudo` is a prefix to a command that essentially runs it as the root user. As a quick example, if you are logged in as user `jdoe` and you need to execute `less /etc/aliases` (more on what this is later) as `root`, you can enter: `sudo less /etc/aliases`. When you enter sudo for the first time in a session you will be prompted for the `root` password. For security reasons you will be periodically prompted to re-enter the `root` password when using `sudo` (exactly how often depends on your configuration).

In order to switch users you can use the `su` command. To switch from user `jdoe` to user `jsmith`, you would enter:

```
su - jsmith
```

You would then be prompted to enter `jsmith`'s password. If no user name is supplied, then `su` will switch to the `root` user account and prompt you for the `root` account password.

## BASH Basics: Directories

In order to manipulate files, you need to be able to move around the various directories on your system. You may already be familiar with the way that directory structures appear, for example `path/to/your/file.ext`. There are a couple of different shorcuts you can use when referring to directories in Unix/Unix based OSes.

**Home Directory**

Your home directory is the directory associated with the current user's account. e.g. If you are user `jdoe` your home directory is `/Users/jdoe`. Since it is very common for user's to access subdirectories of their home directory, there is a shortcut to this directory, which is `~`.

**Relative Directories**

You can also specify directories relative to the directory you are currently in

**Viewing and Changing Directories**

You can view your current directory with the `pwd` command, which stands for "print working directory". If you are in your home directory as user `jdoe` and you execute `pwd`, you would see something like:

```bash
$ pwd
/Users/jdoe
```

You can also change your directory using the `cd` command. If you wished to change your current directory to directory `/new/file/path` then you would use:

```bash
cd /new/file/path
```

Note that there is no output for the `cd` command - it simply moves you to the specified directory with no output.

Let's say that you want to use the above shortcut to change your directory from your current directory back to your home directory. In this case you can enter either `cd ~` or `cd`. In the second case you do not need to specify a path because `cd` assumes the current user's home directory by default. That said, it is usually good practice to use the `~` so that you get in the habit of always specifying an argument.

Now let's say that you want to change directories from your current directory, `/Users/jdoe` to another directory `/Users/jsmith` (and we'll assume `jdoe` can view `jsmith`'s home directory). You could do this by specifying the complete path:

```bash
cd /Users/jsmith
```

Or, because `jdoe` and `jsmith` are both subdirectories of `/Users/` you could do the following:

```bash
cd ../jsmith
```

You can also nest `..` more than once. For example, if you wanted to change from `/this/is/my/current/filepath` to `/this/is/my/new/path` you could enter either:

```bash
cd /this/is/my/new/path
```

OR

```bash
cd ../../new/path
```

Another directory shortcut that is common to use is `.`. `.` refers to the current active directory. This is actually  used relatively frequently - examples will be shown in the next section.

## BASH Basics: Files

To view all the files in your current directory you use the `ls` command. You can also view files in other directories by specifying the file path:

```bash
ls /file/path
```

**Create a File**

Similarly you can create a new file using the `touch` command. This command will create a file of any name and extension you specify and, just like ls, you can specify a directory to put the file in a different directory than your current directory:

```bash
touch /file/path/newfile.extension
```

By default the file is completely blank - you can open it in the appropriate program (e.g. a text editor if it will be a text file) to add content.

**Copy a File**

If you have a file that you want to copy from one directory to another, you will use the `cp` (copy) command. When copying the file, you must first specify the file you wish to copy (including path) and then the directory you want to copy the file to.

Let's say that `jsmith` has the most recent copy of the phone directory and you wanted to copy the file from his directory to yours. You would do that by:

```bash
cp /Users/jsmith/phoneDirectory.doc /Users/jdoe
```

Remember, you can also use `~` for your home directory, so you can also use:

```bash
cp /Users/jsmith/phoneDirectory.doc ~
```

If you want to copy the file to whatever directory you are currently in, you can use `.`:

```bash
cp /Users/jsmith/phoneDirectory.doc .
```

Note that `cp` does not have a default (for example `su -` assumed a default of `root` when no user was specified), so you must always specify both the directory you are copying from as well as the directory you are copying to.

**Move and Rename a File**

You can move files in the exact same way as copying them, excep instead of `cp` you will use `mv`. So if you  want to move a file from `/old/directory` to `/new/directory`, then you would use:

```bash
mv /old/directory/filename.ext /new/directory/
```

If you do not specify a file name with with new directory, `mv` assumes you want to keep the same file name. If you specify a new file name, then it will change the file name when it is moved. This means that the `mv` command is also used to *rename* a file as well as *move* a file.

So, if you want to rename a file from myOldFile.ext to myNewFile.ext:

```bash
mv myOldFile.ext myNewFile.ext
```
**Delete a File**

You can also delete files using the `rm` (remove) command:

```bash
rm myFile.ext
```

**View a Text File**

If you have a text file that you would like to view but do not want to open in a text editor like vi(m) (covered below), you can use the commands `less`, `more`, or `cat` and specify the file name. For the purposes of this overview, these commands can be  used interchangeably. As a quick example:

```bash
less myFile.ext
```

## BASH Plus: Flags

Many commands have optional flags that allow you to specify additional constraints for how the command executes. The easiest way to show this is by example.

**Common flags for `ls`**

```bash
$ ls -l
total 8
-rw-r--r--  1 qanx  staff  233 Aug 21 15:17 file.txt
-rw-r--r--  1 qanx  staff    0 Aug 21 13:52 file1.png
-rw-r--r--  1 qanx  staff    0 Aug 21 13:52 file2.jpg
-rw-r--r--  1 qanx  staff    0 Aug 21 13:52 word.doc
-rw-r--r--  1 qanx  staff    0 Aug 21 13:56 word123.doc
-rw-r--r--  1 qanx  staff    0 Aug 21 13:56 word223.doc
-rw-r--r--  1 qanx  staff    0 Aug 21 13:56 word456.doc


$ ls -al
total 8
drwxr-xr-x   9 qanx  staff  306 Aug 21 15:17 .
drwxr-xr-x  19 qanx  staff  646 Aug 21 13:51 ..
-rw-r--r--   1 qanx  staff  233 Aug 21 15:17 file.txt
-rw-r--r--   1 qanx  staff    0 Aug 21 13:52 file1.png
-rw-r--r--   1 qanx  staff    0 Aug 21 13:52 file2.jpg
-rw-r--r--   1 qanx  staff    0 Aug 21 13:52 word.doc
-rw-r--r--   1 qanx  staff    0 Aug 21 13:56 word123.doc
-rw-r--r--   1 qanx  staff    0 Aug 21 13:56 word223.doc
-rw-r--r--   1 qanx  staff    0 Aug 21 13:56 word456.doc


$ ls -alF
total 8
drwxr-xr-x   9 qanx  staff  306 Aug 21 15:17 ./
drwxr-xr-x  19 qanx  staff  646 Aug 21 13:51 ../
-rw-r--r--   1 qanx  staff  233 Aug 21 15:17 file.txt
-rw-r--r--   1 qanx  staff    0 Aug 21 13:52 file1.png
-rw-r--r--   1 qanx  staff    0 Aug 21 13:52 file2.jpg
-rw-r--r--   1 qanx  staff    0 Aug 21 13:52 word.doc
-rw-r--r--   1 qanx  staff    0 Aug 21 13:56 word123.doc
-rw-r--r--   1 qanx  staff    0 Aug 21 13:56 word223.doc
-rw-r--r--   1 qanx  staff    0 Aug 21 13:56 word456.doc
```

So `ls -l` lists files with permissions, `ls -a` lists all files - including hidden files, and `ls -F` appends "flags" that provide additional information. (Experiment with `ls -F` or `ls -aF` to see how different flags are applied.)

Notice that flags can be stacked: so you can use `ls -al` in place of `ls -l -a`.

**Common flag for `cp`**

Let's say you want to copy all the files from the `temp` directory to `temp2`. In that case, you would use `-r` (`r` stands for "recursive"):

```bash
cp -r temp/* temp2
```

Note that I used the `*` to indicate all files. If I instead entered:

```bash
cp -r temp temp2
```

I would be copying the `temp` directory and all of its files and subdirectories into `temp2`. For added clarification, with the first method `file.txt` would be in `temp2/` and with the second method it would be in `temp2/temp/`.

**Common flags for `rm`**

The two most common flags for `rm` are `-r` and `-f`. `-r` functions the same way as above with `cp`, except `rm` deletes instead of copies. `-f` means "force". As a security measure, when you try to delete certain files you will be prompted asking you to confirm. If you want to skip those confirmations, you use the `-f` flag to suppress them.

Let's say that you are in `~` and you want to remove `temp` and all its files and subdirectories:

```bash
rm -r temp/*
```

If you would also like to remove the parent directory, `temp`, you would enter:

```bash
rm -r temp/*
```

OR

```bash
rm -r ./temp
```

If for some reason the `temp` directory had a confirmation prompt that you wanted to suppress, you could use:

```bash
rm -rf ./temp
```

**A return to `rm -rf /`**

You may have noticed that the above looks remarkably similar to `rm -rf /` from before. You may now have even realized what this does: `/` is the root directory, which means it is the parent directory of literally every directory in your system. As mentioned above, `-rf` is "recursive, force", which means that you have just executed a command that will go through every file and directory in your computer without exception and delete it. To be clear this isn't "oh no, I'll just recover it oops" delete. This is permanent. This "red alert, but don't bother with the shields or bracing for impact because we've already hit the planet's surface" delete.

If you are anything other than `root` you will simply receive an error, because there are checks in place to prevent regular users, even admins in some cases, from harming their systems. `root` has no checks - the command will execute without so much as an "are you sure you really want to be doing this? y/n" prompt. Commands like this just reinforce the importance of using `sudo` over `root`: `sudo` is when you run a single command as `root`, on purpose, without changing whatever user you are logged in as. If you switch to `root` and just hang in there executing commands, it is highly likely eventually you will run into one that will cause you no end of grief as `root` has no restrictions (I cannot emphasize this enough).

## BASH Basics: Variables

There are two different ways to set variables in your session depending on how you intend to use your environmental variables:

```bash
var=value
```

OR

```bash
export var=value
```

The latter option makes the value of the variable available to both parent and child processes, the former option is for parent processes only.

As an example, consider the following:

```bash
$ var1=123

$ export var2=456

$ echo "parent [$var1] [$var2] [$var3]"
parent [123] [456] []

$ var3=789 bash -c 'echo "child [$var1] [$var2] [$var3]"; var1=111; var2=222; var3=333; echo "child [$var1] [$var2] [$var3]"'
child [] [456] [789]
child [111] [222] [333]

$ echo "parent [$var1] [$var2] [$var3]"
parent [123] [456] []
```

Please note that there are no spaces around the equal sign (e.g. "var=value" not "var = value"). If you enter spaces around the equal sign when assigning value to a variable, you will receive an error.

When creating new variables, you can use existing variables or command output.

For example, if you use the `whoami` command prints your user name to terminal. If you wanted to concatenate the string "myUserIs:" with the result of `whoami` then you would enter:

```bash
export myNameIs=myUserIs:$(whoami)
```

`$(<command>)` executes the command in parenthesis and uses the output. If you wanted to concatenate the value of an existing variable with a new variable, you should not use the `$()` syntax - that is for commands only. To use a variable in a string, indicate the variable with a `$` followed by the variable name. For example:

```bash
export myVar=originalValue

export myNewVar=newValue:$myVar
```

You can use `echo` to print both `$myVar` and `$myNewVar` to screen to verify that `$myNewVar` includes the value of `$myVar`.

## BASH Basics: Where to get help

All commands native to BASH have `man` page (for manual). The man page include the name, syntax, as well as all the flags available for the command. Note that not all commands that are accessible in BASH are native - some are added later by installing packages. These commands may or may not have man pages.

Some commands also have an optional `-h` or `-help` command.

## BASH Plus: Some concepts to help you start your journey to becoming a BASH Wizard

The following are all going to be explained example and will help you

**[tab] to Autocomplete**

This is best explained by example. Let's say that in your home directory you have a "Development" and a "Desktop" directory. If you enter the following and then hit [tab]:

```bash
cd ~/D
```

It will auto-complete to:

```bash
cd ~/De
```

If you enter "v", then it will then autocomplete to:

```bash
cd ~/Development
```

Basically: tab will autocomplete as much of the name it can before hitting a conflict. Inthis case, since "Development" and "Desktop" start with "De", autocomplete doesn't get very far. At the third letter both become unique, so you can auto-complete the rest of the word. (Note this works for both file names and directories.)

**Up/Down Arrows to go Through History**

If you need to execute the same command multiple times, or you wish to make a minor edit to a previously executed command, you can use the up/down arrows to go through your command history.

**\* as a wildcard**

\* can be used with several commands as a wildcard. This means that it can be used in place of one or more characters. For example, let's say that you have the following files in your current directory: file.txt, file1.png, file2.jpg, and word.doc.

Take a look at the different outputs, depending on the placement of \*

```bash
$ ls
file.txt	file1.png	file2.jpg	word.doc

$ ls file*
file.txt	file1.png	file2.jpg

$ ls file*png
file1.png

$ ls file*g
file1.png	file2.jpg
```

Let's add a couple more files and see a couple more examples: word123.doc, word223.doc, word456.doc

```bash
$ ls
file.txt	file2.jpg	word123.doc	word456.doc
file1.png	word.doc	word223.doc

$ ls word*2*doc
word123.doc	word223.doc
```

**history**

To view a history of commands executed by your user, you can run the `history` command. How many commands are saved will vary system to system (usually the admin will set a certain number or use the default).

**grep**

Grep allows you to search a file for a particular string. Take a look at the following string of commands (recall `cat <file>` displays the contents of the text file specified):

```bash
$ cat demo_file
THIS LINE IS THE 1ST UPPER CASE LINE IN THIS FILE.
this line is the 1st lower case line in this file.
This Line Has All Its First Character Of The Word With Upper Case.

Two lines above this line is empty.
And this is the last line.

$ grep -i "THIS" file.txt
THIS LINE IS THE 1ST UPPER CASE LINE IN THIS FILE.
this line is the 1st lower case line in this file.
This Line Has All Its First Character Of The Word With Upper Case.
Two lines above this line is empty.
And this is the last line.

$ grep "THIS" file.txt
THIS LINE IS THE 1ST UPPER CASE LINE IN THIS FILE.
```

Let's now also say that you have the following files:

```bash
$ ls
file.txt	file2.jpg	word123.doc	word456.doc
file1.png	word.doc	word223.doc
```

What does the following output?

```bash
ls | grep "file"
```

As you can see, grep is actually incredibly useful for searching files and command output - I strongly encourage you to take a look at examples available on the web.

**wget**

If you wanted to download a file from an external location:

```bash
wget http://www.example.org/files/archive.tar
```

**tar**

The "tar" extension indicates a compressed file, similar to "zip" on windows. If you wanted to extract (uncompress) a tar file (also called a tarball), then you would enter:

```bash
tar xvf archive.tar file1
```

`x` specifies that tar is extracting (as opposed to creating a compressed file), `v` means "verbose" and will list the files being extracted, and `f` tells tar that the file name supplied (here, archive.tar) is the file you wish to extract from.

**Reuse your last argument with !$**

If you frequently need to use the last argument in a command, `!$` will be incredibly useful:

```bash
$ pwd
/Users/<user>

$ mkdir temp

$ cd !$
cd temp

$ pwd
/Users/<user>/temp
```

**Reuse your last command with !!**

```bash
$ echo $PATH
/opt/local/bin:/opt/local/sbin:

$ !!
echo $PATH
/opt/local/bin:/opt/local/sbin:
```

**Execute a Specific Command from History**

If you execute `history`, you should see a list of commands you have executed recently in chronological order. Let's say you want to re-run command number 574 (in my case this was the `ls` command):

```bash
$ !574
ls
file.txt	file2.jpg	word123.doc	word456.doc
file1.png	word.doc	word223.doc
```

**Note:** `!$`, `!<#>`, and `!!` output the full command that was run as part of the output - even if the command itself (e.g. `cd`) does not have other output.

**Searching for a Command in Your History**

Execute the following commands:

```bash
find . -name '*.txt'

echo $PATH
```

Now let's say you need to execute the first command some time again later. You don't remember the specifics of the command, but you know that you executed `find`. You can search through your history using ctrl+R. Try entering ctrl+R and then the word: `find`. You would see:

```bash
(reverse-i-search)`find': find . -name '*.txt'
```

Feel free to play around with finding different commands in your history!

**Listing and Killing Processes**

If you have a process that you want to stop running, then you can kill the process with the aptly named `kill` command. `kill` uses the process ID, so you will need to list the current processes using `ps`:

```bash
$ ps
  PID TTY           TIME CMD
67209 ttys000    0:00.63 -bash
67305 ttys001    0:00.70 -bash
85387 ttys002    0:00.19 -bash
```

Let's say you need to kill the process associated with PID (process ID) 67209, you would enter:

```bash
kill 67209
```

You may also see kill with numeric flags, most commonly `kill -9`.

`kill -9` is typically used for "misbehaving" processes. It terminates the process without a "safe" shut down. In more technical terms, `kill -9` is referred to as the SIGKILL option, which means that the kernel cannot ignore this signal so it stops the process immediately. This process cannot be handled by application programs.

`kill -15` is the default signal, referred to as the SIGTERM signal, that allows a process to cleanly/safely shut down prior to exit.

**awk**

`awk` is a powerful tool that is used for processing rows/columns of text - either in files or in command output. It is actually a programming language, similar in syntax to C. Also like C, it supports string functions, associative arrays, basic operators, conditional blocks, and loops.

As a simple example, let's say you wanted to convert 77<sup>o</sup> Farenheit to Celsius in the command line. You could use `echo` to output the temperature in Farenheit that you were interested in converting, then use `awk` to comput appropriate mathmatical equation and print the result.

```bash
echo 77 | awk '{ print ($1-32)*(5/9) }'
```

`$1` refers to the first (in this case only) output from the echo command. If you changed the first portion of the statement to `echo 77 32`, this would still return the same result. If you wanted to refer to the second result instead, you would indicate the second output with `$2` as follows:

```bash
echo 77 32 | awk '{ print ($2-32)*(5/9) }'
```

A simple tutorial to help get you started:
http://www.hcs.harvard.edu/~dholland/computers/awk.html

**regex**

Regex is actually a very complicated and incredibly useful feature of BASH when you are looking to search/parse strings. It has other applications than BASH, for example regex can be used in some programming languages as well as with vi(m) (the latter is not explicitly covered below).

An excellent beginners guide to using regex with BASH:
http://tldp.org/LDP/Bash-Beginners-Guide/html/chap_04.html

### More information about BASH

A good place to start learning BASH, including the bit of "wizardry" above (and more):
http://www.tldp.org/LDP/Bash-Beginners-Guide/html/

A good place to start learning regex:
http://www.regular-expressions.info/tutorial.html

A good place to start learning awk:
http://www.thegeekstuff.com/2010/01/awk-introduction-tutorial-7-awk-print-examples/

A useful tool to enhance your Unix experience, tmux:
http://tmux.sourceforge.net/

### Review of commands

The commands reviewed above are:

| command | description |
|---------|-------------|
| sudo | executes a command as `root` |
| su | switches user account |
| pwd | print working directory (displays current directory)|
| cd | change directory |
| ls | list files/subdirectories of a particular directory |
| touch | create an empty file with specified name |
| mkdir | create an empty directory with specified name |
| cp | copy file/directory |
| mv | move file/directory |
| rm | delete file/directory |
| less, more, cat | view contents of text file |
| man | view manual entry for specified command |
| history | view history of commands entered |
| grep | search text file/command output for a string |
| wget | download a file |
| tar | create or uncompress tarball (.tar file) |
| export | stores variables for use with parent and child processes |
| echo | prints value of string/variable to console |
| ps | lists active processes |
| kill | kills (terminates) a process specified by the PID that can be found using `ps` |
| awk | language run by command of same name, primarily used for text manipulation of command or file output |
| !$ | reuse last argument |
| !! | reuse last command and argument |
| ![#] | execute command number # from history |
| `tab` | autocomplete file or directory name |
| `*` | wildcard - can be used in file or directory names used as arguments |
| `~` | placeholder for user's home directory |
| `.` | placeholder for current directory |
| `..` | placeholder for parent directory (can be repeated) |
| `ctrl`+R | searches history for string |
