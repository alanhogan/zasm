ZASM: Zero-Ambiguity Stylesheet Methodology
===========================================

<!-- Markdown formatting note:
     This file is authored against PHP Markdown Extra,
     not GitHub-Flavored Markdown. -->

<div class=toc markdown=1>
* [Introduction](#introduction)
* [Base Styles](#base)
* [Modules](#modules)
  * [Parts](#parts)
  * [Variants](#variants)
  * [Layout modules](#layout-modules)
  * [Content-oriented modules](#content-oriented-modules)
* [Don’t-Do’s](#dont-dos)
* [Non-Styling Class Names](#non-styling)
* [Thanks](#thanks)
* [License](#license)
</div>

### Introduction {#introduction}

In this document I hope to define a CSS methodology using a small set of unambigous rules. This methodology has these aims:

+ extreme maintainability
    + high readability 
    + guided writability
    + low barrier to adding collaborators
+ broad compatibility 
    + with mobile-first patterns
    + with responsive designs
    + with pre-processors and build tools
    + with modern web standards
    + with backwards compatibility 
    + with legacy codebases as well as green-field projects

The first major goal is maintainability — important whether your project involves one person or ten. For any sort of styling you want to do, under ZASM, there should be one clear, “right’ way to write the selector for it; this makes the CSS easy to author. Class names written under ZASM are designed to be instantly identifiable as belonging to one of the categories defined in the methodology. This provides readability and helps communicate author intention. By keeping the methodology small and tightly defined, new collaborators won’t face a large uphill climb to productivity — at least, assuming they have already learned enough CSS, an admittedly complicated pre-requisite!

The second top-level goal is broad compatibility. That means that ZASM should be able to be the one methodology you can reach for in all your projects, both shared and personal. To do this, we carefully establish rules that are strict enough to provide some built in ‘defenses’ as well as flexible enough to adapt to any specific project.


### Base Styles {#base}

In as little CSS as possible, and by writing selectors without any class names at all ([pseudo-classes] are OK), set base element styles including your preferred font stack. 

It is _recommended_ to set a good base line height. It is up to you whether that is a ratio (1.2, 1.6, etc) or an absolute measurement (24px).

If desired, style links and form controls (input elements). This is not necessary because you can instead ensure every link and control is styled by a module; it is up to your preference.

In your base styles, fix dumb browser default styling issues. Optionally set sane defaults for `box-sizing` and `overflow-wrap` on everything. 

_If_ using [CSS variables], set globally applicable variables, or even all variables, here. Your color palette is the most obvious thing to define here. Optionally modify these using media queries and/or classes applied to `html` (aka `:root`). It is _recommended_ that any such class names start with `theme-`: `theme-dark`.

Notably, it is _perfectly okay_ under ZASM if you avoid CSS variables altogether. You do not need to support multiple themes at all. Or you can ship a hundred different themes all compiled as different `.css` files using Sass. It doesn’t affect the rest of the methodology at all.

ZASM recommends writing _fewer_ base styles and _more_ modules.


```css
:root {
  --page-bg-color: #def;
  --text-color: #222;
}

/* Automatically use dark mode if the user prefers it */
@media (prefers-color-scheme: dark) {
  :root {
    --page-bg-color: #123;
    --text-color: #eee;
  }
}

/* Note, redundant variables to automatic dark mode for when
 * dark mode is manually applied to <html> by class */
:root.theme-dark {
  --page-bg-color: #123;
  --text-color: #eee;
}

:root.theme-halloween {
  --page-bg-color: black;
  --text-color: orangered;
}

:root, body, input, textarea, button {
  font: 16px/1.4 "Brand Font", system-ui, sans-serif;
}  

* {
  box-sizing: border-box;
  overflow-wrap: break-word;
}

:link { color: blue; }
:visited { color: rebeccapurple; }
:link:active, :visited:active { color: var(--text-color);
```

[pseudo-classes]: https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes
[CSS variables]: http://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties


### Modules {#modules}

Modules are things — just about anything. They are composable and can be placed within each other (when it makes sense). They can be very complicated. They can have parts and variants.

The naming rule for a module's class name (or "base class") is: **any three-letter class name.**

However, you should add a prefix such as `m-` for all module names when introducing ZASM to codebases with existing HTML that uses unprefixed class names: `.m-mdl` instead of `.mdl` and `.m-mdl__header` instead of `.mdl__header`.

If your organization is adopting ZASM, there may be cases where you may not desire or be able to follow the three-letter naming rule. That is an acceptable deviation; just remember to make your module naming rule clear enough that it is readily understood to be a module when it is seen in use. And try to avoid long module names as it makes naming parts and variants very clunky.


```css
.mdl { /* Base class for a "Modal" module */ }
.mdl__header { /* A part called "header" within an "mdl" modal */ }
.mdl--alert { /* Variant of "mdl" fulfilling "alert" purpose */ }
```

#### Parts {#parts}

In ZASM, a "part" is what [BEM] calls an "element": It's something that belongs within its module. I call them "parts" because "element" already has a different meaning in CSS and HTML.

Parts are always named like this: **base module name + two underscores + kebab-cased part name.**

Example: Module `foo` could have parts `foo__intro` and `foo__author-signature`.

A module can have unlimited parts. Parts can be within other parts — for example you might have a close button `.mdl__close` within the modal header `.mdl__header`. Whether the parts of the module are required or optional is up to you.

#### Variants {#variants}

"Variants" are variations of a module. They are what [BEM] calls "modifiers" and what [SMACSS] calls "subclassed modules."

The class name for a variant is: **base module name + two hyphens + kebab-cased variant name.**

Example: Module `foo` could have a variant `in-progress`, so the class name would be `foo--in-progress` (assuming you are not prefixing module names with `m-`).

Multiple variants can be applied to a module simultaneously, if it makes sense. You can style these intersections when needed:

```css
.msg { /* message module */ }
.msg--loading { color: gray; }
.msg--mine { color: blue; }
.msg--loading.msg--mine { color: slategray; }
```

Variants can, but do not necessarily, represent states. They can also transform the module as desired for placement elsewhere. Or they can simply change the appearance to grab more or less visual attention. The classic example is `btn`, a normal button, and `btn--primary`, a 'primary' button with strong contrast to the surrounding area due its purpose as the primary action on the screen.

_Do_ apply multiple variant classes to an element as appropriate:

```html
<input
  type="submit"
  class="btn btn--primary btn--disabled"
  value="Submit" />

<section role="alert" class="mdl mdl--alert mdl--open">
  <header class="mdl__header"> ... </header>
  <div class="mdl__body"> ... </div>
</section>
```

Do _not_ add the base module class to selectors unnecessarily: Style `.btn--primary`, not `.btn.btn--primary`.

#### Layout Modules {#layout-modules}

One valid use of a module is to create one or more containers for laying out content (= other modules). 

There is no special naming designation for layout modules. In fact, a module can handle its own layout as well as doing other things. You do not need to decide whether a module is a layout module or a regular module; it can be both. Or you can separate the concerns if that increases your re-usability. 

You can use the wildcard direct child selector (`> *`) in layout modules, but it is even better to swap this for named parts.

```css
.xyz { /* module that lays out its children */ }
.xyz > * { flex: 1; } /* Acceptable selector */
.xyz__child { flex: 1; } /* Even better selector! */
```

ZASM strongly supports responsive design, which is what we call it when the same page, with the same markup, effortly resizes and rearranges itself to fit screens of any size and shape. ZASM avoids simple "layout" classes that always do the same thing (e.g., apply two columns). Instead we empower modules to lay out their parts however they want. That will often mean using `@media` queries to switch to a compact, single-column layout on narrow screens. Or it can mean using flexbox-based responsive techniques. Or liberal use of `calc()`. Or all of the above. It's up to you!

Caution: _Never_ write CSS with descendant and element name seletors like this for a layout module:

<pre><code class="css bad">
.xyz div { float: left; }
</code></pre>

That's a great way to mess up just about every module nested within `.xyz`, and it’s a completely avoidable violation of ZASM rules.

#### Content-Oriented Modules {#content-oriented-modules}

Sometimes you can't realistically apply class names to *everything.* For instance, the contents of an article probably include lots of headings and paragraphs that don't have class names on them. Or maybe you need to display content generated by a third-party system or a certain software library that has its own ideas about markup.

This is not a problem in ZASM. After all, we have taken care to keep our base styles minimal, and most of our styles only trigger off the specific class names we have invented usng ZASM naming rules.

Cases where you need to bulk-style content that may be using no class names or non-ZASM class names call for _content-oriented modules._

These are just a special case of module that is focused on styling its children and descendants. 

Imagine a "content" module named `cnt`. 

```html
<article class="cnt">
  <h1>Why My Dog Really Did Eat My Homework</h1>
  <p>Toby is not a bad dog. In fact, ... </p>
</article>
```

```css
.cnt h1 { /* ... */ }
.cnt p { /* ... */ }
```

Notably content-oriented modules can easily be nested within other modules, but other modules cannot be nested within a content-oriented module without risking styles breaking. 

For this reason, content-oriented modules should be relatively _few_, lest you ruin the composability and predictability of your ZASM-based stylesheet.

By the same logic, you should strive to use the direct child selector (`>`) whenever possible instead of the descendant selector (space), but this will often be impossible for a well-needed content-oriented module. For example, `.cnt > p` will work on our example HTML, but it will probably break once a piece of content appears that contains a `p` using a `<section>` or `<div>` tag!



### Utilities {#utilities}

Utility classes should be defined _sparingly._ You may not even need or write a single utility class in a well-organized ZASM project.


Name them with a `u-` prefix and kebab-case (hyphenate) the rest.

Utility classes are standalone classes that acheive a narrow purpose. If you are tempted to make a "variant" or "part" of a utility, you should convert it to a module instead.

```css
.u-clear {
  clear: both;
}

.u-small-caps {
  font-variant: small-caps;
}

.u-tabular-nums {
  font-variant-numeric: tabular-nums;
}
```



### Don’t-Do’s {#dont-dos}

* Never use ancestor or parent selectors to style a module differently based on where it appears. Create a variant instead.

* Do not invent _states_ (like `.is-current`). Instead, invent a variant.

* Do not write _layout_ classes; instead, define a module that has a layout purpose.

* Do not use `!important` (unless it is necessary due to some horrible legacy code you can't jettison).

* Do write element-based selectors (except for the smallest number necessary in base styles). For example, do not style `main` or `article`. Instead, invent a module and apply it. It is acceptable to use ancestor selectors to style elements within a content-focused module: `.cnt p { margin 1.2em 0; }` is a fine rule for styling paragraphs within your main content area `.cnt`.



### Non-Styling Class Names {#non-styling}

When elements will be targeted via JavaScript (e.g. with `querySelector()`), prefer a `js-` prefix: `class="js-whatever"`.

For instrumententation of click tracking, use a consistent prefix such as `track-`: `class="track-home-page-call-to-action"`.

For enabling automated QA testing, always use class names beginning with `qa-`: `class="qa-home-page-call-to-action"`.

It is _recommended_ to apply as many non-styling class names to the same element as needed, even if that element also uses one or more ZASM class names for styling purposes.


### Cheat Sheet {#cheat-sheet}

For quick reference, enjoy [the ZASM cheat sheet](https://alanhogan.com/code/css/zasm/cheat-sheet).


### Thanks {#thanks}

This methodology is hugely inspired by [SMACSS][] and [BEM][].

This document is [open source][zasm-gh]. Issues and pull requests are welcome.

[SMACSS]: https://smacss.com/ "Scalable and Modular Approach to CSS"
[BEM]: http://getbem.com/ "Block, Element, Modifier"
[zasm-gh]: https://github.com/alanhogan/zasm/

### License {#license}

<p xmlns:cc="http://creativecommons.org/ns#" xmlns:dct="http://purl.org/dc/terms/"><a property="dct:title" rel="cc:attributionURL" href="https://alanhogan.com/code/css/zasm">The Zero-Ambiguity Stylesheet Methodology (ZASM)</a> by <a rel="cc:attributionURL dct:creator" property="cc:attributionName" href="https://alanhogan.com/">Alan J. Hogan</a> is licensed under <a href="http://creativecommons.org/licenses/by/4.0/?ref=chooser-v1" target="_blank" rel="license noopener noreferrer" style="display:inline-block;">CC BY 4.0 <img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/cc.svg?ref=chooser-v1"><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/by.svg?ref=chooser-v1"></a></p>
