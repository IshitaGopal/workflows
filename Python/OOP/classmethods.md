# Class methods 

- **Regular methods:** automatically takes the instance as the first argument (called *self* by convention).
- **Class methods:** automatically takes class as the first argument (called *cls* by convention). 
    - To make a function into a classmethod, we just need to add the @classmethod decorator. 
    - Class methods can be used as alternative constructors. By convention, these fuction names start with a 'from' (eg: from_string()) 
- **Static methods:** Don't pass anything automatically (neither instance nor the class). 
    - They behave just like regular functions. We use them when there is a logical connection to the class.
    - To make a function into a classmethod, we just add the @staticmethod decorator.
    - If you don't access the instance or class in the method, it likely should be a static method.

```python
# cls is used by convention

class Employee:
    num_of_emps = 0
    raise_amt = 1.04

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.pay = pay

    def fullname(self):
        return '{} {}'.format(self.first, self.last)
    
    # Set class variable 
    @classmethod
    def set_raise_amt(cls, amount): 
        cls.raise_amt = amount 

    # ALternative constructor
    @classmethod
    def from_string(cls, emp_str):
        first, last, pay = emp_str.split('-')
        return cls(first, last, pay)

    # Static method 
    # Return if its a workday
    @staticmethod
    def is_workday(day):
        if day.weekday == 5 or day.weekday == 6:
            return False
        return True

# Class method example
# Working with the class instead of the instance
Employee.set_raise_amt(1.05)

# Alternative constructors 
emp_str_1 = 'John-Doe-70000'
new_emp_1 = Employee.from_string(emp_str_1)

# Static method example 
my_date = datetime.date(2016, 7, 11)
Employee.is_workday(my_date)


```

# Inhertiance

# Decorators