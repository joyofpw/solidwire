# SolidWire

A solid way to represent [ProcessWire](https://processwire.com/) template, fields and relationships.

ProcessWire is a wonderful system with lots of flexibility. In most
cases you don´t need complex hierarchies and relationships to
deliver a great outcome.

However when you are in larger projects some documentation is needed
in order to improve communication between people.

You can use available standards like *UML* or *Database Diagrams* to represent
relationships. But they quickly become outdated specially in the first iterations
and drafts. Creating them usually needs some graphic design tool and collaboration
between people become slower.

Other option is to rely on [migrations](https://modules.processwire.com/modules/migrations/). This option has the issue that in most cases, only programmers could understand them.

*SolidWire* was created as a language that bridges between code and diagrams.
A tool that is meant to be used as a documentation aid providing an eagle view of Processwire's
hierarchies and relationships, that could be easily read by technical and non technical people
alike. Inspiration was taken from different tools and languages like *C*, *UML*, *Markdown*, *Javascript*, *Python* and *PHP*. *Solid Wire* means to be a cascading style language.

## Example

<!-- TODO: Include an example document -->

## Specification (Version 1.0)

The following content represent the specification for a *Solid Wire* document.

### Document

A *Solid Wire* document could have the following extensions:

- *.wire
- *.wd (Short for wire document)

Example:

```
website.wd
```

The document should begin with the keyword `@document` and end with the keyword `@end`.

#### Header

An *optional* header file could be present in order to separate keyword definitions
and helper methods from the hierarchies. This file could have the following extensions:

- *.wh (Short for wire header)

Example:

```
website.wh
```

The header should begin with the keyword `@header` and end with the keyword `@end`.

Header keywords must not be inside `@document` context.
Header keywords must be before `@document`.

### Strings

Strings are defined by either the single quote `'` or double quote `"` char.

Example:

```
"This is a string"

'This is also a string'

This is not a string

```

### Multi line Strings

Multi line strings are defined by using triple quotes of string char.

Example:

```
""" This is a
multi line
string
"""

''' This is also
a multi
line
string
'''
```

### Placeholder Strings

Placeholder strings could used to replace simple variables on build time. 
Begins and ends with the `\`` char. They act similar to multiline strings
but can have dynamic data.

Example:

```
`This is a code string ${data}` 

`This is a code
string with ${data} too`
```

### Comments

Comments are defined by `#` char. Only applies to a single line.

Example:

```
# This is a comment

This is not a comment
```

### Multi line Comments
Multi line comments are defined by beginning with `/*` and ending with `*/`.

```
/*
This is a multi
line 
comment
*/
```

### Cardinality

The cardinality could be defined as `initial..final`. The `*` (asterik) means
"or more". The macro `@any` could be used to write `0..*`.

Examples:

- `0..*`: Zero or more items allowed.
- `@any`: Same as `0..*`.
- `1`: One item minimum and maximum.
- `3`: Three items minimum and maximum.
- `1..2`: One item minimum, two items maximum.

### Crunchbangs

Crunchbangs are special directives that configures some aspects of the document.
They are identified by the `#!` symbol.

#### Document Version (Required)

Specifies the version of *SolidWire* that this document uses.
Should be the first line in documents or headers.

```
#! solidwire.version 1
```

#### Import

The import crunchbang could be used to import headers and documents in order
to enable reutilization and snippets.

```
#! import "path/to/file.extension"
```

#### Region

Regions are used to specify some area inside the document. Mainly used for
better organization and help tool building.

Format is `#! region - "{region name}"` for "parent" regions and 
`#! region "{region name}"` for "child" regions (without the dash).
Only one level deep (parent, child) is allowed.

Example:

```
#! region - "Blog"
#! region  "Blog Posts"
```

This should be interpreted as

```
Parent Region: Blog
    Child Region: Blog Posts
```

#### Message

Messages are used to bring important information to the reader. Mainly used
for better organization in IDEs and show messages in other tools.

##### message

Generic Message.

Example:

```
#! message "TODO: Implement templates"
```

##### message.important

Some message that requires attention.

```
#! message.important "WARNING: This could cause conflicts later"
```

### Types
Type are a set of keywords that would be considered as a 
"base" type for a field.

You could define your own 
Example:

```
@header
    @types
        Any: "Any kind of object or value",
        None: "The value is null",
        int: "Integer value", 
        double: "Double precision number",
        number: "Any kind of number",
        str: "Single line string value. Similar to `<input type="text">`.",
        text: "Multi line string value. Similar to `<textarea></textarea>`.",
        dict: "A dictionary object. Similar to JSON",
        list: "A list of values separated by comma inside `[]` brackets",
        bool: "One of values in [true, false, yes, no] (ignoring case)",
        pointer: "A reference to a template. Alternative syntax `ptr`.",
        ptr: "A reference to a template. Alternative syntax of `pointer`", 
        img: "Image value. Similar to `<img src="">`."
    @end
@end
```

### Components

Components are pieces of content that accept parameters. They usually
do not generate any output, but exists as a data structure for more
readable documents. The follow similar structure to *Python* functions.

Example for the `array()` and `reference()` component:

```
@header
    @components 
        def array(cardinality, component)
        def reference(pointer, selector=None)
    @end
@end
```

Usage:

```
users: pointer.array(0..1, &user)
```

This means: this field will store an array of `0 to 1` user page reference.

### Page Reference

Page reference could be called as a component `reference()` or as a
shorthand syntax using `&` symbol.

Note:

When using the `&` shorthand symbol it does not support parameters.


Usage:

```
admins: pointer.array(0..*, reference(&user, selector("admin=True"))
```

This means: this field will store an array of `zero or more` pages
with the template `user` where it's `admin` field value is `True`.

### Macros

Macros are a set of instructions condensed in a just one. They are helpful
and provide code reutilization. They should be put after the `@components` section.

They must be first defined in the header section to be valid.

Example for the `@title` macro:

```
@header
    @macro title
        title: str
    @end
@end
```

Macros could also have parameters

Like this:

```
@header
    @macro my_macro(var)
        title: str.label(var)
    @end
@end
```

### Document Structure

The structure of a document is similar to a *ProcessWire*'s page tree. With each node representing a `template` with fields.

#### Node
!! TODO: Add nodes docs

