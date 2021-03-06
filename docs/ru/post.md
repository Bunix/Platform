# Записи
----------
Платформа предполагает, что по умолчанию любые элементы, содержащие данные сайта, являются моделью `Post`.
Такая структура подходит для большинства публичных веб-сайтов, так как их структура очень сильно похожа.
Как пример:
- Новости,
- Акции,
- Страницы,
- Вакансии

Вы можете придумать сотни вариаций. Для того, что бы не использовать почти одинаковые модели
и миграции ORCHID использует базовую модель `Post`. Она использует JSON тип для колонок, что 
гораздо удобнее и проще, чем EAV формат, кроме того, это позволяет делать перевод. 
Некоторые подходы мы специально воспроизвели с WordPress для Laravel, что позволит вам быть эффективнее.


Итак, теперь вы можете получить данные базы данных:

```php
use Orchid\CMS\Core\Models\Post;

$posts = Post::all();
```

```php
// Все опубликованные записи
$posts = Post::published()->get();
$posts = Post::status('publish')->get();

// Конкретная запись
$post = Post::find(42);

//Название записи с учетом текущей локализации
echo $post->getContent('name');

```


### Наследование одиночной таблицы

Если вы решили создать новый класс для своего настраиваемого типа сообщений, вы можете вернуть этот класс для всех экземпляров этого типа сообщений.

Определение поведения записи основано на указанном `типе`.
```php
//Все объекты в коллекции $videos будут экземплярами Post
$videos = Post::type('video')->status('publish')->get();
```


### Таксонометрия

Вы можете получить таксонометрию для определенной записи, например:

```php
$post = Post::find(42);
$taxonomy = $post->taxonomies()->first();
echo $taxonomy->taxonomy;
```

Или вы можете искать записи, используя свои таксонометрию:

```php
$post = Post::taxonomy('category', 'php')->first();
```

### Категории и Таксонометрия

Получите категорию или таксономию или загрузите сообщения из определенной категории. Существует несколько способов
Для его достижения.


```php
// все категории
$category = Taxonomy::category()->slug('uncategorized')->posts()->first();


// Только все категории и записи, связанные с ним
$category = Taxonomy::where('taxonomy', 'category')->with('posts')->get();
$category->each(function($category) {
    echo $category->getContent('name');
});

// все записи из категории
$category = Category::slug('uncategorized')->posts()->first();
$category->posts->each(function($post) {
    echo $post->getContent('name');
});
```

### Вложения

Вложения - это файлы, относящиеся к записи.
Эти файлы могут быть разных форматов и разрешений.
Каждый формат можно вызывать отдельно, например, принимать только изображения или только документы в записи.

```php
$item = Post::find(42);
$item->attachment('image')->get();
```

Загруженные изображения автоматически присваиваются разрешениям, указанным в `config/cms`.
Чтобы вызвать их, вы можете использовать ранее указанный ключ.
Если изображения для этого ключа не найдены, исходный файл будет возвращен.

```php
$image = $item->attachment('image')->fisrt();

//Возвращает общий адрес изображения с требуемым разрешением изображения
$image->url('standart');
```
