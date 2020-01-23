# Hierarchical catalog of Active Record models for Yii2 framework

[Русская версия](docs/README_ru.md)

## Table of contents

* [Goal](#goal)
* [Demo](#demo)
* [Installing](#installing)
* [Default AR catalog model of this extension](#default-ar)
* [Using your own AR model](#custom-ar)
* [Module settings](#settings)
* [Example of displaying a catalog at frontend](#frontend-output)



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
can be created by running:

```
php yii migrate --migrationPath=@vendor/mgrechanik/yii2-catalog/src/console/migrations
```

#### Setting the module up  <span id="setup"></span>

As was mentioned [before](#goal) this module follows the approach of *universal module*, and since it gives you
only **backend** pages when you set it up into your application specify the next ```mode``` :
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
they serve to saving tree position. The rest of the fields are ones you need.

If you are satisfied with only one additional text field - ```name``` - then this extension provides
[Catalog](https://github.com/mgrechanik/yii2-catalog/blob/master/src/models/Catalog.php) model which is set as the default catalog model of the module.

The work precisely with it is shown at [demo](#demo) above.


---

## Using your own AR model  <span id="custom-ar"></span>   

If having one additional ```name``` field [default](#default-ar) catalog model gives is not enough 
there is a way to use your own model with fields you need which will serve as catalog model.

To do this you need to follow the next steps:

#### А) Setting up your AR model <span id="custom-ar-a"></span>

1) Generate the class of your AR model starting from table created by migration similar to [Catalog model migration](https://github.com/mgrechanik/yii2-catalog/blob/master/src/console/migrations/m180908_094405_create_catalog_table.php). The main point here are [required](#default-ar) fields

2) Change the code of your AR model exactly like we did the same with [Catalog](https://github.com/mgrechanik/yii2-catalog/blob/master/src/models/Catalog.php) model: 
* change the table name
* make it to be inherited from ```BaseCatalog``` class
* Set up your additional fields in ```rules(), attributeLabels()```

3) Set up your module to use this catalog model by using it's ```$catalogModelClass``` property

4) If your model does not have ```name``` field you need to set up [```$indentedNameCreatorCallback```](#indented-name) module property

#### B) Setting up your catalog form model <span id="custom-ar-b"></span>

AR model and form model are separated so the steps similar to **A)** need to be performed to your form model.

1) Create your form model starting from [CatalogForm](https://github.com/mgrechanik/yii2-catalog/blob/master/src/ui/forms/backend/CatalogForm.php). 
In the default form we added only one field - ```name``` but you need to add your own. Do not forget about inheritance from ```BaseCatalogForm```

2) Set up your module to use this catalog form model by using it's ```$catalogFormModelClass``` property

#### C) Setting up views <span id="custom-ar-c"></span>

This module has an opportunity to set up [which views to use](#setup-views).

The ones of them with information which vary needs to be copied, changed as needed and set up to module.

---

## Module settings <span id="settings"></span>

[Setting up](#setup) the module into application we can use it's next properties:

#### ```$catalogModelClass``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Which catalog AR model class to use

#### ```$catalogFormModelClass``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Which catalog form model class to use

#### ```$indentedNameCreatorCallback``` <span id="indented-name">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Callback which will create the label of the catalog item at catalog view page
considering indent needed to show catalog as a tree

#### ```$catalogIndexView```, ```$catalogCreateView```, ```$catalogUpdateView```, ```$catalogFormView```, ```$catalogViewView``` <span id="setup-views"></span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- the corresponding **views** for module to use. 
For it's format look into [documentation](https://www.yiiframework.com/doc/api/2.0/yii-base-view#render()-detail)

#### ```$redirectToIndexAfterCreate``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Whether to redirect to catalog view page after new element has been created.  
```True``` by default. With ```false``` the redirect will be to catalog element view page

#### ```$redirectToIndexAfterUpdate``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Similar to the previous property but for updation task

#### ```$validateCatalogModel``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Whether to validate catalog model before saving.  
Default ```false``` when we consider that the validation form performes is enough

#### ```$creatingSuccessMessage```, ```$updatingSuccessMessage```, ```$deletingSuccessMessage``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- The texts of flash messages.  
If you change them do not forget about their translations in the ```yii2catalog``` source


---

## Example of displaying a catalog at frontend <span id="frontend-output"></span>

If you need to output your catalog into any template just run:
```php
use mgrechanik\yiimaterializedpath\ServiceInterface;
// This is our default catalog model:
use mgrechanik\yii2catalog\models\Catalog;
use mgrechanik\yiimaterializedpath\widgets\TreeToListWidget;

// get the trees managing service
$service = \Yii::createObject(ServiceInterface::class);
// Get the element relevant to who we build the tree.
// In our case it is the Root node
$root = $service->getRoot(Catalog::class);
// Build the tree from descendants of the Root node
$tree = $service->buildDescendantsTree($root);
// Print at the page
print TreeToListWidget::widget(['tree' => $tree]);
```
*You will see the next tree:*
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

