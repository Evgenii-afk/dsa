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

### Криптоалгоритмы
## Шифр Виженера

```python
import random
import math
from typing import Tuple, List

class VigenereCipher:
    
    def __init__(self):
        self.alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
        self.alphabet_lower = 'abcdefghijklmnopqrstuvwxyz'
    
    def prepare_key(self, text: str, key: str) -> str:
        key = key.upper()
        prepared_key = ""
        key_index = 0
        
        for char in text:
            if char.isalpha():
                prepared_key += key[key_index % len(key)]
                key_index += 1
            else:
                prepared_key += char
                
        return prepared_key
    
    def encrypt(self, plaintext: str, key: str) -> str:
        prepared_key = self.prepare_key(plaintext, key)
        ciphertext = ""
        
        for i, char in enumerate(plaintext):
            if char.isupper():
                shift = self.alphabet.index(prepared_key[i])
                original_pos = self.alphabet.index(char)
                new_pos = (original_pos + shift) % 26
                ciphertext += self.alphabet[new_pos]
            elif char.islower():
                shift = self.alphabet.index(prepared_key[i].upper())
                original_pos = self.alphabet_lower.index(char)
                new_pos = (original_pos + shift) % 26
                ciphertext += self.alphabet_lower[new_pos]
            else:
                ciphertext += char
                
        return ciphertext
    
    def decrypt(self, ciphertext: str, key: str) -> str:
        prepared_key = self.prepare_key(ciphertext, key)
        plaintext = ""
        
        for i, char in enumerate(ciphertext):
            if char.isupper():
                shift = self.alphabet.index(prepared_key[i])
                original_pos = self.alphabet.index(char)
                new_pos = (original_pos - shift) % 26
                plaintext += self.alphabet[new_pos]
            elif char.islower():
                shift = self.alphabet.index(prepared_key[i].upper())
                original_pos = self.alphabet_lower.index(char)
                new_pos = (original_pos - shift) % 26
                plaintext += self.alphabet_lower[new_pos]
            else:
                plaintext += char
                
        return plaintext


def test_vigenere_basic():
    cipher = VigenereCipher()
    
    test_cases = [
        ("HELLO", "KEY", "RIJVS"),
        ("ATTACKATDAWN", "LEMON", "LXFOPVEFRNHR"),
        ("Python", "SECRET", "Hmzgwb"),
        ("Hello World!", "KEY", "Rijvs Uyvjn!"),
    ]
    
    print("Basic Vigenere Tests:")
    for plaintext, key, expected in test_cases:
        encrypted = cipher.encrypt(plaintext, key)
        decrypted = cipher.decrypt(encrypted, key)
        print(f"'{plaintext}' -> '{encrypted}' -> '{decrypted}' : {encrypted == expected and decrypted == plaintext}")


def test_vigenere_edge_cases():
    cipher = VigenereCipher()
    
    edge_cases = [
        ("", "KEY", ""),
        ("123!@#", "KEY", "123!@#"),
        ("AaBbCc", "KEY", "KeFfIi")
    ]
    
    print("Edge Cases Tests:")
    for plaintext, key, expected in edge_cases:
        encrypted = cipher.encrypt(plaintext, key)
        decrypted = cipher.decrypt(encrypted, key)
        print(f"'{plaintext}' -> '{encrypted}' -> '{decrypted}' : {decrypted == plaintext}")


def test_vigenere_comprehensive():
    cipher = VigenereCipher()
    
    texts = [
        "The quick brown fox jumps over the lazy dog",
        "CRYPTOGRAPHY IS IMPORTANT",
        "Python Programming 2024",
        "Hello! How are you? I'm fine, thanks."
    ]
    
    keys = ["KEY", "SECRET", "PASSWORD", "CRYPTO"]
    
    print("Comprehensive Tests:")
    all_passed = True
    for text in texts:
        for key in keys:
            encrypted = cipher.encrypt(text, key)
            decrypted = cipher.decrypt(encrypted, key)
            if decrypted != text:
                print(f"FAIL: '{text}' with key '{key}'")
                all_passed = False
    
    if all_passed:
        print("All comprehensive tests passed")


if __name__ == "__main__":
    test_vigenere_basic()
    print()
    test_vigenere_edge_cases()
    print()
    test_vigenere_comprehensive()
```

### Криптосистема Пэйе

```python
class PaillierCryptosystem:
    
    def __init__(self, key_length: int = 64):
        self.key_length = key_length
    
    def gcd(self, a: int, b: int) -> int:
        while b:
            a, b = b, a % b
        return a
    
    def lcm(self, a: int, b: int) -> int:
        return abs(a * b) // self.gcd(a, b)
    
    def mod_inverse(self, a: int, m: int) -> int:
        def extended_gcd(a, b):
            if a == 0:
                return b, 0, 1
            gcd, x1, y1 = extended_gcd(b % a, a)
            x = y1 - (b // a) * x1
            y = x1
            return gcd, x, y
        
        gcd, x, _ = extended_gcd(a % m, m)
        if gcd != 1:
            raise ValueError("Inverse doesn't exist")
        return (x % m + m) % m
    
    def is_prime(self, n: int, k: int = 5) -> bool:
        if n < 2:
            return False
        if n in (2, 3):
            return True
        if n % 2 == 0:
            return False
        
        s, d = 0, n - 1
        while d % 2 == 0:
            s += 1
            d //= 2
        
        for _ in range(k):
            a = random.randint(2, n - 2)
            x = pow(a, d, n)
            if x in (1, n - 1):
                continue
            for _ in range(s - 1):
                x = pow(x, 2, n)
                if x == n - 1:
                    break
            else:
                return False
        return True
    
    def generate_prime(self) -> int:
        while True:
            p = random.getrandbits(self.key_length // 2)
            p |= (1 << (self.key_length // 2 - 1)) | 1
            if self.is_prime(p):
                return p
    
    def generate_keys(self) -> Tuple[Tuple[int, int], Tuple[int, int]]:
        p = self.generate_prime()
        q = self.generate_prime()
        
        while p == q:
            q = self.generate_prime()
        
        n = p * q
        lambda_val = self.lcm(p - 1, q - 1)
        
        g = n + 1
        
        n_sq = n * n
        L_val = (pow(g, lambda_val, n_sq) - 1) // n
        mu = self.mod_inverse(L_val, n)
        
        public_key = (n, g)
        private_key = (lambda_val, mu)
        
        return public_key, private_key
    
    def L(self, x: int, n: int) -> int:
        return (x - 1) // n
    
    def encrypt(self, m: int, public_key: Tuple[int, int]) -> int:
        n, g = public_key
        n_sq = n * n
        
        while True:
            r = random.randint(1, n - 1)
            if self.gcd(r, n) == 1:
                break
        
        c = (pow(g, m, n_sq) * pow(r, n, n_sq)) % n_sq
        return c
    
    def decrypt(self, c: int, public_key: Tuple[int, int], private_key: Tuple[int, int]) -> int:
        n, g = public_key
        lambda_val, mu = private_key
        n_sq = n * n
        
        m = (self.L(pow(c, lambda_val, n_sq), n) * mu) % n
        return m
    
    def text_to_numbers(self, text: str) -> List[int]:
        return [ord(char) for char in text]
    
    def numbers_to_text(self, numbers: List[int]) -> str:
        return ''.join(chr(num) for num in numbers)


def test_paillier_basic():
    paillier = PaillierCryptosystem(key_length=32)
    public_key, private_key = paillier.generate_keys()
    
    test_numbers = [42, 123, 255, 1000, 777]
    
    print("Basic Paillier Tests:")
    for num in test_numbers:
        encrypted = paillier.encrypt(num, public_key)
        decrypted = paillier.decrypt(encrypted, public_key, private_key)
        print(f"{num} -> {encrypted} -> {decrypted} : {num == decrypted}")


def test_paillier_text():
    paillier = PaillierCryptosystem(key_length=32)
    public_key, private_key = paillier.generate_keys()
    
    test_texts = ["HELLO", "TEST", "ABC", "123"]
    
    print("Text Paillier Tests:")
    for text in test_texts:
        numbers = paillier.text_to_numbers(text)
        encrypted_numbers = [paillier.encrypt(num, public_key) for num in numbers]
        decrypted_numbers = [paillier.decrypt(enc_num, public_key, private_key) for enc_num in encrypted_numbers]
        decrypted_text = paillier.numbers_to_text(decrypted_numbers)
        print(f"'{text}' -> '{decrypted_text}' : {text == decrypted_text}")


def test_paillier_homomorphic():
    paillier = PaillierCryptosystem(key_length=32)
    public_key, private_key = paillier.generate_keys()
    
    a, b = 10, 20
    
    encrypted_a = paillier.encrypt(a, public_key)
    encrypted_b = paillier.encrypt(b, public_key)
    
    n_sq = public_key[0] * public_key[0]
    encrypted_sum = (encrypted_a * encrypted_b) % n_sq
    
    decrypted_sum = paillier.decrypt(encrypted_sum, public_key, private_key)
    
    print("Homomorphic Addition Test:")
    print(f"{a} + {b} = {decrypted_sum} : {a + b == decrypted_sum}")


def test_paillier_multiple_keys():
    paillier = PaillierCryptosystem(key_length=32)
    
    print("Multiple Keys Test:")
    for i in range(3):
        public_key, private_key = paillier.generate_keys()
        test_num = random.randint(1, 100)
        encrypted = paillier.encrypt(test_num, public_key)
        decrypted = paillier.decrypt(encrypted, public_key, private_key)
        print(f"Key set {i+1}: {test_num} -> {decrypted} : {test_num == decrypted}")


if __name__ == "__main__":
    test_paillier_basic()
    print()
    test_paillier_text()
    print()
    test_paillier_homomorphic()
    print()
    test_paillier_multiple_keys()
```

```python

```
