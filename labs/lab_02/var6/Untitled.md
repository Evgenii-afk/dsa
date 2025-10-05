---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.17.3
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

### Вариант 6

## Сортировка расческой (Comb Sort)

# 1. Классификация сортировки расчесткой

Сортировка расчесткой является внутренней сортировкой, сортирующей элементы в оперативной памяти. Она относится к адаптивным алгоритмам, так как учитывает уже относительно отсортированные участки массива. Это сортировка на месте (on place), не требующая дополнительной памяти, кроме переменных для индексов и шага. По устойчивости сортировка неустойчивая, поскольку при обмене элементов порядок одинаковых ключей может изменяться. По сложности в худшем и среднем случаях время работы близко к $O(n^2)$, но за счет использования "расчески" значительно быстрее пузырьковой сортировки, особенно на больших массивах.

# 2. Теоретическое описание сортировки расчесткой

Сортировка расчесткой улучшает сортировку пузырьком, уменьшая вероятность "залипания" мелких элементов в конце массива. Основная идея — использовать меняющийся интервал (gap) сравнения пар элементов, с шагом уменьшающимся примерно в 1.3 раза после каждой итерации, пока не дойдет до 1. При каждом проходе элементы, находящиеся на расстоянии gap, сравниваются и при необходимости меняются местами. Как только gap достигает 1, сортировка превращается в обычную пузырьковую, но к этому моменту массив уже сильно упорядочен, что ускоряет сортировку.

# 3. Блок-схема сортировки расчесткой

![](comb.jpeg)

Пример основных шагов алгоритма:

- Начать с gap = размер массива / 1.3 (обычно)
- Пока gap > 1 или были перестановки:
    - Сравнить элементы на расстоянии gap, при необходимости поменять
    - Уменьшить gap (gap = gap / 1.3)
- Конец

# 4. Псевдокод сортировки расчесткой

```python
def comb_sort(arr):
    gap = len(arr)
    shrink = 1.247  # оптимальный коэффициент
    sorted = False

    while not sorted:
        gap = int(gap / shrink)
        if gap <= 1:
            gap = 1
            sorted = True
        i = 0
        while i + gap < len(arr):
            if arr[i] > arr[i + gap]:
                arr[i], arr[i + gap] = arr[i + gap], arr[i]
                sorted = False
            i += 1
    return arr

```

# 5. Достоинства и недостатки

**Достоинства:**

- Быстрее пузырьковой сортировки благодаря уменьшению инвертированных пар с большим шагом.
- Простая реализация.
- Работает на месте, не требует дополнительной памяти.

**Недостатки:**

- Неустойчивая сортировка.
- Сложность в худшем случае близка к $O(n^2)$, что медленнее более сложных алгоритмов (быстрой или поразрядной сортировки).
- Эффективность зависит от коэффициента уменьшения gap (обычно 1.3).

***

# 6. Реализовать алгоритмы сортировки согласно номеру индивидуального варианта.

```python
import random
import usage_time
import matplotlib.pyplot as plt
import numpy as np

def comb_sort(arr):
    # ваша функция сортировки
    gap = len(arr)
    shrink = 1.247
    sorted = False
    while not sorted:
        gap = int(gap / shrink)
        if gap <= 1:
            gap = 1
            sorted = True
        i = 0
        while i + gap < len(arr):
            if arr[i] > arr[i + gap]:
                arr[i], arr[i + gap] = arr[i + gap], arr[i]
                sorted = False
            i += 1
    return arr

%store comb_sort
```

# 7 Протестировать корректность реализации алгоритма
# 8 Провести ручную трассировку алгоритма

```python
def quick_comb_sort_debug(arr):
    gap = len(arr)
    shrink = 1.247
    step = 0
    
    print(f"Начало: {arr}")
    
    while True:
        gap = max(1, int(gap / shrink))
        step += 1
        print(f"\nШаг {step}, gap={gap}:")
        
        swapped = False
        for i in range(len(arr) - gap):
            if arr[i] > arr[i + gap]:
                arr[i], arr[i + gap] = arr[i + gap], arr[i]
                swapped = True
                print(f"  Меняем {arr[i+gap]}↔{arr[i]}: {arr}")
        
        if gap == 1 and not swapped:
            break
            
    print(f"\nФинальный результат: {arr}")
    return arr

# Быстрая проверка
test = [6, 2, 8, 1, 5, 3]
print("Быстрая проверка сортировки:")
quick_comb_sort_debug(test.copy())
```

# Провести сравнение указанных алгоритмов сортировки массивов, содержащих n1, n2, n3 и n4 элементов.
# Каждую функцию сортировки вызывать трижды: для сортировки упорядоченного массива, массива, упорядоченного в обратном порядке и неупорядоченного массива. Сортируемая последовательность для всех методов должна быть одинаковой (сортировать копии одного массива).
# Проиллюстрировать эффективность алгоритмов сортировок по заданному критерию. Построить диаграммы указанных зависимостей.

```python
sizes = [1000, 5000, 10000, 100000]

# Типы массивов
def generate_arrays(size):
    """Генерирует три типа массивов одного размера"""
    # Упорядоченный массив
    sorted_arr = list(range(size))
    
    # Обратный порядок
    reversed_arr = list(range(size, 0, -1))
    
    # Случайный массив
    random_arr = [random.randint(1, size * 10) for _ in range(size)]
    
    return {
        'sorted': sorted_arr,
        'reversed': reversed_arr,
        'random': random_arr
    }

# Измеряем время выполнения
func = usage_time.get_usage_time(ndigits=6)(comb_sort)

# Словарь для хранения результатов
results = {
    'sorted': [],
    'reversed': [], 
    'random': []
}

print("🔍 ИЗМЕРЕНИЕ ВРЕМЕНИ СОРТИРОВКИ РАСЧЕСКОЙ")
print("=" * 70)

for size in sizes:
    print(f"\n📊 Размер массива: {size} элементов")
    print("-" * 50)
    
    # Генерируем массивы одного набора данных
    arrays = generate_arrays(size)
    
    for array_type, arr in arrays.items():
        # Сортируем копию массива
        time_taken = func(arr.copy())
        results[array_type].append(time_taken)
        
        print(f"  {array_type:>8}: {time_taken:.6f} сек")

# ВИЗУАЛИЗАЦИЯ РЕЗУЛЬТАТОВ

# 1. График по типам массивов для каждого размера
plt.figure(figsize=(15, 10))

# Подграфик 1: Время для разных типов массивов
plt.subplot(2, 2, 1)
x_pos = np.arange(len(sizes))
width = 0.25

plt.bar(x_pos - width, results['sorted'], width, label='Упорядоченный', alpha=0.8)
plt.bar(x_pos, results['reversed'], width, label='Обратный порядок', alpha=0.8)
plt.bar(x_pos + width, results['random'], width, label='Случайный', alpha=0.8)

plt.xlabel('Размер массива')
plt.ylabel('Время, секунды')
plt.title('Время сортировки для разных типов массивов')
plt.xticks(x_pos, sizes)
plt.legend()
plt.grid(True, alpha=0.3)

# Подграфик 2: Линейный график
plt.subplot(2, 2, 2)
for array_type, times in results.items():
    plt.plot(sizes, times, 'o-', linewidth=2, markersize=6, label=array_type)

plt.xlabel('Размер массива')
plt.ylabel('Время, секунды')
plt.title('Зависимость времени от размера массива')
plt.xscale('log')
plt.yscale('log')
plt.legend()
plt.grid(True, alpha=0.3)

# Подграфик 3: Отношение времени для разных типов
plt.subplot(2, 2, 3)
ratios_reversed = [rev/srt for srt, rev in zip(results['sorted'], results['reversed'])]
ratios_random = [rnd/srt for srt, rnd in zip(results['sorted'], results['random'])]

plt.plot(sizes, ratios_reversed, 's-', label='Обратный/Упорядоченный')
plt.plot(sizes, ratios_random, 'o-', label='Случайный/Упорядоченный')
plt.axhline(y=1, color='red', linestyle='--', alpha=0.5)

plt.xlabel('Размер массива')
plt.ylabel('Отношение времени')
plt.title('Относительная эффективность')
plt.legend()
plt.grid(True, alpha=0.3)

# Подграфик 4: Таблица результатов
plt.subplot(2, 2, 4)
plt.axis('off')

# Создаем таблицу
table_data = []
headers = ['Размер'] + [f'{size}' for size in sizes]

for array_type in ['sorted', 'reversed', 'random']:
    row = [array_type] + [f'{time:.4f}' for time in results[array_type]]
    table_data.append(row)

table = plt.table(cellText=table_data,
                  colLabels=headers,
                  cellLoc='center',
                  loc='center',
                  bbox=[0, 0, 1, 1])

table.auto_set_font_size(False)
table.set_fontsize(10)
table.scale(1, 2)

plt.title('Таблица результатов (секунды)')

plt.tight_layout()
plt.show()

# 2. Отдельные графики для каждого типа массива
plt.figure(figsize=(15, 5))

colors = {'sorted': 'green', 'reversed': 'red', 'random': 'blue'}

for i, array_type in enumerate(['sorted', 'reversed', 'random'], 1):
    plt.subplot(1, 3, i)
    plt.plot(sizes, results[array_type], 'o-', color=colors[array_type], 
             linewidth=2, markersize=8)
    
    # Добавляем значения на точки
    for size, time in zip(sizes, results[array_type]):
        plt.annotate(f'{time:.4f}с', (size, time), 
                    textcoords="offset points", xytext=(0,10), 
                    ha='center', fontsize=8)
    
    plt.xlabel('Размер массива')
    plt.ylabel('Время, секунды')
    plt.title(f'Тип: {array_type}')
    plt.grid(True, alpha=0.3)

plt.tight_layout()
plt.show()

# ВЫВОД СТАТИСТИКИ
print("\n📈 СТАТИСТИЧЕСКИЙ АНАЛИЗ")
print("=" * 70)

for array_type in ['sorted', 'reversed', 'random']:
    times = results[array_type]
    print(f"\n{array_type.upper():>15}:")
    for size, time in zip(sizes, times):
        print(f"  {size:6d} эл.: {time:8.4f} сек")
    
    # Вычисляем коэффициент роста
    if len(times) > 1:
        growth = times[-1] / times[0]
        print(f"  Рост времени ({sizes[-1]}/{sizes[0]}): {growth:.2f}x")

# Сравнение эффективности
print(f"\n⚡ СРАВНЕНИЕ ЭФФЕКТИВНОСТИ:")
print(f"Упорядоченный массив сортируется в {results['reversed'][-1]/results['sorted'][-1]:.2f} раза медленнее")
print(f"Случайный массив сортируется в {results['random'][-1]/results['sorted'][-1]:.2f} раза медленнее")
```
