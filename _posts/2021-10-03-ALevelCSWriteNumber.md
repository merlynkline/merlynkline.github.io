---
layout: post
title:  "A-level CS Write numbers in English"
date:   2021-10-03
categories: codecraft,A-level CS
---
(For context see [A-level Computer Science]({% post_url 2021-10-01-ALevelComputerScience %}))

This assignment was to write a program that would ask the user for an integer in the range 0 to 99 and write that out as English words.

My son's solution treated the input as strings and picked the digits out as characters, and then used a series of `if` statements and a little other logic to write the corresponding words':

```python
def IntTest(x):
    try:
        int(x)
       
    except:
        x = IntTest(input('Please input an integer between 0 and 100 - '))

    return str(x)

def DigitToWord(x):
    if int(x) == 0:
        print('zero')
    if int(x) == 1:
        print('one')
    if int(x) == 2:
    # (... etc... removed for brevity...)
    if int(x) == 19:
        print('nineteen')
    if int(x) == 20:
        print('twenty')
    if int(x[0]) == 2 and int(x) > 20:
        print('twenty',end='-')
        DigitToWord(x[1])
    if int(x) == 30:
        print('thirty')
    if int(x[0]) == 3 and int(x) > 30:
        print('thirty',end='-')
        DigitToWord(x[1])
    if int(x) == 40:
        print('fourty')
    if int(x[0]) == 4 and int(x) > 40:
        print('fourty',end='-')
        DigitToWord(x[1])
    # (... etc... removed for brevity...)
    if int(x) == 80:
        print('eighty')
    if int(x[0]) == 8 and int(x) > 80:
        print('eighty',end='-')
        DigitToWord(x[1])
    if int(x) == 90:
        print('ninety')
    if int(x[0]) == 9 and int(x) > 90:
        print('ninety',end='-')
        DigitToWord(x[1])
    if int(x) == 100:
        print('one-hundred')
   
while True:
    Variable = IntTest(input('input a value between 0 and 100 - '))

    if int(Variable) >=0 and int(Variable) <= 100:
        DigitToWord(Variable)
```

It's interesting that he saw this as a string processing problem rather than a numerical problem. We talked about that and various other things including:

* How look-up tables can be created and employed to simplify the code considerably
* The value of choosing good names for things (again!)
* The idea that avoiding duplication is always good - this program duplicates the names of numbers that are multiples of 10 and the `if` statements are very repetitive
* The [importance of maintainability]({% post_url 2016-11-21-GoodCodeIsBroken %}) and how this is impacted by these other points

To illustrate all that, we looked at an alternative solution:

```python
smallNumber = [
    "zero",
    "one",
    "two",
    "three",
    "four",
    "five",
    "six",
    "seven",
    "eight",
    "nine",
    "ten",
    "eleven",
    "twelve",
    "thirteen",
    "fourteen",
    "fifteen",
    "sixteen",
    "seventeen",
    "eighteen",
    "nineteen",
]

multipleOfTen = [
    "twenty",
    "thirty",
    "forty",
    "fifty",
    "sixty",
    "seventy",
    "eighty",
    "ninety",
]


def numberAsString(num):
    if num < 20:
        return smallNumber[num]
    
    result = multipleOfTen[int(num / 10) - 2]
    num = num % 10
    if num > 0:
        result += "-" + numberAsString(num)

    return result


def getNumber():
    while True:   
        num = input("Enter a number between 0 and 99: ")
        try:
            num = int(num)
            if num < 0 or num > 100:
                raise ValueError("Out of range")
            return num
        except:
            print("Please stay within the range")


def test():
    tests = [
        [0, "zero"],
        [9, "nine"],
        [11, "eleven"],
        [19, "nineteen"],
        [20, "twenty"],
        [21, "twenty-one"],
        [22, "twenty-two"],
        [29, "twenty-nine"],
        [30, "thirty"],
        [99, "ninety-nine"],
    ]

    failCounter = 0
    for test in tests:
        (num, expected) = test
        actual = numberAsString(num)
        if(actual != expected):
            failCounter += 1
            print("Failed test for", num, "got", actual, "but expected", expected)

    if failCounter == 0:
        print("All tests passed")

    print()


def main():
    while True:
        num = getNumber()
        print(numberAsString(num))

test()
main()
```

The importance of maintainability was highlighted by the next assignment being to extend this code to handle numbers up to 999. For the refactored code, this was a simple change to the `numberAsString` function:

```python
def numberAsString(num):
    if num < 20:
        return smallNumber[num]

    if num < 100:
        result = multipleOfTen[int(num / 10) - 2]
        num = num % 10
        if num > 0:
            result += "-" + numberAsString(num)
        return result

    result = numberAsString(int(num / 100)) + " hundred"
    num = num % 100
    if num > 0:
        result += " and " + numberAsString(num)

    return result
```

All that was then needed was a tweak to input validation and a few extra tests.

Merlyn Kline  
October 2021
