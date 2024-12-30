# NextLanguage Built-in: Secure Package
A package system, that allows automatic passthrough for NextLanguage's Plugin System in v2.0

# How it works
It allows NextLanguage Developers to create plugins for NextLanguage in v2.0 and above.

**Plugin Registrations**
This is an example of a code on how to register a plugin. In the ``BUILD_CONFIG`` file there should be a new registration section that allows users to register plugins.
The Secure Plugin which is defined by ``Secure @require(path.join(__dirname, '../../../../package/bulit-in/Secure/package.js'));`` **SHOULD NOT BE REMOVED** as of v2.0-dev.

The ``@require`` syntax is only avaliable for NextLanguage's Built-in packages. And any other packages not bulit-in should be using the ``@path: path/to/plugin`` syntax which is avaliable for all other plugins.
The main file which signs the plugin should be listed in ``@path``.
this is NextLanguage's built-in package that allows it to Check Plugin Signatures. Which is a way to register a signed package below:
```yaml
# Plugins registrations
PLUGINS: Secure @require(path.join(__dirname, '../../../../package/bulit-in/Secure/package.js'));
PLUGINS: Test @path: ../../../app.js
```

**Signning Plugins**
As of right now, in v2.0-dev branch. This is actually just the Application to register an signed package.
What you're seeing below is an example testing code used for NextLanguage's v2.0 Plugin System.
This was a custom plugin which won't be included in the v2.0 patch, but we'll link it [here](https://github.com/nxoscloud/Custom).

The functions and unknown modules in the code below, isn't required to be imported by the Plugin Developer, as NextLanguage will automatically pass through the required modules that the Signing file should ever need.
All the pass-through modules will be listed in the Pass-through section.

The ``package.create(type, path, name)`` is the correct syntax for creating a package.
The ``path`` should be your Plugin's main code-file, not the file that is registered in ``BUILD_CONFIG``.
The function that is at the start of the file can be named whatever, but is recommended to stay as a function. To split the other logic from the signning logic.
And the function should also be called on the same file that is signning the plugin.

```javascript
function CustomService() {
    // path.join(__dirname, '../../../../Custom/default.js') just returns (root) which is outside of the
    // NextLanguage-Source folder. And (Custom/default.js) is the main file for the package.
    const Custom = package.create('package', path.join(__dirname, '../../../../Custom/default.js'), 'Custom');
    Custom.config('package', 'Custom', 'enabled', true);
    const enabled = Custom.strings.full.package.dev.self[1];

    // Additional information toggle for non-production ready
    // Updates for Custom.
    const production = false;

    Custom.config('package', 'Custom', 'author', 'Cassitydev');
    Custom.config('package', 'Custom', 'version', '0.1.0');
    Custom.config('package', 'Custom', 'repo', 'No Repository published for Custom yet.');
    Custom.config('package', 'Custom', 'description', '(Custom package for NextLanguage) A package which Customly exposes NextLanguages Modules to external files. I.E. Plugins, packages, postload and preload files.');

    if (enabled === true) {
        if (production === false) {
            addOutput('Custom package is enabled.');
        }
    } else {
        if (production === false) {
            addOutput('Custom package is disabled along an unknown error.');
        } else {
            addOutput('Custom package is disabled.');
        }
    }

    return Custom
}

const Strings = CustomService();

console.log(Strings.strings.full.package);
/**
* Output for (Strings.strings.full.package)
{
  user: {
    name: 'Custom',
    author: 'Cassitydev',
    repo: 'No Repository published for Custom yet.',
    description: '(Custom package for NextLanguage) A package which Customly exposes NextLanguages Modules to external files. I.E. Plugins, packages, postload and preload files.',
    version: '0.1.0',
    license: null,
    app: 'C:\\Users\\jedik\\Documents\\GitHub\\Project Next\\Products\\NextLanguage\\Custom\\default.js'
  },
  dev: { self: [ 'Custom', true ], global: null }
}
*/
```

**Pass-Through Modules**
Signning a plugin, while VSCode may list it as an unknown module, NextLanguage when ran, will automatically pass those modules onto the main file.
Here is a list of all the modules that will be passed from NextLanguage to the plugins:
```javascript
// External Node Modules
const path = require('path');
const fetch = require('fetch);

// Logging systems.
const addOutput = require('@NextLanguage/v2.0/modules/functions/addOutput.js');
const debugOutput = require('@NextLanguage/v2.0/modules/functions/debugOutput.js');

// Classes
const Local = require('@NextLanguage/v2.0/modules/class/temp/Local.js');
const Package = require('@NextLanguage/v2.0/modules/class/Packages.js');
    
const package = new Package;
const local = new Local;

// Session data
const { getVariables, setVariable } = require('@NextLanguage/v2.0/modules/functions/temp/Variables.js');
const { getFunctions, setFunction } = require('@NextLanguage/v2.0/modules/functions/temp/Functions.js');

// Preload Logic
const createPreload = require("@NextLanguage/v2.0/patches/v1.9/modules/nodejs/pre/createPreload.js");
const runPreload = require("@NextLanguage/v2.0/patches/v1.9/modules/nodejs/pre/runPreload.js");

// Postload Logic
const createPostload = require("@NextLanguage/v2.0/patches/v1.9/modules/nodejs/post/createPostload.js");
const runPostload = require("@NextLanguage/v2.0/patches/v1.9/modules/nodejs/post/runPostload.js");
```
