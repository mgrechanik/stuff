# Ant colony optimization

[Русская версия](docs/README_ru.md)

## Table of contents

* [Introdution](#introducion)
* [Demo](#demo)
* [Installing](#installing)
* [How to use](#use)
* [Settings](#settings)
* [Performance](#performance)
* [TSPLIB95](#tsplib95)
* [Terminology](#terminology)


---

## Introdution <span id="introducion"></span>

Ant colony optimization is a probabilistic technique for solving computational problems which can be reduced to finding good paths through graphs (from Wikipedia).

The task we are solving could be either "Travelling salesman problem" or "Shortest path problem", or Constrained Shortest Path First, etc.
Two first tasks are solved within this library.

There are a lot of strategies and variations of Classic ACO algorithm.
This library out of the box implements Classic ACO and ACO with elitist ants.

The library could be easily extended for you to implement your ACO variations and to solve the tasks you need.

Initial data about the graph comes either from adjacency matrix or from a list of nodes (cities, vertices, etc) with their X and Y coordinates.

The work of library had been tested with [TSPLIB95](#tsplib95) data sets, so we could check it's [performance](#performance) and efficiency. 

Amount of ants, all coefficients and parameters could be [changed](#settings) to your need.

---

## Demo <span id="demo"></span>

Определение точек по стратегии ```DifferentColorsStrategy``` , **Рисунок 1**:
![Определение точек на изображении](https://raw.githubusercontent.com/mgrechanik/yii2-categories-and-tags/master/docs/images/categories.png "Определение точек на изображении")


Определение точек по стратегии ```ChoosenColorStrategy``` , **Рисунок 2**:
![Определение точек на изображении](https://raw.githubusercontent.com/mgrechanik/yii2-categories-and-tags/master/docs/images/categories.png "Определение точек на изображении карты США")

	
---
    
## Installing <span id="installing"></span>

#### Installing through composer::

The preferred way to install this library is through composer.

Either run
```
composer require --prefer-dist mgrechanik/ant-colony-optimization
```

or add
```
"mgrechanik/ant-colony-optimization" : "~1.0.0"
```
to the require section of your `composer.json`.



---

## How to use  <span id="use"></span> 

### Basic API

1) **Creating a Manager with dependencies we need**
```php
Manager::__construct(DistanceInterface $distanceStrategy = null, AFinder $finder = null, 
                     MathematicsInterface $mathematics = null, Task $task = null);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- By default **Finder** will be Classic one, and the **Task** will be Travelling salesman problem

2) **Loading data from adjacency matrix**
```php
$manager->setMatrix(array $matrix, int $nameStart = 0)
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- $nameStart - from which number start naming aliases of nodes

3) **Loading data from an array of cities**
```php
$manager->setCities(City ...$cities)
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- This array of cities will be transformed to adjacency matrix. Distances will be calculated according to strategy we set to a Manager.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- If city has a ```name``` property it will become it's name alias

4) **Changing of adjacency matrix**
```php
$manager->updateMatrix(int $y, int $x, float|int $value, bool $double = true)
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Например, можем сделать некий участок непроходимым - ```$manager->updateMatrix(1, 0, 1000000);```

5) **Запуск вычислительного процесса**
```php
$distance = $manager->run(int $iterations = 400)
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- для небольших графов, число итераций можно уменьшить.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Вернет найденное расстояние или ```null``` если поиск не увенчался успехом

6) **Получение найденного пути**
```php
$path = $manager->getInnerPath()
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Найденный путь из номеров нод.   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Все ноды внутри именуются числами от 0 до N-1, где N - кол-во нод.  

7) **Получение найденного пути из алиасов**
```php
$path = $manager->getNamedPath()
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Найденный путь из алиасов имен нод, если вы их задавили.   

### Примеры

#### Решаем Задачу Коммивояжера классическим ACO
```php
use mgrechanik\aco\Manager;

$manager = new Manager();
$matrix = [
            [ 0, 8, 4, 11],
            [ 8, 0, 9, 5 ],
            [ 4, 9, 0, 8 ],
            [11, 5, 8, 0 ]
          ];
$manager->setMatrix($matrix);
$distance = $manager->run(20);
var_dump('Distance=' . $distance);
var_dump($manager->getInnerPath())
```
Получим:
```php
Distance=25

Array
(
    [0] => 0
    [1] => 1
    [2] => 3
    [3] => 2
    [4] => 0
) 
```

#### Решаем задачу "О кратчайшем пути", классическим ACO

```php
use mgrechanik\aco\Manager;
use mgrechanik\aco\SppTask;

$task = new SppTask(0, 3);
$manager = new Manager(task : $task);
$matrix = [
            [ 0 , 8, 4, 100],
            [ 8 , 0, 9, 5  ],
            [ 4 , 9, 0, 8  ],
            [100, 5, 8, 0  ]
          ];
$manager->setMatrix($matrix);   
$finder = $manager->getFinder();
// increase amount of ants to 6
$finder->setM(6);
$distance = $manager->run(50);
var_dump('Distance=' . $distance);
var_dump($manager->getInnerPath())
```
Получим:
```php
Distance=12

Array
(
    [0] => 0
    [1] => 2
    [2] => 3
)
// for comparison, the direct path [0, 3] is closed by big distance and distance of path [0, 1, 3] is 13
```

#### Загрузка данных в виде списка городов
```php
use mgrechanik\aco\Manager;
use mgrechanik\aco\City;

$cities = [new City(10,10), new City(50,50), new City(10,50), new City(60,10)];
$manager = new Manager();
$manager->setCities(...$cities);
```


#### Загрузка данных в виде матрицы смежности
```php
use mgrechanik\aco\Manager;

$matrix = [
            [ 0, 8, 4, 11],
            [ 8, 0, 9, 5 ],
            [ 4, 9, 0, 8 ] ,
            [11, 5, 8, 0 ]
          ];
$manager = new Manager();
$manager->setMatrix($matrix);
```

#### Используем поисковик c элитными муравьями

```php
$finder = new \mgrechanik\aco\elitist\Finder();
$manager = new Manager(finder : $finder);
//...
```

#### Смотрим историю работы
```php
use mgrechanik\aco\Manager;

$matrix = [
            [ 0, 8, 4, 11],
            [ 8, 0, 9, 5 ],
            [ 4, 9, 0, 8 ] ,
            [11, 5, 8, 0 ]
          ];
$manager = new Manager();
$finder = $manager->getFinder();
$manager->setMatrix($matrix);
$manager->run();
var_dump($finder->getHistory());
```

#### Грузим список городов из картинки

При использовании [данной библиотеки](https://github.com/mgrechanik/image-points-searcher "Библиотека поиска точек на изображении") мы можем загрузить список городов из картинки, и результат поиска отобразить на результирующей картинке. Визуально будут картинки как на [Demo](#demo "картинки на демо, получены этим способом").   

Подробнее по подготовке картинки изучайте в описании к той библиотеке, но вкратце - на белом холсте отметьте кружочками диаметром 10 px, вершины графа, и используйте в коде ниже эту картинку.

```php
use mgrechanik\aco\Manager;
use mgrechanik\aco\City;

try {
	
    $imageSearcher = new \mgrechanik\imagepointssearcher\Searcher(
        './images/your_image.jpg',
    );
    $found = $imageSearcher->run();    
    if ($found > 1) {
        $points = $imageSearcher->getPoints();
        $cities = [];
        foreach ($points as $point) {
            $cities[] = new City($point['x'], $point['y']);
        }    
        $manager = new Manager();
        $manager->setCities(...$cities);
        if ($res = $manager->run()) {
            $innerPath = $manager->getInnerPath();
            $imageResult = new \mgrechanik\imagepointssearcher\ImageResult($imageSearcher);
            $imageResult->drawLabels();
            $imageResult->drawMargins();
            $imageResult->drawPath($innerPath);
            $imageResult->save('./images/result.jpg');
        }
    }
  
} catch (Exception $e) {
    //
}
```

---

## Settings  <span id="settings"></span>   

### Настройка Поисковика

Основной настраиваемый обьект - Это поисковик.
Получаем его:
```php
$manager = new Manager();
$finder = $manager->getFinder();
// Настраиваем
//$finder->set...
// ...
//$manager->run();
```

**Доступные настройки:**

- Установим величину расстояния, при которой участок между двумя узлами считается непроходимым  
```->setClosedPathValue(int $value)```

- Установим число муравьев  
```->setM(int $m)```

- Установим число муравьев как процент от числа нод. Используется по умолчанию (=40%)  
```->setMPercent(int $mPercent)```

- Устанавливаем коэффициенты для формулы
```php
->setAlpha(float $alpha);
->setBeta(float $beta);
->setP(float $p);
->setC(float $c);
->setQ(int $q);
```

- Установить стратегию выполнения математических задач  
```->setMathematics(MathematicsInterface $mathematics)```

- Установить текущую выполняемую задачу. TSP, SPP или др.  
```->setTask(Task $task)```

- Установим число элитных муравьев (для поисковика с элитными муравьями)   
```->setSigma(int $sigma)```

- Установим число элитных муравьев как процент от числа регулярных муравьев. Используется по умолчанию (=50%)   (для поисковика с элитными муравьями)  
```->setSigmaPercent(int $sPercent)```

---

## Performance  <span id="performance"></span>   

> Прежде всего отключите XDebug или аналоги, т.к. они могут на порядок влиять на скорость работы

Данный ACO алгоритм находит [хорошие](#demo) пути на графе. И даже лучшие. 

Для примера возьмем задачу ```berlin52.tsp``` из библиотеки [TSPLIB95](#tsplib95), где 52 узла у графа.
Решаем задачу кодом ниже:
```php
$cities = TspLibLoader::loadCitiesFromEuc2dFile(__DIR__ . '/images/data/berlin52.tsp');
$finder = new \mgrechanik\aco\elitist\Finder();
$finder->setSigmaPercent(150);
$finder->setMPercent(30);
$finder->setAlpha(0.7);
$manager = new Manager(finder : $finder);
$manager->setCities(...$cities);
$distance = $manager->run(300);
var_dump('Distance=' . $distance);
var_dump($finder->getHistory());
```
Получили вывод:
```php
Distance=7542

   Array ... 
    [85] => Array
        (
            [distance] => 7542
            [inner_path] => 0_21_30_17_2_16_20_41_6_1_29_22_19_49_28_15_45_43_33_34_35_38_39_36_37_47_23_4_14_5_3_24_11_27_26_25_46_12_13_51_10_50_32_42_9_8_7_40_18_44_31_48_0
            [iteration] => 85
            [time_spent] => 1.94 sec
        )  
    )
```

Данный код, работая на офисном компьютере, за менее чем 2 секунды, смог найти самый лучший путь из известных.

Здесь мы использовали поисковик на основе алгоритма с использования элитных муравьев. Он на практике дает лучшие результаты, чем классический.

Алгоритм вероятностный, каждый раз муравьи будут путешествовать по другому. И очень многое зависит от кол-ва нод, муравьев, настройки всех коэффициентов и параметров, участвующих в формулах.


---

## TSPLIB95 <span id="tsplib95"></span>

Библиотека [TSPLIB95](http://comopt.ifi.uni-heidelberg.de/software/TSPLIB95/), содержит множество ```Задач коммивояжера``` - исходные данные и решения - лучшие рузультаты, которые были когда либо найдены для этих задач ([пути](#tsplib95 "Пути располагаются в соответствующих файлах имя.opt.tour") и [расстояния](http://comopt.ifi.uni-heidelberg.de/software/TSPLIB95/STSP.html "Тут можно смотреть лучшие найденные расстояния")).

Библиотека ценна тем, что на ее данных можно протестировать эффективность наших алгоритмов, коэффициентов и параметров.

Библиотека предоставляет множество разных форматов исходных данных. Мы из коробки поддерживаем два из них.

### Подключение данных в виде списка X и Y координат. Расстояние Эвклидовое.

Пример файла с этим форматом - **berlin52.tsp** .
Загружаем из него список нод (городов) и передаем менеджеру:

```php
use mgrechanik\aco\TspLibLoader;
use mgrechanik\aco\Manager;

$fileName = __DIR__ . '/berlin52.tsp';
$cities = TspLibLoader::loadCitiesFromEuc2dFile($fileName);
$manager = new Manager();
$manager->setCities(...$cities);
```

### Подключение данных в виде матрицы смежности

Пример файла с этим форматом - **bays29.tsp** .

Загружаем из него матрицу смежности и передаем менеджеру:

```php
use mgrechanik\aco\TspLibLoader;
use mgrechanik\aco\Manager;

$fileName = __DIR__ . '/bays29.tsp';
$matrix = TspLibLoader::loadMatrixFromExplicitMatrixFile($fileName);
$manager = new Manager();
// tsplib95 library names nodes starting with "1"
$manager->setMatrix($matrix, 1);
```

---

## Terminology  <span id="terminology"></span>   

#### ```ACO``` - Ant colony optimization algorithm

#### ```Nodes``` - Nodes or vertices or cities. Ants travel between them.

#### ```Adjacency Matrix``` - Adjacency Matrix sets the distances between graph nodes. It is a basic structure our algorithm starts work with.
When graph is loaded like ```Cities``` with their coordinated, this information will be converted to Adjacency Matrix

#### ```Classic Finder``` - Finder who implements Classic ACO algorithm

#### ```Elitist Finder``` - Finder who implements ACO algorithm when we use elitist ants

#### ```Ant``` - ant, working unit, who move through graph searching for paths

#### ```Task``` - Task we are solving on graph. For example it could be  ```"Travelling salesman problem"```. or ```"Shortest path problem"```. Or other.

#### ```Manager``` -  Manager which task is to form adjacency matrix , give it to ```Finder``` to solve ```Task```.

#### ```Iteration``` - Iteration during which all ants find one path and put pheromones on it. We set amount of iterations

#### ```Pheromon``` - is the instance ants leave on paths

#### ```m``` - Amount of ants

#### ```mPercent``` - Amount of ants in percents relatively to amount of nodes

#### ```sigma``` - Amount of elitist ants, if we use corresponding algorithm

#### ```sigmaPercent``` - Amount of elitist ants in percents relatively to amount of regular ants

#### ```alpha``` - Coefficient to control the influence of pheromone amount

#### ```beta``` - Coefficient to control the influence of desirability of path

#### ```p``` - Evaporation coefficient

#### ```c``` - Starting amount of pheromones on paths

#### ```Q``` - The constance used to calculate how many pheromones an ant puts on the path it found
