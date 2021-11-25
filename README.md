# SX 3.0 Sample Theme
This is a template that can be used for building modern SX 3.0 themes. Even though it doesn't style much, it explains the simple syntax of `theme.json` configuration files as well as showing an implementation of an option.

## Configuration
This is a simple `theme.json` file that styles every button in the interface to a red background and white text by default. To do so, it uses the element selector `.ux_button` to select every button control, uses a default property in `elements.styling.style` to set their background color (`background-color`) to red, and assigns the text color (`color`) to an option list defined in `elements.styling.options`. Assigning a style property to an option list is done by providing the property in `elements.styling.style` the zero-based index number of the array in `elements.styling.options` (so, for instance, if you want the `color` property to be something users can choose from then you simply insert an array of options into `elements.styling.options` and set `color`'s value in `elements.styling.style` to 0 if it's the first element, 1 if it's the second, etc.)
```json
{
    "id": "sample-theme",
    "name": "Sample theme",
    "stylesheet": true,
    "elements": [
        {
            "target": { "id": "Buttons", "selectors": [".ux_button"] },

            "styling": {
                "style": {
                    "background-color": "red",
                    "color": 0
                },

                "options": [
                    [ "red", "blue", "white" ]
                ]
            }
        }
    ]
}
```
Option colors can either be a simple color string (like `red`, `blue` or `white`), an hexadecimal number preceded by a `#` (like `#ff0011`), an RGB value (like `rgb(255, 23, 3)`) or an RGBA value (like `rgba(255, 23, 3, 50%)`). If you're customizing a non-color property, such as a button's `border-radius` (corners), you can provide a number. We will provide a list of common styling patterns and properties, but for more information you can [consult Mozilla's web technology documentation.](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference)

## Stylesheet
Users with experience in writing cascading stylesheets can define the `stylesheet` property in `theme.json` to `true` and provide a stylesheet file named `{id}.css` in the root of the theme archive (where `{id}` is the id provided in `theme.json`). Writing stylesheets may be more intuitive and powerful than using the `theme.json` styling syntax but does not offer the capacity to implement options. Generally, using both makes for the best themes.

The stylesheet provided in this repository (`sample-theme.css`) replaces the global interface font with Comic Sans MS and adds a on-hover animation to every interface button using the `transition` property and `transform`.
## Common properties and patterns
There are a list of useful selectors and properties that may be commonly used when making themes. **This is not an exhaustive list** - there is a gigantic number of selectors and properties that can be used to create great customization.

| Selector | Properties | Description |
| - | - | - |
|All elements|`color`<br/>`background-color`<br/>`border-width`<br/>`border-style`<br/>`border-color`<br/>`border`|`color` can be used on any element to replace its text color. `background-color` can be used on any element to replace its background-color. `border-width`, `border-style` and `border-color` can be used to add, remove or modify the border of any selected elements. Protip: to rapidly delete a border, you can simply set the `border` property to `none`.|
|`.ux_button`<br/>`.ux_textbox_container`<br/>`.ux_checkbox_check`|`border-radius`|For people that hate corners, you can set `border-radius` to `0` to remove them from elements.|
|`.ux_checkbox_label`<br/>`.ux_checkbox_check`<br/>`.ux_textbox_label`<br/>`.ux_textbox_container_icon`|`color`<br/>`font-family`|Certain elements have specific selectors for their labels and icons. For instance, if you want to modify the color of a textbox's icon without also changing the color of its label or the text within, then you can simply use the `.ux_textbox_container_icon` selector to customize only the icon.|
|`#ban_bar`<br/>`.ban_control`<br/>`#nav_bar`<br/>`.nav_entry`|`color`<br/>`background-color`|The title bar of the window and its elements have selectors starting with `#ban`. The navigation bar and its entries have selectors starting with `.nav`.|
|`#syn_app`|`color`<br/>`background-color`|`#syn_app` is the selector for the window itself - meaning you can change the background color of the window or the global color, or pretty much any global property by changing its properties.|

**I will reiterate that this is certainly not an exhaustive list of selectors and their properties.** There are hundreds and hundreds of elements that can be customized using the SX 3.0 theming engine. You can either consult the [base stylesheet](https://github.com/synllc/hw/blob/main/src/renderer/hollywood-base.scss) for the interface over at its repository, or [consult Mozilla's web technology documentation.](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference)
## Distribution
SX 2.0 themes were directly extracted into the interface's `bin` folder. **In 3.0, themes are now distributed as individual archives** that must simply be placed into the `/themes` directory **without extraction** (akin to Minecraft resource packs if you are familiar with them). You can then load in the theme at runtime by going to options and choosing the theme of your choice under the respective dropdown.
## Legacy themes
**SX 3.0 has rudimentary and limited support for legacy SX 2.0 themes.** Due to differences between the SX 2.X interface and the SX 3.0 interface, it is essentially impossible to implement full support for old themes. **It is highly recommended that you convert any existing themes to the new format and make adjustments as necessary**. We do not promise your SX 2.0 themes will look great on the 3.0 interface - simple themes however may look fine depending on their composition.

To install a SX 2.0 theme, you simply have to copy all assets and the `theme-wpf.json` file into an archive (`zip` file), and install it into `/themes` like a normal 3.0 theme. The interface will automatically detect that it's a legacy theme and consider that accordingly on load when you select it in options.