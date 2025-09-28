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

1.2 сумма

```python
import random
import usage_time
import matplotlib.pyplot as plt

def sum_nums(v: list):
    total = 0
    for num in v:
        total += num
    return total

items = range(1, 10**5 * (20 - 6), 50000)
func = usage_time.get_usage_time()(sum_nums)
times = [
    func([
        random.randint(1, 3)
        for _ in range(n)
    ])
    for n in items
]

plt.plot(items, times, 'bo-')
ax = plt.gca()

plt.title('The execution time of the sum of list numbers algorithm')
ax.set_xlabel('Number of elements')
ax.set_ylabel('Time, sec')
plt.savefig('sum.png')
plt.show()

```

```python

```

1.3 произведение эллементов

```python

```

```python
import random, usage_time
import matplotlib.pyplot as plt


def multiplication_nums(v: list):
    mult = 1
    for num in v:
        mult *= num
    return mult


items = range(1, 10**5 * (20 - 6), 50000)
func = usage_time.get_usage_time()(multiplication_nums)
times = [
    func([
        random.randint(1, 3) 
        for _ in range(n)
    ]) 
    for n in items
]

fig = plt.plot(items, times, 'bo-')
ax = plt.gca()

plt.title('The execution time of the get multiplication of list numbers algorithm')
ax.set_xlabel('Number of elements')
ax.set_ylabel('Time, sec')
plt.savefig('mult.png')
plt.show()
```

```python

```

1.6. поиск минимума простым перебором

```python
import random
import usage_time
import matplotlib.pyplot as plt


def get_min(v: list) -> int:
    min_num = v[0]
    for num in v:
        if num < min_num:
            min_num = num
    return min_num


items = range(1, 10**5 * (20 - 6), 50000)
func = usage_time.get_usage_time()(get_min)
times = [
    sum([
        func([
            random.randint(1, 10)
            for _ in range(n)
        ])
        for _ in range(20)
    ]) / 20
    for n in items
]

plt.plot(items, times, 'bo-')
ax = plt.gca()

plt.title('The execution time of the getting min of list numbers algorithm')
ax.set_xlabel('Number of elements')
ax.set_ylabel('Time, sec')
plt.grid(True)
plt.savefig('min.png')
plt.show()
```

```python

```

1.4. вычисление полинома методом Горнера 

```python
import random
import usage_time
import matplotlib.pyplot as plt


def horner(coeffs: list, x: float) -> float:
    result = 0
    for coef in reversed(coeffs):
        result = result * x + coef
    return result


items = range(1, 10**5 * (20 - 6), 50000)
func = usage_time.get_usage_time()(horner)

times = [
    sum([
        func(
            [random.random() for _ in range(n)],
            random.random()
        )
        for _ in range(20)
    ]) / 20
    for n in items
]

plt.plot(items, times, 'bo-')
ax = plt.gca()

plt.title('Execution time of polynomial evaluation by Horner\'s method')
ax.set_xlabel('Number of polynomial coefficients')
ax.set_ylabel('Time, sec')
plt.grid(True)
plt.savefig('horn.png')
plt.show()
```

```python

```
