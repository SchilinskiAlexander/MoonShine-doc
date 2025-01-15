# Thumbnails

Для создания миниатюр можно использовать компонент `Thumbnails`.

Вы можете создать `Thumbnails`, используя статический метод `make()`.

```php
make(FileItem|string|array|null $items)
```

- $items - миниатюра или список миниатюр.

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

Можно указывать только одну миниатюру

```blade
<x-moonshine::thumbnails :items="/images/thumb_1.jpg"/>
```

Вы также можете указать атрибут `alt`.

```php
<x-moonshine::thumbnails :items="'/images/thumb_1.jpg'" alt="Description"/>
```