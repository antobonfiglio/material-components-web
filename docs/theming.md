# MDC-Web Theming Guide

## Overview

MDC-Web includes a theming system designed to make it easy to change your application's colors. It provides multiple options
for implementing themes, allowing for maximum flexibility. At the moment, MDC-Web supports theming with Sass and with CSS
Custom Property, with plans for CDN support as well, once that service is available.

## Colors

MDC-Web theming, like Material Design theming, uses two main colors: **primary** and **accent**. The primary color is used
throughout most of the application and components, as the main color for your application. The accent color is used
for floating action buttons and other interactive elements, serving as visual contrast to the primary.

In addition to the primary and accent colors, MDC-Web also defines a _background_ color, which is used as a background in
components, and usually as the page background as well.

Finally, MDC-Web has a number of text colors, which are used for rendering text and other shapes on top of the primary,
accent and background colors. These are specified as either dark or light, in order to provide sufficient contrast to
what's behind them, and have
[different levels of opacity depending on usage](https://material.google.com/style/color.html#color-color-schemes):
- Primary, used for most text.
- Secondary, used for text which is lower in the visual hierarchy.
- Hint, used for text hints (such as those in text fields and labels).
- Disabled, used for text in disabled components and content.
- Icon, used for icons.

## Building a themed application

Let's start with a simple application, which displays several cards for different categories. We ultimately want each
card to have a color scheme that matches its category, but we'll start with the default theming provided by MDC-Web.

You can [take a look at the end result here](https://plnkr.co/edit/qxt7qpPsXgkt9UbMnE36?p=preview), but let's start from
scratch.

> Note: We won't cover the basics of starting an MDC-Web project in this guide, so please take a look at the
[getting started guide](./getting-started.md) if you need more information.

### Step 1: No theming

Here's the markup:

```html
<!DOCTYPE html>
<html class="mdc-typography">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Elements</title>
    <link rel="stylesheet" href="/node_modules/material-components-web/dist/material-components-web.css">
    <style>
      .cards {
        display: flex;
        flex-wrap: wrap;
      }

      .element-card {
        width: 16em;
        margin: 16px;
      }

      .element-card > .mdc-card__media {
        height: 9em;
      }
    </style>
  </head>
  <body>
    <h1>Choose your element</h1>
    <div class="cards">
      <div class="mdc-card element-card earth">
        <section class="mdc-card__media">
          <h1 class="mdc-card__title mdc-card__title--large">Earth</h1>
          <h2 class="mdc-card__subtitle">A solid decision.</h2>
        </section>
        <section class="mdc-card__actions">
          <button class="mdc-button mdc-button--primary mdc-button--compact mdc-card__action">Pick this element</button>
        </section>
      </div>

      <div class="mdc-card element-card wind">
        <section class="mdc-card__media">
          <h1 class="mdc-card__title mdc-card__title--large">Wind</h1>
          <h2 class="mdc-card__subtitle">Stormy weather ahead.</h2>
        </section>
        <section class="mdc-card__actions">
          <button class="mdc-button mdc-button--primary mdc-button--compact mdc-card__action">Pick this element</button>
        </section>
      </div>

      <div class="mdc-card element-card fire">
        <section class="mdc-card__media">
          <h1 class="mdc-card__title mdc-card__title--large">Fire</h1>
          <h2 class="mdc-card__subtitle">Hot-headed much?</h2>
        </section>
        <section class="mdc-card__actions">
          <button class="mdc-button mdc-button--primary mdc-button--compact mdc-card__action">Pick this element</button>
        </section>
      </div>

      <div class="mdc-card element-card water">
        <section class="mdc-card__media">
          <h1 class="mdc-card__title mdc-card__title--large">Water</h1>
          <h2 class="mdc-card__subtitle">Go with the flow.</h2>
        </section>
        <section class="mdc-card__actions">
          <button class="mdc-button mdc-button--primary mdc-button--compact mdc-card__action">Pick this element</button>
        </section>
      </div>
    </div>
  </body>
</html>
```

You'll see that we have a number of pretty empty looking cards, with black text on a white background. The only hint of
color comes from the buttons, which we've made use the primary color by adding the `mdc-button--primary` class.


### Step 2: Use the MDC-Web colors in your own markup

Not everything has a `--primary` option, though, particularly where it comes to your own markup.

Let's make things look a bit more interesting, by using the primary color as a background to the cards' media area.
One way of doing this would be to write your own custom CSS rules, and set the background to the same color that's being
used as the primary:

```css
/* Bad approach */
.element-card > .mdc-card__media {
  background-color: #3f51b5;
}
```

However, that would not take advantage of MDC-Web's theming and would thus be brittle; changes to theming would need to be
copied to your CSS rules, adding a maintenance cost.

MDC-Web provides a number of CSS classes as part of the `mdc-theme` module to help you tackle this problem in a more
maintainable way. Here are the classes that deal with primary, accent and background colors:

| Class                   | Description                                                 |
| ----------------------- | ----------------------------------------------------------- |
| `mdc-theme--primary`    | Sets the text color to the theme primary color.             |
| `mdc-theme--accent`     | Sets the text color to the theme accent color.              |
| `mdc-theme--background` | Sets the background color to the theme background color.    |
| `mdc-theme--primary-bg` | Sets the background color to the theme primary color.       |
| `mdc-theme--accent-bg`  | Sets the background color to the theme accent color.        |

From here, we can see that we want to apply `mdc-theme--primary-bg` to the cards' media areas:

```html
<div class="mdc-card element-card">
  <section class="mdc-card__media mdc-theme--primary-bg">
    <h1 class="mdc-card__title mdc-card__title--large">Earth</h1>
    <h2 class="mdc-card__subtitle">A solid decision.</h2>
  </section>
  <section class="mdc-card__actions">
    <button class="mdc-button mdc-button--primary mdc-button--compact mdc-card__action">Pick this element</button>
  </section>
</div>
```

All the cards now use the default primary color (Indigo 500 from the Material palette) as the background for the media
area, the same color that's being used for the text in the buttons.

However, you'll notice that the text in the media area is still black, which provides very little contrast to the
default primary color. Not all primary colors are dark, though, so you can't just switch the text color to white and
call it a day. Ideally, we want a solution which is as maintainable as the `mdc-theme--primary-bg` class, and which
takes into account the primary color, in order to determine whether to overlay white or black text on top.

`mdc-theme` provides utility classes for that purpose as well. Namely, for overlaying text on a primary color
background, there are:

| Class                                  | Description                                                                               |
| -------------------------------------- | ----------------------------------------------------------------------------------------- |
| `mdc-theme--text-primary-on-primary`   | Set text to suitable color for primary text on top of a theme primary color background.   |
| `mdc-theme--text-secondary-on-primary` | Set text to suitable color for secondary text on top of a theme primary color background. |
| `mdc-theme--text-hint-on-primary`      | Set text to suitable color for hint text on top of a theme primary color background.      |
| `mdc-theme--text-disabled-on-primary`  | Set text to suitable color for disabled text on top of a theme primary color background.  |
| `mdc-theme--text-icon-on-primary`      | Set text to suitable color for icons on top of a theme primary color background.          |

> Note: `primary`, `secondary`, `hint`, `disabled` and `icon` refer to the text's function. The fact that we use the
word _primary_ in the different contexts of _primary color_ and _primary function text_ can be confusing at first.

From here, we can see the right choice is `mdc-theme--text-primary-on-primary`. We could think of applying it to the
media area, but that won't work because of scoping issues. If we apply it directly to the title and subtitle, though:

```html
<div class="mdc-card element-card">
  <section class="mdc-card__media mdc-theme--primary-bg">
    <h1 class="mdc-card__title mdc-card__title--large mdc-theme--text-primary-on-primary">Earth</h1>
    <h2 class="mdc-card__subtitle mdc-theme--text-primary-on-primary">A solid decision.</h2>
  </section>
  <section class="mdc-card__actions">
    <button class="mdc-button mdc-button--primary mdc-button--compact mdc-card__action">Pick this element</button>
  </section>
</div>
```

The text is now white, which provides much better contrast. If we were to change the primary color to a light color,
however, the text would be dark again, for the same reason. So how _do_ we change the primary color?


### Step 3: Changing the theme with Sass

The application-wide theme colors that are used as the default across your entire application can be set in Sass.
This is as easy as defining three variables (`$mdc-theme-primary`, `$mdc-theme-accent` and `$mdc-theme-background`) in
your Sass file, before importing any MDC-Web modules.

```scss
// My main Sass file.
$mdc-theme-primary: #9c27b0;
$mdc-theme-accent: #76ff03;
$mdc-theme-background: #fff;

@import "material-components-web";
```

These definitions will override the defaults included in the `mdc-theme` module, which every themeable component depends
on. As for the text colors, these will all be automatically calculated from the primary, accent and background you
provide, as part of the Sass definitions in `mdc-theme`. Pretty simple!

> Note: theme colors don't have to be part of the Material palette; you can use any valid color. You may want to read
the [color section](https://material.google.com/style/color.html) in the Material Design spec to inform your pick of an
alternative palette.


### Step 4: Changing the theme with CSS Custom Properties

Changing the theme colors with Sass affects the whole application, which is great if you want consistency across the
board. What we want here is slightly different, though: we want each card to have its own internally consistent theme.

So how do we keep all the current theme color "plumbing" for maintainability, while having different themes in different
places? CSS Custom properties to the rescue!

The generated MDC-Web CSS uses CSS Custom Properties with hardcoded fallbacks, which are set to the colors provided in Sass.
This means that you can define your default theme in Sass (like we did above), but override it in CSS, dependent on
context or user preference.

Let's take a closer look at how MDC-Web does things. Here's an excerpt of a compiled MDC-Web CSS rule:

```css
.mdc-button--primary.mdc-button--raised {
  background-color: #3f51b5;
  background-color: var(--mdc-theme-primary, #3f51b5);
}
```

Here, you can see that MDC-Web sets a fallback for the color, for browsers that don't support CSS Custom Properties. If
they do, however, that declaration gets overriden by a `var()` lookup, using the same fallback as the default value
(in case the custom property definition isn't found).

As such, you can easily override the colors that get used in MDC-Web components by simply redefining the custom property at
some level. So if we want to apply it to our cards, we can take advantage of the element classes we had set up:

```css
.element-card.earth {
  --mdc-theme-primary: #795548;
}

.element-card.wind {
  --mdc-theme-primary: #9e9e9e;
}

.element-card.fire {
  --mdc-theme-primary: #f44336;
}

.element-card.water {
  --mdc-theme-primary: #00bcd4;
}
```

It works! You can see that the colors get applied to both the backgrounds and the buttons. If the cards had any other
components, they'd use the correct colors as well.

The custom properties used by MDC-Web follow a similar naming convention to the Sass variables and CSS classes:

| Custom property          | Description                 |
| ------------------------ | --------------------------- |
| `--mdc-theme-primary`    | The theme primary color.    |
| `--mdc-theme-accent`     | The theme accent color.     |
| `--mdc-theme-background` | The theme background color. |

However, if you look closely at the page, we're not quite done yet. The text colors are incorrect: the wind and water
cards should have dark text, rather than white. So what's happening?

The problem is that we only set the `--mdc-theme-primary` custom property. Whereas setting `$mdc-theme-primary` in Sass
allows for calculating all the related text colors, it's currently not possible to perform those complex contrast
calculations in CSS. This means you'll also have to set all the related text colors:

| Custom property                            | Description                                                |
| ------------------------------------------ | ---------------------------------------------------------- |
| `--mdc-theme-text-primary-on-primary`      | Primary text on top of a theme primary color background.   |
| `--mdc-theme-text-secondary-on-primary`    | Secondary text on top of a theme primary color background. |
| `--mdc-theme-text-hint-on-primary`         | Hint text on top of a theme primary color background.      |
| `--mdc-theme-text-disabled-on-primary`     | Disabled text on top of a theme primary color background.  |
| `--mdc-theme-text-icon-on-primary`         | Icons on top of a theme primary color background.          |

The same pattern is followed for text colors on _accent_ and _background_:

| Custom property                            | Description                                                |
| ------------------------------------------ | ---------------------------------------------------------- |
| `--mdc-theme-text-primary-on-accent`       | Primary text on top of a theme accent color background.    |
| `--mdc-theme-text-secondary-on-accent`     | Secondary text on top of a theme accent color background.  |
| `--mdc-theme-text-hint-on-accent`          | Hint text on top of a theme accent color background.       |
| `--mdc-theme-text-disabled-on-accent`      | Disabled text on top of a theme accent color background.   |
| `--mdc-theme-text-icon-on-accent`          | Icons on top of a theme accent color background.           |

| Custom property                            | Description                                                |
| ------------------------------------------ | ---------------------------------------------------------- |
| `--mdc-theme-text-primary-on-background`   | Primary text on top of the theme background color.         |
| `--mdc-theme-text-secondary-on-background` | Secondary text on top of the theme background color.       |
| `--mdc-theme-text-hint-on-background`      | Hint text on top of the theme background color.            |
| `--mdc-theme-text-disabled-on-background`  | Disabled text on top of the theme background color.        |
| `--mdc-theme-text-icon-on-background`      | Icons on top of the theme background color.                |

In addition, we also define custom properties for known dark and light backgrounds:


| Custom property                            | Description                                                |
| ------------------------------------------ | ---------------------------------------------------------- |
| `--mdc-theme-text-primary-on-light`        | Primary text on top of a light-colored background.         |
| `--mdc-theme-text-secondary-on-light`      | Secondary text on top of a light-colored background.       |
| `--mdc-theme-text-hint-on-light`           | Hint text on top of a light-colored background.            |
| `--mdc-theme-text-disabled-on-light`       | Disabled text on top of a light-colored background.        |
| `--mdc-theme-text-icon-on-light`           | Icons on top of a light-colored background.                |

| Custom property                            | Description                                                |
| ------------------------------------------ | ---------------------------------------------------------- |
| `--mdc-theme-text-primary-on-dark`        | Primary text on top of a dark-colored background.         |
| `--mdc-theme-text-secondary-on-dark`      | Secondary text on top of a dark-colored background.       |
| `--mdc-theme-text-hint-on-dark`           | Hint text on top of a dark-colored background.            |
| `--mdc-theme-text-disabled-on-dark`       | Disabled text on top of a dark-colored background.        |
| `--mdc-theme-text-icon-on-dark`           | Icons on top of a dark-colored background.                |


Ideally, we should set all of the text colors on primary, since we never know which one an MDC-Web component might use.
Since we're just using buttons, though, let's keep it simple for now:

```css
.element-card.earth {
  --mdc-theme-primary: #795548;
  --mdc-theme-text-primary-on-primary: var(--mdc-theme-text-primary-on-dark);
}

.element-card.wind {
  --mdc-theme-primary: #9e9e9e;
  --mdc-theme-text-primary-on-primary: var(--mdc-theme-text-primary-on-light);
}

.element-card.fire {
  --mdc-theme-primary: #f44336;
  --mdc-theme-text-primary-on-primary: var(--mdc-theme-text-primary-on-dark);
}

.element-card.water {
  --mdc-theme-primary: #00bcd4;
  --mdc-theme-text-primary-on-primary: var(--mdc-theme-text-primary-on-light);
}
```

> Note: in the future we plan to provide a Javascript utility method for changing all derived colors and making this
use-case easier.


## Dark Themes

Beyond what we've covered in this document so far, there's also the concept of a _dark theme_. All MDC-Web components have
been designed to work with both light themes (that assume a light-colored background) and dark themes (with dark-colored
backgrounds), but the default is always light.

> Note: When using a dark theme, you probably want to choose a dark color as the background for your page, and adjust
the MDC-Web `background` color to match.

In order to apply a dark theme to a single element, you can use its `--dark` class. For example, for a button:

```html
<button class="mdc-button mdc-button--raised mdc-button--dark">
  Raised dark button
</button>
```

Alternatively, you can set your entire page (or a portion of it) to a dark theme by using the `mdc-theme--dark` class:

```html
<section class="mdc-theme--dark">
  <button class="mdc-button mdc-button--raised">
    Still dark
  </button>

  <button class="mdc-button">
    Me too!
  </button>
</section>
```

> Note: there's currently no way to set a light portion inside of a dark one, so if you want to achieve that effect
you'll need to selectively apply dark classes to everything except the light bits.
