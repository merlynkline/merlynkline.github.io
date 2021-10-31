---
layout: post
title:  "A-level CS: SUVAT calculator"
date:   2021-10-31
categories: codecraft,A-level CS
---
(For context see [A-level Computer Science]({% post_url 2021-10-01-ALevelComputerScience %}))

This was a self-assigned task - my son is also taking physics A-level and wanted to create a [SUVAT](https://en.wikipedia.org/wiki/Equations_of_motion) calculator which would allow you to enter three or four of the five variables and then work out how to calculate the unknown(s) using the appropriate equations. His first go at this was reasonably successful although it could end up with complex numbers in the results for some cases! It looked like this:

```python
def IntTest(x):
    while 1:
        try:
            if x == '':
                break
            else:
                x = float(x)
                break
        except:
            x = input('Please Input an Integer - ')
    return x

def SUVAT():
    Known = ['', '', '', '', '']

    s = IntTest(input('Displacement - '))
    Known[0] = 0
    if s != '':
        Known[0] = 1
    u = IntTest(input('Initial Velocity - '))
    Known[1] = 0
    if u != '':
        Known[1] = 1
    v = IntTest(input('Final Velocity - '))
    Known[2] = 0
    if v != '':
        Known[2] = 1
    a = IntTest(input('Acceleration - '))
    Known[3] = 0
    if a != '':
        Known[3] = 1
    t = IntTest(input('Time - '))
    Known[4] = 0
    if t != '':
        Known[4] = 1
    print(Known)

    x = 0

    while x != 5:
        x = sum(Known)
        if Known[1] == 1 and Known[2] == 1 and Known[4] == 1 and Known[3] == 0:
            a = (v-u) / t
            Known[3] = 1
        if Known[1] == 1 and Known[2] == 1 and Known[4] == 0 and Known[3] == 1:
            t = (v-u) / a
            Known[4] = 1
        if Known[1] == 1 and Known[2] == 0 and Known[4] == 1 and Known[3] == 1:
            v = u + (a*t)
            Known[2] = 1
        if Known[1] == 0 and Known[2] == 1 and Known[4] == 1 and Known[3] == 1:
            u = v - (a*t)
            Known[1] = 1
        if Known[0] == 1 and Known[1] == 1 and Known[2] == 0 and Known[3] == 1:
            v = ((u**2) + 2 * a * s)**0.5
            Known[2] = 1
        if Known[0] == 1 and Known[1] == 0 and Known[2] == 1 and Known[3] == 1:
            uSquared = ((v**2) - 2 * a * s)
            if uSquared < 0:
                u = -(abs(uSquared) ** 0.5)
            else:
                u = uSquared ** 0.5
            Known[1] = 1
        if Known[0] == 0 and Known[1] == 1 and Known[3] == 1 and Known[4] == 1:
            s = u*t + 0.5*a*t*t
            Known[0] = 1
        if Known[0] == 0 and Known[2] == 1 and Known[3] == 1 and Known[4] == 1:
            s = v*t - 0.5*a*t*t
            Known[0] = 1
        if Known[0] == 0 and Known[1] == 1 and Known[2] == 1 and Known[4] == 1:
            s = 0.5 * (v - u) * t
            Known[0] = 1

    print('Displacement:', s, '\nInitial Velocity:', u, '\nFinal Velocity', v, '\nAcceleration:', a, '\nTime:', t)

while 1:
    SUVAT()
```

It's easy to see how this has evolved directly from the thought process you might go through if presented with the problem the program is trying to solve. We talked about some aspects of this including:

* naming things (again!)
* how best to represent the unknown values in Python
* what the program's "domain" is and how this can be incorporated into the design
* how we might perform some analysis of the domain
* whether all this would allow the code could be simplified a bit, to make it easier to maintain and track down the source of the complex numbers appearing in some results.

## Analysis

We started our analysis by considering the cases where there are two unknowns in the input. For these cases we would need to an equation with three variables and one unknown, allowing us to calculate one of the two unknowns without dependency on the other. This suggested that we would need five possibilities, for each of five possible missing values, and in each case we would want the simplest possible equation. We labelled these A to E, in order of complexity, like this:

* `A` (without `s`): `a = (v - u) / t`
* `B` (without `a`): `s = 0.5 * (v + u) * t`
* `C` (without `v`): `s = u * t + 0.5 * a * t * t`
* `D` (without `u`): `s = v * t - 0.5 * a * t * t`
* `E` (without `t`): `v ** 2 = u ** 2 + 2 a s`

Each of these could be rearranged depending on which of the four variables was unknown. Although it appeared at first sight that we might need something like 5 * 4 = 20 combinations of rearrangements of these, on thinking through how they would be applied it quickly became obvious that we would need far less. Because the order of unknown variables is irrelevant, and some arrangements would be used in more than one variation of inputs, we could reduce the number of equations required quite signficantly. We performed this analysis by wrting down the possible combinations of two unknown variables and looking at how we would apply our equations calculate these. Using a notation to indicate that we would, for example, use equation `B` to calculate variable `u` like this: `B -> u`. We worked through the primary unknown values in the order above, so as to maximise the use of the simpler equations, and came up with the following combinations:

|Unknowns|First calculation|Second Calculation|
|:-:|:-:|:-:|
|`s` `a`|`A -> a`|`B -> s`|
|`s` `v`|`A -> v`|`B -> s`|
|`s` `u`|`A -> u`|`B -> s`|
|`s` `t`|`A -> t`|`B -> s`|
|`a` `v`|`B -> v`|`A -> a`|
|`a` `u`|`B -> u`|`A -> a`|
|`a` `t`|`B -> t`|`A -> a`|
|`v` `u`|`C -> u`|`A -> v`|
|`v` `t`|`C -> t`|`A -> v`|
|`u` `t`|`D -> t`|`A -> u`|

## Results

Looking at the analysis it's immediately clear that there is a bit of repetition, and that the five possible cases of a single unknown are also covered by entries in this table. From here we can easily express the logic required to choose the appropriate line in the table as a series of `if` statments that are isomorphic with the analysis.

After some experimentation we were able to:

* implement the basic code as planned
* add a bit of simple input validation
* add some output describing the calculations selected
* investigate simple list comprehensions to add further input validation
* fix the bug which resulted in complex numbers being output

After all that, we ended up with this:

```python
def getFloatOrNone(prompt):
    while True:
        value = input("Enter " + prompt + ": ")
        if value == "":
            return None
        try:
            return float(value)
        except:
            print("* Please enter a number or blank for unknown")

def explain(message):
    print(" - Calculating " + message)

def aFrom_uvt(u, v ,t):
    explain("a = (v - u) / t")
    return (v - u) / t

def vFrom_uat(u, a ,t):
    explain("v = u + a * t")
    return u + a * t

def uFrom_vat(v, a ,t):
    explain("u = v - a * t")
    return v - a * t

def tFrom_uva(u, v, a):
    explain("t = (v - u) / a")
    return (v - u) / a

def sFrom_uvt(u, v, t):
    explain("s = 0.5 * (v + u) * t")
    return 0.5 * (v + u) * t

def vFrom_sut(s, u, t):
    explain("v = (2 * s - u * t) / t")
    return (2 * s - u * t) / t

def uFrom_svt(s, v, t):
    explain("u = (2 * s - v * t) / t")
    return (2 * s - v * t) / t

def tFrom_suv(s, u, v):
    explain("t = 2 * s / (v + u)")
    return 2 * s / (v + u)

def uFrom_sat(s, a, t):
    explain("u = -0.5 * a * t + s / t")
    return -0.5 * a * t + s / t

def tFrom_sua(s, u, a):
    explain("t = (abs((u * u + 2 * a * s) ** 0.5) - u) / a")
    return ((abs(u * u + 2 * a * s) ** 0.5) - u) / a

def tFrom_sva(s, v, a):
    explain("t = abs(v * v - 2 * a * s) ** 0.5) / a")
    return (v - abs(v * v - 2 * a * s) ** 0.5) / a

def suvatCalculator():
    print("\nsuvat Calculator")
    s = getFloatOrNone("s=Displacement (m)")
    u = getFloatOrNone("u=Initial velocity (m/s)")
    v = getFloatOrNone("v=Final velocity (m/s)")
    a = getFloatOrNone("a=Acceleration (m/s/s)")
    t = getFloatOrNone("t=Time (s)")

    unknownCount = sum(x is None for x in [s, u, v, a, t])
    if unknownCount > 2 or unknownCount < 1:
        print("ERROR: You must have exactly one or two unknowns")
        return
    
    if s == None:
        if a == None:
            a = aFrom_uvt(u, v, t)
        elif v == None:
            v = vFrom_uat(u, a, t)
        elif u == None:
            u = uFrom_vat(v, a, t)
        elif t == None:
            t = tFrom_uva(u, v, a)
        s = sFrom_uvt(u, v, t)
    elif a == None:
        if v == None:
            v = vFrom_sut(s, u, t)
        elif u == None:
            u = uFrom_svt(s, v, t)
        elif t == None:
            t = tFrom_suv(s, u, v)
        a = aFrom_uvt(u, v, t)
    elif v == None:
        if u == None:
            u = uFrom_sat(s, a, t)
        elif t == None:
            t = tFrom_sua(s, u, a)
        v = vFrom_uat(u, a, t)
    elif u == None:
        if t == None:
            t = tFrom_sva(s, v, a)
        u = uFrom_vat(v, a, t)
    elif t == None:
        t = tFrom_uva(u, v, a)

    print(f"s=Displacement (m):       {s:10.4f}")
    print(f"u=Initial velocity (m/s): {u:10.4f}")
    print(f"v=Final velocity (m/s):   {v:10.4f}")
    print(f"a=Acceleration (m/s/s):   {a:10.4f}")
    print(f"t=Time (s):               {t:10.4f}")

while True:
    suvatCalculator()
```

Merlyn Kline  
October 2021
