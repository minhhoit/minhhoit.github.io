---
layout: post
title: List of Useful Magento 2
comments: false
categories: ["php"]
tags: ["php", "tips"]
---

Instead of running for that commands, why not bookmark this post for any future requirements as it is the compilation of all useful Magento 2 SSH commands. You need to have SSH access to your server or use the Command Line for local access.

## List of Useful Magento 2 SSH Commands, Sorted!

Instead of running for that commands, why not bookmark this post for any future requirements as it is the compilation of all useful Magento 2 SSH commands.

| SSH Commands | Shortcuts | Description |

| *Available commands: * |  &nbsp; | &nbsp; |
| help | <code>h</code> |	Displays help for a command |
| `php bin/magento list` | <code>php bin/magento l</code> |	Lists commands |
| *Admin: * |  &nbsp; | &nbsp; |
| `php bin/magento admin:user:create` |	<code>php bin/magento a:u:c</code> | Creates an administrator |
| `php bin/magento admin:user:unlock` |	<code>php bin/magento a:u:u</code> | Unlock Admin Account |
| *App: * |  &nbsp; | &nbsp; |
| `php bin/magento app:config:dump`	| &nbsp; |	Create dump of application |
| `php bin/magento app:config:import` | &nbsp; | Import data from shared configuration files to appropriate data storage |
| *Cache: * |  &nbsp; | &nbsp; |
| `php bin/magento cache:clean` | <code>php bin/magento c:c</code> | Cleans cache type(s) |
| `php bin/magento cache:disable` |	<code>php bin/magento c:d</code> | Disables cache type(s) |
| `php bin/magento cache:enable` | <code>php bin/magento c:e</code> | Enables cache type(s) |
| `php bin/magento cache:flush`	| <code>php bin/magento c:f</code> | Flushes cache storage used by cache type(s) |
| `php bin/magento cache:status` | <code>php bin/magento c:s</code> | Checks cache status |
| *Catalog: * |  &nbsp; | &nbsp; |
| `php bin/magento catalog:images:resize` | <code>php bin/magento c:i:r</code> | Creates resized product images |
| `php bin/magento catalog:product:attributes:cleanup` | <code>php bin/magento c:p:a:c</code> | Removes unused product attributes. |
| *Config: * |  &nbsp; | &nbsp; |
| `php bin/magento config:sensitive:set` | &nbsp; |	Set sensitive configuration values |
| `php bin/magento config:set` | &nbsp; | Change system configuration |
| `php bin/magento config:show`	| &nbsp; | Shows configuration value for given path. If path is not specified, all saved values will be shown |
| *Cron: * |  &nbsp; | &nbsp; |
| `php bin/magento cron:install` | &nbsp; | Generates and installs crontab for current user |
| `php bin/magento cron:remove` | &nbsp; | Removes tasks from crontab |
| `php bin/magento cron:run` | &nbsp; |	<code>php bin/magento c:r</code> | Runs jobs by schedule |
| *Customer: * |  &nbsp; | &nbsp; |
| `php bin/magento customer:\hash:upgrade` | <code>php bin/magento c:h:u</code> | Upgrade customer’s hash according to the latest algorithm |
| *Deploy: * |  &nbsp; | &nbsp; |
| `php bin/magento deploy:mode:set` | <code>php bin/magento d:m:set</code> | Set application mode. |
| `php bin/magento deploy:mode:show` | <code>php bin/magento d:m:sho</code> | Displays current application mode. |
| *Dev: * |  &nbsp; | &nbsp; |
| `php bin/magento dev:di:info` | &nbsp; | Provides information on Dependency Injection configuration for the Command. |
| `php bin/magento dev:query-log:disable` | &nbsp; | Disable DB query logging |
| `php bin/magento dev:query-log:enable` | &nbsp; |	Enable DB query logging |
| `php bin/magento dev:source-theme:deploy` | <code>php bin/magento d:s:d</code> | Collects and publishes source files for theme. |
| `php bin/magento dev:template-hints:disable` | &nbsp; | Disable frontend template hints. A cache flush might be required. |
| `php bin/magento dev:template-hints:enable` | &nbsp; | Enable frontend template hints. A cache flush might be required. |
| `php bin/magento dev:tests:run` |	<code>php bin/magento d:t:r</code> | Runs tests |
| `php bin/magento dev:urn-catalog:generate` |	<code>php bin/magento d:u:g</code> | Generates the catalog of URNs to *.xsd mappings for the IDE to highlight xml. |
| `php bin/magento dev:xml:convert`	| <code>php bin/magento d:x:c</code> | Converts XML file using XSL style sheets |
| *I18n: * |  &nbsp; | &nbsp; |
| `php bin/magento i18n:collect-phrases` | <code>php bin/magento i1:c</code> | Discovers phrases in the codebase |
| `php bin/magento i18n:pack` | <code>php bin/magento i:p</code> | Saves language package |
| `php bin/magento i18n:uninstall` | <code>php bin/magento i:u</code> | Uninstalls language packages |
| *Indexer: * |  &nbsp; | &nbsp; |
| `php bin/magento indexer:info` | <code>php bin/magento i:i</code> | Shows allowed Indexers |
| `php bin/magento indexer:reindex` | <code>php bin/magento i:rei</code> | Reindexes Data |
| `php bin/magento indexer:reset` | <code>php bin/magento i:res</code> | Resets indexer status to invalid |
| `php bin/magento indexer:set-mode` |	<code>php bin/magento i:set</code> | Sets index mode type |
| `php bin/magento indexer:show-mode` |	<code>php bin/magento i:sho</code> | Shows Index Mode |
| `php bin/magento indexer:status`	| <code>php bin/magento i:sta</code> | Shows status of Indexer |
| `php bin/magento i18n:uninstall` | <code>php bin/magento i:u</code> | Uninstalls language packages |
| *Info: * |  &nbsp; | &nbsp; |
| `php bin/magento info:adminuri` | <code>php bin/magento i:a</code> |	Displays the Magento Admin URI |
| `php bin/magento info:backups:list` | <code>php bin/magento i:b:l</code> | Prints list of available backup files |
| `php bin/magento info:currency:list` | <code>php bin/magento i:c:l</code> | Displays the list of available currencies |
| `php bin/magento info:dependencies:show-framework` | <code>php bin/magento i:d:show-f</code> | Shows number of dependencies on Magento framework |
| `php bin/magento info:dependencies:show-modules` |  &nbsp; | Shows number of dependencies between modules |
| `php bin/magento info:dependencies:show-modules-circular` |	&nbsp; | Shows number of circular dependencies between modules |
| `php bin/magento info:language:list` | <code>php bin/magento i:l:l</code> | Displays the list of available language locales |
| `php bin/magento info:timezone:list` | <code>php bin/magento i:t:l</code> | Displays the list of available timezones |
| *Maintenance: * |  &nbsp; | &nbsp; |
| `php bin/magento maintenance:allow-ips` |	<code>php bin/magento m:a</code> | Sets maintenance mode exempt IPs |
| `php bin/magento maintenance:disable` | <code>php bin/magento ma:d</code> | Disables maintenance mode |
| `php bin/magento maintenance:enable` | <code>php bin/magento ma:e</code> | Enables maintenance mode |
| `php bin/magento maintenance:status` | <code>php bin/magento ma:s</code> | Displays maintenance mode status |
| *Module: * |  &nbsp; | &nbsp; |
| `php bin/magento module:disable` | <code>php bin/magento mo:d</code> | Disables specified modules |
| `php bin/magento module:enable` |	<code>php bin/magento mo:e</code> |	Enables specified modules |
| `php bin/magento module:status` |	<code>php bin/magento mo:s</code> |	Displays status of modules |
| `php bin/magento module:uninstall` | <code>php bin/magento m:u</code>	| Uninstalls modules installed by composer |
| *Sampledata: * |  &nbsp; | &nbsp; |
| `php bin/magento sampledata:deploy` |	<code>php bin/magento sa:d</code> |	Deploy sample data modules |
| `php bin/magento sampledata:remove` |	<code>php bin/magento sa:rem</code> | Remove all sample data packages from composer.json |
| `php bin/magento sampledata:reset` |	<code>php bin/magento sa:res</code> | Reset all sample data modules for re-installation |
| `php bin/magento module:uninstall` | <code>php bin/magento m:u</code>	| Uninstalls modules installed by composer |
| *Setup: * |  &nbsp; | &nbsp; |
| `php bin/magento setup:backup` |	<code>php bin/magento s:b</code> | Takes backup of Magento Application code base, media and database |
| `php bin/magento setup:config:set` |	<code>php bin/magento s:c:s</code> |	Creates or modifies the deployment configuration |
| `php bin/magento setup:cron:run` |	<code>php bin/magento s:c:r</code> |	Runs cron job scheduled for setup application |
| `php bin/magento setup:db-data:upgrade` |	<code>php bin/magento s:db-d:u</code> |	Installs and upgrades data in the DB |
| `php bin/magento setup:db-schema:upgrade` |	<code>php bin/magento s:db-s:u</code> |	Installs and upgrades the DB schema |
| `php bin/magento setup:db:status` |	<code>php bin/magento s:d:s</code> |	Checks if DB schema or data requires upgrade |
| `php bin/magento setup:di:compile` |	<code>php bin/magento s:d:c</code> |	Generates DI configuration and all missing classes that can be auto-generated |
| `php bin/magento setup:install` |	<code>php bin/magento s:i</code> |	Installs the Magento application |
| `php bin/magento setup:performance:generate-fixtures` |	<code>php bin/magento s:p:g</code> |	Generates fixtures |
| `php bin/magento setup:rollback` |	<code>php bin/magento se:r</code> |	Rolls back Magento Application codebase, media and database |
| `php bin/magento setup:static-content:deploy` |	<code>php bin/magento s:s:d</code> |	Deploys static view files |
| `php bin/magento setup:store-config:set` | <code>php bin/magento s:s:s</code> |	Installs the store configuration. Deprecated since 2.2.0. Use config:set instead |
| `php bin/magento setup:uninstall` | <code>php bin/magento s:un</code> |	Uninstalls the Magento application |
| `php bin/magento setup:upgrade` |	<code>php bin/magento s:up</code> |	Upgrades the Magento application, DB data, and schema |
| *Store: * |  &nbsp; | &nbsp; |
| `php bin/magento store:list` | &nbsp; | Displays the list of stores |
| `php bin/magento store:website:list` | &nbsp; | Displays the list of websites |
| *Store: * |  &nbsp; | &nbsp; |
| `php bin/magento theme:uninstall` | <code>php bin/magento t:u</code> | Uninstalls theme |
| *Varnish: * |  &nbsp; | &nbsp; |
| `php bin/magento varnish:vcl:ge` |  &nbsp; | &nbsp; |
Hopefully, the above compilation makes your task easy each time you require an SSH/CLI command!
I’ll surely update the post if I find any handy Magento 2 CLI command useful to the developer!