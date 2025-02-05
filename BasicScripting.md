# Linux Commands and Features

- [Basic commands](#Basic-commands)
- [Streams and Redirection](Streams-and-Redirection)
- [if Statements](if-Statements)
- [Regular Expressions](Regular-Expressions)
- [vim](#vim)
- [git](#git)

## Basic commands
- **`cp`**
  - `*` the wildcard of wildcards, it's used to represent all single characters or any string.
  - `?` used to represent one character
  - `[]` used to represent any character within the brackets

- **`mv`**
  - `mv -i dir1 dir2` - overwrite anything in the same directory. So you can use the -i flag to prompt you before overwriting anything
  - `mv -b directory1 directory2` -  make a backup of that file and it will just rename the old version with a ~.

- **`mkdir`**
  - `-p` - include the parent directories if not existing


- **`find`**
  - s





**Man Pages:**

- Man pages may have multiple sources, indicated by the `(x)` in the corner.
- Sections are identified by their number:
  1. General commands  
  2. System calls  
  3. C library functions  
  4. Special files  
  5. File formats and conventions  
  6. Games and screensavers  
  7. Misc  
  8. System admin commands/daemons  

**Searching the Man Pages:**
- **Search**: Press `/` and type your query to search within a man page.
- **`apropos`**: Search the name section of all man pages (great for finding commands).
- **`whatis`**: Display a brief description of a command.
- **`info`**: Display the info page of a command.


**File Operations:**
- **`cut`**: Extract portions of a file.
  - `-c`: Extract specific characters (e.g., `cut -c 3-5 file`).
  - `-d`: Specify delimiter.
  - `-f`: Specify field to extract (e.g., `cut -d, -f2 file`).

- **`expand / unexpand`**: Convert tabs to 8 spaces and vice versa.
  - Example: `expand file > file2`.

- **`wc`**: Count lines, words, and bytes in a file.

- **`fmt`**: Format text.

- **`head / tail`**: Display the first or last lines of a file.
  - `-n`: Specify the number of lines (default: 5).

- **`join`**: Join two files based on a common field.

- **`nl`**: Similar to `cat` but adds line numbers.

- **`od`**: Octal dump of a file.

- **`paste`**: Merge lines of files.

- **`pr`**: Format files for printing.

- **`sed`**: Stream editor for substitutions.
  - Example: `sed 's/old/new/' file`.

- **`split`**: Split a file (default: 1000 lines per split).
  - Example: `split file newfile`.
- **`split`**: Split a file (default: 1000 lines per split).
  - Example: `split file newfile`.

- **`tr`**: Translate or delete characters.

- **`uniq`**: Remove duplicate adjacent lines.
  - Use `sort` to make duplicates adjacent before using `uniq`.

- **`expr`**: Perform mathematical operations.
  - Example: `expr 5 \* 3`.

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

- **Piping**:
  - Use `|` to chain commands.
  - Example:
    ```bash
    ls | wc -l
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


* `grep` - Globally (search for a) Regular Expression and Print
  - Uses symbols and regular expressions to search plain text.
  - **`grep`**: Basic search.
  - **`egrep`**: Extended `grep`, allowing regular expression quantifiers without escaping.
  - **`fgrep`**: Literal search; no interpretation of special characters.

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

`vim` is a powerful text editor in Linux with two primary modes: **Command Mode** and **Insert Mode**.

Modes
1. Command Mode
- Used for navigating and manipulating text without typing.
- Key bindings control movement and text actions.

2. Insert Mode
- Used for directly editing text.
- Entered using specific keys (e.g., `i`, `a`, `o`) from Command Mode.


**Navigation in Command Mode:**
- **`h`**: Move left.
- **`l`**: Move right.
- **`j`**: Move down.
- **`k`**: Move up.
- **`/`**: Search forward.
  - Use **`n`** for the next occurrence.
- **`?`**: Search backward.
  - Use **`n`** for the next occurrence.
- **`Shift+g`**: Move to the end of the file.


**Editing in Command Mode:**

* **Deleting Text:**
  - **`x`**: Delete the character under the cursor.
  - **`dd`**: Delete the current line.

* **Pasting Text**
  - **`p`**: Paste below the current line.
  - **`P`**: Paste above the current line.

**Entering Insert Mode:**
  - **`i`**: Insert before the cursor.
  - **`a`**: Insert after the cursor.
  - **`o`**: Insert a new line below the current line.
  - **`O`**: Insert a new line above the current line.

**Saving and Exiting:**
* Without Saving
  - **`:q`**: Exit without saving.
  - **`:q!`**: Force exit without saving.

* Saving
  - **`:w`**: Save the file.
  - **`:wq`**: Save and exit.
  - **`Shift+ZZ`**: Save and exit.

**Additional Commands:**
  - **`/`**: Search forward for a term.
  - **`?`**: Search backward for a term.
  - **`n`**: Jump to the next search result.
  - **`N`**: Jump to the previous search result.

---

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

* `git push` 
  * `-u <local-repo> <remote-repo>` - Push commits

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
  * In doubt, keep an eye on the git folder to see where HEAD is pointing to

* `git checkout`
  * `git checkout <name>` - switch to another branch
  * `git checkout -b <name>` - create a branch and move there

* `git merge` - merge two branches
  * `checkout master` - then merge from there

* `git remote`
  * `add origin <repo-url>/git.git` - Add a remote named <name> for the repo at <URL>
  * this is done during the initial push after initializing the repo

* `git merge` - converge two branches

**git conflicts:**
  * resolving must be done manually in the code editor
  * the conflict is shown highlighted in green and blue 
  * Green - branch that you are on
  * Blue - conflict that came from another branch (merge)
  * Keep what you want, **remove markers** and save 

### Managing changes
* `git diff`

* `git stash`


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
      * after this, make the new commit and this will replace all # above mentioned
      * after making the final commit, run: `git push --force` to apply the changes 






**github:**
* `gh repo create <repository-name> --public` - Create a github repo
* `gh repo delete testrepo2 --yes` - Delete github repo








- `set` - set shell options     
    - `-e` - exit on fail 
    - `-o` - Set a option 
        - Noclobber 
        - Do not permit overwriting when using > 
        - Xtrace 
        - Shows full command 
        - So for ls it shows ls --colour=auto 
        - +o <option> 
         

 






## Job Scheduling 

* `at`
  - Execute commands at a specified time by reading them from `STDIN` or a file.
  - All jobs scheduled to run at the same time are executed simultaneously.
  - `echo "command" | at 1000`: Schedule a command to execute at 10 AM.
    - The result will be mailed to the user. If no mail server is available, check logs in `/var/log/syslog`.
  - `atq`: Check the list of scheduled jobs.
  - `atrm <job_id>`: Remove a scheduled job.
  - **Access Control**:
    - `/etc/at.deny`: Users listed here cannot use `at`.
    - `/etc/at.allow`: Users listed here can use `at`. Overrides `at.deny` if present.


* `batch`
  - Similar to `at`, but jobs are executed only if the system load drops below 1.5 or a specified threshold set in `atd`.
  - Jobs are executed sequentially, not simultaneously.

* `cron`
  - Schedules recurring jobs at specified times and intervals.
  - `crontab -e`: Edit or create scheduled jobs.
  - `crontab -l`: List current cron jobs.
  - **Access Control**:
    - `/etc/cron.allow`: List of users who can use cron. Overrides `/etc/cron.deny`.
    - `/etc/cron.deny`: List of users who cannot use cron.
  - **Configuration**:
    - Similar access rules as `at`.

