# Basic Linux command and Scripting
- [Basic commands](#Basic-commands)
- [Streams and Redirection](Streams-and-Redirection)
- [Basic shell scripting](#basic-shell-scripting)
- [Vim](#vim)
- [Git](#git)


## Basic commands

### Man pages
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


### **File Text Operations:**
- **`cp`**
  - `*` the wildcard of wildcards, it's used to represent all single characters or any string.
  - `?` used to represent one character
  - `[]` used to represent any character within the brackets

- **`mv`**
  - `mv -i dir1 dir2` - overwrite anything in the same directory. So you can use the -i flag to prompt you before overwriting anything
  - `mv -b directory1 directory2` -  make a backup of that file and it will just rename the old version with a ~.

- **`mkdir`**
  - `-p` - include the parent directories if not existing
  - `--mode <mask>` - Set the permissions directly at creating
    - e.g. `mkdir /dir --mode 700` 

- **`ls`**
  - `ls -lrt` - list files in long format, sorted by time, oldest first

- **`head`** / **`tail`** - Display the first or last lines of a file
  - `-n` - Specify the number of lines (default: 5).
  - `-f` - follow, using as an option for `tail`. Follow the end of a file as it receives more text, e.g. logs

- **`cat`** / **`tac`** - concatenate
  * Print the contents of a file
  * `tac` does so in reverse
  * options:
    * `-A` - show all non-printable characters
    * `-b` - number the lines
    * `-n` - number lines, but not empty lines
    * `-s` - suppress repeated empty lines

- **`expand / unexpand`**
  - Convert tabs to 8 spaces and vice versa
  - Example: `expand file > file2`.

- **`wc`** - word count
  * Count lines, words, and bytes in a file.

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

* **`grep`** - Globally Regular Expression and Print
  - Uses symbols and regular expressions to search plain text.
  - **`grep`** - Basic search.
  - **`egrep`** - Extended `grep`, allowing regular expression quantifiers without escaping.
  - **`fgrep`** - Literal search; no interpretation of special characters.
  - **`pgrep`** - get PID for a running process

* **options:**
  * `-i` - ignore case. Otherwise `grep` is **case sensitive**
  * `-v` - show lines that **do not** match pattern
  * `-l` - list file names that contain pattern, without showing matching lines
  * `-A5` - show line that matches pattern as well as 5 lines after 
  * `-B5` - show line that matches pattern as well as 5 lines before
  * `-C5` - combination of `-A5` and `-B5`
  * 
  
* **`sed`** - Stream Editor
  - Replace characters in a regular expression.
  - Syntax:
    `sed 's/<pattern>/<replacement>/' <file>`

* **`awk`** - pattern scanning and text processing language

- **`tr`** - translate
  *  Performs operations like removing repeated characters, converting uppercase to lowercase, and basic character replacement and removal
  * `tr <char 1> <char 2>` - first char is the character to be replaced
  * `tr [:lower] [:upper]`

- **`cut`**: Extract portions of a file.
  - `-c` - Extract specific characters (e.g., `cut -c 3-5 file`).
  - `-d` - Specify delimiter, e.g. `-d:`
  - `-f` - Specify field to extract (e.g., `cut -d, -f2 file.txt`). 

- **`split`**
  * Split a file (default: 1000 lines per split).
  - Example: `split file newfile`.
- **`split`**: Split a file (default: 1000 lines per split).
  - Example: `split file newfile`.

- **`paste`** - Merge lines of files.
  * Merge lines from text files horizontally
  * By default the delimeter is a tab
  * Options:
    * `-d` - Specify delimeter

- **`uniq`**: Remove duplicate adjacent lines.
  - Use `sort` to make duplicates adjacent before using `uniq`.

- **`sort`**
  * Utility for sorting lines of text files
  * Options:
    * `-k <column numbers>` - Specify field values
    * `-k2`- Indicate second field
    * `-n` - Compare and sort line based on the string numerical value
    * `-r` - Sort fields in descending order
    * `-t` - Separate one field from another

- **`fmt`** - Format text.

- **`join`**
  - Join two files based on a common field.

- **`nl`**
  * Similar to `cat` but adds line numbers

- **`od`** - Octal dump of a file

- **`pr`** - Format files for printing.

- **`expr`** - Express
  - Perform mathematical operations
  - Example: `expr 5 \* 3`


### Regular Expressions
Regular expressions (regex) are sequences of symbols and characters used to express patterns for searching text.

**Symbols:**
- `|`: OR  
  Example: `( grey|gray )` matches either "grey" and/or "gray".
- `()`: Grouping  
  Example: `gr(e|a)y` matches "grey" and/or "gray".
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

- `set` - set shell options     
    - `-e` - exit on fail 
    - `-o` - Set a option 
        - Noclobber 
        - Do not permit overwriting when using `>` 
        - `xtrace` 
        - Shows full command 
        - So for `ls` it shows `ls --colour=auto` 
        - `+o <option>`  
         

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

  - `<<` 
    - Used in conjunction with `cat << EOF`
    - It allows for printing multiple lines
    - Can be used as well to print multiple lines into a file
    - `cat << EOF > file.txt`


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


## Basic shell scripting

### Pattern-matching operator
* All about cleaning up strings
* This occur when the string provided contains too much information 
* So, the purpose of a pattern mathcing operator is to clean up a string
* It is performed against a variable
* `${1#}` prints the string length of `$1`
* `${1#string}` removes the **shortest match** of string from the **front end** of `$1`
* `${1##string}` removes the **longest match** of string from the **front end** of `$1`
* `${1%string}` removes the **shortest match** of string from the **back end** of `$1`
* `${1%%string}` removes the **longest match** of string from the **back end** of `$1`
```
filename="backup_2025-05-18.tar.gz"

| Expression         | Matches                  | Removes                  | Result                     |
|--------------------|---------------------------|---------------------------|----------------------------|
| `${filename#*.}`   | Shortest from front       | `backup_2025-05-18.`      | `tar.gz`                   |
| `${filename##*.}`  | Longest from front        | `backup_2025-05-18.tar.`  | `gz`                       |
| `${filename%.*}`   | Shortest from back        | `.gz`                     | `backup_2025-05-18.tar`    |
| `${filename%%.*}`  | Longest from back         | `.tar.gz`                 | `backup_2025-05-18`        |

# another example 

filepath="/home/user/projects/myproject/file.txt"

| Expression           | Matches                    | Removes                                | Result                               |
|----------------------|-----------------------------|-----------------------------------------|--------------------------------------|
| `${filepath#*/}`     | Shortest `*/` from front     | `/`                                     | `home/user/projects/myproject/file.txt` |
| `${filepath##*/}`    | Longest `*/` from front      | `/home/user/projects/myproject/`        | `file.txt`                           |
| `${filepath%/*}`     | Shortest `/*` from back      | `/file.txt`                             | `/home/user/projects/myproject`      |
| `${filepath%%/*}`    | Longest `/*` from back       | everything after first `/`              | *(empty)*                            |

```

### Conditional Statements
* Conditional expressions in shell scripting allow you to evaluate strings, files, and directories. Below are some commonly used conditions.
  * if ... then ... fi
  * while ... do ... done
  * until ... do ... done
  * case ... in ... esac
  * for ... in ... do ... done
 
**if statements**
  * **String Conditions:**
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

* **File and Directory Conditions:**
  - **`-e <file>`**:  
    * True if `<file>` exists.
  - **`-f <file>`**:  
    * True if `<file>` exists and is a regular file.
  - **`-d <directory>`**:  
    * True if `<directory>` exists and is a directory.
  - **`-x <file>`**:  
    * True if `<file>` exists and is executable.
  - **`-s <file>`**:  
    * True if `<file>` exists and has a size greater than zero.


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
  * **`ctrl+r`** - redo
 
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

### Good shit
* `:%s/<oldstring>/<newstring>/g`
  * replace **all** occurrences of `oldstring` by `newstring`
* `:m` - move a line
  * `:m-68` - move the line where the cursor by 68 lines upward
* `$` - jump to end of line 
* `0` - jump to beginning of line

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
  * Resolving must be done manually in the code editor
  * The conflict is shown highlighted in green and blue 
  * Green - branch that you are on
  * Blue - conflict that came from another branch (merge)
  * Keep what you want, **remove markers** and save 

### Managing changes
* `git diff` - differences
  * Compare a file between different commits or between different branches
  * Symbols indicate how files have changed
    * `------` is an indication for file 1
    * `++++++` is an indication for file 2
  * `git diff --staged` - compare between the latest commit file and the staged file  
  * `git diff <commit id1>..<commit id2>` - compare two commits

* `git stash` - Stash local Git changes in a temporary area.
  * `git stash` is about saving your work temporarily
  * It can also be a solution for when **conflicts** arise due to having uncommited changes in an existing branch and existing file 
  * **Walkthrough**:
    * Be on any branch (e.g., main).
    * Modify an existing file (a file that has already been committed).
    * Try to switch to another branch (e.g., bugfix).
    * If the changes in your working directory conflict with the target branch, Git will prevent you from switching branches unless you either:
      * Commit the changes.
      * Stash the changes using git stash.
  * `git stash pop` - bring the changes back
    * It works also between branches
  * `git stash list` - list all stashes
  * `git stash apply stash@{#}` - choose which stash to apply


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
  * `git remote rename <oldname> <newname>` - change the remote branch name. By default it is 'origin' and this can be changed

* `git push` - push code to the remote repo
  * `git push --set-upstream origin <name>` - link the remote to the current branch. This is done as the initial push, after which it is remembered
  * `git push -u <remote-repo> <local-repo>` - `-u` is synonym for `--set-upstream` 

* `git pull` - fetch branch from a remote repo

* `git fetch` - Download objects and refs from a remote repo
  * it brings it into the local repo
  * not into the working area, like `git pull`
  * `git pull` = `git fetch` + `git merge`

### Github specific

* `gh repo` - github specific commands
  * `gh repo create <repo-name> --public` - Create a github repo
  * `gh repo delete <repo-name> --yes` - Delete github repo

* **Key authentication**
  * `ssh-keygen`
  * `ssh-add`
  * `cat <key>`
  * Paste key in `Hub → Settings → SSH and GPG Keys` 

* For re-authentication, e.g. for another project
  * `git remote set-url origin git@github.com:<username>/<repo>.git`









## Lessons from Exercism

```bash

main(){
  echo "one for ${1-you}, one for me"                       #  ${1-you} output the first argument given with the command, or else a string of your choosing, in this case 'you'  

}               
main "$@"                                                   # Best always to encapsulate "$@" to prevent globbing and word splitting


(( $# == 0 )) && name=you || name="$1"                    # shorthand way of an if-conditional. If length of argument is 0, then name equals you, else, it is the value of the argument

[[ $total == $number ]] && echo "true" || echo "false"      # if $total equals $number, then echo "true", else echo "false"



(( $1 % 3 )) || output+=Pling                 # If the input is devisable by 3, 5, or 7, append to output a Pling, Plang, Plong.
(( $1 % 5 )) || output+=Plang
(( $1 % 7 )) || output+=Plong

echo ${output:-$1} 


# calling individual characters in a string
var=894
echo ${#var}                                  # ${#var} output the length of the $var
    3                                         # Great way of checking the length of the input argument in a script
echo ${var:0:1}                               # Split or cat separate characters of a string
    8
echo ${var::1}                                # same as above
    8
echo ${var::2}                                                   
    89
echo ${var:2:3}
    4

echo ${var:i:1}                               # good for in for loops

var="Hello World!"
echo ${var:2:3}                               # read it as: the first digit is the index of the string from which to start. The second digit is how many characters should be printed
  llo                                         # print 3 characters, starting from index 2


# for loops
for (( i=0 ; i<$1 ; i++ )); do                # Note the DOUBLE parentheses
  <command>
done

(( i=1 ; i<$(( ${#x} - y )) ; i++ ))          # perfectly fine to do some arithmetic inside 


for i in $var; do                             # No parentheses necessary 
  <command>
done

# arithmetic
echo $(( 5 * 5 ))
25

x=5
y=5
echo $(( x * y ))
25

z=5
echo $(( x * y + z ))
30




# Arrays
declare -a array=()          # create a regular array
declare -A array=()          # create an associative array (key-value pairs)
declare -a -r array=()          # create a read only array


# or just 

array=()                  # for a regular array

var=6896246
for (( i=0 ; i< ${#var} ; i++ )); do     # While i is smaller than the length of $var, 
  array+=(${var:i:1})
done

echo ${array[*]}                         # 6 8 9 6 2 4 6
echo ${var:1:1}                          # 6 

o=1
echo ${var:o:1}                          # 6 




## Sorting Arrays
IFS=$'\n' largest=($(sort <<<"${products[*]}"))          # A-Z
IFS=$'\n' largest=($(sort -nr <<<"${products[*]}"))      # integers

IFS= 