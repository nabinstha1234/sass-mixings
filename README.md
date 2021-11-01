## 1. Push auto

To quickly centre a block element without having to worry about if there is any top or bottom margin already applied.
``` 
@mixin push--auto {
    margin: { 
        left: auto;
        right: auto;
    }
}

```
## 2. Pseudo

When using ::before and ::after you'll always need these three, so we're saving two lines of code every time you use this.

``` 
@mixin pseudo($display: block, $pos: absolute, $content: ''){
    content: $content;
    display: $display;
    position: $pos;
}
```
### usages
```
div::after {
    @include pseudo;
    top: -1rem; left: -1rem;
    width: 1rem; height: 1rem;
}

```

## 3. Responsive Ratio
We use this for creating scalable elements (usually images / background images) that maintain a ratio.

```
@mixin responsive-ratio($x,$y, $pseudo: false) {
    $padding: unquote( ( $y / $x ) * 100 + '%' );
    @if $pseudo {
        &:before {
            @include pseudo($pos: relative);
            width: 100%;
            padding-top: $padding;
        }
    } @else {
        padding-top: $padding;
    }
}

```

### usages

``` 
div {
    @include responsive-ratio(16,9);
}
```

## 4. css triangles

This mixin takes all the hassle out of creating that triangle you'll see coming out of most traditional tooltips, all without images, you just specify it's colour, how big you want it, the direction it's going to come out of your element and you're done!

``` 
@mixin css-triangle($color, $direction, $size: 6px, $position: absolute, $round: false){
    @include pseudo($pos: $position);
    width: 0;
    height: 0;
    @if $round {
        border-radius: 3px;
    }
    @if $direction == down {
        border-left: $size solid transparent;
        border-right: $size solid transparent;
        border-top: $size solid $color;
        margin-top: 0 - round( $size / 2.5 );
    } @else if $direction == up {
        border-left: $size solid transparent;
        border-right: $size solid transparent;
        border-bottom: $size solid $color;
        margin-bottom: 0 - round( $size / 2.5 );
    } @else if $direction == right {
        border-top: $size solid transparent;
        border-bottom: $size solid transparent;
        border-left: $size solid $color;
        margin-right: -$size;
    } @else if  $direction == left {
        border-top: $size solid transparent;
        border-bottom: $size solid transparent;
        border-right: $size solid $color;
        margin-left: -$size;
    }
}
```

## 5. Font styles

Take the pain out of setting styles for a font. (This assumes you have already included the font), set your fallback font once and you never have to worry again.

``` 
@mixin font-source-sans($size: false, $colour: false, $weight: false,  $lh: false) {
    font-family: 'Source Sans Pro', Helvetica, Arial, sans-serif;
    @if $size { font-size: $size; }
    @if $colour { color: $colour; }
    @if $weight { font-weight: $weight; }
    @if $lh { line-height: $lh; }
}

```

## 6. Placeholders
They're a pain as you have to set the style in all the separate formats, this sorts it for you.

``` 
@mixin input-placeholder {
    &.placeholder { @content; }
    &:-moz-placeholder { @content; }
    &::-moz-placeholder { @content; }
    &:-ms-input-placeholder { @content; }
    &::-webkit-input-placeholder { @content; }
}

```

### usages:

``` 
input,  
textarea {  
    @include input-placeholder {
        color: $grey;
    }
}

```

## 7. Media Queries

A massive time saver! You can just use pixel values but we have some default breakpoints setup that work too, another time saver while keeping your breakpoints consistent.

``` 
$breakpoints: (
    "phone":        400px,
    "phone-wide":   480px,
    "phablet":      560px,
    "tablet-small": 640px,
    "tablet":       768px,
    "tablet-wide":  1024px,
    "desktop":      1248px,
    "desktop-wide": 1440px
);
@mixin mq($width, $type: min) {
    @if map_has_key($breakpoints, $width) {
        $width: map_get($breakpoints, $width);
        @if $type == max {
            $width: $width - 1px;
        }
        @media only screen and (#{$type}-width: $width) {
            @content;
        }
    }
}

```

### usages
``` 
.site-header {
    padding: 2rem;
    font-size: 1.8rem;
    @include mq('tablet-wide') {
        padding-top: 4rem;
        font-size: 2.4rem;
    }
}
```

## 8. Z-index

While technically this is just a pure function, but I felt it's worth being on the list as it's so easy to loose track of your z-index values when working in several different files, so we created this so that we could store them in one place for easy editing/recording. Just place this snippet in your main variables/settings.scss file (needs to be imported before any of your other files that reference a x-index of course).
```
@function z($name) {
    @if index($z-indexes, $name) {
        @return (length($z-indexes) - index($z-indexes, $name)) + 1;
    } @else {
        @warn 'There is no item "#{$name}" in this list; choose one of: #{$z-indexes}';
        @return null;
    }
}
$z-indexes: (
    "outdated-browser",
    "modal",
    "site-header",
    "page-wrapper",
    "site-footer"
);
```
Then where ever you're wanting to use a z-index value, name it the same as your class and add in it into your variables/setting file, like below. You'll never have a "z-index: 99999999;" headache again.

``` 
.site-header {
    z-index: z('site-header');
}
```

## 9. Hardware
Simple and effective for when you need to trigger hardware acceleration for some animation, keeping everything fast, slick and flicker-free.

```
@mixin hardware($backface: true, $perspective: 1000) {
    @if $backface {
        backface-visibility: hidden;
    }
    perspective: $perspective;
}
```
## 10. Truncate

Adding Truncation to an element isn't as simple as it should be, although it is with this handy time saver, meaning when you include the mixin, you only have to specify your boundry with a max width value, happy days!

```
@mixin truncate($truncation-boundary) {
    max-width: $truncation-boundary;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

```
## 12.  ADD VENDOR PREFIXES TO ANY CSS PROPERTY

``` 
/* SCSS */
@mixin css3-prefix($prop, $value) {
   -webkit-#{$prop}: #{$value};
   -moz-#{$prop}: #{$value};
   -ms-#{$prop}: #{$value};
   -o-#{$prop}: #{$value};
   #{$prop}: #{$value};
}

```
### usages
``` 
/* SCSS */
div {
   @include css3-prefix(transform, scale3d(2.5, 2, 1.5));
}

```
### compiled css

``` 
/* CSS */
div {
   -webkit-transform: scale3d(2.5, 2, 1.5);
   -moz-transform: scale3d(2.5, 2, 1.5);
   -ms-transform: scale3d(2.5, 2, 1.5);
   -o-transform: scale3d(2.5, 2, 1.5);
   transform: scale3d(2.5, 2, 1.5);
}

```

## 13. Vertical centering

``` 
/* SCSS */
@mixin vertical-center {
   position: relative;
   top: 50%;
   -ms-transform: translateY(-50%);
   -webkit-transform: translateY(-50%);
   transform: translateY(-50%);
}

```
### Usages
``` 
/* SCSS */
div {
   @include vertical-center();
}
```

### Compiled Css

 ```
 /* CSS */
div {
   position: relative;
   top: 50%;
   -ms-transform: translateY(-50%);
   -webkit-transform: translateY(-50%);
   transform: translateY(-50%);
}
 
 ```
 
 ### 14. Font face rule
 
 ``` 
 /* SCSS */
@mixin font-face($name, $file) {
   @font-face {
   font-family: "#{$name}";
   src: url("../fonts/#{$file}.eot");
   src: url("../fonts/#{$file}.eot?#iefix") format("embedded-opentype"),
   url("../fonts/#{$file}.woff") format("woff"),
   url("../fonts/#{$file}.ttf") format("truetype"),
   url("../fonts/#{$file}.svg?#webfont") format("svg");
 }
}
 
 ```

### Usages:

```
/* SCSS */
@include font-face("My Font", my-font);
```

### Compiled CSS

```
/* CSS */
@font-face {
   font-family: "My Font";
   src: url("../fonts/my-font.eot");
   src: url("../fonts/my-font.eot?#iefix") format("embedded-opentype"), 
   url("../fonts/my-font.woff") format("woff"), url("../fonts/my-font.ttf") 
   format("truetype"), url("../fonts/my-font.svg?#webfont") format("svg");
}
```

## 14. Keyframe animation

``` 
/* SCSS */
@mixin keyframes($name) {
   @-webkit-keyframes #{$name} {
     @content;
   }
   @-moz-keyframes #{$name} {
     @content;
   }
   @keyframes #{$name} {
     @content;
   }
}
```

### Usages:

 ``` 
 /* SCSS */
@include keyframes(background) {
   0% {
     background: white;
   }
   50% {
     background: lightblue;
   }
   100% {
     background: royalblue;
   } 
}
 
 ```
### Compiled Css
``` 
/* CSS */
@-webkit-keyframes background {
   0% {
     background: white;
   }
   50% {
     background: lightblue;
   }
   100% {
     background: royalblue;
   }
}
@-moz-keyframes background {
   0% {
     background: white;
   }
   50% {
     background: lightblue;
   }
   100% {
     background: royalblue;
   }
}
@keyframes background {
   0% {
     background: white;
   }
   50% {
     background: lightblue;
   }
   100% {
     background: royalblue;
   }
}

```

## 15. Retina ready images
Here is a great Sass mixin for supporting retina-ready images you can serve to Apple devices with a Retina Display. To use this mixin, you need to provide two versions of the same image, one in single size (1x) and one in double size (2x). You can use this mixin to provide a retina-ready background image for any HTML element.

```
/* SCSS */
@mixin retina-image($image, $width, $height) {
   @media (min--moz-device-pixel-ratio: 1.3),
   (-o-min-device-pixel-ratio: 2.6/2),
   (-webkit-min-device-pixel-ratio: 1.3),
   (min-device-pixel-ratio: 1.3),
   (min-resolution: 1.3dppx) {
       background-image: url($image);
       background-size: $width $height;
   }
}

```

### Usages:
``` 
/* SCSS */
.image {
    background: url("my-image.png") no-repeat;
    @include retina-image("my-image2x.png", 1000px, 500px);
}

```

### Compiled CSS

``` 
/* CSS */
.image {
   background: url("my-image.png") no-repeat;
}
@media (min--moz-device-pixel-ratio: 1.3), 
(-o-min-device-pixel-ratio: 2.6 / 2), 
(-webkit-min-device-pixel-ratio: 1.3), 
(min-device-pixel-ratio: 1.3), 
(min-resolution: 1.3dppx) {
   .image {
     background-image: url("my-image2x.png");
     background-size: 1000px 500px;
   }
}

```

## 16. Absolute Postitioning

```
/* SCSS */
@mixin abs-position ($top, $right, $bottom, $left) {
   position: absolute; 
   top: $top;
   right: $right;
   bottom: $bottom;
   left: $left;
}
```

### Usages:
``` 
/* SCSS */
div {
   @include abs-position(100px, 100px, auto, auto);
}
```
### Compiled CSS
``` 
/* CSS */
div {
   position: absolute;
   top: 100px;
   right: 100px;
   bottom: auto;
   left: auto;
}

```

## 16. Arrow with four optional directions

``` 

/* SCSS */
@mixin arrow($direction: down, $size: 5px, $color: #555) {
   width: 0;
   height: 0;
   @if ($direction == left) {
      border-top: $size solid transparent;
      border-bottom: $size solid transparent; 
      border-right: $size solid $color;
   }
   @else if ($direction == right) {
      border-top: $size solid transparent;
      border-bottom: $size solid transparent; 
      border-left: $size solid $color;
   }
   @else if ($direction == down) {
      border-left: $size solid transparent;
      border-right: $size solid transparent;
      border-top: $size solid $color;
   }
   @else {
      border-left: $size solid transparent;
      border-right: $size solid transparent;
      border-bottom: $size solid $color;
   }
}

```

### Usages

``` 

/* SCSS */

// without arguments (default)
div { 
   @include arrow(); 
}

// with custom arguments
div {
   @include arrow(up, 10px, #efefef);
}

```

### Compiled CSS
``` 
/* CSS */

// without arguments (default)
div {
   width: 0;
   height: 0;
   border-left: 5px solid transparent;
   border-right: 5px solid transparent;
   border-top: 5px solid #555;
}

// with custom arguments
div {
   width: 0;
   height: 0;
   border-left: 10px solid transparent;
   border-right: 10px solid transparent;
   border-bottom: 10px solid #efefef;
}

```

## 16. MEDIA QUERIES FOR MOBILE-FIRST DESIGN

``` 
/* SCSS */
@mixin breakpoint($point) {
   @if $point == large {
      @media only screen and (min-width: 1366px) {
         @content;
      }
   } 
   @else if $point == desktop {
      @media only screen and (min-width: 1024px) {
         @content;
      }
   } 
   @else if $point == tablet {
      @media only screen and (min-width: 600px) {
         @content;
      }
   }
}

```

### Usages:

``` 
/* SCSS */
@include breakpoint(large) {
   div {
      font-size: 2rem;
      line-height: 1.4;
   }
}
```
### Compiled CSS

``` 
/* CSS */
@media only screen and (min-width: 1366px) {
   div {
      font-size: 2rem;
      line-height: 1.4;
   }
}
```

## 17. Fixed Aspect Ratio

``` 
/* SCSS */
@mixin aspect-ratio($width, $height) {
   position: relative;
   &:before {
      display: block;
      content: "";
      width: 100%;
      padding-top: ($height / $width) * 100%;
   }
   > .inner-box {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
   }
}

```

### Usages:
``` 
<!-- HTML -->
<div class="outer-box">
   <div class="inner-box"></div>
</div>

```
 
 ```
 
/* SCSS */
.outer-box {
   @include aspect-ratio(4, 3);
}
 
 ```
 
 ### Usages:
 ```
 /* CSS */
.outer-box {
   position: relative;
}
.outer-box:before {
   display: block;
   content: "";
   width: 100%;
   padding-top: 75%;
}
.outer-box > .inner-box {
   position: absolute;
   top: 0;
   left: 0;
   right: 0;
   bottom: 0;
}
 ```
 
 ## 19. Text Shortening
 
 ``` 
 /* SCSS */
@mixin text-shorten {
   overflow: hidden;
   text-overflow: ellipsis;
   white-space: nowrap;
}
 
 ```
 
 ### Usages
 
 ``` 
 /* SCSS */
div {
   @include text-shorten();
}
 ```
 
 ### Compiled CSS
 ``` 
 /* CSS */
div {
   overflow: hidden;
   text-overflow: ellipsis;
   white-space: nowrap;
}
 ```















