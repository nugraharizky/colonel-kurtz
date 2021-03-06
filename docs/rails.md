# Rails Integration

1. [Overview](#overview)
2. [Installing Node](#installing-node)
3. [Setting up Browserify](#setting-up-browserify)
4. [Parsing JSX](#parsing-jsx)
5. [Adding Colonel Kurtz as a dependency](#adding-colonel-kurtz-as-a-dependency)
6. [Including Colonel Kurtz styles](#including-colonel-kurtz-styles)

## Overview

Rails integration with Colonel Kurtz presents a few
challenges. Colonel Kurtz is published on [`npm`](http://npmjs.com/)
and new block types are described as React components. Rails needs to
be taught how to integrate with [`npm`](http://npmjs.com/) and how to
parse [JSX for React](https://facebook.github.io/react/docs/jsx-in-depth.html).

## Installing Node

Most of the tools described in this document rely on
[NodeJS](https://nodejs.org). Node will not be run as a web
server. The community has built a number of fantastic tools using Node
that we will take advantage of.

### Managing Node with NVM

Our recommended way to install Node is to do it through
[Node Version Manager](https://github.com/creationix/nvm#usage). Additionally,
you can create a `.nvmrc` file to lock down what version of Node is
used for a project (also described in the readme of NVM).

### Add node_modules to .gitignore

`npm` will download project dependencies to this folder. You may also
want to add `node_modules` to your `.gitignore`.

## Setting up Browserify

[browserify](http://browserify.org/) is a JavaScript build tool for
compiling and transforming JavaScript. It allows developers to include
dependencies using the CommonJS module system. We have used it on
several Rails projects because of the relatively easy integration
using
[`browserify-rails`](https://github.com/browserify-rails/browserify-rails).

Browserify is going to do two things for us:

1. Make it easier to include Colonel Kurtz
2. Transform React's JSX syntax into vanilla JavaScript

After following the installation process described in the readme of
[`browserify-rails`](https://github.com/browserify-rails/browserify-rails),
we need to create a `package.json` file to describe dependencies that
should be pulled down from [`npm`](https://npmjs.com).

At the root of your project, you can create a new `package.json` with:

```
npm init
```

This will boot a wizard that will ask you for information about the
project. It is hard to mess this up, and if there does happen to be a
problem, `npm` will warn you of the issue as you run some of the
commands described later in this document.

As a last step, if you have not installed `browserify` or
`browserify-incremental`, do that now:

```shell
npm install --save browserify
npm install --save browserify-incremental
```

We have not mentioned `browserify-incremental` up to this point. This
package is used by `browserify-rails` for incremental
builds. Because of some characteristics of the Rails Asset
Pipeline, compiling JavaScript will otherwise be slower than it should
be.

Returning to the commands above. `npm install` pull down each module from
`npm`. `--save` tells npm to store a record of the dependency in
`package.json`. For the future, when a developer executes `npm
install`, it will pull down these same, versioned, dependencies.

## Parsing JSX

Colonel Kurtz is powered by
[React](http://facebook.github.io/react/). This means that extending
Colonel Kurtz often requires the processing of
[JSX](https://facebook.github.io/jsx/). Fortunately, there is a wide
array of options for doing this. Our favorite method is to use the
[BabelJS](https://babeljs.io/) JavaScript transformer. Not only does
it parse JSX, but it also provides access to the latest features of
the language.

Babel can be integrated with Browserify using their
[babelify](https://github.com/babel/babelify) transform for
Browserify. First, let's pull down the dependency:

```shell
npm install --save babelify
```

Browserify can be configured by adding a `"browserify"` entry in
package.json. To tell Browserify to use Babel, add the following
entry:

```json
{
  "browserify": {
    "transform": [
      ["babelify", { "extensions": [".js", ".jsx"] }]
    ]
  }
}
```

More information on configuring babelify can be found at [its repo](https://github.com/babel/babelify).

## Adding Colonel Kurtz as a dependency

Similarly to `browserify` in the previous step:

```shell
npm install --save colonel-kurtz
```

Thats it! Additional information about configuring Colonel Kurtz is
documented in `./colonel.md`.

## Including Colonel Kurtz styles

The `npm` package for Colonel Kurtz also includes a Sass stylesheet. As
an optional step for those using Sass, the `node_modules` folder
(where NPM places dependencies) can be added to the known Rails asset
paths in order to expose these styles.

In `config/application.rb` add the following entry in your config block:

```ruby
  config.assets.paths << "#{ config.root }/node_modules"
```

Finally, in your stylesheet:

```
@import "colonel-kurtz/style/colonel";
```
