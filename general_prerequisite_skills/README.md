# Appendix: General Prerequisite Skills - Version Control

## Purpose

In order to successfully learn about the topics we will be covering in this book, a basic understanding of version control, shell, and command line text editors (e.g. vi(m)) is recommended. If you are not familiar with some/all of these topics do not despair! Please take a moment to read through the material provided below. Although you may not know all of it before/during training, we encourage you to keep this information close at hand as a guide for current and future usage.

## Version Control

### The What and Why of Version Control

Let's say you have a single Word document that you want to track changes for and you are the only person who edits the document. Maybe you enable "Track changes", or you save a  new file every time you make a change and keep every version of the file. Although the latter is cumbersome, either method will allow you to see every saved changed throughout the life of the document.

What happens when you have multiple files being maintained by multiple editors? Worse, what happens if more than one person tries to edit the same file? This is what version control was made for: to allow multiple people to collaborate on a single project.

Version control systems, or version control software, (VCS) allow users to manage their edits with greater ease. For example, if persons A and B make changes to a single file, the changes can be merged into a single version of the file using the software. VCS also allows file editors to include messages with their saves so that the logic of the change can be preserved (e.g. "formatting fix"). So if person B wants to see why a change to a file was made, they can look at the last commit message of the last change.

### Version control terminology

A high level overview of basic version control terms:

| Term | Meaning |
|------|---------|
| commit | store a change in the version control database |
| push | publish a commit |
| pull/update | save other editors' changes to your local copy of the project |
| commit/log message | short message explaining a change, for example "grammar fix" or "added date feature" |
| repository | the database where changes are stored - this is used to publish the changes when changes are pushed |
| clone/checkout | to obtain either a copy of (clone) or a working copy of (checkout) the files in a project |
| working copy/files | editor's private directory that includes the project source files |
| revision, change, changeset | a particular change or set of changes to one or more files |
| diff | representation of changes between two versions of a file |
| merge | when multiple, non-overlapping, edits have been made to a file the VCS can merge them into a single file |
| conflict | when there are overlapping edits in a single file - a VCS will notify that there is a conflict which must be manually resolved |
| lock | certain files can be locked to either prevent any changes or locked once one person has write access to help prevent conflicts |

### What are some examples of version control systems/software?

One of the most common version control systems is git through its integration with github. Github is essentially an extension of what version control systems are designed to do: it extends the collaborative effort socially, allowing anyone with a github account to fork and modify repositories from any other user (unless the repository is private/has restricted access). Other examples include Apache Subversion (SVN), Mercurial, and Concurrent Versions System (CVS).

### More information about version control

GitHub introduction video to version control:<br />
http://git-scm.com/video/what-is-version-control

Getting Started - About Version Control (from Git documentation):<br />
http://git-scm.com/book/en/Getting-Started-About-Version-Control
