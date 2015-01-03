# Variable VVV - The Best VVV Site Wizard

	 ██    ██ ██    ██
	░██   ░██░██   ░██     Variable VVV 1.4.8
	░░██ ░██ ░░██ ░██
	 ░░████   ░░████       The easiest way to set up
	  ░░██     ░░██        WordPress sites with VVV!
	   ░░       ░░


`vv` makes it extremely easy to create a new WordPress site using [Varying Vagrant Vagrants](https://github.com/Varying-Vagrant-Vagrants/VVV). `vv` supports site creation with many different options; site blueprints to set up all your plugins, themes, and more; deployments; and lots more features.

## Installation

If you have [Homebrew](http://brew.sh/) installed, you run the following in your terminal application:

	$ brew install bradp/vv/vv

Otherwise you'll want to clone and edit your `$PATH` to include the vv core file.

## Updating

vv is currently under development, and you'll probably want the latest and greatest version at all time.

You can run `vv --update` to update to the latest version. This will update via Homebrew if you've installed it that way, otherwise vv will boostrap an update on where ever you've installed it.

vv will automatically check for updates and update itself once a week. You can disble this by adding `"auto_update_disable": false` to the JSON config in `~/.vv-config`.

## Usage

Once installed, you can run `vv` anywhere you'd like. If vv can't automatically find your VVV installation, you will be prompted for the path. It will also save this into a configuration file in `~/.vv-config`, so you won't be prompted again.

At any time, you can run `vv` or `vv --help` to see a list of all possible options and flags.

vv will prompt you for a value for any required flags that were not specified.

The main commands are `list`, `create`, `delete`. These will list your sites, create a site, and delete a site. These each have a few aliases, so for example, if you run `vv show`, vv will know you meant `vv list`.

To start creating a site, simply do `vv create` ( you can also do `vv --create`, or simply `vv -c`). You will then be prompted for all required options.

All options and flags are [listed below](#options).

## Site Creation

`vv create`

Creating a site does the following:

* Halts Vagrant (if running)
* Creates a web root for the site in the `www` folder containing three files: `vvv-init.sh`, `wp-cli.yml`, and `vvv-hosts`
	* `vvv-init.sh` tells Vagrant to create a database if one does not exist and install the latest version of WordPress (via WP-CLI) the next time Vagrant is provisioned
	* `wp-cli.yml` tells WP-CLI that WordPress is in the htdocs folder
	* `vvv-hosts` contains the hosts entry to give your site a nice custom domain (the domain is set in the wizard)
* Creates a file in the `nginx-config` folder to handle server settings for your site
* Restarts Vagrant with `vagrant up --provision`

Provisioning Vagrant takes a couple of minutes, but this is a crucial step as it downloads WordPress into your site's htdocs directory and runs the installation. If you want to skip provisioning and install WordPress manually, you can run the new site's `vvv-init.sh` file directly in the Vagrant shell.

## Site Deletion

`vv delete`

Deleting a site does the following:

* Halts Vagrant (if running)
* Deletes the site's web root (which deletes the `vvv-init.sh`, `wp-cli.yml`, and `vvv-hosts` files as well)
* Deletes the file in the `nginx-config` folder pertaining to the site

Note that it does not delete the site's database.

## Deployments

`vv deployment-create`, `vv deployment-remove`, `vv deployment-config`

vv supports setting up deployments that work with [Vagrant Push](https://docs.vagrantup.com/v2/push/index.html). You'll need to be on version 1.7.0 or later of Vagrant. Simply run `vv --deployment-create` and walk through the wizard.

To deploy a site, you can do `vv vagrant push <sitename><deployment_name>`.

When removing a deployment, your current Vagrantfile will be backed up as Vagrantfile-backup.


## Advanced Usage

Anything that vv prompts you for, you can pass in as an argument. Most of this is realized in the site creation. In fact, there are a few arguments you can pass in that aren't prompted. This gives you total control over creating a new site.

To create a new site named 'mysite' that has the domain 'mysite.dev' and is a multisite with subdomains, with `WP_Debug` turned on would be:

`vv create -d mysite.dev -n mysite -m subdomains -x`

Or, the more readable version with all expanded flags.

`vv create --domain mysite.dev --name mysite --multisite subdomains --debug`

## Blueprints

Blueprints allow you to set up different plugins, themes, mu-plugins, options, or constants that will be installed to a new site you create. First, run `vv --blueprint-init` to have vv create a `vv-blueprints.json` file in your VVV directory. You can edit this file to create and set up different blueprints.

The blueprint should look like this:
```json
{
  "sample": {
    "themes": [
      "automattic/_s"
    ],
    "mu_plugins": [
      "https://github.com/WebDevStudios/WDS-Required-Plugins.git"
    ],
    "plugins": [
      "https://github.com/clef/wordpress/archive/master.zip",
      "cmb2"
    ],
    "options": [
      "active_theme::_s"
    ],
    "defines": [
      "WP_CACHE::false"
    ]
  }
}
```

For themes, plugins, and mu-plugins, you can use:

* Github username/repo
* Full git url
* Url to zip file
* WordPress.org slug

For options and constants, please note the `::` as a seperator between the key and value.

You can create as many named blueprints in this file as you would like, all with as many different settings as you'd like.


## Vagrant Proxy

Because vv knows where you VVV installation is, you can run it from anywhere. vv will proxy any commands passed into `vv vagrant <command>` to your VVV location. So `vv vagrant halt` will halt your VVV vagrant, no matter where you run it.

## `vv` Options

|Option|Description|
|------|-----------|
|`--help`, `-h`| Show help and usage|
|`--version`|Show current vv version number.|
|`--about`|Show about screen.|
|`--update`|Updates vv to the latest stable version|
|`--path`,	`-p`|Path to VVV installation|
|`--force-path`, `-fp`|Override vv auto-VVV locating|

### Commands

|Command|Description|
|-------|-----------|
|`list`, `--list`,  `-l`|List all VVV sites|
|`create`, `--create`, `-c`|Create a new site|
|`remove`, `--remove`, `-r`|Remove a site|
|`deployment-create`, `--deployment-create`|Set up deployment for a site|
|`deployment-remove`, `--deployment-remove`|Remove deployment for a site|
|`deployment-config`, `--deployment-config`|Manually edit deployment configuration|
|`blueprint-init`, `--blueprint-init`|Initalize blueprint file|
|`vagrant`, `v`, `--vagrant`, `-v`|Pass vagrant command through to VVV.|

### Options for Site Creation

|Option|Description|
|------|-----------|
|`--name`, `-n`|Desired name for the site directory (e.g. mysite)|
|`--domain, -d`|Domain of new site|
|`--webroot`, `-wr`|Subdirectory used for web server root|
|`--blueprint`, `-b`|Name of blueprint to use|
|`--live-url`, `-u`|Live URL of site|
|`--files`, `-f`|Do not provision Vagrant, just create the site directory and files|
|`--images`, `-i`|Load images by proxy from the live site|
|`--wp-version`, `-wv`|Version of WordPress to install|
|`--debug`, `-x`|Turn on WP_DEBUG and WP_DEBUG_LOG|
|`--multisite`, `-m`|Install as a multisite. Can also pass in "subdomain" or "subdirectory"|
|`--sample-content`,`-sc`|Adds sample content to site.|
|`--username`|Admin username|
|`--password`|Admin password|
|`--email`|Admin email|
|`--git-repo`|Git repo to clone as wp-content|
|`--path`,	`-p`|Path to VVV installation|
|`--force-path`, `-fp`|Override vv auto-VVV locating|

### Options for Site Removal
|Option|Description|
|------|-----------|
|`--name`, `-n`|Desired name for the site directory (e.g. mysite)|
|`--path`,	`-p`|Path to VVV installation|
|`--force_path`, `-fp`|Override vv auto-VVV locating|

### Options for Deployment Setup
|Option|Description|
|------|-----------|
|`--name`, `-n`|Desired name for the site directory (e.g. mysite)|
|`--deployment-name`|Name of deployment (production, staging, other, etc)|
|`--host`|Host (if SFTP, define port as host:port) |
|`--username`|FTP Username |
|`--password`|FTP Password  |
|`--passive`|Use Passive transfer mode? (y/n)  |
|`--secure`|Use SFTP? (y/n) |
|`--destination`|Destination path ( You probably want / or ~/public_html ) |
|`--confirm-removal`|Used when removing a deployment to skip the confirmation prompt |


## `.vv-config`

The first time you run `vv`, it will attempt to locate your VVV installation location. If it can't find it, you will be prompted for it. This will be written to a .vv-config file in your home directory. (`~/.vv-config`) You can also edit this file if you need to change your VVV path.

You can also add `"auto_update_disable": false` to this file to disable auto-update functionality.

In the future, any additional settings and defaults will be stored here, along with VVV installation path.

## Questions?

Ping me on Twitter at [@bradparbs](http://twitter.com/bradparbs).

## Thanks

Forked and based off of [vvv-site-wizard from Alison Barrett](https://github.com/aliso/vvv-site-wizard).

## Change Log

#### 1.4.6 - *2014-12-15* ####
 Fix git repo setup
 Fix brew update procedure

#### 1.4.5 - *2014-12-15* ####
 Fixes deployment setup

#### 1.4.4 - *2014-12-15* ####
  Exclude wp-config.php when doing a deployment
  update readme to clarify git repo usage
  Fix order of output in vvv-init.sh
  Add note about auto-update disabling to vv-config section of readme
  Make headings consistent, & represent proper hierarchy, & update outdated info.
  Doc Tweaks. Make headings consistent, and represent proper hierarchy, and update outdated info.

#### 1.4.3 - *2014-12-14* ####
 Fix blueprint breaking site creation.

#### 1.4.2 - *2014-12-14* ####
 Fix extra info being output on all commands.

#### 1.4.1 - *2014-12-14* ####
 Fix issue with update procedure when installed with brew.
 Added flag to force update.

#### 1.4.0 - *2014-12-14* ####
 Adds blueprints for site creation.

#### 1.3.1 - *2014-12-13* ####
 Fixes bug in site creation.

#### 1.3.0 - *2014-12-13* ####
 Adds sample content import to new site.
 Fixes bugs in site database creation.

#### 1.2.0 - *2014-12-13* ####
 Adds auto-update functionality.

#### 1.1.0 - *2014-12-13* ####
 Add deployment configuration using Vagrant push.

#### 1.0.1 - *2014-12-12* ####
 Removed version checking, as it was throwing errors.

#### 1.0.0 - *2014-12-12* ####
 Initial documented, stable release.

#### 0.1.0 - *2014-12-11* ####
 Initial release.
