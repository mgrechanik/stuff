# Иерархические SEO категории (или теги) из Active Record моделей под Yii2

[English version](../README.md)

## Содержание

* [Цель](#goal)
* [Установка](#installing)
* [Настройки модуля](#settings)



---

## Цель <span id="goal"></span>

Данное расширение предоставляет **вариацию** [модуля категорий](https://github.com/mgrechanik/yii2-categories-and-tags), 
в котором давалась возможность создавать любые свои ```Active Record``` модели категорий.

Мы предполагаем что создавая на **frontend** страницы с выводом содержимого категории(или тега) нам для
такой страницы категории потребуется управление ее SEO информацией.

Соответственно в нашу модель SEO категории мы добавляем следующие поля:

* ```name``` для обозначения названия категории
* ```title``` для содержимого тега ```<title>```
* ```meta_description``` для содержимого атрибута ```content``` тега ```<meta name="description">```	
* ```meta_keywords``` для содержимого атрибута ```content``` тега ```<meta name="keywords">```	
* ```meta_other``` для вставки любого ```html``` с метатегами, которые вам дополнительно нужны
* ```slug``` служит для управления "хвостовиком" адреса страницы
	
> При этом в настройках модуля вы можете указать не использовать поля 	```meta_other```  
> или ```slug``` - они не будут появляться в форме создания/редактирования SEO категории

---

    
## Установка <span id="installing"></span>

#### Установка через composer:

Выполните
```
composer require --prefer-dist mgrechanik/yii2-seo-categories
```

или добавьте
```
"mgrechanik/yii2-seo-categories" : "~1.0.0"
```
в  `require` секцию вашего `composer.json` файла.

#### Миграции

Данное расширение идет с двумя миграциями:
- первая создает таблицу seo каталога со всеми необходимыми индексами
- вторая создает уникальный индекс по полю ```slug```

Вы можете их обе выполнить:

```
php yii migrate --migrationPath=@vendor/mgrechanik/yii2-seo-categories/src/console/migrations
```

, или если вы не используете поле ```slug``` выполните из них только первую:

```
php yii migrate 1 --migrationPath=@vendor/mgrechanik/yii2-seo-categories/src/console/migrations
```

#### Подключение модуля  <span id="setup"></span>

Как говорилось [выше](#goal), данный модуль следует структуре *универсального модуля* и предоставляет при этом
только страницы **backend**-а, то при его подключении укажите следующий режим (```mode```):
```
    'modules' => [
        'seocategory' => [
            'class' => 'mgrechanik\yii2seocategory\Module',
            'mode' => 'backend',
            // Другие настройки модуля
        ],
        // ...
    ],
```

Все. При переходе по адресу ```/seocategory``` вы будете видеть весь ваш древовидный список SEO категорий.

---

## Настройки модуля <span id="settings"></span>

[Подключяя](#setup) модуль в приложение мы можем воспользоваться следующими его свойствами:

#### ```$categoryModelClass``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Какой класс AR модели категории использовать

#### ```$categoryFormModelClass``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Какой класс модели формы использовать

#### ```$indentedNameCreatorCallback``` <span id="indented-name">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Callback, который сформирует название категории на странице всего
списка категорий с учетом отступа, чтобы отображалось как дерево 

#### ```$categoryIndexView```, ```$categoryCreateView```, ```$categoryUpdateView```, ```$categoryFormView```, ```$categoryViewView``` <span id="setup-views"></span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- указывают соответствующие **views**, которые модуль будет использовать. 
Формат смотрите в [документации](https://www.yiiframework.com/doc/api/2.0/yii-base-view#render()-detail)

#### ```$redirectToIndexAfterCreate``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Редиректить ли на страницу всех категорий после создания нового элемента.  
```True``` по умолчанию. При ```false``` будет редиректить на страницу просмотра категории

#### ```$redirectToIndexAfterUpdate``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Аналогично предыдущему пункту но для задачи редактирования

#### ```$validateCategoryModel``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Валидировать ли модель категории перед сохранением.  
По умолчанию ```false``` когда считается что из формы приходят уже валидные данные, ею проверенные

#### ```$creatingSuccessMessage```, ```$updatingSuccessMessage```, ```$deletingSuccessMessage``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Тексты flash сообщений.  
Если их менять, то не забудьте обеспечить их переводы в источнике ```yii2category```


---

## Пример вывода списка категорий на frontend <span id="frontend-output"></span>

Если вам теперь нужно это все дерево категорий вывести в любой шаблон, выполняем:
```php
use mgrechanik\yiimaterializedpath\ServiceInterface;
// Эта наша дефолтная AR модель категории:
use mgrechanik\yii2category\models\Category;
use mgrechanik\yiimaterializedpath\widgets\TreeToListWidget;

// получаем сервис управления деревьями
$service = \Yii::createObject(ServiceInterface::class);
// Получаем элемент относительно которого строим дерево.
// В данном случае это корневой элемент
$root = $service->getRoot(Category::class);
// Строим дерево из потомков корневого узла
$tree = $service->buildDescendantsTree($root);
// Выводим на странице
print TreeToListWidget::widget(['tree' => $tree]);
```
*Получим следующее дерево:*
<ul>
<li>Laptops &amp; PC<ul>
<li>Laptops</li>
<li>PC</li>
</ul></li>
<li>Phones &amp; Accessories<ul>
<li>Smartphones<ul>
<li>Android</li>
<li>iOS</li>
</ul></li>
<li>Batteries</li>
</ul></li>
</ul>

