# ideal

An abstract templating language based on blocks.

This document serves as the language specification.

## Blocks

Ideal is a templating language based on blocks, where each block can be rendered separately.

The generic block follows the simple specification. The simplest block is just plain text:

```
text { single line of string }

text {
    multiple lines
    of string
}
```

Then comes the generic ordered block:

``` 
block {
    block { value1 }
    block { value2 }
    text {
        values
        galore
    }
}
```

And the dictionary based block:

```
key0 = dict {
    key1 = value1
    key2 = value2
    key3 = block { value3 }
    key4 = block {
        multi
        line
        contents
    }
    key5 = dict {
        key5 = nested
        key6 = values
    }
}
```

The specification strictly enforces line breaks as separators while indentation is enforced lazily, meaning that the all blocks are de-indented to the level of their first element.

Keys are (as usual variable names) expected to be valid single word expressions. Values can contain all characters (including equal signs).

Nested blocks can contain all characters on each line except a single curly right bracket (used to end blocks). 

Keys can also be used for referencing:

```
key0 = block-name {
    Some contents.
}

key1 = block-name {
    key2 = *key0
}
```

Keys on the same hierarchical level must be unique, while normal scope rules apply for nested keys.


### Common blocks

It all may seem a bit abstract, so in order to touch ground, the specifation proposes a set of common blocks:

#### Text formatting

```
text {
    Plain text without any formatting.
}
```

```
markdown {
    Sed ut perspiciatis unde omnis iste natus error sit *voluptatem* accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae **dicta** sunt explicabo. 
    Nemo __enim__ ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. 
    Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet, consectetur, adipisci velit, sed quia non numquam eius modi tempora incidunt ut labore et dolore magnam aliquam quaerat voluptatem.

    1. First element
    2. Second element
}
```

```
code {
    syntax = python
    contents = block {
        print()
    }
}
```

#### Multi media content

```
image {
    url = http://domain.spec/image.png
    size-hint = dict {
        x = 300
    }
}
```

```
gallery {
    images = block {
        Image0 | http://domain.spec/image0.png
        Image1 | http://domain.spec/image1.png
        Image2 | http://domain.spec/image2.png
    }
    size-hint = dict { 
        x = 300
    }
}
```

```
video {
    url = http://domain.spec/image.mp4
    size-hint = block {
        x = 300
    }
}
```

```
map {
    position = block {
        longitude = 52.334
        lattitude = 45.232
    }
    scale = 1:50000
    size-hint = block {
        x = 300 
    }
}
```

#### Special formatting

```
table {
    headers = block {
        year | prime number?
    }
    rows = block {
        1985 | No
        1986 | No
        1987 | Yes
        1988 | No
    }
}
```

## Rationale

Writing a language specification without a parser is like driving a cart without a horse -- so let me just shortly try to reason about the document parsing/rendering.

A number of special parsers is registered to convert each of the ideal blocks to some abstract data structure. This would usually entail some hierarchy of objects, where each object can render themselves.

Say, we wanted to use ideal as the source of a web document, then html could be produced in the following steps:

1. Create custom html block parsers.
2. Parse a document
3. Render document to valid html and javascript. This is done recursively from the base level down.
4. Layout document.

The creation of custom block parsers is a fundamental part of working with ideal, and should be easy and straight forward.

A set of basic parser blocks in javascript could look (at some level of abstraction) look like this:

```
class Block {
    constructor(contents) {
        this.contents = contents;
    }

    fields() {
        return ['contents'];
    }

    render() {
        return false;
    }
}

class Dict extends Block {
    constructor(contents) {
        super(contents);

        parse(this, contents);
    }

    fields() {
        return this.keys;
    }
}


class Text extends Block {
    render() {
        return '<div class="ideal-text">' + this.contents + '</div>';
    }
}
```
 
Instead of just static html, the render function could in general also generate interactive content.









