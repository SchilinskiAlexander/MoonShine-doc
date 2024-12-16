# Konfiguration

- [Einführung](#einführung)
- [Konfigurationsmethoden](#konfigurationsmethoden)
    - [Konfiguration über die moonshine.php-Datei](#config-datei)
    - [Konfiguration über MoonShineServiceProvider](#service-provider)
- [Grundeinstellungen](#grundeinstellungen)
    - [Optionen](#optionen)
    - [Titel](#titel)
    - [Logo](#logo)
    - [Middleware](#middleware)
    - [Routing](#routing)
    - [Authentifizierung](#authentifizierung)
    - [Lokalisierung](#lokalisierung)
    - [Speicher](#speicher)
    - [Layout](#layout)
    - [Formulare](#formulare)
    - [Seiten](#seiten)
- [Seiten und Formulare abrufen](#seiten-formulare)
- [Vollständige Liste der Konfigurationsparameter](#konfigurationsoptionen)
- [Wahl der Konfigurationsmethode](#wahl-der-konfigurationsmethode)

---

<a name="einführung"></a>
## Einführung

`MoonShine` bietet flexible Optionen zur Konfiguration Ihrer Anwendung. In diesem Abschnitt werden die zwei Hauptwege der Konfiguration und die grundlegenden Einstellungen vorgestellt.

<a name="konfigurationsmethoden"></a>
## Konfigurationsmethoden

`MoonShine` kann auf zwei Arten konfiguriert werden:

1. Über die Konfigurationsdatei `config/moonshine.php`
2. Über `MoonShineServiceProvider` mit der Klasse `MoonShineConfigurator`

<a name="config-datei"></a>
### Konfiguration über die moonshine.php-Datei

Die Datei `config/moonshine.php` enthält alle verfügbaren Einstellungen für MoonShine. Sie können diese Einstellungen direkt in der Datei ändern.

Beispielinhalt der `moonshine.php`-Datei:

```php
return [
    'title' => env('MOONSHINE_TITLE', 'MoonShine'),
    'logo' => '/assets/logo.png',
    'domain' => env('MOONSHINE_DOMAIN'),
    'prefix' => 'admin',
    'auth' => [
        'enable' => true,
        'guard' => 'moonshine',
    ],
    'use_migrations' => true,
    'use_notifications' => true,
    'use_database_notifications' => true,
    'middleware' => [
        // ...
    ],
    'layout' => \MoonShine\Laravel\Layouts\AppLayout::class,

    // ...
];
```

#### Partielle Konfiguration

Alternativ können Sie nur die Parameter in der `moonshine.php`-Datei belassen, die von den Standardwerten abweichen. Dadurch wird die Konfiguration übersichtlicher und leichter verständlich.

Beispiel für optimierten Inhalt der `moonshine.php`-Datei:

```php
return [
    'title' => 'Meine MoonShine-Anwendung',
    'use_migrations' => true,
    'use_notifications' => true,
    'use_database_notifications' => true,
];
```

> [!NOTE]
> `use_migrations`, `use_notifications`, `use_database_notifications` müssen entweder in `moonshine.php` oder im `MoonShineServiceProvider` angegeben werden.

> [!NOTE]
> Alle anderen nicht in der Datei angegebenen Parameter verwenden die Standardwerte.

<a name="service-provider"></a>
### Konfiguration über MoonShineServiceProvider

Eine alternative Möglichkeit der Konfiguration ist die Verwendung des `MoonShineServiceProvider`. Diese Methode bietet einen programmatischen Ansatz zur Konfiguration.

Beispielkonfiguration im `MoonShineServiceProvider`:

```php
use Illuminate\Support\ServiceProvider;
use MoonShine\Contracts\Core\DependencyInjection\CoreContract;
use MoonShine\Laravel\DependencyInjection\MoonShine;
use MoonShine\Laravel\DependencyInjection\MoonShineConfigurator;
use MoonShine\Laravel\DependencyInjection\ConfiguratorContract;

class MoonShineServiceProvider extends ServiceProvider
{
    public function boot(CoreContract $core, ConfiguratorContract $config): void
    {
        $config
            ->title('Meine Anwendung')
            ->logo('/assets/logo.png')
            ->prefix('admin')
            ->guard('moonshine')
            ->authEnable()
            ->useMigrations()
            ->useNotifications()
            ->useDatabaseNotifications()
            ->middleware([
                // ...
            ])
            ->layout(\MoonShine\Laravel\Layouts\AppLayout::class);

        $core
            ->resources([
                MoonShineUserResource::class,
                MoonShineUserRoleResource::class,
            ])
            ->pages([
                ...$config->getPages(),
            ]);
    }
}
```

> [!NOTE]
> Die Konfiguration über `MoonShineServiceProvider` hat Vorrang vor den Einstellungen in der `moonshine.php`-Datei.
> Bei Verwendung dieser Methode können Sie die Datei `moonshine.php` vollständig aus Ihrem Projekt entfernen.

<a name="grundeinstellungen"></a>
## Grundeinstellungen

Unabhängig von der gewählten Konfigurationsmethode können Sie die folgenden grundlegenden Parameter einrichten:

### Optionen

- `use_migrations`: Verwendung von Standard-Systemmigrationen (`moonshine_users`, `moonshine_user_roles`),
- `use_notifications`: Verwendung des Benachrichtigungssystems,
- `use_database_notifications`: Verwendung des Laravel-Benachrichtigungssystems auf Basis des Datenbanktreibers,
- `dir`: Verzeichnis für `MoonShine` (standardmäßig `app/MoonShine`),
- `namespace`: Namespace für Klassen, die über `artisan`-Befehle erstellt werden (standardmäßig `App\MoonShine`).

~~~tabs
tab: config/moonshine.php
```php
'dir' => 'app/MoonShine',
'namespace' => 'App\MoonShine',

'use_migrations' => true,
'use_notifications' => true,
'use_database_notifications' => true,
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config
    ->dir(dir: 'app/MoonShine', namespace: 'App\MoonShine')
    ->useMigrations()
    ->useNotifications()
    ->useDatabaseNotifications()
;
```
~~~

### Titel

Meta-Titel auf den Seiten (`<title>Meine Anwendung</title>`)

~~~tabs
tab: config/moonshine.php
```php
'title' => 'Meine Anwendung',
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->title('Meine Anwendung');
```
~~~

### Logo

~~~tabs
tab: config/moonshine.php
```php
'logo' => '/assets/logo.png',
'logo_small' => '/assets/logo-small.png',
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->logo('/assets/logo.png')->logo('/assets/logo-small.png', small: true);
```
~~~

### Middleware

Middleware überschreiben oder hinzufügen

~~~tabs
tab: config/moonshine.php
```php
'middleware' => [
    'web',
    'auth',
    // ...
],
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->middleware(['web', 'auth'])
       ->addMiddleware('custom-middleware')
       ->exceptMiddleware(['auth']);
```
~~~

### Routing

#### Präfixe festlegen

~~~tabs
tab: config/moonshine.php
```php
'prefix' => 'admin',
'page_prefix' => 'page',
'resource_prefix' => 'resource',
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->prefixes('admin', 'page', 'resource');
```
~~~

#### Domain festlegen

~~~tabs
tab: config/moonshine.php
```php
'domain' => 'admin.example.com',
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->domain('admin.example.com');
```
~~~

#### 404-Fehlerseite

~~~tabs
tab: config/moonshine.php
```php
'not_found_exception' => MoonShineNotFoundException::class,
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->notFoundException(MoonShineNotFoundException::class);
```
~~~

### Authentifizierung

#### Guard einstellen

~~~tabs
tab: config/moonshine.php
```php
'auth' => [
    'guard' => 'admin',
    // ...
],
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->guard('admin');
```
~~~

#### Eingebaute Authentifizierung deaktivieren

~~~tabs
tab: config/moonshine.php
```php
'auth' => [
    'enable' => false,
    // ...
],
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->authDisable();
```
~~~

#### Modell ändern

```php
'auth' => [
    // ...
    'model' => User::class,
    // ...
],
```

> [!NOTE]
> Dies wird während der Anwendungsinitialisierung angegeben und muss daher ausschließlich über die Konfigurationsdatei festgelegt werden.

#### Middleware für Sitzung überprüfen

~~~tabs
tab: config/moonshine.php
```php
'auth' => [
    // ...
    'middleware' => Authenticate::class,
    // ...
],
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->authMiddleware(Authenticate::class);
```
~~~

### Lokalisierung

#### Standardsprache

~~~tabs
tab: config/moonshine.php
```php
'locale' => 'de',
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->locale('de');
```
~~~

#### Verfügbare Sprachen einstellen

~~~tabs
tab: config/moonshine.php
```php
'locales' => ['de', 'en'],
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->locales(['de', 'en']);
```
~~~

### Speicher

#### Standardspeicher einstellen

~~~tabs
tab: config/moonshine.php
```php
'disk' => 'public',
'disk_options' => [],
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->disk('public', options: []);
```
~~~

#### Cache-Driver

~~~tabs
tab: config/moonshine.php
```php
'cache' => 'file',
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->cacheDriver('redis');
```
~~~

### Layout

~~~tabs
tab: config/moonshine.php
```php
'layout' => \App\MoonShine\Layouts\CustomLayout::class,
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->layout(\App\MoonShine\Layouts\CustomLayout::class);
```
~~~

### Formulare

~~~tabs
tab: config/moonshine.php
```php
'forms' => [
    'login' => LoginForm::class,
    'filters' => FiltersForm::class,
],
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->set('forms.login', MyLoginForm::class);
```
~~~

### Seiten

~~~tabs
tab: config/moonshine.php
```php
'pages' => [
    'dashboard' => Dashboard::class,
    'profile' => ProfilePage::class,
    'login' => LoginPage::class,
    'error' => ErrorPage::class,
],
```
tab: app/Providers/MoonShineServiceProvider.php
```php
$config->changePage(LoginPage::class, MyLoginPage::class);
```
~~~

<a name="seiten-formulare"></a>
## Seiten und Formulare abrufen

`MoonShine` bietet bequeme Methoden zum Abrufen von Seiten und Formularen in Ihrer Anwendung.

### Seiten abrufen

Die Methode `getPage` ermöglicht es, eine Instanz einer Seite anhand ihres Namens oder eine Standardseite abzurufen.

```php
public function getPage(string $name, string $default, mixed ...$parameters): PageContract
```

**Parameter:**

- `$name`: Name der Seite in der Konfiguration
- `$default`: Standardseitenklasse, wenn in der Konfiguration nicht gefunden
- `$parameters`: Zusätzliche Parameter für den Seitenkonstruktor

**Beispiel:**

```php
// Helper
$customPage = moonshineConfig()->getPage('custom');
```

```php
// DI
use MoonShine\Contracts\Core\DependencyInjection\ConfiguratorContract;
use MoonShine\Laravel\DependencyInjection\MoonShineConfigurator;

/**
 * @param MoonShineConfigurator $configurator
 */
public function index(ConfiguratorContract $config)
{
  $customPage = $config->getPage('custom');
}
```

### Formulare abrufen

Die Methode `getForm` ermöglicht es, eine Instanz eines Formulars anhand seines Namens oder ein Standardformular abzurufen.

```php
public function getForm(string $name, string $default, mixed ...$parameters): FormBuilderContract
```

**Parameter:**

- `$name`: Name des Formulars in der Konfiguration
- `$default`: Standardformularklasse
- `$parameters`: Zusätzliche Parameter für den Formularkonstruktor

**Beispiel:**

```php
// Helper
$form = moonshineConfig()->getForm('login');
```

```php
// DI
use MoonShine\Contracts\Core\DependencyInjection\ConfiguratorContract;
use MoonShine\Laravel\DependencyInjection\MoonShineConfigurator;

/**
 * @param MoonShineConfigurator $configurator
 */
public function index(ConfiguratorContract $config)
{
  $form = $config->getForm('login');
}
```

### Seiten und Formulare in der Konfiguration deklarieren

Sie können die Zuordnung zwischen den Namen und Klassen von Seiten und Formularen in der Datei `moonshine.php` einrichten:

```php
return [
    // Weitere Einstellungen...

    'pages' => [
        'dashboard' => \App\MoonShine\Pages\DashboardPage::class,
        'custom' => \App\MoonShine\Pages\CustomPage::class,
    ],

    'forms' => [
        'login' => \App\MoonShine\Forms\LoginForm::class,
        'custom' => \App\MoonShine\Forms\CustomForm::class,
    ],
];
```

Dadurch können Sie die gewünschten Seiten und Formulare anhand ihrer Namen mit den Methoden `getPage` und `getForm` einfach abrufen.

> [!NOTE]
> Einige Methoden des `MoonShineConfigurator` haben keine direkten Entsprechungen in der Datei `moonshine.php` und umgekehrt. Dies liegt an den Unterschieden in den Ansätzen zur Konfiguration über die Datei und den Code.

### Beispiel für die Nutzung im MoonShineServiceProvider

```php
use Illuminate\Support\ServiceProvider;
use MoonShine\Contracts\Core\DependencyInjection\CoreContract;
use MoonShine\Laravel\DependencyInjection\MoonShine;
use MoonShine\Laravel\DependencyInjection\MoonShineConfigurator;
use MoonShine\Laravel\DependencyInjection\ConfiguratorContract;

class MoonShineServiceProvider extends ServiceProvider
{
    /**
     * @param MoonShine $core
     * @param MoonShineConfigurator $config
     */
    public function boot(
        CoreContract $core,
        ConfiguratorContract $config,
    ): void
    {
        $config
            ->title('Meine Anwendung')
            ->dir('app/MoonShine', 'App\MoonShine')
            ->prefix('admin')
            ->guard('moonshine')
            ->middleware(['web', 'auth'])
            ->layout(\App\MoonShine\Layouts\CustomLayout::class)
            ->locale('de')
            ->locales(['de', 'en'])
            ->useMigrations()
            ->useNotifications()
            ->useDatabaseNotifications()
            ->cacheDriver('redis')
            ->authorizationRules(function(ResourceContract $ctx, mixed $user, Ability $ability, mixed $data): bool {
                 return true;
            });

        // ..
    }
}
```

Diese vollständige Liste von Parametern und Methoden ermöglicht es Ihnen, nahezu jeden Aspekt der Operationen von `MoonShine` zu konfigurieren. Wählen Sie die Optionen, die am besten zu Ihren Projektanforderungen passen.

<a name="wahl-der-konfigurationsmethode"></a>
## Wahl der Konfigurationsmethode

Berücksichtigen Sie bei der Wahl der Konfigurationsmethode Folgendes:

1. **Priorität**: Die Konfiguration über den `MoonShineServiceProvider` hat Vorrang vor Einstellungen in der `moonshine.php`-Datei.

2. **Flexibilität**:
    - Eine vollständige Konfiguration über `moonshine.php` bietet einen klaren Überblick über alle Einstellungen.
    - Eine partielle Konfiguration über `moonshine.php` erleichtert das Erkennen geänderter Parameter.
    - Die Konfiguration über `MoonShineServiceProvider` bietet maximale Flexibilität.

3. **Wartungsfreundlichkeit**:
    - Die `moonshine.php`-Datei eignet sich besser für schnelle Änderungen und einen Überblick über die gesamte Struktur.
    - `MoonShineServiceProvider` ermöglicht eine zentrale Verwaltung von Einstellungen im Code.

Wählen Sie die Methode, die Ihrem Entwicklungsstil und Ihren Projektanforderungen am besten entspricht.
