# 動機

由於 `foundation-rails` 6.4.1 版本有個 [Issue](https://github.com/zurb/foundation-rails/issues/231) 目前還沒合併。加上 Rails 已經支援了 webpack 2.x。這篇文章純粹紀錄另外一種做法。

# 準備

開始使用之前需要確保 `ruby`，`node`，`yarn` 已經安裝完成。

# 安裝流程

```bash
# 建立專案
$ rails new <project_name> --webpack

# 安裝 Foundation 相關套件
$ yarn add jquery foundation-sites motion-ui

# 安裝 script-loader （匯出至全域）
$ yarn add script-loader
```

## 設定 script-loader

新增 `config/webpack/loaders/global.js`

```js
module.exports = {
  test: /^(jquery|foundation-sites)$/,
  exclude: /node_modules/,
  loader: 'script-loader'
}
```

新增 `config/webpack/app-config.js` 擴充 webpack 設定

```js
const webpack = require('webpack')

module.exports = {
  shared: {
    plugins: [
      new webpack.ProvidePlugin({
        $: 'jquery',
        jQuery: 'jquery',
        "window.jQuery": 'jquery'
      })
    ]
  }
}
```

修改 `config/webpack/shared.js`

```js
const webpack = require('webpack')
const { basename, dirname, join, relative, resolve } = require('path')
// <略>
const appConfig = require('./app-config')

module.exports = {
  // <略>

  plugins: appConfig.shared.plugins.concat([
    new webpack.EnvironmentPlugin(JSON.parse(JSON.stringify(env))),
    new ExtractTextPlugin(env.NODE_ENV === 'production' ? '[name]-[hash].css' : '[name].css'),
    new ManifestPlugin({
      publicPath: output.publicPath,
      writeToFileEmit: true
    })
  ]),

  // <略>
}

```

## 設定 Foundation 模組

新增 `app/javascript/packs/vendor.js`

```js
import 'foundation'
```

1. 新增 `app/javascript/foundation` 目錄。
2. 新增 `app/javascript/foundation/index.js`
3. 新增 `app/javascript/foundation/application.scss`
4. 新增 `app/javascript/foundation/_settings.scss`


```js
// app/javascript/foundation/index.js

import 'jquery'
import 'foundation-sites'
import './application.scss'

$(document).ready(function () {
  $(document).foundation()
})
```

```css
// app/javascript/foundation/application.scss

@charset 'utf-8';

@import 'settings';
@import '~foundation-sites/scss/foundation';
@import '~motion-ui/src/motion-ui';

@include foundation-global-styles;
@include foundation-grid;
// @include foundation-flex-grid;
@include foundation-xy-grid-classes;
@include foundation-typography;
@include foundation-button;
@include foundation-forms;
// @include foundation-range-input;
@include foundation-accordion;
@include foundation-accordion-menu;
@include foundation-badge;
@include foundation-breadcrumbs;
@include foundation-button-group;
@include foundation-callout;
@include foundation-card;
@include foundation-close-button;
@include foundation-menu;
@include foundation-menu-icon;
@include foundation-drilldown-menu;
@include foundation-dropdown;
@include foundation-dropdown-menu;
@include foundation-responsive-embed;
@include foundation-label;
@include foundation-media-object;
@include foundation-off-canvas;
@include foundation-orbit;
@include foundation-pagination;
@include foundation-progress-bar;
// @include foundation-progress-element;
// @include foundation-progress-element;
// @include foundation-meter-element;
@include foundation-slider;
@include foundation-sticky;
@include foundation-reveal;
@include foundation-switch;
@include foundation-table;
@include foundation-tabs;
@include foundation-thumbnail;
@include foundation-title-bar;
@include foundation-tooltip;
@include foundation-top-bar;
@include foundation-visibility-classes;
@include foundation-float-classes;
@include foundation-flex-classes;

// @include foundation-prototype-classes;

@include motion-ui-transitions;
@include motion-ui-animations;
```

```css
// app/javascript/foundation/_settings.scss

//  Foundation for Sites Settings
//  -----------------------------
//
//  Table of Contents:
//
//   1. Global
//   2. Breakpoints
//   3. The Grid
//   4. Base Typography
//   5. Typography Helpers
//   6. Abide
//   7. Accordion
//   8. Accordion Menu
//   9. Badge
//  10. Breadcrumbs
//  11. Button
//  12. Button Group
//  13. Callout
//  14. Card
//  15. Close Button
//  16. Drilldown
//  17. Dropdown
//  18. Dropdown Menu
//  19. Flexbox Utilities
//  20. Forms
//  21. Label
//  22. Media Object
//  23. Menu
//  24. Meter
//  25. Off-canvas
//  26. Orbit
//  27. Pagination
//  28. Progress Bar
//  29. Prototype Arrow
//  30. Prototype Border-Box
//  31. Prototype Border-None
//  32. Prototype Bordered
//  33. Prototype Display
//  34. Prototype Font-Styling
//  35. Prototype List-Style-Type
//  36. Prototype Overflow
//  37. Prototype Position
//  38. Prototype Rounded
//  39. Prototype Separator
//  40. Prototype Shadow
//  41. Prototype Sizing
//  42. Prototype Spacing
//  43. Prototype Text-Decoration
//  44. Prototype Text-Transformation
//  45. Prototype Text-Utilities
//  46. Responsive Embed
//  47. Reveal
//  48. Slider
//  49. Switch
//  50. Table
//  51. Tabs
//  52. Thumbnail
//  53. Title Bar
//  54. Tooltip
//  55. Top Bar
//  56. Xy Grid

@import '~foundation-sites/scss/util/util';

// 1. Global
// ---------

$global-font-size: 100%;
$global-width: rem-calc(1200);
$global-lineheight: 1.5;
$foundation-palette: (
  primary: #1779ba,
  secondary: #767676,
  tertiary: #ff00ff,
  success: #3adb76,
  warning: #ffae00,
  alert: #cc4b37,
);
$light-gray: #e6e6e6;
$medium-gray: #cacaca;
$dark-gray: #8a8a8a;
$black: #0a0a0a;
$white: #fefefe;
$body-background: $white;
$body-font-color: $black;
$body-font-family: 'Helvetica Neue', Helvetica, Roboto, Arial, sans-serif;
$body-antialiased: true;
$global-margin: 1rem;
$global-padding: 1rem;
$global-position: 1rem;
$global-weight-normal: normal;
$global-weight-bold: bold;
$global-radius: 0;
$global-menu-padding: 0.7rem 1rem;
$global-menu-nested-margin: 1rem;
$global-text-direction: ltr;
$global-flexbox: true;
$global-prototype-breakpoints: false;
$global-color-pick-contrast-tolerance: 0;
$print-transparent-backgrounds: true;

@include add-foundation-colors;

// 2. Breakpoints
// --------------

$breakpoints: (
  small: 0,
  medium: 640px,
  large: 1024px,
  xlarge: 1200px,
  xxlarge: 1440px,
);
$print-breakpoint: large;
$breakpoint-classes: (small medium large);

// 3. The Grid
// -----------

$grid-row-width: $global-width;
$grid-column-count: 12;
$grid-column-gutter: (
  small: 20px,
  medium: 30px,
);
$grid-column-align-edge: true;
$grid-column-alias: 'columns';
$block-grid-max: 8;

// 4. Base Typography
// ------------------

$header-font-family: $body-font-family;
$header-font-weight: $global-weight-normal;
$header-font-style: normal;
$font-family-monospace: Consolas, 'Liberation Mono', Courier, monospace;
$header-color: inherit;
$header-lineheight: 1.4;
$header-margin-bottom: 0.5rem;
$header-styles: (
  small: (
    'h1': ('font-size': 24),
    'h2': ('font-size': 20),
    'h3': ('font-size': 19),
    'h4': ('font-size': 18),
    'h5': ('font-size': 17),
    'h6': ('font-size': 16),
  ),
  medium: (
    'h1': ('font-size': 48),
    'h2': ('font-size': 40),
    'h3': ('font-size': 31),
    'h4': ('font-size': 25),
    'h5': ('font-size': 20),
    'h6': ('font-size': 16),
  ),
);
$header-text-rendering: optimizeLegibility;
$small-font-size: 80%;
$header-small-font-color: $medium-gray;
$paragraph-lineheight: 1.6;
$paragraph-margin-bottom: 1rem;
$paragraph-text-rendering: optimizeLegibility;
$code-color: $black;
$code-font-family: $font-family-monospace;
$code-font-weight: $global-weight-normal;
$code-background: $light-gray;
$code-border: 1px solid $medium-gray;
$code-padding: rem-calc(2 5 1);
$anchor-color: $primary-color;
$anchor-color-hover: scale-color($anchor-color, $lightness: -14%);
$anchor-text-decoration: none;
$anchor-text-decoration-hover: none;
$hr-width: $global-width;
$hr-border: 1px solid $medium-gray;
$hr-margin: rem-calc(20) auto;
$list-lineheight: $paragraph-lineheight;
$list-margin-bottom: $paragraph-margin-bottom;
$list-style-type: disc;
$list-style-position: outside;
$list-side-margin: 1.25rem;
$list-nested-side-margin: 1.25rem;
$defnlist-margin-bottom: 1rem;
$defnlist-term-weight: $global-weight-bold;
$defnlist-term-margin-bottom: 0.3rem;
$blockquote-color: $dark-gray;
$blockquote-padding: rem-calc(9 20 0 19);
$blockquote-border: 1px solid $medium-gray;
$cite-font-size: rem-calc(13);
$cite-color: $dark-gray;
$cite-pseudo-content: '\2014 \0020';
$keystroke-font: $font-family-monospace;
$keystroke-color: $black;
$keystroke-background: $light-gray;
$keystroke-padding: rem-calc(2 4 0);
$keystroke-radius: $global-radius;
$abbr-underline: 1px dotted $black;

// 5. Typography Helpers
// ---------------------

$lead-font-size: $global-font-size * 1.25;
$lead-lineheight: 1.6;
$subheader-lineheight: 1.4;
$subheader-color: $dark-gray;
$subheader-font-weight: $global-weight-normal;
$subheader-margin-top: 0.2rem;
$subheader-margin-bottom: 0.5rem;
$stat-font-size: 2.5rem;

// 6. Abide
// --------

$abide-inputs: true;
$abide-labels: true;
$input-background-invalid: get-color(alert);
$form-label-color-invalid: get-color(alert);
$input-error-color: get-color(alert);
$input-error-font-size: rem-calc(12);
$input-error-font-weight: $global-weight-bold;

// 7. Accordion
// ------------

$accordion-background: $white;
$accordion-plusminus: true;
$accordion-title-font-size: rem-calc(12);
$accordion-item-color: $primary-color;
$accordion-item-background-hover: $light-gray;
$accordion-item-padding: 1.25rem 1rem;
$accordion-content-background: $white;
$accordion-content-border: 1px solid $light-gray;
$accordion-content-color: $body-font-color;
$accordion-content-padding: 1rem;

// 8. Accordion Menu
// -----------------

$accordionmenu-padding: $global-menu-padding;
$accordionmenu-nested-margin: $global-menu-nested-margin;
$accordionmenu-submenu-padding: $accordionmenu-padding;
$accordionmenu-arrows: true;
$accordionmenu-arrow-color: $primary-color;
$accordionmenu-item-background: null;
$accordionmenu-border: null;
$accordionmenu-submenu-toggle-background: null;
$accordion-submenu-toggle-border: $accordionmenu-border;
$accordionmenu-submenu-toggle-width: 40px;
$accordionmenu-submenu-toggle-height: $accordionmenu-submenu-toggle-width;
$accordionmenu-arrow-size: 6px;

// 9. Badge
// --------

$badge-background: $primary-color;
$badge-color: $white;
$badge-color-alt: $black;
$badge-palette: $foundation-palette;
$badge-padding: 0.3em;
$badge-minwidth: 2.1em;
$badge-font-size: 0.6rem;

// 10. Breadcrumbs
// ---------------

$breadcrumbs-margin: 0 0 $global-margin 0;
$breadcrumbs-item-font-size: rem-calc(11);
$breadcrumbs-item-color: $primary-color;
$breadcrumbs-item-color-current: $black;
$breadcrumbs-item-color-disabled: $medium-gray;
$breadcrumbs-item-margin: 0.75rem;
$breadcrumbs-item-uppercase: true;
$breadcrumbs-item-separator: true;
$breadcrumbs-item-separator-item: '/';
$breadcrumbs-item-separator-item-rtl: '\\';
$breadcrumbs-item-separator-color: $medium-gray;

// 11. Button
// ----------

$button-font-family: inherit;
$button-padding: 0.85em 1em;
$button-margin: 0 0 $global-margin 0;
$button-fill: solid;
$button-background: $primary-color;
$button-background-hover: scale-color($button-background, $lightness: -15%);
$button-color: $white;
$button-color-alt: $black;
$button-radius: $global-radius;
$button-hollow-border-width: 1px;
$button-sizes: (
  tiny: 0.6rem,
  small: 0.75rem,
  default: 0.9rem,
  large: 1.25rem,
);
$button-palette: $foundation-palette;
$button-opacity-disabled: 0.25;
$button-background-hover-lightness: -20%;
$button-hollow-hover-lightness: -50%;
$button-transition: background-color 0.25s ease-out, color 0.25s ease-out;

// 12. Button Group
// ----------------

$buttongroup-margin: 1rem;
$buttongroup-spacing: 1px;
$buttongroup-child-selector: '.button';
$buttongroup-expand-max: 6;
$buttongroup-radius-on-each: true;

// 13. Callout
// -----------

$callout-background: $white;
$callout-background-fade: 85%;
$callout-border: 1px solid rgba($black, 0.25);
$callout-margin: 0 0 1rem 0;
$callout-padding: 1rem;
$callout-font-color: $body-font-color;
$callout-font-color-alt: $body-background;
$callout-radius: $global-radius;
$callout-link-tint: 30%;

// 14. Card
// --------

$card-background: $white;
$card-font-color: $body-font-color;
$card-divider-background: $light-gray;
$card-border: 1px solid $light-gray;
$card-shadow: none;
$card-border-radius: $global-radius;
$card-padding: $global-padding;
$card-margin-bottom: $global-margin;

// 15. Close Button
// ----------------

$closebutton-position: right top;
$closebutton-offset-horizontal: (
  small: 0.66rem,
  medium: 1rem,
);
$closebutton-offset-vertical: (
  small: 0.33em,
  medium: 0.5rem,
);
$closebutton-size: (
  small: 1.5em,
  medium: 2em,
);
$closebutton-lineheight: 1;
$closebutton-color: $dark-gray;
$closebutton-color-hover: $black;

// 16. Drilldown
// -------------

$drilldown-transition: transform 0.15s linear;
$drilldown-arrows: true;
$drilldown-padding: $global-menu-padding;
$drilldown-nested-margin: 0;
$drilldown-background: $white;
$drilldown-submenu-padding: $drilldown-padding;
$drilldown-submenu-background: $white;
$drilldown-arrow-color: $primary-color;
$drilldown-arrow-size: 6px;

// 17. Dropdown
// ------------

$dropdown-padding: 1rem;
$dropdown-background: $body-background;
$dropdown-border: 1px solid $medium-gray;
$dropdown-font-size: 1rem;
$dropdown-width: 300px;
$dropdown-radius: $global-radius;
$dropdown-sizes: (
  tiny: 100px,
  small: 200px,
  large: 400px,
);

// 18. Dropdown Menu
// -----------------

$dropdownmenu-arrows: true;
$dropdownmenu-arrow-color: $anchor-color;
$dropdownmenu-arrow-size: 6px;
$dropdownmenu-arrow-padding: 1.5rem;
$dropdownmenu-min-width: 200px;
$dropdownmenu-background: $white;
$dropdownmenu-submenu-background: $dropdownmenu-background;
$dropdownmenu-padding: $global-menu-padding;
$dropdownmenu-nested-margin: 0;
$dropdownmenu-submenu-padding: $dropdownmenu-padding;
$dropdownmenu-border: 1px solid $medium-gray;
$dropdown-menu-item-color-active: get-color(primary);
$dropdown-menu-item-background-active: transparent;

// 19. Flexbox Utilities
// ---------------------

$flex-source-ordering-count: 6;
$flexbox-responsive-breakpoints: true;

// 20. Forms
// ---------

$fieldset-border: 1px solid $medium-gray;
$fieldset-padding: rem-calc(20);
$fieldset-margin: rem-calc(18 0);
$legend-padding: rem-calc(0 3);
$form-spacing: rem-calc(16);
$helptext-color: $black;
$helptext-font-size: rem-calc(13);
$helptext-font-style: italic;
$input-prefix-color: $black;
$input-prefix-background: $light-gray;
$input-prefix-border: 1px solid $medium-gray;
$input-prefix-padding: 1rem;
$form-label-color: $black;
$form-label-font-size: rem-calc(14);
$form-label-font-weight: $global-weight-normal;
$form-label-line-height: 1.8;
$select-background: $white;
$select-triangle-color: $dark-gray;
$select-radius: $global-radius;
$input-color: $black;
$input-placeholder-color: $medium-gray;
$input-font-family: inherit;
$input-font-size: rem-calc(16);
$input-font-weight: $global-weight-normal;
$input-line-height: $global-lineheight;
$input-background: $white;
$input-background-focus: $white;
$input-background-disabled: $light-gray;
$input-border: 1px solid $medium-gray;
$input-border-focus: 1px solid $dark-gray;
$input-padding: $form-spacing / 2;
$input-shadow: inset 0 1px 2px rgba($black, 0.1);
$input-shadow-focus: 0 0 5px $medium-gray;
$input-cursor-disabled: not-allowed;
$input-transition: box-shadow 0.5s, border-color 0.25s ease-in-out;
$input-number-spinners: true;
$input-radius: $global-radius;
$form-button-radius: $global-radius;

// 21. Label
// ---------

$label-background: $primary-color;
$label-color: $white;
$label-color-alt: $black;
$label-palette: $foundation-palette;
$label-font-size: 0.8rem;
$label-padding: 0.33333rem 0.5rem;
$label-radius: $global-radius;

// 22. Media Object
// ----------------

$mediaobject-margin-bottom: $global-margin;
$mediaobject-section-padding: $global-padding;
$mediaobject-image-width-stacked: 100%;

// 23. Menu
// --------

$menu-margin: 0;
$menu-nested-margin: $global-menu-nested-margin;
$menu-items-padding: $global-menu-padding;
$menu-simple-margin: 1rem;
$menu-item-color-active: $white;
$menu-item-background-active: get-color(primary);
$menu-icon-spacing: 0.25rem;
$menu-item-background-hover: $light-gray;
$menu-state-back-compat: true;
$menu-centered-back-compat: true;

// 24. Meter
// ---------

$meter-height: 1rem;
$meter-radius: $global-radius;
$meter-background: $medium-gray;
$meter-fill-good: $success-color;
$meter-fill-medium: $warning-color;
$meter-fill-bad: $alert-color;

// 25. Off-canvas
// --------------

$offcanvas-size: 250px;
$offcanvas-vertical-size: 250px;
$offcanvas-background: $light-gray;
$offcanvas-shadow: 0 0 10px rgba($black, 0.7);
$offcanvas-inner-shadow-size: 20px;
$offcanvas-inner-shadow-color: rgba($black, 0.25);
$offcanvas-overlay-zindex: 11;
$offcanvas-push-zindex: 12;
$offcanvas-overlap-zindex: 13;
$offcanvas-reveal-zindex: 12;
$offcanvas-transition-length: 0.5s;
$offcanvas-transition-timing: ease;
$offcanvas-fixed-reveal: true;
$offcanvas-exit-background: rgba($white, 0.25);
$maincontent-class: 'off-canvas-content';

// 26. Orbit
// ---------

$orbit-bullet-background: $medium-gray;
$orbit-bullet-background-active: $dark-gray;
$orbit-bullet-diameter: 1.2rem;
$orbit-bullet-margin: 0.1rem;
$orbit-bullet-margin-top: 0.8rem;
$orbit-bullet-margin-bottom: 0.8rem;
$orbit-caption-background: rgba($black, 0.5);
$orbit-caption-padding: 1rem;
$orbit-control-background-hover: rgba($black, 0.5);
$orbit-control-padding: 1rem;
$orbit-control-zindex: 10;

// 27. Pagination
// --------------

$pagination-font-size: rem-calc(14);
$pagination-margin-bottom: $global-margin;
$pagination-item-color: $black;
$pagination-item-padding: rem-calc(3 10);
$pagination-item-spacing: rem-calc(1);
$pagination-radius: $global-radius;
$pagination-item-background-hover: $light-gray;
$pagination-item-background-current: $primary-color;
$pagination-item-color-current: $white;
$pagination-item-color-disabled: $medium-gray;
$pagination-ellipsis-color: $black;
$pagination-mobile-items: false;
$pagination-mobile-current-item: false;
$pagination-arrows: true;

// 28. Progress Bar
// ----------------

$progress-height: 1rem;
$progress-background: $medium-gray;
$progress-margin-bottom: $global-margin;
$progress-meter-background: $primary-color;
$progress-radius: $global-radius;

// 29. Prototype Arrow
// -------------------

$prototype-arrow-directions: (
  down,
  up,
  right,
  left
);
$prototype-arrow-size: 0.4375rem;
$prototype-arrow-color: $black;

// 30. Prototype Border-Box
// ------------------------

$prototype-border-box-breakpoints: $global-prototype-breakpoints;

// 31. Prototype Border-None
// -------------------------

$prototype-border-none-breakpoints: $global-prototype-breakpoints;

// 32. Prototype Bordered
// ----------------------

$prototype-bordered-breakpoints: $global-prototype-breakpoints;
$prototype-border-width: rem-calc(1);
$prototype-border-type: solid;
$prototype-border-color: $medium-gray;

// 33. Prototype Display
// ---------------------

$prototype-display-breakpoints: $global-prototype-breakpoints;
$prototype-display: (
  inline,
  inline-block,
  block,
  table,
  table-cell
);

// 34. Prototype Font-Styling
// --------------------------

$prototype-font-breakpoints: $global-prototype-breakpoints;
$prototype-wide-letter-spacing: rem-calc(4);
$prototype-font-normal: $global-weight-normal;
$prototype-font-bold: $global-weight-bold;

// 35. Prototype List-Style-Type
// -----------------------------

$prototype-list-breakpoints: $global-prototype-breakpoints;
$prototype-style-type-unordered: (
  disc,
  circle,
  square
);
$prototype-style-type-ordered: (
  decimal,
  lower-alpha,
  lower-latin,
  lower-roman,
  upper-alpha,
  upper-latin,
  upper-roman
);

// 36. Prototype Overflow
// ----------------------

$prototype-overflow-breakpoints: $global-prototype-breakpoints;
$prototype-overflow: (
  visible,
  hidden,
  scroll
);

// 37. Prototype Position
// ----------------------

$prototype-position-breakpoints: $global-prototype-breakpoints;
$prototype-position: (
  static,
  relative,
  absolute,
  fixed
);
$prototype-position-z-index: 975;

// 38. Prototype Rounded
// ---------------------

$prototype-rounded-breakpoints: $global-prototype-breakpoints;
$prototype-border-radius: rem-calc(3);

// 39. Prototype Separator
// -----------------------

$prototype-separator-breakpoints: $global-prototype-breakpoints;
$prototype-separator-align: center;
$prototype-separator-height: rem-calc(2);
$prototype-separator-width: 3rem;
$prototype-separator-background: $primary-color;
$prototype-separator-margin-top: $global-margin;

// 40. Prototype Shadow
// --------------------

$prototype-shadow-breakpoints: $global-prototype-breakpoints;
$prototype-box-shadow: 0 2px 5px 0 rgba(0,0,0,.16),
                       0 2px 10px 0 rgba(0,0,0,.12);

// 41. Prototype Sizing
// --------------------

$prototype-sizing-breakpoints: $global-prototype-breakpoints;
$prototype-sizing: (
  width,
  height
);
$prototype-sizes: (
  25: 25%,
  50: 50%,
  75: 75%,
  100: 100%
);

// 42. Prototype Spacing
// ---------------------

$prototype-spacing-breakpoints: $global-prototype-breakpoints;
$prototype-spacers-count: 3;

// 43. Prototype Text-Decoration
// -----------------------------

$prototype-decoration-breakpoints: $global-prototype-breakpoints;
$prototype-text-decoration: (
  overline,
  underline,
  line-through,
);

// 44. Prototype Text-Transformation
// ---------------------------------

$prototype-transformation-breakpoints: $global-prototype-breakpoints;
$prototype-text-transformation: (
  lowercase,
  uppercase,
  capitalize
);

// 45. Prototype Text-Utilities
// ----------------------------

$prototype-utilities-breakpoints: $global-prototype-breakpoints;
$prototype-text-overflow: ellipsis;

// 46. Responsive Embed
// --------------------

$responsive-embed-margin-bottom: rem-calc(16);
$responsive-embed-ratios: (
  default: 4 by 3,
  widescreen: 16 by 9,
);

// 47. Reveal
// ----------

$reveal-background: $white;
$reveal-width: 600px;
$reveal-max-width: $global-width;
$reveal-padding: $global-padding;
$reveal-border: 1px solid $medium-gray;
$reveal-radius: $global-radius;
$reveal-zindex: 1005;
$reveal-overlay-background: rgba($black, 0.45);

// 48. Slider
// ----------

$slider-width-vertical: 0.5rem;
$slider-transition: all 0.2s ease-in-out;
$slider-height: 0.5rem;
$slider-background: $light-gray;
$slider-fill-background: $medium-gray;
$slider-handle-height: 1.4rem;
$slider-handle-width: 1.4rem;
$slider-handle-background: $primary-color;
$slider-opacity-disabled: 0.25;
$slider-radius: $global-radius;

// 49. Switch
// ----------

$switch-background: $medium-gray;
$switch-background-active: $primary-color;
$switch-height: 2rem;
$switch-height-tiny: 1.5rem;
$switch-height-small: 1.75rem;
$switch-height-large: 2.5rem;
$switch-radius: $global-radius;
$switch-margin: $global-margin;
$switch-paddle-background: $white;
$switch-paddle-offset: 0.25rem;
$switch-paddle-radius: $global-radius;
$switch-paddle-transition: all 0.25s ease-out;

// 50. Table
// ---------

$table-background: $white;
$table-color-scale: 5%;
$table-border: 1px solid smart-scale($table-background, $table-color-scale);
$table-padding: rem-calc(8 10 10);
$table-hover-scale: 2%;
$table-row-hover: darken($table-background, $table-hover-scale);
$table-row-stripe-hover: darken($table-background, $table-color-scale + $table-hover-scale);
$table-is-striped: true;
$table-striped-background: smart-scale($table-background, $table-color-scale);
$table-stripe: even;
$table-head-background: smart-scale($table-background, $table-color-scale / 2);
$table-head-row-hover: darken($table-head-background, $table-hover-scale);
$table-foot-background: smart-scale($table-background, $table-color-scale);
$table-foot-row-hover: darken($table-foot-background, $table-hover-scale);
$table-head-font-color: $body-font-color;
$table-foot-font-color: $body-font-color;
$show-header-for-stacked: false;
$table-stack-breakpoint: medium;

// 51. Tabs
// --------

$tab-margin: 0;
$tab-background: $white;
$tab-color: $primary-color;
$tab-background-active: $light-gray;
$tab-active-color: $primary-color;
$tab-item-font-size: rem-calc(12);
$tab-item-background-hover: $white;
$tab-item-padding: 1.25rem 1.5rem;
$tab-expand-max: 6;
$tab-content-background: $white;
$tab-content-border: $light-gray;
$tab-content-color: $body-font-color;
$tab-content-padding: 1rem;

// 52. Thumbnail
// -------------

$thumbnail-border: solid 4px $white;
$thumbnail-margin-bottom: $global-margin;
$thumbnail-shadow: 0 0 0 1px rgba($black, 0.2);
$thumbnail-shadow-hover: 0 0 6px 1px rgba($primary-color, 0.5);
$thumbnail-transition: box-shadow 200ms ease-out;
$thumbnail-radius: $global-radius;

// 53. Title Bar
// -------------

$titlebar-background: $black;
$titlebar-color: $white;
$titlebar-padding: 0.5rem;
$titlebar-text-font-weight: bold;
$titlebar-icon-color: $white;
$titlebar-icon-color-hover: $medium-gray;
$titlebar-icon-spacing: 0.25rem;

// 54. Tooltip
// -----------

$has-tip-cursor: help;
$has-tip-font-weight: $global-weight-bold;
$has-tip-border-bottom: dotted 1px $dark-gray;
$tooltip-background-color: $black;
$tooltip-color: $white;
$tooltip-padding: 0.75rem;
$tooltip-max-width: 10rem;
$tooltip-font-size: $small-font-size;
$tooltip-pip-width: 0.75rem;
$tooltip-pip-height: $tooltip-pip-width * 0.866;
$tooltip-radius: $global-radius;

// 55. Top Bar
// -----------

$topbar-padding: 0.5rem;
$topbar-background: $light-gray;
$topbar-submenu-background: $topbar-background;
$topbar-title-spacing: 0.5rem 1rem 0.5rem 0;
$topbar-input-width: 200px;
$topbar-unstack-breakpoint: medium;

// 56. Xy Grid
// -----------

$xy-grid: true;
$grid-container: $global-width;
$grid-columns: 12;
$grid-margin-gutters: (
  small: 20px,
  medium: 30px
);
$grid-padding-gutters: $grid-margin-gutters;
$block-grid-max: 8;
```

# 測試

```bash
$ rails g controller pages index
```

開啟 `app/views/layouts/application.html.erb` 於 `<head>` 加入

```html
<%= stylesheet_pack_tag 'vendor' %>
<%= javascript_pack_tag 'vendor' %>
```

開啟 `app/views/pages/index.html.erb` 加入

```html
<div class="row">
  <div class="columns">
    <ul class="accordion" data-accordion>
      <li class="accordion-item is-active" data-accordion-item>
        <a href="#" class="accordion-title">Accordion 1</a>
        <div class="accordion-content" data-tab-content >
          <p>Panel 1. Lorem ipsum dolor</p>
          <a href="#">Nowhere to Go</a>
        </div>
      </li>
      <li class="accordion-item" data-accordion-item>
        <a href="#" class="accordion-title">Accordion 2</a>
        <div class="accordion-content" data-tab-content>
          <textarea></textarea>
          <button class="button">I do nothing!</button>
        </div>
      </li>
      <li class="accordion-item" data-accordion-item>
        <a href="#" class="accordion-title">Accordion 3</a>
        <div class="accordion-content" data-tab-content>
          Type your name!
          <input type="text"></input>
        </div>
      </li>
    </ul>
  </div>
</div>
```

執行開發伺服器，開啟兩個 terminal 分別執行

```
$ bin/webpack-dev-server
$ rails server
```

瀏覽 `http://localhost:3000`，完成測試。

# 結論

此方法善用了 webpacker 提供的功能也讓我們可以設定 scss 檔案。提供了除了使用 `foundation-rails` 外的另一種方式。

* [範例程式](https://github.com/andyyou/rails-webpacker-foundation)