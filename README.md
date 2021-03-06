# grunt-sprite-packer

> Sprite packer converts multiple png files into single sprite file using efficient packer algorithm. Metadata (names, dimensions and positions of individual sprites) are written into separate template-based text file.

## Getting Started
This plugin requires Grunt `~0.4.1` and ImageMagick library.

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-sprite-packer --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-sprite-packer');
```

ImageMagick library have to be installed in your system. See [www.imagemagick.org](http://www.imagemagick.org) for installation instructions.

## The "spritepacker" task

### Overview

This task converts multiple images into single image using efficient packing algorithm. It outputs two files: resulting sprite image and a text file with information about positions and dimensions of individual sprites. This text file has no fixed format or structure, instead it is generated using a handlebars template, so that you have full control of its format. It can be plain CSS with classes or some declarations for additional processing (LESS, SASS, Stylus or JSON).

In your project's Gruntfile, add a section named `spritepacker` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
  spritepacker: {
    default_options: {
      options: {
        // Path to the template for generating metafile:
        template: 'css/sprites.css.tpl',

        // Destination metafile:
        destCss: 'css/sprites.css',

        // Base URL for sprite image, used in template
        baseUrl: '../img/'
      },
      files: {
        'img/sprites.png': ['img/sprites/*.png']
      }
    }
  }
})
```

### Options

#### options.template
Type: `String`

A path to the template for generating metafile. Template is processed through Handlebars with the following variables:

* `sprites` - an array of object with metainfo for each sprite
* `baseUrl` - base URL from task options
* `fileName` - file name of generated sprite file (i.e. task destination)
* `width` - width of generated sprite file
* `height` - height of generated sprite file
* `timestamp` - timestamp (can be used in template as image url parameter to prevent caching issues)

Each item in sprites array contains the following properties:

* `name` - name of the sprite (= original file name without extension)
* `x` - x position of the sprite in generated sprite file
* `y` - y position of the sprite in generated sprite file
* `width` - width of the sprite
* `height` - height of the sprite

Example of template file for generating simple CSS:

```
{{#sprites}}.{{name}} = { background: url('{{../baseUrl}}{{../fileName}}') no-repeat -{{x}}px -{{y}}px; width: {{width}}px; height: {{height}}px; }
{{/sprites}}
```

Example of template file for generating Stylus declarations to be processed later by some Stylus function:

```
{{#sprites}}{{name}} = ('{{../baseUrl}}{{../fileName}}' -{{x}}px -{{y}}px {{width}}px {{height}}px {{../name}})
{{/sprites}}
```

#### options.template
Type: `String`

A path to the template file.

#### options.destCss
Type: `String`

A path to the metafile that will be generated from the template.

#### options.baseUrl
Type: `String`

URL path to the generated sprite file.

#### options.padding
Type: `Number`
Default: `0`

White space that will be added as a padding around every sprite (in px). It is useful to prevent bleeding pixels from one sprite to another when scaled / interpolated in the browser.

#### options.format
Type: `String`
Default: `'png32'`

Output file format. Any image format supported by ImageMagick should work (tested `'png32'` and `'jpg'`).

#### options.background
Type: `String`
Default: 'none'

Background color of output image. `'none'` = transparent, `'#ffffff'` = white, `'#ff0000'` = red etc.

#### options.quality
Type: `String`
Default: `'100%'`

Compression quality of output file. Has no effect for file formats other than jpg.

#### options.evenPixels
Type: `Boolean`
Default: `false`

When set to `true`, dimensions of sprites will be aligned to even pixels (useful when scaled down by factor of 2 in CSS for mobile/retina/nonretina optimization). Note that padding must be set to (multiple of) 2 as well.

## Credits

This plugin uses binary tree bin packer algorithm by Jake Gordon (<https://github.com/jakesgordon/bin-packing/>).