
Задачи классификации
 =======================
 Метрические алгоритмы классификации 
------------------------------------------------------------------------

**Метрические методы обучения** - методы, основанные на анализе сходства объектов. Для формализации понятия сходства вводится функция расстояния между объектами ![](http://latex.codecogs.com/gif.latex?p(x_1,x_2)). Чем меньше расстояние между объектами, тем больше объекты похожи друг на друга. Метрические классификаторы опираются на гипотезу компактности, которая предполагает, что *схожим объектам чаще соответствуют схожие ответы*.

**Опр 1.1.** Метрические алгоритмы классификации с обучающей выборкой ![](https://latex.codecogs.com/gif.latex?%5Cinline%20X%5E%7Bl%7D) относят классифицируемый объект *u* к тому классу *y*, для которого суммарный вес ближайших обучающих объектов максимален.  

Алгоритм k ближайших соседей (kNN)
-------------------------------------
Данный алгоритм классификации относит классифицируемый объект *u* к тому классу *y*, к которому относится большинство из *k* его ближайших соседей.    
Имеется некоторая выборка ![](https://latex.codecogs.com/gif.latex?%5Cinline%20X%5E%7Bl%7D), состоящая из объектов *x(i), i = 1, ..., l* (например, выборка ирисов Фишера), и класифицируемый объект(точка).  
Чтобы посчитать расстояние от классифицируемого объекта до ближайших точек, нужно использовать  
Далее нужно упорядочить выборку
 
Данный алгоритм классификации относит классифицируемый объект *u* к тому классу *y*, к которому относится большинство из *k* его ближайших соседей.  

Ниже представлен итог работы алгоритма: классифицируемая точка покрасилась в цвет того класса, к которому ближе находится.  
![knn](https://user-images.githubusercontent.com/43229815/47093219-6dabb480-d231-11e8-8466-98db0a7b09fd.jpg)

### Преимущества:

1. Простота реализации и возможность введения различных модификаций весовой функции *w(i,u)*.
2. При k, подобранном около оптимального, алгоритм "неплохо" классифицирует.
3. "Прецедентная" логика работы алгоритма.

### Недостатки:
1. Приходится хранить всю обучающую выборку целиком.
2. Максимальная сумма объектов в counts может достигаться в нескольких классах одновременно.
3. "Скудный" набор параметров.
4. Точки, расстояние между которыми одинаково, не все будут учитываться.  

При k = ℓ, алгоритм чрезмерно устойчив и вырождается в константу. Таким образом, крайние значения k нежелательны. На практике оптимальное значение параметра k определяют по критерию скользящего контроля с исключением объектов по одному (leave-one-out, LOO). Для каждого объекта xi ∈ Xℓ проверяется, правильно ли он классифицируется по своим k ближайшим соседям. 

![](https://latex.codecogs.com/gif.latex?LOO%28k%2C%20X%5El%29%3D%5Csum_%7Bi%3D1%7D%5E%7Bl%7D%20%5Ba%28x_i%3B%20X%5El%20%5Csetminus%20%5C%7Bx_i%5C%7D%2C%20k%29%5Cneq%20y_i%5D%20%5Crightarrow%20%5Cmin)    
Если классифицируемый объект xi не исключать из обучающей выборки, то ближайшим соседом xi всегда будет сам xi, и минимальное (нулевое) значение функционала LOO(k) будет достигаться при k = 1.  

*График зависимости LOO от k*:  
![loo](https://user-images.githubusercontent.com/43229815/47098458-efa0db00-d23b-11e8-94ab-1135d9d4020e.png)  

k оптимальное (k_opt) достигается при минимальном LOO (k оптимальное, если оно равно 6).  

Алгоритм 1NN
-----------------------------------
Алгоритм ближайшего соседа(1NN) является самым простым алгоритмом клссификации.  
Данный алгоритм классификации относит классифицируемый объект u к тому классу y, к которому относится его ближайший сосед.
Единственное достоинство этого алгоритма - простота реализации.

***Описание кода***:

Имеется некоторая выборка Xl, состоящая из объектов x(i), i = 1, ..., l (снова выборка ирисов Фишера). 
Необходимо задать функцию расстояния. В качестве функции расстояния использовать обычное евклидово расстояние.  
Далее необходимо отсортировать объекты согласно этого расстояния до классифицируемого объекта.
Реализация весовой функции производится таким образом:

```diff
 distances <- matrix(NA, l, 2) #расстояния от классифицируемого объекта u до каждого i-го соседа  
	      for (i in 1:l)  
		  {         
			distances[i, ] <- c(i, metricFunction(xl[i, 1:n], point)) # сортировка расстояний
		  }  
	 orderedXl <- xl[order(distances[, 2]), ] # сортировка выборки 
```
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
Реализация алгоритма:
--------------------------------
![1nn](https://user-images.githubusercontent.com/43229815/47092627-1a853200-d230-11e8-9544-90b9f41fb3ff.jpg)  

Алгоритм k взвешенных ближайших соседей (wkNN)
----------------------------------------------------------------------  
Имеется некоторая выборка Xl, состоящая из объектов x(i), i = 1, ..., l (в приложенной программе используется выборка ирисов Фишера). Данный алгоритм классификации относит объект u к тому классу y, у которого максимальна сумма весов w_i его ближайших k соседей x(u_i).  
Для оценки близости классифицируемого объекта u к классу y алгоритм wkNN использует следующую функцию:  
![](https://latex.codecogs.com/gif.latex?W%28i%2Cu%29%3D%5Bi%5Cleqslant%20k%5Dw%28i%29),  
 где i -- порядок соседа по расстоянию к классифицируемому объекту u, а w(i) -- строго убывающая функция веса, задаёт вклад i-го соседа в классификацию.  
 

