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

### Test your changes

```bash
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ ls -la ~/.atom/packages/sourcefetch
lrwxr-xr-x  1 casiano  staff  33 18 dic 10:48 /Users/casiano/.atom/packages/sourcefetch -> /Users/casiano/github/sourcefetch
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ rm ~/.atom/packages/sourcefetch
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ ln -s ~/TFGsrc/build-atom-plugin-sourcefetch-tutorial/ ~/.atom/packages/sourcefetch
[~/TFGsrc/build-atom-plugin-sourcefetch-tutorial(step-1)]$ ls -la ~/.atom/packages/sourcefetch
lrwxr-xr-x  1 casiano  staff  61 18 dic 12:41 /Users/casiano/.atom/packages/sourcefetch -> /Users/casiano/TFGsrc/build-atom-plugin-sourcefetch-tutorial/
```

Reload Atom by running `Window: Reload` in the Command Palette

Navigate to 

`File > New` 

to create a new file, type anything you like and select it with the cursor.

Run the `sourcefetch:toggle` command using the Command Palette, Atom menu, 
or by right clicking and selecting `“Toggle sourcefetch”``

The updated command will toggle the order of the selected text:

![Reversing Selected Text](https://cloud.githubusercontent.com/assets/6755555/17759381/836acd60-64ab-11e6-84dc-4ef4471a361f.gif)

[See all code changes for this step in the sourcefetch tutorial repository](https://github.com/NickTikhonov/sourcefetch-tutorial/commit/89e174ab6ec6e270938338b34905f75bb74dbede).
