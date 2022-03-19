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

## External settings files

If you want to keep your settings file in a separate settings.json file, you need to add the following object to the list in the json file in this repository:

```json
    {
        "name": "<Name of the shader>",
	"settingsLink": "<Link to your settings file. Must be a JSON file and must allow cross origin requests (e.g. raw links to files in github repos)>",
	"tags": "<Optional, see: Tags>"
    }
```

The contents are the same as what you'd have put in place of this object, so follow the steps below.

## Shaders.json format

This documentation won't go over the JSON format, for that please refer to a [different site](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/JSON).

The file is a JSON array containing the descriptions of the shaders. Each of those are an object that should look something like this:

```json
    {
        "url": "<URL to a zip file or to the github page>",
        "link": "<Optional, if present, the link button will lead here, otherwise url will be used. Must include this if url points to a zip file>",
        "name": "<Name of the shader>",
        "description": "<The description of the shader>",
		"longDescription": "<Optional, a longer version of the description. This is only displayed on the edit page. If you write \\n anywhere (double "\"!) it will add a new line>",
        "creator": "<Username of the creator>",
        "creatorLink": "<Optional, whatever you want your name to link to, e.g. GitHub profile page, website, CurseForge page, etc.>",
        "thumbnail": "<Optional, link to the image you want to be displayed next to the shader on the main page, preferably the pack.png>",
        "settings": [
            "<See: Settings>"
        ],
	"stringReplace": [
	    "<Optional, see: String replace>"	
	],
	"tags": "<Optional, see: Tags>",
	"fileFilters": [
		"<Optional, see: File filters>"
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
                "format": "<The format of the setting, more below, (enum | float | vec2 | vec3 | vec4 | int | bool | color)>",
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

#### Color

The `defaultValue` field must be an array with 3 entries in a normalized (0-1) format.

The variable must be declared as a vec3.

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

The shaders will get the value of the selected option as an integer.

## String replace

This is an optional string editing tool to update some metadata. The format:

```json
            {
                "regex": "<regex to look for>",
                "with": "<format string to replace the matches with>",
                "mapping": {
                    "the mapping table is optional": {},    
                    "<setting name>": {
                        "<value 1>": "<mapped result 1>",
                        "<value 2>": "<mapped result 2>"
                    }
                }
            }
```

In short it gives you a way to replace some string specified with a regex with a dynamically interpreted format string. 

> Examples for when to use and not to use string replace:
>  - ✔️ DO: When you want to change the description of the shader based on some values
>  - ✔️ DO: When you want to change the pack_format parameter based on a variable
>  - ✔️ DO: When the name of a uniform changes in a json file (but not in the shader code)
>  - ❌ DON'T: When you want to change what function gets called in the glsl code
>  - ❌ DON'T: When you want to modify how the code behaves in any way

The option deliberately doesn't work for shader files (.vsh/.fsh/.glsl) to discourage abuse. Use settings for those.

### Example

Let's say you created a setting to choose the version for backwards compatibility as an enum with the name `MINECRAFT_VERSION` and following values:

 - 11700 for 1.17
 - 11800 for 1.18
 - 11801 for 1.18.1
 - 11802 for 1.18.2

If you want to update the version id in the pack.mcmeta file automatically based on the result you need the following settings:

```json
            {
                "regex": "\"pack_format\": \\d+",
                "with": "\"pack_format\": ${MINECRAFT_VERSION}",
                "mapping": {
                    "MINECRAFT_VERSION": {
                        "11700": "7",
                        "11800": "8",
                        "11801": "8",
                        "11802": "8"
                    }
                }
            }
```

### Regex

This is an ECMAScript flavour regex string. Since it has to be encoded as a string, every backslash must be escaped, so `\d` becomes `\\d`. 

Groups are supported, more on that in "Format strings".

To test a regex, use https://regex101.com/ and switch to the ecmascript flavour.

### "with" format string

When the editor finds a match for the regex, it will replace it with the provided format string (`"with"`), but first it formats it. There are two different supported options:

$0, $1, $2 and so on get replaced with the different regex group results, $0 being the full result and $1, $2, $3 being the subgroups. For example running `(.)(\d)` on `A1, B2, C3, D4` will have the following results:

 1.  $0 = A1, $1 = A, $2 = 1
 2.  $0 = B2, $1 = B, $2 = 2
 3.  $0 = C3, $1 = C, $2 = 3
 4.  $0 = D4, $1 = D, $2 = 4

The other option is `${<setting name>}` (this only let's you enter a single setting name unlike in bash and javascript where the syntax originates from). If you don't provide a mapping table, this will get replaced with raw value of the provided setting. For example in the `pack_version` example with 1.18.2 selected it would become
```json
"pack_format": 11802
```

### The mapping table

This is an optional setting, it affects the `${}` format string option. When the editor tries to replace these, it first checks if a mapping option is present for the setting name and then it checks if the current value is inside this table or not. If either of these checks fail, the value will be replaced with the raw value of the string, otherwise it uses the provided alternative. For example if we provide the following mapping table:

```json
"mapping": {
    "MINECRAFT_VERSION": {
        "11700": "7",
        "11800": "8"
    }
}
```

then if we select 1.17 as the current version, then the reference will be replaced with `7`, but if we select 1.18.1 or 1.18.2, since those don't exist in the table they will be replaced with `11801` and `11802` respectively.

## Tags

To help differentiate your shaders from the rest, you can add tags to them. This will help properly filter them when searching for a specific category. 

Tags are implemented as an array of strings, each entry should be one of the names from the "tags.json" file. Make sure the description of the tag applies to your shader before using one. When using an external reference (settingsLink), the tags have to be on the reference object (the one with the settingsLink property), this is for the future when we won't be able to load every shader and can only do so on a search.

Example:
```json
	"tags": ["minimal", "1.18.2", "colorful"]
```

If you don't like the selection of the tags, the descriptions or just feel like something is missing, you can add your own, follow the instructions under "Opening a PR" for this.

## File filters

If you need to remove a whole file based on some setting, you can use file filters. These use the following format:

```json
{
    "file": "<Path to the file>",
    "condition": "<The condition of the file staying>"
}
```

Condition is a booleann expression. The following operators are supported: `(, ), !, ==, !=, <, <=, >, >=, ||, &&`. On top of this you can use any setting you declared beforehand. 

For example if you have a version selector and you want to use a file only if the version is 1.18.2, then you can do the following:

```json
{
    "file": "myFile.glsl",
    "condition": "MINECRAFT_VERSION == 11802"
}
```

## Opening a PR

Fork the this repository first and edit the shaders.json file. Make sure to test it on the site (by enabling developer mode) first. If it doesn't work as intended, press 
F12 to bring up the developer console to see if it has any insights.

After testing, push the changes and open a PR comparing your branch with this one.

### When adding a new shader

The PR description should link to the shader's page.

If you aren't linking to github, you will need to somehow prove you created the shader yourself or you added the options with the permission from the original creator.

There should only be a single commit saying `Adds the shader <shader name>` (Do as I say, not as I do). If you need to fix this, use `git squash`.

### When editing a shader added previously

The PR description should contain a short summary of what it adds.

There should only be a single commit saying `Edits the shader <shader name>`.
