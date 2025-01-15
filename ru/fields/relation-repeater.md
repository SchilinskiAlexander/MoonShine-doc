# RelationRepeater

- [Основы](#basics)
- [Набор полей](#fields)
- [Вертикальный режим](#vertical)
- [Добавление/Удаление](#creatable-removable)
- [Кнопки](#buttons)
- [Модификаторы](#modify)

---

<a name="basics"></a>
## Основы

Поле `RelationRepeater` предназначено для работы с отношениями `HasMany` и `HasOne`. Оно позволяет создавать, редактировать и удалять связанные записи прямо из формы основной модели.

> [!NOTE]
> Поле автоматически синхронизирует связанные записи при сохранении основной модели.

Для работы с полем необходимо указать:
- Label поля
- Имя отношения
- Ресурс для связанной модели

```php
use MoonShine\Laravel\Fields\Relationships\RelationRepeater;

//...

protected function formFields(): iterable
{
    return [
        RelationRepeater::make('Характеристики', 'characteristics', resource: CharacteristicResource::class)
    ];
}
```

<a name="fields"></a>
## Набор полей

По умолчанию поле использует все поля формы из указанного ресурса. Однако вы можете переопределить набор полей с помощью метода `fields()`:

```php
use MoonShine\UI\Fields\Text;
use MoonShine\UI\Fields\Switcher;
use MoonShine\Laravel\Fields\Relationships\RelationRepeater;

//...

protected function formFields(): iterable
{
    return [
        RelationRepeater::make('Характеристики', 'characteristics')
            ->fields([
                ID::make(),
                Text::make('Название', 'name'),
                Text::make('Значение', 'value'),
                Switcher::make('Активно', 'is_active')
            ])
    ];
}
```

> [!WARNING]
> Наличие поля `ID` обязательно, иначе записи будут всегда добавляться.

<a name="vertical"></a>
## Вертикальный режим

Метод `vertical()` изменяет отображение таблицы из горизонтального режима на вертикальный:

```php
vertical(Closure|bool|null $condition = null)
```

Пример:

```php
RelationRepeater::make('Характеристики', 'characteristics')
    ->vertical()
```

<a name="creatable-removable"></a>
## Добавление/Удаление

По умолчанию поле позволяет добавлять новые элементы. Это поведение можно изменить с помощью метода `creatable()`:

```php
creatable(
    Closure|bool|null $condition = null,
    ?int $limit = null,
    ?ActionButtonContract $button = null
)
```

- `$condition` - условие, при котором метод должен быть применён
- `$limit` - ограничение на количество возможных элементов
- `$button` - возможность заменить кнопку добавления на свою

Для возможности удаления элементов используется метод `removable()`:

```php
removable(
    Closure|bool|null $condition = null,
    array $attributes = []
)
```

Пример:

```php
RelationRepeater::make('Характеристики', 'characteristics')
    ->creatable(limit: 5)
    ->removable()
```

<a name="buttons"></a>
## Кнопки

Метод `buttons()` позволяет переопределить кнопки, используемые в поле:

```php
use MoonShine\UI\Components\ActionButton;

RelationRepeater::make('Характеристики', 'characteristics')
    ->buttons([
        ActionButton::make('', '#')
            ->icon('trash')
            ->onClick(fn() => 'remove()', 'prevent')
            ->class('btn-error')
            ->showInLine()
    ])
```

<a name="modify"></a>
## Модификаторы

### Модификатор таблицы

Метод `modifyTable()` позволяет модифицировать таблицу (`TableBuilder`):

```php
use MoonShine\Components\TableBuilder;

RelationRepeater::make('Характеристики', 'characteristics')
    ->modifyTable(
        fn(TableBuilder $table, bool $preview) => $table
            ->customAttributes([
                'class' => 'custom-table'
            ])
    )
```

### Модификатор кнопки удаления

Метод `modifyRemoveButton()` позволяет изменить кнопку удаления:

```php
use MoonShine\UI\Components\ActionButton;

RelationRepeater::make('Характеристики', 'characteristics')
    ->modifyRemoveButton(
        fn(ActionButton $button) => $button
            ->customAttributes([
                'class' => 'btn-secondary'
            ])
    )
```