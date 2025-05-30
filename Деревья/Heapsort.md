# Алгоритм Heapsort: подробное пошаговое объяснение

## Что такое Heapsort?
**Heapsort** (сортировка кучей) - это эффективный алгоритм сортировки, который использует структуру данных "бинарная куча". Он работает за время O(n log n) и использует O(1) дополнительной памяти.

## Как работает Heapsort?
Алгоритм состоит из двух основных этапов:
1. Построение max-heap из исходного массива
2. Последовательное извлечение максимальных элементов и восстановление свойств кучи

Мы знаем, что корнем max-heap всегда является первый элемент массива, `arr[0]`. Что мы с ним делаем? Мы ставим его на свое место в массиве - на самое последнее место.

Делаем мы это обменом значений: первый элемент меняется с последним.

Теперь максимальный элемент стоит на месте. Однако свойство нашей кучи могло нарушиться. Поэтому нам необходимо провести всплытие вниз нашего корня. 

При этом мы уже будем рассматривать в куче на одну вершину меньше. Похоже на сортировку пузырьком, где с каждым разом мы уменьшали рассматриваемый массив с правой стороны.

Аналогичный процесс повторяется дальше.

### Построение max-heap из исходного массива
Как мы помним, после удаления максимального элемента мы меняли корень нашего дерева. И затем восстанавливали свойство кучи при помощи `heapify_down`. Здесь процесс будет примерно таким же.

```python
def heapsort(arr: list[int]) -> list[int]:
	n = len(arr) # длина переданного массива

	def max_heapify(arr: list[int], heap_size: int, i: int) -> None: # наше всплытие вниз - heapify_down для восстановления свойства кучи
	largest = i  # Изначально считаем текущий узел наибольшим
	left = 2 * i + 1  # Индекс левого потомка
	right = 2 * i + 2  # Индекс правого потомка

	# Если левый потомок существует и больше текущего largest
	if left < heap_size and arr[left] > arr[largest]:
		largest = left

	# Если правый потомок существует и больше текущего largest
	if right < heap_size and arr[right] > arr[largest]:
		largest = right

	# Если largest изменился, меняем местами и рекурсивно проверяем дальше
	if largest != i:
		arr[i], arr[largest] = arr[largest], arr[i]
		max_heapify(arr, heap_size, largest)
```

На основе этого метода мы и будем строить кучу из нашего массива.

Я предоставлю реализацию, а затем объясню:

```python
def build_heap(arr):
	for i in range(n // 2 - 1, -1, -1):
		max_heapify(arr, n, i)
```

Что здесь происходит? Пока видно, что мы вызываем max_heapify несколько раз, примерно половину, если судить по `n // 2 - 1`.

Смысл этой функции такой: 
```
Начиная с последнего нелистового узла проводи всплытие этого узла вниз, и так до корня
```

**Как вообще понять где начинаются листья?** С помощью формулы `n // 2`. Последний уровень начинается с примерно серединного индекса массива. А чтобы получить последний нелистовый узел, необходимо сместиться влево на один, отсюда и -1.

**Почему мы вообще начинаем выполнять всплытие без листовых узлов, да еще и в обратном порядке, т.е. снизу вверх?**

**Во-первых**, **листовые узлы уже являются валидными кучами**: у них нет потомков, а значит и свойство кучи точно выполняется. Мы бы делали лишнюю работу.
**Во-вторых**, начиная с нижних уровней, мы гарантируем, что при обработке каждого узла его поддеревья уже удовлетворяют свойству кучи.

Если начать с корня и идти вперед (`0, 1, 2, ...`), то после обработки узла его поддеревья могут нарушить свойство кучи. А это может привести к повторным всплытиям, что влияет на производительность алгоритма.

### Полная реализация с пояснениями каждой строки:

```python
def heapsort(arr: list[int]) -> list[int]:
    n = len(arr)  # Запоминаем длину массива

    def max_heapify(arr: list[int], heap_size: int, i: int) -> None:
        largest = i  # Изначально считаем текущий узел наибольшим
        left = 2 * i + 1  # Индекс левого потомка
        right = 2 * i + 2  # Индекс правого потомка

        # Если левый потомок существует и больше текущего largest
        if left < heap_size and arr[left] > arr[largest]:
            largest = left

        # Если правый потомок существует и больше текущего largest
        if right < heap_size and arr[right] > arr[largest]:
            largest = right

        # Если largest изменился, меняем местами и рекурсивно проверяем дальше
        if largest != i:
            arr[i], arr[largest] = arr[largest], arr[i]
            max_heapify(arr, heap_size, largest)

    def build_max_heap(arr: list[int]) -> None:
        # Начинаем с последнего нелистового узла (родителя последнего элемента)
        # и идем назад до корня (индекс 0)
        for i in range(n // 2 - 1, -1, -1):
            max_heapify(arr, n, i)

    # Основной алгоритм сортировки:
    build_max_heap(arr)  # Сначала строим max-heap из массива
    
    # Последовательно извлекаем элементы из кучи
    for i in range(n - 1, 0, -1):
        # Перемещаем текущий максимум (корень) в конец
        arr[0], arr[i] = arr[i], arr[0]
        # Восстанавливаем свойство кучи для уменьшенной кучи (исключая отсортированные элементы)
        max_heapify(arr, i, 0)

    return arr
```

## Временная сложность
Heapsort выполняется за O(n log n) времени. Функция `max_heapify `работает за O(log n), что пропорционально оптимальной высоте дерева.

Внутри алгоритма мы вызываем функцию `build_max_heap`, которая работает через вызовы `max_heapify`. Получается, мы вызываем нашу функцию `max_heapify` `n // 2 - 1` раз - для каждого узла, кроме листьев. Пока получается `O( (n // 2 - 1) * log n ) = O(n log n)`.

И после чего идет еще один цикл, где мы как раз-таки сортируем наш массив и опять же вызываем `max_heapify` `O(n)` раз.

Получаем `O(2nlog n) = O(n log n)`.

## Заключение
Heapsort — это **надежный универсальный алгоритм** с отличным балансом между скоростью и потреблением памяти.  Он сортирует массив на месте, потребляя `O(1)` памяти, а его временная сложность даже в худшем случае `O(n log n)`