# RelationRepeater

- [Basics](#basics)
- [Field Set](#fields)
- [Vertical Mode](#vertical)
- [Add/Delete](#creatable-removable)
- [Buttons](#buttons)
- [Modifiers](#modify)

---

<a name="basics"></a>
## Basics

The `RelationRepeater` field is designed for working with `HasMany` and `HasOne` relationships. It allows you to create, edit, and delete related records directly from the main model's form.

> [!NOTE]
> The field automatically syncs related records when saving the main model.

To use the field, you need to specify:
- Field Label
- Relationship Name
- Resource for the related model

```php
use MoonShine\Laravel\Fields\Relationships\RelationRepeater;

//...

protected function formFields(): iterable
{
    return [
        RelationRepeater::make('Characteristics', 'characteristics', resource: CharacteristicResource::class)
    ];
}
```

<a name="fields"></a>
## Field Set

By default, the field uses all form fields from the specified resource. However, you can override the field set using the `fields()` method:

```php
use MoonShine\UI\Fields\Text;
use MoonShine\UI\Fields\Switcher;
use MoonShine\Laravel\Fields\Relationships\RelationRepeater;

//...

protected function formFields(): iterable
{
    return [
        RelationRepeater::make('Characteristics', 'characteristics')
            ->fields([
                ID::make(),
                Text::make('Name', 'name'),
                Text::make('Value', 'value'),
                Switcher::make('Active', 'is_active')
            ])
    ];
}
```

> [!WARNING]
> The presence of the `ID` field is required, otherwise records will always be added.

<a name="vertical"></a>
## Vertical Mode

The `vertical()` method changes the display of the table from horizontal mode to vertical:

```php
vertical(Closure|bool|null $condition = null)
```

Example:

```php
RelationRepeater::make('Characteristics', 'characteristics')
    ->vertical()
```

<a name="creatable-removable"></a>
## Add/Delete

By default, the field allows adding new items. This behavior can be changed using the `creatable()` method:

```php
creatable(
    Closure|bool|null $condition = null,
    ?int $limit = null,
    ?ActionButtonContract $button = null
)
```

- `$condition` - the condition under which the method should be applied
- `$limit` - limit on the number of possible elements
- `$button` - ability to replace the adding button with your own

The ability to delete items is handled by the `removable()` method:

```php
removable(
    Closure|bool|null $condition = null,
    array $attributes = []
)
```

Example:

```php
RelationRepeater::make('Characteristics', 'characteristics')
    ->creatable(limit: 5)
    ->removable()
```

<a name="buttons"></a>
## Buttons

The `buttons()` method allows overriding the buttons used in the field:

```php
use MoonShine\UI\Components\ActionButton;

RelationRepeater::make('Characteristics', 'characteristics')
    ->buttons([
        ActionButton::make('', '#')
            ->icon('trash')
            ->onClick(fn() => 'remove()', 'prevent')
            ->class('btn-error')
            ->showInLine()
    ])
```

<a name="modify"></a>
## Modifiers

### Table Modifier

The `modifyTable()` method allows modifying the table (`TableBuilder`):

```php
use MoonShine\Components\TableBuilder;

RelationRepeater::make('Characteristics', 'characteristics')
    ->modifyTable(
        fn(TableBuilder $table, bool $preview) => $table
            ->customAttributes([
                'class' => 'custom-table'
            ])
    )
```

### Remove Button Modifier

The `modifyRemoveButton()` method allows changing the remove button:

```php
use MoonShine\UI\Components\ActionButton;

RelationRepeater::make('Characteristics', 'characteristics')
    ->modifyRemoveButton(
        fn(ActionButton $button) => $button
            ->customAttributes([
                'class' => 'btn-secondary'
            ])
    )
``` 