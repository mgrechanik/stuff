# Searching for points on image

[Русская версия](docs/README_ru.md)

## Содержание

* [Goal](#goal)
* [Demo](#demo)
* [Installing](#installing)
* [Search for points](#search)
* [Display search result](#display-result)


---

## Goal <span id="goal"></span>

This  library allows to find the coordinates of the points you are looking for on image (```jpg``` format).

The point is the set of nearby pixels of choosen color.

For example when you draw a graph on image, we will find it's vertices.

Out of the box we have two strategies to determite our points.

1. ```DifferentColorsStrategy``` - when the point is different from background color. Default behavior. See Demo, picture 1.
2. ```ChoosenColorStrategy``` - when point is defined by it's exact color.  See Demo, picture 2.

Вы можете создать свою стратегию поиска и использовать ее.
You can create your own strategy and use it.

Points could be of different size, so they could be easily seen, not just 1px size. We use parameter ```Searcher::$margin``` to determine all those pixels as one point.

The result of the search work is an array of points, represented by their X and Y coordinates.

We also supply ```ImageResult```, который позволяет создать изображение результата поиска:
- Найденным точкам можно проставить надписи
- Границы найденных точек можно обвести, чтобы понятней было как происходил поиск
- Между точками можно проложить путь из линий


---

## Демо <span id="demo"></span>

Определение точек по стратегии ```DifferentColorsStrategy``` , **Рисунок 1**:
![Определение точек на изображении](https://raw.githubusercontent.com/mgrechanik/yii2-categories-and-tags/master/docs/images/categories.png "Определение точек на изображении")


Определение точек по стратегии ```ChoosenColorStrategy``` , **Рисунок 2**:
![Определение точек на изображении](https://raw.githubusercontent.com/mgrechanik/yii2-categories-and-tags/master/docs/images/categories.png "Определение точек на изображении карты США")

	
---
    
## Установка <span id="installing"></span>

#### Установка через composer:

Выполните
```
composer require --prefer-dist mgrechanik/yii2-categories-and-tags
```

или добавьте
```
"mgrechanik/yii2-categories-and-tags" : "~1.0.0"
```
в  `require` секцию вашего `composer.json` файла.



---

## Поиск точек  <span id="search"></span> 

#### Поиск по стратегии ```DifferentColorsStrategy```
```php
try {
  $searcher = new \mgrechanik\imagepointssearcher\Searcher(
    './images/graph.jpg'
  );
  $found = $searcher->run();
  print 'Найдено - ' . $found;
  $points = $searcher->getPoints();
  var_dump($points);
} catch (Exception $e) {
	
}
```
Результат будет, например:
```
Найдено - 2
[
	['x' => 10, 'y' => 10],
	['x' => 80, 'y' => 80],
]
```

#### Поиск по стратегии ```ChoosenColorStrategy```
```php
try {
  $searcher = new \mgrechanik\imagepointssearcher\Searcher(
    './images/usa.jpg',
    new \mgrechanik\imagepointssearcher\ChoosenColorStrategy(60, 132, 253)
  );
  $found = $searcher->run();
  print 'Найдено - ' . $found;
  $points = $searcher->getPoints();
  var_dump($points);
} catch (Exception $e) {
	
}
```

---

## Отображение результата поиска  <span id="display-result"></span>   

```php
$imageResult = new \mgrechanik\imagepointssearcher\ImageResult($searcher);

// Можно самому задать цвета
$imageResult->setLabelsColor(1, 14, 230);
$imageResult->setLinesColor(255, 33, 73);
$imageResult->setMarginsColor(255, 106, 0);


// Рисуем надписи по умолчанию
$imageResult->drawLabels();
//Рисуем надписи, сами определяя текст
$imageResult->drawLabels(function($key, $point) { return "{$point['x']},{$point['y']}";});

// Рисуем границы найденных точек
$imageResult->drawMargins();

// Рисуем путь между точками
$imageResult->drawPath([2,9,20,28,41,48,44]);

// Сохраняем результат в виде изображения. См. Демо.
$imageResult->save('./images/result.jpg');
```