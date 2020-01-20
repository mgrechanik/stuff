# Hierarchical catalog of Active Record models for Yii2 framework

[Русская версия](docs/README_ru.md)

## Table of contents

* [Goal](#goal)
* [Demo](#demo)
* [Installing](#installing)
* [Дефолтная AR модель каталога данного расширения](#default-ar)
* [Использование своей AR модели](#custom-ar)
* [Настройки модуля](#settings)
* [Пример вывода каталога на frontend](#frontend-output)



---

## Goal <span id="goal"></span>

This extension gives you the module with the next functionality:

1. It connects ```Active Record``` models of one table to a tree according to ```Materialized path``` algorithm using [this](https://github.com/mgrechanik/yii2-materialized-path) extension
2. You can use your own ```ActiveRecord``` model with fiels you need by inheriting it from base catalog model of this extension. [Details](#custom-ar)
3. This module follows approach of [universal module](https://github.com/mgrechanik/yii2-universal-module-sceleton)
4. In fact you will have a set of ```Active Record``` models connected into a tree with ```CRUD``` operations with them at **backend** section

    * This module gives no **frontend** section since we are not aware of what will be put into catalog
    * It will also fit to serve for **tags system** (if they are organized hierarchically)	
	* Functionality of ```CRUD``` pages provides a possibility to set up/change a position of each node in the tree to any valid position
	* The futher work with a catalog tree is meant by using [Materialized path](https://github.com/mgrechanik/yii2-materialized-path) extension **!** [Example](#frontend-output)
	* The index page of viewing a catalog tree assumes that **all** catalog needs to be displayed, without pagination or filtering

---

## Demo <span id="demo"></span>

The functionality of **backend** section will look like):
![получившийся функционал каталога](https://raw.githubusercontent.com/mgrechanik/yii2-materialized-path/master/docs/images/catalog.png "Функционал каталога")
	
---
    
## Installing <span id="installing"></span>

#### Установка через composer:

Выполните
```
composer require --prefer-dist mgrechanik/yii2-catalog
```

или добавьте
```
"mgrechanik/yii2-catalog" : "~1.0.0"
```
в  `require` секцию вашего `composer.json` файла.

#### Миграции

Если вам не требуются дополнительные поля для ```Active Record``` модели ([подробнее](#custom-ar)), то таблицу для [дефолтного](#default-ar) каталога
вы можете создать выполнив:

```
php yii migrate --migrationPath=@vendor/mgrechanik/yii2-catalog/src/console/migrations
```

#### Подключение модуля  <span id="setup"></span>

Как говорилось [выше](#goal), данный модуль следует структуре *универсального модуля* и предоставляет при этом
только страницы **backend**-а, то при его подключении укажите следующий режим (```mode```):
```
    'modules' => [
        'catalog' => [
            'class' => 'mgrechanik\yii2catalog\Module',
            'mode' => 'backend',
            // Другие настройки модуля
        ],
        // ...
    ],
```

Все. При переходе по адресу ```/catalog``` вы будете видеть весь ваш каталог.

---

## Дефолтная AR модель каталога данного расширения  <span id="default-ar"></span> 

**Обязательными** полями для модели каталога являются ```id, path, level, weight ``` (`id` при этом - первичный ключ), 
они нужны для хранения позиции в дереве, . Остальные поля - уже те, которые требуются вам.

Если вам достаточно только одного дополнительного поля - ```name``` - то для этого в расширении имеется
модель [Catalog](#потом), которая установлена моделью по умолчанию данного модуля.

Именно работа с ней показана на [демо](#demo) выше.


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
