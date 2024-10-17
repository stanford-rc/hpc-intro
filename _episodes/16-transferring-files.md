---
title: "Transferring files with remote computers"
teaching: 15
exercises: 15
questions:
- "How do I transfer files to (and from) the cluster?"
objectives:
- "Transfer files to and from a computing cluster."
keypoints:
- "`wget` and `git clone` download a file from the internet."
- "`scp` and `rsync` transfer files to and from your computer."
- "You can use Globus or FarmShare OnDemand to transfer data through a GUI."
---

Performing work on a remote computer is not very useful if we cannot get files
to or from the cluster. There are several options for transferring data between
computing resources using CLI and GUI utilities, a few of which we will cover.

## Download Files from the Internet with `wget` and `git`

One of the most straightforward ways to download files is to use either `wget`
or `git`. These are usually installed in most Linux shells, on Mac OS
terminal and in GitBash. Any file that can be downloaded in your web browser
through a direct link can be downloaded using `wget`. This is a
quick way to download datasets or source code. The syntax for this command is

* `wget https://some/link/to/a/file`

`git` can be used to download files and code from a repository, such as GitHub.
You can

* `git clone https://github.com/stanford-rc/hpc-intro`

## Transferring Single Files with `scp`

To copy a single file to or from the cluster, we can use `scp` ("secure copy").
The syntax can be a little complex for new users, but we'll break it down.
The `scp` command is a relative of the `ssh` command we used to
access the system, and can use the same public-key authentication
mechanism.

To _upload to_ another computer, the template command is

```
{{ site.local.prompt }} scp local_file {{ site.remote.user }}@{{ site.remote.login }}:remote_destination
```
{: .language-bash}

in which `@` and `:` are field separators and `remote_destination` is a path
relative to your remote home directory, or a new filename if you wish to change
it, or both a relative path _and_ a new filename.
If you don't have a specific folder in mind you can omit the
`remote_destination` and the file will be copied to your home directory on the
remote computer (with its original name).
If you include a `remote_destination`, note that `scp` interprets this the same
way `cp` does when making local copies:
if it exists and is a folder, the file is copied inside the folder; if it
exists and is a file, the file is overwritten with the contents of
`local_file`; if it does not exist, it is assumed to be a destination filename
for `local_file`.

## Transferring a Directory with `scp`

To transfer an entire directory, we add the `-r` flag for "**r**ecursive":
copy the item specified, and every item below it, and every item below those...
until it reaches the bottom of the directory tree rooted at the folder name you
provided.

```
{{ site.local.prompt }} scp -r local_dir {{ site.remote.user }}@{{ site.remote.login }}:
```
{: .language-bash}

> ## Caution
>
> For a large directory -- either in size or number of files --
> copying with `-r` can take a long time to complete.
{: .callout}

When using `scp`, you may have noticed that a `:` _always_ follows the remote
computer name.
A string _after_ the `:` specifies the remote directory you wish to transfer
the file or folder to, including a new name if you wish to rename the remote
material.
If you leave this field blank, `scp` defaults to your home directory and the
name of the local material to be transferred.

On Linux computers, `/` is the separator in file or directory paths.
A path starting with a `/` is called _absolute_, since there can be nothing
above the root `/`.
A path that does not start with `/` is called _relative_, since it is not
anchored to the root.

If you want to upload a file to a location inside your home directory --
which is often the case -- then you don't need a _leading_ `/`. After the `:`,
you can type the destination path relative to your home directory.
If your home directory _is_ the destination, you can leave the destination
field blank, or type `~` -- the shorthand for your home directory -- for
completeness.

With `scp`, a trailing slash on the target directory is optional, and has no effect.
A trailing slash on a source directory is important for other commands, like `rsync`.

## Transferring Data with `rsync`

As you gain experience with transferring files, you may find the `scp`
command limiting. The [rsync] utility provides
advanced features for file transfer and is typically faster compared to both
`scp` and `sftp` (see below). It is especially useful for transferring large
and/or many files and for synchronizing folder contents between computers.

The syntax is similar to `scp`. To transfer _to_ another computer with
commonly used options:

```
{{ site.local.prompt }} rsync -avP local_file {{ site.remote.user }}@{{ site.remote.login }}:
```
{: .language-bash}

The options are:

* `-a` (**a**rchive) to preserve file timestamps, permissions, and folders,
    among other things; implies recursion
* `-v` (**v**erbose) to get verbose output to help monitor the transfer
* `-P` (partial/progress) to preserve partially transferred files in case
  of an interruption and also displays the progress of the transfer.

To recursively copy a directory, we can use the same options:

```
{{ site.local.prompt }} rsync -avP local_dir {{ site.remote.user }}@{{ site.remote.login }}:~/
```
{: .language-bash}

As written, this will place the local directory and its contents under your
home directory on the remote system. If a trailing slash is added to the
source, a new directory corresponding to the transferred directory
will not be created, and the contents of the source directory will be
copied directly into the destination directory.

To download a file, we simply change the source and destination:

```
{{ site.local.prompt }} rsync -avP {{ site.remote.user }}@{{ site.remote.login }}:local_dir ./
```
{: .language-bash}

## Transferring Data with Globus

While `scp` and `rsync` are excellent tools for quick transfers from your local
machine to HPC filesystems, sometimes you need to transfer many gigabytes or even
terabytes of data. In this case, `scp` and `rsync` may not be robust enough to
complete your data transfer; they require a constant connection. If there is an
interruption in your connection then you will need to rerun the command, often
from the beginning to ensure that files were not corrupted during the disconnect.
This is where Globus comes in.

[Globus] is a not-for-profit service developed and operated by the University of
Chicago. Globus allows you to move, share, and access data on any system where the
Globus software is installed. Globus can be installed on your laptop or lab computer,
and many universities and national labs have Globus installed for their core
facilities and HPC systems.

Globus has three main advantages over `scp` and `rsync`:

* Data transfers can be initiated and monitored from any web browser
* One-time or recurring data transfers can be scheduled
* Data transfers will automatically continue where they left off if interrupted

> ## Installing Globus on Your Local Machine
>
> To use Globus to transfer data between your local machine and {{ site.remote.name }},
> you will first need to install Globus on your own computer.
>
> 1. Go to [https://www.globus.org/globus-connect-personal].
> 2. Click the "INSTALL NOW" link for the Globus Connect Personal for your
> operating system (e.g. MacOS, Windows, Linux).
> 3. Follow the installation instructions for your operating system.
{: .callout}

### How to Transfer Data from Your Local Machine to FarmShare

Once Globus Connect Personal is installed on your local machine, you can transfer
data between your computer and {{ site.remote.name }}.

1. In your local machine's web browser, go to [https://app.globus.org/]. You may
need to authenticate with your SUNetID or Cardinal Key first (select "Stanford
University" from the dropdown menu that appears).
2. In the top right-hand corner, click the Panels icon that has two panels
(the middle one).
3. Above the left panel, click the Search field next to "Collection".
4. In the screen that opens, click "Your Collections" and select the collection
that represents your local machine.
5. Navigate to the files/folders you want to transfer and select them.
6. Above the right hand panel, click the Search field next to "Collection".
7. Start typing "{{ site.remote.name }}" and select that collection once it appears.
8. Navigate to the desired destination for your files.
9. Once your source files and desired destination are selected, you can begin
the transfer. Click the "Start" button over the left panel.

> ## Transferring Data to Your Local Machine
>
> The above steps can also be used to transfer data from {{ site.remote.name }} to
> your local machine. Simply select the files you want to transfer from
> {{ site.remote.name }} and the desired destination on your local machine, and then
> click the "Start" button over the right-side "{{ site.remote.name }}" panel.
{: .callout}

## Transferring Data with {{ site.remote.name }} OnDemand

[{{ site.remote.name }} OnDemand] is a web interface to the {{ site.remote.name }}
HPC system. OnDemand allows you to manage your files, access a shell session, and use
interactive apps like JupyterLab, RStudio, and VS Code. We will only discuss OnDemand's
File Manager below, but we will cover the other features in-depth in the next section.

### Managing Files with the {{ site.remote.name }} OnDemand File Manager

To create, edit or move files, click on the Files menu from the Dashboard page.
A drop-down menu will appear, listing your most common storage locations on
{{ site.remote.name }}: $HOME, Class Directories, Group Directories, and $SCRATCH.

Choosing one of the file spaces opens the **File Explorer** in a new browser tab.
The files in the selected directory are listed.

There are two sets of buttons in the File Explorer.

* Under the three vertical dots menu next to each filename:
{% include figure.html url="" max-width="50%"
   file="/fig/file_explorer_btn1.png"
   alt="Connect to cluster" caption="" %}
Those buttons allow you to View, Edit, Rename, Download, or Delete a file.
* At the top of the window, on the right side:
{% include figure.html url="" max-width="50%"
   file="/fig/file_explorer_btn2.png"
   alt="Connect to cluster" caption="" %}

| Button | Function |
| ------ | -------- |
| **Open in Terminal** | Open a terminal window on Sherlock in a new browser tab |
| **Refresh** | Refresh the list of directory contents |
| **New File** | Create a new, empty file |
| **New Directory** | Create a new subdirectory |
| **Upload** | Copy a file from your local machine to Sherlock |
| **Download** | Download selected files to your local machine |
| **Copy/Move** | Copy or move selected files (after moving to a different directory) |
| **Delete** | Delete selected files |
| **Change directory** | Change your current working directory |
| **Copy path** | Copy the current working directory path to your clipboard |
| **Show Dotfiles** | Toggle the display of dotfiles (files starting with a `.`, which are usually hidden) |
| **Show Owner/Mode** | Toggle the display of owner and permission settings |

> ## Working with Windows
>
> When you transfer text files from a Windows system to a Unix system (Mac,
> Linux, BSD, Solaris, etc.) this can cause problems. Windows encodes its files
> slightly different than Unix, and adds an extra character to every line.
>
> On a Unix system, every line in a file ends with a `\n` (newline). On
> Windows, every line in a file ends with a `\r\n` (carriage return + newline).
> This causes problems sometimes.
>
> Though most modern programming languages and software handles this correctly,
> in some rare instances, you may run into an issue. The solution is to convert
> a file from Windows to Unix encoding with the `dos2unix` command.
>
> You can identify if a file has Windows line endings with `cat -A filename`. A
> file with Windows line endings will have `^M$` at the end of every line. A
> file with Unix line endings will have `$` at the end of a line.
>
> To convert the file, just run `dos2unix filename`. (Conversely, to convert
> back to Windows format, you can run `unix2dos filename`.)
{: .callout}

{% include links.md %}
[{{ site.remote.name }} OnDemand]: http://ondemand.farmshare.stanford.edu/
[Globus]: https://www.globus.org/
[https://www.globus.org/globus-connect-personal]: https://www.globus.org/globus-connect-personal
[https://app.globus.org/]: https://app.globus.org/
[git-swc]: https://swcarpentry.github.io/git-novice/
[rsync]: https://rsync.samba.org/
