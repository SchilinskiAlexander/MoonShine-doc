# MoonShine Upgrade-Anleitung 2.x → 3.0

- [Paketaktualisierung](#aktualisierung)
- [Erste Einrichtung](#installation)
- [Änderungsprotokoll](#änderungen)
  - [Namespaces](#namespaces)
  - [Methoden](#methoden)
  - [Variablen](#variablen)

---

<a name="aktualisierung"></a>
## Paketaktualisierung

### 1. composer.json aktualisieren
Ändern Sie die Paketversion in Ihrer composer.json:

```json
{
    "require": {
        "moonshine/moonshine": "^3.0"
    }
}
```

### 2. Backups erstellen
Erstellen Sie vor dem Update Backups der folgenden Dateien:

```bash
mv config/moonshine.php config/moonshine_alt.php
mv app/Providers/MoonShineServiceProvider.php app/Providers/MoonShineServiceProvider_alt.php
mv app/MoonShine/Pages/Dashboard.php app/MoonShine/Pages/Dashboard_alt.php
```

Diese Dateien werden benötigt, um Konfigurationen und Einstellungen zu übertragen. Siehe den Abschnitt [Änderungsprotokoll](#änderungen).

### 3. Anwendungskonfiguration aktualisieren
Falls Sie Laravel < 11 verwenden, suchen und entfernen Sie `App\Providers\MoonShineServiceProvider::class` aus der `config/app.php`-Konfiguration.

> [!WARNUNG]
> Nach Ausführung des Befehls `moonshine:install` wird der Service-Provider automatisch hinzugefügt.

### 4. Update durchführen

```bash
composer update
```

<a name="installation"></a>
## Erste Einrichtung

### 1. Neue Version installieren
Führen Sie den folgenden Befehl aus:

```bash
php artisan moonshine:install
```

Dieser Befehl erstellt:
- Einen neuen Service-Provider
- Aktualisierte Konfigurationen
- Ein neues Layout
- Ein neues Dashboard

### 2. Einstellungen migrieren
1. Übertragen Sie die Parameter aus der alten Konfiguration (`moonshine_alt.php`) in die neue. Siehe die Dokumentation zur [Konfiguration](/docs/{{version}}/configuration).
2. Die Menüstruktur hat sich in der neuen Version geändert:
- Öffnen Sie `app/MoonShine/Layouts/MoonShineLayout.php`.
- Kopieren Sie das alte Menü aus `MoonShineServiceProvider_alt.php` in die Methode `menu`.
- Entfernen Sie das Präfix `heroicons.outline.` von Symbolen.
- Aktualisieren Sie alle Ressourceninstanzen auf String-Klassen:

   ```php
   // Vorher
   MenuItem::make('Einstellungen', new SettingResource(), 'heroicons.outline.adjustments-vertical')

   // Jetzt
   MenuItem::make('Einstellungen', SettingResource::class, 'adjustments-vertical')
   ```

### 3. Ressourcen und Seiten registrieren
Im neuen `MoonShineServiceProvider.php` müssen Sie alle Ressourcen und Seiten registrieren:

```php
$core->resources([
    MoonShineUserResource::class,
    MoonShineUserRoleResource::class,
    // Fügen Sie alle Ihre Ressourcen hinzu
]);

$core->pages([
    ...$config->getPages(),
    SettingPage::class,
]);
```

Befehle zum Generieren von Listen:

Für Namespaces:
```bash
find app/MoonShine/Resources -type f | sed "s/app/use App/" | sed "s|/|\\|g" | sed "s/.php/;/" | sort
```

Für Ressourcenlisten:
```bash
find app/MoonShine/Resources -type f -exec basename {} \; | sed "s/.php/::class,/" | sort
```

### 4. Dashboard aktualisieren
- Verschieben Sie erforderliche Komponenten aus `Dashboard_alt.php` in das neue `Dashboard.php`.
- Beachten Sie die Änderungen im Abschnitt [Änderungsprotokoll](#änderungen).

### 5. Alte Dateien entfernen
Nach erfolgreicher Migration entfernen Sie:

```bash
# Altes Layout (falls vorhanden)
rm app/MoonShine/MoonShineLayout.php

# Backups von Dateien aus 2.x
rm config/moonshine_alt.php
rm app/Providers/MoonShineServiceProvider_alt.php
rm app/MoonShine/Pages/Dashboard_alt.php
```

<a name="änderungen"></a>
## Änderungsprotokoll

<a name="namespaces"></a>
### Namespaces

#### Hauptänderungen
```
MoonShine\Resources\ → MoonShine\Laravel\Resources\
MoonShine\Fields\Relationships\ → MoonShine\Laravel\Fields\Relationships\
MoonShine\Fields\Slug → MoonShine\Laravel\Fields\Slug
MoonShine\Fields\ → MoonShine\UI\Fields\
MoonShine\Decorations\Block → MoonShine\UI\Components\Layout\Box
MoonShine\Decorations\ → MoonShine\UI\Components\Layout\*
    (einige zu MoonShine\UI\Components\, manuell überprüfen)
MoonShine\Enums\ → MoonShine\Support\Enums\
MoonShine\Pages\ → MoonShine\Laravel\Pages\
MoonShine\Models\ → MoonShine\Laravel\Models\
MoonShine\QueryTags\ → MoonShine\Laravel\QueryTags\
MoonShine\Attributes\ → MoonShine\Support\Attributes\
MoonShine\Components\ → MoonShine\UI\Components\
MoonShine\Metrics\ → MoonShine\UI\Components\Metrics\Wrapped\
MoonShine\ActionButtons\ → MoonShine\UI\Components\
MoonShine\Http\Responses\ → MoonShine\Laravel\Http\Responses\
MoonShine\Http\Controllers\ → MoonShine\Laravel\Http\Controllers\
MoonShine\MoonShineAuth → MoonShine\Laravel\MoonShineAuth
```

#### Zusätzliche Pakete
Falls erforderlich, installieren und aktualisieren Sie Namespaces für:

1. [Import/Export](https://github.com/moonshine-software/import-export):
- `MoonShine\Laravel\Handlers\ExportHandler`
- `MoonShine\Laravel\Handlers\ImportHandler`

2. [Apexcharts](https://github.com/moonshine-software/apexcharts):
- `MoonShine\UI\Components\Metrics\Wrapped\DonutChartMetric`
- `MoonShine\UI\Components\Metrics\Wrapped\LineChartMetric`

3. [Ace Editor](https://github.com/moonshine-software/ace):
- `MoonShine\Fields\Code`

4. [EasyMDE](https://github.com/moonshine-software/easymde):
- `MoonShine\Fields\Markdown`

5. [TinyMce](https://github.com/moonshine-software/tinymce):
- `MoonShine\Fields\TinyMce`

<a name="methoden"></a>
### Methoden

#### Hauptänderungen
1. Erstellen von Instanzen für Ressourcen und Seiten:

```php
// Vorher
new NameResource()

// Jetzt
// Empfohlen über DI
// oder:
app(NameResource::class)
```

2. Methodensignaturen:
```php
// Vorher
public function components(): array
public function title(): string
public function breadcrumbs(): string
public function rules(Model $item): array
protected function afterUpdated(Model $user): Model
public function detailButtons(): array
public function modifyListComponent(MoonShineRenderable|TableBuilder $table): MoonShineRenderable
$field->getData()
detailPageUrl
MoonShineAuth::guard()
getActiveActions()

// Jetzt
protected function components(): iterable
public function getTitle(): string
public function getBreadcrumbs(): string
protected function rules($item): array
protected function afterUpdated($user): Model
public function detailButtons(): ListOf
public function modifyListComponent(ComponentContract $table): ComponentContract
$field->getData()->getOriginal()
getDetailPageUrl
MoonShineAuth::getGuard()
activeActions()
```

3. Änderungen in Feldmethoden:
```php
// Vorher
public function fields(): array

// Jetzt
protected function indexFields(): iterable // akzeptiert nur Felder
protected function detailFields(): iterable
protected function formFields(): iterable
```

4. Tabellenattribute:
```php
// Neues Format
TableBuilder::make()
    ->tdAttributes(fn(mixed $data, int $row, TableBuilder $table): array =>
        $row === 3 ? ['class' => 'bgc-yellow'] : []
    )
    ->tdAttributes(fn(mixed $data, int $row, int $cell, TableBuilder $table): array =>
        $cell === 3 ? ['align' => 'right'] : []
    )
```

5. Änderungen in anderen Methoden:
- Helper `to_page` → `toPage`
- Anstelle der Methode `columnSpan` in Komponenten verwenden Sie die Komponente `Column`: `Column::make([...])->columnSpan(..)`

#### Entfernte Methoden
1. Methoden zur Anzeige von Feldern:
- hideOnIndex, showOnIndex
- hideOnForm, showOnForm
- hideOnCreate, showOnCreate
- hideOnUpdate, showOnUpdate
- hideOnDetail, showOnDetail
- hideOnAll
- hideOnExport, showOnExport
- useOnImport (verwenden Sie das [import-export](https://github.com/moonshine-software/import-export)-Paket)

2. Helfer:
- form
- table
- actionBtn

<a name="variablen"></a>
### Variablen

#### Hauptänderungen
1. Symbole:
- Entfernen Sie die Präfixe `heroicons.outline` und `heroicons.solid`.
- Diese Symbole sind jetzt standardmäßig verfügbar.

2. Menü:

```php
// Vorher
MenuItem::make('Einstellungen', new SettingResource(), 'heroicons.outline.adjustments-vertical')

// Jetzt
MenuItem::make('Einstellungen', SettingResource::class, 'adjustments-vertical')
```

3. Asynchrone Ereignisse:
```php
// Vorher
->async(asyncUrl: ..., asyncEvents: ...)
'table-updated-{name}'

// Jetzt
->async(url: ..., events: ...)
AlpineJs::event(JsEvent::TABLE_UPDATED, {name})
```

4. Sortierrichtung:
```php
// Vorher
protected string $sortDirection = 'ASC';

// Jetzt
protected SortDirection $sortDirection = SortDirection::ASC;
```

5. Assets:
```php
// Vorher
$assets // Strings

// Jetzt
$assets // akzeptiert AssetElementContract, wie Css, InlineCss, Js, InlineJs
Für die Verwaltung verwenden Sie [AssetManager](/docs/{{version}}/appearance/assets).
```

#### Entfernte Variablen
- `protected bool $isAsync = true;` (jetzt standardmäßig aktiviert)
