# Sass

> Sass is a stylesheet that's compiled to CSS.

**SCSS Online Playground:** [sassmeister](https://www.sassmeister.com)

## $variables

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

## @mixin

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
