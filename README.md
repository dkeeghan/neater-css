# Neater CSS: Setting structure for simpler maintenance

## Background

CSS, at first glance, appears deceptively simple and intuitive, however, as web applications grew in complexity and size, so did the challenges of managing and maintaining CSS at scale.

Approaches like BEM, while effective in creating modular and reusable CSS, led to verbose and repetitive class names making HTML templates less readable and harder to maintain. 

CSS-in-JS solutions, while offering powerful scoping mechanisms, introduced performance overhead, a significantly more complicated development workflow, and while they solved scoping issues, they also caused the cascading part of CSS to be ignored, limiting reusibility and global theming approaches, and limiting the flexibility of global styles, making it difficult to share styles between components.

## A new (old) approach

We take an old approach and make it new using [CSS Nesting](https://www.w3.org/TR/css-nesting-1/) by using container classes to scope components, and 'private' CSS classes similar to BEM, but much shorted class names in your HTML.

The solution involves:

1. **Container Classes**: A class that sits on the container of a reusable component - uses prefixes to indicate the purpose of the container, e.g. `.c-` for components, `.l-` for layouts etc...
1. **Modifier Classes**: A class that sits alongside the Container class to modify the container using a prefix like `.has-`, `.is-` to indicate the state or variant. This class is *ONLY* used alongside a container class, never by itself, e.g. `.c-card.has-video` and NOT `.has-video`
1. **Private Classes**: A 'private' class only used inside containers starting with the prefix `._` and short descriptive class names like `._content`, `._media`, `._tags`. This class is *ONLY* used as a child class to a container class, never by itself e.g. `.c-card ._content` and not `._content`.
1. **Global Theme Variables / Design Tokens**: Variables for items including colours, fonts, spacing, layout and primitive elements
1. **Primitive Elements**: Global styles for primitive elements like Headings, Rich Text, Form Controls

This approach empowers developers to:

* **Simplify HTML:** Reduce the clutter of class names, making templates more readable and easier to understand.
* **Puts Class Logic on the Container:** Moving style variation logic to the container only, so styles only change in a single location (the container) simplifying both logic in the HTML and the CSS
* **Enhance Reusability:** Create reusable style components that leverage global theme variables, but are also scoped to itself so it can be easily shared across different sites/applications.
* **Improve Maintainability:** Organize CSS into logical themes, global primitives, and component-specific styles, promoting better code organisation and reducing the risk of style conflicts using the 'private' class names.
* **Maximize Flexibility:** Maintain the ability to define global styles and override them as needed, providing the flexibility required for complex web applications.

By adopting this new CSS scoping approach, developers can leverage the full potential of CSS, creating more maintainable, scalable, and performant web applications which can be reused across projects and evolved over time. 

## Container, Modifier and Private classes

BEM always had a good structure, however often got unweildy with a huge amount of duplication of text in class names solely for the purantan view of having as little nested classes as possible.

Using classes with prefixes to indicate the distinct purpose of the class ensures readibility, and also gives a clear indication of scope.

### Container Classes

Container Classes sit on the container of a reusable component. It uses a short prefix to indicate the purpose of the container, and a simple clear and readable name, e.g. 

* `.c-` for components like `.c-card` for a reusable card componennt and `.c-hero-banner` for a hero banner
* `.g-` for global elements like `.g-header` and `.g-footer` for the page global header/footer
* `.l-` for layouts like `.l-main` for the main page layout, and `.l-cols` for a reusable column layout container.
* `.m-` for smaller reusabable modules like `.m-tags`, `.m-image` which are modules that can be used inside multiple components.

The Container Class is used in CSS as the scoping class for all child styles. 

#### Styling Containers inside Containers

You should never style a container inside another container in CSS, use a Modifier Class (see below) instead which allows you to reuse the customised style anywhere, giving you significantly more flexibility and reusability.

##### DON'T: Add specific styles to the child container based on it's parent container

```html 
<div class="c-card">
    <div class="c-image"></div>
</div>
```

```scss
.c-card .c-image {
    /* unique styles for .c-image when inside of .c-card */
}
```


##### DO: Use Modifer Classes to adjust styles and add class to HTML when inside the Parent Container

```html 
<div class="c-card">
    <div class="c-image is-card"></div>
</div>
```

```scss
.c-card {}

.c-image.is-card {
    /* unique styles for .c-image when inside of .c-card */
}
```

#### Positioning Containers inside Containers

If you need to use CSS to position a container inside another container use a *Private Class* alongside the container in order to position it. This is important to ensure that the CSS for the design of the container is decoupled from how it's placed on the page and ensures proper scoping to either the parent page (the main *Container Class*), or the parent component (the *Private Class* contained inside the Parent Container)

##### DON'T: Position child containers using Container Classes

```html
<div class="c-card">
    <div class="m-tags"></div>
</div>
```

```scss
.c-card .m-tags {
    /* position .m-tags */
}
```

##### DO: Position child containers using Private Classes

```html
<div class="c-card">
    <div class="m-tags _tags"></div>
</div>
```

```scss
.c-card ._tags {
    /* position ._tags */
}
```

***NOTE***: only adjust styles to position this container, if you need to change child styles, refer to "Styling Containers in Containers" above 

### Modifier Classes

The Modifier Class sits alongside the Container class to modify the container using a prefix like `.has-`, `.is-` to indicate a state or variant of the container or module. This class is *ONLY* used alongside a container class, never by itself.

#### DON'T: Use a Modifier Class by itself

```scss
.has-image {}
.is-hero {}
```

#### DO: Use a Modifier Class in combination with a Container Class

```scss
.c-card.has-image {}
.c-banner.is-hero {}
```

### Private Classes

A 'private' class only used inside containers starting with the prefix `._` and short descriptive class names like `._content`, `._media`, `._tags`. This class is *ONLY* used as a child class to a container class, never by itself

#### DON'T: Use a Private Class by itself

```scss
._text {}
._image {}
```

#### DO: Use a Private Class as a child of a Container Class

```scss
.c-card ._text {}
.c-banner ._image {}
```

### Global Theme Variables / Design Tokens

Variables for design elements that generally change per brand and site across projects, this would include colours, fonts, spacing, layout and primitive elements. There would be an element of smart defaults that can be applied here in order to make the default styles to still look "un-designed" but still in good shape.

#### Theme & Branding

Think about a brand theme in the context of primary, secondary and tertiary colours - this avoids the need to extensively change variable names through the rest of the components if the new brand getting applied aligns to a similar primary/secondary alignment as the default theme. Also include lighter and darker variations of the theme colours if needed and a range of greyscale as needed

```scss
:root {
    // primary brand
	--theme-primary: #92BB44;
	--theme-primary-lighter: #A7DD40;
	--theme-primary-darker: #43B02A;
	--theme-primary-a11y: #2b741b;

	// secondary palette
	--theme-secondary: #FFBFFB;
	
    // tertiary palette
    --theme-tertiary: #A9D2CF;

    // warnings/alerts palette
    --theme-status-success: #43B02A; // Green
    --theme-status-warning: #FFC107; // Yellow
    --theme-status-alert: #FF3300; // Red

	// greyscale
	--theme-white: #FFF;
	--theme-grey-100: #F9F9F9;
	--theme-grey-200: #E1E1DA;
	--theme-grey-300: #A4A5A6;
	--theme-grey-400: #999;
	--theme-grey-500: #767779;
	--theme-grey-600: #5b5b5d;
	--theme-grey-700: #3F3F3F;
	--theme-grey-800: #272727;
	--theme-grey-900: #1B1D20;
	--theme-black: #000;
}
```

#### Text, Borders & Links

Set your theme text color and borders globally as variables. By setting an inverted variation 

```scss 
// text & borders
--theme-heading-color: var(--theme-grey-900);
--theme-paragraph-color: var(--theme-grey-900);
--theme-paragraph-subdued-color: var(--theme-grey-500);
--theme-paragraph-primary-color: var(--theme-primary);
--theme-border: color-mix(in srgb, var(--theme-grey-900) 10%, transparent);

// text & borders on inverted (dark) background
--theme-heading-color-on-inverted: var(--theme-white);
--theme-paragraph-color-on-inverted: var(--theme-white);
--theme-paragraph-subdued-color-on-inverted: var(--theme-grey-300);
--theme-paragraph-primary-color-on-inverted: var(--theme-primary);
--theme-border-on-inverted: color-mix(in srgb, var(--theme-white) 10%, transparent);

// links
--theme-link-color-default: var(--theme-paragraph-color);
--theme-link-color-visited: var(--theme-paragraph-color);
--theme-link-color-hover: var(--theme-primary);
--theme-link-color-active: var(--theme-primary-darker);
```


#### 'Little touches' to consider for themes

CSS has some interesting properties which you can use to apply your theme to the browser, small simple touches can make a big impact:

* `accent-color` sets the accent colour of Checkbox, Radio, Range and Progress elements
```scss
:root {
    accent-color: var(--theme-primary);
}
```

* `outline-color` inside `:focus-visible` sets the default outline colour when you focus on an item
```scss
:focus-visible {
    outline-color: var(--theme-secondary);
}
```

* `::selection` sets the highlight colour when you select text
``` scss
::selection { 
    background-color: color-mix(in srgb, var(--theme-secondary) 50%, transparent);
}
```

* `::marker` sets the colour of the marker in the psuedo-element of an item set to `display: list-item` (e.g. `li` and `summary`)
``` scss
::marker {
    color: var(--theme-primary);
}
```

#### Accessibility overrides

If you have theme variables that the client wants to use which aren't accessible, however there is a Level AA requirement, you can override the theme variables based on a class on the `html` tag. Try to avoid component level changes, focus on only updating the global variables (i.e. give text more contrast and backgrounds less).

```scss
html.use-theme-a11y {
	--theme-paragraph-color: var(--theme-grey-900);
	--theme-paragraph-subdued-color: var(--theme-grey-600);
	--theme-paragraph-primary-color: var(--theme-primary-a11y);
}
```

### Primitive Elements

Global styles for primitive elements like Headings, Rich Text, Form Controls


## CSS Nesting

### What is CSS Nesting?

CSS Nesting allows you to add child selectors inside a parent in order to reduce duplication of classes in the CSS file, both reducing the size of the file and making it significantly more readible.

### Browser Compatibility

[CSS Nesting](https://caniuse.com/css-nesting) has been [Baseline](https://web-platform-dx.github.io/web-features/) since December 2023, and is supported by all major browsers with a minor note for Samsung Internet browser that the `&` must be used for child elements.

If you need wider browser support, you can leverage SCSS to achieve the same thing.

#### Traditional approach:

```css
.c-container {
    /* container styles */
}

.c-container ._content {
    /* content styles for container */
}

.c-container.is-variant {
    /* variant styles for container */
}

.c-container.is-variant ._content {
    /* variant styles for content of container */
}

@media (width >= 50rem) {
    .c-container {
        /* larger screen styles */
    }
}

.m-module {
    /* module styles */
}
```

#### Nested approach:

```scss
.c-container {
    /* container styles */

    & ._content {
         /* content styles for container */
    }

    &.is-variant {
        /* variant styles for container */

        & ._content {
              /* variant styles for content of container */
        }
    }

    @media (width >= 50rem) {
        /* larger screen styles */
    }
}

.m-module {
    /* module styles */
}
```

