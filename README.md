# wp-install
## What is wp-install?
It's like `npm install` for WordPress.

WordPress encourages the use of long-lived servers:
* Plugins are typically downloaded directly to a running server, rather than being stored in source control
* WordPress (and it's plugins) update themselves by downloading new code directly to the server
* To deploy a custom theme, a developer typically FTP's files to a running server

This goes against the modern idea of [Immutable Infrastructure](https://www.digitalocean.com/community/tutorials/what-is-immutable-infrastructure).

This package is here to help make immutable WordPress deployment easier!  
This package:
* Downloads the latest version of WordPress and puts it in a `./dist` folder.
* Downloads the latest version of the WordPress plugins that you configure in `.wp-install.yml`
* Copies plugins that you have saved locally
* Copies your custom theme that you have saved locally

This ends up with a working WordPress installation in `./dist/wordpress` that you can serve or deploy.

## Installation
```
npm install wp-install -g
```
This will install wp-install globally so it can be run from the command line.

## Usage
* Create a file `.wp-install.yml`  
The structure should be like this:
```yaml
---
plugins-to-download:
  plugins:
  - name: custom-post-type-ui
  - name: custom-post-type-permalinks

plugins-to-copy:
  directory: plugins-to-copy
  plugins:
  - name: advanced-custom-fields-pro
    type: directory
    directory: advanced-custom-fields-pro
  - name: advanced-custom-fields-pro-2
    type: zip
    filename: advanced-custom-fields-pro-2.zip

custom-theme:
  directory: my-custom-theme
```

* In the same folder as `.wp-install.yml` simply run:
```
wp-install
```

## What does it do? / what are the options?
* Deletes and re-creates a `./dist` folder.

* Downloads WordPress:
  * It checks the [WordPress.org API](https://codex.wordpress.org/WordPress.org_API) (specifically the [Version Checker](https://api.wordpress.org/core/version-check/1.7/)) to find which version of WordPress to download.  
  Note: it chooses the first `offer` with `response:"upgrade"`.
  * Downloads the file to `./dist/temp/wordpress.zip`
  * Unzips WordPress into the `./dist/wordpress` folder

* Plugins (part 1) - download plugins
  * Looks in `.wp-install.yml` to see if you want to download any plugins from WordPress.org  
  This is configured in the `plugins-to-download` section.
  * Checks the [WordPress.org API](https://codex.wordpress.org/WordPress.org_API) to find which version of the plugin to download.  
  For instance, for the `custom-post-type-ui` plugin, uses [this page on the API](https://api.wordpress.org/plugins/info/1.0/custom-post-type-ui.json)
  * Downloads the plugin to `./dist/temp/plugin--[plugin-name].zip`
  * Unzips the plugin to `./dist/wordpress/wp-content/plugins/`

* Plugins (part 2) - locally saved plugins
  * Looks in `.wp-install.yml` to see if you have any locally saved plugins to copy
  * Plugins can be folders or zip files

* Copies your custom theme
  * Looks in `.wp-install.yml` to see if you have a custom theme to copy
  * The theme is copied to `./dist/wordpress/wp-content/themes`
