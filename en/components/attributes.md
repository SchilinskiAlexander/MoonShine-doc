# Component Attributes

- [Adding](#set-attribute)
- [Removing](#remove-attribute)
- [iterable Attributes](#iterable-attributes)
- [Massive Change](#custom-attributes)
- [Merging Values](#merge-attribute)
- [Adding Class](#class)
- [Adding Style](#style)
- [Attributes for Alpine.js](#alpine)
  - [x-data](#x-data)
  - [x-model](#x-model)
  - [x-if](#x-if)
  - [x-show](#x-show)
  - [x-html](#x-html)

___

Components offer a convenient mechanism for managing HTML classes, styles, and other attributes, allowing for more flexibility in configuring their behavior and appearance.

<a name="set-attribute"></a>
## Adding

The method `setAttribute()` adds or alters a component's attribute.

```php
setAttribute(string $name, string|bool $value)
```

- `$name` - the name of the attribute,
- `$value` - the value.

```php
$component->setAttribute('data-id', '123');
```

<a name="remove-attribute"></a>
## Removing

The method `removeAttribute()` removes an attribute by its name.

```php
removeAttribute(string $name)
```

- `$name` - the name of the attribute.

```php
$component->removeAttribute('data-id');
```

<a name="iterable-attributes"></a>
## Iterable Attributes

The method `iterableAttributes` adds attributes necessary for working with iterable components.

```php
iterableAttributes(int $level = 0)
```
- `$level` - the level of nesting.

<a name="custom-attributes"></a>
## Massive Change

The method `customAttributes()` adds or replaces multiple attributes of a component.

```php
customAttributes(array $attributes, bool $override = false)
```

- `$attributes` - an array of attributes to be added,
- `$override` - a key that allows overwriting existing attributes.

```php
$component->customAttributes(['data-role' => 'admin'], true);
```

<a name="merge-attribute"></a>
## Merging Values

The method `mergeAttribute()` merges the value of an attribute with a new value, using the specified separator.

```php
mergeAttribute(string $name, string $value, string $separator = ' ')
```

- `$name` - the name of the attribute,
- `$value` - the value,
- `$separator` - the separator.

```php
$component->mergeAttribute('class', 'new-class');
```

<a name="class"></a>
## Adding Class

The method `class()` adds CSS classes to the attributes of a component.

```php
class(string|array $classes)
```
- `$classes` - classes that need to be added to the component.

```php
$component->class(['btn', 'btn-primary']);
```

<a name="style"></a>
## Adding Style

The method `style` adds CSS styles to the attributes of a component.

```php
style(string|array $styles)
```

```php
$component->style(['color: red']);
```

<a name="alpine"></a>
## Quick Attributes for Alpine.js

For convenient integration with the JavaScript framework `Alpine.js`, methods for setting corresponding attributes are used.

# Alpine.js (Editing...)
- [Description](#description)
    - [x-data](#x-data-link)
    - [x-model](#x-model-link)
    - [x-if](#x-if-link)
    - [x-show](#x-show-link)
    - [x-html](#x-html-link)
---

<a name="description"></a>
## Description

Methods that allow convenient interaction with Alpine.js

<a name="x-data"></a>
### x-data

```php
xData(null|array|string $data = null)
```

Everything in Alpine starts with the directive `x-data`. The `xData` method defines an HTML fragment as an Alpine component and provides reactive data to reference this component.

```php
Div::make([])->xData(['title' => 'Hello world']) // title is a reactive variable inside
```

```php
xDataMethod(string $method, ...$parameters)
```

`x-data` specifying the component and its parameters

```php
Div::make([])->xDataMethod('some-component', 'var', ['foo' => 'bar'])
```

<a name="x-model"></a>
### x-model
`x-model` binds a field to a reactive variable
```php
xModel(?string $column = null)
```
```php
Div::make([
    Text::make('Title')->xModel()
])->xData(['title' => 'Hello world'])

// or

Div::make([
    Text::make('Name')->xModel('title')
])->xData(['title' => 'Hello world'])
```

<a name="x-if"></a>
### x-if
```php
xIf(
    string|Closure $variable,
    ?string $operator = null,
    ?string $value = null,
    bool $wrapper = true
)
```

`x-if` hides a field by removing it from the DOM

```php
Div::make([
    Select::make('Type')->native()->options([1 => 1, 2 => 2])->xModel(),
    Text::make('Title')->xModel()->xIf('type', 1)
])->xData(['title' => 'Hello world', 'type' => 1])

// or

Div::make([
    Select::make('Type')->options([1 => 1, 2 => 2])->xModel(),
    Text::make('Title')->xModel()->xIf(fn() => 'type==2||type.value==2')
])->xData(['title' => 'Hello world', 'type' => 1])

// if you need to hide the field without a container

Div::make([
    Select::make('Type')->native()->options([1 => 1, 2 => 2])->xModel(),
    Text::make('Title')->xModel()->xIf('type', '=', 2, wrapper: false)
])->xData(['title' => 'Hello world', 'type' => 1])
```

<a name="x-show"></a>
### x-show
```php
xShow(
    string|Closure $variable,
    ?string $operator = null,
    ?string $value = null,
    bool $wrapper = true
)
```

`x-show` is the same as `x-if`, but it does not remove the element from the DOM; it only hides it.

```php
xDisplay(string $value, bool $html = true)
```

<a name="x-html"></a>
### x-html

`x-html` outputs the value

```php
Div::make([
    Select::make('Type')
        ->native()
        ->options([
            1 => 'Paid',
            2 => 'Free',
        ])
        ->xModel(),

    Number::make('Cost', 'price')
        ->xModel()
        ->xIf('type', '1'),

    Number::make('Rate', 'rate')
        ->xModel()
        ->xIf('type', '1')
        ->setValue(90),

    LineBreak::make(),

    Div::make()
        ->xShow('type', '1')
        ->xDisplay('"Result:" + (price * rate)')
    ,
])->xData([
    'price' => 0,
    'rate' => 90,
    'type' => '2',
]),
```