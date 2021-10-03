---
layout: post
title:  "A-level CS: Set Inclusion Checker"
date:   2021-10-03
categories: codecraft,A-level CS
---
(For context see [A-level Computer Science]({% post_url 2021-10-01-ALevelComputerScience %}))

This assignment was to write a program that would ask the user for a set of integers and then repeatedly ask for single integers and say whether or not they were included in the original set.

My son completed this at school with no input from me and came up with the following:

```python
def inttest(x):
    try:
        int(x)
    except:
        return 0
    return int(x)

a = []
print('type a collection of integers, and press return after each')
print('To end your collection, enter -1')
while True:
    val = 0
    while True:
        val = inttest(input())
        if val == -1:
            break
        a.append(val)
    while True:
        search = False
        print('Enter an integer to look for ')
        Test = inttest(input())
        for x in range(len(a)):
            if Test == a[x]:
                print('yes, this number is in your collection')
                search = True
        if search == False:
            print('no, this integer is not in your collection')
```

Some observations on this:

* It works!
* Things could be named better - we'd already discussed this after the [first assignment]({% post_url 2021-10-02-ALevelCSTaxCalculator %}).
* The `inttest` function could be simplified so it doesn't do the cast twice. It could also be made more useful by taking a prompt as a parameter and actually getting the input.
* The search could be terminated early with a `break`.
* We could use a dictionary to do the searching.

```python
def getInteger(prompt):
    while True:
        try:
            return int(input(prompt + " "))
        except:
            print("Please enter a valid integer")

collection = {}
print('Type a collection of integers, and press return after each')
print('To end your collection, enter -1')

while True:
    member = getInteger("Enter a integer for your collection")
    if member == -1:
        break
    collection[member] = True

print()
print('Enter integers to look for in your collection')
print('To finish, enter -1')
while True:
    test = getInteger('Enter an integer to look for')
    if test == -1:
        break
    if test in collection:
        print('yes, this number is in your collection')
    else:
        print('no, this integer is not in your collection')
```

Merlyn Kline  
October 2021
