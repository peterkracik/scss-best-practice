# Clean CSS Rules
**This document should help with the keeping SCSS/CSS code clean, to prevent spaghetti effect and unorganised files.
It is not another boilerplate, framework, or helper library. It is just a list of some basic rules, to keep your code clean and well-organised. Doesn't matter what framework do you use or what mixin for media queries, despite this is mainly focused on BEM methodology, following these steps helps you to understand for code 1,2,10 years later or someone who get your code later.**

Take them as ideas kinda css design patterns, not a CSS bible, which needs to be follow exactly.

ps: These are not only my ideas, I was inspired by my collegues and other devs and I am sure that most of the rules you've been already using. I just tried to create one doc with most important of them.

## What we don't want
1. Multiple overriding rules for an element in my compiled CSS
2. Loooong selectors ex: body .class1 ul li.is-active a.blue:first-child
3. Using element tag names or id as a selector
4. Searching for a selector and rules for it in my SCSS files more than 5s
5. Infinity indents in SCSS files
6. Calc functions which needs a scientific calculator to have idea what could be the final results (Hello Slim ;) ) ex: padding: calc(100vh - 67/129em);
7. Utility class ex: <div class="product bold fs-12 pb3 pt4 mr1 mtd2">...</div>

## What we want
1. All rules for one element in one place in my SCSS files
2. Reduce the count of selector for each element to 1 max. 2 (accepting flags coming from JS for example) in compiled CSS
3. Clear rules for media queries
4. Immediately find the selector and all its rules
5. Clear names of mixins, extends

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

__page.scss__
```
.page {
    ...
}
.page__wrapper {

}
```
__page-header.scss__
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

__page-header-nav.scss__
```
.page-header_nav {
    ...
}
```

#### Vendors
external css files (if not imported differently)

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
Keep rules for an item all together, so you don't need to search file(s) to find specific behaviour. In this case you're modifying selector .child not the .item, so it's should be in the same place as other .child's rules.

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

#### Specify media queries for each element separatly
those are rules for your element, not the whole block. And it's much easier to read the file.

NO
```
.item {
    font-size: 12px;
}
.item__child {
    color: black;
    &:hover {
        color: red;
    }
}
@media only screen and (min-width: 768px) {
    .item {
        font-weight: bold;
    }
    .item__child {
        font-style: italic;
        &:hover {
            text-decoration: underline;
        }
    }
}
```

NO
```
.item {
    font-size: 12px;
}
.item__child {
    color: black;
    &:hover {
        color: red;
    }
    @media only screen and (min-width: 768px) {
        font-style: italic;
        &:hover {
            text-decoration: underline;
        }
    }
}
```

YES
```
.item {
    font-size: 12px;
    @media only screen and (min-width: 768px) {
        font-weight: bold;
    }
}
.item__child {
    color: black;
    @media only screen and (min-width: 768px) {
        font-style: italic;
    }
    &:hover {
        color: red;
        @media only screen and (min-width: 768px) {
            text-decoration: underline;
        }
    }
}

```

#### Limit media queries
I try to keed my media queries with the same idea as the rest of the CSS - override as little as possible, the best would be to don't override any rules/selectors.
So I apply media query rules only to the specific resolutions limited by min and max. No Mobile first or Desktop first. Each rule only for its specific resolution.

Another reason for this method is to be able to use the plugin [gulp-combine-mq](https://www.npmjs.com/package/gulp-combine-mq). It combine same media queries to one and place them at the end of the CSS file. But it means, the order of applied media queries for the selector could be different than the order in the SCSS file and so media queries could override each other in different way that we'd like.

In the example I use classic media query statement, but normally I do have my mixins for simplify.

NO
```
.item {
    font-size: 10px;
    @media only screen and (min-width: 768px) {
        font-size: 14px;
    }
    @media only screen and (min-width: 960px) {
        font-size: 16px;
    }
    @media only screen and (min-width: 1025px) {
        font-size: 17px;
    }
    @media only screen and (min-width: 1280px) {
        font-size: 18px;
    }
}
```

because the compiled CSS would look like this:  
font-size: 18px;  
~~font-size: 17px;~~  
~~font-size: 16px;~~  
~~font-size: 14px;~~  
~~font-size: 10px;~~  

YES
```
.item {
    @media only screen and (max-width: 767px) { 
        font-size: 10px;
    }
    @media only screen and (min-width: 768px) and (max-width: 959px) {
        font-size: 14px;
    }
    @media only screen and (min-width: 960px) and (max-width: 1024px) {
        font-size: 16px;
    }
    @media only screen and (min-width: 1025px) and (max-width: 1279px) {
        font-size: 17px;
    }
    @media only screen and (min-width: 1280px) {
        font-size: 18px;
    }
}
```
This will give us only one final rule.

### Don't use general rules, if you gonna override it
Dont apply general selector rule, if you need different value for certain resolution.

NO
```
.item {
    color: red;
    font-size: 16px;
    @media only screen and (max-width: 767px) { 
        font-size: 10px;
    }
}
```

YES
```
.item {
    color: red;
    @media only screen and (max-width: 767px) { 
        font-size: 10px;
    }
    @media only screen and (min-width: 768px) { 
        font-size: 16px;
    }
}
```


### 6. rules order
// TODO

#### layout
// TODO

#### text
// TODO
#### font
// TODO

#### style
// TODO

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
HTML
```
<div class="item--red"></div>

```

YES
```
.item {
    background: blue;
    &--red {
        color: red;
    }
    &--green {
        color: green;
    }
}
```
HTML
```
<div class="item item--red"></div>
```

#### JS flags
Classed applied by Javascript - instead of using BEM type modifier, is easier to use special flags which are added by JS and then modified in the CSS. 
- they should start with is- or has- ie: is-active, .is-selected, .has-child
- they shouldn't be threated as a standalone selector

NO
```
.item {
    color: black;
}
.is-active {
    color: red;
}
```

YES
```
.item {
    color: black;
    &.is-active {
        color: red;
    }
}
```

### 8. properties
Dont add too specific rules to tag selectors, if there will be at least one case where needs to be overridden, use them only to reset the element. less rules are better.

NO

/general/_common.scss
```
a {
    color: red;
    text-decoration: underline;
}
```

because you would need to use tag selector again

/components/_item.scss
```
a.item {
    &.is-selected {
        color: green;
        text-decoration: none;
    }
}
```

YES

/general/_common.scss
```
a {
    text-decoration: none;
}
```
/components/_item.scss
```
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
// TODO

#### font-size
// TODO

### 9. mixins, includes, extends, variables
// TODO

### 10. Utility classes
I really don't like to use utility classes. In the most cases we create them to do think easier (maybe we are already lost in our or someone's else code), but not cleaner.
```
    <div class="bold fs12 color-red mt10 p3">
    ...
    </div>
```
Why they are bad:
1. CSS style is like clothes for the HTML body. You should be able to change outwear (style) without changing HTML by removing or adding classes or elements. Classes like 'bold' or 'fs12' are too specific and if we decide to change 'clothes' we would need removed/change them in html, if we want to change font-weight or font-size. Classes 'item item--selected', don't need to be replaced, because they don't tell us anything about appearance of the element, it's managed entirely by the CSS. **We use the CSS to add an appearance to our document. We don't create our document to apply a style**.
2. It's create mess in your code. In the beginning you have 5 u-classes. But as project goes on, you need another one and another, you start to override them within themself for specific cases and at the end you'll get lost in your css. And the next developer will get head-ache from something like this <div class="bold fs12 c-red mt10 p3 mt20-d mt-15-t p-rel"></div>
3. As I mentionned, I am against of mixing different approaches - either you use BEM, bootstrap or utility classes, but not 2 or more in the same project

### 11. Using classes in HTML
// TODO
