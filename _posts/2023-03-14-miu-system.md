---
title: Solving the MIU puzzle with programming
categories : [Puzzle Solving]
tags :  [prolog, python, maths, miu-puzzle]
description: Discover the process of implementing a solution for the MIU puzzle by Douglas Hofstadter, employing Prolog and Python programming languages.
comments: true
math : true
media_subpath: /assets/miu/
---

The MIU puzzle, introduced by mathematician and logician Douglas Hofstadter in his book "Gödel, Escher, Bach: An Eternal Golden Braid", is a classic example of a formal system and has been a topic of interest for logicians, mathematicians, and computer scientists for decades. The game involves starting with the string `MI` and applying a set of four rules to create new strings of symbols:

| Rule | Formal rule      | Informal explanation                             |
| ---- | ---------------- | ------------------------------------------------ |
| 1    | `xI` → `xIU`     | Add a `U` to the end of any string ending in `I` |
| 2    | `Mx` → `Mxx`     | Double the string after the `M`                  |
| 3    | `xIIIy`  → `xUy` | Replace any `III` with a `U`                     |
| 4    | `xUUy` → `xy`    | Remove any `UU`                                  |

The challenge is to transform the starting string `MI` into a target string such as `MU` using only these four rules.

In this blog post, we will explore the MIU puzzle and discuss its implementation in programming. 

## A decidable criterion for derivability
>An arbitrarily given string $x$ can be derived from `MI` by the above four rules if, and only if, $x$ respects the **all** three of the following properties:
>
>1. $x$ is only composed with one `M` and any number of `I` and `U`
>2. $x$ begins with `M`
>3. the number of `I` in $x$ is **not** divisible by 3
>
> Source: [Wikipedia](https://en.wikipedia.org/wiki/MU_puzzle#A_decidable_criterion_for_derivability)

For example the string `MIIIIU` is derivable (apply Rule 2 twice and Rule 1 once) but `MIIIIII` and `MU` are not.

### Implementation of a theorem checker in Prolog

> A theorem in a formal system is a valid string that can be formed from the axioms and inference rules. For example `MIIII` is a theorem in the `MIU` system but `MIT` is not.
{: .prompt-tip }

Based on the criterion for derivability, we can implement a Prolog program to check the derivability of a string.

To be able to do this, we first need to write facts/rules for the following:

- count the number of occurrences of an element in a list:
  ```prolog
count(_, [], 0).
count(M, [M |TAIL], N) :- count(M, TAIL, N1), N is N1 + 1.
count(M, [HEAD|TAIL], N) :- HEAD\= M, count(M, TAIL, N).
  ```
  For example to count the number of occurrences of `i` in $[m,i,u,i]$, use the following query: 
  ```prolog
  ?- count(i, [m,i,u,i], Count).
  % Count = 2
  ```
- check if the first element of the list is `m`:
  ```prolog
starts_with_m([m|_]).
  ```
- check if all elements of a list belong to the set $\\{m, i, u\\}$:
    ```prolog
valid_alphabet([m]).
valid_alphabet([i]).
valid_alphabet([u]).
valid_alphabet([H|T]):-
(H == m; H == i; H == u),
valid_alphabet(T).
    ```

Here’s the full program:

```prolog
% count number of occurrences of an element in a list
count(_, [], 0).
count(M, [M |TAIL], N) :- count(M, TAIL, N1), N is N1 + 1.
count(M, [HEAD|TAIL], N) :- HEAD\= M, count(M, TAIL, N).

% check if list starts with m
starts_with_m([m|_]).

% check if all elements of a list belong to the set {m, i, u}
valid_alphabet([m]).
valid_alphabet([i]).
valid_alphabet([u]).
valid_alphabet([H|T]):-
    (H == m; H == i; H == u),
    valid_alphabet(T).

% check if a list is a theorem in MIU-system 
theorem([], false).
theorem(List):- 
    valid_alphabet(List),
    count(m, List, Mcount), 
    count(i, List, Icount),
    starts_with_m(List),
    Mcount == 1,
    (Icount mod 3) =\= 0.

/** <examples>
?- theorem([m,u,i,i,u]). -> true
?- theorem([m,i,i,i,i,i,i,i,i,i]). -> false
*/
```

>You can run the Prolog code on an online compiler like [SWISH Prolog](https://swish.swi-prolog.org/).
{: .prompt-tip }

## Algorithm to form any given theorem

Now suppose that we are given a theorem $x$ to derive. Since we know that the criteria for derivability are satisfied, we can deduce an algorithm to form the theorem:

Let $N_i$ be the number of $I$s in $x$.

Let $N_u$ be the number of $U$s in $x$.

Assuming $N_i$ is not divisible by 3 (third criterion for derivability is satisfied), 

1. Let $N=N_i+3N_u$. 
2. Find the minimum value of $n$, where $n\in\mathbb Z^+$, such that $2^n\ge N$ and $2^n\mod 3 \equiv N \mod 3$.
3. Starting from `MI`, apply Rule 2 $n$ times to form a string with $2^n$ `I`.

The first $N$ `I` are used to create our final string and the remaining $2^n-N$ `I` (highlighted in red) must be eliminated. To achieve this, we need to convert all `I` in the red region into an even number of `U` which can then be eliminated with Rule 4.

<img src="exp.png" height ="300" width="400" alt = "Image showing what strings looks like after the first 3 steps">

{:start="4"}
4. If $\lfloor\cfrac{2^n-N}{3}\rfloor$ is odd, apply Rule 1 once.
5. Using Rule 3 as many times as needed, convert all triples of `I` in the red region to `U` .
6. Eliminate all `U` in the red region by applying Rule 4 as many times as needed.
7. Use Rule 3 where needed on the green region to form the desired string.

>The logic behind step 1 is the fact that each `U` in a given theorem is worth 3 `I`, based on Rule 3. The value of $N$ represents the minimum number of `I` required to form the theorem. 
{: .prompt-tip }

>Step 4 ensures that **one** of the following cases is satisfied for the red region:
> - There is an even number of triples of `I`
> - There is an odd number of triples of `I` and a `U`. 
> 
> In both of these cases, all letters in the red region can thus be eliminated.
{: .prompt-tip }

Here's a diagrammatic way of representing all the steps required to form `MIIUII` starting from `MI`:

$N=7$ and $n=4$. Rule 2 is applied 4 times.

<img src="miiuii-example.png" height ="300" width="400" alt = "">

### Implementation of a theorem solver in Python
A crude implementation of the above algorithm in Python is as follows:
```python
def print_steps(theorem):
    """Prints the steps required to form a given theorem starting from MI.

    Args:
        theorem (string): String must contain only valid characters and 
        must satisfy the derivability criterion.
    """
        
    theorem.replace(" ", "") .lower()
    print("mi")

    iCount = theorem.count('i')
    uCount = theorem.count('u')
    totalI = iCount + 3*uCount
    
    n = 0
    while((2**n< totalI) or (2**n % 3 != totalI%3)):
        n+=1
        
    print(f'Apply Rule 2 [{n} times]')
    x = "m" +  ("i" * 2**n)
    print(x)
    if(x == theorem):
        return
    
    badU = (2**n - totalI)//3
    if(badU % 2 == 1):
        print('Apply Rule 1')
        x += "u"
        badU+=1
        print(x)
    
    ## get rid of Is in useless region if it exists
    if(2**n-totalI > 0):
        print('Make groups of 3 Is for all Is after the first', totalI, "Is")
        y = 0
        f = ""
        for i in range(totalI+1, len(x)):
            if(y%3==0):
                f+= " "
            f+=(x[i])
            y+=1
        x = "m" + ("i" * totalI) + f
        print(x)
        print("Replace grouped Is with U")
        
        x = "m" + ("i" * totalI)
        for i in range(0, badU):
            x += " u"
        print(x)
        
        print(f"Apply Rule 4 [{badU//2} times]")
        x = "m" + ("i" * totalI)
        print(x)
    
    print("Group Is")
    f = ''
    y = 0
    for i in range (0, len(theorem)):
        if(theorem[i]=='u'):
            f+= " [iii] "
            y+=1
        else:
            f+= theorem[i]
    print(f)
    
    print(f"Apply  Rule 1 [{y} times]")
    print(theorem)

## print_steps('miiuuuii')
## print_steps('miiii')
## print_steps('miiii')
## print_steps('muiiu')
```

Output for `miiuuuii`:
```console
mi
Apply Rule 2 [4 times]
miiiiiiiiiiiiiiii
Apply Rule 1
miiiiiiiiiiiiiiiiu
Make groups of 3 Is for all Is after the first 13 Is
miiiiiiiiiiiii iii u
Replace grouped Is with U
miiiiiiiiiiiii u u
Apply Rule 4 [1 times]
miiiiiiiiiiiii
Group Is
mii [iii]  [iii]  [iii] ii
Apply  Rule 1 [3 times]
miiuuuii
```

Output for `muiiu`:
```console
mi
Apply Rule 2 [3 times]
miiiiiiii
Group Is
m [iii] ii [iii] 
Apply  Rule 1 [2 times]
muiiu
```
> My program does not check if the theorem passed as parameter is a true theorem. Invalid inputs like `mu` could cause infinite loops.
{: .prompt-warning }