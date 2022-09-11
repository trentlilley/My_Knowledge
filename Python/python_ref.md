# Table of Contents
[Basics](#basics)
- [Variables](#variables)
- [Strings and Printing](#strings-and-printing)
- [Math](#math)
- [Booleans and Logic](#booleans-and-logic)
- [Lists](#lists)
- [Tuples](#tuples)
- [Dictionaries](#dictionaries)
- [Conditionals and Loops](#conditionals-and-loops)

[Object Oriented Programming](#object-oriented-programming)

# Overview

# Basics

## Variables

- Multiple assignment

```python
# one value, multiple variables
a = b = c = 5

# multiple values, multiple variables
a, b, c = 1, 2, 3

# data types
text: str = "Hello World"
x: int = 20
y: float = 20.5467
z: complex = 1j
is_running: bool = True
data: bytes: = b"Hello World"
result = None

# type casting
text: str = str(3 + 4 - 2)
x: float = float(20)

# check data types
isinstance("hello", int) #false
isinstance(3.342, float) #true
```

## Strings and Printing
- Can use single or double quotes in python
- Recommended to use double quotes for strings and single quotes for single characters

```python
# escaping characters with \
text = "He said, \"I'm thirsty!,\" and left to buy some water"
path = "C:\\src\\package\\program.py"

# treat all chars (except quotes) as non-special chars by using raw mode
text = r"C:\src\package\program.py"
text = r"I automatically escape anything except quotes"

# multi-line strings using triple quotes
"""This string is very long so we need to use
triple quotes in order to ensure this text
does not extend past the right margin of our
code editor"""

# case sensitive search
text = "Hello World"
"hello" in text #false

# case insensitive search
text = "Hello World"
"hello".casefold() in text.casefold() #true

# upper, lower and capitalize
text.upper()
text.lower()
text.capitalize()

# getting length of string
length = len(text)

# accessing characters with index
first_char = text[0]
last_char = text[-1]
last_char = text[length - 1]
first_char = text[-length]

# convert other data types to string
a = 101
a = str(a) # a = "101"

# get index for substring, -1 if not exists
text = "Welcome to Python 101, Python is a great language"
text.find("to") #8
text.find("Java") #-1

# define bounds for substring search
# find(string to search, start index, end index)
# start index is inclusive, end index is exclusive
text.find('P', 8, 17) #11

# check if substring exists or not
"Python" in text #true
"Java" in text #false
"Class" not in text #true

# slicing strings
text = "Hello World"
hello = text[:5]
world = text[6:]
world = text[-5:]
ello = text[1:5]

# concatenating strings
msg = "Hello " + "World"

# cannot concatentate non-strings with strings
# convert to string first
num = 3
msg = "I have " + str(num) + " apples"

# string templates
a = 2
b = 5
print(f"The sum of a and b is {a + b}")

# repeating strings
msg = "Hi"
3 * msg #"HiHiHi"
0 *msg #""

# priting mulitple objects, delimited by space
print("Hello", "World") #Hello World

# print without newline
print("Hello World", end=' ')

# List to string
data = ["Hello", "World"]
print(",".join(data)) #Hello,World

# String replace, all instances
text = "Python is a great language. Python is easy"
text.replace("Python", "Javascript")
  # Javascript is a great language. Javascript is easy"

# String replace, limit to first n instances
text = "Python is a great language. Python is easy"
text.replace("Python", "Javascript", 1)
  # Javascript is a great language. Python is easy"

```
- String templates can be formatted to space contents

```Python
# string templates with formatting
a, b = 3.111, 2.0
print(f"the answer is {(a*b):.2f}") #rounds to 2 decimals

# scientific notation formatting
print(f"{1/7000:.5e}") #1.42857E-04

# allocating char space, right justified
data = [1, 10, 100]
for i in data: print(f"|{i:3}| ")
  # |  1| 
  # | 10| 
  # |100|

# allocating char space, left justified
for i in data: print(f"|{i:<4}| ")
  # |1  | 
  # |10 | 
  # |100|

# allocating char space, center justified
names = ["fred", "al", "thegreathenrythethird", "richard"]
for i in names: print(f"{i:^21}")
```
- String validation in python

```python
# check all chars are alphanumeric
text.isalnum()

# check if all characters are alphabetic
text.isalpha()

# check if all characters are digits
text.isdigit()

# check if all characters are lowercase
text.islower()

# check if all characters are uppercase
text.isupper()

# check if all characters are whitespace
text.isspace()

# check if string starts with substring
text.startswith("substring")
```

- String splitting and processing

```python
# remove whitespace
text = " message     "
text.strip() #"message"
text.rstrip() #" message"
text.lstrip() #"message     "

# split by delimiter
text = "ABC.DEF.GHI"
data = text.split('.') #[ABC, DEF, GHI]

# limit number of splits to n times
text = "ABC.DEF.GHI"
data = text.split('.', 1) #[ABC, DEF.GHI]
```

# Math
```python
# floats are contagious
5 * 3   #15
5 * 3.0 #15.0

# comparisons between floats and ints are not super precise
1_000_000.1 == 1_000_000 #true

## division will always give floats unless // is used
11 / 4 #2.75
11 // 4 #2

## modulo
11 % 4 #3

## easily get both quotient and remainder
q, r = divmod(13, 5) #q=2, r=3

## exponentials
4**3 #64

## absolute value
abs(-78.3) #78.3

## rounding, .5 and below are rounded down
round(2.4) #2
round(2.5) #2
round(2.6) #3

## rounding to specific digits
calculated_price = 209.667846
actual_price = round(calculated_price, 2) #209.67
round(1_234, -1) #1230
round(1_234, -2) #1200 

# maximum and minimum
max(3.45, 3.98) #3.98
max(1, 2, 3, 4, 5) #5
min([1, -2, 3]) #-2

# sum (must use list)
sum([5, 6, 7]) #18

# shorthand
count +=1 #increment 1
```

## Booleans and Logic
- In python, booleans are capitalized

```python
# inverse
not True #false
not False #true

# and
True and True #true
True and False #false
False and True #false
False and False #false

# or
True or True #true
True or False #true
False or True #true
False or False #false

# exclusive or (xor)
True ^ True #false
True ^ False #true
False ^ True #true
False ^ False #false

# check for equality
a = "Hello World"
b = "Hello World"
a == b  #true

# different data types are never equal
a = 10
b = "10"
a == b #false

# check for inequality
a = 2
b = 3
a != b #true

# Empty data types, empty strings, and zeroes are always false
# Populated data are always true
nums1 = []
bool(nums1) #false

nums2 = [1, 2, 3]
bool(nums2) #true

# Performing and/or operation on two data will not return a boolean
# it will instead return the data that returns true
# if both are true the second item will be returned

nums3 = nums1 or nums2 # nums3 assigned nums2

```

## Lists
- All splicing and extracting operations used for strings can be used for lists since strings are treated as lists of characters

```python
ages = [12, 24, 56]

# list unpacking
age_1, age_2, age_3 = ages

# mixed type lists
data = [12, 'F', False, 13, 'C', True]

# get list length
length = len(data)

# get list item
data = [45, 23, 11, 21]
first_number = data[0]
last_number = data[-1]

# concatenate lists
data1, data2 = [1, 2, 3], [4, 5, 6]
data = data1 + data2 # [1, 2, 3, 4, 5, 6]

# append single item to end of list
data.append(7)

# append multiple items to end of list
data.extend([8, 9, 10])

# insert at index
data = [2, 3, 5]
data.insert(2, 4) #2345

# extract at index
first_num = data.pop(1)
last_num = data.pop()

# delete at index
del data[2]

# copy list contents into another
data_cpy = data.copy()

# get index given data, exception if !exists
data = ["muffin", "cream", "bun"]
data.index("cream")

# check if element exists in list
if "cream" in data:
  print("True")

# clear list
data.clear()

# slice list, start inclusive, end exclusive
data = ['a', 'b', 'c', 'd', 'e', 'f']
sub = data[:]     # abcdef
sub = data[2:]    # cdef
sub = data[:2]    # ab
sub = data[::2]   # ace
sub = data[1:4:2] # bd

# reverse copy
data_reversed = data[::-1]

# reverse no copy
data.reverse()

# replace before (changes existing list)
data = [0, 3, 4]
data[:1] = [1, 2] #1234

# replace after (changes existing list)
data = [1, 2, 5]
data[1:] = [3, 4] #1234 

# unpacking
data = [1, 2, 3, 4]
first, second, third = data #1, 2, 3
first, *middle, last = data #1, [2, 3], 4
first, *rest  = data #1, [2, 3, 4]
*rest, last = data #[1, 2, 3], 4

# sort ascending (changes existing list)
data.sort()

# sort descending (changes existing list)
data.sort(reverse=True)

# sorting without changing the list
sort_data = sorted(data)
```

- List comprehension provides concise and advanced processing and creation of lists

```python
brands = ["HP", "Dell", "Acer"]

# copy list
b_cpy = [brand for brand in brands]

# get items that have an 'e' in them
b_cpy = [brand for brand in brands if 'e' in brands]

# copy list but make all items lowercase
b_cpy = [brand.lower() for brand in brands]

# make a list of the first 9 squares
squares = [x**2 for x in range(1, 10)]
  # 1, 4, 9, 16, 25, 36, 49, 64, 81

# make a list of the first 9 odd squares
odd_sqrs = [x**2 for x in range(1, 10) if x % 2 != 0]
  # 1, 9, 25, 49, 81

# make a list of 4 zeroes
zeroes = [0 for i in range(4)]

# make a 4x4 matrix of zeroes
# j is the outer loop, i is the inner loop
mat_4x4 = [[0 for j in range(4)] for i in range(4)]

# make a 5x2 matrix of zeroes

# make a 4x4 identity matrix
def one_zed(i, j): return 1 if i == j else 0
id_mat = [[one_zed(i, j) for j in range(4)] for i in range(4)]

# more concise verison of above
id_mat = [[1 if i == j else 0 for j in range(4)] for i in range(4)]

```

- Zipping lists allows for parallel processing of lists
- the length of a zip is defined by the shortest collection

```python
brands = ["fender", "fender", "gibson", "gibson", "ibanez"]
models = ["Stratocaster", "Telecaster", "Les Paul", "Flying V", "RG"]
years = [1954, 1950, 1952, 1958, 1987]

# without zip, slower
sentences = [f"the {brands[i]} {models[i]} was first introduced in {years[i]}" for i in range(len(brands))]
print(sentences)

# with zip, faster recommended
for brand, model, year in zip(brands, models, years):
  print(f"the {brand} {model} was first introduced in {year}")

```

## Tuples
- Are immutable
- Enclosed in parenthesis but do not have to be
- Muliple assignments actually make use of tuples
- Can be mixed types just like lists

```python
# basic tuple
tup = (1, "joe")

# basic triple
tri = (1, "joe", False)

# basic single
sin = ("joe",)

# items accessed by ordered index
id = tup[0]
name = tup[1]
```

- Enumerate is a more efficient way to loop through a list and collect its index and data as tuples

```python
queue = ["fred", "frank", "bob"]

# without enumerate
for i in range(len(queue)):
  print(f"{queue[i]} is at position {i}")

# with enumerate
for i, person in enumerate(queue):
  print(f"{person} is at position {i}")
```

## Dictionaries
```python
# empty dictionaries
freqs = {}
freqs = dict()

# populated dictionaries
cart = {"lettuce": 1, "tomato": 2, "beef": 1}

# check if key exists
if 'lettuce' in cart:
  print(freqs["lettuce"])

# check if a value exists for given key
cart["lettuce"] is not None
  print("value exists")

# give a default value if value does not exist
cart = {"lettuce": 1, "tomato": 2, "beef": 1, "pepper": None}
item = cart.get("onion", 0)
print(item) # 0

item = cart.get("tomato", 0)
print(item) # 2

item = cart.get("pepper", 0)
print(item) # None

# iterate through keys
for item in cart: print(item)

# iterate through values
for quantity in cart.values(): print(quantity)

# iterate through key value tuples
for item, quantity in cart.items(): print(f"{item}, {quantity}")

# if key does not exist, key and value are added to dict
cart["macaroni"] = 1

# if key exists, existing value is overwritten
cart["lettuce"] = 5  # updated from 1 to 5

# merge two dictionaries
cart1 = {"lettuce": 1, "tomato": 2, "beef": 1, "pepper": None}
cart2 = {"carrot": 3, "brocoli": 3}
combined_cart = {**cart1, **cart2}

# delete a key value pair
del cart["pepper"]

# delete all entries
cart.clear()

```

- A common use for dictionaries is to count frquencies of various data

```python
# histogram generator (long version)
freqs = {}
words = ["get", "haste", "the", "or", "the"]
for w in words:
  if w not in freqs:
    freqs[w] = 1
  else:
    freqs[w] = ferqs[w] + 1
print(freqs)

# histogram generator (concise version) with default 0
freqs = {}
words = ["get", "haste", "the", "or", "the"]
for w in words:
  freqs[w] = freqs.get(w, 0) + 1
print(freqs)
```

- Dictionary comprehensions can be used to create dictionaries and process them in advanced and concise ways

```python

```

# Sets
- Contain no duplicates and elements are not ordered

```python
# empty set
set()

# populated set
names = {"fred", "frank", "bob", "joe"}

# set from list or tuple
set([1, 2, 3, 3])
set(['a', 'd', 'a', 'd'])

# copy set
copy = names.copy()

# check membership
"joe" in names

# add to set
names.add("Gary")

# clear set
names.clear()

# remove single item
names.discard("Gary")

# remove and return random item
rand_name = names.pop()

# test if x is a subset of y
x = {1, 2, 3}
y = {1, 2, 3}

print(x <= y) #true

# test if x is a proper subset of y
# y is not a proper subset of x if y == x
print(x < y) #false

# test if x is a superset of y
x = {1, 2, 3, 4}
y = {1, 2, 3}

print(x >= y) #true

# test if x is a proper superset of y
# false if x == y
print(x > y) #true

# get the union of two sets
x = {1, 2, 3}
y = {3, 4, 5}

union = x | y #{1, 2, 3, 4, 5}
x |= y #x = {1, 2, 3, 4, 5}

# get the intersection of two sets
x = {1, 2, 3}
y = {3, 4, 5}

intersec = x & y # {3}

# get all items in x that are not in y
x = {1, 2, 3}
y = {3, 4, 5}

diff = x - y #{1, 2}

```

# Conditionals and Loops
- else elif and if structure

```python
if num > 0:
  print(f"the number {num} is positive")
elif num < 0:
  print(f"the number {num} is negeative")
else:
  print("the number is zero")
```

- Empty lists, empty strings, empty dictionaries, int 0, and float 0.0 always return false in conditionals
- all other values return true

```python
log = "error thrown at 10:00"
if log:
  print(log)
else:
  print("no log message found")
```

- Loop structures

```python
# while counter loop
counter = 0
while counter <= 100:
  counter += 1

# infinite loop
while True:
  print("Looping infintely")

# for loop, start at iteration 0
for i in range(100):
  print(i)

# for loop, set start and end iteration
iterations = 0
for i in range(5, 100):
  iterations +=1
print(iterations) #95

# for loop, increment by more than 1 each iteration
for i in range(1, 100, 20):
  iterations += 1
print(iterations) #5

# for each loop, break, continue
data = ['A', 'B', 0, 12, 'C']
new_data = []

for item in data:
  if item == 0:
    print("data should not contain 0")
    break
  elif item == 'B':
    print("not adding 'B' to new list")
    continue
  else:
    new_data.append(item)
  
  print(f"added {item} to the list!")

# do for loop
# used to construct lists
[x for x in range(1, 4)] #[1, 2, 3, 4]
[x**2 for x in range(2, 4)] #[4, 9, 16]
```

# Object Oriented Programming

## Functions
- by default, functions can take any value

```python

# simple empty function
def my_function():
  pass

# one parameter and return value
def odd_numbers_less_than_n(n):
  results = []
  for i in range(n):
    if i % 2 == 1:
      results.append(i)
  return results

# packages all parameters into one tuple
def my_function(*numbers):
  for params in numbers:
    print(f"{param} was entered as a parameter to this function")

```

- Many ways to pass arguments in python

```Python
# when there are a large number of args, package
# all of them into a single tuple prefixed by a '*'
def my_function(*numbers):
  for params in numbers:
    print(f"{param} was entered as a parameter to this function")

my_function(2, 4, 6, 8, 10, 12)

# alternatively, package all of them into a dictionary using **
def my_function(**data):
  for key, val in data.items():
    print(f"{key} position is {val}")

my_function(x=1232, y=87, z=-1927)

# can use parameter names when passing args
# allows passing of args out of order
# these are called keyword arguments
def my_function(name, age):
  print(f"{name} is {age} years old")

my_function(age=10, name="Bob")

# force users to supply keyword arguments by including
# a '*' character as the first parameter
def my_function(*, name, age):
  print(f"{name} is {age} years old")

# you can set default args in python
# if mixing default and non-default args, default args must be listed first
def string_transform(text="Hello World", uppercase=False)
  if uppercase:
    return text.upper()
  else:
    return text.lower() # default

```

- Functions are first-class in Python and behave like objects

```Python
# use function name without () to treat as an object
def message():
  print("Hello World")

my_var = message
my_var() # prints "Hello World"

# use lambdas to define anonymous functions
operations = {}

def adder(x, y): return x + y # without lambda
operations["+"] = adder

operations["-"] = lambda x, y: x - y # with lambda

operations["-"](12, 10) # 2

```

## Classes

## Error Handling
- Try Except

```python
try:
  pass
except:

```

## Files and Text Processing
- Working with filepaths

```Python
import os, sys

# get path to current working directory
os.getcwd()

# get path to parent directory
os.path.dirname()

# use r-string to automatically escape file separators
path = r"../output/data"
```

## Sorting
- Selection sort

```python
def sort(items):
  for i in range(0, len(items) - 1):
    for j in range(i + 1, len(items)):
      if items[i] > items[j]:
        items[i], items[j] = items[j], items[i]
  return items

numbers = [3, 2, 1, 4]
sorted = sort(numbers)
print(sorted)
```

## Command Line Arguments
- `import sys` and use `sys.argv` to get a list of all command line args
- by default args are delimited by spaces on the command line
- args always come after the `python` command on the command line
- the first arg is always the name of the file
- for exmaple typing `python main.py Command Line Args` gives 4 args
- They are main.py, Command, Line, Args

```Python
# python main.py Command Line Args
import sys

def main(args: list):
  print(f"{len(args)} found")
  for i, arg in enumerate(args):
    print(f"  [{i}] {arg}")

if __name__ == '__main__':
  main(sys.argv[1:])

""" Prints:
3 args found
  [0] Command
  [1] Line
  [2] Args
"""
```

## Timing Code for Performance
- Many ways to calculate the execution time of a block of code in python
- The first and most intuitive method is to record the time at the line before your code executes, then record the time at the line after your code executes, then subtract the start time from the end time
- Eaiest way is to use the performance counter, there are other clocks like time.time() or time.monotonic() but these offer less precision and reliability for only a negligible performance increase over perf_counter()

```Python
import time

start = time.perf_counter()
my_function()
end = time.perf_counter()
print(f"Function executed in {end - start:0.4f} seconds")
```

- The `timeit` module can be used to test code blocks more rigorously
- It uses time.perf_counter() internally
- It requires a slighly more complicated setup and the arguments you need to pass may be strange on first impression
- *setup*: any imports required to run the code to test, you must enclose this code in a string
- *stmt*: the function you wish to test, you must enclose this code in a string
- *number*: the number of times you want to test the function, important for statistical purposes

```Python
# importing the required modules
import timeit

# binary search function
def binary_search(mylist, find):
	while len(mylist) > 0:
		mid = (len(mylist))//2
		if mylist[mid] == find:
			return True
		else if mylist[mid] < find:
			mylist = mylist[:mid]
		else:
			mylist = mylist[mid + 1:]
	return False


# linear search function
def linear_search(mylist, find):
	for x in mylist:
		if x == find:
			return True
	return False


# compute binary search time
def binary_time():
	SETUP_CODE = '''
from __main__ import binary_search
from random import randint'''

	TEST_CODE = '''
mylist = [x for x in range(10000)]
find = randint(0, len(mylist))
binary_search(mylist, find)'''
	
	# timeit.repeat statement
	times = timeit.repeat(setup = SETUP_CODE,
						stmt = TEST_CODE,
						repeat = 3,
						number = 10000)

	# printing minimum exec. time
	print('Binary search time: {}'.format(min(times)))	


# compute linear search time
def linear_time():
	SETUP_CODE = '''
from __main__ import linear_search
from random import randint'''
	
	TEST_CODE = '''
mylist = [x for x in range(10000)]
find = randint(0, len(mylist))
linear_search(mylist, find)
	'''
	# timeit.repeat statement
	times = timeit.repeat(setup = SETUP_CODE,
						stmt = TEST_CODE,
						repeat = 3,
						number = 10000)

	# printing minimum exec. time
	print('Linear search time: {}'.format(min(times)))

if __name__ == "__main__":
	linear_time()
	binary_time()

```

## Logging
- Can log to console and output file using logging library
- debug: for diagnosing problems (level 10)
- info: confirm that things are working as expected (level 20)
- warning: something unexpected happened but the program still works (level 30)
- error: serious problem, software was unable to perform some function (level 40)
- critical: program crashed (level 50)

```Python
# Logging to console
import logging
logging.info("program started")

# Send logs to output file, do not clear output file after a run
logging.basicConfig(filename='program.log', encoding='utf-8', level=logging.DEBUG)
logging.info("program started")

# Send logs to output file, clear output file after every run
logging.basicConfig(filename='program.log', filemode='w', encoding='utf-8', level=logging.DEBUG)
logging.info("program started")

# Only log events that are labeled as warnings or above
logging.basicConfig(filename='program.log', filemode='w', encoding='utf-8', level=logging.WARNING)
logging.info("program started") # not written to file

# Recommended formatting for log statements
logging.basicconfig(
  format='%(asctime)s | %(levelname)s: %(message)s', 
  datefmt= '%m/%d/%Y %I:%M:%S%p',
  filemode='w'
  encoding='utf-8'
  level=logging.DEBUG
)
  logging.warning("Disk space low")
  # with format: 12/12/2010 11:46:36AM | WARNING: Disk space low
  # without: WARNING:root: Disk space low
```

- Logging across multiple modules
- Set up basicConfig in the main module
- Import logging in all other modules

```Python
# in main module (main.py)
import logging
import mymodule

def main():
  logging.basicConfig(filename='program.log', filemode='w' level=logging.DEBUG)
  logging.info("Program started")
  mymodule.process_data()
  logging.info("Program finished")

if __name__ == '__main__':
  main()
```
```Python
# in mymodule.py
import logging
def process_data():
  logging.info("Processing data...")
```

- Choose the logging level from the command line

```Python
# python main.py --log=critical
import logging
import sys

def main(args):
  log_level = args[1]
  numeric_level = getattr(logging, log_level.upper(), None)
  if not isinstance(numeric_level, int):
    raise ValueError('Invalid log level: %s' % log_level)
  logging.basicConfig(level=numeric_level)
  logging.info("Program started")
  logging.critical("System crashed")


if __name__ = '__main__':
  main(sys.argv)
```

- logging exceptions
- traceback will be included automatically after the message

```Python
try:
  my_broken_function()
except Exception:
  logging.exception("Exception caught")
```

## Multiprocessing
- Takes advantage of multiple cores on a computer
- Use a for loop to construct workers and jobs
- The below code takes roughly 0.5s to execute, without multiprocessing it would take 10 * 0.5 seconds to execute

```Python
import multiprocessing
import time

def task():
    print('Sleeping for 0.5 seconds')
    time.sleep(0.5)
    print('Finished sleeping')

if __name__ == "__main__": 
    start_time = time.perf_counter()
    processes = []

    # Creates 10 processes then starts them
    for i in range(10):
        p = multiprocessing.Process(target = task)
        p.start()
        processes.append(p)
    
    # Joins all the processes 
    for p in processes:
        p.join()

    finish_time = time.perf_counter()

    print(f"Program finished in {finish_time-start_time} seconds")
```

- Using an executor pool

```Python
import concurrent.futures
import time

def cube(x):
    return x**3

if __name__ == "__main__":
    with concurrent.futures.ProcessPoolExecutor(3) as executor:
        start_time = time.perf_counter()
        result = list(executor.map(cube, range(1,100)))
        finish_time = time.perf_counter()
    print(f"Program finished in {finish_time-start_time} seconds")
    print(result)
```



