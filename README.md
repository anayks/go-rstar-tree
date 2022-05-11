rstar_treego
=======

Программа разработана на языке Go.
За основу взята библиотека dhconnelly. 
(Огромное спасибо за труд, dhconnelly!)

[![Author of the basic R-tree](github.com/dhconnelly/)](https://github.com/dhconnelly/)

О библиотеке:
-----

Если Вы знакомы с R-tree и не знакомы с R*-tree, объясню простымы словами.

R*-tree - это подвид алгоритма R-tree, более эффективное решение 
хранения пространственных данных ДЛЯ ПОИСКА. R*-tree балансируется по
сторонам, уменьшает перекрытие между областями и уменьшает площадь
покрытия веток и деревьев, чтобы можно было искать эффективно.

Для изменений данных в дереве требуется, по сравнению с обычным R-tree, 
больше времени, одна поиск, наоборот, занимает меньше времени.

Начало работы
---------------

Скачать исходный код с помощью [GitHub](https://github.com/anayks/go-rstar-tree) или
с помощью одной установки Go, запустите `go get github.com/anayks/go-rstar-tree`.

Убедитесь, что вы импортировали `import github.com/anayks/go-rstar-tree` в ваших исходных файлах.

Документация
-------------

### Запросы поиска и вставка в дерево

Чтобы создать новое дерево, используйте функцию, в которой идут параметры:
	1. Количество измерений
	2. Минимальное количество элементов на ветке
	3. Максимальное количество элементов на ветке

  rt := rtreego.NewTree(2, 25, 50)

Любой тип данных, который имплементирует интерфейс `Spatial` может быть помещен в дерево:

	type Spatial interface {
		Bounds() *Rect
	}

`Rect` (прямоугольник) это структура данных для предоставления пространственных данных, 
в то время как `Point` (точка) показывает лишь пространственно положение. 
Создать `Point` легко - это просто слайс из `float64`:

	p1 := rtreego.Point{0.4, 0.5}
	p2 := rtreego.Point{6.2, -3.4}

Чтобы создать `Rect`, нужно поместить туда позицию и задать длину его сторон:

	r1, _ := rtreego.NewRect(p1, []float64{1, 2})
	r2, _ := rtreego.NewRect(p2, []float64{1.7, 2.7})

Давайте продемонстрирую, создав и сохранив некоторые тестовые данные

	type Thing struct {
		where *Rect
		name string
	}

	func (t *Thing) Bounds() *Rect {
		return t.where
	}

	rt.Insert(&Thing{r1, "foo"})
	rt.Insert(&Thing{r2, "bar"})

	size := rt.Size() // returns 2

Мы можем помещать данные в любом порядке

  rt.Insert(anotherThing)

Если вы хотите хранить Точку вместо прямоугольника, Вы легко можете
конвертировать точку в прямоугольник, используя метод `ToRect`

	var tol = 0.01

	type Somewhere struct {
		location rtreego.Point
		name string
		wormhole chan int
	}

	func (s *Somewhere) Bounds() *Rect {
		// define the bounds of s to be a rectangle centered at s.location
		// with side lengths 2 * tol:
		return s.location.ToRect(tol)
	}

	rt.Insert(&Somewhere{rtreego.Point{0, 0}, "Где-нибудь", nil})

### Запросы

Запросы поиска по месту требуют использовать `*Rect`. Этот метод вернет
все возможные элементы, которые находятся внутри или хотя бы прикасаются
к указанному прямоугольнику

	bb, _ := rtreego.NewRect(rtreego.Point{1.7, -3.4}, []float64{3.2, 1.9})

	results := rt.SearchIntersect(bb)

### Больше информации

Ссылки
----------

- A. Guttman.  R-trees: A Dynamic Index Structure for Spatial Searching.
  Proceedings of ACM SIGMOD, pages 47-57, 1984.
  http://www.cs.jhu.edu/~misha/ReadingSeminar/Papers/Guttman84.pdf

- N. Beckmann, H .P. Kriegel, R. Schneider and B. Seeger.  The R*-tree: An
  Efficient and Robust Access Method for Points and Rectangles.  Proceedings
  of ACM SIGMOD, pages 323-331, May 1990.
  http://infolab.usc.edu/csci587/Fall2011/papers/p322-beckmann.pdf

- N. Roussopoulos, S. Kelley and F. Vincent.  Nearest Neighbor Queries.  ACM
  SIGMOD, pages 71-79, 1995.
  http://www.postgis.org/support/nearestneighbor.pdf

- R*-tree или индексация геопространственных данных
	rayz_razko, 2 июня 2014 в 19:27
	https://habr.com/ru/post/224965/

- rtreego
	dhconnelly, released 28 Aug 2021
	https://github.com/dhconnelly/rtreego/releases

- R*-tree
	https://en.wikipediam.org/wiki/R*-tree

Авторы
------

R-tree written by [Daniel Connelly](http://dhconnelly.com) (<dhconnelly@gmail.com>).
R*-tree с R-tree модифицирован [Даниловым Кириллом][https://t.me/anaykskd] (<anayks@yandex.ru>)

License
-------

rtreego is released under a BSD-style license, described in the `LICENSE` file.
rstar_treego is released under a BSD-style license too, described in the `LICENSE` file.

Протестировать библиотеку:
------------------------------
Сервер: https://github.com/anayks/rstar_treego-polygon
Клиент: https://github.com/anayks/go-web-client-r-sync
