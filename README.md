# Editing DocBook in VSCode

## general information

VSCode stores its configuration in JSON files. They can either affect all files
edited by a user, or only all files in the currently opened directory.
Per user configuration files are stored in `~/.config/VSCodium/User/`
directory, while per directory files are stored in the `.vscode` subdirectory
of the project's directory. I prefer to use the per-directory approach which is
useful if you have checkouts/projects of the same kind in a single directory on
the file system.

You can find my collection of configuration files for VSCode at
https://github.com/tbazant/vscode - feel free to add you suggestions via GitHub
Pull Requests or Issues.


## XML support

Install the XML extension by RedHat.

### Support for geekodoc's RELAX NG schema

To let VSCode be aware of geekodoc, you need to associate XML files with the
geekodoc schema. Add the following to `.vscode/settings.json`:

```json
"xml.fileAssociations": [
    {
        "pattern": "**/*.xml",
        "systemId": "file:///usr/share/xml/geekodoc/rng/2_5.2/geekodoc-v2-flat.rng"
    }
],
```

### Resolve references inside the current file

To autocomplete XML IDs from the current file in `<xref linkend="">` references,
add the following to `.vscode/settings.json`:
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
Use `ctrl+space` to view the suggestions.

## Spell checker

Install the Code Spell Checker extension

### Basic settings

Following are useful settings. Consider adding them to `.vscode/settings.json`:
```json
"cSpell.caseSensitive": true,
"cSpell.enableFiletypes": [
    "xml"
],
"cSpell.minWordLength": 3,
"cSpell.showAutocompleteSuggestions": true,
```

### Custom dictionaries

To include SUSE's dictionary, install the `suse-documentation-dicts-en` package
and the following to `.vscode/settings.json`:

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
matching regular expression to `.vscode/settings.json`:

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

Insert custom snippets into the `.vscode/docbook.code-snippets` file.

See https://github.com/tbazant/vscode for my collection of DocBook snippets.

## daps integration

VSCode can run build tasks that can run commands on the active file. To run
shell commands, install the 'Task Shell Input' extension. Tasks are stored in
the `.vscode/tasks.json` file.

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

See https://github.com/tbazant/vscode for my collection of daps tasks.

## Style checker

Dmitri Popov developed style files for the Vale style checker in an open DocBook
file. He has written a comprehensive HOWTO on installing `vale` and 'Vale'
extension to make things working. Refer to his page
https://github.com/dmpop/technically for detailed information.