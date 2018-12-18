<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Building your first Atom plugin](#building-your-first-atom-plugin)
  - [Getting Started](#getting-started)
    - [Installing Atom](#installing-atom)
    - [Generating starter code](#generating-starter-code)
    - [Where are Atom packages installed](#where-are-atom-packages-installed)
    - [Running the starter code](#running-the-starter-code)
    - [The “toggle” command](#the-toggle-command)
    - [Atom Commands](#atom-commands)
  - [Making your first code change](#making-your-first-code-change)
    - [Change “toggle”](#change-toggle)
    - [Test your changes](#test-your-changes)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Building your first Atom plugin

[This repo ULL-ESIT-GRADOII-TFG/sourcefetch-tutorial](https://github.com/ULL-ESIT-GRADOII-TFG/sourcefetch-tutorial)

## Getting Started

### Installing Atom

```bash
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(master)]$ apm -v
apm  2.1.2
npm  6.2.0
node 8.9.3 x64
atom 1.33.0
python 2.7.9
git 2.17.1
```

### Generating starter code

Start in an empty folder  or go to branch `begin`.

If you start with an empty folder you can do this:

- Let’s begin by creating a new package using a utility provided by Atom.
- Launch the editor and press `Cmd+Shift+P` (on MacOS) or Ctrl+Shift+P (on Windows/Linux) to open the Command Palette.
- Search for `“Package Generator: Generate Package”` and click the corresponding item on the list.
- You will see a prompt where you can enter the name of the package - `“sourcefetch”`.
- Press enter to generate the starter package, which should automatically be opened in Atom.
- If you don’t see package files appear in the sidebar, press Cmd+K Cmd+B (on MacOS) or Ctrl+K Ctrl+B (on Windows/Linux).

Otherwise go to the branch `begin`: it has the same initial code

![https://cloud.githubusercontent.com/assets/6755555/17759387/8387a354-64ab-11e6-97db-ea469f008bef.gif](https://cloud.githubusercontent.com/assets/6755555/17759387/8387a354-64ab-11e6-97db-ea469f008bef.gif)


### Where are Atom packages installed

They are in `~/.atom/packages`

```bash
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ ls -l ~/.atom/packages/sourcefetch
lrwxr-xr-x  1 casiano  staff  33 18 dic 10:48 /Users/casiano/.atom/packages/sourcefetch -> /Users/casiano/github/sourcefetch
```

Observe the symbolic link

### Running the starter code

Let’s try out the starter package before diving into the code itself. We will first need to reload Atom to make it aware of the new package that was added. Open the Command Palette again and run the `“Window: Reload”` command.

Reloading the current window ensures that Atom runs the latest version of our source code.
We will be running this command every time we want to test the changes we make to our package.

Run the package toggle command by navigating to

Packages > sourcefetch > Toggle

using the editor menu, or run

sourcefetch: Toggle

using the Command Palette.

You should see a black box appear at the top of the screen. Hide it by running the command again.

![https://cloud.githubusercontent.com/assets/6755555/17759386/83799fc0-64ab-11e6-9f0c-0df9b1dbff8b.gif](https://cloud.githubusercontent.com/assets/6755555/17759386/83799fc0-64ab-11e6-9f0c-0df9b1dbff8b.gif)

### The “toggle” command

Let’s open [`lib/sourcefetch.js`](https://github.com/ULL-ESIT-GRADOII-TFG/sourcefetch-tutorial/blob/begin/lib/sourcefetch.js#L40-L47), which contains the package logic and defines the `toggle` command.

```js
toggle() {
 console.log('Sourcefetch was toggled!');
 return (
   this.modalPanel.isVisible() ? this.modalPanel.hide() : this.modalPanel.show()
 );
}
```

`toggle` is a function exported by the module.

It uses a ternary operator to call `show` and `hide` on the modal panel based on its visibility. 

`modalPanel` is an instance of [`Panel`](https://atom.io/docs/api/v1.9.4/Panel), 
a UI element provided by the Atom API.

A `Panel` is a container representing a panel on the edges of the editor window.

You should not create a `Panel` directly, instead use `Workspace::addTopPanel` and friends to add panels.

We declare `modalPanel` inside `export` default, which lets us access it as an instance variable with `this`.

```js
this.subscriptions.add(atom.commands.add('atom-workspace', {
  'sourcefetch:toggle': () => this.toggle()
}));
```

The above statement tells `Atom` to execute `toggle` every time the user runs `sourcefetch:toggle`.

We *subscribe* an anonymous function, `() => this.toggle()`, 
to be called every time the command is run. 

This is an example of event-driven programming, a common paradigm in JavaScript.

```js
activate(state) {
    this.sourcefetchView = new SourcefetchView(state.sourcefetchViewState);
    this.modalPanel = atom.workspace.addModalPanel({
      item: this.sourcefetchView.getElement(),
      visible: false
    });

    // Events subscribed to in atom's system can be easily cleaned up with a CompositeDisposable
    this.subscriptions = new CompositeDisposable();

    // Register command that toggles this view
    this.subscriptions.add(atom.commands.add('atom-workspace', {
      'sourcefetch:toggle': () => this.toggle()
    }));
  },
```

### Atom Commands

Commands are nothing more than string identifiers for events triggered by the user, defined within a package namespace. We’ve already used:

```json
package-generator:generate-package
window:reload
sourcefetch:toggle
```

Packages `subscribe` to `commands` in order to execute code in response to these events.

## Making your first code change

Let’s make our first code change—we’re going to change `toggle` 
to reverse text selected by the user.

### Change “toggle”

Change the toggle function to match the snippet below (see 
file [lib/sourcefetch.js](https://github.com/ULL-ESIT-GRADOII-TFG/sourcefetch-tutorial/blob/step-1/lib/sourcefetch.js#L40-L47branch `step-1`).

```js
toggle() {
  let editor
  if (editor = atom.workspace.getActiveTextEditor()) {
    let selection = editor.getSelectedText()
    let reversed = selection.split('').reverse().join('')
    editor.insertText(reversed)
  }
}
```

## Linking the Working Space with the Atom Package System

### linking
```bash
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ ls -la ~/.atom/packages/sourcefetch
lrwxr-xr-x  1 casiano  staff  33 18 dic 10:48 /Users/casiano/.atom/packages/sourcefetch -> /Users/casiano/github/sourcefetch
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ rm ~/.atom/packages/sourcefetch
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ ln -s ~/TFGsrc/build-atom-plugin-sourcefetch-tutorial/ ~/.atom/packages/sourcefetch
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ ls -la ~/.atom/packages/sourcefetch
lrwxr-xr-x  1 casiano  staff  61 18 dic 12:41 /Users/casiano/.atom/packages/sourcefetch -> /Users/casiano/TFGsrc/build-atom-plugin-sourcefetch-tutorial/
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(master)]$ ls -l
total 40
-rw-r--r--  1 casiano  staff    65 18 dic 10:35 CHANGELOG.md
-rw-r--r--  1 casiano  staff  1060 18 dic 10:35 LICENSE.md
-rw-r--r--  1 casiano  staff  7817 18 dic 12:50 README.md
drwxr-xr-x  3 casiano  staff    96 18 dic 12:50 keymaps
drwxr-xr-x  3 casiano  staff    96 18 dic 12:50 lib
drwxr-xr-x  3 casiano  staff    96 18 dic 12:50 menus
-rw-r--r--  1 casiano  staff   452 18 dic 12:50 package.json
lrwxr-xr-x  1 casiano  staff    41 18 dic 12:40 sourcefetch -> /Users/casiano/.atom/packages/sourcefetch
drwxr-xr-x  4 casiano  staff   128 18 dic 10:35 spec
drwxr-xr-x  3 casiano  staff    96 18 dic 10:35 styles
```

Or shorter:

```bash
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(master)]$ apm help link

Usage: apm link [<package_path>] [--name <package_name>]

Create a symlink for the package in ~/.atom/packages. The package in the
current working directory is linked if no path is given.

Run `apm links` to view all the currently linked packages.

Opciones:
  -h, --help  Print this usage message
  -d, --dev   Link to ~/.atom/dev/packages                                                 [boolean]

```

Or use `apm develop`:

```
~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(master)]$ apm help develop
Usage: apm develop <package_name> [<directory>]

Clone the given package's Git repository to the directory specified,
install its dependencies, and link it for development to
~/.atom/dev/packages/<package_name>.

If no directory is specified then the repository is cloned to
~/github/<package_name>. The default folder to clone packages into can
be overridden using the ATOM_REPOS_HOME environment variable.

Once this command completes you can open a dev window from atom using
cmd-shift-o to run the package out of the newly cloned repository.

Opciones:
  -h, --help  Print this usage message
```

The workflow - as far as I understand it - is:

```
apm develop <package_name>
cd ~/.atom/dev/packages/<package_name>
atom -d
````

- First command clones the github repo into `~/github/<package_name>` and links it to `~/.atom/dev/packages/<package_name>`, 
- second is obvious, 
- and third runs atom in development mode in which it’s also loading `development` packages from `dev/packages/`
- If you want to use your modified package in normal mode, too, simply link create a link to it in `~/.atom/packages/`
- You’ll of course need atom’s shell commands installed for all that.

## Atom command line Options

Observe option `-d`, and `ATOM_DEV_RESOURCE_PATH` and `ATOM_HOME`.
See [Load developing package](https://discuss.atom.io/t/load-developing-package/2554/5)

```
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(master)]$ atom --help
Atom Editor v1.33.0

Usage:
  atom [options] [path ...]
  atom file[:line[:column]]

One or more paths to files or folders may be specified. If there is an
existing Atom window that contains all of the given folders, the paths
will be opened in that window. Otherwise, they will be opened in a new
window.

A file may be opened at the desired line (and optionally column) by
appending the numbers right after the file name, e.g. `atom file:5:8`.

Paths that start with `atom://` will be interpreted as URLs.

Environment Variables:

  ATOM_DEV_RESOURCE_PATH  The path from which Atom loads source code in dev mode.
                          Defaults to `~/github/atom`.

  ATOM_HOME               The root path for all configuration files and folders.
                          Defaults to `~/.atom`.

Opciones:
  -1, --one                  This option is no longer supported.  [boolean]
  --include-deprecated-apis  This option is not currently supported.  [boolean]
  -d, --dev                  Run in development mode.  [boolean]
  -f, --foreground           Keep the main process in the foreground.  [boolean]
  -h, --help                 Print this usage message.  [boolean]
  -l, --log-file             Log all output to file.  [cadena de caracteres]
  -n, --new-window           Open a new window.  [boolean]
  --profile-startup          Create a profile of the startup execution time.  [boolean]
  -r, --resource-path        Set the path to the Atom source directory and enable dev-mode.  [cadena de caracteres]
  --safe                     Do not load packages from ~/.atom/packages or ~/.atom/dev/packages.  [boolean]
  --benchmark                Open a new window that runs the specified benchmarks.  [boolean]
  --benchmark-test           Run a faster version of the benchmarks in headless mode.  [boolean]
  -t, --test                 Run the specified specs and exit with error code on failures.  [boolean]
  -m, --main-process         Run the specified specs in the main process.  [boolean]
  --timeout                  When in test mode, waits until the specified time (in minutes) and kills the process (exit code: 130).  [cadena de caracteres]
  -v, --version              Print the version information.  [boolean]
  -w, --wait                 Wait for window to be closed before returning.  [boolean]
  --clear-window-state       Delete all Atom environment state.  [boolean]
  --enable-electron-logging  Enable low-level logging messages from Electron.  [boolean]
  -a, --add                  Open path as a new project in last used window.  [boolean]
```

### Test your changes

Reload Atom by running `Window: Reload` in the Command Palette

Navigate to 

`File > New` 

to create a new file, type anything you like and select it with the cursor.

Run the `sourcefetch:toggle` command using the Command Palette, Atom menu, 
or by right clicking and selecting `“Toggle sourcefetch”``

The updated command will toggle the order of the selected text:

![Reversing Selected Text](https://cloud.githubusercontent.com/assets/6755555/17759381/836acd60-64ab-11e6-84dc-4ef4471a361f.gif)

[See all code changes for this step in the sourcefetch tutorial repository](https://github.com/NickTikhonov/sourcefetch-tutorial/commit/89e174ab6ec6e270938338b34905f75bb74dbede).


#### The Atom Editor API

The code we added uses the [TextEditor API](https://atom.io/docs/api/latest/TextEditor) 
to access and manipulate the text inside the editor. 

The [TextEditor](https://atom.io/docs/api/latest/TextEditor) class represents all essential editing state for a single [TextBuffer](https://atom.io/docs/api/v1.33.0/TextBuffer), including cursor and selection positions, folds, and soft wraps. 
If you're manipulating the state of an editor, use this class.

Let’s take a closer look.

```js
let editor
if (editor = atom.workspace.getActiveTextEditor()) { /* ... */ }
```

The first two lines obtain a reference to a [TextEditor](https://atom.io/docs/api/latest/TextEditor) instance. 

The variable assignment and following code is wrapped in a conditional to handle the case where there is no text editor instance available, for example, if the command was run while the user was in the settings menu.

```js
let selection = editor.getSelectedText()
```

Calling `getSelectedText` gives us access to text selected by the user. 

If no text is currently selected, the function returns an empty string.

```js
let reversed = selection.split('').reverse().join('')
editor.insertText(reversed)
```

Our selected text is reversed using JavaScript String methods. 

Finally, we call `insertText` to replace the selected text with the reversed counterpart.

You can learn more about the different [TextEditor](https://atom.io/docs/api/latest/TextEditor)  methods available by reading the [Atom API documentation](https://atom.io/docs/api/latest/TextEditor).

## Exploring the starter package
Now that we’ve made our first code change, let’s take a closer look at how an Atom package is organized by exploring the starter code.

### The main file

The main file is the entry-point to an Atom package. Atom knows where to find the main file from an entry in package.json:

```json
"main": "./lib/sourcefetch",
```

The file exports an object with lifecycle functions which Atom calls on certain events.

- **activate** is called when the package is initially loaded by Atom. This function is used to initialize objects such as user interface elements needed by the package, and to subscribe handler functions to package commands.
- **deactivate** is called when the package is deactivated, for example, when the editor is closed or refreshed by the user.
- **serialize** is called by Atom to allow you to save the state of the package between uses. The returned value is passed as an argument to activate when the package is next loaded by Atom.

We are going to rename our package command to `fetch`, and remove user interface elements we won’t be using. Update the file to match the version below:

```js
'use babel';

import { CompositeDisposable } from 'atom'

export default {

  subscriptions: null,

  activate() {
    this.subscriptions = new CompositeDisposable()

    this.subscriptions.add(atom.commands.add('atom-workspace', {
      'sourcefetch:fetch': () => this.fetch()
    }))
  },

  deactivate() {
    this.subscriptions.dispose()
  },

  fetch() {
    let editor
    if (editor = atom.workspace.getActiveTextEditor()) {
      let selection = editor.getSelectedText()
      selection = selection.split('').reverse().join('')
      editor.insertText(selection)
    }
  }
};
```
