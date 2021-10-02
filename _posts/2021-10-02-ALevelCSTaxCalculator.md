---
layout: post
title:  "A-level CS Tax Calculator"
date:   2021-10-02
categories: codecraft,A-level CS
---
(For context see [A-level Computer Science]({% post_url 2021-10-01-ALevelComputerScience %}))

This assignment was to write a basic income tax calculator using the simplest rules for personal allowance and tax rates at different income bands. The necessary information is advertised on the [UK Government's HMRC site](https://www.gov.uk/income-tax-rates) and an [online tax calculator](https://listentotaxman.com/) was suggested for checking the results.

My son completed this at school with no input from me and came up with the following:

```python
def TestInt(x):  
    try:  
        int(x)  
    except:  
        return -1  
    return x  

def personal(y):  
    y = int(y)  
    if int(y) > 100000:  
        y -= 100000  
        if y % 2 == 0:  
            personal = 12570 - (y / 2)  
            return personal  
        else:  
            personal = 12570 - ((y - 1) / 2)  
            return personal  
    else:  
        return 12570  

def taxamount(income):  
    allowance = personal(income)  
    if int(income) < 125140:  
        income = int(income) - allowance  
    else:  
        pass  
    tax = 0  
    output = 0  
    if (int(income) < 37700):  
        tax = 20  
        bound = 0  
        additional = -2  
    elif (37700 < int(income)) and (int(income) < 137430):  
        tax = 40  
        bound = 37700  
        additional = 7536 
    elif int(income) > 137430:  
        tax = 45  
        bound = 137430  
        additional = 46804 
    output = (((int(income) - int(bound)) / 100) * tax) + additional 
    if output < 0: 
        output = 0 
    return output  

while True:  
    inc = int(-1)  
    while (int(inc) < 0):  
        inc = input("What are your total earnings for this year in GBP?")  
        inc = TestInt(inc)  
    tax = int(taxamount(inc))  
    TI = int(inc) - int(tax)  
    print("your tax is", tax, "and you keep", TI)  
```

We talked through this code and made several observations, including:

* The names of many things could be better. This lead to a discussion of the fact that the two hard problems in computing are naming things, cache invalidation and off-by-one errors. This in turn lead to an interesting but irrelevant derail concerning what exactly is meant by cache invalidation. We also talked about [maintainability being more important than 'correct' functioning]({% post_url 2016-11-21-GoodCodeIsBroken %}).
* There is some input validation, which is good to see at this early stage. We talked about why this is so important, in terms of both UX and security, with an entertaining diversion to [Little Bobby Tables](https://xkcd.com/327/). The validation code might be improved by moving some more of the logic into the `TestInt` function.
* The personal allowance calculator could be simplified by simply dividing the amount above £100,000 by 2 and rounding, rather than the special cases for odd and even numbers we see here.
* The tax calculation is very much hard-coded and would be difficult to maintain if the tax bands changed, and even more difficult if the number of bands increased.
* There's a lot of casting to `int` which could be better encapsulated.
* There are no unit tests! Of course, the idea of unit tests hasn't been mentioned in this A-level course yet. I will be interested to see if it ever is. Meanwhile I couldn't not mention it!

Aside from all this, the worst problem was that the results do not match those produced by the suggested online tax calculator. After a bit of research we narrowed this down to two issues:

* UK personal allowance (untaxed income) is based on a code, the numerical part of which is multiplied by 10 to produce an untaxed income allowance. Thus if your tax code is, say, 1257A then you may earn £12,570 before having to pay income tax. Since the multiplication by 10 means that the next possible level of allowance would be £12,580 there is obviously a gap of £10 between the possible levels. Unfortunately there seems to be some ambiguity about how rounding should be applied and thus whether the boundary between these bands effectively occurs at 12570, 12575 or 12579 (or elsewhere!). This leads to discrepancies between different online tax calculators.
* There is a further discrepancy between the suggested online calculator and our code, which we were unable to identify. Performing the calculations manually on the suggested test incomes produces outputs that match our code but not the online calculator, and the online calculator does not expose enough of its working for us to be able to see why that should be.

This was a useful talking point to illustrate that one of the biggest problems faced by software developers in the real world is understanding the client's requirements, especially as on close investigation it often turns out that the clients themselves do not have an adequately precise understanding of their own requirements for them to be reliably encoded in software. This means that refining requirements must often be a collaboration between developers and their clients leading to a common understanding, which might be different from that of both parties at the beginning of the process.

After talking about these things, we went through the code and refactored several times before eventually settling on this:

```python
bands = [
    {"income":     50270, "tax": 20},
    {"income":    137430, "tax": 40},
    {"income": 999999999, "tax": 45}
]


def getAllowance(income):
    allowance = 12570.0
    if income > 100000:
        allowance -= int((income - 100000) / 2)
    if allowance < 0:
        allowance = 0
    return allowance


def taxOn(income):
    allowance = getAllowance(income)
    tax = 0.0
    lastBreakPoint = allowance
    for band in bands:
        bandTop = band["income"]
        if bandTop > income:
            bandTop = income
        if bandTop > lastBreakPoint:
            tax += (bandTop - lastBreakPoint) * band["tax"] / 100
        lastBreakPoint = bandTop
    return tax


def getIntegerInput(prompt):
    while True:
        try:
            return int(input(prompt + " "))
        except:
            print("Please enter an integer")


def test():
    tests = [
        {"income":  10000, "tax":     0.00},
        {"income":  32000, "tax":  3884.20},
        {"income":  52000, "tax":  8232.00}, # https://www.moneyhelper.org.uk/en/work/employment/how-income-tax-and-personal-allowance-works 
        {"income":  52000, "tax":  8228.40}, # https://listentotaxman.com/52000? 
        {"income":  65000, "tax": 13428.40},
        {"income": 106000, "tax": 31028.40},
        {"income": 132500, "tax": 45460.00},
        {"income": 175000, "tax": 63710.00}
    ] 

    for test in tests:
        income = test["income"]
        expectedTax = test["tax"]

        tax = taxOn(income)

        if (abs(tax - expectedTax) <= 2):
            result = f"pass - tax = {tax}"
        else:
            result = f"FAIL - expected {expectedTax} but got {tax}"

        print("Test income", income, ":", result)


def main(): 
    while True:
        income = getIntegerInput("What are your total earnings for this year in GBP?")
        if income <= 0:
            break
        tax = taxOn(income)
        net = income - tax
        print("Your tax is", tax, "and you keep", net)

test()
main()
```

Merlyn Kline  
October 2021
