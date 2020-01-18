# Иерархический каталог из Active Record моделей под Yii2

[English version](../README.md)

## Содержание

* [Цель](#goal)
* [Демо](#demo)
* [Установка](#installing)
* [Дефолтная AR модель каталога данного расширения](#default-ar)
* [Использование своей AR модели](#custom-ar)
* [Настройки модуля](#settings)
* [Пример вывода каталога на frontend](#example-basic)



---

## Цель <span id="goal"></span>

Данное расширение предоставляет модуль со следующим функционалом:

1. Объединяет ActiveRecord модели одной таблицы в дерево по алгоритму Materialized path, с помощью [данного](https://github.com/mgrechanik/yii2-materialized-path) расширения
2. Вы можете использовать свою ActiveRecord модель, с нужными вам полями, унаследовав ее от базовой модели данного расширения. [Подробнее](#AAAAAAA)
3. Данный модуль следует структуре [универсального модуля](https://github.com/mgrechanik/yii2-universal-module-sceleton)
4. По сути вы получите набор ActiveRecord моделей, организованных в дерево, и CRUD над ними в backend части

    * Модуль не предоставляет функционала frontend-a, т.к. мы не знаем что будет помещаться в каталог
    * Также подойдет он для организации хранения **системы тегов** (если они организованы иерархически)	
	* Функционал CRUD страниц обеспечивает возможность указания/изменения позиции узла в дереве на любую допустимую
	* Дальнейшая работа с таким деревом предполагает использование возможностей [Materialized path](https://github.com/mgrechanik/yii2-materialized-path) расширения
	* Индексная страница просмотра каталога предполагает вывод **всего** каталога, без пагинаций и фильтров

---

## Демо <span id="demo"></span>

Функционал backend части будет выглядеть так (есть перевод на русский):
![получившийся функционал каталога](https://raw.githubusercontent.com/mgrechanik/yii2-materialized-path/master/docs/images/catalog.png "Функционал каталога")
	
---
    
## Установка <span id="installing"></span>

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

Если вам не требуются дополнительные поля для Active Record модели ([подробнее](#AAAAAAA)), то таблицу для каталога
вы можете создать выполнив:

```
php yii migrate --migrationPath=@vendor/mgrechanik/yii2-catalog/src/console/migrations
```

#### Подключение модуля

Как говорилось [выше](#goal), данный модуль следует структуре универсального модуля и предоставляет при этом
только страницы backend-а, то при его подключении укажите следующий режим (mode):
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

Обязательными полями для модели каталога являются ```id, path, level, weight ``` (`id` при этом - первичный ключ), 
они нужны для хранения позиции в дереве, . Остальные поля - уже те, которые требуются вам.

Если вам достаточно только одного дополнительного поля - имени - то для этого в расширении имеется
модель [Catalog](#потом), которая установлена моделью по умолчанию данного модуля.

Именно работа с ней показана на [демо](#demo) выше.


---

## Использование своей AR модели  <span id="custom-ar"></span>   

Если вам недостаточно одного дополнительного поля имени, предоставленного (дефолтной)[#default-ar] моделью,
имеется возможность создать свою модель, с нужными вам полями, и указать ее как модель каталога.

Для того чтобы все это сделать, вам нужно проделать следующие шаги:

#### А) Создание своей AR модели

1) Сгенерируйте класс вашей AR модели, взяв за основу (миграцию для модели Catalog)[#потом], главно тут - [обязательные](#default-ar) поля.

2) Измените код вашей модели полностью идентично как мы сделали для [Catalog](#потом) модели: 
* указать имя таблицы
* указали ваши дополнительные поля

3) Укажите данному модулю использовать этот класс модели , через его св-во ```catalogModelClass```
---

## Настройки модуля <span id="settings"></span>

[Подключяя](#setup) модуль в приложение мы можем воспользоваться следующими его свойствами:

#### ```$mode``` - режим работы модуля
Обязательное свойство для установки. [Подробнее](#mode)

#### ```$backendLayout``` - layout для backend контроллеров
Указывается ```layout```, устанавливаемый модулю, когда обращение идет к **backend** контроллеру.  
Полезно для *Basic* шаблона приложения.

#### ```$frontendControllers``` - карта frontend контроллеров
[Подробнее](#fcontroller)

#### ```$backendControllers``` - карта backend контроллеров
[Подробнее](#bcontroller)

#### ```$controllerMapAdjustCallback``` - callback для финальной настройки карты контроллеров

После того как карта контроллеров модуля сгенерирована, ее можно тонко настроить этой функцией, 
ее сигнатура: ```function($map) { ...; return $map; }```

#### ```$backendControllerConfig``` - настройки **backend** контроллеров
Когда модуль [создает](#mknows) **backend** контроллер он перед работой может настроить его данными свойствами.

Удобно, например, чтобы через поведение закрыть доступ к таким контроллерам через фильтры доступа.

[Пример использования](#example-basic). 

#### ```$frontendControllerConfig``` - настройки **frontend** контроллеров
По аналогии с ```$backendControllerConfig```

---

## Пример подключения на *Basic* шаблоне <span id="example-basic"></span>

Предположим что у нас имеется два таких наших модуля - ```example``` и ```omega```.  
Для их подключения вот пример рабочих конфигов:

**config/params.php:**
```php
return [
    'backendLayout' => '//lte/main',
    'backendControllerConfig' => [
        'as backendaccess' => [
            'class' => \yii\filters\AccessControl::class,
            'rules' => [
                [
                    'allow' => true,
                    'ips' => ['54.54.22.44'],
                    'matchCallback' => function ($rule, $action){
                        $user = \Yii::$app->user;
                        return !$user->isGuest &&
                            ($user->id == 1);
                },
                ]
            ],
        ],
    ],	
  
];
```
В данном примере мы разрешили доступ "в админку" только одному указанному пользователю ```(id==1)```, плюс ограничиваем по ```ip```.


**config/web.php:**
```php
    'components' => [
	//...
        'urlManager' => [
            'enablePrettyUrl' => true,
            'showScriptName' => false,
            'rules' => [
                'admin/<module:(example|omega)>-<controllersuffix>/<action:\w*>' =>
                    '<module>/admin-<controllersuffix>/<action>',
                'admin/<module:(example|omega)>-<controllersuffix>' =>
                    '<module>/admin-<controllersuffix>',
            ],
        ],	
    ],
    'modules' => [
        'example' => [
            'class' => 'modules\example\Module',
            'mode' => 'backend and frontend',
            'backendLayout' => $params['backendLayout'],
            'backendControllerConfig' => $params['backendControllerConfig'],
        ],
        'omega' => [
            'class' => 'modules\username1\omega\Module',
            'mode' => 'backend and frontend',
            'backendLayout' => $params['backendLayout'],
            'backendControllerConfig' => $params['backendControllerConfig'],
        ],        
    ], 
```

---

## Рецепты <span id="recipe"></span>

#### Делаем чтобы все админские адреса начинались с ```/admin```  <span id="recipe-admin-url"></span>
Рассмотрим *Basic* приложение, к которому мы подключили два наших модуля:
```php
    'modules' => [
        'example' => [
            ...
        ],
        'omega' => [
            ...
        ],  
```
Если мы следовали [совету](#bcontroller) насчет админских контроллеров, то все они именуются по шаблону ```Admin...Controller```.  
Соответственно адреса к ним будут вида ```example/admin-default``` и ```omega/admin-default```.  
Мы же хотим чтобы все админские адреса начинались с ```admin/```.

Это легко достигается следующими двумя правилами вашего ```urlManager```:
```php
	'urlManager' => [
		'enablePrettyUrl' => true,
		'showScriptName' => false,
		'rules' => [
			'admin/<module:(example|omega)>-<controllersuffix>/<action:\w*>' =>
				'<module>/admin-<controllersuffix>/<action>',
			'admin/<module:(example|omega)>-<controllersuffix>' =>
				'<module>/admin-<controllersuffix>',
		],
	],
```

#### Генерация функционала **backend**-а с помощью Gii CRUD generator   <span id="recipe-crud"></span>

Вы легко можете сгенерировать функционал CRUD-а как обычно, учитывая:
* Имя контроллера и его пространство имен выбирайте соответственно [документации](#bcontroller)
* ```View Path``` указывайте соответственно [структуре папок](#dir-structure) требуемой модулем

#### Как подключать модуль в консольное приложение?   <span id="recipe-other-console"></span>

Если в вашем модуле предусмотрены консольные команды, которые располагаются например тут:
```
Папка_с_модулем/
  console/
    commands/                // Папка для консольных комманд
      HelloController.php
  Module.php
```	  
, то в конфиге консольного приложения данный модуль подключается:

```php
    'modules' => [
        'example' => [
            'class' => 'modules\example\Module',
            'controllerNamespace' => 'yourModuleNamespace\console\commands',
        ],
    ],
```

#### Куда располагать остальной функционал модуля?   <span id="recipe-other-functionality"></span>

Данный модуль регламентирует только указанную выше [структуру папок](#dir-structure), где только от контроллеров
и их виевсов ожидается конкретное расположение.  
По остальному функционалу вы можете придерживаться следующих правил:
* Если компонент четко относится только к одному типу - **backend** или **frontend**, то и помещайте его 
в соответствующую подпапку
* Если же такого разделения нет, то располагайте его в корне его папки.  
Например для моделей
```
  models/
    backend/
      SomeBackendModel.php
    frontend/
      SomeFrontendModel.php	  
    SomeCommonModel.php  
```





php yii migrate --migrationPath=@vendor/mgrechanik/yii2-catalog/src/console/migrations
