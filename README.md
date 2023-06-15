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
https://github.com/openSUSE/suse-vscode-doc -- feel free to add your suggestions
via GitHub pull requests or issues.

## Extensions

Many tasks can be automatized by using *extensions*. They are available from
VSCode marketplaces:
* for VSCode: https://marketplace.visualstudio.com/VSCode
* for VSCodium: https://open-vsx.org/

The documentation team delivers the following extension for VSCode:
* DocBook Snippets
* SUSE Vale rules

TIP: you can install them from the editor by pressing CTRL+SHIFT+X and typing the
name of the extension.

## XML support

Install the XML extension by RedHat. The exention ID is
[`redhat.vscode-xml`](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml).

### Support for RELAX NG schema

To let VSCode be aware of GeekoDoc and DocBook assembly schemas, you have two
options:

- RECOMMENDED: associate XML files based on a file suffix, for example, `*.xml`.
  The drawback is that *all* files with the suffix are associated, no matter
  whether it is a DocBook file or not. `settings.json` example:

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


### Resolve references inside the current file

To autocomplete DocBook XML IDs from the current file when using `<xref linkend=""/>`
references, add the following lines to `settings.json`:

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
    "cSpell.enabledLanguageIds": [
        "xml"
    ],
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
        "/<(screen|command|package|option|filename|systemitem).*?>(\n|.)*?</(\\1)>/g",
    ],
    ```

### Marking as incorrect

You can insert a list of phrases that are always considered incorrectly spelled.
Add the list to  `settings.json`:

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

Obtain all snippets by installing the *DocBook Snippets* extension.
Alternatively, clone the
https://github.com/openSUSE/vscode-docbook-snippets repository and make a
symbolic link in your `~/.config/VSCodium/User/snippets` directory, for example:

    ```bash
    ln -s ~/devel/vscode-extensions/vscode-docbook-snippets/snippets/snippets.code-snippets docbook.code-snippets
    ```

## daps integration

VSCode can run build tasks that can run commands on the active file. Build tasks
are triggered with the `ctrl+shift+b` shortcut. To run shell commands, install
the *Task Shell Input* extension. Tasks are stored in the `tasks.json` file.

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

TIP: because the XML extension already defines `ctrl+shift+i`, disable XML
formatting in `tasks.json`:

    ```json
    "xml.format.enabled": false,
    ```

See https://github.com/openSUSE/suse-vscode-doc for my collection of daps tasks.

## Style checker

Vale style checker can apply rules from SUSE styleguide and highlight errors and
warning in the text after savin the file with CTRL+S. To make it work, you need
to install the Vale binary first. Then either install the *SUSE Vale rules* extension
into VSCode or configure Vale manually as described in
https://github.com/openSUSE/suse-vale-styleguide.
