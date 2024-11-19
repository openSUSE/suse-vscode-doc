# Using VSCode as a SUSE technical writer

## Introduction

VSCode is an IDE with an advanced text editor. It comes in 2 flavors:

* *Code* - proprietary with closed-source licensed plugins
* *Codium* - community driven build based on open-source software

You can use either Code or Codium according to your personal preference.

## Useful shortcuts

* `CTRL+B` - Toggle the left pane.
* `CTRL+~` - Toggles the bottom terminal pane.
* `CTRL+\` - Split the current editor view vertically.
* `CTRL+F` - Find or replace a string or regexp in the currently active editor.
* `CTRL+SHIFT+F` - Find or replace a string across all files in the workspace.
* `CTRL+P` - Find a file.
* `CTRL+SHIFT+P` - Run VSCode command. You can assign shortcuts to any command.
* `CTRL+R` - Open recent workspaces.

## Extensions

*Extensions* extend VSCode's basic functionality. They are available from
VSCode marketplaces:
* https://marketplace.visualstudio.com/VSCode (Code)
* https://open-vsx.org/ (Codium)

TIP: you can install the extensions from the editor by pressing `CTRL+SHIFT+X` and typing the
name of the extension.

TIP: You can view all settings specific to an extension by pressing `CTRL+SHIFT+P`,
then typing the initial letters of the extension name, such as `daps`


### Required extensions
* DAPS - run `daps` commands from VSCode (requires `daps` package installed)
* Vale VSCode - document style checker (requires `vale` package installed)
* SUSE Vale rules - set of style checker rules that follow SUSE style guide
* Code Spell Checker - spell checker

### DocBook required extensions
* DocBook Snippets - snippets that automate inserting simple and complex DB structures
* XML - basic XML support
    * `F2` renames a tag

### AsciiDoc required extensions
* AsciiDoc - basic AsciiDoc support (requires `ruby<RUBY_VERSION>-rubygem-asciidoctor` package installed)

### Recommended extensions
* HTML / XML / RSS link checker - checks XML links and finds HTTPs alternatives
    * `ALT+H` checks the links and searches HTTPS alternatives
    * `ALT+T` in the bottom pane opens the link in the Web browser
* Rewrap - aligns the flow of a paragraph
    * `ALT+Q` rewraps the paragraph
* GitLens - extended features for Git (source graph, commits, branches, cherry-picking)
* Dupe File - duplicates a file in the explorer view
* GitHub Pull requests - create and manage GH PRs (roll back to v0.100.0)
* Todo Tree - displays TODO items from the source code comments

## Configuration

VSCode stores its configuration in `settings.json` files. Settings apply either
globally (user settings) or to the current workspace.

User settings are stored in one of the following directories:

* `~/.config/Code/User/` (VSCode)
* `~/.config/VSCodium/User/` (VSCodium)

TIP: Access user settings by pressing `CTRL+SHIFT+P`, then typing `json` and
selecting `Preferences: Open User Settings (JSON)`

Settings for the current workspace are stored in its `.vscode` subdirectory
and override user settings.

### XML: common settings

```json
"xml.catalogs": [ // path to a system-wide XML catalog
    "/etc/xml/catalog"
],
"xml.symbols.enabled": false, // ugly XML file structure
"xml.validation.resolveExternalEntities": true, // enable XML entity processing
"xml.validation.xInclude.enabled": false, // turn off xInclude parsing, it does not work
"xml.codeLens.enabled": false, // disbale, we've got our own :-)
```

### XML: support for RELAX NG schema

To let VSCode be aware of GeekoDoc and DocBook assembly schemas, you have two
options:

- RECOMMENDED: associate XML files based on a file suffix, for example, `*.xml`.
  The drawback is that *all* files with the suffix are associated, no matter
  whether it is a DocBook file or not.

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

- ALTERNATIVE: insert an XML model definition at the top of the XML file
  *before* the root element (for example, after `<?xml version="1.0"/>`).
    - for GeekoDoc files:

        ```xml
        <?xml-model href="urn:x-suse:rng:v2:geekodoc-flat"
            type="application/xml"
            schematypens="http://relaxng.org/ns/structure/1.0"?>
        ```
    - for DocBook assmbly files:
        ```xml
        <?xml-model href="https://cdn.docbook.org/schema/5.2/rng/assemblyxi.rng"
            type="application/xml"
            schematypens="http://relaxng.org/ns/structure/1.0"?>
        ```

### cSpell: Basic settings

Following are useful settings. Consider adding them to `settings.json`:

```json
"cSpell.enabled": true,
"cSpell.caseSensitive": true,
"cSpell.minWordLength": 3,
"cSpell.enabledFileTypes": {
    "*": false,
    "xml": true,
    "markdown": true,
    "asciidoc": true
},
```

### cSpell: custom dictionaries

To include SUSE's dictionary, install the `suse-documentation-dicts-en` package
and the following to VSCode settings:

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

TIP: To help improve the spell-checking experience, you can submit your locally-collected
list of words as described in https://github.com/openSUSE/suse-documentation-dicts?tab=readme-ov-file#adding-words.

### cSpell: ignoring strings

You can ignore specific strings such as XML markup from being spell-checked.

```json
"cSpell.ignoreRegExpList": [
    "/<(\n|.)*?>/g",
    "/<\/.*?>/g",
    "/&.*;/g",
    "/<(screen|command|package|option|filename|systemitem|literal|guimenu|envar|remark).*?>(\n|.)*?</(\\1)>/g",
    "/:[a-zA-Z_-]+:/",
    "/----\n[\\S\\s]+?\n----/",
    "/```\n[\\S\\s]+?\n```/"
],
```

### cSpell: marking as incorrect (optional)

You can insert a list of phrases that are always considered incorrectly spelled.

```json
"cSpell.flagWords": [
    "filesystem"
],
```

## DAPS integration

Refer to https://github.com/openSUSE/vscode-daps for more details.

## DocBook snippets

Snippets are templates of complete DocBook structures. You can insert them into
a document by typing predefined shortcuts. For example, `pa` inserts the following snippet and places the cursor between start and end tag:
`<para></para>`.

## AsciiDoc

`Locked Preview to the Side` is a nice feature, activate it by pressing
`CTRL+SHIFT+P`, then typing `asciidoc`.

## Tips

* Always open a *workspace*, not a single file. A documentation workspace is a folder where the `DC-*` file resides.
* Create GitHub issues if an extension is not working as expected.
    - DAPS: https://github.com/openSUSE/vscode-daps/issues
    - SUSE Vale rules: https://github.com/openSUSE/suse-vale-styleguide/issues
    - DocBook Snippets: https://github.com/openSUSE/vscode-docbook-snippets/issues
