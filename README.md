# Clean CSS Rules
**This document should help with the keeping SCSS/CSS code clean, to prevent spaghetti effect and unorganised files.
It is not another boilerplate, framework, or helper library. It is just a list of some basic rules, to keep your code clean and well-organised. Doesn't matter what framework do you use or what mixin for media queries, following these steps helps you to understand for code 1,2,10 years later or someone who get your code later.**

Take them as ideas kinda css design patterns, not a CSS bible, which needs to be follow exactly. If you have something to add, modify or you don't agree at all, I'm open to your opinion.

ps: These are not only my ideas, I was inspired by my collegues and other devs and I am sure that most of the rules you've been already using. I just tried to create one doc with most important of them.

## What we don't want
1. multiple overriding rules for an element in my compiled CSS
2. loooong selectors ex: body .class1 ul li.is-active a.blue:first-child
3. using element tag names or id as a selector
4. searching for a selector and rules for it in my SCSS files more than 5s
5. multiple indenting of SCSS files
6. calc functions which needs a scientific calculator to have idea what could be the final results (Hello Slim ;) )
7. no utility class

## What we want
1. in my SCSS files all rules for one element in one place
2. reduce the count of selector for each element to 1 max. 2 (accepting flags coming from JS for example) in my compiled CSS
3. clear rules rules for media queries
4. immediately find the selector and all its rules
5. clear names of mixins, extends without

## Environments
For my styles I use the [BEM](http://getbem.com) methodology, and I don't combine it with [utility classes](https://adamwathan.me/css-utility-classes-and-separation-of-concerns/) and I don't use (or try to not use) any kind of framework - bootstrap, grid or other.
**Why?**
Because combining two or more different approach doesn't work well. It just doesn't!

### Tools
for compiling I use mostly [Gulp](https://gulpjs.com) with packages [gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer), [gulp-combine-mq](https://www.npmjs.com/package/gulp-combine-mq) and of course something to minify [gulp-uglify](https://www.npmjs.com/package/gulp-uglify).
Which tools is not so important, but what they achieve it is.
1. Adding prefixes to some 'exotic' rules like transform :)
2. Combining media queries - it takes all media queries from all files, group it by the same type and place it at the end of css.
3. To have my compiled file as small as possible

## Rules
### 1. Get your files organised
```
./styles
├─ general
|   ├─ _common.scss
|   ├─ _variables.scss
|   ├─ _fonts.scss
|   └─ _mixins.scss
├─ components
|   ├─ _header-nav.scss
|   ├─ ...
|   └─ _header-banner.scss
├─ vendors
|   ├─ _normalize.scss
|   ├─ ...
|   └─ _carousel.scss
├─ main.scss
```

#### General
files containing default settins, functions, variables and everything to set up your project.

#### Components
one file per block.
example:
page.scss
```
.page {
    ...
}
.page__wrapper {

}
```
page-header.scss
```
.page-header {
    ...
    &--blue {
        ...
    }
}
.page-header__wrapper {
    ...
}
```

in the case of big file, could be separated also by elements
for example:
page-header-nav.scss
```
.page-header_nav {
    ...
}
```

#### Vendors
external css files (if not loaded differently)

### 2. Name them right
1. All files except the endpoint files should start with the underscore. So we'll know they only includes files.
2. Names of the components files should be always the same as the model or element where we use them. So we know directly in which file we have to search for a specific selector. underscore should be replace by -

### 3. Selectors
We should use following as selectors:

#### CLASS: YES
But flags not as a standalone selectors.

NO
```
.is-active {
    color: red;
}
```

YES
```
.item {
    &.is-active {
        color: red;
    }
}
```
#### ID: NO
these are only for JS, hard to override (needs 256 classes selector to override an ID)

#### TAG: NO*
*only in specific cases:
 - when there is no possibility to add class directly in the html
 - if it is an output of a wysiwyg
 - for default settings (body, html, reset ul... )
 - inputs with type attribute ( input[type='text'], textarea)

#### ATTRIBUTES: YES*
But not as a standalone selector

NO
```
[type='text'] {
    color: red;
}
```
YES
```
input[type='type'] {
    color: red;
}
```

### 3. Don't ident. just don't!
Dont indent selectors - no multiple classes allowed. Flags and parental classes are exceptions. You most probably will have problems to apply new rules.

The compiled CSS SHOULDN'T look like
```
.product .product-item ul > li .image {
    ...
}
```
to apply new rule to the .image, you would have to override all these classes. So the compiled CSS SHOULD have one selector for the rule(s). As I mentioned, flags and parental rules are exceptions:
```
.product__image {
    ...
}
.is-active.product__image {
    ...
}
.product__image.is-selected {
    ...
}
```

So this idea in the SCSS:

NO
```
.product {
    background: red;
    .product__image {
        ...
    }
    .title {
        ...
    }
}
```
YES
```
.product {
    background: red;
    &__image {
        ...
    }
    .is-active & { // for parental class
        ...
    }
    &--blue {
        ...
    }
    &.is-selected {
        ...
    }
}
.title {

}
```

Dont inden't BEM rules, keep it flat for readability and to make it easy to find your class. Each B and E standalone. Modifier should be inside.

NO
```
.product {
    ...
    &__item {
        ...
        &-image {
            ...
            &--small {
                ...
            }
        }
    }
}
```
YES
```
.product {
    ...
}
.product__item {
    ...
}
.product__item-image {
    ...
    &--small {

    }
}
```

### 4. DON'T change item's children properties, DO change item's properties based on its parent
Keep rules for an item all together, so you don't need to search file(s) to find specific behaviour

#### DONT

```
.item {
    &.is-selected {
        & .child {
            color: red;
        }
    }
}
...
.child {
    color: blue;
}
```

#### DO:
```
.item {
}
...
.child {
    color: blue;
    .item.is-selected & {
        color: red;
    }
}
```

### 4. Selector order
They should be organised by:
1. rules for the selector
2. states of selector (events, flags)
3. variations of the selector created by modifier
4. elements directly attached to the selector (pseudo-elements)
5. child elements

```
.item {
    color: red;
    ...
    // events
    &:hover {
        ...
    }
    // flags
    &.is-active {
        ...
    }
    // parent flags
    .selected & {
        ...
    }
    // modifiers, because its already a variation of the block or element
    &--blue {
        ...
    }
    // before and after, they are considered as a child (standalone) elements
    &:before, &:after {
        ...
    }
    // child elements
    &__child {
        ...
    }
}
```

### 5. media queries

### 6. rules order
#### layout
#### text
#### font
#### style

### 7. flags
#### modifiers
modifiers are not standalone classes. They just modifie the block or element selector.

NO
```
.item {
    background: blue;
}
.item--red {
    background: blue;
    color: red;
}
.item--green {
    background: blue;
    color: green;
}
```
```
<div class="item--red"></div>

```

YES
```
.item {
    background: blue;
}
.item--red {
    color: red;
}
.item--green {
    color: green;
}
```
```
<div class="item item--red"></div>
```

#### js flags

### 8. properties - do and don't
dont add too specific rules to tag selectors, if there will be at least one case where needs to be overridden, use them only to reset the element. less rules are better
NO
```
a {
    color: red;
    text-decoration: underline;
}
```

because you would need to use tag selector again
```
a.item {
    &.is-selected {
        color: green;
        text-decoration: none;
    }
}
```

YES
```
a {
    text-decoration: none;
}
.item1 {
    &.is-selected {
        color: red;
    }
}
.item2 {
    color: green;
    text-decoration: underline;
}
```

#### calc
#### font-size

### 9. mixins, includes, extends
