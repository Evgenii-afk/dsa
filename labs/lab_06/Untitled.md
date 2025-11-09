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

## Лаба 6

## Задание 1

```python
def factorial(n):
    if n == 0:
        return 1
    else:
        return n * factorial(n - 1)

def recursive_series_sum_with_custom_factorial(x, n):
    if n <= 0:
        return 0
    else:
        term = (x**(2*n - 1)) / factorial(n)
        return recursive_series_sum_with_custom_factorial(x, n - 1) + term

x_value = 2
n_value = 5
sum_result = recursive_series_sum_with_custom_factorial(x_value, n_value)
print(f"Значение x: {x_value}")
print(f"Количество членов n: {n_value}")
print(f"Сумма ряда: {sum_result}")

x_value_2 = 1
n_value_2 = 10
sum_result_2 = recursive_series_sum_with_custom_factorial(x_value_2, n_value_2)
print(f"\nЗначение x: {x_value_2}")
print(f"Количество членов n: {n_value_2}")
print(f"Сумма ряда: {sum_result_2}")

```

![]() 


## Задание 2

```python
def factorial_iterative(n):
    if n < 0:
        return 0  
    if n == 0:
        return 1
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result

def iterative_series_sum(x, n):
    total_sum = 0.0
    for i in range(1, n + 1):
        term = (x**(2*i - 1)) / factorial_iterative(i)
        total_sum += term
    return total_sum

x_value = 2
n_value = 5
sum_result = iterative_series_sum(x_value, n_value)
print(f"Значение x: {x_value}")
print(f"Количество членов n: {n_value}")
print(f"Сумма ряда (итеративно): {sum_result}")

x_value_2 = 1
n_value_2 = 10
sum_result_2 = iterative_series_sum(x_value_2, n_value_2)
print(f"\nЗначение x: {x_value_2}")
print(f"Количество членов n: {n_value_2}")
print(f"Сумма ряда (итеративно): {sum_result_2}")
```

```python

```
