# Autorisierung

- [Grundlagen](#grundlagen)
- [Zusätzliche Logik](#zusätzliche-logik)

---

<a name="grundlagen"></a>
## Grundlagen

Das **MoonShine**-Admin-Panel bleibt den Laravel-Konzepten treu und verwendet ebenfalls *Laravel Policies* für die Arbeit mit Zugriffsrechten. In den Resource-Controllern von MoonShine wird jede Methode auf Berechtigungen überprüft. Wenn Sie auf Schwierigkeiten stoßen, lesen Sie die offizielle [Laravel-Dokumentation](https://laravel.com/docs/authorization#creating-policies).

Standardmäßig sind Berechtigungsprüfungen für Ressourcen deaktiviert. Um sie zu aktivieren, müssen Sie die Eigenschaft `withPolicy` hinzufügen.

```php
namespace App\MoonShine\Resources;
use App\Models\Post;
use MoonShine\Resources\ModelResource;

class PostResource extends ModelResource
{
    protected string $model = Post::class;

    protected bool $withPolicy = true;
    //...
}
```

Um eine *Policy* zu erstellen, die mit dem Benutzer des Admin-Panels verknüpft ist, können Sie den Konsolenbefehl verwenden:

```bash
php artisan moonshine:policy
```

Verfügbare Policy-Methoden:
- `viewAny` - Index-Seite;
- `view` - Detail-Seite;
- `create` - Erstellen eines Eintrags;
- `update` - Bearbeiten eines Eintrags;
- `delete` - Löschen eines Eintrags;
- `massDelete` - Massenlöschung von Einträgen;
- `restore` - Wiederherstellen eines Eintrags nach einem Soft Delete;
- `forceDelete` - Permanentes Löschen eines Eintrags aus der Datenbank.

```php
namespace App\Policies;
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\Post;
use MoonShine\Models\MoonshineUser;

class PostPolicy
{
    use HandlesAuthorization;

    public function viewAny(MoonshineUser $user)
    {
        return true;
    }

    public function view(MoonshineUser $user, Post $item)
    {
        return true;
    }

    public function create(MoonshineUser $user)
    {
        return true;
    }

    public function update(MoonshineUser $user, Post $item)
    {
        return true;
    }

    public function delete(MoonshineUser $user, Post $item)
    {
        return true;
    }

    public function restore(MoonshineUser $user, Post $item)
    {
        return true;
    }

    public function forceDelete(MoonshineUser $user, Post $item)
    {
        return true;
    }

    public function massDelete(MoonshineUser $user)
    {
        return true;
    }
}
```

<a name="zusätzliche-logik"></a>
## Zusätzliche Logik

Falls Sie zusätzliche Autorisierungslogik zu Ihrer Anwendung oder einem externen Paket hinzufügen müssen, verwenden Sie die Methode `authorizationRules` im `AuthServiceProvider` oder `MoonShineServiceProvider`.

```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use MoonShine\Contracts\Core\DependencyInjection\CoreContract;
use MoonShine\Laravel\DependencyInjection\MoonShine;
use MoonShine\Laravel\DependencyInjection\MoonShineConfigurator;
use MoonShine\Laravel\DependencyInjection\ConfiguratorContract;
use MoonShine\Laravel\Enums\Ability;
use Illuminate\Database\Eloquent\Model;
use MoonShine\Contracts\Core\DependencyInjection\ConfiguratorContract;
use MoonShine\Contracts\Core\ResourceContract;

class MoonShineServiceProvider extends ServiceProvider
{
    /**
     * @param  MoonShine  $core
     * @param  MoonShineConfigurator  $config
     *
     */
    public function boot(
        CoreContract $core,
        ConfiguratorContract $config,
    ): void
    {
        $config->authorizationRules(
            static function (ResourceContract $resource, Model $user, Ability $ability, Model $item): bool {
                return true;
            }
        );

        // ..
    }
}
```