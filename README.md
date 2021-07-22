# Sass

> Sass is a stylesheet that's compiled to CSS.

**SCSS Online Playground:** [sassmeister](https://www.sassmeister.com)

## $variables <sup>[guide](https://sass-lang.com/documentation/variables)</sup>

**CSS**

```css
.button {
  color: hsl(0, 100%, 50%);
  border: 1px solid hsl(0, 100%, 50%);
}
```

**CSS Custom Properties**

```css
:root {
  --red: hsl(0, 100%, 50%);
}

.button {
  color: var(--red);
  border: 1px solid var(--red);
}
```

**SCSS**

```scss
$red: hsl(0, 100%, 50%);

.button {
  color: $red;
  border: 1px solid $red;
}
```

## NESTING

**CSS**

```css
.button a {
  font-weight: bold;
}

.button .success {
  color: green;
}

.button:hover {
  color: gray;
}
```

**SCSS**

```scss
.button {
  a {
    font-weight: bold;
  }

  .success {
    color: green;
  }

  // & - refer to parent selector
  &:hover {
    color: gray;
  }
}
```

## @mixin <sup>[guide](https://sass-lang.com/documentation/at-rules/mixin)</sup>

> mixin allows to reuse similar group of styles in multiple classes

**CSS**

```css
.card {
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  background: gray;
}

.aside {
  color: black;
  display: flex;
  flex-direction: column;
  background: gray;
}
```

**SCSS**

```scss
@mixin flex-column {
  display: flex;
  flex-direction: column;
  background: gray;
}

.card {
  border-radius: 10px;
  @include flex-column;
}

.aside {
  color: black;
  @include flex-column;
}
```

@mixin also takes parameters

```scss
@mixin cool-btn($color, $bg) {
  display: flex;
  color: $color;
  background: $bg;
}

.btn-orange {
  @include cool-btn(black, orange);
}
```

mixin @content

```scss
@mixin respond($breakpoint) {
  @if $breakpoint == phone {
    @media only screen and (max-width: 37.5em) {
      @content;
    }
  } @else if $breakpoint == tab {
    @media only screen and (max-width: 75em) {
      @content;
    }
  } @else {
    @media only screen and (max-width: 112.5em) {
      @content;
    }
  }
}

html {
  font-size: 62.5%; // 1rem = 10px; 10px/16px = 62.5%

  @include respond(tab) {
    font-size: 56.25%; // 1rem = 9px; 10px/16px = 56.25%
  }

  @include respond(phone) {
    font-size: 50%; // 1rem = 8px; 10px/16px = 50%
  }

  @include respond(desktop) {
    font-size: 75%; // 1rem = 12px; 12px/16px = 75%
  }
}
```

**Compiled CSS:**

```css
html {
  font-size: 62.5%;
}
@media only screen and (max-width: 75em) {
  html {
    font-size: 56.25%;
  }
}
@media only screen and (max-width: 37.5em) {
  html {
    font-size: 50%;
  }
}
@media only screen and (max-width: 112.5em) {
  html {
    font-size: 75%;
  }
}
```

## @function

```scss
@function colWidth($device, $width) {
  @if $device == 'phone' {
    @return calc(100% - #{$width});
  } @else {
    @return $width;
  }
}

.col {
  width: colWidth(phone, 40px);
}
```

**compiled css:**

```css
.col {
  width: calc(100% - 40px);
}
```

## @import, @use, @include & @forward <sup>[guide](https://css-tricks.com/introducing-sass-modules/)</sup>

**@import:** It loads mixins, functions, and variables from other Sass stylesheets, and combines CSS from multiple stylesheets together. It makes everything globally accessible in the target file.\

Limitations:

- @import is also a CSS feature, and the differences can be confusing
- If you @import the same file multiple times, it can slow down compilation, cause override conflicts, and generate duplicate output.
- Everything is in the global namespace, including third-party packages – so my color() function might override your existing color() function, or vice versa.
- When you use a function like color(). it’s impossible to know exactly where it was defined. Which @import does it come from?

\
⚠️ Alert
```
The Sass team discourages the continued use of the @import rule. 
Sass will gradually phase it out over the next few years, and eventually remove it from the language entirely. 
Prefer the @use rule instead.
```

**@use:** It also loads `mixins`, `functions`, and `variables` from other Sass stylesheets, and combines CSS from multiple stylesheets together. Stylesheets loaded by `@use` are called "modules". 

- The file is only imported once, no matter how many times you @use it in a project.
- Variables, mixins, and functions (what Sass calls “members”) that start with an underscore (_) or hyphen (-) are considered private, and not imported.
- Members from the used file (buttons.scss in this case) are only made available locally, but not passed along to future imports.
- Similarly, @extends will only apply up the chain; extending selectors in imported files, but not extending files that import this one.
- All imported members are namespaced by default.

```scss
// _corners.scss

$radius: 3px;

@mixin roundedButton {
  border-radius: $radius;
  padding: 1rem;
}
```

```scss
// _button.scss

@use 'corners'; // creates a `corners` namespace
// @use 'conrners' as c;

// @use 'buttons' as *; // the star removes any namespace
// @include roundedButton;

.boxButton {
  // <namespace>.[$variable|@function()|@mixin()]
  @include corners.roundedButton;
  margin: 5px + corners.$radius;
}
```

Plain CSS

```css
.button {
  border-radius: 3px;
  padding: 1rem;
  margin: 8px;
}
```

**@forward:**

The @forward rule loads a Sass stylesheet and makes its mixins, functions, and variables available when your stylesheet is loaded with the @use rule. It makes it possible to organize Sass libraries across many files, while allowing their users to load a single entrypoint file.

```scss
// src/_list.scss
$horizontal-list-gap: 2em;

@mixin list-reset {
  margin: 0;
  padding: 0;
  list-style: none;
}

@mixin list-horizontal {
  @include list-reset;

  li {
    display: inline-block;
    margin: {
      left: -2px;
      right: $horizontal-list-gap;
    }
  }
}
```

```scss
// bootstrap.scss
@forward "src/list";
// @forward "src/list" as form-*; - adding prefix
// @forward "src/list" hide list-reset, $horizontal-list-gap; - Controlling Visibility
```

```scss
// styles.scss
@use "bootstrap";

li {
  @include bootstrap.list-reset;
  // @include bootstrap.form-list-reset;
  // @include bootstrap.list-reset; - can't be accessed
}
```

Configuring modules

```scss
// _library.scss
$black: #000 !default; // The default allows a module to change the defaults of an upstream stylesheet while still allowing downstream stylesheets to override them.
$border-radius: 0.25rem !default;
$box-shadow: 0 0.5rem 1rem rgba($black, 0.15) !default;

code {
  border-radius: $border-radius;
  box-shadow: $box-shadow;
}
```

```scss
// _opinionated.scss
@forward 'library' with (
  $black: #222 !default,
  $border-radius: 0.1rem !default
);
```

```scss
// style.scss
@use 'opinionated' with ($black: #333);
```

## Atom Features

- starts with: `^`
- ends with: `$`

```scss
// class starts with `col-`
[class^='col-'] {
  float: left;
  background: green;

  &:not(:last-child) {
    margin-right: 10px;
  }
}
```

```html
<div>
  <div class="col-6">one</div>
  <div class="col-6">tow</div>
</div>
```

- loop

```scss
$sizes: 40px, 50px, 80px;

@each $size in $sizes {
  .icon-#{$size} {
    font-size: $size;
  }
}
```

**compiled css:**

```css
.icon-40px {
  font-size: 40px;
}

.icon-50px {
  font-size: 50px;
}

.icon-80px {
  font-size: 80px;
}
```

- adjust colors

```scss
$base-color: green;

.card {
  background: lighten($base-color, 25%);
}

.aside {
  background: darken($base-color, 10%);
}
```
