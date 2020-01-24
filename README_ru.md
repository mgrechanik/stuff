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

## Демо <span id="demo"></span>

Функционал **backend** части будет выглядеть так (если вывод на английском):
![получившийся функционал списка категорий](https://raw.githubusercontent.com/mgrechanik/yii2-categories-and-tags/master/docs/images/categories.png "Функционал дерева категорий")
	
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

Если вам требуется поле ```slug``` то таблицу и индекс для этого поля
вы можете создать выполнив:

```
php yii migrate --migrationPath=@vendor/mgrechanik/yii2-seo-categories/src/console/migrations
```

Если же поле ```slug``` вам не требуется, вам нужно выполнить только первую из миграций из папки следующим образом:

```
php yii migrate 1 --migrationPath=@vendor/mgrechanik/yii2-seo-categories/src/console/migrations
```

#### Подключение модуля  <span id="setup"></span>

Как говорилось [выше](#goal), данный модуль следует структуре *универсального модуля* и предоставляет при этом
только страницы **backend**-а, то при его подключении укажите следующий режим (```mode```):
```
    'modules' => [
        'category' => [
            'class' => 'mgrechanik\yii2category\Module',
            'mode' => 'backend',
            // Другие настройки модуля
        ],
        // ...
    ],
```

Все. При переходе по адресу ```/category``` вы будете видеть весь ваш древовидный список категорий.

---

## Дефолтная AR модель категории данного расширения  <span id="default-ar"></span> 

**Обязательными** полями для модели категории являются ```id, path, level, weight ``` (`id` при этом - первичный ключ), 
они нужны для хранения позиции в дереве. Остальные поля - уже те, которые требуются вам.

Если вам достаточно только одного дополнительного текстового поля - ```name``` - то для этого в расширении имеется
модель [Category](https://github.com/mgrechanik/yii2-categories-and-tags/blob/master/src/models/Category.php), которая установлена моделью по умолчанию данного модуля.

Именно работа с ней показана на [демо](#demo) выше.


---

## Использование своей AR модели  <span id="custom-ar"></span>   

Если вам недостаточно одного дополнительного поля имени, предоставленного [дефолтной](#default-ar) моделью,
имеется возможность создать свою модель, с нужными вам полями, и указать ее как модель категории.

Для того чтобы все это сделать, вам нужно проделать следующие шаги:

#### А) Настройка своей AR модели <span id="custom-ar-a"></span>

1) Сгенерируйте класс вашей AR модели, из таблицы, для которой взята за основу [миграция для модели Category](https://github.com/mgrechanik/yii2-categories-and-tags/blob/master/src/console/migrations/m180908_094405_create_category_table.php), главное тут - [обязательные](#default-ar) поля

2) Измените код вашей модели полностью идентично как мы сделали для [Category](https://github.com/mgrechanik/yii2-categories-and-tags/blob/master/src/models/Category.php) модели: 
* указать имя таблицы
* указать наследование от ```BaseCategory```
* указать ваши дополнительные поля в ```rules(), attributeLabels()```

3) Укажите данному модулю использовать этот класс модели, через его св-во ```$categoryModelClass```

4) Если у вашей модели нет имени ```name``` то настройте свойство модуля - [```$indentedNameCreatorCallback```](#indented-name)

#### B) Настройка своей модели формы <span id="custom-ar-b"></span>

AR модель и форма у нас не смешаны, поэтому действия похожие на **A)** должны быть произведены и над моделью формы.

1) Создайте свою модель формы, взяв полностью как пример модель [CategoryForm](https://github.com/mgrechanik/yii2-categories-and-tags/blob/master/src/ui/forms/backend/CategoryForm.php). 
В ней мы добавили одно поле - ```name``` - а вы укажите ваши. Не забудьте про наследование от ```BaseCategoryForm```

2) Укажите данному модулю использовать этот класс модели формы, через его св-во ```$categoryFormModelClass```

#### C) Настройка views <span id="custom-ar-c"></span>

Данный модуль имеет возможность настроить [какие views использовать](#setup-views).

Вот те из них, которые несут дополнительную информацию, скопируйте, измените под вашу модель, и укажите модулю.

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

