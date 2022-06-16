# Hollywood Sample Theme
This is a template that can be used for building modern Hollywood (SX 3.0) themes. It is a direct copy of an early 2022 version of the Hollywood Dark theme.

For more information about building dynamic, animated and configurable themes, we highly suggest you take a look at our [great selection](https://github.com/synllc/hollywood/tree/main/src/renderer/style/default-themes) of default pre-included themes in the Hollywood repository. This repository serves more as a documentation reference for people in the process of building themes and researching the internals of our theming engine.

## Differences between legacy and Hollywood themes
- **SX 2.0 themes** are limited to color, font and image customization. **SX 3.0 themes** are **unlimited** in terms of customization, allowing you to change colors, fonts, borders, corners, element sizes, layouts, and much more. The power of a SX 3.0 theme is limited by the limitations of [Sass](https://sass-lang.com/) and cascading stylesheets, which are arguably the most powerful theming engines available.
- **SX 2.0 themes** are static and cannot offer theme-specific configuration and customization. **SX 3.0 themes** can be **greatly customized** by the user if the theme allows it using `theme.json` configuration records, palettes, and metrics.
- **SX 2.0 themes** often have to replace files within the installation directory to achieve extent customization, namely editor customization. **SX 3.0 themes** are **dynamically loaded** by Hollywood and does not require any modifications to the installation or source code to do most things, _including_ customizing editor colors.
- Only one **SX 2.0 theme** can be installed at a time due to their hardcoded nature, whereas multiple **SX 3.0 themes** can be installed into `/themes` and chosen within the interface.

## Legacy theme support
While Hollywood does not natively include backwards compatibility with SX 2.0 themes, it does include a theme converter, which consumes a SX 2.0 archive (`.zip` file) and outputs a valid SX 3.0 theme that can be subsequently loaded in Hollywood. Bear in mind that the theme converter is not perfect and only tries to approximate what a 2.0 theme would look on the 3.0 interface.

# Structure reference
## `theme.json` File
This is the format of the `theme.json` file, which dictates theme information, variables and metrics. Only `id` and `name` are required.
| Field | Type | Description |
| --- | --- | --- |
| `id` | `string` | Internal identifier for the theme. If `stylesheet` is `true`, its filename must be this. |
| `name` | `string` | Human-readable name for the theme that will show up in the interface. |
| `stylesheet` | `boolean` | Indicates whether or not a cascading stylesheet will be loaded. **Using a stylesheet is highly recommended as it allows you to access the most powerful theming features.** |
| `editor` | `string` | ID of the editor theme to use. `vscode`, `vscode-dark`, `synapse-dark` and `synapse-light` are supported by default (the latter two offer specialized syntax highlighting for Luau but are otherwise equivalent to their vscode analogues). You can use a default monaco theme by setting this to `custom` and supplying a theme in a file called `editor.json`. If unset, the interface will load `synapse-dark` by default. |
| `vibrancy` | `boolean` | If enabled, then this theme will be able to make use of vibrant [acrylic](https://docs.microsoft.com/en-us/windows/apps/design/style/acrylic)/[mica](https://docs.microsoft.com/en-us/windows/apps/design/style/mica) (transparent) window elements, on Windows 10 and Windows 11 respectively. Vibrancy affects are also supported on macOS. This setting will not change anything on operating systems other than Windows 10/11/macOS and is useless. Requires the experimental acrylic renderer to be enabled by the user. |
| `invertconsole` | `boolean` | Whether the luminosity of console messages should be inverted. Enable this if you have a dark theme and would like for dark-colored prints to be lightly recolored so users can read them comfortably. |
| `settingOverrides` | Dictionary mapping `string` to `any` | Allows the theme to manually override specific settings, such as the UI or editor layout configuration. Certain settings cannot be overriden by the theme for safety reasons. Each key must be the ID of the setting (IDs of indivdual settings can be found in the [Hollywood source code](https://github.com/synllc/hollywood/blob/main/src/renderer/settings-ui.tsx)) and its associated value must be the override. **The user will be prompted to allow these changes and may deny them if they desire.** |
| `groups` | Dictionary mapping `string` to `VariableGroup` | The theme can define value groups through this dictionary, and individual element properties in `list` can be bound to a variable group through its name. Each value group has a unique set value. If you point two properties towards the same value group, and the user chooses a value from that group, then these two props will have that group's chosen value. If you want a property to have the same selection of colors, but bound to distinct value groups, simply duplicate the value group. |
| `list` | Dictionary mapping `string` to `Variable` | List of variable elements. Maps a human-readable name to a Variable object. More on the variable object below. |
| `metrics` | Dictionary mapping `string` to `ThemeMetric` | List of adjustable metrics. Useful to provide layout customization to users. More on the metric object below. |

## `Variable`
| Field | Type | Description |
| --- | --- | --- |
| `groups` | Dictionary mapping `string` to `VariableGroup`  | Same as the `groups` property in `theme.json`. This can either be a value group OR a constant string specifying a CSS value. Bear in mind the string will be stiched into the resulting stylesheet - there won't be any value type detection, so if you're going to pass a string for the stylesheet (for "content" properties for instance), you need to make sure the actual quote symbols are included. |
| `map` | Dictionary mapping `string` to `string`  | Variable name-group map. Each key is the variable you want to style, and the value is the name of the value group (local and global). |
| `description` | `string` | Human-readable description for the variable that will show up in the interface. |

## `ThemeMetricValue`
| Field | Type | Description |
| --- | --- | --- |
| `default` | `number` | Default value for the metric. We suggest something midway between your minimum and maximum. |
| `minimum` (optional) | `number` | Minimum number for the metric. Defaults to `default` * -2, unless `negative` is falsy. |
| `maximum` (optional) | `number` | Maximum number for the metric. Defaults to `default` * 2. |
| `negative` (optional) | `boolean` | Sets whether or not the value can go negative. |


## `ThemeMetric`
| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Human-readable title for your metric. |
| `description` | `string` | Should describe what your metric impacts in greater detail. |
| `metric` | `ThemeMetricValue` | The data which defines the metric's behavior. See above. |

# Stylesheet duties
## Variables
**Variables** allow themes to include their own customization options. Accessible through the theme's stylesheet, their value is bound to an author-defined group of values inserted into either the variable's own local groups or in the theme's global groups, which allows for the creation of palettes and a consistent design language. If more flexibility is desired, value groups can also include a `picker`, which allows users to select the color of their choosing from a color picker. For more information about how variables work within stylesheets, please take a look at the [Sass documentation](https://sass-lang.com/documentation/variables).

Even though you can define your own variables, Hollywood already comes with a [list of variables](hollywood-dark.scss) ready to customize. Their values automatically default to the values defined in the `Hollywood Dark` theme (which is used as the base for this sample theme), so if you are making a light theme or anything that would contrast poorly with the dark palette of Hollywood, make sure to override the necessary variables or otherwise you will have an ugly Christmas sweater of a theme.

## Metrics
**Metrics** allow themes to include customization options that can impact the theme layout. Similar to variables, their values are accessible via the stylesheet, but unlike variables, they are not bound to color value groups. Instead, they are bound to the value of their sliders which can be manually adjusted by the user in the UI. Because metrics can make the UI look extremely ugly when misconfigured, it's important that the range of values which the user can pick from is limited for every single metric. `Hollywood Dark` already comes with a set of demonstration metrics, including one that controls the roundedness of buttons (as not everyone appreciates round buttons and would much rather have squares).

## Stitching point
If you take a look at the sample theme or any of the included Hollywood themes, you will see this comment after variable declarations:
```ts
//@STITCH
```
This comment is _highly important_ and your theme will **not** compile without it. This is what is called the stitching point, and this is where Hollywood will insert its own definitions, variables and utilities that you can use in your stylesheet. The stitching point **must come after** variable declarations **but not before Sass styles and directives** (such as classes, selectors, `@` commands, you name it). It is of course evident that a single `//@STITCH` must be in your stylesheet and not multiple.