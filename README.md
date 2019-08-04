
# create-desktop-shortcuts

[![Build Status](https://travis-ci.org/nwutils/create-desktop-shortcuts.svg?branch=master)](https://travis-ci.org/nwutils/create-desktop-shortcuts)


## Small, lightweight, dependency free, cross-platform!

An easy, cross-platform, API to create desktop shortcuts with Node.

This library is completely **synchronous**.


## Installation

```
npm install --save create-desktop-shortcuts
```


### Examples

**Simple example:**

```js
const createDesktopShortcut = require('create-desktop-shortcuts');

const shortCutsCreated = createDesktopShortcut({
  windows: { filePath: 'C:\\path\\to\\executable.exe' },
  linux:   { filePath: '/home/path/to/executable'     },
  osx:     { filePath: '/home/path/to/executable'     }
});

// returns true if everything worked correctly, or false if it could not create the icon or set its permissions (Linux)
console.log(shortCutsCreated);
```

**Advanced Example:**

Each OS handles the conscept of a shortcut icon slightly differently. So they each have a slightly different API, but I tried to keep them similar when they overlap.

```js
const createDesktopShortcut = require('create-desktop-shortcuts');

const shortCutsCreated = createDesktopShortcut({
  onlyCurrentOS: true,
  verbose: true,
  /**
   * Your own custom logging function called with helpful warning/error messages from the internal validators
   * @param  {string} message The human readable warning/error message
   * @param  {object} error   Sometimes an error or options object is passed
   */
  customLogger: function (message, error) {
    console.log(message, error);
  },
  windows: {
    filePath: 'C:\\path\\to\\executable.exe',
    outputPath: 'C:\\some\\folder',
    name: 'My App Name',
    description: 'My App description',
    icon: 'C:\\path\\to\\file.ico',
    arguments: '--my-argument -f \'other stuff\'',
    windowMode: 'normal',
    hotkey: 'ALT+CTRL+F'
  },
  linux: {
    filePath: '/home/path/to/executable',
    outputPath: '/home/some/folder',
    name: 'My App Name',
    description: 'My comment',
    icon: '/home/path/to/file.png',
    type: 'Application',
    terminal: false,
    chmod: true
  },
  osx: {
    filePath: '/Applications/My App.app',
    outputPath: '/home/some/folder',
    name: 'My App Name',
    overwrite: false
  }
});

// returns true if everything worked correctly, or false if it could not create the icon or set its permissions
console.log(shortCutsCreated);
```


## Documentation


### Global Settings

Key             | Type     | Allowed         | Default | Description
:--             | :--      | :--             | :--     | :--
`onlyCurrentOS` | Boolean  | `true`, `false` | `true`  | If true and you pass in objects for multiple OS's, this will only create a shortcut for the OS it was ran on.
`verbose`       | Boolean  | `true`, `false` | `true`  | If true, consoles out helpful warnings and errors.
`customLogger`  | Function | Any function    | None    | You can pass in your own custom function to log errors/warnings to. When called the function will have a message string for the first argument and sometimes an error object. This is useful in NW.js to see the messages logged to the regular Chromium devtools instead of the background page DevTools. But this can also be useful in other scenarios, like adding in custom wrappers or colors in a commandline/terminal. This function may be called multiple times before all synchronous tasks complete.


### Windows Settings

Key          | Type   | Allowed                                  | Default                      | Description
:--          | :--    | :--                                      | :--                          | :--
`filePath`   | String | Any valid path or URL                    | **This is a required field** | This is the target the shortcut points to.
`outputPath` | String | Any valid path to a folder               | `'%USERPROFILE%\\Desktop'`   | Path where the shortcut will be placed.
`name`       | String | Any file system safe string              | Uses name from filePath      | The name of the shortcut file.
`comment`    | String | Any string                               | Not used if not supplied     | Metadata file "comment" property. Description of what the shortcut would open.
`icon`       | String | Valid path to ICO file                   | Uses OS default icon         | The image shown on the shortcut icon. Must be valid ICO file.
`arguments`  | String | Any string                               | None                         | Additional arguments passed in to the end of your target `filePath`
`windowMode` | String | `'normal'`, `'maximized'`, `'minimized'` | `'normal'`                   | How the window should be displayed by default
`hotkey`     | String | Any string                               | None                         | A global hotkey to associate to opening this shortcut, like `'CTRL+ALT+F'`


### Linux Settings

Key          | Type    | Allowed                                  | Default                      | Description
:--          | :--     | :--                                      | :--                          | :--
`filePath`   | String  | Any valid path or URL                    | **This is a required field** | This is the target the shortcut points to. Must be a valid/existing folder if `type: 'Directory'`, or file if `type: 'Application'`.
`outputPath` | String  | Any valid path to a folder               | Current user's desktop       | Path where the shortcut will be placed.
`name`       | String  | Any file system safe string              | Uses name from filePath      | The name of the shortcut file.
`comment`    | String  | Any string                               | Not used if not supplied     | Metadata file "comment" property. Description of what the shortcut would open.
`icon`       | String  | Valid path to PNG or ICNS file           | Uses OS default icon         | The image shown on the shortcut icon. Preferably a 256x256 PNG.
`type`       | String  | `'Application'`, `'Link'`, `'Directory'` | `'Application'`              | Type of shortcut. Must be an exact match to this string.
`terminal`   | Boolean | `true`, `false`                          | `false`                      | If true, will run in a terminal.
`chmod`      | Boolean | `true`, `false`                          | `true`                       | If true, will apply a `chmod +x` (755) to the shortcut after creation to allow execution permission.


### OSX Settings

OSX will automatically inherit the icon of the target you point to. It doesn't care if you point to a folder, file, or application.

If overwrite is set to false and a matching file already exists, a console log will occur to inform you of this, however `create-desktop-shortcuts` will still report successful. This console log can be hidden by setting verbose to false.

Key          | Type    | Allowed                     | Default                      | Description
:--          | :--     | :--                         | :--                          | :--
`filePath`   | String  | Any valid path or URL       | **This is a required field** | This is the target the shortcut points to.
`outputPath` | String  | Any valid path to a folder  | Current user's desktop       | Path where the shortcut will be placed.
`name`       | String  | Any file system safe string | Uses name from filePath      | The name of the shortcut file.
`overwrite`  | Boolean | `true`, `false`             | false                        | If true, will replace any existing file in the `outputPath` with matching `name`


* * *


## Credits

Author: The Jared Wilcurt

Parts of the `windows.vbs` were copied/modified based on:

 * https://www.vbsedit.com/html/a239a3ac-e51c-4e70-859e-d2d8c2eb3135.asp
 * https://forums.techguy.org/threads/solved-vbscript-create-a-shortcut-within-a-folder.886401/


* * *


## How can you help improve this repo?

* Write unit tests. Preferably in Jest.
* Offer an async and sync mode, instead of just sync.
  * Note: Make sure it can still run in older versions of NW.js
