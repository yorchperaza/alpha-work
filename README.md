# CaregiversDirect

Development repository for caregiversdirect.com.

# Acquia URLs
* https://caregiversdev.prod.acquia-sites.com (Dev)
* https://stage.caregiversdirect.com (Test)
* TBD (Prod)

# Development URLs
* CaregiversDirect site: http(s)://cgd.local
* DrupalVM Dashboard: http://dashboard.cgd.local
* Adminer: http://adminer.cgd.local
* Pimp My Logs: http://pimpmylog.cgd.local
* MailHog: http://cgd.local:8025

# Development Setup

This site is pre-configured to leverage DrupalVM as its local development environment.

## Prerequisites
* Vagrant 1.8.6 (https://www.vagrantup.com/downloads.html)
* VirtualBox 5.1.10+ (https://www.virtualbox.org/wiki/Downloads)
* Composer installed globally (https://getcomposer.org/)
* PHP 7.1 installed on host computer

## Steps to setup local environment:

### Setup PHP

If you don't have PHP 7.1 installed already:

Remove PHP 7.0 if it is installed:
```
brew uninstall php70-mycrypt
brew uninstall php70
brew uninstall --force libjpeg
```

Install PHP 7.1:
```bash
brew install --build-from-source php71
brew install php71-mcrypt
```

### Initialize the vagrant machine

```
vagrant up
```

You may have to increase php's memory if you are seeing memory issues after running composer install.

Type ```php --ini``` to find your loaded php.ini file. Find the memory_limit setting and increase it.

### Ensure that you are on the latest develop branch.

```
git pull
git checkout develop 
```

### Import the dev database
```
vagrant ssh
```

Download the latest dev snapshot from Acquia Cloud (https://cloud.acquia.com/app/develop/applications/7c125bfb-51d0-4c3f-9827-04d6a1cec056/environments/35877-7c125bfb-51d0-4c3f-9827-04d6a1cec056/databases) and import the site's database. 
```
drush sql-cli < path/to/your/db_file.sql
```
 
### Ensure that the site configuration is up to date
```
drush cim
``` 

### Setup keys

The site uses Lockr to store sensitive information (mainly API keys). Go to https://cgd.local/admin/config/system/lockr to setup your certificate path.

Ensure that the certificate path is set to `/opt/cgd/private/lockr/dev/pair.pem`. 

If this is not your first time doing this, this file may already exist for you. In that case, just update the Certificate path to be `/opt/cgd/private/lockr/dev/pair.pem` and click "Save".

If it does not, you'll have to generate a certificate by clicking "Create Certificate". You can leave the Country, State or Province, Locality, and Organization fields as the defaults. This should create a certificate at the path `/opt/cgd/private/lockr/dev/pair.pem` (you may have to change file permissions for it to generate).

Manually recreate the keys found here: https://www.dev.caregiversdirect.com/admin/config/system/keys. You won't be able to see the "key value" field, so you'll need to go here https://achieveinternet.atlassian.net/wiki/spaces/CAR/pages/12484989/Integrations to get them.

### Setup encryption profile

Go to https://cgd.local/admin/config/system/encryption/profiles and click "Add Profile" to create the encryption profile.

Copy the settings for the Lockr Encrypt Profile found on dev: https://www.dev.caregiversdirect.com/admin/config/system/encryption/profiles/manage/lockr_encrypt. Be sure the machine name matches.

*NOTE: it's a good idea to export the both the keys and profile config files and stash them somewhere so that they don't get deleted anytime you need to synchronize new site config. Do not commit these files.*

### Setup Terms & Conditions
* Go to https://cgd.local/admin/structure/legal. You should have all the documents listed here: https://www.dev.caregiversdirect.com/admin/structure/legal
* On your local site, edit each one to setup the version -- just match everything up to dev.
* Note: If the Terms & Conditions are needed for your current ticket, you'll need to do this every time you import new site config. For some reason, the version setting gets lost.

## What gets setup
Running ```composer install``` and ```vagrant up``` creates the following:
* A DrupalVM instance supporting both www and non-www URLs.
* A DrupalVM instance supporting SSL on the www URL.

## Debugging
You can debug the site using XDEBUG just as you would with any normal locally installed development environment.

# Theming
The theme is a subtheme of Drupal's contrib bootstrap implementation. It is located at ./docroot/themes/custom/cgd_bootstrap

## Images
New theme images can be added directly into the images folder of the theme. They are not additionally processed or minified, and should be preprocessed before adding.

## SCSS
See the README.md file inside the theme for instructions on writing, editing, and compiling the scss in the theme.

## Templates
Templates for entities are grouped by folders of each entity type. For any extensive logic or repeat markup, use a preprocess function.

## Modals
You can make a modal using the standard bootstrap code, no additional javascript required. 
Be sure to use an ID for your modal that will be unique, since there can be several on a page.

Twig:
```
  {%
    set modal_id = 'video-modal--node-' ~ node.id()|clean_class
  %}
  
  <!-- Link HTML (to Trigger Modal) -->
  <a href="#{{ modal_id }}" role="button" data-toggle="modal">Link</a>

  <!-- Modal HTML -->
  <div id="{{ modal_id }}" class="modal fade">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
        </div>
        <div class="modal-body">
          Modal Content
        </div>
      </div>
    </div>
  </div>
```

Plain HTML:
```<!-- Link HTML (to Trigger Modal) -->
  <a href="#my-unique-id" role="button" data-toggle="modal">Link</a>

  <!-- Modal HTML -->
  <div id="my-unique-id" class="modal fade">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
        </div>
        <div class="modal-body">
          Modal Content
        </div>
      </div>
    </div>
  </div>
```

The link and modal html in each example do not have to appear together or within the same parent to work.

# Testing Cloud Hook Scripts Locally
Add the following lines to the ~/.drush/drushrc.php file inside of your VM:
```php
$options['shell-aliases']['ah-sql-cli'] = 'sql-cli --strict=0';
```

NOTE: you can run `drush init` if you do not have the file ~/.drush/drushrc.php.


This will allow you to directly execute the post-db-copy cloud hook using:
```bash
./db-scrub.sh cgd.cgd local drupal prod
```
