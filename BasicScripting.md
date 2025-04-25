# Linux Commands and Features

- [Basic commands](#Basic-commands)
- [Streams and Redirection](Streams-and-Redirection)
- [if Statements](if-Statements)
- [Regular Expressions](Regular-Expressions)
- [Vim](#vim)
- [Task Autoanmtion](#task-automation)
- [Git](#git)


## Basic commands

* `man` - manual
  * find info on commands
  - Man pages may have multiple sources, indicated by the `(#)` in the corner.
  - Sections are identified by their number:
    1. General commands, end-user commands  
    2. System calls  
    3. C library functions  
    4. Special files  
    5. Config files
    6. Games and screensavers  
    7. Misc  
    8. System commands, root commands
  - **Search**: Press `/` and type your query to search within a man page.
* **Reading the man pages**
  * `[ ]` = optional 
  * `...` = multiple arguments can be passed
  * `|` = exclusive or. It is either or 
  * `{ | }` = same as `|`
* **Searching the Man Pages:**
  * There are tools to help find the command when you do not know which one to use, or forgot the same
  * They use the man database which indexes the man through a daily cron job 
    * **`mandb`** - updates the database manually 
  * Tools finding commands:
    - **`man -k`** - Search the name section of all man pages (great for finding commands).
      * This is exactly the same as **`apropos`**
    - **`whatis`**: Display a brief description of a command.
    - **`info`**: Display the info page of a command.

- **`cp`**
  - `*` the wildcard of wildcards, it's used to represent all single characters or any string.
  - `?` used to represent one character
  - `[]` used to represent any character within the brackets

- **`mv`**
  - `mv -i dir1 dir2` - overwrite anything in the same directory. So you can use the -i flag to prompt you before overwriting anything
  - `mv -b directory1 directory2` -  make a backup of that file and it will just rename the old version with a ~.

- **`mkdir`**
  - `-p` - include the parent directories if not existing











**File Text Operations:**
- **`ls`**
  - `ls -lrt` - list files in long format, sorted by time, oldest first

- **`expand / unexpand`**
  - Convert tabs to 8 spaces and vice versa
  - Example: `expand file > file2`.

- **`wc`** - word count
  * Count lines, words, and bytes in a file.

- **`fmt`**: Format text.

- **`head / tail`**: Display the first or last lines of a file.
  - `-n`: Specify the number of lines (default: 5).

- **`join`**: Join two files based on a common field.

- **`nl`**
  * Similar to `cat` but adds line numbers

- **`od`** - Octal dump of a file

- **`pr`** - Format files for printing.

- **`split`**
  * Split a file (default: 1000 lines per split).
  - Example: `split file newfile`.
- **`split`**: Split a file (default: 1000 lines per split).
  - Example: `split file newfile`.

- **`tr`** - truncate / translate
  *  Performs operations like removing repeated characters, converting uppercase to lowercase, and basic character replacement and removal
  * `tr <char 1> <char 2>` - first char is the character to be replaced

- **`uniq`**: Remove duplicate adjacent lines.
  - Use `sort` to make duplicates adjacent before using `uniq`.

- **`sort`**
  * Tool for sorting lines of text files
  * Options:
    * `-k <column numbers>` - Specify field values
    * `-k2` - Indicate second field
    * `-n`  - Compare and sort line based on the string numerical value
    * `-r`  - Sort fields in descending order
    * `-t`  - Separate one field from another

- **`cut`**: Extract portions of a file.
  - `-c` - Extract specific characters (e.g., `cut -c 3-5 file`).
  - `-d` - Specify delimiter, e.g. `-d:`
  - `-f` - Specify field to extract (e.g., `cut -d, -f2 file.txt`). 

- **`paste`** - Merge lines of files.
  * Merge lines from text files horizontally
  * By default the delimeter is a tab
  * Options:
    * `-d` - Specify delimeter

- **`expr`**: Perform mathematical operations.
  - Example: `expr 5 \* 3`.


* **`find`**
  * Some interesting options:
    * `-name` - find files based on the file name
    * `-user` - find files owned by a user
    * `-size <-|+ # G|M|k|c>` - find files smaller or larger than # gib, mib,kib, bytes 
    * `-exec {} <command> \;` - use the results of the `find` command as the input `{}` for another command. Piping does not work here, although you can use `| xargs`... 
    * `-perm <e.g. \<e.g. 4000>` - find files based on their permissions. 
    * `-type` - find files based on their file type. Some of the options: [b]lock, [d]irectory, regular [f]ile, sym [l]ink
    * `-ls` - equivalent of `ls -l`. List all the files that were specified by the other options, but list hte in the long format 
    * `-printf` - specify the output format     
      * This option is quite extensive. You need to completely make up the entire output.
      * tip: end with a newline, `\n`, so the output is not one ginormous string
    * 


* **Environment and Basics**
  - **`env`**: Display user environment variables (temporary, only for the session).
  - **`pwd`**: Print working (current) directory.
  - **`$HISTFILE`**: Path to the history file (e.g., `~/.bash_history`).
    - View history: `cat ~/.bash_history` or use the `history` command.
    - Export session history:
      ```bash
      export HISTFILE=<path>
      ```
    - Defaults back after logout.


---

## Stream manipulation
- **Streams**:
  - `STDIN` (0): Input.
  - `STDOUT` (1): Output.
  - `STDERR` (2): Error.

- **Redirection**:
  - `>`: Redirect output to a file (overwrites existing content).
    ```bash
    command > file
    ```
  - `>>`: Append output to a file.
    ```bash
    command >> file
    ```
  - `<`: Redirect a file as input to a command.
    ```bash
    command < file
    ```
  - `2>`: Redirect errors to a file.
    ```bash
    command 2> error.log
    ```
  - Redirect both `STDOUT` and `STDERR`:
    ```bash
    command > file 2>&1
    ```


- **`xargs`**: Process input line by line.
  - Example:
    ```bash
    find . -name "*.txt" | xargs rm
    ```

- **`tee`**: Redirect output to a file while also displaying it.
  - Example:
    ```bash
    ls | tee output.txt
    ```


* **`grep`** - Globally Regular Expression and Print
  - Uses symbols and regular expressions to search plain text.
  - **`grep`**: Basic search.
  - **`egrep`**: Extended `grep`, allowing regular expression quantifiers without escaping.
  - **`fgrep`**: Literal search; no interpretation of special characters.
  - **`pgrep`** - get PID for a running process

  - `grep <pattern> *`: Search files in the current directory.
  - `grep -v 'e$' *`: Find strings that do not end with "e".
  - `egrep 'l+' *`: Match one or more occurrences of "l" without escaping.
  - `fgrep 'l\+' *`: Search for a literal `\` and `+`.


* `sed` - Stream Editor
  - Replace characters in a regular expression.
  - Syntax:
    ```bash
    sed 's/<pattern>/<replacement>/' <file>

* `awk` - pattern scanning and text processing language





---

## `if` Statements

Conditional expressions in shell scripting allow you to evaluate strings, files, and directories. Below are some commonly used conditions.


**String Conditions:**
- **`-z <string>`**:  
  True if the length of `<string>` is zero.
- **`-n <string>`**:  
  True if the length of `<string>` is non-zero.
- **`<string1> = <string2>`**:  
  True if `<string1>` is equal to `<string2>`.
- **`<string1> != <string2>`**:  
  True if `<string1>` is not equal to `<string2>`.
- **`<string>`**:  
  True if `<string>` is not empty.


**File and Directory Conditions:**
- **`-e <file>`**:  
  True if `<file>` exists.
- **`-f <file>`**:  
  True if `<file>` exists and is a regular file.
- **`-d <directory>`**:  
  True if `<directory>` exists and is a directory.
- **`-x <file>`**:  
  True if `<file>` exists and is executable.
- **`-s <file>`**:  
  True if `<file>` exists and has a size greater than zero.



---

## Regular Expressions
Regular expressions (regex) are sequences of symbols and characters used to express patterns for searching text.

**Symbols:**
- `|`: OR  
  Example: `grey|gray` matches either "grey" or "gray".
- `()`: Grouping  
  Example: `gr(e|a)y` matches "grey" or "gray".
- `$`: End of line  
  Example: `e$` matches any line ending with "e".
- `^`: Start of line  
  Example: `^Hello` matches lines starting with "Hello".
- `.`: Matches any single character.

**Quantifiers:**
- `?`: 0 or 1 occurrence of the preceding element.
- `*`: 0 or more occurrences of the preceding element.
- `+`: 1 or more occurrences of the preceding element.
- `{n}`: Matches the preceding element exactly `n` times.
- `{min,}`: Matches the preceding element at least `min` times.
- `{min,max}`: Matches the preceding element between `min` and `max` times.

---



## vim 
* `vim` is a powerful text editor in Linux with two primary modes: **Command Mode** and **Insert Mode**.

* Vim has two **modes**
  1. **Command Mode**
    - Used for navigating and manipulating text without typing.
    - Key bindings control movement and text actions.
  2. **Insert Mode** 
    - Used for directly editing text.
    - Entered using specific keys (e.g., `i`, `a`, `o`) from Command Mode.
    - Leave insert mode by pressing `esc`

* **Navigation in Command Mode:**
  - **`h`** - Move left.
  - **`l`** - Move right.
  - **`j`** - Move down.
  - **`k`** - Move up.
  - **`/`** - Search forward.
    - Use **`n`** for the next occurrence.
  - **`?`** - Search backward.
    - Use **`n`** for the next occurrence.
  - **`Shift+g`** - Move to the end of the file.
  - **`w`** - Go to next word
  - **`b`** - Go to previous word
  - **`^`** - Move to start of current line 
  - **`$`** - Move to end of current line 

**Editing in Command Mode:**
  * **Deleting Text:**
    - **`x`** - Delete the character under the cursor.
    - **`dd`** - Delete the current line
      - **`3dd`** - Delete 3 lines
    - **`dw`** - Delete the current word
  * **Pasting Text**
    - **`p`** - Paste below the current line
    - **`P`** - Paste above the current line
  * **Entering Insert Mode:**
    - **`i`** - Insert before the cursor.
    - **`a`** - Insert after the cursor.
    - **`o`** - Insert a new line below the cursor position.
    - **`O`** - Insert a new line above the cursor position.
  * **Saving and Exiting:**
    - **`:q`** - Exit without saving.
    - **`:q!`** - Force exit without saving.
    - **`:w`** - Save the file.
    - **`:wq!`** - Save and exit, no complaining!
    - **`Shift+ZZ`** - Save and exit.

* **Simple text manipulation**
  * **`y`** - copy
  * **`p`** - paste
  * **`u`** - undo 
  * **`ctrl+r`** - Redo
 

* **Additional Commands:**
  * **`/`** - Search forward for a term.
  * **`?`** - Search backward for a term.
  * **`n`** - Jump to the next search result.
  * **`N`** - Jump to the previous search result.
  * **`v`** - enter visual mode, use arrow keys to select a block 
  * **`:%s/old/new/g`** - replace all occurrences of `old` with `new`
  * **`/<text>`** - search for text 
  * **`:se number`** - show line numbers

  * `vimtutor` - command for opening the vim learning module 

## Task Automation 

* `at`
  - Execute commands at a specified time by reading them from `STDIN` or a file.
  - All jobs scheduled to run at the same time are executed simultaneously.
  - Great tool for **one-time** tasks
  - `at <options> <time>`
    - `-m` - send mail to user
    - `-M` - prevent sending mail to user
    - `-f <file name>` - Execute a file 
    - `-t <time>` - Run the job at the specified time
  - Time can be given in words and text
    - `Noon`
    - `Teatime` - 4 PM
    - `Midnight`
    - `3 minutes from now`
    - `1 hr from now`
  - `echo "command" | at -m 1000`: Schedule a command to execute at 10 AM.
    - The result will be mailed to the user. If no mail server is available, check logs in `/var/log/syslog`.
  - `atq`: Check the list of scheduled jobs
  - `atrm <job_id>`: Remove a scheduled job
  - **Access Control**:
    - `/etc/at.deny`: Users listed here cannot use `at`
    - `/etc/at.allow`: Users listed here can use `at`. Overrides `at.deny` if present.

* `batch`
  - Similar to `at`, but jobs are executed only if the system load drops below 1.5 or a specified threshold set in `atd`.
  - Jobs are executed sequentially, not simultaneously.

* **Cron**
  - Schedules recurring jobs at specified times and intervals.
  - Best for repetitive tasks
  - Works by command `crontab <options> <file / user>`
    - `-e` - Edit or create scheduled jobs.
    - `-l` - List current cron jobs.
    - `-r` - Delete current crontab file
    - `-u` - Create crontab file for the specified user
  - **Access Control**:
    - `/etc/cron.allow`: List of users who can use cron. Overrides `/etc/cron.deny`.
    - `/etc/cron.deny`: List of users who cannot use cron.
  - **Configuration**:
    - Similar access rules as `at`.










## git

### **git basics**
* `git init`- initialize a git repository or reinitialize an existing one.

* `git add`
  *  `<file>` - Add file contents to the staging area

* `git commit`
  * `-m "<text>"` - Record changes to the repository.
  * each commits receives a unique hash with which it can be recognized
  * after the first commit, all commits are related to the previous commit

* `git log` - see commit history
  * `--oneline` - make it compact
  * `HEAD -> main, origin/main` - 

* `git status` - Show the changes to files in a Git repository.
  * `--verbose --verbose` - see information on changes in both the statging are and working directory

### **configuring git:**
* `git config --global` - configure git: user info, default text editor
  * `user.name "<name>"`
  * `user.email "<email>"`
  * `core.editor <e.g. nano, vim, code --wait>` - configure default text editor

* `.gitignore`
  * mention all files that should be ignored from tracking
    * e.g. `.env`
 
* `~/.gitconfig`
  * config file for git
  * contains user, email, text editor, signin key 

* `.git` - inside each init directory.
  * **key directories:**
    * **`refs/`** → Stores branch (`refs/heads/`) and tag (`refs/tags/`) pointers.  
    * **`objects/`** → Stores commit, tree, and file data using SHA-1 hashes.  
    * **`logs/`** → Tracks branch updates and movements.  
    * **`hooks/`** → Contains scripts for automation (e.g., pre-commit checks).  
    * **`info/`** → Holds repository-specific settings (e.g., ignored files).  
  * **key files:**
    - **`COMMIT_EDITMSG`** → Stores the last commit message.  
    - **`config`** → Repository settings (overrides global `~/.gitconfig`).  
    - **`description`** → Used by GitWeb, not relevant for local repos.  
    - **`HEAD`** → Points to the currently checked-out branch.  

### **git branching and merging**
* `git branch` - by default it shows existing branches
  * branches are like timelines that can diverge and converge
  * `git branch <name>` - create a new branch
  * `-m <oldname> <newname>` - move or rename a branch
  * When in doubt, keep an eye on the git folder to see where HEAD is pointing to

* `git checkout`
  * `git checkout <name>` - switch to another branch
  * `git checkout -b <name>` - create a branch and move there
  * `git checkout <hash>` - move to a specific commit using its hash
  * `git checkout HEAD~#` - go n# commits back 
    * `git reflog` - go back to the previous place where you were  

* `git merge` - merge two branches
  * `git checkout master` - then merge from there
  
### **git conflicts:**
  * resolving must be done manually in the code editor
  * the conflict is shown highlighted in green and blue 
  * Green - branch that you are on
  * Blue - conflict that came from another branch (merge)
  * Keep what you want, **remove markers** and save 

### Managing changes
* `git diff` - differences
  * compare a file between different commits or between different branches
  * symbols indicate how files have changed
    * `------` is an indication for file 1
    * `++++++` is an indication for file 2
  * `git diff --staged` - compare between the latest commit file and the staged file  
  * `git diff <commit id1>..<commit id2>` - compare two commits

* `git stash` - Stash local Git changes in a temporary area.
  * `git stash` is about saving your work temporarily
  * it can also be a solution for when **conflicts** arise due to having uncommited changes in an existing branch and existing file 
  * **Walkthrough**:
    * Be on any branch (e.g., main).
    * Modify an existing file (a file that has already been committed).
    * Try to switch to another branch (e.g., bugfix).
    * If the changes in your working directory conflict with the target branch, Git will prevent you from switching branches unless you either:
      * Commit the changes.
      * Stash the changes using git stash.
  * `git stash pop` - bring the changes back
    * it works also between branches
  * `git stash list` - list all stashes
  * `git stash apply stash@{#}` - choose which stash to apply
  * 
    * 


### git history and rewrites
* `git rebase` 
  * Commonly used to "move" an entire branch to another base, creating copies of the commits in the new location
    * `git rebase <new_base_branch>` - Rebase the current branch on top of another specified branch
  * `git rebase -i HEAD~#` - reapply commits for the last `n#` commits. This is good for squashing commits and cleaning up the history
    * after going this route, run `git push origin HEAD:<branch> --force` to push 
  

* `git reset` - Undo commits or unstage changes
  * `<path/to/file1> <path/to/file2>` - unstage files
  * can do a quick and dirty variant of what git rebase does with uncommiting commits
    * `git reset --soft HEAD~#` 
      * after this, make the new commit and this will replace all #-amount commits previously done
      * after making the final commit, run: `git push --force` to apply the changes 
      * don't do this on branches where you collaborate


### remote repos - e.g. github
* `git remote` - manage set a tracked repos
  * `git remote -v` - check the current connection with remote repo
  * `git remote add origin https://github.com<username>/<repo-name>.git` - Add a remote named <name> for the repo at <URL>
    * this is done during the initial push after initializing the repo
    * 'origin' is the remote branch
  * `git remote rename <oldname> <newname> - change the remote branch name. By default it is 'origin' and this can be changed

* `git push` - push code to the remote repo
  * `git push --set-upstream origin <name>` - link the remote to the current branch. This is done as the initial push, after which it is remembered
    * `git push -u <remote-repo> <local-repo>` - `-u` is synonym for `--set-upstream` 
    * 

* `git pull` - fetch branch from a remote repo

* `git fetch` - Download objects and refs from a remote repo
  * it brings it into the local repo
  * not into the working area, like `git pull`
  * `git pull` = `git fetch` + `git merge`


* `gh repo` - github specific commands
  * `gh repo create <repo-name> --public` - Create a github repo
  * `gh repo delete <repo-name> --yes` - Delete github repo












- `set` - set shell options     
    - `-e` - exit on fail 
    - `-o` - Set a option 
        - Noclobber 
        - Do not permit overwriting when using `>` 
        - `xtrace` 
        - Shows full command 
        - So for `ls` it shows `ls --colour=auto` 
        - `+o <option>` 
         

 




