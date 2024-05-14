# Using VSCode as a SUSE technical writer

## Introduction

VSCode is an IDE with an advanced text editor. It comes in 2 flavors:
* *VSCode* - proprietary with closed-source licensed plugins
* *VSCodium* - community driven build based on open-source software

## Extensions

*Extensions* extend VSCode's basic functionality. They are available from
VSCode marketplaces:
* https://marketplace.visualstudio.com/VSCode (VSCode)
* https://open-vsx.org/ (VSCodium)

You will need to download the following VSCode extensions:

* XML - basic XML support
* AsciiDoc - basic AsciiDoc support (requires `ruby<RUBY_VERSION>-rubygem-asciidoctor` package installed)
* DAPS - run `daps` commands from VSCode (requires `daps` package installed)
* Vale VSCode - document style checker (requires `vale` package installed)
* SUSE Vale rules - set of style checker rules that follow SUSE style guide
* Code Spell Checker - spell checker
* DocBook Snippets - snippets that automate inserting simple and complex DB structures
* HTML / XML / RSS link checker - checks XML links and finds HTTPs alternatives
* Rewrap - aligns the flow of a paragraph

TIP: you can install them from the editor by pressing CTRL+SHIFT+X and typing the
name of the extension.

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
"xml.validation.resolveExternalEntities": true, // enable XML entity processing
"xml.validation.xInclude.enabled": false, // turn off xInclude parsing, it does not work
"xml.codeLens.enabled": true, // display info about referencing and referenced lines
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


### XML: resolve references inside the current file

To autocomplete DocBook XML IDs from the current file when using `<xref linkend=""/>`
references, add the following lines:

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

### Spellchecker: basic settings

```json
"cSpell.enabled": true,
"cSpell.caseSensitive": true,
"cSpell.minWordLength": 3,
"cSpell.showAutocompleteSuggestions": true,
"cSpell.enabledLanguageIds": [
    "xml",
    "asciidoc",
    "markdown"
],
```

### Spellchecker: custom dictionaries

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

### Spellchecker: ignoring strings

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

### Spellchecker: marking as incorrect

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
* You can view all settings specific to an extension by pressing `CTRL+SHIFT+P`,
  then typing the initial letters of the extension name, such as `daps`
* Create GitHub issues if an extension is not working as expected.
    - DAPS: https://github.com/openSUSE/vscode-daps/issues
    - SUSE Vale rules: https://github.com/openSUSE/suse-vale-styleguide/issues
    - DocBook Snippets: https://github.com/openSUSE/vscode-docbook-snippets/issues
