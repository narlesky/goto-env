# goto-env
shell scripts for navigating to and activating an associated virtual environment (MacOS)

## About
Scripts in this project enable a new shell command for MacOS Terminal, `goto`, which changes directory to a specific project directory then activates the associated virtual environment.  If a non-existent virtual environment name is supplied, no virtual enviroment will be activated.  However, the directory will still be changed to the location specified in a system variable called `$GOTO_HOME`.  This handy shortcut (`$ goto example`) takes the place of opening a new Terminal window and executing `workon example` followed by `$ cd /your/file/directory/`.

## Requirements
This assumes use of `virtualenvwrapper` on MacOS High Sierra.  Requirements include `virtualenv` and `virtualenvwrapper`.

## Set Up

### 1. Create a Python virtual environment using `virtualenvwrapper`
The following line creates a virtual environment using Python3 called "example":
```bash
$ mkvirtualenv --python=python3 example
```

### 2. Customize Bash Profile
#### Create the `.bash_profile` file if it doesn't exist.
```bash
$ touch ~/.bash_profile
```
#### Configure `GOTO_HOME` system variable in your `.bash_profile` file (typically `~/.bash_profile`)
The `GOTO_HOME` system variable should be the base path for where you store your projects, or a directory for `goto` to default to if no valid environment is supplied.
```bash
$ touch ~/.bash_profile
$ echo -e "\nexport GOTO_HOME=/your/file/directory/">>~/.bash_profile
```
or open the `.bash_profile` in the default text editor with `$ open ~/.bash_profile` and type `export GOTO_HOME=/your/file/directory/` into the file directly.

Example `.bash_profile` File:
```bash
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/code
export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3
export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/bin/virtualenv
export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'
export RESOPS_HOME=$HOME/Desktop/mbk/resops_results
export GOTO_HOME=$HOME/Desktop/mbk
source /usr/local/bin/virtualenvwrapper.sh

```
* Note: I set `GOTO_HOME` to the folder where I store all of my active local git repositories.

### 3. Configure the `goto` Script
The content of the `goto` script must be updated for your Python virtual environments, created using `virtualenvwrapper` commands `mkvirtualenv yourenv`.  For each project you'd like to include, insert an `elif` statement defining the project directory `DESTINATION` based on the specified virtual environment.

Example:
```bash
elif [ "$1" == "resops" ]; then
	DESTINATION="${GOTO_HOME}/resops/folsom"
```

### 4. Copy the `goto` and `cleanto` scripts to `/usr/local/bin/`

Modify the file permissions for each script to allow use as an executable
```bash
$ cd /usr/local/bin/
$ chmod 711 goto
$ chmod 711 cleanto
```

### 5. Edit Terminal Preferences
1. Open the Terminal app Preferences menu
2. Select the Terminal profile for which you'd like to enable *__GOTO-ENV__* (for best results this should be the profile that Terminal defaults to when you start the app)
3. Under the tab titled _*Shell*_, add `workon $WINDOWENV; cleanto; clear` to *_Run Command_*
4. check the boxes next to "Run Command" and "Run inside shell"
5. this command activates the virtual environment set by the `goto` script in the new Terminal window with **virtualenvwrapper**'s `workon`, then cleans up the `.bash_profile` with `cleanto`.
6. Quit Terminal

### 6. Extend with Sublime CLI
Install the Sublime Text CLI documented at https://www.sublimetext.com/docs/3/osx_command_line.html to add support for automatically opening a Sublime Text window in the `goto` destination directory.

Open the Terminal and run to create a symlink `subl` for the Sublime Text CLI.
```bash
$ ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" ~/bin/subl
```
Update the Bash Profile PATH variable by adding the following to `.bash_profile`:
```bash
export PATH=$HOME/bin:$PATH
```

Add the following to the end of `goto`:
```bash
if [ "$2" == "-s"  ]; then
	subl -n -a $DESTINATION
fi
```
This opens a new Sublime window in the target directory when `goto` is invoked from the command line with the argument `-s`.

## Usage 
Configuring the `goto` script enables you to change directories to the project directory most commonly used with a given virtual environment.  This handy shortcut takes the place of `workon example` followed by `$ cd /your/file/directory/`.

Open Terminal with the updated profile and run:
```bash
$ goto example -s
```
Result:
Changes directory to the project directory configured in `goto` then activates the `example` virtual environment.  If the optional `-s` flag is included, a new Sublime window will be opened in the project directory.  If a non-existent virtual environment name is supplied, no virtual enviroment will be activated.  However, the directory will still be changed to the location specified in `$GOTO_HOME`.


