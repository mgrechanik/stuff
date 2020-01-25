# Active Record hierarchical SEO categories and tags for Yii2 framework

[Русская версия](docs/README_ru.md)

## Table of contents

* [Goal](#goal)
* [Demo](#demo)
* [Installing](#installing)
* [Module settings](#settings)

---

## Goal <span id="goal"></span>

This extension gives you a **variation** of [categories module](https://github.com/mgrechanik/yii2-categories-and-tags), 
in which the [opportunity](https://github.com/mgrechanik/yii2-categories-and-tags#custom-ar) to create any your own ```Active Record``` category models was given.

We suggest that when creating pages at **frontend**  to display associated content of the category (or tag) 
we would need to manage SEO information of such category page.

Respectively we add next fields to our SEO category ```Active Record``` model:

* ```name``` to name category
* ```title``` for content of ```<title>``` tag
* ```meta_description``` for value of ```content``` attribute of ```<meta name="description">``` tag
* ```meta_keywords``` for value of ```content``` attribute of ```<meta name="keywords">```	tag
* ```meta_other``` to inserting any other ```html``` meta tags  you may need
* ```slug``` serves as a "slug", or tail, in the page address
	
> With all this in module's settings you may choose not to use 	```meta_other```  
> or ```slug``` fields and they will not appear in the web form of creating/editing SEO category
---
    
## Installing <span id="installing"></span>

#### Installing through composer:

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).:

Either run
```
composer require --prefer-dist mgrechanik/yii2-seo-categories
```

or add
```
"mgrechanik/yii2-seo-categories" : "~1.0.0"
```
to the require section of your `composer.json`

#### Migrations

This extension comes with two migrations:
- the first creates SEO categories table with all indexes needed
- the second creates unique index for ```slug``` field

You can run both of them:

```
php yii migrate --migrationPath=@vendor/mgrechanik/yii2-seo-categories/src/console/migrations
```

, or when you do not use ```slug``` field run only the first migration:

```
php yii migrate 1 --migrationPath=@vendor/mgrechanik/yii2-seo-categories/src/console/migrations
```

#### Setting the module up  <span id="setup"></span>

As was mentioned in the [basic categories module](https://github.com/mgrechanik/yii2-categories-and-tags#goal), 
this module follows the approach of *universal module*, and since it gives you
only **backend** pages when you set it up into your application specify the next ```mode``` :
```php
    'modules' => [
        'seocategory' => [
            'class' => 'mgrechanik\yii2seocategory\Module',
            'mode' => 'backend',
            // Other module settings
        ],
        // ...
    ],
```

Done. When you access ```/seocategory``` page you will see all your SEO categories in a form of tree.

---

## Module settings <span id="settings"></span>

[Setting up](#setup) the module into application, along with all properties of the [base categories module](https://github.com/mgrechanik/yii2-categories-and-tags#settings), we can use it's next properties:

#### ```$useMetaOtherField = false``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Whether to use ```other meta tags``` field

#### ```$useSlugField = true``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Whether to use ```slug``` field. It is supposed to be unique

#### ```$slugPattern``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- When we use previous field in this property we set up regular expression of expected symbols 

#### ```$showTitleColumnAtIndexPage = true```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Whether to display ```title``` field in the categories list grid

#### ```$showSlugColumnAtIndexPage = false``` 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Whether to display ```slug``` field in the categories list grid