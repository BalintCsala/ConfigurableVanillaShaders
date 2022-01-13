# ConfigurableVanillaShaders

This page is mostly for developers. If you want to use the program, please go to https://vanillashadereditor.web.app/

## Adding shaders to the list

If you are a developer and you want your work to be featured on the page, you can do so by creating editing shaders.json from this repo and creating a pull request. The format of the file is defined below.

The main contributor(s) can decline a PR if it doesn't follow the standards:

1. Use 4 spaces, not tabs, when editing the file.
2. Don't put everything in 1 line, it's only a couple of bytes, start using new lines.
3. Avoid typos.
4. The shader must be configurable in a meaningful way, otherwise having it on the site would just waste bandwidth.
5. No profanity or otherwise controversial content. Breaking this in a serious way (gore, porn, Nazi symbols, etc.) will result in a complete ban from the system.
6. Don't make the descriptions overly long to keep them from taking up a whole page.

## Testing a shaders.json file

Go to the website and click on the wrench icon at the top, then confirm that you want to view developer mode. A new button will show up on the bottom right of the page 
letting you upload shader.json files. Please test them before you open a PR.

## Shaders.json format

This documentation won't go over the JSON format, for that please refer to a [different site](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/JSON).

The file is a JSON array containing the descriptions of the shaders. Each of those are an object that should look something like this:

```json
    {
        "url": "<URL to a zip file or to the github page>",
        "link": "<Optional, if present, the link button will lead here, otherwise url will be used. Must include this if url points to a zip file>",
        "name": "<Name of the shader>",
        "description": "<The description of the shader>",
        "creator": "<Username of the creator>",
        "creatorLink": "<Optional, whatever you want your name to link to, e.g. GitHub profile page, website, CurseForge page, etc.>",
        "thumbnail": "<Optional, link to the image you want to be displayed next to the shader on the main page, preferably the pack.png>",
        "settings": [
            "<See: Settings>"
        ]
    }
```
(The default 4 space indent is intentional)

## Settings

Each setting is a JSON object with the following properties:

```json
            {
                "type": "<The type of the setting, (uniform | constant | define)>",
                "name": "<Name of the setting, must match the name inside the pack>",
                "displayName": "<Optional, what you want to display as the name of the setting>",
                "format": "<The format of the setting, more below, (enum | float | vec2 | vec3 | vec4 | int | bool)>",
                "defaultValue": "<The default value of the setting>",
                "enumValues": "<Only for enums, see 'Enum'>",
                "description": "<The description of the setting>",
                "min": "<Optional, only applicable to some formats, the minimum value the setting can reach>",
                "max": "<Optional, only applicable to some formats, the maximum value the setting can reach>",
                "step": "<Optional, only applicable to some formats, the difference between each subsequent value the setting can be>"
            }
```
(The indentation is intentional)

### Type

The three different types are:

#### Uniform

These override the `values` field of the uniforms defined in json files, for example:
```json
"uniforms": [
    { "name": "SkyColor", "type": "float", "count": 4, "values": [ 0.0, 0.0, 1.0, 1.0 ] },
    { "...": "..." }
]
```

Uniforms can only have the formats `float`, `vec2`, `vec3`, `vec4` and `enum`.

#### Constant

Constants are defined inside the shaders using the following format:
```glsl
const <format> <name> = <some value here>;

// Example:
const float FOG_STRENGTH = 10.0;
```

#### Defines

These are similar to Optifine's options system. They are also defined inside the shaders with the following format:
```glsl
// Booleans:
#define <name>
// Other types
#define <name> <some value here>

// Example
#define SKY_COLOR vec4(0, 0, 1, 1);
```

### Format

The following formats are supported:

#### Float

The `defaultValue` field must be a JSON number.

Three additional options are available when defining a float input `min`, `max` and `step`:
- `min` controls the minimum value the output can reach.
- `max` controls the maximum value the output can reach.
- `step` controls the distance between two values. This is counted from the minimum value (or 0 if it isn't defined) and it will be enforced. E.g. if `min` is 1, `step` is 0.5 and the user enters the value 1.45, then it will be rounded to 1.5.

#### Int

The `defaultValue` field must be a JSON number.

Two additional options are available when defining an integer input `min` and `max`:
- `min` controls the minimum value the output can reach.
- `max` controls the maximum value the output can reach.

#### Vec2, vec3, vec4

The `defaultValue` field must be an array with as many entries as the dimensions of the vector

Two additional options are available when defining an integer input `min` and `max`. These are done component-wise:
- `min` controls the minimum value the output can reach. Must be a JSON array containing as many numbers as the vector has components.
- `max` controls the maximum value the output can reach. Must be a JSON array containing as many numbers as the vector has components.

#### Bool

The `defaultValue` field must be a JSON boolean.

#### Enum

Enums require you to add an extra `enumValues` property to the setting. This is an array containing JSON objects with the following properties:
```json
{
    "name": "<Optional, the display name of the value, if not present, 'value' will be used instead",
    "value": "<The value you want to receive in the shader, must be a JSON number>"
}
```
(You can and probably should put these in a single line to not make the whole file 5000 lines long with a single long enum)

Example:
```json
"enumValues": [{"name": "A", "value": 0}, {"name": "B", "value": 1}, {"name": "C", "value": 2}],
```

The shaders will get the value of the selected option as a float.

## Opening PR

Fork the this repository first, edit the shaders.json file and then open a pull request.

### When adding a new shader

The PR description should link to the shader's page.

If you aren't linking to github, you will need to somehow prove you created the shader yourself or you added the options with the permission from the original creator.

There should only be a single commit saying `Adds the shader <shader name>` (Do as I say, not as I do). If you need to fix this, use `git squash`.

### When editing a shader added previously

The PR description should contain a short summary of what it adds.

There should only be a single commit saying `Edits the shader <shader name>`.
