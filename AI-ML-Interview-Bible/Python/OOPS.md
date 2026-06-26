# OOPS (Object-Oriented Programming)

## What is it?
OOP is a programming style based on classes (blueprints) and objects (instances). Four pillars: Inheritance (child gets parent features), Polymorphism (same method, different behavior), Encapsulation (hide internal data), Abstraction (hide complex logic). Magic methods are special methods like `__init__`, `__str__`, `__len__` that make objects behave like built-in types.

## Why do we use it?
OOP makes code reusable, scalable, and organized. In AI/ML, OOP is used to create custom models, datasets, and pipelines. Most interviewers test OOP to see if you can write production-quality code.

## Real-Time Example 1
**Banking System (Finance)**

Scenario: A bank needs to manage different account types.

Step 1: Create base class `Account` with balance and account number.
Step 2: Encapsulate balance with `__balance` and provide getter/setter.
Step 3: Create `SavingsAccount` and `CurrentAccount` using inheritance.
Step 4: Override `withdraw()` method for polymorphism (different rules for each account).
Step 5: Use abstraction to hide interest calculation logic.

```python
class Account:
    def __init__(self, acc_no, balance=0):
        self.acc_no = acc_no
        self.__balance = balance  # encapsulated

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance

    def withdraw(self, amount):
        if amount <= self.__balance:
            self.__balance -= amount
            return True
        return False

class SavingsAccount(Account):
    def withdraw(self, amount):
        if self.get_balance() - amount >= 1000:  # min balance
            return super().withdraw(amount)
        return False
```

## Real-Time Example 2
**E-Commerce Product System (Retail)**

Scenario: Amazon manages different product types with varying pricing.

Step 1: Base class `Product` with name, price, and abstract `get_discounted_price()`.
Step 2: `Electronics` class — discount based on brand.
Step 3: `Clothing` class — discount based on season.
Step 4: Use polymorphism to process all products uniformly in cart.
Step 5: Use magic methods `__str__` and `__repr__` for product display.

```python
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price

    def __str__(self):
        return f"{self.name}: ${self.price:.2f}"

class Electronics(Product):
    def get_discounted_price(self):
        return self.price * 0.9  # 10% off

class Clothing(Product):
    def get_discounted_price(self):
        return self.price * 0.8  # 20% off

products = [Electronics("Laptop", 1000), Clothing("Shirt", 50)]
for p in products:
    print(f"{p.name}: ${p.get_discounted_price():.2f}")
```

## Concept Example 1
**Class and Inheritance**

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

dog = Dog("Buddy")
cat = Cat("Kitty")
print(dog.speak())  # Woof!
print(cat.speak())  # Meow!
```

Input: None.
Output:
```
Woof!
Meow!
```
Explanation: Dog and Cat inherit from Animal. Each child overrides `speak()` to provide its own implementation — this is polymorphism.

## Concept Example 2
**Magic Methods**

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __str__(self):
        return f"Vector({self.x}, {self.y})"

    def __len__(self):
        return 2

v1 = Vector(1, 2)
v2 = Vector(3, 4)
v3 = v1 + v2
print(v3)      # Vector(4, 6)
print(len(v3)) # 2
```

Input: None.
Output:
```
Vector(4, 6)
2
```
Explanation: Magic methods `__add__`, `__str__`, `__len__` make custom objects work with `+`, `print()`, and `len()` just like built-in types.

## Common Mistakes
1. Forgetting `self` as first parameter in instance methods.
2. Confusing class variable (shared) with instance variable (unique).
3. Not calling `super().__init__()` in child class.
4. Thinking private attributes (`__var`) are truly private (they are name-mangled, not truly private).
5. Overusing inheritance instead of composition (favor `has-a` over `is-a`).

## How Interviewers Ask This
1. "Explain the four pillars of OOP with examples."
2. "What is the difference between `classmethod` and `staticmethod`?"
3. "What is method overriding and method overloading?"
4. "How does Python handle multiple inheritance?"
5. "What are magic methods? Give five examples."

## How To Impress The Interviewer
1. Mention the MRO (Method Resolution Order) — Python uses C3 linearization for multiple inheritance.
2. Explain that `@staticmethod` does not receive self/cls, `@classmethod` receives cls, instance method receives self.
3. Know that Python does not have method overloading (last definition wins) but you can use default args or `*args`.
4. Understand composition vs inheritance: prefer composition for flexible code.
5. Mention `abc.ABC` and `@abstractmethod` for creating abstract base classes.

## Most Asked Questions

### Q1: What are the four pillars of OOP?
- Inheritance: Child class gets parent properties. (`class Dog(Animal)`)
- Polymorphism: Same interface, different implementation. (`speak()` method works differently for Dog and Cat)
- Encapsulation: Hide internal data using `__private` attributes and provide getters/setters.
- Abstraction: Hide complex logic, show only necessary features. (Using `abc` module)

### Q2: What is `__init__` and `__str__`?
- `__init__`: Constructor, called when object is created.
- `__str__`: Called by `print()` and `str()`, returns human-readable string.
```python
class Person:
    def __init__(self, name):
        self.name = name
    def __str__(self):
        return f"Person: {self.name}"
```

### Q3: What is the difference between class variable and instance variable?
- Class variable: shared across all instances, defined outside `__init__`.
- Instance variable: unique to each instance, defined with `self` in `__init__`.
```python
class Employee:
    company = "Google"  # class variable
    def __init__(self, name):
        self.name = name  # instance variable
```

### Q4: What is multiple inheritance? Give example.
A class can inherit from multiple parent classes.
```python
class A: pass
class B: pass
class C(A, B): pass  # C inherits from both A and B
```
Real example: Django `ListCreateAPIView` inherits from multiple mixins.

### Q5: What is MRO?
Method Resolution Order defines the order in which Python looks for methods. Use `ClassName.__mro__` to see it. Python uses C3 linearization.
```python
print(C.__mro__)  # (<class 'C'>, <class 'A'>, <class 'B'>, <class 'object'>)
```

### Q6: What is method overriding?
Child class provides a new implementation for a method inherited from parent.
```python
class Parent:
    def show(self): print("Parent")

class Child(Parent):
    def show(self): print("Child")  # overrides
```

### Q7: What is the difference between `@staticmethod` and `@classmethod`?
- `@staticmethod`: does not receive self or cls, just a regular function inside class.
- `@classmethod`: receives cls as first parameter, can access/modify class state.
```python
class MyClass:
    @staticmethod
    def util(): return "static"

    @classmethod
    def factory(cls): return cls()
```

### Q8: What are magic/dunder methods?
Special methods with double underscores. Examples: `__init__`, `__str__`, `__repr__`, `__len__`, `__add__`, `__eq__`, `__getitem__`.
Real example: `__getitem__` makes objects support `obj[key]` syntax.

### Q9: What is encapsulation? How is it achieved?
Encapsulation hides internal data using `__var` (name mangling). Access via getters/setters or `@property`.
```python
class Bank:
    def __init__(self):
        self.__balance = 0  # private

    @property
    def balance(self):
        return self.__balance
```

### Q10: What is abstraction? How do you create abstract classes?
Abstraction hides implementation details. Use `abc` module.
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self): pass

class Circle(Shape):
    def area(self): return 3.14 * r**2
```
Cannot instantiate `Shape` directly — must subclass it.

### Q11: What is `super()`?
`super()` calls a method from the parent class. Used in `__init__` to avoid repeating code.
```python
class Child(Parent):
    def __init__(self):
        super().__init__()  # calls Parent.__init__
```

### Q12: What is composition? Example?
Composition means a class "has-a" object of another class (vs inheritance "is-a").
```python
class Engine:
    pass

class Car:
    def __init__(self):
        self.engine = Engine()  # composition
```

### Q13: Can you create a class without `__init__`?
Yes. If no `__init__` is defined, Python uses the parent's or `object.__init__`.

### Q14: What is the difference between `isinstance()` and `issubclass()`?
- `isinstance(obj, Class)` — checks if obj is an instance of Class.
- `issubclass(Child, Parent)` — checks if Child is a subclass of Parent.

### Q15: What is the diamond problem in multiple inheritance?
When class D inherits from B and C, which both inherit from A. Python resolves it using MRO — calls A only once.

### Q16: Explain `__new__` vs `__init__`.
- `__new__`: Creates the object (called first, returns instance).
- `__init__`: Initializes the object (called second, returns None).
Used in Singleton pattern.

### Q17: What is a singleton class?
A class that can have only one instance.
```python
class Singleton:
    _instance = None
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```
Real example: Database connection manager.

### Q18: What is `__slots__`?
Reduces memory by preventing `__dict__` creation. Only declared attributes are allowed.
```python
class Point:
    __slots__ = ('x', 'y')
    def __init__(self, x, y):
        self.x, self.y = x, y
```

### Q19: What is method overloading? Does Python support it?
Method overloading = same name, different parameters. Python does NOT support it — last definition of method wins. Use default arguments instead:
```python
def add(a, b, c=0): return a + b + c
```

### Q20: What is the difference between `__repr__` and `__str__`?
- `__repr__`: unambiguous representation for debugging (ideally eval-like).
- `__str__`: readable representation for end users. Falls back to `__repr__` if not defined.
