# Иерархические Active Record SEO категории (или теги) под Yii2

[English version](../README.md)

## Содержание

* [Цель](#goal)
* [Установка](#installing)
* [Настройки модуля](#settings)



---

## Цель <span id="goal"></span>

Данное расширение предоставляет **вариацию** [модуля категорий](https://github.com/mgrechanik/yii2-categories-and-tags), 
в котором давалась [возможность](https://github.com/mgrechanik/yii2-categories-and-tags/blob/master/docs/README_ru.md#custom-ar) создавать любые свои ```Active Record``` модели категорий.

Мы предполагаем что создавая на **frontend** страницы с выводом содержимого категории (или тега) нам для
такой страницы категории потребуется управление ее SEO информацией.

Соответственно в нашу AR модель SEO категории мы добавляем следующие поля:

* ```name``` для обозначения названия категории
* ```title``` для содержимого тега ```<title>```
* ```meta_description``` для значения атрибута ```content``` тега ```<meta name="description">```	
* ```meta_keywords``` для значения атрибута ```content``` тега ```<meta name="keywords">```	
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
- первая создает таблицу SEO категорий со всеми необходимыми индексами
- вторая создает уникальный индекс по полю ```slug```

Вы можете выполнить их обе:

```
php yii migrate --migrationPath=@vendor/mgrechanik/yii2-seo-categories/src/console/migrations
```

, или если вы не используете поле ```slug``` выполните из них только первую:

```
php yii migrate 1 --migrationPath=@vendor/mgrechanik/yii2-seo-categories/src/console/migrations
```

#### Подключение модуля  <span id="setup"></span>

Как говорилось [в базом модуле категорий](https://github.com/mgrechanik/yii2-categories-and-tags/blob/master/docs/README_ru.md#goal), данный модуль следует структуре *универсального модуля* и предоставляет при этом
только страницы **backend**-а, то при его подключении укажите следующий режим (```mode```):
```php
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

[Подключяя](#setup) модуль в приложение мы, помимо всех св-в [модуля категорий](https://github.com/mgrechanik/yii2-categories-and-tags/blob/master/docs/README_ru.md#settings),
 можем воспользоваться следующими его свойствами:

#### ```$useMetaOtherField = false``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Использовать ли поле ввода всех прочих мета тегов

#### ```$useSlugField = true``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Использовать ли поле ввода "хвостовика адреса". Он предполагается быть уникальным

#### ```$slugPattern``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- При использовании предыдущего поля здесь указываем регулярку допустимых символов 

#### ```$showTitleColumnAtIndexPage = true```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Показывать ли поле ```title``` в гриде списка категорий

#### ```$showSlugColumnAtIndexPage = false``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Показывать ли поле ```slug``` в гриде списка категорий

