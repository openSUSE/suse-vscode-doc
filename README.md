# Editing DocBook in VSCode

## General information

VSCode stores its configuration in JSON files. Depending on the scope your
settings apply to, you can store the configuration either in your user directory
or in your workspace, the current project you work.

User configuration files apply settings to all project that you open in VScode.
They are stored in one of the following directories:

* `~/.config/Code/User/` (VSCode)
* `~/.config/VSCodium/User/` (VSCodium)

Settings in the current project's workspace take precedence over user settings.
The project's workspace settings are stored in the `.vscode` subdirectory.

You can find my collection of configuration files for VSCode at
https://github.com/openSUSE/suse-vscode-doc -- feel free to add you suggestions
via GitHub Pull Requests or Issues.


## XML support

Install the XML extension by RedHat. The exention ID is
[`redhat.vscode-xml`](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml).

### Support for GeekoDoc's RELAX NG schema

To let VSCode be aware of GeekoDoc, you need to associate XML files with the
GeekoDoc schema. Add the following to `settings.json`:

```json
"xml.fileAssociations": [
    {
        "pattern": "**/*.asm.xml",
        "systemId": "file:///usr/share/xml/docbook/schema/rng/5.2/assemblyxi.rng"
    },
    {
        "pattern": "**/*.xml",
        "systemId": "file:///usr/share/xml/geekodoc/rng/geekodoc-latest-flat.rng"
    }
],
```

### Resolve references inside the current file

To autocomplete XML IDs from the current file in `<xref linkend=""/>` references,
add the following to `settings.json`:

```json
"xml.references": [
    {
        "pattern": "**/*.xml",
        "expressions": [
            {
                "from": "xref/@linkend",
                "to": "@xml:id"
            },
        ]
    }
],
```

Use `Ctrl+space` to view the suggestions.

## Spell checker

Install the Code Spell Checker extension

### Basic settings

Following are useful settings. Consider adding them to `settings.json`:

```json
"cSpell.caseSensitive": true,
"cSpell.minWordLength": 3,
"cSpell.showAutocompleteSuggestions": true,
```

### Custom dictionaries

To include SUSE's dictionary, install the `suse-documentation-dicts-en` package
and the following to `settings.json`:

```json
"cSpell.customDictionaries": {
    "SUSE custom": {
        "name": "SUSE custom dictionary",
        "path": "/usr/share/hunspell/en_US-suse-doc.dic",
        "addWords": false,
    },
    "SUSE adWords": {
        "name": "SUSE adWords dictionary",
        "path": "~/SUSE-adWords-dict.dic",
        "addWords": true,
    }
},
```

The first dictionary is read-only and enhances the common English language with
in-house collected technical terms. The second one is your local writable
dictionary to collect still-unknown words.

### Ignoring strings

To ignore specific strings such as XML markup from being spell-checked, insert
matching regular expression to `settings.json`:

```json
"cSpell.ignoreRegExpList": [
    "/<(\n|.)*?>/g",
    "/<\/.*?>/g",
    "/&.*;/g",
    "/<screen.*?>(\n|.)*?<\/screen>/g",
    "/<command.*?>(\n|.)*?<\/command>/g",
],
```

### Marking as incorrect

You can insert a list of phrases that are always considered incorrectly spelled.
Add the list to  `.vscode/settings.json`:
```json
"cSpell.flagWords": [
    "filesystem"
],
```

## DocBook snippets

Snippets are templates of complete DocBook structures. You can insert them into
a document by typing defined prefixes. This is an example of a `<para>` snippet:

```json
"Paragraph": {
    "scope": "xml",
    "prefix": "pa",
    "body": [
        "<para>",
        "\t${1:insert text}",
        "</para>"
    ]
},
```

Insert custom snippets into the `snippets/docbook.code-snippets` file or install
the 'DocBook Snippets' extension from the VSCode marketplace.

See https://github.com/openSUSE/suse-vscode-doc for my collection of DocBook
snippets.

## daps integration

VSCode can run build tasks that can run commands on the active file. Build tasks
are triggered with the `ctrl+shift+b` shortcut. To run shell commands, install
the 'Task Shell Input' extension. Tasks are stored in the `tasks.json` file.

The following task example formats the open XML file with the `daps-xmlformat`
command:

```json
"label": "Daps: XML format",
"type": "shell",
"command": "daps-xmlformat",
"args": [
    "-i",
    "${file}"
],
"group": "build",
"presentation": {
    "echo": true,
    "reveal": "silent",
    "panel": "shared",
},
```

To assign a shortcut to this particular task, create a `keybindings.json` file
in your user configuration directory add the following content:

```json
[
    {
        "key": "ctrl+shift+i",
        "command": "workbench.action.tasks.runTask",
        "args": "Daps: XML format",
        "when": "editorLangId == xml"
    },
]
```

TIP: Because the XML extension already defines `ctrl+shift+i`, disable XML
formatting in `.vscode/tasks.json`:

```json
"xml.format.enabled": false,
```

See https://github.com/openSUSE/suse-vscode-doc for my collection of daps tasks.

## Style checker

Dmitri Popov developed style files for the Vale style checker in an open DocBook
file. He has written a comprehensive HOWTO on installing `vale` and 'Vale'
extension to make things working. Refer to his page
https://github.com/openSUSE/suse-vale-styleguide for detailed information.
