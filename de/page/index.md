# Seiten

- [Grundlagen](#grundlagen)
- [Erstellen einer Seite](#erstellen)
- [Titel](#titel)
- [Komponenten](#komponenten)
- [Breadcrumbs](#breadcrumbs)
- [Layout](#layout)
  - [Layout ändern](#layout-ändern)
- [Alias](#alias)
- [Rendering](#rendering)
- [Vor dem Rendern](#vor-rendern)
- [Antwortmodifikation](#antwort-modifikation)
- [Lebenszyklus](#lebenszyklus)
  - [Aktive Seite](#aktive-seite)
  - [Instanzinitialisierung](#instanzinitialisierung)
- [Link von einer Ressource zu einer Seite erstellen](#link-ressource)
- [Assets](#assets)

<a name="grundlagen"></a>
## Grundlagen

*Page* ist die Grundlage des `MoonShine`-Admin-Panels. Der Hauptzweck von `Page` ist es, Komponenten darzustellen.

Seiten mit ähnlicher Logik können in einer `Ressource` zusammengefasst werden.

<a name="erstellen"></a>
## Erstellen einer Seite

Um eine Seitenklasse zu erstellen, können Sie den Konsolenbefehl verwenden:

```php
php artisan moonshine:page
```

Nach Eingabe des Klassennamens wird eine Datei erstellt, die als Grundlage für die Seite im Admin-Panel dient. Standardmäßig befindet sie sich im Verzeichnis `app/MoonShine/Pages`.

> [!NOTE]
> Seiten werden automatisch im System registriert, wenn der Befehl ausgeführt wird. Wenn Sie jedoch eine Seite manuell erstellen, muss sie im `MoonShineServiceProvider` in der Methode `$core->pages()` registriert werden.

Sie können auch den Klassennamen und den Verzeichnispfad im Befehl angeben.

```php
php artisan moonshine:page OrderStatistics --dir=Pages/Statistics
```

Die Datei `OrderStatistics` wird im Verzeichnis `app/MoonShine/Pages/Statistics` erstellt.

<a name="titel"></a>
## Titel

Der Seitentitel kann über die Eigenschaft `title` und der Untertitel über `subtitle` festgelegt werden:

```php
use MoonShine\Laravel\Pages\Page;

class CustomPage extends Page
{
    protected string $title = 'CustomPage';

    protected string $subtitle = 'Untertitel';

    //...
}
```

Falls Logik für Titel und Untertitel erforderlich ist, können die Methoden `title()` und `subtitle()` implementiert werden:

```php
class CustomPage extends Page
{
    // ...

    public function getTitle(): string
    {
        return $this->title ?: 'CustomPage';
    }

    public function getSubtitle(): string
    {
        return $this->subtitle ?: 'Untertitel';
    }

    //...
}
```

<a name="komponenten"></a>
## Komponenten

Zum Registrieren der Komponenten einer Seite wird die Methode `components()` verwendet.

```php
class CustomPage extends Page
{
    // ...

    protected function components(): iterable
    {
        return [
            Grid::make([
                Column::make([
                    Box::make([
                        //
                    ])
                ])->columnSpan(6),
                Column::make([
                    Box::make([
                        //
                    ])
                ])->columnSpan(6),
            ])
        ];
    }

    //...
}
```

> [!NOTE]
> Weitere Informationen finden Sie im Abschnitt [Komponenten](/docs/{{version}}/components/index).

<a name="breadcrumbs"></a>
## Breadcrumbs

Die Methode `getBreadcrumbs()` ist für die Generierung der Breadcrumbs verantwortlich.

```php
use MoonShine\Pages\Page;

class CustomPage extends Page
{
    // ...

    public function getBreadcrumbs(): array
    {
        return [
            '#' => $this->getTitle()
        ];
    }

    //...
}
```

<a name="layout"></a>
## Layout

Standardmäßig verwenden Seiten das Anzeige-Template `AppLayout` oder `CompactLayout`. Weitere Informationen zu Templates finden Sie im Abschnitt [Layout](/docs/{{version}}/appearance/layout).

```php
use MoonShine\Laravel\Layouts\AppLayout;

class CustomPage extends Page
{
    protected ?string $layout = AppLayout::class;

    //...
}
```

<a name="layout-ändern"></a>
### Layout ändern

Beim Entwickeln eines Admin-Panels mit `MoonShine` besteht oft die Notwendigkeit, Templates flexibel zu verwalten. Anstatt zahlreiche separate Templates für verschiedene Situationen zu erstellen, bietet `MoonShine` die Möglichkeit, bestehende Templates dynamisch zu ändern. Dies wird durch die Methode `modifyLayout` erreicht.

Die Methode `modifyLayout` ermöglicht den Zugriff auf das Template nach dessen Erstellung, um erforderliche Änderungen vorzunehmen. Dies ist besonders nützlich, wenn Sie das Template an spezifische Bedingungen anpassen oder dynamische Inhalte hinzufügen müssen.

Beispiel:

```php
/**
 * @param  LoginLayout  $layout
 */
protected function modifyLayout(LayoutContract $layout): LayoutContract
{
    return $layout->title(
        __('moonshine-two-factor::ui.2fa')
    )->description(
        __('moonshine-two-factor::ui.confirm')
    );
}
```

<a name="alias"></a>
## Alias

Falls Sie den Alias einer Seite ändern müssen, kann dies über die Eigenschaft `alias` erfolgen.

```php
class CustomPage extends Page
{
    protected ?string $alias = null;

    //...
}
```

Sie können auch die Methode `getAlias()` überschreiben.

```php
use MoonShine\Pages\Page;

class CustomPage extends Page
{
    public function getAlias(): ?string
    {
        return 'custom_page';
    }

    //...
}
```

<a name="rendering"></a>
## Rendering

Sie können die Seite außerhalb von `MoonShine` anzeigen, indem Sie sie einfach in einem Controller zurückgeben:

```php
class ProfileController extends Controller
{
    public function __invoke(ProfilePage $page): ProfilePage
    {
        return $page->loaded();
    }
}
```

Oder mit Fortify:

```php
Fortify::loginView(static fn() => app(ProfilePage::class));
```

<a name="vor-rendern"></a>
## Vor dem Rendern

Die Methode `prepareBeforeRender()` ermöglicht das Ausführen von Aktionen, bevor die Seite angezeigt wird.

```php
class CustomPage extends Page
{
    protected function prepareBeforeRender(): void
    {
        parent::prepareBeforeRender();

        if (auth()->user()->moonshine_user_role_id !== MoonshineUserRole::DEFAULT_ROLE_ID) {
            abort(403);
        }
    }
}
```

<a name="antwort-modifikation"></a>
## Antwortmodifikation

Standardmäßig wird die Seite über den `PageController` gerendert, der die Methode `render()` aufruft. Es kann jedoch Situationen geben, in denen es erforderlich ist, die Standardantwort zu ändern, z. B. bei einer Weiterleitung unter bestimmten Bedingungen. In solchen Fällen kann die Methode `modifyResponse()` verwendet werden.

```php
protected function modifyResponse(): ?Response
{
    if (request()->has('id')) {
        return redirect()->to('/admin/article-resource/index-page');
    }

    return null;
}
```

<a name="lebenszyklus"></a>
## Lebenszyklus

`Page` bietet mehrere verschiedene Methoden, um in verschiedene Teile ihres Lebenszyklus einzugreifen. Lassen Sie uns diese durchgehen:

<a name="aktive-seite"></a>
### Aktive Seite

Die Methode `onLoad` ermöglicht die Integration zu dem Zeitpunkt, an dem die Seite geladen wird und aktuell aktiv ist.

```php
namespace App\MoonShine\Pages;

use MoonShine\Laravel\Pages\Page;

class PostPage extends Page
{
    // ...
    protected function onLoad(): void
    {
        parent::onLoad();

        //
    }
    // ...
}
```

<a name="instanzinitialisierung"></a>
### Instanzinitialisierung

Die Methode `booted` ermöglicht die Integration zu dem Zeitpunkt, an dem `MoonShine` eine Instanz der Seite im System erstellt.

```php
namespace App\MoonShine\Pages;

use MoonShine\Laravel\Pages\Page;

class PostPage extends Page
{
    // ...
    protected function booted(): void
    {
        parent::booted();

        //
    }
    // ...
}
```

<a name="link-ressource"></a>
## Link von einer Ressource zu einer Seite erstellen

In diesem Beispiel verwenden wir den [ActionButton](/docs/{{version}}/components/action-button) und die Methode [getPageUrl](/docs/{{version}}/model-resource/routes), um einen Link zu einer neuen Seite zu erstellen.

```php
/**
 * @throws Throwable
 */
public function indexButtons(): ListOf
{
    return parent::indexButtons()->add(
        ActionButton::make('Zur benutzerdefinierten Seite',
            url: fn($model) => $this->getPageUrl(
                PostPage::class, params: ['resourceItem' => $model->getKey()]
            ),
        ),
    );
}
```

<a name="assets"></a>
## Assets

```php
protected function onLoad(): void
{
    parent::onLoad();
    
    $this->getAssetManager()
        ->add(Css::make('/css/app.css'))
        ->append(Js::make('/js/app.js'));
}
```