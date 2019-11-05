# Drupal 8 for Platform.sh

This template builds Drupal 8 using the [Drupal Composer project](https://github.com/drupal-composer/drupal-project) for better flexibility.  It also includes configuration to use Redis for caching, although that must be enabled post-install in `.platform.app.yaml`.

Drupal is a flexible and extensible PHP-based CMS framework.

## Services

* PHP 7.2
* MariaDB 10.2
* Redis 5

## Post-install

1. Run through the Drupal installer as normal.  You will not be asked for database credentials as those are already provided.

2. Once Drupal is fully installed, edit your `.platform.app.yaml` file and uncomment the line under the `relationships` block that reads `redis: 'rediscache:redis'`.  Commit and push the changes.  That will enable Drupal's Redis cache integration.  (The Redis cache integration cannot be active during the installer.)

3. Review the docs for more information: [platform.sh docs for Redis with Drupal 8](https://docs.platform.sh/frameworks/drupal8/redis.html#using-redis-with-drupal-8x)

## Customizations

The following changes have been made relative to Drupal 8 as it is downloaded from Drupal.org.  If using this project as a reference for your own existing project, replicate the changes below to your project.

* It uses the Drupal Composer project, which allow the site to be managed entirely with Composer. That also causes the `vendor` and `config` directories to be placed outside of the web root for added security.  See the [Drupal documentation](https://www.drupal.org/node/2404989) for tips on how best to leverage Composer with Drupal 8.
* The `.platform.app.yaml`, `.platform/services.yaml`, and `.platform/routes.yaml` files have been added.  These provide Platform.sh-specific configuration and are present in all projects on Platform.sh.  You may customize them as you see fit.
* The `.platform.template.yaml` file contains information needed by Platform.sh's project setup process for templates.  It may be safely ignored or removed.
* An additional Composer library, [`platformsh/config-reader`](https://github.com/platformsh/config-reader-php), has been added.  It provides convenience wrappers for accessing the Platform.sh environment variables.
* The `settings.platformsh.php` file contains Platform.sh-specific code to map environment variables into Drupal configuration. You can add to it as needed. See the documentation for more examples of common snippets to include here.  It uses the Config Reader library.
* The `settings.php` file has been heavily customized to only define those values needed for both Platform.sh and local development.  It calls out to `settings.platformsh.php` if available.  You can add additional values as documented in `default.settings.php` as desired.  It is also setup such that when you install Drupal on Platform.sh the installer will not ask for database credentials as they will already be defined.

## References

* [Drupal](https://www.drupal.org/)
* [Drupal on Platform.sh](https://docs.platform.sh/frameworks/drupal8.html)
* [PHP on Platform.sh](https://docs.platform.sh/languages/php.html)

## Lando Quick Start

This assumes you've installed Drupal using the Standard installation profile

### Enable and set themes

```lando drush theme:enable adminimal_theme && lando drush theme:enable octopus && lando drush config-set system.theme admin adminimal_theme -y && lando drush config-set system.theme default octopus -y```

### Enable contrib modules

```lando drush en address, admin_toolbar_links_access_filter, admin_toolbar_tools, adminimal_admin_toolbar, allowed_formats, anchor_link, antibot, auto_entitylabel, ckeditor_a11ychecker, content_moderation, crop, ctools, datetime_range, dropzonejs, dropzonejs_eb_widget, editor_advanced_link, editor_button_link, entity_browser, entity_browser_entity_form, entity_embed, entity_reference_revisions, entity_tasks, embed, field_group, ga, image_widget_crop, inline_entity_form, inline_responsive_images, linkit, media, menu_block, metatag, pathauto, paragraphs, simple_gmap, simple_sitemap, smtp, telephone, token, twig_tweak, webform, webform_ui, workflows -y```

### Enable custom modules for general settings

```lando drush en ubc_ckeditor_widgets, ubc_date_formats, ubc_editor_config, ubc_image_styles, ubc_paragraph_entities, ubc_general_shared_config -y```

### Enable custom content type modules

```lando drush en ubc_editor_file_entities, ubc_announcement, ubc_homepage, ubc_event, ubc_landing_page, ubc_page, ubc_profile -y```

### Enable User Role and Views modules last, once all the pieces are in place.

```lando drush en ubc_announcement_views, ubc_homepage_views, ubc_event_views, ubc_landing_page_views, ubc_profile_views, ubc_taxonomy_terms, ubc_user_roles -y```

### POST INSTALL

GO to:

- /admin/structure/types/manage/article/delete
- /admin/structure/types/manage/page/delete
(TODO: these (below) could probably be run via drush)
- /admin/config/content/formats/manage/full_html/disable
- /admin/config/content/formats/manage/restricted_html/disable
- /admin/config/content/formats/manage/basic_html/disable
