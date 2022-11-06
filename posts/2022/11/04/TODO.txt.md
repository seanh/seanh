TODO.txt
========

[Todo.txt](http://todotxt.org/) is a bash script for managing a todo list in a plain text `todo.txt` file:

```console
$ todo.sh add Go grocery shopping
1 Go grocery shopping
TODO: 1 added.
$ todo.sh add Feed the cat
2 Feed the cat
TODO: 2 added.
$ todo.sh add Feed the dog
3 Feed the dog
TODO: 3 added.
$ todo.sh ls
1 Go grocery shopping
2 Feed the cat
3 Feed the dog
--
TODO: 3 of 3 tasks shown
```

I find that I can never make the habit of using any other todo app stick, but todo.txt works because it puts my todo list right at my fingertips very immediately whether I'm in a shell or in vim (since you can just open the `todo.txt` file and edit it directly).

If you put the `todo.txt` file in Dropbox then you also get continuous cloud backup and syncing between devices and you can read and edit the file on your phone using the Dropbox app.

Todo.txt doesn't give you much that you wouldn't get by just having a `todo.txt` file that you open in a text editor. It just provides a simple file format to follow and a `todo.sh` script with some convenient commands for working with the `todo.txt` file. There are actually desktop and mobile todo.txt apps but I don't use any of those.

There's no due dates or scheduled or recurring reminders, at least not without add-ons. I use the Reminders app on my phone for a lot of life-admin reminders and use that app's scheduled and recurring reminders. I use todo.txt for keeping track of my while-at-the-computer work tasks, and I don't need scheduled or recurring reminders for that.

This post is my quick-start guide to installing, configuring and using todo.txt.

Installation
------------

You can install todo.txt with [Homebrew](https://formulae.brew.sh/formula/todo-txt), [apt](https://packages.ubuntu.com/jammy/todotxt-cli), [pacman](https://aur.archlinux.org/packages/todotxt) or [make](https://github.com/todotxt/todo.txt-cli#linux), but it's just a [single-file bash script](https://github.com/todotxt/todo.txt-cli/blob/master/todo.sh) so the easiest way to install todo.txt is just to download a tarball of the [latest release](https://github.com/todotxt/todo.txt-cli/releases) and extract the `todo.sh` and `todo.cfg` files into a directory.

I place both files in a `TODO.txt` folder in my [Dropbox](https://www.dropbox.com/) folder	and add that `TODO.txt` dir to my shell's `PATH` (for [fish](https://fishshell.com/) just add `fish_add_path ~/Dropbox/TODO.txt` to your `config.fish` file). This way todo.txt gets automatically "installed" on any computer that I have Dropbox installed on. Just make sure the `todo.sh` script is marked as executable: `chmod u+x ~/Dropbox/TODO.txt/todo.sh`.

The downside of this approach is you won't automatically get upgraded if they release a new version. But new versions of todo.txt are rarely released, and you can subscribe to their [GitHub releases feed](https://github.com/todotxt/todo.txt-cli/releases.atom) and manually update your `todo.sh` file if they do release one.

Configuration
-------------

The `todo.cfg` file that you placed in your `Dropbox/TODO.txt` folder is todo.txt's config file. According to `todo.sh help` this file is supposed to be located at `~/.todo/config` but `todo.sh` actually looks for the config file in [a number of locations](https://github.com/todotxt/todo.txt-cli/blob/ea32af34e6a0efef59bdf9acaa1c38d5d32bef0d/todo.sh#L693-L700) including a `todo.cfg` file in the same directory as the `todo.sh` script, so placing `todo.cfg` alongside `todo.sh` in `Dropbox/TODO.txt` works.

[The default config file](https://github.com/todotxt/todo.txt-cli/blob/master/todo.cfg) documents many of the settings that you can use in `todo.cfg` and there's also [some more listed in `todo.sh`](https://github.com/todotxt/todo.txt-cli/blob/ea32af34e6a0efef59bdf9acaa1c38d5d32bef0d/todo.sh#L141-L156).

Here are the settings that I use:

```bash
export TODO_DIR=$(dirname "$0")

export TODO_FILE="$TODO_DIR/todo.txt"
export DONE_FILE="$TODO_DIR/done.txt"
export REPORT_FILE="$TODO_DIR/report.txt"
export TODO_ACTIONS_DIR="$TODO_DIR/actions"

export TODOTXT_AUTO_ARCHIVE=0
export TODOTXT_DEFAULT_ACTION=ls
export TODOTXT_PRESERVE_LINE_NUMBERS=1
export TODOTXT_SORT_COMMAND='sort'
```

The `export TODO_DIR=$(dirname "$0")`, which comes from the default `todo.cfg` file, sets `$TODO_DIR` to the directory containing the `todo.sh` script, i.e. the `Dropbox/TODO.txt` folder. So the four lines that follow configure `todo.sh` to look for its `todo.txt` file, `done.txt` file, `report.txt` file and `actions` folder all in the `Dropbox/TODO.txt` folder.

`export TODOTXT_SORT_COMMAND='sort'` makes `todo.sh` print out todo list items in the same line order as they appear in the `todo.txt` file. By default it seems to print them rearranged into alphabetical order which I thought was a bit odd, especially because I like to manually arrange the tasks in the file into the order that I intend to do them in.

`export TODOTXT_DEFAULT_ACTION=ls` means that just running `todo.sh` with no action will list all tasks instead of printing the help.

`export TODOTXT_AUTO_ARCHIVE=0` and `export TODOTXT_PRESERVE_LINE_NUMBERS=1` prevent the `todo.sh do` and `todo.sh rm` commands from changing the line numbers of the remaining uncompleted tasks, which can be confusing and lead to mistakes.

Usage
-----

### Adding tasks

Use `todo.sh add <task>` to add a task to your todo list:

```console
$ todo.sh add Go grocery shopping
1 Go grocery shopping
TODO: 1 added.
```

This can also be shortened to `todo.sh a <task>`:

```console
$ todo.sh a Take out the trash
2 Take out the trash
TODO: 2 added
```

There's also an `addm` command for adding multiple tasks at once. You have to start and end the multiline argument with quotes:

```console
$ todo.sh addm "Go grocery shopping
       Take out the trash
       Feed the dog"
3 Go grocery shopping
TODO: 3 added.
4 Take out the trash
TODO: 4 added.
5 Feed the dog
TODO: 5 added.
```

You can use `-t` to prepend the creation date to a task when adding it:

```console
$ todo.sh -t add Feed the dog
4 2022-11-04 Feed the dog
TODO: 4 added.
```

Or put `export TODOTXT_DATE_ON_ADD=1` in your `todo.cfg` to have it always prepend the date when adding tasks.

### Listing tasks

Use `todo.sh list` or `ls` to print out your todo list:

```console
$ todo.sh ls
1 Go grocery shopping
2 Take out the trash
--
TODO: 2 of 2 tasks shown
```

The `ls` command takes an optional list of filter terms that can be used to filter which tasks are printed:

```console
$ todo.sh ls grocery
1 Go grocery shopping
--
TODO: 1 of 2 tasks shown
```

If multiple filter terms are given then only tasks that match *all* the terms are printed. You can print tasks that contain *either* `TERM1` or `TERM2` with `ls TERM1\\|TERM2`. You can print tasks that *don't* contain `TERM` with `ls -TERM`.

### The `todo.txt` file

The todo list is stored in a plain text file that you can can read or edit in any text editor. The location of the file is determined by the `TODO_FILE` setting in `todo.cfg`. The file format is simply one task per line:

```console
$ cat ~/Dropbox/TODO.txt/todo.txt
Go grocery shopping
Take out the trash
```

Completed tasks are lines that begin with `x `. Task lines can also contain optional priorities, completed dates, creation dates, projects, contexts, and key:value tags. The rest of this post explains what all these features are and how to use them. Here's an example task line from [the TODO.txt docs](https://github.com/todotxt/todo.txt/blob/master/README.md) showing all the features:

    x (A) 2016-05-20 2016-04-30 measure space for +chapelShelving @chapel due:2016-05-30

From left to right:

1. An optional leading `x ` marks a task as completed.
2. If a task is not completed then it may optionally start with a parenthesised capital letter to assign a priority to a task from `(A)` (highest) to `(Z)` (lowest). Completed tasks can't have priorities, the `t do` command removes any priority from the task's line.
3. For completed tasks, after the leading `x ` a date in `YYYY-MM-DD` format specifies the task's completion date.
4. For uncompleted tasks, after any optional priority a date in `YYYY-MM-DD` format specifies the task's _creation_ date. A completed task can have both a completed date and a creation date by having two dates one after another (completed date first).
5. One or more project tags like `+chapelShelving` can appear anywhere in the task's description.
6. One or more context tags like `@chapel` can appear anywhere in the task's description.
5. One or more key:value tags like `due:2016-05-30` can appear anywhere in the task's description. `todo.sh` doesn't do anything special with key:value tags but custom actions can use them to add new features.

See [todo.txt's docs](https://github.com/todotxt/todo.txt/blob/master/README.md) for full documentation of the file format.

### Completing and archiving tasks, and the `done.txt` file

Use `todo.sh done` or `do` to mark a task as done, using the line number to identify the task to mark:

```console
$ todo.sh do 2
2 x 2022-11-04 Take out the trash
TODO: 2 marked as done
```

You can give multiple space-separated numbers to complete multiple tasks at once. As well as marking the task as completed by prepending a leading `x `, `todo.sh do` will also remove any priority and insert the completion date.

By default completing a task also archives it: moves the task from `todo.txt` into `done.txt`, which means it'll no longer show up in the output of `ls`. I don't like auto-archiving because it can change the line numbers of the remaining tasks. This can be confusing because line numbers shown in any previous `ls` output in your terminal session may no longer be correct. For example if you type another `todo.sh do <NUMBER>` command based on a line number that you can still see in the output from a previous `ls` command then you might complete the wrong task. You can disable auto-archiving with with the `-a` argument: `todo.sh -a do <NUMBER>`, or with `export TODOTXT_AUTO_ARCHIVE=0` in your `todo.cfg` file.

With auto-archiving disabled completed tasks will still be in `todo.txt` but just marked as done. They'll still show in the output of `todo.sh ls`:

```console
$ todo.sh
1 Go grocery shopping
2 x 2022-11-04 Take out the trash
--
TODO: 2 of 2 tasks shown
```

To get rid of them you can use the `todo.sh archive` command, which moves completed tasks from `todo.txt` into `done.txt` (and does change the line numbers of the remaining tasks in `todo.txt`):

```console
$ todo.sh archive
x 2022-11-04 Take out the trash
TODO: /Users/seanh/Dropbox/TODO.txt/todo.txt archived.
$ todo.sh ls
1 Go grocery shopping
--
TODO: 1 of 1 tasks shown
```	

The `todo.sh listall` or `lsa` command list all tasks from both `todo.txt` and `done.txt`. `lsa` takes the same filter term arguments as `ls` does.

`todo.sh listfile done.txt` or `lf done.txt` will list only the tasks from your `done.txt` file and again accepts filter term arguments.

### Deleting tasks

Instead of marking an item as done you can delete it with `todo.sh del` or `rm`. This will simply delete the line from your `todo.txt` file:

```console
$ todo.sh rm 2
Delete 'Feed the cat'?  (y/n)
y
2 Feed the cat
TODO: 2 deleted.
```

By default `rm` leaves a blank line where the deleted line was, so that the line numbers of the remaining lines don't change. A subsequent `todo.sh archive` will delete any blanks lines from `todo.txt` as well as moving any done tasks into `done.txt`. If you don't want a blank line to be left behind you can pass the `-n` argument to `todo.sh -n rm <line_number>` or put `export TODOTXT_PRESERVE_LINE_NUMBERS=0` in your `todo.cfg`.

### Editing tasks

The easiest way to edit existing lines in your `todo.txt` file is just to open it in a text editor. But `todo.sh` does provide some commands for editing tasks:

* `todo.sh rm <line_number> <term>` removes all instances of `<term>` from the task on the given line number
* `todo.sh prepend <line_number> <text_to_append>` prepends text to the front of a task
* `todo.sh append <line_number> <text_to_append>` appends text to the end of a task
* `todo.sh replace <line_number> <new_task>` completely replaces the task on the given line number with the new text

### Priorities

You can assign a priority to a task with `pri` or just `p`. A priority is just a parenthesised capital letter at the beginning of the line, from `A` (highest) to `Z` (lowest):

```console
$ todo.sh pri 3 A
3 (A) Feed the dog
TODO: 3 prioritized (A).
```

`ls` prints prioritised items in colour and bold. To change the priority of an item just run `pri` again with a different priority. To remove the priority from an item run `depri` or `dp`:

```console
$ todo.sh depri 3
3 Feed the dog
TODO: 3 deprioritized.
```

`listpri` or `lsp` lists all prioritised tasks only. Or `lsp A` to list only priority `A` tasks, `lsp A-C` to list priority `A`, `B` or `C` tasks. `lsp` can also take the same term arguments as `ls` does to further filter the tasks.

### Projects and contexts

You can add one or more projects (e.g. `+garage_cleaning`) and contexts (the place and situation where you'll work on a task, e.g, `@home`) to a task by just using `+<project>` or `@<context>` anywhere in the task:

```console
$ todo.sh add Put away tools +garage_cleaning @home
```

`ls` can filter on projects and contexts like any other term, for example: `todo.sh ls +garage_cleaning` or `ls @home`. Or use `-+garage_cleaning` or `-@home` to list only tasks that _don't_ have a project or context.

`listproj` or `lsprj` lists all the projects in `todo.txt` (not to be confused with `lsp` which lists prioritised tasks). `listcon` or `lsc` lists all contexts.

### Multiple todo files

You can have multiple todo files in your `TODO.txt` folder and use `addto` to add tasks to a file other than `todo.txt`, for example a `somedaymaybe.txt` file. You have to create the file yourself first, `todo.sh` won't create the file for you (`touch Dropbox/TODO.txt/somedaymaybe.txt`). Then:

```console
$ todo.sh addto somedaymaybe.txt Water the plants
1 Water the plants
SOMEDAYMAYBE: 1 added.
```

To list the contents of a file other than `todo.txt` use `listfile` or `lf`:

```console
$ todo.sh lf somedaymaybe.txt
1 Water the plants
--
SOMEDAYMAYBE: 1 of 1 tasks shown
```

If you run `lf` with no arguments it'll list the available files for you.

You can move items between files with `move` or `mv`:

```console
$ todo.sh mv 2 somedaymaybe.txt
Move 'Feed the dog' from Dropbox/TODO.txt/todo.txt to Dropbox/TODO.txt/somedaymaybe.txt? (y/n)
y
2 Feed the dog
TODO: 2 moved from 'Dropbox/TODO.txt/todo.txt' to 'Dropbox/TODO.txt/somedaymaybe.txt'.
```

Or pass two filename arguments to `mv` to move a task from another file back into `todo.txt` (or to move a task between two arbitrary files):

```console
$ todo.sh mv 2 todo.txt somedaymaybe.txt
Move 'Feed the dog' from Dropbox/TODO.txt/somedaymaybe.txt to Dropbox/TODO.txt/todo.txt? (y/n)
y
2 Feed the dog
TODO: 2 moved from 'Dropbox/TODO.txt/somedaymaybe.txt' to 'Dropbox/TODO.txt/todo.txt'.
```

Confusingly **the destination file comes first**: `todo.sh mv 2 todo.txt somedaymaybe.txt` moves line 2 from `somedaymaybe.txt` into `todo.txt` not the other way round.

As far as I can tell commands like `do`, `rm`, `append`, `prepend`, `replace`, `archive`, `pri`, `depri` and others only work with `todo.txt`. There seems to be no way to operate on other files. I think you're supposed to use `lf` to list the contents of files and then `mv` to move tasks into `todo.txt` before operating on them. Or edit the files directly in a text editor.

Custom actions
--------------

`todo.sh` add-ons can add new commands or override existing commands. Add-ons are just executable files in the `TODO_ACTIONS_DIR` set in your `todo.cfg`, written in any language. To install an add-on you just download the script file into your `$TODO_ACTIONS_DIR` and mark it executable. Then if you run `todo.sh foo` it'll execute the script at `$TODO_ACTIONS_DIR/foo`, passing any command line args to the script.

See [Creating and Installing Add ons](https://github.com/todotxt/todo.txt-cli/wiki/Creating-and-Installing-Add-ons) in the `todo.sh` wiki for how to install add-ons or create your own, and the [Todo.sh Add on Directory](https://github.com/todotxt/todo.txt-cli/wiki/Todo.sh-Add-on-Directory) for a list of add-ons to download.

A few add-ons that I like are:

* [due](https://github.com/rebecca-owen/due): uses `due:YYYY-MM-DD` tags to list tasks that are due today or are overdue.

* [edit](https://github.com/todotxt/todo.txt-cli/wiki/Todo.sh-Add-on-Directory#edit-open-in-text-editor): adds a `todo.sh edit` command that opens your `todo.txt` file in your `$EDITOR`. Or `todo.sh edit <filename_without_extension>` to open another file, e.g. `todo.sh edit done`.

* [note](https://github.com/mgarrido/todo.txt-cli/tree/note/todo.actions.d): uses key:value tags to let you associate multi-line notes (stored in separate text files) with tasks.

  Notes are stored in a `notes` subdir in the same directory as your `todo.txt` files, one note file per task. The note files have short, randomly generated IDs for filenames and when you attach a note to a task a `note:<filename>.txt` tag is appended to the task's `todo.txt` line to record which note belongs to the task.

  It's a good idea but the implementation seems poor: it has to override the built-in `archive`, `del` and `rm` commands which could clash with any other plugin that overrides those commands. And it makes you use separate `note add` and `note edit` commands instead of a single command that adds a note to a task or edits the task's existing note.

  `todo.sh add <n>` adds a note to task `n` and opens your `$EDITOR` to let you write the note. `todo.sh edit <n>` edits task `n`'s note in your `$EDITOR`. `todo.sh note show <n>` prints out task `n`'s note.

Accessing your todo list on your phone
--------------------------------------

If you keep your todo.txt files in Dropbox then you can access them using the Dropbox app on your phone. I don't use any particular TODO.txt app on my phone. I just use the Dropbox app's built-in ability to view and edit plain text files.

Fish shell integration
----------------------

You'll want to add `fish_add_path ~/Dropbox/TODO.txt` to your `config.fish` file so that you can run the `todo.sh` command directly without having to type out the full path every time.

In addition I also add a couple of other todo.txt integrations into my fish config:

1. I "abbreviate" `todo.sh` as `t` so I can just type <kbd>t</kbd> <kbd>Enter</kbd> to print my todo list (since I also have `export TODOTXT_DEFAULT_ACTION=ls` in `todo.cfg`) or <kbd>t</kbd> <kbd>Space</kbd> to begin entering a `todo.sh` command:

   ```fish
   abbr t todo.sh
   ```

2. I add the number of uncompleted tasks in my `todo.txt` file to my fish shell prompt. Here's the relevant bit of fish script from my `fish_prompt.fish` file:

   ```fish
   set todo_txt_file ~/Dropbox/TODO.txt/todo.txt
   if test -e $todo_txt_file
     set num_tasks (grep . $todo_txt_file | grep --count --invert-match '^x ')
     if test $num_tasks -gt 0
       set_color --bold yellow
       printf "{%s} " $num_tasks
       set_color normal
     end
   end
   ```

   This adds an `{n}` to my prompt where `n` is the number of uncompleted tasks in my `todo.txt` file. If there are no uncompleted tasks it doesn't add anything to my prompt.

Vim integration
---------------

I put this in my `vimrc` file to bind <kbd>Leader</kbd> <kbd>t</kbd> to open my `todo.txt` file from within vim:

```vimrc
nnoremap <leader>t :e ~/Dropbox/TODO.txt/todo.txt<Enter>
```

There's also a couple of todo.txt plugins for vim but they look like they do a lot more than I'd want: lots of keybindings for editing tasks, sorting, etc. Maybe I'll make a simple vim plugin one day that just does todo.txt syntax highlighting.
