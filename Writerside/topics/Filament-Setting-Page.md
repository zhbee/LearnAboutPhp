# Setting Page

first we need to install setting plugin for filament

## Installation

[spatie-laravel-settings-plugin](https://github.com/filamentphp/spatie-laravel-settings-plugin?tab=readme-ov-file)

```shell
composer require filament/spatie-laravel-settings-plugin:"^3.2" -W
composer require spatie/laravel-settings
```

You can publish and run the migrations with:

```shell
php artisan vendor:publish --provider="Spatie\LaravelSettings\LaravelSettingsServiceProvider" --tag="migrations"
php artisan migrate
```

You can publish the config file with:

```shell
php artisan vendor:publish --provider="Spatie\LaravelSettings\LaravelSettingsServiceProvider" --tag="config"
```

This is the contents of the published config file:

```php
return [

    /*
     * Each settings class used in your application must be registered, you can
     * add them (manually) here.
     */
    'settings' => [

    ],

    /*
     * The path where the settings classes will be created.
     */
    'setting_class_path' => app_path('Settings'),

    /*
     * In these directories settings migrations will be stored and ran when migrating. A settings 
     * migration created via the make:settings-migration command will be stored in the first path or
     * a custom defined path when running the command.
     */
    'migrations_paths' => [
        database_path('settings'),
    ]

    /*
     * When no repository is set for a settings class, the following repository
     * will be used for loading and saving settings.
     */
    'default_repository' => 'database',

    /*
     * Settings will be stored and loaded from these repositories.
     */
    'repositories' => [
        'database' => [
            'type' => Spatie\LaravelSettings\SettingsRepositories\DatabaseSettingsRepository::class,
            'model' => null,
            'table' => null,
            'connection' => null,
        ],
        'redis' => [
            'type' => Spatie\LaravelSettings\SettingsRepositories\RedisSettingsRepository::class,
            'connection' => null,
            'prefix' => null,
        ],
    ],

    /*
     * The encoder and decoder will determine how settings are stored and
     * retrieved in the database. By default, `json_encode` and `json_decode`
     * are used.
     */
    'encoder' => null,
    'decoder' => null,

    /*
     * The contents of settings classes can be cached through your application,
     * settings will be stored within a provided Laravel store and can have an
     * additional prefix.
     */
    'cache' => [
        'enabled' => env('SETTINGS_CACHE_ENABLED', false),
        'store' => null,
        'prefix' => null,
    ],

    /*
     * These global casts will be automatically used whenever a property within
     * your settings class isn't the default PHP type.
     */
    'global_casts' => [
        DateTimeInterface::class => Spatie\LaravelSettings\SettingsCasts\DateTimeInterfaceCast::class,
        DateTimeZone::class => Spatie\LaravelSettings\SettingsCasts\DateTimeZoneCast::class,
     // Spatie\DataTransferObject\DataTransferObject::class => Spatie\LaravelSettings\SettingsCasts\DtoCast::class,
        Spatie\LaravelData\Data::class => Spatie\LaravelSettings\SettingsCasts\DataCast::class,
    ],

    /*
     * The package will look for settings in these paths and automatically
     * register them.
     */
    'auto_discover_settings' => [
        app_path('Settings'),
    ],

    /*
     * Automatically discovered settings classes can be cached, so they don't
     * need to be searched each time the application boots up.
     */
    'discovered_settings_cache_path' => base_path('bootstrap/cache'),
];
```
## Preparing your page class

then we can create a setting class using the following command:

```shell
php artisan make:setting SettingName --group=groupName 
```

Now, you will have to add this settings class to the settings.php config file in the settings array, so it can be loaded
by Laravel:

```php
/*
 * Each settings class used in your application must be registered, you can
 * add them (manually) here.
 */
'settings' => [
    GeneralSettings::class
],
```

Each property in a settings class needs a default value that should be set in its migration. You can create a migration
as such:

```shell
php artisan make:settings-migration CreateGeneralSettings
```

This command will create a new file in database/settings where you can add the properties and their default values:

```php
use Spatie\LaravelSettings\Migrations\SettingsMigration;

class CreateGeneralSettings extends SettingsMigration
{
    public function up(): void
    {
        $this->migrator->add('general.site_name', 'Spatie');
        $this->migrator->add('general.site_active', true);
    }
}
```

We add the properties site_name and site_active here to the general group with values Spatie and true. More on
migrations later.

and now we add properties to the settings class:

```php
use Spatie\LaravelSettings\Settings;

class GeneralSettings extends Settings
{
    public string $site_name;
    
    public bool $site_active;
    
    public static function group(): string
    {
        return 'general';
    }
}
```

We should migrate your database to add the properties:

```shell
php artisan migrate
``` 

Now, we done for the laravel setting plugin, now we can move to the filament setting page.

<tip>
    for more information about the setting plugin, you can visit the 
    <a href="https://github.com/spatie/laravel-settings">laravel-settings</a>
</tip>

## Building a form

now we can create a setting page using the following command:

```shell
php artisan make:filament-settings-page GeneralSettingManage GeneralSettings
```

look at the admin panel, you will find the setting page

![截屏2024-03-16 06.17.36.png](截屏2024-03-16_06.17.36.png)

You must define a form schema to interact with your settings class inside the form() method.

```php
TextInput::make('site_name')
    ->label('Site name')
    ->required()
TextInput::make('site_active')
    ->label('Site active')
    ->required(),
```

![截屏2024-03-16 06.34.31.png](截屏2024-03-16_06.34.31.png)

The name of each form field must correspond with the name of the property on your settings class.

The form will automatically be filled with settings from the database, and saved without any extra work.

<tip>
    for more information about the filament setting page form builder, you can visit the 
    <a href="https://filamentphp.com/docs/3.x/forms/installation"> Form Builder</a>
</tip>

### Publishing translations

use the following command to publish the translations:

```shell
php artisan vendor:publish --tag=filament-spatie-laravel-settings-plugin-translations
```

for now we finished the setting page, now we can move to the next topic.



