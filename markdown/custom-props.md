# Custom style props

Stylix allows you to create custom style props that you can use to apply reusable styles to any element. With this feature, you can extend Stylix's functionality many useful ways.

Stylix is built on a plugin system, and you could add custom props by creating a custom plugin. But because this is such a common use-case, Stylix provides `customProps`, a convenience function (a plugin factory, to be specific) that makes it easier to extend Stylix with custom props.

With the `customProps` function, you can create various kinds of custom props. It accepts an object containing new prop names as keys, and their associated style values (depending on the type of custom prop). The resulting value is a plugin you can add to your `<StylixProvider>` element's `plugins` array.

For example:

```tsx-render
import { customProps } from '@stylix/core';

const myCustomProps = customProps({
  blueHeader: {
    fontSize: 30,
    fontWeight: 'bold',
    color: 'RoyalBlue',
    textAlign: 'center'
  }
});

<StylixProvider plugins={[myCustomProps]}>
  <$.div blueHeader>
    Custom Stylix props!
  </$.div>
</StylixProvider>
```

There are three types of custom props you can create:

- Prop aliases
- Style blocks
- Style functions

### Prop aliases

Prop aliases are the simplest kind of custom prop, and simply map one prop name to another. This is useful for making shortcuts for common style props. To do this, simply specify the shortcut as the key and the original prop name as the value:

```tsx-render
import { customProps } from '@stylix/core';

const paddingShortcut = customProps({
  p: 'padding',
  bg: 'background'
});

<StylixProvider plugins={[paddingShortcut]}>
  <$.div p="20px 50px" bg="WhiteSmoke">
    A padded box.
  </$.div>
</StylixProvider>
```

Stylix actually includes a set of these shortcuts in a plugin called `tinyProps`, which can be enabled by simply importing it and adding it to the `plugins` array:

```tsx
import { tinyProps } from '@stylix/core';

<StylixProvider plugins={[tinyProps]}>
  <$.div p="20px 50px" bg="WhiteSmoke">
    A padded box.
  </$.div>
</StylixProvider>
```

### Style blocks

Style blocks let you define entire style snippets and use them with a single prop. To create a style block, specify the desired prop name as the key, and the style object as the value:

```tsx-render
import { customProps } from '@stylix/core';

const dodgerBlueShortcut = customProps({
  dodgerBlue: {
    color: 'DodgerBlue',
  }
});

<StylixProvider plugins={[dodgerBlueShortcut]}>
  <$.div dodgerBlue>
    Dodger blue text.
  </$.div>
</StylixProvider>
```

Style objects can be as complex as you want, and can include nested selectors and pseudo-classes. The props created in this way will be boolean props, and the styles will be disabled if you pass a falsy value (e.g. `dodgerBlue={false}`).

We recommend using this feature sparingly. Although it has valid uses, a more flexible and practical approach is to create custom components:

```tsx-render
const DodgerBlue = (props) => (
  <$.div color="DodgerBlue" {...props} />
);

<DodgerBlue>
  Dodger blue text (but with a custom component).
</DodgerBlue>
```

### Style functions

Similar to **style blocks**, style functions allow you to define reusable style objects, with a function instead of a plain object. The function will be passed the prop value as its only argument, and should return the styles to apply:

```tsx-render
import { customProps } from '@stylix/core';

const fontSizeAndPaddingShortcut = customProps({
  fontSizeAndPadding: (value: number) => ({
    fontSize: value,
    padding: value
  })
});

<StylixProvider plugins={[fontSizeAndPaddingShortcut]}>
  <$.div fontSizeAndPadding={20}>
    20px font-size and padding
  </$.div>
</StylixProvider>
```

Again, we recommend using this feature sparingly and preferring to use custom components for reusable functionality. But the feature exists for those use cases where you might find it useful.

## Custom props with TypeScript

If you define custom props, TypeScript will also require you to define their types so that you can safely use them on Stylix elements. This is easy to do with interface extensions: Stylix provides a `StylixPropsExtensions` interface specifically for this purpose.

The above examples would require the following extensions to work in TypeScript:

```tsx
import { Property } from 'csstype';

declare module '@stylix/core' {
  interface StylixPropsExtensions {
    p?: Property.Padding;
    bg?: Property.Background;
    dodgerBlue?: boolean;
    fontSizeAndPadding?: number;
  }
}
```

> **Note:** The `csstype` package allows you to use the same type definitions that React uses for style definitions. It is a dependency of `@types/react` and is used for typing information only, so it will not add to your bundle size.