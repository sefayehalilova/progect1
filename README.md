
* [Задачи классификации](#Задачи-классификации)  
    - [Метрические алгоритмы классификации](#Метрические-алгоритмы-классификации)  
        - [Алгоритм k ближайших соседей (kNN)](#Алгоритм-k-ближайших-соседей-(kNN))
	    - [Алгоритм 1NN](#Алгоритм-1NN)  
	    - [Алгоритм k взвешенных ближайших соседей (kwNN)](#Алгоритм-k-взвешенных-ближайших-соседей-(kwNN))  
	    - [Метод Парзеновского окна](#Метод-Парзеновского-окна)  
    - [Байесовские классификаторы.](#Байесовские-классификаторы.)  
        - ["Наивный" байесовский классификатор.](#"Наивный"-байесовский-классификатор.)  
	    - [Подстановочный(Plug-in) алгоритм](#Подстановочный(Plug-in)-алгоритм)  
	    - [Линейный дискриминант Фишера](#Линейный-дискриминант-Фишера)  
    - [Линейные алгоритмы классификации](#Линейные-алгоритмы-классификации)
        - [Метод стохастического градиента для линейных алгоритмов.](#Метод-стохастического-градиента-для-линейных-алгоритмов.)
    

	

Задачи классификации
 =======================
 Метрические алгоритмы классификации 
------------------------------------------------------------------------

**Метрические методы обучения** - методы, основанные на анализе сходства объектов. Для формализации понятия сходства вводится функция расстояния между объектами ![](http://latex.codecogs.com/gif.latex?p(x_1,x_2)). Чем меньше расстояние между объектами, тем больше объекты похожи друг на друга. Метрические классификаторы опираются на гипотезу компактности, которая предполагает, что *схожим объектам чаще соответствуют схожие ответы*.

**Опр 1.1.** Метрические алгоритмы классификации с обучающей выборкой ![](https://latex.codecogs.com/gif.latex?%5Cinline%20X%5E%7Bl%7D) относят классифицируемый объект *u* к тому классу *y*, для которого суммарный вес ближайших обучающих объектов максимален.  

Ниже представлена таблица, в которой сравниваются разные алгоритмы классификации:  

| Метод         | Параметры       | Точность      |
|:------------- |:---------------:| -------------:|
| knn           | k=6             |     0.0333    |
| kwnn          | k=6,q=0.5       |     0.0466    |  
| PW.Pryamoyg   | h=0.3           |     0.04      |
| PW.Treyg      | h=0.3           |     0.04      |  
| PW.Kvartch    | h=0.3           |     0.04      |
| PW.Epanech    | h=0.3           |     0.04      |  
| PW.Gays       | h=0.1           |     0.04      |



Алгоритм k ближайших соседей (kNN)
-------------------------------------
Данный алгоритм классификации относит классифицируемый объект *z* к тому классу *y*, к которому относится большинство из *k* его ближайших соседей.    
Имеется некоторая выборка ![](https://latex.codecogs.com/gif.latex?%5Cinline%20X%5E%7Bl%7D), состоящая из объектов *x(i), i = 1, ..., l* (например, выборка ирисов Фишера), и класифицируемый объект, который обозначим *z*. Чтобы посчитать расстояние от классифицируемого объекта до остальных точек, нужно использовать функцию расстояния(Евклидово расстояние).  
Далее отсортируем объекты согласно посчитаного расстояния до объекта *z*:  
```diff
sortObjectsByDist <- function(xl, z, metricFunction = euclideanDistance) ## задаем функцию расстояния
 {
    distances <- matrix(NA, l, 2)## задаем матрицу расстояния
     for (i in 1:l)
       {
        distances[i, ] <- c(i, metricFunction(xl[i, 1:n], z)) ## считаем расстояние от классифицируемой точки до остальных точек выборки
       }

 orderedXl <- xl[order(distances[, 2]), ] ##сортируем согласно посчитаного расстояния
 return (orderedXl);
 }
```
Применяем сам метод kNN, то есть создаем функцию, которая сортирует выборку согласно нашего классифицируемого объекта *z* и относит его к классу ближайших соседей:
```diff
kNN <- function(xl, z, k)
{
 orderedXl <- sortObjectsByDist(xl, z) ## Сортируем выборку согласно классифицируемого объекта
 n <- dim(orderedXl)[2] - 1

 classes <- orderedXl[1:k, n + 1] ## Получаем классы первых k соседей
 counts <- table(classes) ## Составляем таблицу встречаемости каждого класса
 class <- names(which.max(counts)) ## Находим класс, который доминирует среди первых соседей
 return (class) ## возвращаем класс
 }

```
 В конце задаем точку *z*, которую нужно классфицировать(ее координаты, выборку и тд).  

Ниже представлен итог работы алгоритма при *k=6*.  
![knn](https://user-images.githubusercontent.com/43229815/48431971-23601980-e784-11e8-9b3a-f1736d24173b.png)   
[kNN](https://github.com/sefayehalilova/progect1/blob/master/knn.R)

**Скользящий контроль(leave-one-out) для knn**  
   
LeaveOneOut (или LOO) - простая перекрестная проверка, которая необходима, чтобы оценить при каких значениях k алгоритм knn оптимален, и на сколько он ошибается.  
Нужно проверить, как часто будет ошибаться алгоритм, если по одному выбирать элементы из обучающей выборки.  
*Алгоритм состоит в следующем*: извлечь элемент, обучить оставшиеся элементы, классифицировать извлеченный, затем вернуть его обратно. Так нужно поступить со всеми элементами выборки.  
Если классифицируемый объект xi не исключать из обучающей выборки, то ближайшим соседом xi всегда будет сам xi, и минимальное (нулевое) значение функционала LOO(k) будет достигаться при k = 1.  
Реализация самого LOO в коде выглядит так:  
```diff

for(k in Ox) {
  R <- 0 
  for(i in 1:l) {
    iris_new <- iris[-i, ] 
    z <- iris[i, 3:4]
    if(knn(iris_new, z, k) != iris[i, 5]) { 
      R <- R + 1 
    } 
  }
#после того как мы перебрали все элементы, считаем loo
  LOO <- R/l #где R накопитель ошибки, а l-количество элементов выборки
  Oy <- c(Oy, LOO)
  
#Если найденное loo оказалось меньше изначального оптимального значения, то сделаем это новое значение оптимальным и соотвественно k тоже оптимально
  if(LOO < LOO_opt) {
    LOO_opt <- LOO
    k_opt <- k
  }
}

```

*График зависимости LOO от k*:  
![loo](https://user-images.githubusercontent.com/43229815/48148795-adfbd100-e2cb-11e8-9c79-b74f736a31bd.png)  

k оптимальное (k_opt) достигается при минимальном LOO (оптимальное k равно 6).  

Алгоритм 1NN
-----------------------------------
Алгоритм ближайшего соседа(1NN) является самым простым алгоритмом клссификации.  
Данный алгоритм классификации относит классифицируемый объект u к тому классу y, к которому относится его ближайший сосед.
Для начала нужно задать метрическую функцию, затем нужно отсортировать выборку по расстояниям от точек до классфицируемой точки, после чего классифицируемый элемент относим к классу, к которому принадлежит ближайший элемент(первый в отсортированной выборке).

Имеется некоторая выборка Xl, состоящая из объектов x(i), i = 1, ..., l (снова выборка ирисов Фишера). Как и в задаче kNN, задаем функцию расстояния, считаем расстояния от классифицируемой точки до остальных точек выборки, сортируем эти расстояния.  


Далее применяем метод 1NN и классифицируем данный объект, то есть найти ближайший объект выборки и вернуть класс, к которому принадлежит наш объект:
```diff
# применяем метод 1NN
NN1 <- function(xl, point) {	  
	  orderedXl <- sortObjectsByDist(xl, point) # сортировка выборки согласно классифицируемого объекта    
	  n <- dim(orderedXl)[2] - 1 
	  class <- orderedXl[1, n + 1] # получение класса соседа
	  return (class) # возвращаем класс
}

for(i in OX){
	for(j in OY){
	  point<-c(i,j)
	  class <- NN1(xl, point) 
	  points(point[1], point[2], pch = 21, col = colors[class], asp = 1) } # классификация заданного объекта
}
```
Ниже представлен итог работы данного алгоритма:
![1nn](https://user-images.githubusercontent.com/43229815/48433796-1691f480-e789-11e8-8c17-54f60a593be9.png)   

[1NN](https://github.com/sefayehalilova/progect1/blob/master/1nn.R)  

Алгоритм k взвешенных ближайших соседей (kwNN)
----------------------------------------------------------------------  
  Имеется некоторая выборка Xl, состоящая из объектов x(i), i = 1, ..., l ( выборка ирисов Фишера). Данный алгоритм классификации относит объект u к тому классу y, у которого максимальна сумма весов w_i его ближайших k соседей x(u_i), то есть объект относится к тому классу, который набирает больший суммарный вес среди k ближайших соседей.  
  В алгоритме kwNN используется весовая функция, которая оценивает степень важности при классификации заданного элемента к какому-либо классу, что и отличает его от алгоритма kNN.  
  kwNN отличается от kNN, тем что учитывает порядок соседей классифицируемого объекта, улучшая качество классификации.  
  Весовая функция kwNN выглядит следующим образом:  
  ```diff
for(i in 1:l) {
       weights[i] <- q^i
    }
```    
Сама функция выглядит так:  
  ```diff
distances <- matrix(NA, l, 2)# расстояния от классифицируемого объекта u до каждого i-го соседа 
    for(p in 1:l) {
      distances[p, ] <- c(p, euclideanDistance(xl[p, 1:n], z))
    }
    orderedxl <- xl[order(distances[ , 2]), ]# сортировка расстояний
    
    weights <- c(NA)# подсчёт весов для каждого i-го объекта
    for(i in 1:l) {
       weights[i] <- q^i 
    }
    orderedxl_weighted <- cbind(orderedxl, weights)
    classes <- orderedxl_weighted[1:k, (n + 1):(n + 2)] # названия первых k ближайших соседей и их веса
    sumSetosa <- sum(classes[classes$Species == "setosa", 2])
    sumVersicolor <- sum(classes[classes$Species == "versicolor", 2])
    sumVirginica <- sum(classes[classes$Species == "virginica", 2])
    answer <- matrix(c(sumSetosa, sumVersicolor, sumVirginica), 
                  nrow = 1, ncol = 3, byrow = TRUE, list(c(1), c('setosa', 'versicolor', 'virginica')))#матрица имен классов и их сумм весов, которая заполняется по строкам
points(z[1], z[2], pch = 21, col = colors[which.max(answer)], asp=1) #закрашиваем точки в тот цвет класса, чей вес максимальный
```    
Результат работы алгоритма:  
![kwnn](https://user-images.githubusercontent.com/43229815/48442083-3253c580-e79e-11e8-879e-847b926014df.png)  
*Ниже представлен график зависимости LOO от q*:  

![wknnloo](https://user-images.githubusercontent.com/43229815/48499976-cd09de00-e84a-11e8-918d-baa683dad670.png)  

Метод Парзеновского окна  
---------------------------------  
Имеется некоторая выборка Xl, состоящая из объектов x(i), i = 1, ..., l (в нашем случае выборка ирисов Фишера).  
В данном алгоритме используется такая весовая функция, которая зависит от расстояния между классфицируемым объектом и его соседями, тогда как в алгоритме взвешенных kNN весовая функция зависела от ранга соседа.  
Весовая функция для данного алгоритма выглядит так:  
![](https://camo.githubusercontent.com/7c2ed26a40e7613a600b2f6a7cf6fbe7341bd68b/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f5725323869253243253230752532392532302533442532304b2535436c6566742532302532382532302535436672616325374225354372686f25323025323875253243253230785f2537427525374425354525374269253744253239253744253742682537442532302535437269676874253230253239), где K(z)-функция ядра, а h-ширина окна.  

Используют 5 ядер:  
*1.* прямоугольное:  
![](https://camo.githubusercontent.com/025171a6fc01cd4b059c86262757f8d3a8acd6ed/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f4b2532387a2532392532302533442532302535436672616325374231253744253742322537442535422537437a25374325323025334325334425323031253544).  
*2.* треугольное:  
![](https://camo.githubusercontent.com/a513b3e1d90529afd518141b2bb2e5cd279d588c/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f4b2532387a253239253230253344253230253238312532302d2532302537437a2537432532392535422537437a25374325323025334325334425323031253544).  
*3.* квартическое:  
![](https://camo.githubusercontent.com/9621b1489712f591c395330ef15e6456097b13fc/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f4b2532387a25323925323025334425323025354366726163253742313525374425374231362537442a253238312532302d2532307a253545253742322537442532392535422537437a25374325323025334325334425323031253544).  
*4.* Епанечникова:  
![](https://camo.githubusercontent.com/046fe35bcec11a1aad941201159c6b72111d3bf5/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f4b2532387a2532392532302533442532302535436672616325374233253744253742342537442a253238312532302d2532307a253545322532392535422537437a25374325323025334325334425323031253544).  
*5.* Гауссовское:  
![](https://camo.githubusercontent.com/639585d43d5e398c76c8240cdcb79f1600056192/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f4b2532387a253239253230253344253230253238253238322535437069253239253545253742253545253742253238253543667261632537422d31253744253742322537442537442532392537442532392a65253545253742253238253543667261632537422d7a2535453225374425374232253744253239253744).  

Программная реализация данных функций:  
 ```diff
RectangularКernel = function(z){
  return ((0.5 * (abs(z) <= 1) )) #функция прямоугольного ядра
}
TriangularKernel = function(z){
  return ((1 - abs(z)) * (abs(z) <= 1)) #функция для треугольного ядра
}
QuarticKernel = function(z){
  return ((15 / 16) * (1 - z ^ 2) ^ 2 * (abs(z) <= 1)) #функция для квартического ядра
}
EpanechnikovKernel = function(z){
  return ((3/4*(1-z^2)*(abs(z)<=1))) #функция для ядра Епанечникова
}
GaussianKernel = function(z){
  (((2*pi)^(-1/2)) * exp(-1/2*z^2)) #функция для Гауссовского ядра
}
```  
Программная реализация самой функции PW:  

 ```diff
PW <- function(xl,point, h)
{
    weight <- matrix(NA, l, 2) #матрица расстояний и весов
    for (p in 1:l) {
      weight[p, 1] <- euclideanDistance(xl[p, 1:n], point) # расстояния от классифицируемого объекта u до каждого i-го соседа
      z <- weight[p, 1] / h # аргумент функции ядра
      cores <- c(RectangularКernel(z), TriangularKernel(z), QuarticKernel(z), EpanechnikovKernel(z), GaussianKernel(z)) #функции ядер
      
      weight[p, 2] <- cores[2] # подсчёт веса для треугольного ядра
    }
    
     
    colnames(classes) <- c("Distances", "Weights", "Species")
    
   
    sumSetosa <- sum(classes[classes$Species == "setosa", 2])
    sumVersicolor <- sum(classes[classes$Species == "versicolor", 2])
    sumVirginica <- sum(classes[classes$Species == "virginica", 2])
    answer <- matrix(c(sumSetosa, sumVersicolor, sumVirginica), 
                     nrow = 1, ncol = 3, byrow = T, list(c(1), c('setosa', 'versicolor', 'virginica')))
    if(answer[1,1]==0&&answer[1,2]==0&&answer[1,3]==0){
      class='white'
    }
    else{
      class = colors[which.max(answer)]
    }
    return(class)
} 
```   
Ниже представлены результаты работы алгоритма с разными ядрами, также посчитано LOO:  
**1**.  
![pw pryamoyg](https://user-images.githubusercontent.com/43229815/49181737-31f42680-f369-11e8-819f-c2457385d396.png)  
**2**.  
![pw treyg](https://user-images.githubusercontent.com/43229815/49181026-5949f400-f367-11e8-9ea7-a1e174a8edb1.png)  
**3**.  
![pw kvar](https://user-images.githubusercontent.com/43229815/49182335-11c56700-f36b-11e8-8120-cc4e62c8b664.png)  
**4**.  
![pw epanch](https://user-images.githubusercontent.com/43229815/49182767-340bb480-f36c-11e8-87ed-a45b74fa17a8.png)  
**5**.  
![pw gayss](https://user-images.githubusercontent.com/43229815/49183543-3707a480-f36e-11e8-9f13-531ebbcda207.png)  










Байесовские классификаторы.
=====================================  
Байесовский подход основан на следующей теореме: *если плотности распределения классов известны, то алгоритм классификации, имеющий минимальную вероятность ошибок, можно выписать в явном виде*.  
Чтобы классифицировать точку, для начала, нужно вычислить функции правдоподобия каждого из классов, затем вычислить апостериорные вероятности классов. Классифицируемый объект относится к тому классу, у которого апостериорная вероятность максимальна.  
**Байесовское решающее правило**:  
![](https://camo.githubusercontent.com/cfb0e23e2816ceaed4e85bec306e0b71d1f16ceb/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f61253238782532392532302533442532306172672532302535436d61785f25374279253230253543657073696c6f6e253230592537442535436c616d6264612532305f25374279253744502532387925323925354372686f2532387825374379253239)

"Наивный" байесовский классификатор.
----------------------------------------------------  
Все объекты выборки X описываются n числовыми признаками fj, где j=1,..,n. Эти признаки являются независимыми случайными величинами.
Функции правдоподобия классов выглядят так:  
py(x) = py1(ξ1)⋅⋅⋅pyn(ξn), где pyj(ξj) плотность распределения значений j-го признака для класса y.  
Эмпирическая оценка n-мерной плотности:  
![](https://camo.githubusercontent.com/fee3006512d8bad465e8973b1e6c6d9bf3ed020b/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f253543686174253742702537445f682532387825323925323025334425323025354366726163253742312537442537426d25374425323025354373756d5f25374269253230253344253230312537442535452537426d25374425323025354370726f645f2537426a253230253344253230312537442535452537426e2537442532302535436672616325374231253744253742685f6a2537442532304b2532302535436c65667425323825323025354366726163253742665f6a253238782532392532302d253230665f6a253238785f69253239253239253239253744253742685f6a2537442532302535437269676874253230253239), где x -- классифицируемая точка, x_i -- точки обучающей выборки, h -- ширина окна, m -- кол-во точек выборки, n -- кол-во признаков, K -- функция ядра, f_j -- признаки.    
Подставив в байесовское решающее правило эмпирические оценки одномерных плотностей признаков получим алгоритм, который называется   **"наивным" байесовким классификатором**.  
![](https://github.com/PavlyukovVladimir/SMPR/blob/master/img/naivnyy_bayes.jpg)  

Подстановочный(Plug-in) алгоритм  
------------------------------------------------  
Нормальный дискриминантный анализ - это специальный случай байесовской классификации, предполагающий, что плотности всех классов являются многомерными нормальными.  
Случайная величина x имеет многомерное нормальное распределение, если ее плотность задается выражением:  


![](https://camo.githubusercontent.com/8e7cf0a285068cff21acc2a6d67cfaa81d85d184/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f4e253238782532432532302535436d752532432532302535435369676d612532392532302533442532302535436672616325374231253744253742253543737172742537422532383225354370692532392535456e2537432535435369676d61253743253744253744657870253238253543667261632537422d3125374425374232253744253238782532302d2532302535436d75253239253545542532302535435369676d612535452537422d31253744253238782532302d2532302535436d7525323925323925324325323078253230253543657073696c6f6e2532302535436d6174686262253742522537442535452537426e253744)  
где  𝜇 ∈ ℝ - математическое ожидание (центр), а  𝛴 ∈ ℝ - ковариационная матрица (симметричная, невырожденная, положительно определённая).  
Алгоритм заключается в том, чтобы найти неизвестные параметры 𝜇 и  𝛴  для каждого класса y и подставить их в формулу оптимального байесовского классификатора. В отличие от линейного дискриминанта Фишера(ЛДФ), в данном алгоритме мы предполагаем, что ковариационные матрицы не равны. 
Оценка параметров нормального распределения производится на основе параметров функций правдоподобия:  
![](https://camo.githubusercontent.com/94f89606a1238459d5fd563685797efb5709e35a/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f2535436d755f7925323025334425323025354366726163253742312537442537426c5f7925374425354373756d5f253742785f69253341795f6925323025334425323079253744253230785f69)  
![](https://camo.githubusercontent.com/cbc86c5d558986438255e75821f912a7df470d90/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f2535435369676d615f7925323025334425323025354366726163253742312537442537426c5f792532302d2532303125374425354373756d5f253742785f69253341795f6925323025334425323079253744253238785f692532302d2532302535436d755f79253239253238785f692532302d2532302535436d755f7925323925354554)  
Программная реализация восстановления данных параметров:  
Для математического ожидания 𝜇, то есть находим центр нормального распределения элементов класса:  

```diff  

  for (col in 1:cols){
   mu[1, col] = mean(objects[,col])
  }


```  
Для восстановления ковариационной матрицы 𝛴:  

```diff  

  for (i in 1:rows){
    sigma <- sigma + (t(objects[i,] - mu) %*% (objects[i,] - mu)) / (rows - 1)
  }


```     


Результаты работы подстановочного алгоритма:  
1. Здесь по 250 элементов в каждом классе и ковариационные матрицы равны, поэтому разделяющая линия, как видим, вырождается в прямую(данный случай рассматривается в алгоритме ЛДФ(Линейный дискриминант Фишера), который мы рассмотрим позже):    
![plugin2](https://user-images.githubusercontent.com/43229815/50246731-300d0880-03e7-11e9-83ef-8cc9b5bfc36d.png) 
2. Здесь 300 элементов в каждом классе. Видим, что эллипсоиды параллельны осям координат:  
Ковариационные матрицы:  
```diff  
Sigma1 <- matrix(c(10, 0, 0, 1), 2, 2)
Sigma2 <- matrix(c(3, 0, 0, 3), 2, 2)

```   
![plugin](https://user-images.githubusercontent.com/43229815/50246455-74e46f80-03e6-11e9-927d-4a930c0684a3.png)  
3. Здесь по 70 эелементов каждом классе. Ковариационные матрицы не равны, разделяющая плотность является квадратичной и прогибается таким образом, что менее плотный класс охватывает более плотный.  
Ковариационные матрицы:  
```diff  
Sigma1 <- matrix(c(3, 0, 0, 1), 2, 2)
Sigma2 <- matrix(c(10, 0, 0, 15), 2, 2)
``` 
![plugin1](https://user-images.githubusercontent.com/43229815/50246590-df95ab00-03e6-11e9-98de-0d1028f0c137.png)



Линейный дискриминант Фишера
---------------------------------  
Алгоритм ЛДФ отличается от подстановочного алгоритма тем, что ковариационые матрицы классов равны, поэтому для их восстановления необходимо использовать все объекты выборки. В этом случае разделяющая кривая вырождается в прямую.  
Если оценить неизвестную 𝛴(ковариационная матрица, то есть их равенство), с учетом смещенности, то получим следующую формулу:  
![](https://camo.githubusercontent.com/a8d4c8e1eabfffb775b2e63c6e113c9e8e0f54ed/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f25354373756d2535452537422d2537442673706163653b3d2673706163653b25354366726163253742312537442537426c2d2537435925374325374425354373756d5f253742693d312537442535456c2673706163653b28785f692673706163653b2d2673706163653b2535436d752535452537422d2537445f253742795f692537442928785f692673706163653b2d2673706163653b2535436d752535452537422d2537445f253742795f692537442925354554)  
Восстановление ковариационных матриц в коде алгоритма:  
```diff  


    for (i in 1:rows1){
        sigma = sigma + (t(points1[i,] - mu1) %*% (points1[i,] - mu1))
	}

    for (i in 1:rows2){
        sigma = sigma + (t(points2[i,] - mu2) %*% (points2[i,] - mu2))
	}


```  
Разделяющая плоскость здается формулой:  
![](https://camo.githubusercontent.com/855779b58e3e1dcbdb989877d5fd3232da1d55f2/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f253543616c70686125323078253545542532302b2532302535436265746125323025334425323030),   
коэффициенты которой находятся следующим образом:  
![](https://camo.githubusercontent.com/863ea89add5cf8957b74c8efb86b0d69ae07c448/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f253543616c7068612532302533442532302535435369676d612535452537422d3125374425323025354363646f742532302532382535436d755f795f312532302d2532302535436d755f795f3225323925354554)  
![](https://camo.githubusercontent.com/65a258474daf14d2a0a47e3ae10e252e78cae132/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f2535436265746125323025334425323025354366726163253742312537442537423225374425323025354363646f742532302535436d755f795f3125323025354363646f742532302535435369676d612535452537422d3125374425323025354363646f742532302535436d755f795f31253545542532302d25323025354366726163253742312537442537423225374425323025354363646f742532302535436d755f795f3225323025354363646f742532302535435369676d612535452537422d3125374425323025354363646f742532302535436d755f795f3225354554)  
Программная реализация данной функции нахождения коэффициентов ЛДФ выглядит следующим образом:  
```diff  
inverseSigma <- solve(Sigma)
alpha <- inverseSigma %*% t(mu1 - mu2)
beta <- (mu1 %*% inverseSigma %*% t(mu1) - mu2 %*% inverseSigma %*% t(mu2)) / 2
```  
  

Результат работы алгоритма выглядит следующим образом:  
![fisher1](https://user-images.githubusercontent.com/43229815/50239683-e9fa7980-03d3-11e9-9951-8c73bc48a399.png)  
Можно сравнить результаты работы алгоритмов с одинаковыми параметрами:

Здесь параметры следующие:  
```diff    
Sigma1 <- matrix(c(2, 0, 0, 2), 2, 2)
Sigma2 <- matrix(c(2, 0, 0, 2), 2, 2)  
```  
Количество элементов в каждом классе: 50.  
1.Подстановочный алгоритм.  
![pl](https://user-images.githubusercontent.com/43229815/50247232-64cd8f80-03e8-11e9-9c26-1206e4936ab5.png)  

2.ЛДФ алгоритм.  
![fisher2](https://user-images.githubusercontent.com/43229815/50240256-575ada00-03d5-11e9-9fb2-ce3a15dc0fbb.png)  
Видим, что превосходство ЛДФ очевидно. При чем, я заметила, что при малом количестве элементов в каждом классе ЛДФ превосходит подстановочный алгоритм. Чем меньше элементов, тем хуже работает подстановочный алгоритм.  

Линейные алгоритмы классификации
======================================
Пусть ![](https://camo.githubusercontent.com/3612865dfb433fc39b8e49eb768f1afeced3cb41/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f582532302533442532302535436d6174686262253742522537442535452537426e253744253243253230592532302533442532302535436c6566742532302535432537422532302d312533422532302b312532302535437269676874253230253543253744), тогда алгоритм ![](https://camo.githubusercontent.com/d17f42554c08ca0cc4e040a9c288cb66ef4eec6d/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f6125323878253243253230772532392532302533442532307369676e2532302533437725324325323078253345) называется линейным алгоритмом.  
В данном пространстве классы разделяет гиперплоскость, которая задается уравнением:![](https://camo.githubusercontent.com/4760b2d7ccf75c8945f628d83440131cbf7b20a8/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f2535436c616e676c65253230772532437825323025354372616e676c6525334430).  
Если x находится по одну сторону гиперплоскости с её направляющим вектором w, то объект x относится к классу +1, в противном случае - к классу -1.  


Эмпирический риск представлен следующей формулой: ![](https://camo.githubusercontent.com/212214890c628ad501d943664cdad24ffb1129df/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f5125323877253243253230582535456c25323925323025334425323025354373756d5f25374269253230253344253230312537442535452537426c2537444c25323825334377253243253230785f69253345795f69253239).  
Для того, чтобы минимизировать его и подобрать оптимальный вектор весов *w*, рекомендуется пользоваться методом стохастического градиента.  
 

Существует величина ![](https://camo.githubusercontent.com/9054359e62df82ec0a86de2ec203f14bbd3cdde8/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f4d5f6925323877253239253344795f692535436c616e676c65253230785f692532437725323025354372616e676c65), которая называется отступом объекта относительно алгоритма клссификации. Если данный отступ отрицательный, тогда алгоритм совершает ошибку.  

*L(M)* - функция потерь.  

Метод стохастического градиента для линейных алгоритмов.  
--------------------------------  
Пусть дана обучающая выборка: множество входных значений X и множество выходящих значений Y, такие что каждому входу xj соответствует yj - выход, j = 1..m.  
Нужно найти вектор параметром *w*, при котором достигается минимум эмпирического риска.  
Для этого применим метод градиентного спуска:  
1.выберем приближенное значение для вектора параметров *w*;  
2.запускаем итерационный процесс, на каждом шаге которого сдвигаемся в сторону, противоположную вектору
градиента 𝑄′(𝑤, 𝑋ℓ)) до тех пор, пока вектор весов 𝑤 не перестанет изменяться, причем вычисления градиента производится не на всех
объектах обучения, а выбирается случайный объект (отсюда и название метода «стохастический»), на основе которого и происходят
вычисления.  
**В зависимости от функции потерь, которая используется в функционале эмпирического риска, будем получать различные
линейные алгоритмы классификации.**  
Рассмотрим только сам итерационный процесс стохастического градиента.  


Для начала нужно инициализировть веса * w_j; j = 0,..., n* и начальную оценку функционала *Q*, запускаем итерационный процесс:  

```diff
# стандартная инициализация весов w
  w <- c(1/2, 1/2, 1/2)

  iterCount <- 0
  # определяем Q
  Q <- 0
  for (i in 1:l) {
    wx <- sum(w * xl[i, 1:n])
    margin <- wx * xl[i, n + 1]
    Q <- Q + lossQuad(margin)
  }
  repeat {

  margins <- array(dim = l)
   for (i in 1:l)
    {
     xi <- xl[i, 1:n]
     yi <- xl[i, n + 1]
     margins[i] <- crossprod(w, xi) * yi ##находим отступ
    }

errorIndexes <- which(margins <= 0) ##инициализируем объекты, на которых выполняется ошибка
    
if (length(errorIndexes) > 0)
{
    # случайным образом выбираем ошибочный объект
    i <- sample(1:l, 1)
    iterCount <- iterCount + 1
    xi <- xl[i, 1:n]
    yi <- xl[i, n + 1]
    
    wx <- crossprod(w, xi)
    margin <- wx * yi
    ex <- lossQuad(margin)
    w <- w - eta * (wx - yi) * xi ##расчитываем градиентный шаг для итерации (здесь правило обновления весов представлено для ADALINE)
    
    Qprev <- Q
    Q <- (1 - lambda) * Q + lambda * ex ##обновляем Q
  
    } else
  {
      break
    }
  }
  return(w) 
}

```  
 
 Значение функционала *Q*:  
![](https://latex.codecogs.com/gif.latex?Q%20%5C%2C%20%7B%3A%3D%7D%20%5C%2C%20%281%20%5C%2C%20-%20%5C%2C%20%5Clambda%29Q%20%5C%2C%20&plus;%20%5C%2C%20%5Clambda%5Cvarepsilon_i) - критеий останова 

```diff
Qprev <- Q
Q <- (1 - lambda) * Q + lambda * ex
}
```   
Повторять все, что вычисляется после инициализации веса и начального значения *Q* пока значение *Q* не стабилизируется и/или веса *w* не перестанут изменяться.  
Ниже я рассмотрела примеры работы метода стохастического градиента для линейных алгоритмов(ADALINE, Персептрон Розенблатта и Логистическая регрессия), изменяя лишь функции потерь и правила обновления весов.   


**1.Адаптивный линейный элемент(ADALINE):**  
![](https://camo.githubusercontent.com/8972321c0046dede7d7689f0f75d795c710e90ea/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f2535436d61746863616c2537424c2537442532384d2532392533442532384d2d31253239253545322533442532382535436c616e676c6525323077253243785f6925323025354372616e676c65253230795f692d3125323925354532) - это квадратичная функция потерь.  
![](https://camo.githubusercontent.com/aefe5920605d5b7b56b9ffe17f12f8816a79daae/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f77253344772d2535436574612532382535436c616e676c6525323077253243785f6925323025354372616e676c652d795f69253239785f69) - правило обновления весов на каждом шаге итерации метода стохастического градиента. Данное правило получено путем дифференцирования квадратичной функции.  
Программная реализация квадратичной функции потерь:  
```diff    
lossQuad <- function(x)
{
return ((x-1)^2)
} 
```  
Правило обновления весов:  
```diff    
w <- w - eta * (wx - yi) * xi
``` 
**2.Персептрон Розенблатта:**  
![](https://camo.githubusercontent.com/97f2b6593c8f819f61676cb91aefc2a65784a8d4/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f2535436d61746863616c2537424c2537442533442532382d4d2532395f2b2533442535436d61782532382d4d25324330253239) - данную функцию потерь называют кусочно-линейной.  
![](https://camo.githubusercontent.com/90d33699a1b3302dc1879c2c7eb823ff940b63f0/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f2535437465787425374269662532302537442535436c616e676c6525323077253243785f6925323025354372616e676c65253230795f6925334330253230253543746578742537422532307468656e25323025374425323077253341253344772b253543657461253230785f69795f69) - правило обновления весов, которое называют правилом Хебба.  
Программная реализация функции потерь:  

```diff    
lossPerceptron <- function(x)
{
return (max(-x, 0))
}
```  
Правило обновления весов:  
```diff    
w <- w + eta * yi * xi
``` 
**3.Логистическая регрессия:**  
![](https://camo.githubusercontent.com/ee365cf43e497d5a900ef9c367bb83d742bdaecc/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f2535436d61746863616c2537424c2537442532384d2532392532302533442532302535436c6f675f32253238312532302b253230652535452537422d4d253744253239) - логистическая функция потерь.  
![](https://camo.githubusercontent.com/c1094cd0e0fefcaa0a2608da4bee189773cd1201/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f77253230253341253344253230772b253543657461253230795f69785f692535437369676d612532382d2535436c616e676c6525323077253243785f6925323025354372616e676c65253230795f69253239) - правило обновления весов, которое называют логистическим, где сигмоидная функция:  
![](https://camo.githubusercontent.com/a169c0ba965ef8fe5740bce9f2cd9d3ce47a5f38/687474703a2f2f6c617465782e636f6465636f67732e636f6d2f7376672e6c617465783f2535437369676d612532387a2532392533442535436672616325374231253744253742312b652535452537422d7a253744253744).  
Программная реализация функции потерь и сигмоидной функции:  
```diff
##Функция потерь
lossLog <- function(x)
{
return (log2(1 + exp(-x)))
}
## Сигмоидная функция
sigmoidFunction <- function(z)
{
return (1 / (1 + exp(-z)))
}
```  
Правило обновления весов:  
```diff
w <- w + eta * xi * yi * sigmoidFunction(-wx * yi)
```  
Сравнение всех трех линейных алгоритмов:  
ADALINE-зеленая линия.  
Персептрон Розенблатта - синяя линия.  
Логистическая регрессия - желтая линия.  
![linear2](https://user-images.githubusercontent.com/43229815/51684305-cfcc4080-1ffc-11e9-8136-1bd463a913cd.png)
  


























  
  
 

