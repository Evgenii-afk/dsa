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

## Динамическое программирование
### Задача о рюкзаке с дробными предметами (жадный алгоритм)

```python
class Item:
    def __init__(self, value, weight):
        self.value = value
        self.weight = weight
        self.ratio = value / weight

def fractional_knapsack(items, capacity):
    items.sort(key=lambda x: x.ratio, reverse=True)
    total_value = 0.0
    
    for item in items:
        if capacity >= item.weight:
            total_value += item.value
            capacity -= item.weight
        else:
            fraction = capacity / item.weight
            total_value += item.value * fraction
            break
            
    return total_value

items1 = [Item(60, 10), Item(100, 20), Item(120, 30)]
print(fractional_knapsack(items1, 50))  # 240.0

items2 = [Item(60, 10), Item(100, 20)]
print(fractional_knapsack(items2, 15))  # 80.0

items3 = [Item(40, 5), Item(100, 10), Item(50, 5)]
print(fractional_knapsack(items3, 10))  # 90.0

items4 = [Item(10, 2), Item(5, 3), Item(15, 5)]
print(fractional_knapsack(items4, 8))  # 26.666...
```

### Задача о покрытии отрезками (жадный алгоритм) 

```python
def min_segments_to_cover(target, segments):
    if not segments:
        return []
    
    segments.sort()
    result = []
    current_end = target[0]
    i = 0
    
    while current_end < target[1] and i < len(segments):
        best_segment = None
        best_right = current_end
        
        while i < len(segments) and segments[i][0] <= current_end:
            if segments[i][1] > best_right:
                best_right = segments[i][1]
                best_segment = segments[i]
            i += 1
        
        if best_segment is None:
            return []
        
        result.append(best_segment)
        current_end = best_right
    
    return result if current_end >= target[1] else []

segments1 = [(1, 4), (2, 5), (3, 6), (5, 7), (6, 8)]
print(min_segments_to_cover((2, 7), segments1))  # [(2, 5), (5, 7)]

segments2 = [(1, 3), (2, 4), (3, 5)]
print(min_segments_to_cover((0, 5), segments2))  # []

segments3 = [(0, 2), (1, 3), (2, 4), (3, 5)]
print(min_segments_to_cover((1, 4), segments3))  # [(1, 3), (3, 5)]

segments4 = [(0, 1), (0.5, 2), (1.5, 3), (2.5, 4)]
print(min_segments_to_cover((0, 4), segments4))  # [(0.5, 2), (2.5, 4)]
```

### Задача о размене монет (динамическое программирование)

```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], dp[i - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1

print(coin_change([1, 5, 11], 15))  # 3
print(coin_change([2], 3))  # -1
print(coin_change([1, 2, 5], 11))  # 3
print(coin_change([1], 0))  # 0
print(coin_change([1, 3, 4], 6))  # 2
```

### Алгоритм Флойда-Уоршелла (динамическое программирование)

```python
def floyd_warshall(graph):
    n = len(graph)
    dist = [[float('inf')] * n for _ in range(n)]
    
    for i in range(n):
        for j in range(n):
            dist[i][j] = graph[i][j]
        dist[i][i] = 0
    
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    
    return dist

INF = float('inf')
graph1 = [
    [0, 3, INF, 7],
    [8, 0, 2, INF],
    [5, INF, 0, 1],
    [2, INF, INF, 0]
]

result1 = floyd_warshall(graph1)
for row in result1:
    print(row)

graph2 = [
    [0, 5, INF, 10],
    [INF, 0, 3, INF],
    [INF, INF, 0, 1],
    [INF, INF, INF, 0]
]

result2 = floyd_warshall(graph2)
print(result2[0][3])  # 11

graph3 = [
    [0, 1, INF],
    [INF, 0, 1],
    [INF, INF, 0]
]

result3 = floyd_warshall(graph3)
print(result3[0][2])  # 2
```

### Генерация разбиений множества (комбинаторный алгоритм)

```python
def set_partitions(elements):
    if not elements:
        return [[]]
    
    result = []
    first = elements[0]
    
    for smaller in set_partitions(elements[1:]):
        for i in range(len(smaller)):
            new_partition = [subset.copy() for subset in smaller]
            new_partition[i].append(first)
            result.append(new_partition)
        
        new_partition = [subset.copy() for subset in smaller]
        new_partition.append([first])
        result.append(new_partition)
    
    return result

# Тесты
print(set_partitions([1, 2]))
print(set_partitions([1, 2, 3]))
print(len(set_partitions([1, 2, 3, 4])))  # 15
print(len(set_partitions([])))  # 1
```

### Оптимизация многомерной функции (генетический алгоритм)

```python
import random
import math

def genetic_algorithm_multidim(dim=2):
    POP_SIZE = 50
    GENERATIONS = 100
    MUTATION_RATE = 0.1
    
    def fitness(x):
        return -sum(xi**2 for xi in x)
    
    def random_individual():
        return [random.uniform(-10, 10) for _ in range(dim)]
    
    population = [random_individual() for _ in range(POP_SIZE)]
    
    for _ in range(GENERATIONS):
        population.sort(key=fitness, reverse=True)
        parents = population[:POP_SIZE//2]
        
        new_population = parents.copy()
        while len(new_population) < POP_SIZE:
            p1, p2 = random.sample(parents, 2)
            child = [(p1[i] + p2[i]) / 2 for i in range(dim)]
            
            if random.random() < MUTATION_RATE:
                idx = random.randint(0, dim-1)
                child[idx] += random.gauss(0, 1)
            
            new_population.append(child)
        
        population = new_population
    
    best = max(population, key=fitness)
    return best, fitness(best)

# Тесты
best1, fit1 = genetic_algorithm_multidim(2)
print(f"2D: {best1}, fitness: {fit1}")

best2, fit2 = genetic_algorithm_multidim(3)
print(f"3D: {best2}, fitness: {fit2}")

def sphere_function(x):
    return -sum(xi**2 for xi in x)

test_point = [0.1, 0.2, 0.3]
print(f"Test fitness: {sphere_function(test_point)}")
```

```python

```
