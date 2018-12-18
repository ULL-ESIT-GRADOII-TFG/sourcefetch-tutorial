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

Let’s begin by creating a new package using a utility provided by Atom.

Launch the editor and press Cmd+Shift+P (on MacOS) or Ctrl+Shift+P (on Windows/Linux) to open the Command Palette.

Search for “Package Generator: Generate Package” and click the corresponding item on the list.
You will see a prompt where you can enter the name of the package - “sourcefetch”.

Press enter to generate the starter package, which should automatically be opened in Atom.
If you don’t see package files appear in the sidebar, press Cmd+K Cmd+B (on MacOS) or Ctrl+K Ctrl+B (on Windows/Linux).

![https://cloud.githubusercontent.com/assets/6755555/17759387/8387a354-64ab-11e6-97db-ea469f008bef.gif](https://cloud.githubusercontent.com/assets/6755555/17759387/8387a354-64ab-11e6-97db-ea469f008bef.gif)

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

Let’s open `lib/sourcefetch.js`, which contains the package logic and defines the `toggle` command.

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
