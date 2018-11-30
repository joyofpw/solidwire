# SolidWire

A solid way to represent [ProcessWire](https://processwire.com/) template, fields and relationships.

ProcessWire is a wonderful system with lots of flexibility. In most
cases you don´t need complex hierarchies and relationships to
deliver a great outcome.

However when you are in larger projects some documentation is needed
in order to improve communication between people.

You can use available standards like *UML* or *Entity Relationship Diagrams* to represent
relationships. But they quickly become outdated specially in the first iterations
and drafts. Creating them usually needs some graphic design tool and collaboration
between people become slower.

Other option is to rely on [migrations](https://modules.processwire.com/modules/migrations/). This option has the issue that in most cases, only programmers could understand them.

*SolidWire* was created as a language that bridges between code and diagrams.
A tool that is meant to be used as a documentation aid providing an eagle view of *Processwire's
hierarchies and relationships*. Inspiration was taken from different tools and languages like *C*, *UML*, *Markdown*, *Javascript*, *Python* and *PHP*. 

***Solid Wire* means to be like cascading style language. *CSS* for data relationships.**

## Specification (Version 1.0)

The specs provide an standarized way of the language in order to create tools like syntax highlighters, linters or document compilers in the future.

### Document

A *Solid Wire* document could have the following extensions:

- *.wire
- *.wd *(Short for wire document)*

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

- The header should begin with the keyword `@header` and end with the keyword `@end`.
- Header keywords must not be inside `@document` context.
- Header keywords must be before `@document`.
- A document can have many headers.

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

### Code Strings

Code strings could used to represent some code. 
Begins and ends with triple *`* (backtick) char. They act similar to multiline strings
but indicate this is some code. Similar to [Markdown syntax](https://help.github.com/articles/creating-and-highlighting-code-blocks/).

Example:

    ```php
        <?php echo "This is a code string" ?>
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
"or more". The macro `@many` could be used to write `0..*`. 
You can use any (and only) integer for the initial and final parts.

*Examples*:

- `0..*`: Zero or more items allowed.
- `@many`: Same as `0..*`.
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

#### Constants

Constants are useful for storing recurring values. Use all Uppercase.
The contents could be a number or string.

`#! const CONSTANT 80`

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
        def array(size:cardinality, component:object)
        def reference(template:pointer, sel:selector=None)
    @end
@end
```

*Example*:

```
users: array(0..1, &user)
```

This means: this field will store an array of `0 to 1` user page reference.

### Page Reference

Page reference could be called as a component `reference()` or as a
shorthand syntax using `&` symbol.

**Note**:

When using the `&` shorthand symbol it does not support parameters.


*Example*:

```
admins: array(0..*, reference(user, selector("admin=1"))
```

This means: this field will store an array of `zero or more` pages
with the template `user` where it's `admin` field value is `1` (*true*).

*Example with shorthand syntax*:

```
puppies: array(0..*, &dogs)
```

**Note**

Selectors are a components that accepts a string that follows the
[ProcessWire selector rules](https://processwire.com/api/ref/selectors/).


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
    # This macro set the title for `default` and `english` languages.
    @macro english_title(input)
        title: str.label({'default': input, 'en': input})
    @end
@end
```

### Document Structure

The structure of a document is similar to a *ProcessWire*'s page tree. With each node representing a `template` with fields.

#### Node
A node is the base structure for a relationship three. A node can have children or just be a single node. The base structure is the following:

`<root|public|private> "<label>" (<args>) <cardianlity> -> ...`

Example: 

```
/++ "Home" ([home, {@once, @strong}]) @many -> 

    # All children of the home node
    
    ++ "Contact" (contact) @many ->
        -- "Contact Message" (contact-message, {message:text})
    ... #/contact
    
    ++ "About Us" (about, {content: text})
    
... #/home
```

#### Child Node
Child nodes can use the special `!!` for args to indicate they will reutilize the same args as the previous child sibling.

```
 ++ "Posts" ([posts, {@strong}]) @many ->
        ++ "My Post 1" (posts-item, {body:textarea}) 
        ++ "My Post 2" (!!)
        ++ "My Post 3" (!!)
    ... #/posts
```


#### Root Node
For stablishing a `root` node the first char of a node should be `/`. Only one root node must be present in a document.

#### Private/Public

- `++` should be used if the pages that would be using this node will have `public` access. (In ProcessWire this means it will have a file named the same as the template).

- `--` should be used if the pages using this node will have `private` access. This is the `default` value if ommited.

#### Label
The label is a string that would serve as an aid for navigating the node three. Could be in two formats.

- `"<label>"` : Simple `label` in a string.
- `<id>$"<label>"` : The `id` enables to identify the node specifically. It uses the symbol `$` to separate `id` from `label`. Using the `id` is optional. The `id` must follow the conventions of the *ProcessWire page names*.

*Example*

`++ "Posts" ([posts, {@strong}]) @many ->`

*Example With id*

`++ blog1$"Posts" ([posts, {@strong}]) @many ->`

#### Args
The arguments is similar to a function that could contain different structures depending on the args given.

- `(<template name>)`: Just the template name. Assumes default properties for a node.
- `(<template name>, {node properties})`: A template name with custom properties.
- `([<template name>, <node configuration>])` : A template name with custom configuration and default properties.
- `([<template name>, {node configuration}], {node properties})` : A template name with custom configuration and custom properties.

##### Template name 
The template name follows the same rules as [*ProcessWire* templates](https://processwire.com/api/ref/sanitizer/page-name/).
And is used for identification across the document. The template name could be repeated across the document by different
nodes. Just like [*ProcessWire Pages*](https://processwire.com/api/ref/page/).

When the node has an `id` associated with it the template name could be called like `id$name`. In order to specify exactly which node you would accept as a parameter.

*Example*

`product: reference(products-item)`

*Example With id*

`tags:reference(fashion-tags$products-tags-item)`

#### Cardinality
Cardinality can be any cardinality operator. Default value is `0..*` (`@many`). Only required if the node have children.

#### Children
The node would detemine to have children if the operator `->` is present and ends with the `...` operator.

## Example
The following document shows how a simple blog could be expressed using *Solid Wire* syntax.

```
#! solidwire.version 1
#! import "foundation.wh"

@document
/++ "Home" ([home, {@once, @strong}]) @many -> 

    ++ "Posts" ([posts, {@strong}]) @many ->
        ++ "My Post 1" (posts-item, {body:textarea}) 
        ++ "My Post 2" (!!)
        ++ "My Post 3" (!!)
    ... #/posts
... #/home
@end
```

`#! import "foundation.wh"`

Import the common *Processwire* fields and macros,

`/++ "Home" ([home, {@once, @strong}]) @many -> `

- This is the root node. As noted by the initial `/++`. 
- It's template is named `home`.
- It will only allow one copy (one page with the template home).
- It will have a `strong` relationship with it's parent (could not be deleted unless it's parent is deleted). Because this is a root node. This page is permanent.
- `@many` indicates it could have 0 or more children (`0..*`).

`++ "Posts" ([posts, {@strong}]) @many ->`

- The template is named `posts`.
- Will have a `strong` relationship with it's parent.
- Can have zero or more children.
- It's parent is the `root` node.

`++ "My Post 1" (posts-item, {body:textarea}) `

- The template is named `posts-item`.
- Have a field named `body` of type `textarea`.
- Have no children.

`    ... #/posts`
`...` Indicates the end of the `Posts` child context. `#/posts` is just a comment to represent that childs of this page ends here.


