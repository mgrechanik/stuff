# Hierarchical catalog of Active Record models for Yii2 framework

[Русская версия](docs/README_ru.md)

## Table of contents

* [Goal](#goal)
* [Demo](#demo)
* [Installing](#installing)
* [Default AR catalog model of this extension](#default-ar)
* [Использование своей AR модели](#custom-ar)
* [Настройки модуля](#settings)
* [Пример вывода каталога на frontend](#frontend-output)



---

## Goal <span id="goal"></span>

This extension gives you the module with the next functionality:

1. It connects ```Active Record``` models of one table to a tree according to ```Materialized path``` algorithm using [this](https://github.com/mgrechanik/yii2-materialized-path) extension
2. You can use your own ```ActiveRecord``` model with fields you need by inheriting it from base catalog model of this extension. [Details](#custom-ar)
3. This module follows approach of [universal module](https://github.com/mgrechanik/yii2-universal-module-sceleton)
4. In fact you will have a set of ```Active Record``` models connected into a tree with ```CRUD``` operations with them at **backend** section

    * This module gives no **frontend** section since we are not aware of what will be put into catalog
    * It will also fit to serve for **tags system** (if they are organized hierarchically)	
	* Functionality of ```CRUD``` pages provides a possibility to set up/change a position of each node in the tree to any valid position
	* The futher work with a catalog tree is meant by using [Materialized path](https://github.com/mgrechanik/yii2-materialized-path) extension **!** [Example](#frontend-output)
	* The index page of viewing a catalog tree assumes that **all** catalog needs to be displayed, without pagination or filtering

---

## Demo <span id="demo"></span>

The functionality of **backend** section will look like:
![Functionality of catalog we get](https://raw.githubusercontent.com/mgrechanik/yii2-materialized-path/master/docs/images/catalog.png "Catalog functionality")
	
---
    
## Installing <span id="installing"></span>

#### Installing through composer:

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).:

Either run
```
composer require --prefer-dist mgrechanik/yii2-catalog
```

or add
```
"mgrechanik/yii2-catalog" : "~1.0.0"
```
to the require section of your `composer.json`

#### Migrations

If you do not need additional fields to catalog ```Active Record``` model ([details](#custom-ar)) then the table for [default](#default-ar) catalog
you can create by running:

```
php yii migrate --migrationPath=@vendor/mgrechanik/yii2-catalog/src/console/migrations
```

#### Setting the module up  <span id="setup"></span>

As was mentioned [before](#goal) this module follows the approach of *universal module*, and since it gives you
only **backend** pages when you set up it into your application specify the next ```mode``` :
```
    'modules' => [
        'catalog' => [
            'class' => 'mgrechanik\yii2catalog\Module',
            'mode' => 'backend',
            // Other module settings
        ],
        // ...
    ],
```

Done. When you access ```/catalog``` page you will see all your catalog.

---

## Default AR catalog model of this extension  <span id="default-ar"></span> 

The **required** fields for catalog model are ```id, path, level, weight ``` (`id` is the **primary key**), 
they serve to saving tree position. The rest fields are ones you need.

If you are satisfied with only one additional text field - ```name``` - then this extension provides
[Catalog](#потом) model which is set as default catalog model of the mmodule.

The work precisely with it is shown at [demo](#demo) above.


---

## Использование своей AR модели  <span id="custom-ar"></span>   

Если вам недостаточно одного дополнительного поля имени, предоставленного [дефолтной](#default-ar) моделью,
имеется возможность создать свою модель, с нужными вам полями, и указать ее как модель каталога.

Для того чтобы все это сделать, вам нужно проделать следующие шаги:

#### А) Настройка своей AR модели <span id="custom-ar-a"></span>

1) Сгенерируйте класс вашей AR модели, взяв за основу [миграцию для модели Catalog](#потом), главно тут - [обязательные](#default-ar) поля.

2) Измените код вашей модели полностью идентично как мы сделали для [Catalog](#потом) модели: 
* указать имя таблицы
* указать ваши дополнительные поля в ```rules(), attributeLabels()```

3) Укажите данному модулю использовать этот класс модели , через его св-во ```$catalogModelClass```

4) Если у вашей модели нет имени ```name``` то настройте свойство модуля - [```$indentedNameCreatorCallback```](#indented-name)

#### B) Настройка своей модели формы <span id="custom-ar-b"></span>

AR модель и форма у нас не смешаны, поэтому действия похожие на **A)** должны быть произведены и над моделью формы.

1) Создайте свою модель формы, взяв полностью как пример модель [CatalogForm](#потом). 
В ней мы добавили одно поле - ```name``` - а вы укажите ваши. Не забудьте про наследование от ```BaseCatalogForm```.

2) Укажите данному модулю использовать этот класс модели формы, через его св-во ```$catalogFormModelClass```

#### C) Настройка views <span id="custom-ar-c"></span>

Данный модуль имеет возможность настроить [какие views использовать](#setup-views).

Вот те из них, которые несут дополнительную информацию, скопируйте, измените под вашу модель, и укажите модулю.

---

## Настройки модуля <span id="settings"></span>

[Подключяя](#setup) модуль в приложение мы можем воспользоваться следующими его свойствами:

#### ```$catalogModelClass``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Какой класс AR модели каталога использовать

#### ```$catalogFormModelClass``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Какой класс модели формы использовать

#### ```$indentedNameCreatorCallback``` <span id="indented-name">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Callback, который сформирует название пункта каталога на странице всего
каталога с учетом отступа, чтобы отображалось как дерево 

#### ```$catalogIndexView```, ```$catalogCreateView```, ```$catalogUpdateView```, ```$catalogFormView```, ```$catalogViewView``` <span id="setup-views"></span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- указывают соответствующие **views**, которые модуль будет использовать. 
Формат смотрите в [документации](https://www.yiiframework.com/doc/api/2.0/yii-base-view#render()-detail). 

#### ```$redirectToIndexAfterCreate``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Редиректить ли на страницу каталога после создания нового элемента.  
```True``` по умолчанию. При ```false``` будет редиректить на страницу просмотра элемента каталога.

#### ```$redirectToIndexAfterUpdate``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Аналогично предыдущему пункту но для задачи редактирования..

#### ```$validateCatalogModel``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Валидировать ли модель каталога перед сохранением.  
По умолчанию ```false``` когда считается что из формы приходят уже валидные данные, ею проверенные.

#### ```$creatingSuccessMessage```, ```$updatingSuccessMessage```, ```$deletingSuccessMessage``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- тексты flash сообщений.  
Если их менять, то не забудьте обеспечить их переводы в источнике ```yii2catalog```


---

## Пример вывода каталога на frontend <span id="frontend-output"></span>

Если вам теперь нужно это все дерево каталога вывести в любой шаблон, выполняем:
```php
use mgrechanik\yiimaterializedpath\ServiceInterface;
// Эта наша дефолтная AR модель каталога:
use mgrechanik\yii2catalog\models\Catalog;
use mgrechanik\yiimaterializedpath\widgets\TreeToListWidget;

// получаем сервис управления деревьями
$service = \Yii::createObject(ServiceInterface::class);
// Получаем элемент относительно которого строим дерево.
// В данном случае это корневой элемент
$root = $service->getRoot(Catalog::class);
// Строим дерево из потомков корневого узла
$tree = $service->buildDescendantsTree($root);
// Выводим на странице
print TreeToListWidget::widget(['tree' => $tree]);
```
Получим следующее дерево:
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

