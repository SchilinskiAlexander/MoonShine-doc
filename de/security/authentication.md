# Authentifizierung

- [Grundlagen](#grundlagen)
- [Konfiguration](#konfiguration)
- [Anpassung](#anpassung)
- [Deaktivierung der Authentifizierung](#authentifizierung-deaktivieren)
- [Eigenes Benutzermodell](#eigenes-benutzermodell)
- [Benutzerdefinierte Felder und Profil](#benutzerdefinierte-felder)
- [Rollenbasierter Zugriff](#rollenbasierter-zugriff)
- [Authentifizierungspipelines](#authentifizierungspipelines)
- [Socialite](#socialite)
- [Zwei-Faktor-Authentifizierung](#zwei-faktor-authentifizierung)
- [JWT](#jwt)

---

<a name="grundlagen"></a>
## Grundlagen

`MoonShine` bietet ein integriertes Authentifizierungssystem, das standardmäßig ein eigenes Benutzermodell und `guard` verwendet.
Damit können Sie schnell mit dem Admin-Panel starten, ohne sich um die Einrichtung der Authentifizierung kümmern zu müssen.

<a name="konfiguration"></a>
## Konfiguration

Die Hauptauthentifizierungseinstellungen befinden sich in der Konfigurationsdatei `config/moonshine.php` im Abschnitt `auth`:

```php
'auth' => [
    'enabled' => true,
    'guard' => 'moonshine',
    'model' => MoonshineUser::class,
    'middleware' => Authenticate::class,
    'pipelines' => [],
],
```

Hier können Sie Folgendes konfigurieren:

- `enabled`: Aktivieren/Deaktivieren der integrierten Authentifizierung
- `guard`: Name des Guards für die Authentifizierung
- `model`: Klasse des Benutzermodells
- `middleware`: Middleware für die Authentifizierung
- `pipelines`: Zusätzliche Pipelines für den Authentifizierungsprozess

<a name="anpassung"></a>
## Anpassung

Sie können die Authentifizierung im `MoonShineServiceProvider` anpassen:

```php
$config
    ->guard('admin')
    ->authMiddleware(CustomAuthMiddleware::class)
    ->authPipelines([
        TwoFactorAuthentication::class,
        PhoneVerification::class,
    ]);
```

<a name="authentifizierung-deaktivieren"></a>
## Deaktivierung der Authentifizierung

Wenn Sie die integrierte Authentifizierung von `MoonShine` deaktivieren möchten, können Sie dies im `MoonShineServiceProvider` tun:

```php
$config->authDisable();
```

<a name="eigenes-benutzermodell"></a>
## Eigenes Benutzermodell

Falls Sie ein eigenes Benutzermodell anstelle von `MoonshineUser` verwenden möchten, können Sie es in der Konfiguration angeben:

```php
'auth' => [
    'model' => App\Models\Admin::class,
],
```

<a name="benutzerdefinierte-felder"></a>
## Benutzerdefinierte Felder und Profil

MoonShine ermöglicht es, die Benutzerfelder für die Authentifizierung und das Profil anzupassen:

```php
$config
    ->userField('username', 'login')
    ->userField('password', 'pass')
    ->userField('name', 'full_name')
    ->userField('avatar', 'profile_image');
```

Falls Sie die Profilseite komplett durch Ihre eigene ersetzen möchten, können Sie dies über die Konfiguration `moonshine.php` tun:

```php
'pages' => [
    'profile' => App\MoonShine\Pages\CustomProfile::class,
],
```

Oder über den `MoonShineServiceProvider`:

```php
$config->changePage(\MoonShine\Laravel\Pages\ProfilePage::class, \App\MoonShine\Pages\CustomProfile::class);
```

<a name="rollenbasierter-zugriff"></a>
## Rollenbasierter Zugriff

Falls Sie den Zugriff auf das MoonShine-Admin-Panel basierend auf Benutzerrollen oder anderen Bedingungen einschränken möchten, können Sie einfach eine eigene `middleware` hinzufügen.

### Middleware erstellen

Erstellen Sie eine neue `middleware`, beispielsweise `CheckAdminRole`:

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class CheckAdminRole
{
    public function handle(Request $request, Closure $next): Response
    {
        if ($request->user() && ! $request->user()->hasRole('admin')) {
            abort(403, 'Zugriff verweigert.');
        }

        return $next($request);
    }
}
```

### Middleware zur Konfiguration hinzufügen

Fügen Sie die `middleware` zur Datei `config/moonshine.php` hinzu:

```php
'middleware' => [
    // ... andere Middleware
    \App\Http\Middleware\CheckAdminRole::class,
],
```

Oder im `MoonShineServiceProvider`:

```php
$config->addMiddleware([
    \App\Http\Middleware\CheckAdminRole::class,
]);
```

<a name="authentifizierungspipelines"></a>
## Authentifizierungspipelines

Authentifizierungs-`pipelines` in MoonShine ermöglichen es, zusätzliche Prüfungen und Aktionen zum Authentifizierungsprozess hinzuzufügen.

### Pipelines konfigurieren

Konfigurieren Sie `pipelines` im `MoonShineServiceProvider`:

```php
$config->authPipelines([
    \App\MoonShine\AuthPipelines\TwoFactorAuthentication::class,
    \App\MoonShine\AuthPipelines\PhoneVerification::class,
]);
```

### Pipeline erstellen

Beispiel: Telefonbestätigung bei der Anmeldung:

```php
<?php

namespace App\MoonShine\AuthPipelines;

use Closure;
use MoonShine\Laravel\Models\MoonshineUser;

class PhoneVerification
{
    public function handle(Request $request, Closure $next)
    {
        $user = MoonshineUser::query()
            ->where('email', $request->get('username'))
            ->first();

         if (! is_null($user) && ! is_null($user->getAttribute('phone'))) {
            $request->session()->put([
                'login.id' => $user->getKey(),
                'login.remember' => $request->boolean('remember'),
            ]);

            return redirect()->route('sms-challenge');
        }

        return $next($user);
    }
}
```

### Vorteile von Pipelines

- **Flexibilität**: Einfaches Hinzufügen, Entfernen oder Ändern der Reihenfolge von Prüfungen.
- **Modularität**: Jeder Aspekt der erweiterten Authentifizierung ist in einer separaten Klasse isoliert.
- **Erweiterbarkeit**: Neue Authentifizierungsmethoden oder Prüfungen können leicht hinzugefügt werden.

<a name="socialite"></a>
# Socialite

Für mehr Komfort können Sie Ihr Konto mit sozialen Netzwerken verknüpfen, um den Authentifizierungsprozess zu vereinfachen.

Diese Funktionalität basiert auf dem [Laravel Socialite](https://laravel.com/docs/socialite)-Paket.

Stellen Sie sicher, dass es installiert und konfiguriert ist.

Installieren Sie das Paket für die Integration von `Socialite` in `MoonShine`:

```shell
composer require moonshine/socialite
```

Führen Sie Migrationen aus:

```shell
php artisan migrate
```

Veröffentlichen Sie die Konfigurationsdatei:

```shell
php artisan vendor:publish --provider="MoonShine\Socialite\Providers\SocialiteServiceProvider"
```

Fügen Sie dann im `config/moonshine-socialite.php` die verfügbaren Treiber und das Bild für die Schaltfläche hinzu:

```php
return [
    'drivers' => [
        'github' => '/images/github.png',
        'facebook' => '/images/facebook.svg',
    ],
];
```

> [!NOTE]
> Treiber müssen im `Socialite`-Paket vorab konfiguriert werden.

<a name="zwei-faktor-authentifizierung"></a>
# Zwei-Faktor-Authentifizierung

Für zusätzliche Sicherheit können Sie die Zwei-Faktor-Authentifizierung einrichten:

```shell
composer require moonshine/two-factor
```

Führen Sie anschließend die Migrationen aus:

```shell
php artisan migrate
```

Fügen Sie dann `authPipeline` hinzu:

~~~tabs
tab: config
```php
use MoonShine\TwoFactor\TwoFactorAuthPipe;

return [
    // ...
    'auth' => [
        // ...
        'pipelines' => [
            TwoFactorAuthPipe::class
        ],
        // ...
    ]
    // ...
];
```
tab: MoonShineServiceProvider
```php
use MoonShine\TwoFactor\TwoFactorAuthPipe;

$config->authPipelines([
    TwoFactorAuthPipe::class
]);
```
~~~

Fügen Sie das Trait `MoonShine\TwoFactor\Traits\TwoFactorAuthenticatable` zum Modell hinzu, das für die Benutzer des Admin-Panels verantwortlich ist (standardmäßig `MoonshineUser`).

<a name="jwt"></a>
# JWT

`MoonShine` bietet außerdem eine einfache Möglichkeit, das Admin-Panel in den `API`-Modus zu versetzen und über Tokens zu interagieren.

Für weitere Details lesen Sie den Abschnitt [API](/docs/{{version}}/frontend/api).
