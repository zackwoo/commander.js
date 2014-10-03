Contributing to Commander.js
============================

Contributing Guidelines
-----------------------

TODO

Planned API Changes
-------------------

### Implement a plugin system.

This will provide a way for users to add new features to commander without needing to change core. The default `--help` and `--version` will be reimplemented as plugins that are loaded by default.

The expected API for this is:

```javascript
var program = require('commander');
program.plugins(['custom-help','custom-version'],
  {defaultHelp: false, defaultVersion: false});
```

Plugins will always expect only two arguments, the array of plugin names and a setting object which is mostly used to turn off built-in plugins.

Plugins can declare `isHelp` and `isVersion` values attached to their exported object. When loading a plugin commander will check for these values. If found they'll automatically use that plugin for help or version instead of the defaults. Once found the plugin function will keep track of this and if another help or version plugin is found it will exit the app with and error and message that you cannot use more than one custom help or version plugin. For plugins designed to replace built-ins the option name might be set automatically too, both for convenience and to stop cases where a plugin declares it is custom help but the option is `--version` or something else.

For the actual finding and loading of the plugins it will simply attempt to require `plugin-name` or `commander-plugin-name`. Each plugin will be passed the commander object that calls `.plugins()` and will return it. Plugins are loaded from left to right.

The problem with this approach is it doesn't allow for plugins outside the applications directory or `node_modules` folder. While it's probably a bad idea to have plugins stored somewhere else and I dislike the idea of a `/plugins` folder since I believe everything should be in `node_modules` we could experiment with attempting to detect if the string is a directory path. This would have to work on Windows and Unix. I recommend ignoring it for now.

### Moving the default plugins and the parser into their own files

Having everything in one file is convenient but it makes the code harder to read and makes it easier to tightly couple different components. The parser is one of the best parts of commander.js but I do think it should also be loaded as a plugin since it may not be suitable for some use cases. One example is a CLI app I wrote some time ago for quickly searching information on media from the cli. This was designed to work even with part of the title. The API was `media-app search gone with wind`. For most apps 'gone with wind' should be wrapped in quotes. However in this case remembering to wrap the search term in qoutes every time was a hassle.

What's also cool about doing this is for changing parser behavior the plugin writer can require the default parser and only replace the part they want to change instead of needing to replicate all the code.

Other ways to help
------------------

TODO
