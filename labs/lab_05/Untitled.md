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

## Хеш-функции и хеш-таблицы

## Задание 1:

```python
class HashTableChaining:
    def __init__(self, size=10):
        self.size = size
        self.table = [[] for _ in range(size)]
        self.count = 0
    
    def _hash(self, key):
        return hash(key) % self.size
    
    def insert(self, key, value):
        index = self._hash(key)
        bucket = self.table[index]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return
        
        bucket.append((key, value))
        self.count += 1
        
        if self.count / self.size > 0.7:
            self._rehash()
    
    def search(self, key):
        index = self._hash(key)
        bucket = self.table[index]
        
        for k, v in bucket:
            if k == key:
                return v
        return None
    
    def delete(self, key):
        index = self._hash(key)
        bucket = self.table[index]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                del bucket[i]
                self.count -= 1
                return True
        return False
    
    def _rehash(self):
        old_table = self.table
        self.size *= 2
        self.table = [[] for _ in range(self.size)]
        self.count = 0
        
        for bucket in old_table:
            for key, value in bucket:
                self.insert(key, value)
    
    def __str__(self):
        result = []
        for i, bucket in enumerate(self.table):
            if bucket:
                result.append(f"{i}: {bucket}")
        return "\n".join(result)
```

Тест:

```python
print("=== Тестирование HashTableChaining ===")

ht = HashTableChaining(5)

ht.insert("apple", 1)
ht.insert("banana", 2)
ht.insert("cherry", 3)

assert ht.search("apple") == 1, "Ошибка поиска apple"
assert ht.search("banana") == 2, "Ошибка поиска banana"
assert ht.search("cherry") == 3, "Ошибка поиска cherry"
assert ht.search("unknown") is None, "Ошибка поиска неизвестного ключа"
print("✓ Тест вставки и поиска пройден")

ht.insert("apple", 10)
assert ht.search("apple") == 10, "Ошибка обновления значения"
print("✓ Тест обновления значения пройден")

# Тест удаления
assert ht.delete("banana") == True, "Ошибка удаления существующего ключа"
assert ht.search("banana") is None, "Ошибка: ключ не удален"
assert ht.delete("unknown") == False, "Ошибка удаления несуществующего ключа"
print("✓ Тест удаления пройден")

ht2 = HashTableChaining(1)  
ht2.insert("key1", "value1")
ht2.insert("key2", "value2")
ht2.insert("key3", "value3")

assert ht2.search("key1") == "value1", "Ошибка при коллизии"
assert ht2.search("key2") == "value2", "Ошибка при коллизии"
assert ht2.search("key3") == "value3", "Ошибка при коллизии"
print("✓ Тест коллизий пройден")

print("Все тесты HashTableChaining пройдены успешно!\n")
```

## Задание 2:

```python
class HashTableOpenAddressing:
    def __init__(self, size=10):
        self.size = size
        self.table = [None] * size
        self.count = 0
        self.DELETED = object()  
    
    def _hash(self, key, i=0):
        return (hash(key) + i) % self.size
    
    def insert(self, key, value):
        if self.count / self.size > 0.7:
            self._rehash()
        
        i = 0
        while i < self.size:
            index = self._hash(key, i)
            
            if self.table[index] is None or self.table[index] is self.DELETED:
                self.table[index] = (key, value)
                self.count += 1
                return
            elif self.table[index][0] == key:
                self.table[index] = (key, value)
                return
            
            i += 1
        
        self._rehash()
        self.insert(key, value)
    
    def search(self, key):
        i = 0
        while i < self.size:
            index = self._hash(key, i)
            
            if self.table[index] is None:
                return None
            elif self.table[index] is not self.DELETED and self.table[index][0] == key:
                return self.table[index][1]
            
            i += 1
        
        return None
    
    def delete(self, key):
        i = 0
        while i < self.size:
            index = self._hash(key, i)
            
            if self.table[index] is None:
                return False
            elif self.table[index] is not self.DELETED and self.table[index][0] == key:
                self.table[index] = self.DELETED
                self.count -= 1
                return True
            
            i += 1
        
        return False
    
    def _rehash(self):
        old_table = self.table
        self.size *= 2
        self.table = [None] * self.size
        self.count = 0
        
        for item in old_table:
            if item is not None and item is not self.DELETED:
                self.insert(item[0], item[1])
    
    def __str__(self):
        result = []
        for i, item in enumerate(self.table):
            if item is not None and item is not self.DELETED:
                result.append(f"{i}: {item}")
            elif item is self.DELETED:
                result.append(f"{i}: DELETED")
        return "\n".join(result)
```

Тесты 

```python
print("=== Тестирование HashTableOpenAddressing ===")

# Создаем хеш-таблицу
ht = HashTableOpenAddressing(5)

# Тест вставки и поиска
ht.insert("apple", 1)
ht.insert("banana", 2)
ht.insert("cherry", 3)

assert ht.search("apple") == 1, "Ошибка поиска apple"
assert ht.search("banana") == 2, "Ошибка поиска banana"
assert ht.search("cherry") == 3, "Ошибка поиска cherry"
assert ht.search("unknown") is None, "Ошибка поиска неизвестного ключа"
print("✓ Тест вставки и поиска пройден")

# Тест обновления значения
ht.insert("apple", 10)
assert ht.search("apple") == 10, "Ошибка обновления значения"
print("✓ Тест обновления значения пройден")

# Тест удаления
assert ht.delete("banana") == True, "Ошибка удаления существующего ключа"
assert ht.search("banana") is None, "Ошибка: ключ не удален"
assert ht.delete("unknown") == False, "Ошибка удаления несуществующего ключа"
print("✓ Тест удаления пройден")

# Тест коллизий
ht2 = HashTableOpenAddressing(3)  
ht2.insert("key1", "value1")
ht2.insert("key2", "value2")
ht2.insert("key3", "value3")

assert ht2.search("key1") == "value1", "Ошибка при коллизии"
assert ht2.search("key2") == "value2", "Ошибка при коллизии"
assert ht2.search("key3") == "value3", "Ошибка при коллизии"
print("✓ Тест коллизий пройден")

print("Все тесты HashTableOpenAddressing пройдены успешно!\n")
```

```python

```
