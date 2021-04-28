# Sass

> Sass is a stylesheet that's compiled to CSS.

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
```
