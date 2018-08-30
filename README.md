# Clean CSS Rules

<!-- TOC -->

- [Clean CSS Rules](#clean-css-rules)
    - [What we don't want](#what-we-dont-want)
    - [What we want](#what-we-want)
    - [Environments](#environments)
        - [Tools](#tools)
    - [Rules](#rules)
        - [1. Get your files organised](#1-get-your-files-organised)
            - [General](#general)
            - [Components](#components)
            - [Vendors](#vendors)
        - [2. Name them right](#2-name-them-right)
        - [3. Selectors](#3-selectors)
            - [CLASS: YES](#class-yes)
            - [ID: NO](#id-no)
            - [TAG: NO*](#tag-no)
            - [ATTRIBUTES: YES*](#attributes-yes)
        - [3. Don't ident. just don't!](#3-dont-ident-just-dont)
        - [4. DON'T change item's children properties, DO change item's properties based on its parent](#4-dont-change-items-children-properties-do-change-items-properties-based-on-its-parent)
            - [DONT](#dont)
            - [DO](#do)
        - [4. Selector order](#4-selector-order)
        - [5. media queries](#5-media-queries)
            - [Specify media queries for each element separatly](#specify-media-queries-for-each-element-separatly)
            - [Limit media queries](#limit-media-queries)
        - [Don't use general rules, if you gonna override it](#dont-use-general-rules-if-you-gonna-override-it)
        - [6. Rules order](#6-rules-order)
            - [Includes, extends](#includes-extends)
            - [Layout](#layout)
            - [Text, list](#text-list)
            - [Font](#font)
            - [Decorative](#decorative)
            - [Transitionz and animations](#transitionz-and-animations)
            - [Other](#other)
        - [7. Flags](#7-flags)
            - [Modifiers](#modifiers)
            - [JS flags](#js-flags)
        - [8. properties](#8-properties)
            - [calc](#calc)
            - [font-size](#font-size)
                - [1. Set the HTML element font-sized using percetage](#1-set-the-html-element-font-sized-using-percetage)
                - [2. Don't set font-size for the body](#2-dont-set-font-size-for-the-body)
                - [3.a Set font-size per element](#3a-set-font-size-per-element)
                - [3.b Set font-size per block and modify in element](#3b-set-font-size-per-block-and-modify-in-element)
        - [9. mixins, includes, extends, variables](#9-mixins-includes-extends-variables)
        - [10. Utility classes](#10-utility-classes)
        - [11. NEVER* use !important](#11-never-use-important)
        - [12. Using classes in HTML](#12-using-classes-in-html)

<!-- /TOC -->

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
7. Utility class ex: ```<div class="product bold fs-12 pb3 pt4 mr1 mtd2">...</div>```
8. Using **!important**

## What we want

1. All rules for one element in one place in my SCSS files
2. Reduce the count of selector for each element to 1 max. 2 (accepting flags coming from JS for example) in compiled CSS
3. Clear rules for media queries
4. Immediately find the selector and all its rules
5. Clear names of mixins, extends
6. Not even think about of using the **!important**

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

files containing default settins, functions, variables and everything to set up the project.

#### Components

SCSS files should contain only one block or element. If you need same element in another project, in the best case you just copy html html and this file and you're ready to go. Probably variables would match, but that's the least.  
example:  
_page.scss_  

```scss
.page {
    ...
}
.page__wrapper {

}
```

_page-header.scss_  

```scss
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
_page-header-nav.scss_  

```scss
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

***Dont use selectors name or part of their names, that directly refer to specific style*** ie: .h4, item--bold, .title--red ...
If you decide to change change your style or tag name, you will have to change also the name of the class and all referencies. Or you will finish with ```<h5 class="h4">``` or ```<h1 class="title title--red title--bold">``` which is blue and light.

We should use following as selectors:

#### CLASS: YES

But flags not as a standalone selectors.

**DON'T**  

```scss
.is-active {
    color: red;
}
```

**DO**  

```scss
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
- inputs with type attribute (input[type='text'], textarea), but class is still preferable option

#### ATTRIBUTES: YES*

But not as a standalone selector

**DON'T**  

```scss
[type='text'] {
    color: red;
}
```

**DO**  

```scss
input[type='type'] {
    color: red;
}
```

### 3. Don't ident. just don't!

Dont indent selectors - no multiple classes allowed. Flags and parental classes are exceptions. You most probably will have problems to apply new rules later.

The compiled CSS SHOULDN'T look like

```css
.product .product-item ul > li .image {
    ...
}
```

to apply new rule to the .image, you would have to override all these classes. So the compiled CSS SHOULD have one selector for the rule(s). As I mentioned, flags and parental rules are exceptions:

```scss
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

**DON'T**  

```scss
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

**DO**  

```scss
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

**DON'T**  

```scss
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

**DO**  

```scss
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

**One more thing - I use very rarely empty lines. It's not about gaining on file size, just to keep the file visualy clean.**

### 4. DON'T change item's children properties, DO change item's properties based on its parent

Keep rules for an item all together, so you don't need to search file(s) to find specific behaviour. In this case you're modifying selector .child not the .item, so it's should be in the same place as other .child's rules.

#### DONT

```scss
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

#### DO  

```scss
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

```scss
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

those are rules for the element, not the whole block. And it's much easier to read the file.

**DON'T**  

```scss
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
        &:before {
            content: '->';
        }
    }
}
```

**DON'T**  

```scss
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
         &:before {
            content: '->';
        }
    }
}
```

**DO**  

```scss
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
    &:before {
        @media only screen and (min-width: 768px) {
            content: '->';
        }
    }
}

```

#### Limit media queries

I try to keed my media queries with the same idea as the rest of the CSS - override as little as possible, the best would be to don't override any rules/selectors.
So I apply media query rules only to the specific resolutions limited by min and max. No Mobile first or Desktop first. Each rule only for its specific resolution.

Another reason for this method is to be able to use the plugin [gulp-combine-mq](https://www.npmjs.com/package/gulp-combine-mq). It combine same media queries to one and place them at the end of the CSS file. But it means, the order of applied media queries for the selector could be different than the order in the SCSS file and so media queries could override each other in different way that we'd like.

*In the example I use classic media query statement without any mixin to simplify the example.

**DON'T**  

```scss
.item {
    // for all
    font-size: 10px;

    // from tablet up
    @media only screen and (min-width: 768px) {
        font-size: 14px;
    }
    // from tablet landscape up
    @media only screen and (min-width: 960px) {
        font-size: 16px;
    }
    // from desktop up
    @media only screen and (min-width: 1025px) {
        font-size: 17px;
    }
    // from wider desktop up
    @media only screen and (min-width: 1280px) {
        font-size: 18px;
    }
}
```

because the compiled CSS would for the wider desktop screen look like this:  
font-size: 18px;  
~~font-size: 17px;~~  
~~font-size: 16px;~~  
~~font-size: 14px;~~  
~~font-size: 10px;~~  

**DO**  

```scss
.item {
    // only for small devices
    @media only screen and (max-width: 767px) { 
        font-size: 10px;
    }
    // only for portrait tablets
    @media only screen and (min-width: 768px) and (max-width: 959px) {
        font-size: 14px;
    }
    // only for lanscape tablets
    @media only screen and (min-width: 960px) and (max-width: 1024px) {
        font-size: 16px;
    }
    // only for small desktops
    @media only screen and (min-width: 1025px) and (max-width: 1279px) {
        font-size: 17px;
    }
    // only for wider desktop
    @media only screen and (min-width: 1280px) {
        font-size: 18px;
    }
}
```

This will give us only one final rule by a device.

### Don't use general rules, if you gonna override it

Dont apply general selector rule, if you need different value for certain resolution.
First apply all default rules, which are the same for each resolution and then resolution specific.

**DON'T**  

```scss
.item {
    color: red;
    font-size: 16px;
    @media only screen and (max-width: 767px) { 
        font-size: 10px;
    }
}
```

**DO**  

```scss
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


### 6. Rules order

I don't have exact list of the order of rules, but I have a logic, and try to stick with it. I prefer to order my rules within a selector from importance of positioning and total 'impact' to the website to decorative rules.
It means _left_ comes before _width_, and _width_ comes before _color_. But of course there are exceptions and cases when it's debatable if it's a decorative or positioning rule - for example line-height, font-size, border...
That's why there is no exact list, just trying always to keep it logic. I created kind of logical 'groups' where they belongs based on this rules. Because 'Text rules' change more in the layout than 'font rules', and 'font rules'
more than 'decorative'... 

#### Includes, extends

**Extend** - I place them always at the top. As they bring us some rules and those rules needs to be sometimes override, they have to be placed first.
**Include** - I put them mostly at the top for the same reason as extends, but I have some specific cases when I don't. for example I created a mixins for responsive design, where I insert one rule (font-size in this example) as the first parameter, and its values for mobile, tablet and desktop as 2nd, 3rd and 4th parameter - ```@include responsive(font-size, 12px, 14px, 16px)```. So in this case I place this rule as it was a basic _font-size_ rule.

#### Layout

Always from 'outside' as it is the most important, to the 'inside'.

- display
- position
- float
- top, left, right, bottom
- z-index
- transform
- margin
- width, height, max-width, max-height
- border (but it could be also in decorative rules)
- padding
- ...

#### Text, list

Everything around text in the meaning as a paragraph.

- line-height
- text-align
- text-transform
- text-decoration
- list-style
- ...

#### Font

Same logic as well - importance of layout impact. Font-size changes layout, color doesnt...

- font-family
- font-size
- font-weight
- font-style
- ...

#### Decorative

Everything to make it 'more nice' :)

- color
- background
- border (if it has decorative function)
- box-shadows
- ...

#### Transitionz and animations

- animation
- transition

#### Other

There are still lot of rules, which we can't really decide if it's a positioning or decorating, or place them by important, but there are often logically linked to another rule(s). For example _clear_ is for me linked to _float_, so I put them together same as _overflow_ is often linked to _width_ and _height_.  
I like to put _content_ rule at the top. When I started to use _:before_, _:after_ I often forgot this rule and than I spent after few minutes to figure out, why the element didn't show up. So now everytime I write _:before_ or _:after_ I write the _content_ rule right afterwards, so I won't forget it.

### 7. Flags

#### Modifiers

modifiers are not standalone classes. They just modify the block or element selector.

**DON'T**  

```scss
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

```html
<div class="item--red"></div>

```

**DO**  

```scss
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

```html
<div class="item item--red"></div>
```

#### JS flags

Classed applied by Javascript - instead of using BEM type modifier, is easier to use special flags which are added by JS and then modified in the CSS.

- they should start with is- or has- ie: is-active, .is-selected, .has-child
- they shouldn't be threated as a standalone selector

**DON'T**  

```scss
.item {
    color: black;
}
.is-active {
    color: red;
}
```

**DO**  

```scss
.item {
    color: black;
    &.is-active {
        color: red;
    }
}
```

### 8. properties

Dont add too specific rules to tag selectors, if there will be at least one case where needs to be overridden, use them only to reset the element. less rules are better.

**DON'T**  
_/general/_common.scss_

```scss
a {
    color: red;
    text-decoration: underline;
}
```

because you would need to use tag selector again

_/components/_item.scss_

```scss
a.item {
    &.is-selected {
        color: green;
        text-decoration: none;
    }
}
```

**DO**  
_/general/_common.scss_

```scss
a {
    text-decoration: none;
}
```

_/components/_item.scss_

```scss
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

Calc is a really powerful function and I love it! Just keep it simple, because next person who'll come by to change the value, will need a scientific calculator.

**DON'T**

``` scss
padding: calc(100vh - 67/129em); // what's 67? and why 129? and how much em is it? and in pixels?
```

**THIS IS OK**

```scss
width: calc(100vw - 20px);
```

#### font-size

When it comes to font-size, everybody prefers something else. All of units have their pros and cons and all their place where to use them.

##### 1. Set the HTML element font-sized using percetage

This way the root font-size comes from browser settings, what's important for accessibility. 
The value 62.5% is to reset it to 10px assuming the browser have 16px as default, what's easier for rem, em usage, it's easier to multiply ;) So you will work exactly same as it was ```font-size: 10px```, just now, there is a support for accessibility.
And of course, than insteand of px you'll use rem with 1/10 of value.

```scss
html {
    font-size: 62.5%;
}
body {
    font-size: 1em;
}
.teaser {
    font-size: 1.5rem;
}
```

##### 2. Don't set font-size for the body

You would just override the previous rule. Or you can set it to 1em/1rem

##### 3.a Set font-size per element

I use often this method, each element has it's own independant font-size, and based on design I decide if it's in px (preferably rem), rem or vw. This solution is good for the case, when font-sizes of elements within the same block don't have same ratio for different screen resolutions. **In this case I never use em**.

```scss
.teaser {
    &__title {
        @media only screen and (max-width: 767px) { 
            font-size: 26px;
        }
        @media only screen and (min-width: 767px) and (max-width: 1024px) { 
            font-size: 4vw;
        }
        @media only screen and (min-width: 1025px) { 
            font-size: 4rem;
        }
    }
    &__excerpt {
        @media only screen and (max-width: 767px) { 
            font-size: 13px;
        }
        @media only screen and (min-width: 767px) and (max-width: 1024px) { 
            font-size: 1.6vw;
        }
        @media only screen and (min-width: 1025px) { 
            font-size: 2rem;
        }
    }
    &__date {
        @media only screen and (max-width: 767px) { 
            font-size: 10px;
        }
        @media only screen and (min-width: 767px) and (max-width: 1024px) { 
            font-size: 1.6vw;
        }
        @media only screen and (min-width: 1025px) { 
            font-size: 12rem;
        }
    }
}
```

##### 3.b Set font-size per block and modify in element

In the case, where design of a block is identical for all screen resolutions, just smaller (ie. block _teaser_ on mobile looks same as on desktop, only font-sizes are smaller) I use this method.  
Set the font-size for the block - it could be in px (preferably rem), rem, vw just not em. And then set the font-size of the element within this block with em. So for the responsive design, we'll need media queries only for the block (parent).

``` scss
.teaser {
    @media only screen and (max-width: 767px) { 
        font-size: 13px;
    }
    @media only screen and (min-width: 767px) and (max-width: 1024px) { 
        font-size: 2vw;
    }
    @media only screen and (min-width: 1025px) { 
        font-size: 2rem;
    }
    &__title {
        font-size: 2em;
    }
    &__excerpt {
        font-size: 1em; // this line is not necessary of course
    }
    &__date {
        font-size: 0.8em;
    }
}
```

### 9. mixins, includes, extends, variables

// TODO

### 10. Utility classes

I really don't like to use utility classes. In the most cases we create them to do think easier (maybe we are already lost in our or someone's else code), but not cleaner.

```html
    <div class="bold fs12 color-red mt10 p3">
    ...
    </div>
```

Why they are bad:

1. CSS style is like clothes for the HTML body. You should be able to change outwear (style) without changing HTML by removing or adding classes or elements. Classes like 'bold' or 'fs12' are too specific and if we decide to change 'clothes' we would need to remove/change them in the HTML, if we want to change font-weight or font-size. On the other hand BEM classes like ```<div class='item item--selected'></div>```, don't need to be replaced, because they don't tell us anything about appearance of the element, they are telling us the meaning and structure positioning of the element, the appearance is managed entirely by the CSS. **We use the CSS to add an appearance to our document. We don't create our document based on a style**.
2. It's create mess in your code. In the beginning you have 5 u-classes. But as project goes on, you need another one and another, you start to override them within themself for specific cases and at the end you'll get lost in your css. And the next developer will get head-ache from something like this ```<div class="bold fs12 c-red mt10 p3 mt20-d mt-15-t p-rel"></div>```.

As I mentionned before, I am against of mixing different approaches - either you use BEM, bootstrap or utility classes, but not 2 or more in the same project.

### 11. NEVER* use !important

If you need to use !important, you should start thinking about revising your code! Because it means there are so many classes and elements or a ID in the selector, that you can't override it easily - you're code's got dirty!  

*Ok, there could be exceptions, but still, try to find a better solution. Sometimes when you use some jquery plugin with some nasty CSS inside, this could be the only solution. 

### 12. Using classes in HTML

// TODO
