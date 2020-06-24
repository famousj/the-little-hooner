# The Little Hooner

## Adapted from The Little Schemer (Fourth Edition) by Daniel P. Friedman and Matthias Felleisen
## Adaptation by ~ribben-donnyl (AKA J LeBlanc)

## The Ten Commandments

KM First commandment

###  The Second Commandment
> Use `:-` to build lists.

###  The Third Commandment
> When building a list, describe the first typical element, then 'cons' it onto the natural recursion.

KM Fourth commandment

## The <strike>Five</strike> Four Rules

###  The Law of Head
> The primitive `head` is defined only for cells and non-null lists.

### The Law of Tail
> The primitive `tail` is defined only for cells and non-null lists. The `tail` of any non-null list is always another list.

### The Law of Colhep
> The rune `:-` takes two arguments.  The second argument to `:-` must be a 
list or null.  The result is a list.

### The Law of Dottis
> The rune `.=` takes two arguments.  Each must be an atom.


## Rune Pronunciation

```
ace [1 space]       gap [>1 space, nl]  pat @
bar |               gar >               sel [
bas \               hax #               ser ]
buc $               hep -               sig ~
cab _               kel {               soq '
cen %               ker }               tar *
col :               ket ^               tic `
com ,               lus +               tis =
doq "               mic ;               wut ?
dot .               pal (               zap !
fas /               pam &
gal <               par )
```

## Table of Contents


**1. [Toys](#chap1)**  
**2. [Do It, Do It Again, and Again, and Again ...](#chap2)**  
**3. [Colhep the Magnificent](#chap3)**

## 0. Preface and Setup

### Introductory Notes

- While this is based on The Little Schemer, Hoon and Scheme are
  different languages and they do things differently.

- If you see "JL", this is a translation note, or mentions a section
  that is original to this document.  

- TLS stands for "The Little Schemer".

- "KM" means "TODO", but it's two keystrokes shorter.

- The purpose of the original TLS is to understand how recursion works.
  This book has two purposes: to understand recursion but also to get
  your head around how writing code in Hoon works.

  So I occasionally toss in some Hoon concepts on top of the recursion
  concepts.  If it feels like a needless distraction, let me know.

- There are two ways to enter a null-terminated list.  You can either do 

```
[1 2 3 ~]
```

Or you can do

```
~[1 2 3]
```

I went with the former.  This is closer to how the data is being stored
interally.  I might at least mention the latter style at some point.

- Scheme has a somewhat muddied distinction between code and data that Hoon
  doesn't have.  
  So in Scheme you can have a list, like `(a b c)` and prepend a soq, like 
  `'(a b c)`, to tell the compiler "everything in these parentheses are 
  data".  

  This isn't possible in Hoon.  Either that or I'm not aware of someway to do it.  So I've had to make all the words into cords.  This is not ideal, but it works.

- Conceptually cells in Hoon act as binary trees.  I am more or less
  ignoring this fact.  I'm going to discuss atoms, cells, and lists.
  Also tuples when appropriate.

- If you see any egregious errors, DM me at ~ribben-donnyl. Also, pull requests are welcome!

### Introduction

KM

I plan to note here that what I'm describing is a superset of Hoon, and
that we'll add to it as we go along.  

KM Make sure to note that punctuation has a three-letter pronounciation.

### How to Read This Book - JL

-KM - Pull in content from the TLS preface here

This book is written in a question-and-answer format.  Go slowly!  Make sure you really understand a chapter before you go onto the next chapter.

To get started, you'll need a [running ship on Urbit](https://urbit.org/using/install/).  Developing on your own ship is Considered Harmful, so we recommend setting up a [development ship](https://urbit.org/using/develop/#creating-a-development-ship).

Once you're runnning, you'll have a dojo prompt.

If you want to assign a variable, you can do that like so:

```
> =a 42
> =l ['ham' 'and' 'eggs' ~]
```

You are highly encouraged to follow along and test things out in the dojo.  

When you get to chapter 2, you'll be writing a "generator", which is a kind 
of program you can run on your Urbit ship.  

To do this, you'll need to run this command in the dojo:
```
> |mount /=home=
```

You'll only have to do this once.  Once you do that, you'll get a folder
called "home" inside your ship's directory.  If you created a
development ship, this will be `zod/home`.

Whenever you change one of the files in your home directory, you'll need
to run this command to add these changes to your ship

```
> |commit %home
```

### TODO

- Right now this is a Markdown file. It'd be pretty sweet to make some
  kind of interactive version where it doesn't give the answer until you
  hit the spacebar or something.

- Gently introduce the concept of types of atoms.  

- Make a chapter describing the aura type system.  We might, in chapter
  1, mention that most of the atoms discussed here are "cords".

- Introduce tree addressing at some point.  Possibly a new, original
  chapter, since Scheme doesn't have anything like that.

- At some point (possibly in chapter 1) I should introduce the `:~` rune and discuss that this is a way to construct a null-terminated list. 

- Change "The Law of Null?" so it comes after 'dottis', then introduce
  `?~`

- In re: The Law of Null?, Hoon doesn't exactly have a function `null?`.
  What it does have is `?~`, but this has a built in if-then-else
  structure, so I'll introduce that later.

- TLS uses a scaled-down superset of Scheme.  I'm kind of doing the same
  thing here with Hoon, but need to be a bit more transparent about what 
  you can and can't do in Hoon.

- Do we want section headers?

- Gently introduce types in terms of `~`. Then `?=`, to do a type check,
  then `?~` to test if something is null.  (Possibly skip step 2 here).

- Introduce the term "gate" and "trap" for functions?  Maybe in the
  future.

- Add instructions for unbinding to the introduction

- Introduce cord and number types of atoms

- For |- use %= to show what's actually happening, i.e. we are calling
  the function `$` with changes (running style).

- Cast the result!  This is good style

- Introduce more tall form

- At some point explain what a 'core' is (much later)

- Come up with an entirely new non-food-based theme and rewrite the
  whole thing.

- Find a place to use the `?~` shorthand

- What about a gate that is not recursive?  Just pairs?  Ease into the
  idea of how functions work in hoon

- Special format for list of cords: <|this is a list|>

- `lest` is a non-null list

- Add a discussion about faces?

<a name="chap1"></a>

## 1. Toys

**Q:** Is it true that this is an atom?

> `'atom'`

**A:** Yes,  

> because `'atom'` is a string of characters beginning with the letter a,
> surrounded by single quotes  or `soq`s.

**Q:** Is it true that this is an atom?

> `'turkey'`

**A:** Yes,

> because `'turkey'` is a string of characters beginning with a letter,
> surrounded by `soq`s.

**Q:** Is it true that this is an atom?

> `42`

**A:** Yes,

> because `42` is a number 

**Q:** Is it true that this is an atom?

> `'u'`

**A:** Yes,

> because `'u'` is a string of one character, which is a letter.

**Q:** Is it true that this is an atom?

> `'*abc$'`

**A:** Yes,

> because `'*abc$'` is a string of characters.

**Q:** Is it true that this is an atom?

> `~`

**A:** Yes,

> `~` is the null value.  It is pronounced 'sig' or sometimes 'null'.  It
> is an atom with no value.

**Q:** Is it true that this is a cell?

> `['grilled' 'cheese']`

**A:** Yes,

> because it is a pair of two atoms, 'grilled' and 'cheese', surrounded
> by a left square bracked (which is pronounced 'sel') and a right square
> bracket, which is pronounced 'ser'.

**Q:** Is it true that this is a cell?

> `'tomato' 'soup'`

**A:** No,

> because cells must be enclosed in a 'sel' and a 'ser'.

**Q:** Is it true that this is a cell?

> `['atom' 'turkey'] 'or'`

**A:** No,

> these are actually two nouns, not enclosed by a 'sel' and a
> 'ser'.  The first one is a cell and the second one is an atom.

**Q:** Is it true that this is a cell?

> `[['atom' 'turkey'] 'or']`

**A:** Yes,

> because the two nouns are now enclosed by square brackets.

**Q:** Is it true that this is a cell?

> `['atom' 'turkey' 'or']`

**A:** Yes,

> because this is another way to write

> > `['atom' ['turkey' 'or']]`

> This is a cell containing an atom and another cell.

**Q:** Is it true that this is a cell?

> `['two' 'all' 'beef' 'patties']`

**A:** Yes,

> because this is another way to write

> > `['two' ['all' ['beef' 'patties']]]`

> This is a cell containing an atom and another cell.  That cell also 
contains an atom > and another cell.

**Q:** Is it true that this is a cell?

> `['cheese']`

**A:** No,

> A cell must have two nouns

**Q:** Is it true that this is a noun?

> `'xyz'`

**A:** Yes,

> because all atoms are nouns.

**Q:** Is it true that this is a noun?

> `['x' 'y' 'z']`

**A:** Yes,

> because it is a cell

**Q:** Is it true that this is a noun?

> `[['x' 'y'] 'z']

**A:** Yes,

> because all cells are nouns.

### Lists

JL: Should I put this discussion at the end or leave it here?

**Q:** Is it true that this is a list?

> `['atom' ['turkey ~]]`

**A:** Yes,

> because `['atom' ['turkey' ~]]` is a cell where the last item is `~`,
> i.e. the null value or 'sig'.

**Q:** Is it true that this is a list?

> `['atom' 'turkey' ~]`

> This is another way of writing

**A:** Yes,

because `['atom' 'turkey' ~]` is another way of writing

> `['atom' ['turkey ~]]`

**Q:** Is it true that this is a list?

> `['atom' ~]`

**A:** Yes,

> because `['atom' ~]` is a cell ending in the null value, `~`,
> which is pronounced ('sig' or "null").

**Q:** Is it true that this is a list?

> `['tomato' 'soup']`

**A:** No,

> because the last value of `['tomato' 'soup']` is not the null value, `~`.

**Q:** Is it true that this is a list?

> `~['atom' 'turkey' 'or']`

**Q:** Is it true that this is a list?

> `[['x' 'y' ~] 'z' ~]`

**A:** Yes,

> This is a list whose first item is another list.

**A:** Yes,

> Because `~['atom' 'turkey' 'or']` is another way to write

> > `['atom' 'turkey' 'or' ~]`

KM: Do we want to introduce the shorthand for making lists now, or save it for later, or skip it in this doc?

**Q:** Is it true that this is a noun?

> `['x' 'y' 'z' ~]`

**A:** Yes,

> because it is a list, and all lists are nouns

**Q:** Is it true that this is a list

> `['how' 'are' 'you' 'doing' 'so' far' ~]`

**A:** Yes,

> because it is a collection of nouns enclosed by square brackets,
> ending in null.

**Q:** Is it true that this is an atom?

> `~`

**A:** Yes,

> the null value is an atom with no value. 

**Q:** Is it true that this is a list?

> `~`

**A:** Yes,

> the null value is an empty list, a list with no nouns.

**Q:** So the null value is both an atom and a list?

**A:** Yes.

> `~` is special.  It can act like a list when it needs to be a list.
> Most of the time it's just an atom.

**Q:** Is it true that this is a cell?

> `~`

**A:** No,

> the null value is the only list that is not also a cell.

**Q:**  How many item are in the list?

> `['how' 'are' 'you' 'doing' 'so' far' ~]`

and what are they?

**A:** Six,

> `'how'`, `'are'`, `'you'`, `'doing'`, `'so'`, and `'far'`.  Plus the
> `~`

**Q:** Is it true that this is a list?

> `[[['how' ~] 'are' ~] [['you' ~] ['doing' 'so' ~] ~] 'far' ~]`

**A:** Yes,

> because it is a collection of nouns enclosed by square brackets,
> ending in null.

**Q:** How many items are in the list

> `[[['how' ~] 'are' ~] [['you' ~] ['doing' 'so' ~] ~] 'far' ~]`

and what are they?

**A:** Three,

> `[['how' ~] 'are' ~]`, `[['you' ~] ['doing' 'so' ~] ~]`, and `'far'`, plus 
> the `~` 

**Q:** What is the advantages of using lists versus using cells?

**A:** A cell can only have two or more items.  You can't have a cell
with one item.

> `['item']`

is converted into the atom

You also can't have a cell with zero items.

A list can have zero items:

> `~`

It can have one item:

> `['item' ~]`

It can have as many or as few items as you want, as long as you
terminate with a `~`.

> `['this' 'list' 'has' 'a' 'lot' 'of' 'items' ~]`

### Heads and Tails

KM: Start with cells here

**Q:** What is the `head` of the cell `c` where `c` is

> `['a' 'b']`

**A:** `'a'`

> because `'a'` is the first item in the cell.

**Q:** What is the `head` of `c` where `c` is 

> `[['a' 'b' 'c'] 'x' 'y' 'z']`

**A:**  `['a' 'b' 'c']`

> because `['a' 'b' 'c']` is the first noun in the cell.

**Q:** What is the `head` of `a` where `a` is `hotdog`

**A:** No answer.

> You cannot ask for the `head` of an atom.

**Q:** What is the `head` of `l` where `l` is `~`

**A:** No answer.
 
> You cannot ask for the `head` of `~`, since `~` is not a cell.

---

###  The Law of Head
#### The primitive `head` is defined only for cells and non-null lists.

---

**Q:** What is the `head` of `l` 

where

> `l` is `[[['hotdogs' ~] ~] ['and' ~] ['pickle' ~] 'relish' ~]`

**A:** `[['hotdogs' ~] ~]`

read as:

> "The list of the list of 'hotdogs'."

`[['hotdogs' ~] ~]` is the first noun of `l`

**Q:** What is `(head c)`

where

> `c` is `[['hotdogs' 'and'] ['pickle' 'relish']]`

**A:** `['hotdogs' 'and']`

> because `(head l)` is another way to ask for "the `head` of the cell
> `c`"

KM: `(head c)` is the irregular form of %-(head c)  This might be a good place
to break that down.  Or maybe not.

**Q:** What is `(head (head c))`

where

> `l` is `[['hotdogs' 'and'] ['pickle' 'relish']]`

**A:** `hotdogs`

**Q:** What is the `tail` of `c`

where

> `c` is `['a' b' 'c']`


**A:** `['b' 'c']`

> because `['a' 'b' 'c']` is another way to write `['a' ['b' 'c']]`.

And

> `['b' 'c']` is the cell `c` without `(head c)`.

**Q:** What is the `tail` of `c`

where

> `c` is `[['a' 'b' 'c'] 'x' 'y' 'z']`

**A:** `['x' 'y' 'z']`

**Q:** What is the `tail` of `l`

where 

> `l` is `['hamburger' ~]`

**A:** `~`

> the null value, in this case acting as an empty list.

**Q:** What is `(tail l)`

where

> `l` is `[['x' ~] 't' 'r' ~]`

**A:** `['t' 'r' ~]`

> becuase `(tail l)` is just another way to ask for "the `tail` of the
> list `l`."

**Q:** What is `(tail a)`

where

> `a` is `hotdogs`

**A:** No answer.

> You cannot ask for the `tail` of an atom.

**Q:** What is `(tail l)`

where `l` is `~`

**A:** No answer.

> You cannot ask for the `tail` of `~`, since `~` is an empty list.

---

###  The Law of Tail
#### The primitive `tail` is defined only for cells and non-null lists. The `tail` of any non-null list is always another list.

---

KM: Consider reorganizing this so you do cells, head and tails, and then
throw out lists and do heads and tails on lists.

**Q:** What is `(head (tail c))`

where

> `c` is `['b' ['x' 'y'] ['c' 'd']]`

**A:** `['x' 'y']`,

> because `[['x' 'y'] ['c' 'd']]` is `(tail l)` and `['x' 'y']`
> is the `head` of `(tail l)`.

**Q:** What is `(tail (tail c))`

where

> `c` is `['b' ['x' 'y'] ['c' 'd']]`

**A:** `['c' 'd']`

> because `[['x' 'y'] ['c' 'd']]` is `(tail l)` and `['c' 'd']` is the 
> `tail` of `(tail l)`.

**Q:** What is `(tail (head l))`

where

> `l` is `['a' ['b' ['c' ~] ~] 'd' ~]`

**A:** No answer,

> since `(head l)` is an atom, and `tail` does not take an atom as an
> argument; see The Law of Tail.

**Q:** What does `head` take as an argument?

**A:** It takes any cell or non-empty list.

**Q:** What does `tail` take as an argument?

**A:**  It takes any cell or non-empty list.

KM: Explain how :- really works!

**Q:** What is the 'cons' of `'a'` and `'b'`?

**A:** `['a' 'b']`

This can be written as

> :-('a' 'b')

**Q:** What is the 'cons' of the atom `a` and the list `l` 

where `a` is `'peanut'`

and

> `l` is `['butter' 'and' 'jelly' ~]`

> You can read this as: "_cons_ the atom `a` onto the list `l`"

**A:** `['peanut' 'butter' 'and' 'jelly' ~]`

> because the _cons_ operation can add an atom to the front of the list
> and make a new list.

**Q:** What is the _cons_ of `s` and `l`

where `s` is `['banana' 'and' ~]`

and

> `l` is `['peanut' 'butter' 'and' 'jelly' ~]`

**A:** `[['banana' 'and' ~] 'peanut' 'butter' 'and' 'jelly' ~]`

> because _cons_ adds any noun to the front of a list.

**Q:** What is `:-`

**A:** `:-` is a _rune_, a two-character function.

> Specifically it's the rune to do the _cons_ operation.

**Q:** How do you pronounce `:-`

**A:** It's pronounced 'colhep'

**Q:** What is `:-(s l)`

where

> `s` is `[['help' ~] 'this' ~]`

and 

> `l` is `['is' 'very' [['hard' ~] 'to' 'learn' ~] ~]`

**A:** `[[['help' ~] 'this' ~] 'is' 'very' [['hard' ~] 'to' 'learn' ~] ~]`

**Q:** What does the `:-` rune take as its arguments?

**A:**  `:-` takes two arguments:

> the first one is any noun;  
> the second one is either a list or null.

KM: If we're going to introduce lists separately, introduce how cons
works with lists after that.

**Q:** What is `:-(s l)`

where

> `s` is `['a' 'b' ['c' ~] ~]`

and

> `l` is ~

**A:** `[['a' 'b' ['c' ~] ~] ~]`

> because `~` can act as a list.

**Q:** What is `:-(s l)`

where `s` is `'a'`

and `l` is `~`

**A:** `['a' ~]`

**Q:** What is `:-(s l)`

where

> `s` is `['a' 'b' 'c' ~]`

and

> `l` is `'b'`

**A:** No answer,<sup>1</sup>

> since the second argument `l` must be a list or null.

<sup>1</sup> In practice, `:-(a b)` works for all values `a` and `b`, and
```
(head :-(a b)) => a
(tail :-(a b)) => b
```

**Q:** What is `:-(s l)`

where `s` is `'a'`

and `l` is `'b'`

**A:** No answer.

> Why?

JL: This is not true.  The answer is `[a b]`.  At some point, TLS
starts talking about tuples.  When I get to that section, I'll possibly
come back and revise this.

---

### The Law of Colhep
#### The rune `:-` takes two arguments.  The second argument to `:-` must be a list or null.  The result is a list.

---

**Q:** What is `:-(s (head l))`

where `s` is `'a'`

and

> `l` is `[['b' ~] 'c' 'd' ~]`

**A:** `['a' 'b' ~]`

> Why?

**Q:** What is `:-(s (tail l))`

where `s` is `'a'`

and

> `l` is `[['b' ~] 'c' 'd' ~]`

**A:** `['a' 'c' 'd' ~]`

> Why?

- JL - The next section of TLS referst to empty lists and then
  introduces `null?`.  Hoon doesn't work this way, so I left it out.

- KM: At some point consider introducing `?~` (i.e. "if this thing is
  null") into the discussion of recursion.

**Q:** Is it true or false that `s` is an atom

where `s` is `'Harry'`

**A:** True,

> because `'Harry'` is a string of characters surrounded by single
> quotes.

**Q:** Is `.?(s)` true or false<sup>1</sup>

where

> `s` is `'Harry'`

**A:** False,

> because `.?` is another way to ask "Is `s` a list?"

**Q:** How do you pronounce `.?`

**A:** `.?` is pronounced 'dotwut'

**Q:** Is `.?(s)` true or false

where

> `s` is `['Harry' 'had' 'a' 'heap' 'of' 'apples' ~]`

**A:** True,

> since `s` is a list.

**Q:** How many arguments does `.?` take and what are they?

**A:** It takes one argument.  The argument can be any noun.

**Q:** Is `.?((head l))` true or false

where

> `l` is `['Harry' 'had' 'a' 'heap' 'of' 'apples' ~]`

**A:** False,

> because `(head l)` is 'Harry', and 'Harry' is an atom.

**Q:** Is `.?((tail l))` true or false

where

> `l` is `['Harry' 'had' 'a' 'heap' 'of' 'apples' ~]`

**A:** True

**Q:** Is `.?((tail l))` true or false

where

> `l` is `['Harry' ~]`

**A:** False,

> because the null value, `~`, acts as an atom here.

**Q:** Is `.?((head (tail l)))` true or false

where

> `l` is `['swing' 'low' 'sweet' 'cherry' 'oat' ~]`

**A:** False

> because `(tail l)` is `['low' 'sweet' 'cherry' 'oat' ~]`
> and `(head (tail l))` is `'low'`, which is an atom.

**Q:** Is `.?((head (tail l)))` true or false

where

> `l` is `['swing' ['low' 'sweet' ~] 'cherry' 'oat' ~]`

**A:** True,

> since `(tail l)` is `[['low' 'sweet' ~] 'cherry' 'oat' ~]`
> and `(head (tail l))` is `['low' 'sweet' ~]`, which is a list.

**Q:** True or false, `a1` and `a2` are the same atom

where `a1` is `'Harry'`

and

> `a2` is `'Harry'`

**A:** True,

> because `a1` is the atom `'Harry'` and `a2` is the atom `'Harry'`.

**Q:** Is `.=(a1 a2)` true or false

where `a1` is `'Harry'`

and

> `a2` is `'Harry'`

**A:** True,

> because `.=(a1 a2)` is just another way to ask, "Are a1 and a2 the
> same non-numerica atom?"

**Q:** How do you pronounce `.=`

**A:** `.=` is pronounced 'dottis'

**Q:** Is `.=(a1 a2)` true or false

where `a1` is `'margarine'`

and

> `a2` is `'butter'`

**A:** False,

> since `a1` and `a2` are different atoms.

**Q:** How many argumenst does `.=` take and what are they?

**A:** It takes two arguments.  Both of them must be non-numeric atoms.

**Q:** Is `.=(l1 l2)` true or false.

where `l1` is `['raspberry' ~]`

and 

> `l2` is `['strawberry' ~]`

**A:** No answer<sup>1</sup>,

> `['raspberry' ~]` and `['strawberry' ~]` are lists.

<sup>1</sup> In practice, lists may be arguments of `.=`.  `.=` is true
for two lists if they are the same list.

JL - I'm deeply ambivalent about this answer being "No answer", but I'll
stick with it for now.

JL - TLS also says that `eq?` only works with non-numeric atoms.  I seem
to recall Scheme having some distinction between numeric and non-numeric data
which Hoon doesn't have.

---

### The Law of Dottis
#### The rune `.=` takes two arguments.  Each must be an atom.

---

**Q:** Is `.=((head l) a)` true or false

where

> `l` is `['Mary' 'had' 'a' 'little' 'lamb' 'chop' ~]`

and

> `a` is `'Mary'`

**A:** True,

> because `(head l)` is the atom `'Mary'`, and the argument `a` is also
> the atom `'Mary'`.

**Q:** Is `.=((tail l) a)` true or false

where

> `l` is `['soured' 'milk' ~]`

and

> `a` is `'milk'`.

**A:** No answer.

> See The Laws of Dottis and Tail

**Q:** Is `.=((head l) (head (tail l)))` true or false

where

> `l` is `['beans' 'beans' 'we' 'need' 'jelly' 'beans' ~]`

**A:** True

> because it compares the first and second atoms in the list

### =&gt; Now go make yourself a peanut butter and jelly sandwich &lt;=

<br>
<br>
<br>
<br>


<center>
This space reserved for   
**JELLY STAINS!**
</center>

<br>
<br>
<br>
<br>


<a name="chap2"></a>

## 2. Do It, Do It Again, and Again, and Again ...

- JL: `is-lat` is right now specified as a generator.  I would prefer for
  it to be a regular function that just runs in the dojo like `head`.
  There's some experimental features that could make this happen, but
  they're very new.  I'll stick with generators for now.

**Q:** True or false: `+is-lat l`

where

> `l` is `['Jack' 'Sprat' 'could' 'eat' 'no' 'chicken' 'fat' ~]`

**A:** True,

> because each noun in `l` is an atom.

**Q:** True or false: `+is-lat l`

where

> `l` is `[['Jack' ~] 'Sprat' 'could' 'eat' 'no' 'chicken' 'fat' ~]`

**A:** False,

> since `(head l)` is a list.

**Q:** True or false: `+is-lat l`

where

> `l` is `['Jack' ['Sprat' 'could' ~] 'eat' 'no' 'chicken' 'fat' ~]`

**A:** False,

> since one of the nouns in `l` is a list.

**Q:** True or false: `+is-lat l`

where

> `l` is `~`

**A:** True,

> because `~` is the empty list, and therefore doesn't contain any lists.

**Q:** True or false: a lat is a list of atoms.

**A:** True!

> Every lat is a list of atoms!

**Q:** Write the function `is-lat` using some, but not necessarily all
of the following:

> `head`, `tail`, `:-`, `.?`, `.=`

**A:** You were not expected to be able to do this yet, because you are
still missing some ingredients.  Go on to the next question.  
Good luck.

<br>
<br>
<br>
<br>
<center>**Are you rested?**</center>
<br>
<br>
<br>
<br>

**Q:** Here are the contents of the generator `is-lat.hoon`<sup>1</sup>

```
|=  l=(list)
|-
?:  .=(~ l)
  %.y
?:  .?((head l))
  %.n
$(l (tail l))
```

What is the value of `+is-lat l`

where

> `l` is the argument `['bacon' 'and' 'eggs' ~]`

<sup>1</sup> Add this code to a file called `gen/is-lat.hoon` inside your
`home` directory.  Then run the following in the dojo:
```
|commit %home
```

**A:** `%.y`

> The application of `+is-lat l`  
> where  

> > `l` is `['bacon' 'and' 'eggs' ~]`

> has the value `%.y`&mdash;true&mdash;because `l` is a lat.

**Q:** How do we determine the answer `%.y` for the function 

> `+is-lat l`

**A:** You were not expected to know this one either.  The answer is
determined by answering the questions asked by `+is-lat`

> Hint: Write down the contents of `is-lat.hoon` and refer to
> it for the next group of questions.

**Q:** What does the first line of `+is-lat` do?

> `|=  l=(list)`

**A:** The rune `|=` (pronounced 'bartis') declares a function.
According to the rules of `|=` the next thing should be the arguments
for the function.

**Q:** What are the arguments?

**A:** `l=(list)`

**Q:** What is the meaning of

> `l=(list)`

**A:** This function has one argument, named `l`.  It is type of type `list`.

**Q:** What does the next line do?

> `|-`

**A:** `|-` (pronounced 'barhep') sets a restart point.  More on this in
a bit.

**Q:** What is the next line?

**A:** `?:  .=(~ l)`

**Q:** What does `?:` do?

**A:** `?:` (pronounced 'wutcol') asks a question.  If the answer is
"true", it performs the next action.  If the answer is "false", it skips
the next action, and keep going.

**Q:** What the question that `?:` is asking?

**A:** `=(~ l)`

**Q:** What is the meaning of

> `.=(~ l)`

where

> `l` is `['bacon' 'and' 'eggs' ~]`

**A:** `.=(~ l)` asks if the argument `l` is equal to null.  In this
case, `l` is not the null value, so the answer is false.

**Q:** So what do we do?

**A:** Per the rules of `?:`, if the answer to the question is true, we 
evaluate the next action, which is

> `%.y`

on the next line, and the value of the function is true.  If the answer 
to the question is false, we keep going.

Since the answer is false, we skip the `%.y` and keep going.

**Q:** What comes next?

**A:** `?:  .?((head l))`

**Q:** What is the question for `?:`

**A:** `.?((head l))`

**Q:** What is the meaning of

> `?((head l))`

where

> `l` is `['bacon' 'and' 'eggs' ~]`

**A:** `.?((head l))` asks if the first noun of the list `l` is a list.
In this case, `(head l)` is an atom, so the answer if false.

**Q:** So what do we do?

**A:** Per the rules of `?:`, if the answer to the question is true, we 
evaluate the next action, which is 

> `%.n`

on the next line, and the value of the function is false.  If the answer 
to the question is false, we keep going.

Since the answer is false, we skip the `%.n` and keep going.

**Q:** What comes next?

**A:** `$(l (tail l))`

**Q:** What is the meaning of 

>  `$(l (tail l))`

**A:** `$(l (tail l))` finds out if the rest of the list `l` is composed
only of atoms, by returning to our restart point `|-` (or 'barhep') with
a new value for `l`.

**Q:** What is the new value for `l`?

**A:** The new value for `l` is `(tail l)`, which is 

> `['and' 'eggs' ~]`

**Q:** Why do we use `(tail l)`?

**Q:** What is after our restart point, `|-`?

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `['and' 'eggs' ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.  
Per the rules of `?:`, if the answer to the question is true, we 
evaluate the next action, which is 

> `%.y`

on the next line, and the value of the function is true.  If the answer 
to the question is false, we keep going.

In this case, l is not the null value, so we skip the `%.y` and keep going.

**Q:** What is next?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is now `['and' 'eggs' ~]`

**A:** `.?((head l))` asks if `(head l)` is a list.
Per the rules of `?:`, if the answer to the question is true, we 
evaluate the next action, which is 

> `%.n`

on the next line, and the value of the function is false.  If the answer 
to the question is false, we keep going.

In this case, `(head l)` is not a list, so we skip the `%.n` and keep going.

**Q:** What is the meaning of 

>  `$(l (tail l))`

**A:** `$(l (tail l))` finds out if the rest of the list `l` is composed
only of atoms, by returning to our restart point `|-` with a new value for 
`l`.  This new value is `(tail l)` or `['eggs' ~]`.

**Q:** What is the after `|-`

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `['eggs' ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.  
Per the rules of `?:`, since l is not the null value, so we skip the `%.y` 
and keep going.

**Q:** What is next?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is now `['eggs' ~]`

**A:** `.?((head l))` asks if the argument `l` is null.  
Per the rules of `?:`, since `(head l)` is not a list, we skip the `%.n` and keep going.

**Q:** What is the meaning of 

>  `$(l (tail l))`

**A:** `$(l (tail l))` finds out if the rest of the list `l` is composed
only of atoms, by returning to our restart point `|-`, with l becoming the value of `(tail l)`.

**Q:** Now, what is the new value for `l`?

**A:** `~`

**Q:** What is the line after `|-`?

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `~`

**A:** `.=(~ l)` asks if the argument `l` is null.  
Per the rules of `?:`, since l is the null value, we evaluate the next action, which is the value `%.y`, or true.  Therefore, the value of

> `+is-lat l`  

where

> `l` is `['bacon' 'and' 'eggs' ~]` is `%.y`&mdash;true

**Q:** Do you remember the question about

> `+is-lat l`

**A:** Probably not.  `+is-lat l` has the value of `%.y`
if the list `l` is a list of atoms where 

> `l` is `['bacon' 'and' 'eggs' ~]`

**Q:** Can you describe what the generator `+is-lat` does in your own
words?

**A:** Here are our words:

> "`+is-lat` looks at each noun in a list, in turn, and asks if each
> noun is an atom, until it gets to the null value.  If it runs out
> without encountering a list, the value is `%.y`.  If it finds a list,
> the value is `%.n`&mdash;false."

To see how we could arrive at a value of "false", consider the next few
questions.

**Q:** Here are the contents of `+is-lat` again.
```
|=  l=(list)
|-
?:  .=(~ l)
  %.y
?:  .?((head l))
  %.n
$(l (tail l))
```

What is the value of `+is-lat l`

where

> `l` is now `['bacon' ['and' 'eggs' ~] ~]`

**A:** `%.n`

> since the list `l` contains a noun that is a list.

**Q:** What is the first line after the restart point, `|-`

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of

> `?:  .=(~ l)`

where

> `l` is `['bacon' ['and' 'eggs' ~] ~]`

**A:** `.=(~ l)` asks if the argument `l` is equal to null.  
Per the rules of `?:`, since `l` is not the null value, we skip the
`%.y` and keep going.

**Q:** What is next?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is `['bacon' ['and' 'eggs' ~] ~]`

**A:** `.?((head l))` asks if the first noun of the list `l` is a list.
Per the rules of `?:`, since `(head l)` is not a list, we skip the `%.n`
and keep going.

**Q:** What is the meaning of 

>  `$(l (tail l))`

**A:** `$(l (tail l))` checks to see if the rest of the list `l` is composed
only of atoms, by returning to our restart point `|-` with l replaced by
`(tail l)`.

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `[['and' 'eggs' ~] ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.
Per the rules of `?:`, since `l` is not null, we skip the `%.y` and keep
going.

**Q:** What is next?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is now `[['and' 'eggs' ~] ~]`

**A:** `.?((head l))` asks if the first noun of the list `l` is a list.
Per the rules of `?:`, since `(head l)` is a list, we evaluate the next
action, which is `%.n`.  
So the answer is `%.n`&mdash;false.

**Q:** Can you describe how we determined the value `%.n` for

>  `+is-lat l`

where

> `l` is `['bacon' ['and' 'eggs' ~] ~]`

**A:** Here is one way to say it:

> "`+is-lat l` looks at each item in its argument to see if it is an
> atom.  If it runs out of items before it finds a list, the value of
> `+is-lat l` is `%.y`.  If it finds a list, as it did in the example
> `['bacon' ['and' 'eggs' ~] ~]`, the value of `+is-lat l` is `%.n`."

**Q:** What does the first line of `+is-lat` again?

**A:** `|=  l=(list)`

**Q:** What is the meaning of

> `l=(list)`

**A:** The function has one argument,  named `l`.  It is of type `list`.

**Q:** What would be the result if you called `+is-lat l`  
where

> `l` is `'cheeseburger'`

**A:** No answer,

Because the argument `l` must be a list.

**Q:** What would be the result if you called `+is-lat l`  
where

> `l` is `~`

**A:** It would return `%.y`

Because `~` is the only atom that is also a list, in this case it is a list of zero nouns. And since an empty list does not contain any lists, it returns true, or `%.y`.

Hint: Look at the code one last time and make sure this is true.

JL: This is a deep concept which kind of muddies the waters here.  I'm seeing 
now why TLS made a scheme function called `atom?`, which explicitly ruled 
out `~`.  I'm okay with how we describe things here, but I'm open to
hearing about why this is wrong.

**Q:** Is `?|(.=(~ l1) .=(~ l2))` true or false
where `l1` is `~`   
and  

> `l2` is `['d' 'e' 'f' 'g' ~]`

**A:** True,

> because `.=(~ l1)`is true where `l1` is `~`.

**Q:** Is `?|(.=(~ l1) .=(~ l2))` true or false
where 

> `l1` is `['a' 'b' 'c' ~]`   

and  

> `l2` is `~`

**A:** True,

> because `.=(~ l2)`is true where `l2` is `~`.

**Q:** Is `?|(.=(~ l1) .=(~ l2))` true or false
where 

> `l1` is `['a' 'b' 'c' ~]`   

and  

> `l2` is `['atom' ~]`

**A:** False,

> because neither `.=(~ l1)` nor `.=(~ l2)` are true where

> `l1` is `['a' 'b' 'c' ~]`

and 

> `l2` is `['atom' ~]`

**Q:** Is this true or false:

```
?|
  .=(~ l1)
  .=(~ l2)
==
```

where 

> `l1` is `['a' 'b' 'c' ~]`   

and  

> `l2` is `['atom' ~]`


**A:** False,

because

```
?|
  .=(~ l1)
  .=(~ l2)
==
```

is another way of writing

```
?|(.=(~ l1) .=(~ l2))
```

**Q:** Does `?|` only work with two questions?

**A:** No,

you can ask as many questions as you want.

**Q:** What does `?|(...)` do?

**A:** `?|` (pronounced 'wutbar') asks questions, one at a time.  If
the first one is true it stops and answers true.  Otherwise it asks the
next question.  It continues until it gets to the final question, and
answers with whatever the last question answers.

This is sometimes called the 'OR' operation.

**Q:** What form of Hoon is this?

```
?|(.=(~ l1) .=(~ l2))
```

**A:** This is called "short form".

**Q:** What form of Hoon is this?

```
?|
  .=(~ l1)
  .=(~ l2)
==
```

**A:** This is called "tall form".

**Q:** What does the `==` in the tall form do?

**A:** In tall form, `?|` uses what's called a "running series".  You can keep asking questions, and you use `==` to say that you are finished.

**Q:** Is it true or false that `a` is a member of `lat`  
where `a` is `'tea'`  
and

> `lat` is `['coffee' 'tea' 'or' 'milk' ~]`

**A:**  True,

> because one of the atoms of the lat,
> > `['coffee' 'tea' 'or' 'milk' ~]`
> is the same as the atom `a`&mdash;tea.

**Q:** Is `+is-member [a lat]` true or false  
where `a` is 'poached'  
and

> `lat` is `['fried' 'eggs' 'and' 'scrambled' eggs' ~]`

**A:** False,

> since `a` is not one of the atoms of the lat.

**Q:**   

Here are the contents of the generator `is-member.hoon`<sup>1</sup>

```
|=  [a=@ lat=(list @)]
|-
?:  .=(~ lat)
  %.n
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

What is the value of `+is-member [a lat]`

where `a` is `'meat`   
and

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

<sup>1</sup> Add this code to a file called `gen/is-member.hoon` inside your
`home` directory.  Don't forget to run `|commit %home` in the dojo!

**A:** `%.y`,

> because the atom `'meat'` is one of the atoms of `lat`,
> > `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** How do we determine the value `%.y` for the calling
`+is-member [a lat]`?

**A:** The value is determined by asking the questions in `+is-member
[a lat]`.

> Hint: Write down the contents of `is-member.hoon` and refer to
> it while you work on the next group of questions.

**Q:** What does the first line of `+is-member` do?

> `|=  [a=@ lat=(list @)]`

**A:** The `|=` declares a function.  This function has a pair of
arguments.

**Q:** What are the arguments?

**A:** `a=@`

and

> `lat=(list @)`

**Q:** What is the meaning of

> `a=@`

**A:** The first argument is named `a`.  The `@` (pronounced 'pat') means its type is an atom.

**Q:** What is the meaning of

> `lat=(list @)`

**A:** The second argument is named `lat`. The `(list @)` means its type is a lat or list of atoms.

**Q:** What does the second line do?

> `|-`

**A:** It sets the restart point.

**Q:** What is after the restart point?

**A:** `?:  .=(~ lat)`

> This is also the first question asked by `is-lat`

---

###  The First Commandment
##### (preliminary)
#### Always ask if a list is null as the first question in expressing any function.

---

**Q:** What is the meaning of the line

> `?:  .=(~ lat)`

where 

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `.=(~ lat)` asks if lat is the null value, `~`.  
Per the rules of `?:`, since `lat` is not null, we skip the `%.n` and keep going.

**Q:** What is the meaning of:
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** Now that we know that `lat` is not `~`, we have to find out
whether the `head` of `lat` is the same atom as `a`, or whether `a` is
somewhere in the rest of `lat`.  The answer to
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

does this.

**Q:** True or false
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

where `a` is `'meat'`  
and

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**A:** We will find out by looking at each question in turn.

**Q:** Is `.=((head lat) a)` true or false  
where `a` is `'meat'`  
and

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**A:** False,

because `'meat'` is not equal to `'mashed'`, which is the `head` of

> `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** What is the second question of `?|`

**A:** `$(lat (tail lat))`

> This refers to returing to our restart point with `lat` replaced by
> `(tail lat)`.

**Q:** Now what are the two arguments?

**A:** `a` is `'meat'`  

and

> `lat` is now `(tail lat)`, specifically  
> `['potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** What comes next?

**A:** `?:  .=(~ lat)`

> Remember The First Commandment

**Q:** is `.=(~ lat)` true or false  
where

> `lat` is `['potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.n`&mdash;false

**Q:** What do we do now?

**A:** Skip the `%.n` and keep going

**Q:** What's the meaning of 

```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```
> 

**A:** 
This finds out if `a` is equal to the `head` of `lat` or if `a` is a member
of the `tail` of `lat` by returning to the restart point.

**Q:** Is `a` equal to the `head` of `lat`?

**A:** No, because `a` is `'meat'` and the `head` of `lat` is
`'potatoes'`.

**Q:** What do we do next?

**A:** We return to our restart point.

**Q:** Now, what are our arguments?

**A:** `a` is `'meat'`, and  
`lat` is `['and' 'meat' 'gravy' ~]`

**Q:**  What comes next?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Skip over the `%.n` and keep going, since `.=(~ lat)` is false.

**Q:** What is the value of

```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** The value of `$(lat (tail lat))`.

**Q:** Why?

**A:** Because `.=((head lat) a)` is false.

**Q:** What do we do now?

**A:** Recur&mdash;return to the restart point with new arguments.

**Q:** What are the new arguments?

**A:** `a` is `'meat'`, and  
`lat` is `['meat' 'gravy' ~]`

**Q:** What comes next?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Since `.=(~ lat)` is false, we skip over the `%.n` and keep going

**Q:** What is the value of
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `%.y`,
> because `(head lat)`, which is `'meat'`, and `a`, which is `'meat'`,
> are the same atom.  
> Therefore, `?|` answers with `%.t`.

**Q:** What is the value of

> `+is-member [a lat]`

where `a` is `'meat'`  
and   

> `lat` is `['meat' 'gravy' ~]`

**A:** `%.t`,

> because we have found that `'meat'` is a member of `['meat' 'gravy' ~]`.

**Q:** What is the value of

> `+is-member [a lat]`

where `a` is `'meat'`  
and   

> `lat` is `['and' 'meat' 'gravy' ~]`

**A:** `%.t`,

> because `'meat'` is also a member of the `lat` `['and' 'meat' 'gravy' ~]`

**Q:** What is the value of

> `+is-member [a lat]`

where `a` is `'meat'`  
and   

> `lat` is `['potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.t`,

> because `'meat'` is also a member of the `lat` `['potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** What is the value of

> `+is-member [a lat]`

where `a` is `'meat'`  
and   

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.t`,

> because `'meat'` is also a member of the `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`  
> Of course, this is our original `lat`.

**Q:** Just to make sure you have it right, let's quickly run through it
again.  What is the value of `+is-member [a lat]`  
where

> `a` is `'meat'`

and

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.y`.

> Hint: Write down the contents of `is-member.hoon` and its arguments
> and refer to them as you go through the next group of questions.

**Q:** `?:  .=(~ lat)`

**A:**  No.  Skip the `%.n` and keep going.

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** Perhaps

**Q:** `.=((head lat) a)`

**A:** No.  Ask the next question.

**Q:** What next?

**A:** Return to the restart point with `a` and `(tail lat)`  
where `a` is `'meat'`  
and

> `(tail lat)` is `['potatoes' 'and' 'meat' 'gravy' ~]`


KM:  Technically, `|-` creates a 'trap' (core with one arm, named `$`) and then 
executes it.  This is actually the thing that is recursing.  

I think my description is pretty close to what's happening, but verify
that my description isn't more misleading than necessary.  We aren't
calling the 'gate' (defined by `|=`) we are calling the trap (defined by
`|-`).

**Q:** `?:  .=(~ lat)`

**A:** No.  Skip the `%.n` and keep going.

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> So return to `|-`  with `a` and `(tail lat)`  
> where  `a` is `'meat'`  
> and  
> > `(tail lat)` is `['and' 'meat' 'gravy' ~]`

**Q:**  `?:  .=(~ lat)`

**A:**  No.  Skip the `%.n` and keep going.

**Q:**
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Return to `|-`  with `a` and `(tail lat)`  
> where  `a` is `'meat'`  
> and  
> > `(tail lat)` is `['meat' 'gravy' ~]`

**Q:**  `?:  .=(~ lat)`

**A:**  No.  Skip the `%.n` and keep going.

**Q:** `.=((head lat) a)`

**A:** Yes, the value is `%.y`.

**Q:**
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `%.y`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `'meat'`  
and  

> `lat` is `['meat' 'gravy' ~]`

**A:** `%.y`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `'meat'`  
and  

> `lat` is `['and' 'meat' 'gravy' ~]`

**A:** `%.y`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `'meat'`  
and  

> `lat` is `['potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.y`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `'meat'`  
and  

> `lat` is `['mashed' 'potatoes' 'and 'meat' 'gravy' ~]`

**A:** `%.y`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `'liver'`  
and  

> `lat` is `['bagels' 'and' 'lox' ~]`

**A:** `%.n`

**Q:** Let's work out why it is `%.n`.  What's the first question
`+is-member` asks?

**A:** `?:  .=(~ lat)`

**Q:** `.=(~ lat)`

**A:** No.  Skip the `%.n` and keep going

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Return to the `|-`  with `a` and `(tail lat)`  
> where  `a` is `'liver'`  
> and  
> > `(tail lat)` is `['and' 'lox' ~]`

**Q:** `?: .=(~ lat)`

**A:** No.  Skip the `%.n` and keep going

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Return to the `|-`  with `a` and `(tail lat)`  
> where  `a` is `'liver'`  
> and  
> > `(tail lat)` is `['lox' ~]`

**Q:** `?:  .=(~ lat)`

**A:** No.  Skip the `%.n` and keep going

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Return to the `|-`  with `a` and `(tail lat)`  
> where  `a` is `'liver'`  
> and  
> > `(tail lat)` is `~`

**Q:** `?:  .=(~ lat)`

**A:** Yes.

**Q:** What do we do now?

**A:** Since `.=(~ lat)` is true, we return the next value, which is
`%.n`.

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `'liver'`  
and  

> `lat` is `~`

**A:** `%.n`

**Q:** What is the value of
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```
where  

> `a` is `'liver'`

and

> `lat` is `['lox' ~]`


**A:** `%.n`

**Q:** What is the value of
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```
where  

> `a` is `'liver'`

and

> `lat` is `['and' 'lox' ~]`


**A:** `%.n`

**Q:** What is the value of
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```
where  

> `a` is `'liver'`

and

> `lat` is `['bagels' 'and' 'lox' ~]`


**A:** `%.n`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `'liver'`  
and  

> `lat` is `['bagels' 'and' 'lox' ~]`

**A:** `%.n`

KM: Add a section about calling this with a list of lists.

<center>Do you believe all this? Then you may rest!</center>

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

### This space for doodling

<a name="chap3"></a>

## 3. Colhep the Magnificent

JL: TLS calls this "Cons the Magnificent", but we're focusing on the
`:-` rune in this chapter.


**Q:** What is `+rember [a lat]`  
where `a` is `'mint'`  
and  

>`lat` is `['lamb' 'chops' 'and' 'mint' 'jelly' ~]`

**A:** `['lamb' 'chops' 'and' 'jelly' ~]`

> `rember` stands for `rem`ove  mem`ber`.

**Q:** `+rember [a lat]`  
where `a` is `'mint'`  
and

> `lat` is `['lamb' 'chops' 'and' 'mint' 'flavored' 'mint' 'jelly' ~]`

**A:** `['lamb' 'chops' 'and' 'flavored' 'mint' 'jelly' ~]`

**Q:** `+rember [a lat]`  
where `a` is `'cup'`  
and  

> `lat` is `['coffee' 'cup' 'tea' 'cup' 'and' 'hick' 'cup' ~]`

**A:** `['coffee' 'tea' 'cup' 'and' 'hick' 'cup' ~]`

**Q:** What does `+rember [a lat]` do?

**A:** It takes an atom and a lat as its arguments, and makes a new lat
with the first occurrence of the atom in the old lat removed.

**Q:** What steps should we use to do this?

**A:** First we need to declare a function with two arguments, `a`,
which is an atom, and `lat`, which is a list of atoms.

**Q:** How would we do that?

**A:** `|=  [a=@ lat=(list @)]`

**Q:** What is the first question?

**A:** First we will test `.=(~ lat)`&mdash;The First Commandment.

**Q:** And if `.=(~ lat)` is true?

**A:** Return `~`, the empty list.

**Q:** What do we know if `.=(~ lat)` is not true?

**A:** We know that there must be at least one atom in the lat.

**Q:** Is there any other question we should ask about the lat?

**A:** No.  Either a lat is empty or it contains at least one atom.

KM: Introduce `?~` here?

**Q:** What do we do if we know that the lat contains at least one atom?

**A:** We ask whether `a` is equal to `(head lat)`.

**Q:** How do we ask questions?

**A:** By using `?:`, i.e. 'wutcol'

**Q:** How do we ask if `a` is the same as `(head lat)`?

**A:** `.=((head lat) a)`

KM: Use dottis in chapter 1 to introduce tall form

**Q:** What would be the value of `+rember [a lat]` if a were the same
as `(head lat)`?

**A:** `(tail lat)`

**Q:** What do we do if the `a` is not the same as `(head lat)`

**A:** We want to keep `(head lat)`, but also find out if `a` is
somewhere in the rest of the lat.

**Q:** How do we remove the first occurrance of `a` in the rest of `lat`

**A:** `+rember [a (tail lat)]`

**Q:** Is there any other question we should ask?

**A:** No.

**Q:** Since we are going to call the function again with different
arguments, what do we need to add?

**A:** We need `|-`, our restart point, right before we start asking
questions.

**Q:** Now, let's write down what we have so far<sup>1</sup>:

```
|=  [a=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) a)
  (tail lat)
$(lat (tail lat))
```


KM: Well...  This works.  Except... it casts the tapes into `@ud`.
So... deal with this.  Maybe update the code to handle tapes
KM: Also check the above generators to make sure they don't do the same
thing.

What is the value of `+rember [a lat]` where  

> `a` is `'bacon'`

and

> `lat` is `['bacon' 'lettuce' 'and' 'tomato' ~]`

<sup>1</sup> You can put this code in a file `gen/rember.hoon` in your home
directory to try it out (don't forget to `|commit %home`).  

KM: Add questions about the first line
KM: Note that we are going to recurse, so you need a `|-`

**A:** `['lettuce' 'and' 'tomato' ~]`

> Hint: write down the code for `+rember` and its arguments, and refer
> to them as you go through the next sequence of questions.

**Q:** Now, let's see if this function works.  What is the first
question?

**A:** `?:  .=(~ lat)`

KM: Either we should do `?~` here or we should wait until we revisit the
First Commandment

**Q:**  What do we do now?

**A:** Skip the `~` and ask the next question.

**Q:** What is the next question?

**A:** `?:  .=((head lat) a)`

**Q:** So what do we do?

**A:** `.=((head lat) a)` is true, so the value is `(tail lat)`  In this
case, it is the list

> `['lettuce' 'and' 'tomato' ~]`

**Q:**  Is this the correct value?

**A:** Yes, because it is the original list without the atom `'bacon'`

**Q:** But did we really use a good example?

**A:** Who knows?  But the proof of the pudding is in the eating, so
let's try another example.

**Q:** What does `+rember` do?

**A:** It takes an atom and a lat as its arguments and makes a new lat
with the first occurrence of the atom in the old lat removed.

**Q:** What do we do now?

**A:** We compare each atom of the lat with the atom `a`, and if the
comparison fails we build a list that begins with the atom we just
compared.

**Q:** What is the value of `+rember [a lat]`  
where `a` is `'and'`  
and  

> `lat` is `['bacon' 'lettuce' 'and' 'tomato' ~]`

**A:** `['bacon' 'lettuce' 'tomato' ~]`

**Q:** Let us see if our function `rember` works.  
What is the first question asked by `rember`?


**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Skip the `~`, and ask the next question.

**Q:** `?:  .=((head lat) a)`

**A:** No, so skip over `(tail lat)` and keep going.

**Q:** What is the meaning of  
`$(lat (tail lat))`

**A:** Go back to our restart point `|-` with `lat` set to `(tail lat)`
or `['lettuce' 'and' 'tomato' ~]`

**Q:** `?:  .=(~ lat)`

**A:** No, skip over `~` and continue

**Q:** `?:  .=((head lat) a)`

**A:** No, skip over `(tail lat)` and keep going.

**Q:** What is the meaning of  
`$(lat (tail lat))`

**A:** Recur  
setting `lat` to `(tail lat)` or `['and' 'tomato' ~]`

**Q:** `?:  .=(~ lat)`

**A:** No, skip over `~` and continue

**Q:** `?:  .=((head lat) a)`

**A:** Yes.  The result is the value of the next line.

**Q:** So what is the result?

**A:** `(tail lat)`&mdash;`['tomato' ~]`

**Q:** Is this correct?

**A:** No, since `['tomato' ~]` is not the list  

> `['bacon' 'lettuce' 'and' 'tomato' ~]`

with just `a`&mdash;`'and`&mdash;removed.

**Q:** What did we do wrong?

**A:** We dropped `'and'`, but we also lost all the atoms preceding
`'and`.

**Q:** How can wek eep from losing the atoms

> `'bacon'` `'and'` `'lettuce'`

**A:** We use Colhep the Magnificent.  Remember `:-` from chapter 1?

---

###  The Second Commandment
#### Use `:-` to build lists.

---

**Q:** Let's see what happens when we use `:-`
```
|=  [a=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) a)
  (tail lat)
:-  (head lat)
$(lat (tail lat))
```

What is the value of `+rember [a lat]`  
where `a` is `'and'`  
and  

> `lat` is `['bacon' 'lettuce' 'and' 'tomato' ~]`

**A:** `['bacon' 'lettuce' 'tomato' ~]`

**Q:** What is the first question?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Skip `(tail lat)` and continue

**Q:** What is the meaning of
```
:-  (head lat)
$(lat (tail lat))
```

where

> `a` is `'and'`

and

> `lat` is `['bacon' 'lettuce' 'and' 'tomato' ~]`

KM: this bit is an argument for using pairs instead of lists, and adding
lists as a type of pair.

**A:** It says to 'cons' the `head` of `lat`&mdash;`'bacon'`&mdash; onto
the value of  

> `+rember`, using `(tail lat)` as the value for `lat`

But since we don't know the value of `+rember` using `(tail lat)` yet,
we must find out before we can 'cons' `(tail lat)` onto it.

KM: Should I ditch the word "cons"? Use something like "combine"

KM: This thing with `|-` and `$(lat (tail lat))` is a bit hand-wavey.  I'm
considering actually describing what barhep does i.e. makes a new function
(i.e. `arm`) called `$`. 

KM: I'm also considering using `%=` to describe that we're calling this 
function `$` with new parameters.

**Q:** What is the meaning of `$(lat (tail lat))`

**A:** Go back to the restart point with `lat` replaced by `(tail
lat)`&mdash;`['lettuce' 'and' 'tomato' ~]`

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((car lat) a)`

**A:** No, so skip `(tail lat)` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
$(lat (tail lat))
```

**A:** It says to 'cons' the `tail` of
`lat`&mdash;`'lettuce'`&mdash;onto the value of 

> `+rember`, using `(tail lat)` as the value for `lat`

But since we don't know the value of `+rember` using `(tail lat)` yet,
we must find out before we can 'cons' `(tail lat)` onto it.

KM: Is this actually true?  Can you start the 'cons' with a promise that
you'll fill in the details at some point?
KM: Really need to rework this to make it a bit more accurate.

**Q:** What is the meaning of `$(lat (tail lat))`

**A:** Go back to the restart point with `lat` replaced by `(tail
lat)`, that is, `['and' 'tomato' ~]`.

**Q:** `?:  .=(~ lat)`

**A:** No, so skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Yes, so return the next value.

**Q:** What is the next value?

**A:** `(tail lat)`&mdash;`['tomato' ~]`

**Q:** Are we finished?

**A:** Certainly not!  We know what `+rember [a lat]` is when `lat` is
`['and' 'tomato' ~]`, but we don't yet know what it is when `lat` is

> `['lettuce' 'and' 'tomato' ~]`

or

> `['bacon' 'lettuce' 'and' 'tomato' ~]`

**Q:** We now have a value for 

> `+rember [a (tail lat)]`

where `a` is `'and'`  
and

> `(tail lat)` is `['and' 'tomato' ~]`

This value is `['tomato' ~]`  
What next?

**A:** Recall that we wanted to 'cons' `'lettuce'` onto the value of
`+rember [a (tail lat)]`  
where  

> `a` was `'and'` and `(tail lat)` was `['and' 'tomato' ~]`

Now that we have this value, which is `['tomato' ~]`, we can 'cons'
`'lettuce'` onto it.

**Q:** What is the result when we 'cons' `'lettuce'` onto `['tomato' ~]`

**A:** `['lettuce' 'tomato' ~]`

**Q:** What does `['lettuce' 'tomato' ~]` represent?

**A:** It represents the value of
```
:-  (head lat)
$(lat (tail lat))
```

when

> `lat` is `['lettuce' 'and' 'tomato' ~]`

and

> `$(lat (tail lat))` is `['tomato' ~]`


**Q:** Are we finished yet?

**A:** Not quite.  So far we know what `+rember [a lat]` is when

> `lat` is `['lettuce' 'and' 'tomato' ~]`,

but we don't yet know what it is when

> `lat` is `['bacon' 'lettuce' 'and' 'tomato' ~]`

KM: If we're going to introduce pairs/cells before lists, we should make a
point to mention _why_ we use lists, i.e. if we don't know exactly how
many items we are going to be dealing with.

**Q:** Now we have a value for `+rember [a (tail lat)]`  
where `a` is `'and'`  
and  

> `(tail lat)` is `['lettuce' 'and' 'tomato' ~]`

This value is `['lettuce' 'tomato' ~]`  
This is not the final value, so what must we do again?

**A:** Recall that, at one time, we wanted to 'cons' `'bacon'` onto the
value of `+rember [a (tail lat)]`,  
where

> `a` is `'and'`

and 

> `(tail lat)` was `['lettuce' 'and' 'tomato' ~]`

Now that we have this value, which is `['lettuce' 'tomato' ~]`,  
we can 'cons' `'bacon'` onto it.

**Q:** What is the result when we 'cons' `'bacon'` onto `['lettuce'
'tomato' ~]`

**A:** `['bacon' 'lettuce' 'tomato' ~]`

**Q:** What does `['bacon' 'lettuce' 'tomato' ~]` represent?&dagger;

&dagger; Lunch?

**A:** It represents the value of
```
:-  (head lat)
$(lat (tail lat))
```

when

> `lat` is `['bacon' 'lettuce' 'and' 'tomato' ~]`

and

> `+rember [a (tail lat)]` was `['lettuce' 'tomato' ~]`

**Q:** Are we finished yet?

**A:** Yes.

**Q:** Can you put into your own words how we determined the final value

> `['bacon' 'lettuce' 'tomato' ~]`

**A:** In our words:

> "The function `rember` checked each atom of the lat, one at a time, to
> see if it was the same atom `'and'`.  If the `car` was not the same as
> the atom, we saved it to be 'cons'-ed to the final value later.  When
> `rember` found the atom  `'and'`, it dropped it, and 'cons'-ed the
> previous atoms back onto the rest of the lat."

JL: At this point TLS has a digression where they refactor the function
they'd just been working through,  Fewer if-then-else and one big
`cond`.  This doesn't make sense for what we're doing here.

**Q:**  Now that we have completed `rember`, try this example: `+rember
[a lat]` where `a` is `'sauce'`  
and  

> `lat` is `['soy' 'sauce' 'and' 'tomato' 'sauce' ~]`

**A:**  `+rember [a lat]` is `['soy' 'and' 'tomato' 'sauce' ~]`

**Q:** What is `+firsts l`  
where  `l` is 
```
[['apple' 'peach' 'pumpkin' ~]
 ['plum' 'pear' 'cherry' ~]
 ['grape' 'raisin' 'pea' ~]
 ['bean' 'carrot' 'eggplant' ~] ~]
```

**A:**  `['apple' 'plum' 'grape' 'bean' ~]`

**Q:** What is `+firsts l`  
where  

> `l` is `[['a' 'b' ~] ['c' 'd' ~] ['e' 'f' ~] ~]`

**A:** `['a' 'c' 'e' ~]`

**Q:** What is `+firsts l`  
where `l` is `~`

**A:** `~`

**Q:** What is `+firsts l`  
where `l` is 
```
[['five' 'plums' ~]
 ['four' ~]
 ['eleven' 'green' 'oranges' ~]]
```

**A:** `['five 'four' 'eleven' ~]`

**Q:** What is `+firsts l`  
where `l` is
```
[[['five' 'plums' ~] 'four' ~]
  ['eleven' 'green' 'oranges' ~]
  [['no' ~] 'more' ~] ~]
```

**A:** `[['five' 'plums' ~] 'eleven' ['no' ~] ~]`

**Q:** In your own words, what does `+firsts l` do?

**A:** We tried the following:

> "The functions `firsts` takes one argument, a list, which is either a
> null list or contains only non-empty lists.  It builds another list
> composed of the first noun of each internal list"

**Q:** See if you can write the function `firsts`

> Remember the Commandments!

**A:**  This much is easy:
```
|=  l=list
|-
?:  .=(~ l)
  ...
:-  ...
$(l (tail l))
```

KM: Change the elipses to `!!` and explain what that means
  
**Q:** Why 
```
|=  l=list
```

**A:** Because we need to create a function, and the argument is a list

**Q:** Why `|-`

**A:** Because we can only look at one noun at a time.  To look at the
rest, we must recur.  

**Q:** Why `?:  .=(~ l)`

**A:** The First Commandment

**Q:** Why is this the only question?

**A:** Because the list `l` is either the null list, or it contains at
least one non-empty list.

**Q:** Why `:-`

**A:** Because we are building a list&mdash;The Second Commandment

**Q:** Why `$(l (tail l))`

**A:** Because at this point, we need to look at the rest of the list.

**Q:** Keeping in mind the definition of `+firsts l`  
what is the typical element of the value of `+firsts l`  
where  

> `l` is `[['a' 'b' ~] ['c' 'd' ~] ['e' 'f' ~] ~]`

**A:**  `'a'`

**Q:** What is another typical element?

**A:** `'c'` or even `'e'`

**Q:** Consider the function `+seconds`  
What would be a typical element of the value of `+seconds l`
where  

> `l` is `[['a' 'b' ~] ['c' 'd' ~] ['e' 'f' ~] ~]`

**A:** `'b'`, `'d'`, or `'f'`.

**Q:** How do we describe a typical element for `+firsts l`

**A:** As the `head` of an element of `l`&mdash;(head (head l)).

> See chapter 1

**Q:** When we find a typical element of `firsts l` what do we do with
it?

**A:** 'cons' it onto the recursion&mdash;`$(l (tail l))`

---

###  The Third Commandment
#### When building a list, describe the first typical element, then 'cons' it onto the natural recursion.

---


**Q:** With The Third Commandment, we can now fill in more of the
function `firsts`.  
What do the last two lines look like now?

**A:** 
```
:-  (head (head l))  :: typical element
$(l (tail l))        :: natural recursion
```

Note: `::`  (pronounced 'colcol') is a way to make a "comment", a note
to yourself or whoever else might read your code

**Q:** What does `+firsts l` do
```
|=  l=list
|-
?:  .=(~ l)
  !!
:-  (head (head l))
$(l (tail l))
```

where `l` is `[['a' 'b' ~] ['c' 'd' ~] ['e' 'f' ~] ~]`
 
**A:** Nothing yet.  We are still missing one important ingredient in
our recipe.  The line `?:  .=(~ l)` needs a value for the case where `l`
is the null list.  We can, however, proceed without it for now.

**Q:** `?:  .=(~ l)` where `l` is `[['a' 'b' ~] ['c' 'd' ~] ['e' 'f' ~] ~]`

**A:** No, so skip the `!!` and keep going.

**Q:** What is the meaning of
```
:-  (head (head l))
$(l (tail l))
```

**A:** It saves `(head (head l))` to 'cons' onto `$(l (tail l))`.  To
find `$(l (tail l))`, we return to our restart point with the new
argument `(tail l)`

KM: Rework these descriptions.  We don't actually call `+firsts`, we call
$, so make sure this is clear.

**Q:** `?:  .=(~ l)`  
where

> `l` is `[['c' 'd' ~] ['e' 'f' ~] ~]`

**A:** No, so skip `!!` and keep going.

**Q:** What is the meaning of
```
:-  (head (head l))
$(l (tail l))
```

**A:** Save `(head (head l))`, and recur with `l` set to  `(tail l)`.

**Q:** `?:  .=(~ l)`  
where

> `l` is `[['e' 'f' ~] ~]`

**A:** No, so skip `!!` and keep going.

**Q:** What is the meaning of
```
:-  (head (head l))
$(l (tail l))
```

**A:** Save `(head (head l))`, and recur with `l` set to  `(tail l)`.

**Q:** `?:  .=(~ l)`

**A:** Yes.

**Q:** Now, what is the value of the line
```
!!
```

**A:** Crash!  This is where we need to add our value.

**Q:** What do we need to `con` atoms onto?

**A:** A list.

> Remember the Law of Colhep.

KM: The law of colhep is true, but only if you're working with lists.
To make a list with colhep the second argument must be another list.
Fix this in chap 1.

**Q:** For the purposes of 'cons'-ing, what value can we give when `.=(~
l)` is true?

**A:** Since the final value must be a list, we cannot use `%.y` or
`%.n`.  Let's try `~`.

**Q:** With `~` as a value, we now have three 'cons' steps to go back
and pick up.  We need to:

> I. either

> > 1. 'cons' `'e'` onto `~` 
> > 2. 'cons' `'c'` onto the value of 1
> > 3. 'cons' `'a'` onto the value of 2

> II. or

> > 1. 'cons' `'a'` onto the value of 2
> > 2. 'cons' `'c'` onto the value of 3
> > 3. 'cons' `'e'` onto `~`

> III. or

> > 'cons' `'a'` onto
> > > the 'cons' of `'c'` onto
> > > > the 'cons' of `'e'` onto
> > > > > `~`

In any case, what is the value of `+firsts l`

**A:** `['a' 'c' 'e' ~]`

**Q:** With which of the three alternatives do you feel most
comfortable?

**A:** Correct!  Now you should use that one.

**Q:** What is `+insert-r [new old lat]`  
where

> `new` is `'topping'`  
> `old` is `'fudge'`

and

> `lat` is `['ice' 'cream' 'with' 'fudge' 'for' 'dessert' ~]`

**A:** `['ice' 'cream' 'with' 'fudge' 'topping' 'for' 'dessert' ~]`

**Q:** `+insert-r [new old lat]`  
where

> `new` is `'jalapeño'`  
> `old` is `'and'`

and 

> `lat` is `['tacos' 'tamales' 'and' 'salsa' ~]`

**A:** `['tacos' 'tamales' 'and' 'jalapeño' 'salsa' ~]`

**Q:** `+insert-r [new old lat]`  
where  

> `new` is `'e'`  
> `old` is `'d'`

and

> `lat` is `['a' 'b' 'c' 'd' 'e' 'f' 'g' 'd' 'h' ~]`

**A:** `['a' 'b' 'c' 'd' 'e' 'f' 'g' 'd' 'h' ~]`

**Q:** In your own words, what does  
`+insert-r [new old lat]` do?

**A:** In our words:

> "It takes three arguments: the atoms `new` and `old`, and a lat.  The
> generator `+insert-r` builds a lat with `new` inserted to the right of
> the first occurrence of `old`."

**Q:** See if you can write the first two lines of the function
`insert-r`

**A:**
```
|=  [new=@ old=@ lat=(list @)]
|-

```

**Q:** Which argument changes when we recur with `insert-r`

**A:** `lat`, because we can only look at one of its atoms at a time.

**Q:** How many questions can we ask about the lat?

**A:** Two.

> A lat is either the null list or a non-empty list of atoms.

**Q:** Which question do we ask?

**A:** We ask `?:  .=(~ lat)`.

**Q:** What do we know if `.=(~ lat)` is not true?

**A:** We know that `lat` has at least one element.

KM: `?~` actually uses the type for null, i.e. `$~`.  As such, `.=(~
lat)` is not exactly the same a `?~`.  Although there might be a better
place for a deep dive into types.

**Q:** Which questions do we ask about the first element?

**A:** We ask `.=(~ (car lat) old)`.  Then we continue because there are
no other interesting case.

**Q:** Now see if you can write the code for `insert-r.hoon`
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  !!
  !!
?:  !!
  !!
!!
```

Replacing `!!` with actual code.

**A:** Here's our first attempt.
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  (tail lat)
:-  (head lat)
$(lat (tail lat))
```

KM: This needs to use 'tape' otherwise, we can't inspect the results!

**Q:** What is the value of the generator

> `+insert-r [new old lat]`

that we just determined  
where

> `new` is `'topping'`
> `old` is `'fudge'`

and

> `lat` is `['ice' 'cream' 'with' 'fudge' 'for' 'dessert' ~]`

Hint: Try adding the code to `gen/insert-r.hoon`.  Then `|commit %home`
and try running it in the dojo.
```
> =new 'topping'
> =old 'fudge'
> =lat ['ice' 'cream' 'with' 'fudge' 'for' 'dessert' ~] 
> +insert-r [new old lat]
```

KM: verify that insert-r.hoon works as expected.
KM: make a dojo helper description above when we first do a generator.

**A:** `['ice' 'cream' 'with' 'for' 'dessert' ~]`

**Q:** So far this is the same as `rember`  What do we do in `insert-r` when `.=((head lat) old)` is true?

**A:** When `(head lat)` is the same as `old`, we want to insert `new`
to the right.

**Q:** How is this done?

**A:** Instead of returning `(tail lat)`, let's try 'cons'-ing `new` onto `(tail lat)`

**Q:** Now we have
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  (tail lat)
:-  (head lat)
$(lat (tail lat))
```

**A:** Yes.

**Q:** So what is `+insert-r [new old lat]` now
that we just determined  
where

> `new` is `'topping'`
> `old` is `'fudge'`

and

> `lat` is `['ice' 'cream' 'with' 'fudge' 'for' 'dessert' ~]`

**A:** `['ice' 'cream' 'with' 'topping' 'for' 'dessert' ~]`

**Q:** Is this the list we wanted?

**A:** No, we have only replaced `'fudge'` with `'topping'`.

**Q:** What still needs to be done?

**A:** Somehow we need to include the atom that is the same as `old`
before the atom `new`.

**Q:** How can we include `old` before `new`

**A:** Try 'cons'-ing `old` onto
```
:-  new
(tail lat)
```

**Q:** Now let's write the rest of the generator `+insert-r`

**A:** 
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  old
  :-  new
  (tail lat)
:-  (head lat)
$(lat (tail lat))
```

When `new` is `'topping'`, `old` is `'fudge'`, and `lat` is `['ice'
'cream' 'with' 'fudge' 'for' 'dessert' ~]`, the value of the generator
`+insert-r [new old lat]`, is 

> `['ice' 'cream' 'with' 'fudge' 'topping' 'for' 'dessert' ~]`

If you got this right, have one.

**Q:** Now try `+insert-l`

Hint: `+insert-l` inserts the atom `new` to the left of the first
occurrence of the atom `old` in `lat`

**A:** This much is easy, right?
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  :-  old
  (tail lat)
:-  (head lat)
$(lat (tail lat))
```

**Q:** Did you think of a different way to do it?


**A:** For example,
```
?:  .=((head lat) old)
  :-  new
  :-  old
  (tail lat)
```

could have been

```
?:  .=((head lat) old)
  :-  new
  lat
```

since `:-(old (tail lat))` where `old` is equal to `(head lat)` is the
same as `lat`.

**Q:** Now try `+subst`

> Hint: `+subst [new old lat]` replaces the first occurrence of `old`
> in the `lat` with `new`.  
> For example,  
> where  

> > `new` is `'topping'`  
> > `old` is `'fudge'`

> and

> > `lat` is `['ice' 'cream' 'with' 'fudge' 'for' 'dessert' ~]`

> the value is

> > `['ice' 'cream' 'with' 'topping' 'for' 'dessert' ~]`

> Now you have the idea.

**A:** Obviously,
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  (tail lat)
:-  (head lat)
$(lat (tail lat))
```

This is the same as one of our incorrect attempts at `+insert-r`

---

<center>**Go cons a piece of cake onto your mouth.**</center>

---

**Q:** Now try `+subst2`

> Hint: 

> > `+subst2 [new o1 o2 lat]` 

> replaces either the first occurrence of `o1` or the first occurrence
> of `o2` by `new`  
> For example,  
> where  

> > `new` is `'vanilla'`  
> > `o1` is `'chocolate'`
> > `o2` is `'banana'`

> and

> > `lat` is `['banana' 'ice' 'cream' 'with' 'chocolate' 'topping' ~]`

> the value is

> > `['vanilla' 'ice' 'cream' 'with' 'chocolate' 'topping' ~]`

**A:** 
```
|=  [new=@ o1=@ o2=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) o1)
  :-  new
  (tail lat)
?:  .=((head lat) o2)
  :-  new
  (tail lat)
:-  (head lat)
$(lat (tail lat))
```

**Q:** Did you think of a better way?


**A:** Combine the questions

> `?:  .=((head lat) o1)`

and

> `?:  .=((head lat) o2)`

with
```
?|
  .=((head lat) o1)
  .=((head lat) o2)
==
  :-  new
  (tail lat)
```

If you do this, you `subst2.hoon` will look like this:

```
|=  [new=@ o1=@ o2=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  ?|
      .=((head lat) o1)
      .=((head lat) o2)
    ==
  :-  new
  (tail lat)
:-  (head lat)
$(lat (tail lat))
~
```

---

<center>**If you got the last function, go and repeat the cake-consing.**</center>

---


**Q:** Do you recall what `rember` does?

**A:** The function `rember` looks at each atom of a lat to see if it is
the same as the atom `a`.  If it is not, `rember` saves the atom and
proceeds.  When it finds the first occurrence of `a`, it stops and gives
the value `(tail lat)`, or the rest of the lat, so that the value
returned is the original lat, with only that occurrence of `a` removed.

**Q:** Write the generator `multirember.hoon` which gives as its final
value the lat with all occurrences of `a` removed.
```
|=  [a=@ lat=(list @)]
|-
?:  !!
  !!
?:  !!
  !!
!!
```

Hint: What do we want as the value when 

> `.=((head lat) a)` is true?

Consider the example  
where `a` is `'cup'`  
and  

> `lat` is `['coffee' 'cup' 'tea' 'cup' 'and' 'hick' 'cup' ~]`
 

**A:** 
```
|=  [a=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) a)
  $(lat (tail lat))
:-  (head lat)
$(lat (tail lat))
```

> After the first occurrence of `a`, we now recur with `lat` set to
> `(tail lat)`, in order to remove the other occurrences.

The value of this generator is

> `['coffee' 'tea' 'and' 'hick' ~]`

**Q:** Can you see how `+multirember` works?

**A:** Possibly not, so we will go through the steps necessary to arrive
at the value

> `['coffee' 'tea' 'and' 'hick' ~]`

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** No, skip `$(lat (tail lat))` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
$(lat (tail lat))
```

**A:** Save `(head lat)`&mdash;`'coffee'`&mdash;to be 'cons'-ed onto the
result when we return to our restart point with `lat` set to `(tail lat)`.  
Now determine

> `$(lat (tail lat))`

**Q:** `?:  .=(~ lat)`

**A:** No, so skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Yes, so forget `(head lat)`, and return to the restart point with
`lat` set to `(tail lat)`.

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** No, so skip `$(lat (tail lat))` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
$(lat (tail lat))
```

**A:** Save `(head lat)`&mdash;`'tea'`&mdash;to be 'cons'-ed onto the
result when we return to our restart point with `lat` set to `(tail lat)`.  
Now determine

> `$(lat (tail lat))`

**Q:** `?:  .=(~ lat)`

**A:** No, so skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Yes, so forget `(head lat)`, and return to the restart point with
`lat` set to `(tail lat)`.

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** No, so skip `$(lat (tail lat))` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
$(lat (tail lat))
```

**A:** Save `(head lat)`&mdash;`'and'`&mdash;to be 'cons'-ed onto the
result when we return to our restart point with `lat` set to `(tail lat)`.  
Now determine

> `$(lat (tail lat))`

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** No, so skip `$(lat (tail lat))` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
$(lat (tail lat))
```

**A:** Save `(head lat)`&mdash;`'hick'`&mdash;to be 'cons'-ed onto the
result when we return to our restart point with `lat` set to `(tail lat)`.  
Now determine

> `$(lat (tail lat))`

**Q:** `?:  .=(~ lat)`

**A:** No, so skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Yes, so forget `(head lat)`, and return to the restart point with
`lat` set to `(tail lat)`.

**Q:** `?:  .=(~ lat)`

**A:** Yes, so the value is `~`

**Q:** Are we finished?

**A:** No, we still have several `cons`-es to pick up.

**Q:** What do we do next?

**A:** We 'cons' the most recent `(head lat)` we
have&mdash;`'hick'`&mdash;onto `~`.

**Q:** What do we do next?

**A:** We 'cons' `'and'` onto `['hick' ~]`

**Q:** What do we do next?

**A:** We 'cons' `'tea'` onto `['and' 'hick' ~]`

**Q:** What do we do next?

**A:** We 'cons' `'coffee'` onto `['tea' 'and' 'hick' ~]`

**Q:** Are we finished now?

**A:** Yes.

**Q:** Write the generator `+multiinsert-r`
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  !!
  !!
?:  !!
  !!
!!
```

**A:** 
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  (head lat)
  :-  new
  $(lat (tail lat))
:-  (head lat)
$(lat (tail lat))
```

It would also be correct to use `old` in place of `(head lat)` in the
first `:-` on line 6 because we know that `.=((car lat) old)`

**Q:** Write the generator `+multiinsert-l`:
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  !!
  !!
?:  !!
  !!
!!
```

**A:** 
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  :-  old
  $(lat (tail lat))
:-  (head lat)
$(lat (tail lat))
```

---

###  The Fourth Commandment
##### (preliminary)
#### When recurring, always change one argument to be closer to termination.  The changing argument must be tested in the termination condition:
#### when using `(tail l)`, test termination with `.=(~ l)

---


**Q:** Now write the generator `multisubst`
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  !!
  !!
?:  !!
  !!
!!
```

**A:** 
```
|=  [new=@ old=@ lat=(list @)]
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  $(lat (tail lat))
:-  (head lat)
$(lat (tail lat))
```

<a name="chap4"></a>

## 4. Numbers Game

**A:** 

**Q:** 


