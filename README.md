# Laravel 5 Vue Cli integration

This package includes a preset that will modify a fresh Laravel 5.7 installation.
It allows you to use Vue-cli along Laravel.
It will also let you include them in your blade views, any assets generated by vue-cli-service.

## Installation

With Composer

`$ composer require vhanla/laravel-vue-cli`

Next, add service provider into your `config/app.php` file in `providers` section:

+ `Vhanla\VueCli\VuecliServiceProvider::class,`

## Configuration

To change package configuration you need to publish configuration files:

`$ php artisan vendor:publish`

This will publish `vuecli.php` file inside your `config` directory.

## Usage

+ First create a `vue` project **inside `resources` directory** using `vue cli`,
it is recommended not to use git e.g. `vue create --no-git <app-name>`
+ Once finished creation of `vue` project using Vue-Cli, execute the `vuecli` preset:
`php artisan preset vuecli` and follow the interactive setup. The preset command will detect `vue` project inside `resources` directory.
Then it will ask you to pick it.

+ `Blade` views has two methods to invoke assets.
  + Development method: This method is better when we are developing.
    `<script src="{{ vuecli('app.js') }}"></script>`

    It allows to always read generated `assets.json` file contents even from cached views.
    Recommended only for local development mode, specially when using `hashed` filenames.

  + Cacheable method: This method is better for production mode.
    `<script src="@vuecli(app.js)"></script>`

    Once `blade` view is cached, the returned assets path will always be the same, until
    the `blade` template file is modified or `php artisan view:clear` is invoked.

  In other words, the first one will always read `public/assets.json` file in order to find out the
  specific `asset` file, while the latter will only read once before caching.
  Even clearer, with the first one, cached `blade` views will include `<?php echo vuecli('app.js')...`
  while the latter will not, instead it will write the path returned.

## Features

+ Includes a `preset` command `vuecli` that sets up a fresh Laravel project for `vue cli`.
+ You can use `vue ui`. However, running `tasks` from there seems to ignore `vue.config.js` file. It seems to be a `vue cli` bug.
+ Includes a `blade` directive `@vuecli(assetname, boolean)` where boolean value tells whether to ignore
if asset is not found.
+ Includes a `helper` function `vuecli('assetname', boolean)` where boolean value tells whether to ignore
not found asset.
+ Includes a `blade` directive `@livereload(host,port)` that adds it only if local (development) is detected.

## Requirements

+ Laravel 5.7 - maybe it would work on previous ones, I haven't tested.
+ `vue cli` 3+
+ [`webpack-manifest-plugin`](https://github.com/danethurber/webpack-manifest-plugin) but `preset` will add it, so just update it if newer version.
+ [`webpack-livereload-plugin`](https://github.com/statianzo/webpack-livereload-plugin) but `preset` will add it too, so just update it if newer version.

## Limitations

+ `vue cli` projects has some limitations, specially a static and even mandatory static assets directory `public`
 whose contents copies along with generated built assets into a default `dist` folder,
which as you can see, it will force us to somehow modify that folder tree structure in order
to use it along Laravel's. But don't worry, `vue.config.js` file is configured to achieve that,
by allowing `public` directory as `output` directory, and settings the static assets `resources/public`
which also will contain the `index.php` file that `yarn build` or `npm run build` will move to `/public`
directory.

+ Vue development mode should be used only with `yarn serve` or `npm run serve`, that's due to
the previous issue, i.e. files and changes generated for `vue` in development mode won't be easily
accessible from PHP's webserver side.

+ HMR (Hot Module Replacement) is not included via Laravel's web server, only via `vue cli`'s.
+ Some plugins for `vue cli` requires to make sure anything that writes to `public` directory be manually
move to `resources/public` directory.

## Known issues

+ If blank page is presented, make sure to use a virtual host a.k.a. local test domain, or update `baseUrl` since `vue` app will find maybe `http://localhost/mylaravelapp/public/` different `baseUrl` which by default expects to be `/` instead of `mylaravelapp/public`.
+ If exception error about `my asset name.js`, make sure it exists in `/public/assets.json`.
+ If error on `yarn build` or `npm run build` shows `- Propery "rules" is wrong type (expected object but got '[{}]')` remove it from `package.json`.

## TODO

- [ ] Improve handling of `vue cli` development, as of now, `production` mode integrates better.
- [ ] Improve handling of `vue cli` plugins, since some still uses and overrides modified `public` assets directory.
- [ ] Improve default spa.blade.php template