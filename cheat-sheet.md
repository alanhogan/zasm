# ZASM Cheat Sheet

ZASM v1.0.0 © 2022 by Alan Hogan. Licensed under CC-BY-4.0.

### Base {#base}

```css
:root {
  --page-bg-color: #def;
  --text-color: #222;
}

@media (prefers-color-scheme: dark) {
  :root {
    --page-bg-color: #123;
    --text-color: #eee;
  }
}

:root.theme-halloween {
  --page-bg-color: black;
  --text-color: orangered;
}

:root, input, textarea, button {
  font: 16px/1.4 "Brand Font", system-ui, sans-serif;
}  

* {
  box-sizing: border-box;
  overflow-wrap: break-word;
}

:link { color: blue; }
:visited { color: rebeccapurple; }
```

### Modules {#modules}

```css
.mdl { /* Base class for a "Modal" module */ }
.mdl__header { /* A part called "header" within an "mdl" modal */ }
.mdl--alert { /* A variant of "mdl" fulfilling "alert" purpose */ }
```

```html
<section role="alert" class="mdl mdl--alert mdl--open">
  <header class="mdl__header"> ... </header>
  <div class="mdl__body"> ... </div>
</section>
```

Optionally, especially in legacy codebases, name modules with a prefix `m-`.

It is _recommended but not required_ that all module names be three-letter abbreviations: `.btn`, `.tbl`.

Layouts can be handled responsively by modules.

### Utilities

Utilities are single-purpose class names beginning with `u-`.

```css
.u-strikethrough { text-decoration: line-through; }
```

### Non-Styling Class Names {#non-styling}

For JavaScript (`querySelectorAll()`), use a `js-` prefix: `class="js-whatever"`.

For click tracking, use `track-`: `class="track-home-page-call-to-action"`.

For automated QA testing, use `qa-`: `class="qa-home-page-call-to-action"`.

### ZASM Neutral Positions {#neutral-positions}

* ZASM does not recommend whether or not to use native CSS variables and/or preprocessor variables.
* ZASM is compatible with "CSS Modules" type techology (e.g. Webpack loaders) just as well as plain old `<link rel=stylesheet>` inclusion.
