---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.17.3
  kernelspec:
    display_name: ''
    name: ''
---

## Лабораторная работа №3
# Вариант 6
Цели лабораторной работы:
- Изучить, что такое линейный список — структура данных, где элементы связаны ссылками, а не расположены подряд в памяти.
- Научиться создавать и использовать односвязные линейные списки в программе.
- Освоить основные операции со списками: добавление, удаление, поиск элементов.
- Развить навыки работы с динамической памятью при построении и изменении списков.
- Научиться писать функции для обработки списков (например, удаление повторов, разворот, сортировка).
- Приобрести умения структурного и модульного программирования через реализацию этих задач.

# Задание 1 
Версия 1:

```python
from typing import Any, Self
import doctest

class Node:
    def __init__(self, data: Any = None, next: 'Node' = None):
        self.data = data
        self.next = next

    def __repr__(self):
        return f'Node(data={self.data}, next={self.next})'


class SingleLinkedListV1:
    def __init__(self) -> Self:
        self._head = None

    def insert_first_node(self, value: Any) -> None:
        '''Добавить элемент в начало списка'''
        self._head = Node(value, self._head)

    def remove_first_node(self) -> Any:
        '''Удалить первый элемент списка'''
        if self._head is None:
            raise ValueError("Список пуст")
        temp = self._head.data
        self._head = self._head.next
        return temp

    def insert_last_node(self, value: Any) -> None:
        '''Добавить элемент в конец списка'''
        if self._head is None:
            self.insert_first_node(value)
        else:
            current_node = self._head
            while current_node.next is not None:
                current_node = current_node.next
            current_node.next = Node(value)

    def remove_last_node(self) -> Any:
        '''Удалить последний элемент списка'''
        if self._head is None:
            raise ValueError("Список пуст")
        
        if self._head.next is None:
            return self.remove_first_node()
        
        current_node = self._head
        while current_node.next.next is not None:
            current_node = current_node.next
        temp = current_node.next.data
        current_node.next = None
        return temp

    def __repr__(self) -> str:
        return f'SingleLinkedListV1({self._head})'

    def __str__(self):
        node = self._head
        elements = []
        while node:
            elements.append(str(node.data))
            node = node.next
        return 'LinkedList.head -> ' + ' -> '.join(elements) + ' -> None'

```

Версия 2: Добавлены методы поиска, замены и удаления по значению + получение размера

```python
class SingleLinkedListV2(SingleLinkedListV1):
    def get_size(self) -> int:
        '''Вернуть размер списка (сложность O(n))'''
        count = 0
        current = self._head
        while current:
            count += 1
            current = current.next
        return count

    def find_node(self, value: Any) -> Node:
        '''Найти первый узел с заданным значением'''
        current = self._head
        while current:
            if current.data == value:
                return current
            current = current.next
        return None

    def replace_node(self, old_value: Any, new_value: Any) -> bool:
        '''Заменить значение первого найденного узла'''
        node = self.find_node(old_value)
        if node:
            node.data = new_value
            return True
        return False

    def remove_node(self, value: Any) -> bool:
        '''Удалить первый узел с заданным значением'''
        if self._head is None:
            return False
        
        # Если удаляем голову
        if self._head.data == value:
            self.remove_first_node()
            return True
        
        # Поиск элемента для удаления
        current = self._head
        while current.next:
            if current.next.data == value:
                current.next = current.next.next
                return True
            current = current.next
        
        return False
```

Версия 3: Добавлено хранение размера списка для оптимизации

```python
class SingleLinkedListV3(SingleLinkedListV2):
    def __init__(self) -> Self:
        '''Инициализация с счетчиком размера'''
        super().__init__()
        self.size = 0

    def insert_first_node(self, value: Any) -> None:
        '''Добавление в начало с обновлением размера'''
        super().insert_first_node(value)
        self.size += 1

    def insert_last_node(self, value: Any) -> None:
        '''Добавление в конец с обновлением размера'''
        super().insert_last_node(value)
        self.size += 1

    def remove_first_node(self) -> Any:
        '''Удаление из начала с обновлением размера'''
        result = super().remove_first_node()
        self.size -= 1
        return result

    def remove_last_node(self) -> Any:
        '''Удаление из конца с обновлением размера'''
        result = super().remove_last_node()
        self.size -= 1
        return result

    def remove_node(self, value: Any) -> bool:
        '''Удаление узла по значению с обновлением размера'''
        if super().remove_node(value):
            self.size -= 1
            return True
        return False

    def get_size(self) -> int:
        '''Получение размера за O(1)'''
        return self.size
```

Версия 4: Добавлены методы для работы с соседними узлами

```python
class SingleLinkedListV4(SingleLinkedListV3):
    def find_previous_node(self, value: Any) -> Any:
        '''Найти значение предыдущего узла относительно узла с заданным значением'''
        if self._head is None or self._head.data == value:
            return None
        
        current = self._head
        while current.next:
            if current.next.data == value:
                return current.data
            current = current.next
        return None

    def find_next_node(self, value: Any) -> Any:
        '''Найти значение следующего узла относительно узла с заданным значением'''
        node = self.find_node(value)
        if node and node.next:
            return node.next.data
        return None

    def insert_before_node(self, target_value: Any, new_value: Any) -> bool:
        '''Вставить новый узел перед узлом с заданным значением'''
        if self._head is None:
            return False
        
        # Вставка перед головой
        if self._head.data == target_value:
            self.insert_first_node(new_value)
            return True
        
        # Поиск узла, предшествующего целевому
        current = self._head
        while current.next:
            if current.next.data == target_value:
                new_node = Node(new_value, current.next)
                current.next = new_node
                self.size += 1
                return True
            current = current.next
        
        return False

    def insert_after_node(self, target_value: Any, new_value: Any) -> bool:
        '''Вставить новый узел после узла с заданным значением'''
        node = self.find_node(target_value)
        if node:
            new_node = Node(new_value, node.next)
            node.next = new_node
            self.size += 1
            return True
        return False

    def replace_previous_node(self, target_value: Any, new_value: Any) -> bool:
        '''Заменить значение в предыдущем узле относительно узла с заданным значением'''
        if self._head is None or self._head.data == target_value:
            return False  # У головы нет предыдущего узла
        
        current = self._head
        while current.next:
            if current.next.data == target_value:
                current.data = new_value
                return True
            current = current.next
        return False

    def replace_next_node(self, target_value: Any, new_value: Any) -> bool:
        '''Заменить значение в следующем узле относительно узла с заданным значением'''
        node = self.find_node(target_value)
        if node and node.next:
            node.next.data = new_value
            return True
        return False

    def remove_previous_node(self, target_value: Any) -> Any:
        '''Удалить предыдущий узел относительно узла с заданным значением'''
        if self._head is None or self._head.data == target_value:
            return None  # У головы нет предыдущего узла
        
        # Если целевой узел - второй в списке
        if self._head.next and self._head.next.data == target_value:
            return self.remove_first_node()
        
        # Поиск узла, который находится за два до целевого
        current = self._head
        while current.next and current.next.next:
            if current.next.next.data == target_value:
                removed_data = current.next.data
                current.next = current.next.next
                self.size -= 1
                return removed_data
            current = current.next
        return None

    def remove_next_node(self, target_value: Any) -> Any:
        '''Удалить следующий узел относительно узла с заданным значением'''
        node = self.find_node(target_value)
        if node and node.next:
            removed_data = node.next.data
            node.next = node.next.next
            self.size -= 1
            return removed_data
        return None
```

```python

```
