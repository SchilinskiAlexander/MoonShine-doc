# Thumbnails

To create thumbnails, you can use the `Thumbnails` component.

You can create `Thumbnails` using the static method `make()`.

```php
make(FileItem|string|array|null $items)
```

- $items - thumbnail or a list of thumbnails.

~~~tabs
tab: Class
```php
use MoonShine\UI\Components\Thumbnails;

Thumbnails::make([
    '/images/image_1.jpg',
    '/images/image_2.jpg',
    '/images/image_3.jpg',
]),
```
tab: Blade
```blade
<x-moonshine::thumbnails :items="[   
    '/images/image_1.jpg',
    '/images/image_2.jpg',
    '/images/image_3.jpg',
]"/>
```
~~~ 

You can specify only one thumbnail

```blade
<x-moonshine::thumbnails :items="'/images/thumb_1.jpg'"/>
```

You can also specify the `alt` attribute.

```php
<x-moonshine::thumbnails :items="/images/thumb_1.jpg" alt="Description"/>
```