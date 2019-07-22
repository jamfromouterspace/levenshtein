# Levenshtein Distance in Python 3


## Introduction
This function simply implements the levenshtein distance algorithm. The levenshtein distance is the number of 'edits' required to transform string *a* into string *b*. If the two are the same, zero edits are required. With this, we can do 'fuzzy' string comparisons.

An edit is an **insertion**,**deletion**, or **substitution**. This is represented formally in the equation below.

![Levenshtein Equation](levenshtein_equation.jpg)

To understand this more intuitively, it helps to visualize it with the following table, where `a = 'hello'` and `b = 'yellow'`.


|   |   | Y | E | L | L | O | W |
|---|---|---|---|---|---|---|---|
|   | **0** | 1 | 2 | 3 | 4 | 5 | 6 |
| **H** | 1 | **1** | 2 | 3 | 4 | 5 | 6 |
| **E** | 2 | 2 | **1** | 2 | 3 | 4 | 5 |
| **L** | 3 | 3 | 2 | **1** | 2 | 3 | 4 |
| **L** | 4 | 4 | 3 | 2 | **1** | 2 | 3 |
| **O** | 5 | 5 | 4 | 3 | 2 | **1** | **2** |

The highlited path represents a possible sequence of edits to turn 'hello' into 'yellow'. Along the path, when an edit has been made, the number increases by 1. A diagonal step *with an increasing number* represents a substitution `(i-1,j-1) -> (i,j)`. This happens when `a[i] != b[j]` (as shown in the subscript under 1 in the equation). Otherwise, a diagonal step with no change in number means no edit is made.

A downward step means a deletion from string *a* has been made `(i,j-1) -> (i,j)`. A right step means an insertion into string *a* has been made `(i-1,j) -> (i,j)`. You can see this at the end, when 'w' must be added to the end of 'hello'.

**The levenshtein distance is the bottom-right entry in the matrix, in this case 2.** The path is simply for visualization, and not necessary for implementation.

To clarify the equation a bit more, the first piece-wise entry of *max(i,j)* is simply to generate the first column containing (0...*n*) and first row containing (0...*m*), where *n* is the length of string *a* and *m* is the length of string *b*.


## Implementation

A recursive implementation without the table is possible, but very inefficient (time complexity of `O(n^4 * m)`). The better, dynamic programming approach is simply to create the table depicted above (time complexity `O(n * m)`).

1. Let `n = len(a)` and `m = len(b)`.
2. If `a == ''` return `m` and if `b == ''` return `n`
3. Construct an `(n+1,m+1)` matrix `lev`. Many resources will tell you to construct an `(n,m)` matrix, but as you can see from the table it actually needs to be one bigger in each dimension.
4. Fill the zeroth row with numbers 0 to `m` and fill the zeroth column with numbers 0 to `n`
5. Fill the rest of the matrix by taking the minimum of :
	1. `lev(i-1,j) + 1`
	2. `lev(i,j=1) + 1`
	3. `lev(i-1,j) + (1 if a[i-1] != b[j-1])`

	**Note**: we have to subtract by 1 when accessing the chars in `a` and `b` because the matrix is bigger. Don't let that confuse you.
6. Return `lev[n,m]`.


## Levenshtein Ratio
A more useful metric is the Levenshtein *ratio*, which can give you a percent difference between the two strings.

`ratio = (n + m - lev_distance) / (n + m)`

## More Examples 


|   |   | T | A | B | L | E |
|---|---|---|---|---|---|---|
|   | **0** | 1 | 2 | 3 | 4 | 5 |
| **T** | 1 | **0** | 1 | 2 | 3 | 4 |
| **A** | 2 | 1 | **0** | 1 | 2 | 3 |
| **B** | 3 | 2 | 1 | **0** | 1 | 2 |
| **L** | 4 | 3 | 2 | 1 | **0** | 1 |
| **E** | 5 | 4 | 3 | 2 | 1 | **0** |

*Zero substitutions*.

|   |   | B | I | L | L |
|---|---|---|---|---|---|
|   | **0** | 1 | 2 | 3 | 4 |
| **G** | 1 | **1** | 2 | 3 | 4 |
| **I** | 2 | 2 | **1** | 2 | 3 |
| **L** | 3 | 3 | 2 | **1** | 2 |
| **L** | 4 | 4 | 3 | 2 | **1** |
| **S** | 5 | 5 | 4 | 3 | **2** |

*Two substitutions (1 sub, 1 deletion)*.

