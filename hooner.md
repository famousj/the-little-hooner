# The Little Hooner

## Adapted from The Little Schemer (Fourth Edition) by Daniel P. Friedman and Matthias Felleisen
## Adaptation by ~ribben-donnyl (AKA J LeBlanc)

## The Ten Commandments

KM

## The <strike>Five</strike> Four Rules

###  The Law of Head
> The primitive `head` is defined only for non-null lists.

### The Law of Tail
> The primitive `tail` is defined only for non-null lists. The `tail` of any non-null list is always another list.

### The Law of Colhep
> The rune `:-` takes two arguments.  The second argument to `:-` must be a 
list or null.  The result is a list.

### The Law of Dottis</center>
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

## 0. Preface and Setup

### Introductory Notes

- I've tried to stick with how things are presented in The Little
  Schemer, but Hoon and Scheme are different languages and they just do
  things differently.

- If you see "JL", this is a translation note, or mentions a section
  that is original to this document.  

- TLS stands for "The Little Schemer".

- "KM" means "TODO", but it's two keystrokes shorter.

- The purpose of the original TLS is to understand how recursion works.
  This book has two purposes: to understand recursion but also to get
  your head around how writing code in Hoon works.

  So I occasionally toss in some Hoon concepts on top of the recursion
  concepts.  If it feels like a needless distraction, let me know.

- I'm on the fence about what style of lists to show.  You can either do 
```
[1 2 3 ~]
```
Or you can do
```
~[1 2 3]
```

I went with the former.  This is closer to how the data is being stored
interally.  Also this is also the format the dojo uses, in case
you want to check your answers.  

I would like to introduce the shorter version at some point, but haven't found a place yet.

- Scheme has a somewhat muddied distinction between code and data that Hoon
  doesn't have.  
  So in Scheme you can have a list, like `(a b c)` and prepend a soq, like 
  `'(a b c)`, to tell the compiler "everything in these parentheses are 
  data".  

  This isn't possible in Hoon.  Either that or I'm not aware of someway to do it.  So I've had to make all the words into cords.  This is not ideal, but it works.

- I am completely ignoring the fact that there are many data types other
  than atoms and lists.  Pairs, tuples, trees, these are all ignored for
  now.  This is how TLS works, as well, with the occasional footnote for
  the "Um, akshually..." crowd.


- I'm using the `.=` rune for testing equality and the `:-` rune for
  the _cons_ operation.  I could see the advantage of doing `=(a b)`
  instead of `.=(a b)` or `[a b] instead of `[a b]`, but I want to
  introduce the concept of the two-character rune.

- I was going to refer to function as "gates", which is what the Hoon
  docs call them.  But a "gate" is not precisely a function, it's a
  "core" with one arm named `$`.  I'd rather not get into this, so I'm
  just going to call them "functions".  I'll try to peel back the covers
  on this whenver appropriate.

- If you see any egregious errors, pull requests are welcome!


### Introduction

KM

I plan to note here that what I'm describing is a superset of Hoon, and
that we'll add to it as we go along.  

### How to Read This Book - JL

-KM - Pull in content from the TLS preface here

This book is written in a question-and-answer format.  Go slowly!  Make sure you really understand a chapter before you go onto the next chapter.

To get started, you'll need a [running ship on Urbit](https://urbit.org/using/install/).  Developing on your own ship is Considered Harmful, so you might consider setting up a [development ship](https://urbit.org/using/develop/#creating-a-development-ship).

Once you're runnning, you'll have a dojo prompt.

If you want to assign a variable, you can do that like so:

```
> =a 42
> =l ['ham' 'and' 'eggs' ~]
```

You are highly encouraged to follow along and test things out in the dojo.  

When you get to chapter 2, you'll make something called a "generator",
which is a kind of program you can run on your Urbit ship.  

To do this, you'll need to run this command in the dojo
```
> |mount /=home=
```

You'll only have to do this once.  Once you do that, you'll get a folder
called "home" inside your ship's directory.  If you created a developer
ship, this will be `zod/home`.

### TODO

- Right now this is a Markdown file. It'd be pretty sweet to make some
  kind of interactive version where it doesn't give the answer until you
  hit the spacebar or something.

- The section to get the tools running is extremely manual.  Probably an
  opportunity to improve that

- Make a chapter describing the aura type system.  We could, in chapter
  1, mention that most of the atoms discussed here are "cords", but I
  thought we'd save that for a bit.

- Introduce tree addressing at some point.  Possibly a new, original
  chapter, since Scheme doesn't have anything like that.

- At some point (possibly in chapter 1) I should introduce the `:~` rune and discuss that this is a way to construct a null-terminated list. 

- In re: The Law of Null?, Hoon doesn't exactly have a function `null?`.
  What it does have is `?~`, but this has a built in if-then-else
  structure, so I'll introduce that later.

- Also need to figure out how to discuss "tall form".

- TLS uses a scaled-down superset of Scheme.  I'm kind of doing the same
  thing here with Hoon, but need to be a bit more transparent about what 
  you can and can't do in Hoon.

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

> `1492`

**A:** Yes,

> because `1492` is a number 

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

> `~` is the null value.  It's pronounced 'sig' or sometimes 'null'.  It
> is an atom with no value.

**Q:** Is it true that this is a list?

> `['atom' 'turkey' ~]`

**A:** Yes,

> because `['atom' 'turkey']` is two atoms enclosed by a left square
> bracket (which is called a '`sel`') and a right square bracket (which is 
> called a '`ser`'), ending in the null value (i.e. `~`).

**Q:** Is it true that this is a list?

> `['atom' ~]`

**A:** Yes,

> because `['atom' ~]` is an atom enclosed by '[' and ']' (i.e. 'sel' and
> 'ser'), ending in a null value, i.e. `~`.

**Q:** Is it true that this is a list?

> `['atom']`

**A:** No,

> because `['atom']` does not end in the null value, `~`.

**Q:** Is it true that this is a list?

> `['atom' 'turkey' 'or' ~]`

**A:** Yes,

> because it is a collection of atoms enclosed by square brackets,
> ending in null.

JL: This might be a good place to introduce list shorthand, i.e. ~['atom' 'turkey' 'or']

**Q:** Is it true that this is a list?

> `['atom' 'turkey' ~] 'or'`

**A:** No,

> because these are actually two nouns not enclosed by square brackets. 
> The first one is a list containing two atoms plus the null value, and the second one is just an atom.

JL: TLS uses the term "S-expression", but I'm using the Hoon term "noun"

**Q:** Is it true that this is a noun?

> `'xyz'`

**A:** Yes,

> because all atoms are nouns.

**Q:** Is it true that this is a noun?

> `['x' 'y' 'z' ~]`

**A:** Yes,

> because it is a list.

**Q:** Is it true that this is a noun?

> `[['x' 'y' ~] 'z' ~]`

**A:** Yes,

> because all lists are nouns.

**Q:** Is it true that this is a list

> `['how' 'are' 'you' 'doing' 'so' far' ~]`

**A:** Yes,

> because it is a collection of nouns enclosed by square brackets,
> ending in null.

**Q:**  How many nouns are in the list?

> `['how' 'are' 'you' 'doing' 'so' far' ~]`

and what are they?

**A:** Seven,

> `'how'`, `'are'`, `'you'`, `'doing'`, `'so'`, `'far'`, and `~`

**Q:** Is it true that this is a list?

> `[[['how' ~] 'are' ~] [['you' ~] ['doing' 'so' ~] ~] 'far' ~]`

**A:** Yes,

> because it is a collection of nouns enclosed by square brackets,
> ending in null.

**Q:** How many nouns are in the list

> `[[['how' ~] 'are' ~] [['you' ~] ['doing' 'so' ~] ~] 'far' ~]`

and what are they?

**A:** Four,

> `[['how' ~] 'are' ~]`, `[['you' ~] ['doing' 'so' ~] ~]`, `'far'`, and
> `~`, because it is a collection of nouns enclosed by square brackets,
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

**Q:** What is the `head` of `l` where `l` is the argument

> `['a' 'b' 'c' ~]`

**A:** `'a'`

> because `'a'` is the first atom on the list.

**Q:** What is the `head` of `l` where `l` is 

> `[['a' 'b' 'c' ~] 'x' 'y' 'z' ~]`

**A:**  `['a' 'b' 'c' ~]`

> because `['a' 'b' 'c' ~]` is the first noun of this list.

- TLS - "this non-empty list"

**Q:** What is the `head` of `l` where `l` is `hotdog`

**A:** No answer.

> You cannot ask for the `head` of an atom.

**Q:** What is the `head` of `l` where `l` is `~`

**A:** No answer.
 
> You cannot ask for the `head` of `~`, since `~` is the empty list.

---

###  The Law of Head
#### The primitive `head` is defined only for non-null lists.

---

**Q:** What is the `head` of `l` 

where

> `l` is `[[['hotdogs' ~] ~] ['and' ~] ['pickle' ~] 'relish' ~]`

**A:** `[['hotdogs' ~] ~]`

read as:

> "The list of the list of 'hotdogs'."

`[['hotdogs' ~] ~]` is the first noun of `l`

**Q:** What is `(head l)`

where

> `l` is `[[['hotdogs' ~] ~] ['and' ~] ['pickle' ~] 'relish' ~]`

**A:** `[['hotdogs' ~] ~]`

> because `(head l)` is another way to ask for "the `head` of the list
> `l`"

**Q:** What is `(head (head l))`

where

> `l` is `[[['hotdogs' ~] ~] ['and' ~] ~]`

**A:** `[hotdogs ~]`

**Q:** What is the `tail` of `l`

where

> `l` is `['a' b' 'c' ~]`

- TLS uses `cdr`

**A:** `['b' 'c' ~]`

> because `['b' 'c' ~]` is the list `l` without `(head l)`.

**Q:** What is the `tail` of `l`

where

> `l` is `[['a' 'b' 'c' ~] 'x' 'y' 'z' ~]`

**A:** `['x' 'y' 'z' ~]`

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
#### The primitive `tail` is defined only for non-null lists. The `tail` of any non-null list is always another list.

---

**Q:** What is `(head (tail l))`

where

> `l` is `[['b' ~] ['x' 'y' ~] [['c' ~] ~] ~]`

**A:** `['x' 'y' ~]`,

> because `[['x' 'y' ~] [['c' ~] ~] ~]` is `(tail l)` and `['x'  'y' ~]`
> is the `head` of `(tail l)`.

**Q:** What is `(tail (tail l))`

where

> `l` is `[['b' ~] ['x' 'y' ~] [['c' ~] ~] ~]`

**A:** `[[['c' ~] ~] ~]`

> because `[['x' 'y' ~] [['c' ~] ~] ~]` is `(tail l)` and `[[['c' ~] ~]
 ~]` is the `tail` of `(tail l)`.

**Q:** What is `(tail (head l))`

where

> `l` is `['a' ['b' ['c' ~] ~] 'd' ~]`

**A:** No answer,

> since `(head l)` is an atom, and `tail` does not take an atom as an
> argument; see The Law of Tail.

**Q:** What does `head` take as an argument?

**A:** It takes any non-empty list.

**Q:** What does `tail` take as an argument?

**A:**  It takes any non-empty list.

**Q:** What is the _cons_ of the atom `a` and the list `l` 

where `a` is `'peanut'`

and

> `l` is `['butter' 'and' 'jelly' ~]`

This can be written as `:-(a l)`

> You can read this as: "_cons_ the atom `a` onto the list `l`"

**A:** `['peanut' 'butter' 'and' 'jelly' ~]`

> because the _cons_ operation adds an atom to the front of the list.

**Q:** What is the _cons_ of `s` and `l`

where `s` is `['banana' 'and' ~]`

and

> `l` is `['peanut' 'butter' 'and' 'jelly' ~]`

**A:** `[['banana' 'and' ~] 'peanut' 'butter' 'and' 'jelly' ~]`

> because _cons_ adds any noun to the front of a list.

- JL: I thought this would be a good spot for a brief digression about runes 
and ASCII pronunciation.

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

- KM: At some point consider introducing `?~` into the disucssion of
  recursion.

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

**Q:** 

Here are the contents of the generator `is-lat.hoon`<sup>1</sup>

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

KM: Add a section in the intro about `|mount`ing home and the necessity
of `|commit`ing.

**A:** `%.y`

> The application of `+is-lat l`  
> where  
> > `l` is `['bacon' 'and' 'eggs' ~]`
> has the value `%.y` &mdash; true &mdash; because `l` is a lat.

**Q:** How do we determine the answer `%.y` for the function 

> `+is-lat l`

**A:** You were not expected to know this one either.  The answer is
determined by answering the questions asked by `+is-lat`

> Hint: Write down the contents of `is-lat.hoon` and refer to
> it for the next group of questions.

**Q:** What does the first line of `+is-lat` do?

> `|=  l=(list)`

**A:** It declares a function with one parameter.

**Q:** What is the one parameter?

**A:** `l=(list)`

**Q:** What is the meaning of

> `l=(list)`

**A:** The parameter is named `l` and it is of type `list`.

**Q:** What is the first question asked by `+is-lat l`

**A:** `?:  .=(~ l)`

> Note:
> `?:` (or 'wutdot') asks questions;
> `|=` (or 'bartis') creates a function; and
> `|-` (or 'barhep') sets a restart point (more on this shortly)

**Q:** What is the meaning of

> `?:  .=(~ l)`

where

> `l` is `['bacon' 'and' 'eggs' ~]`

**A:** `.=(~ l)` asks if the argument `l` is equal to null.  If this is true, we evaluate the next line, which is

> `%.y`

This means the value for the function will be "true".  If `l` is not null, we
keep going.  

In this case, `l` is not the null value, so we keep going and ask the next question.

**Q:** What is the next question?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is `['bacon' 'and' 'eggs' ~]`

**A:** `.?((head l))` asks if the first noun of the list `l` is a list.
If this is true, we evaluate the next line, which is

> `%.n`

This means the value for the function will be "false".  If `(head l)` is an 
atom, we keep going.

In this case, `(head l)` is an atom, so we keep going.

**Q:** What is the meaning of 

>  `$(l (tail l))`

**A:** `$(l (tail l))` finds out if the rest of the list `l` is composed
only of atoms, by returning to our restart point `|-` (or 'barhep') with
a new value for `l`.

**Q:** What is the new value for `l`?

**A:** The new value for `l` is `(tail l)`, which is 

> `['and' 'eggs' ~]`

**Q:** What is the next question after `|-`?

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `['and' 'eggs' ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.  If this is true, we
evaluate the next line, which is 

> `%.y`

This means the value for the function will be "true".  If `l` is not null, we keep going.  

In this case, `l` is not the null value, so we keep going and ask the next question.

**Q:** What is the next question?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is now `['and' 'eggs' ~]`

**A:** `.?((head l))` asks if the first noun of the list `l` is a list.
If this is true, we evaluate the next line, which is

> `%.n`

This means the value for the function will be "false".  If `(head
l)` is an atom, we keep going.

In this case, `(head l)` is an atom.  So we keep going.

**Q:** What is the meaning of 

>  `$(l (tail l))`

**A:** `$(l (tail l))` finds out if the rest of the list `l` is composed
only of atoms, by returning to our restart point `|-` with a new value for 
`l`.  This new value is `(tail l)` or `['eggs' ~]`.

**Q:** What is the next question after `|-`?

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where
> `l` is now `['eggs' ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.  If this is true, we
evaluate the next line, which is

> `%.y`

which means the value for the function will be "true".  If `l` is not null, we keep going.  

In this case, `l` is not the null value, so we keep going and ask the next question.

**Q:** What is the next question?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is now `['eggs' ~]`

**A:** `.?((head l))` asks if the first noun of the list `l` is a list.
If this is true, we evaluate the next line, which is

> `%.n`

Which means the value for the function will be "false".  If `(head l)` is an 
atom, we keep going.

In this case, `(head l)` is an atom.  So we keep going.

KM: In TLS (in the next section) they clarify that if something isn't an
empty list or an atom, it must be a list.

**Q:** What is the meaning of 

>  `$(l (tail l))`

**A:** `$(l (tail l))` finds out if the rest of the list `l` is composed
only of atoms, by returning to our restart point `|-`, with l becoming the value of `(tail l)`.

**Q:** Now, what is the new value for `l`?

**A:** `[~]`

**Q:** What is the next question after `|-`?

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `[~]`

**A:** `.=(~ l)` asks if the argument `l` is null.  If it is null, we
evaluate the next line, which is

> `%.y`

which means the value for the function will be "true".  If `l` is not null, we keep going.  

In this case, `l` is equal to the null value.  So, the value of the
function

> `+is-lat l`

where `l` is `['bacon' 'and' 'eggs' ~]` is `%.y`&mdash;true.

**Q:** Do you remember the question about

> `+is-lat l`

**A:** Probably not.  The function `+is-lat l` has the value of `%.y`
if the list `l` is a list of atoms where 

> `l` is `['bacon' 'and' 'eggs' ~]`

**Q:** Can you describe what the function `+is-lat` does in your own
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

**Q:** What is the first question?

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of

> `?:  .=(~ l)`

where

> `l` is `['bacon' ['and' 'eggs' ~] ~]`

**A:** `.=(~ l)` asks if the argument `l` is equal to null.  If this is true, we return the value on the next line, `%.y`.  If not, we keep going.  In this case, it is not null, so we ask the next question.

**Q:** What is the next question?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is `['bacon' ['and' 'eggs' ~] ~]`

**A:** `.?((head l))` asks if the first noun of the list `l` is a list.
If it is, the return the value on the next line, `%.n`.  If not, we keep
n this case, `(head l)` is an atom, so we keep going. 

**Q:** What is the meaning of 

>  `$(l (tail l))`

**A:** `$(l (tail l))` checks to see if the rest of the list `l` is composed
only of atoms, by returning to our restart point `|-` with l replaced by
`(tail l)`.

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `[['and' 'eggs' ~] ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.  If this is true, we
return `%.y`.  If not, we keep going.  In this case, `l` is not null, so
we move to the next question.

**Q:** What is the next question?

**A:** `?:  .?((head l))`

**Q:** What is the meaning of

> `?:  .?((head l))`

where

> `l` is now `[['and' 'eggs' ~] ~]`

**A:** `.?((head l))` asks if the first noun of the list `l` is a list.
If this is true, we evaluate the next line, which is

> `%.n`

If `(head l)` is an atom, we keep going.

In this case, `(head l)` is a a list.  So the answer is
`%.n`&mdash;false.

**Q:** Can you describe how we determined the value `%.n` for

>  `+is-lat l`

where

> `l` is `['bacon' ['and' 'eggs' ~] ~]`

**A:** Here is one way to say it:

> "`+is-lat l` looks at each item in its argument to see if it is an
> atom.  If it runs out of items before it finds a list, the value of
> `+is-lat l` is `%.y`.  If it finds a list, as it did in the example
> `['bacon' ['and' 'eggs' ~] ~]`, the value of `+is-lat l` is `%.n`."

JL: A brief digression where we talk about declaring gates in Hoon

KM: Introduce the word "gate" at some point before now.

**Q:** What does the first line of `+is-lat` again?

**A:** `|=  l=(list)`

**Q:** What is the meaning of

> `l=(list)`

**A:** The parameter is named `l` and it is of type `list`.

**Q:** What would be the result if you called `+is-lat l`  
where

> `l` is `'cheeseburger'`

**A:** No answer,

Because the parameter `l` must be a list.

**Q:** What would be the result if you called `+is-lat l`  
where

> `l` is `~`

**A:** It would return `%.y`

Because `~` is the only atom that is also a list, in this case it is a list of zero nouns. And since an empty list does not contain any lists, it returns true, or `%.y`.

KM: Add this fact (~ is both an atom and a zero-item list) tidbit to chapter 1

KM: This is a deep concept which kind of muddies the waters here.  I'm seeing now why TLS made a function called `atom?`, which explicitly ruled out `~`.  Revisit the decision to exclude `atom?`.

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

> because `.=(~ l1)` nor `.=(~ l2)` is true where

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

**Q:** What does the `==` do?

**A:** In tall form, `?|` uses what's called a "running series".  You use `==` to finish asking questions.

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
`home` directory.  Then run the following in the dojo:
```
|commit %home
```

**A:** `%.y`,

> because the atom `'meat'` is one of the atoms of `lat`,
> > `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** How do we determine the value `%.y` for the above application?

**A:** The value is determined by asking the questions about `%is-member
[a lat]`.

> Hint: Write down the contents of `is-member.hoon` and refer to
> it while you work on the next group of questions.

**Q:** What does the first line of `+is-member` do?

> `|=  [a=@ lat=(list @)]`

**A:** The `|=` declares a function.  This function has a pair of
parameters.

**Q:** What are the parameters

**A:** `a=@`

and

> `lat=(list @)`

**Q:** What is the meaning of

> `a=@`

**A:** The first parameter is named `a` and its type is an atom (i.e. `@`).

**Q:** What is the meaning of

> `lat=(list @)`

**A:** The second parameter is named `lat` and its type is a list of atoms (i.e. `(list @)`).

**Q:** What does the second line do?

> `|-`

**A:** It sets the return point 

**Q:** What is the first question?

> `+is-member [a lat]`

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

**A:** `.=(~ lat)` asks if lat is the null value, `~`.  If it is, we have an empty list, so the atom `'meat'` was not in `lat`.  So we evaluate the next line, which is `$.n`.  If `lat` is not `~`, we keep going.

In this case, it is not null, so we keep going.

**Q:** What is the meaning of:
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** Now that we know that `lat` is not `~`, we have to find out
whether the `head` of `lat` is the same atom as `a`, or whether `a` is
somewhere in the rest of `lat`.  The answer
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

does this

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

**A:** We will find out by looking at each question in turn

**Q:** Is `.=((head lat) a)` true or false  
where `a` is `'meat'`  
and

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**A:** False,

because `'meat'` is not equal to `'mashed'`,  
the `head` of

> `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** What is the second question of `?|`

**A:** `$(lat (tail lat))`

> This refers to returing to our restart point with `lat` replaced by
> `(tail lat)`.

**Q:** Now what are the two parameters?

**A:** `a` is `'meat'`  
and

> `lat` is now `(tail lat)`, specifically
> `['potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** What is the next question?

**A:** `.=(~ lat)`

> Remember The First Commandment

**Q:** is `.=(~ lat)` true or false  
where

> `lat` is `['potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.n`&mdash;false

**Q:** What do we do now?

**A:** Keep going

**Q:** What's the meaning of 

```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```
> 

**A:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

finds out if `a` is equal to the `head` of `lat` or if `a` is a member
of the `tail` of `lat` by returning to the restart point.


**Q:** Is `a` equal to the `head` of `lat`?

**A:** No, because `a` is `'meat'` and the `head` of `lat` is
`'potatoes'`.

**Q:** What do we do next?

**A:** We return to our restart point.

**Q:** Now, what are our parameters?

**A:** `a` is `'meat'`, and  
`lat` is `['and' 'meat' 'gravy' ~]`

**Q:**  What is the next question?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Keep going, since `.=(~ lat)` is false.

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

KM: Decide on the terminology: argument or parameter?  And define them
at some point.

**Q:** What are the new arguments?

**A:** `a` is `'meat'`, and  
`lat` is `['meat' 'gravy' ~]`

**Q:** What is the next question?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Since `.=(~ lat)` is false, keep going

**Q:** What is the value of
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `%.t`,
> because `(head lat)`, which is `'meat'`, and `a`, which is `'meat'`,
> are the same atom.  
> Therefore, `?|` answers with `%.t`.

**Q:** What is the value of the application

> `+is-member [a lat]`

where `a` is `'meat'`  
and   

> `lat` is `['meat' 'gravy' ~]`

**A:** `%.t`,

> because we have found that `'meat'` is a member of `['meat' 'gravy' ~]`.

**Q:** What is the value of the application

> `+is-member [a lat]`

where `a` is `'meat'`  
and   

> `lat` is `['and' 'meat' 'gravy' ~]`

**A:** `%.t`,

> because `'meat'` is also a member of the `lat` `['and' 'meat' 'gravy' ~]`

**Q:** What is the value of the application

> `+is-member [a lat]`

where `a` is `'meat'`  
and   

> `lat` is `['potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.t`,

> because `'meat'` is also a member of the `lat` `['potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** What is the value of the application

> `+is-member [a lat]`

where `a` is `'meat'`  
and   

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.t`,

> because `'meat'` is also a member of the `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** Just to make sure you have it right, let's quickly run through it
again.  What is the value of `+is-member [a lat]`  
where

> `a` is `'meat'`

and

> `lat` is `['mashed' 'potatoes' 'and' 'meat' 'gravy' ~]`

**A:** `%.y`.

> Hint: Write down the contents of `is-member.hoon` and its arguments
> and refer to them as you go through the next group of questions.

**Q:** `.=(~ lat)`

**A:**  No.  Skip the next line and keep going

KM: Instead of "keep going", call out that you should skip a line

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

**Q:** What's next?

**A:** Return to the restart point with `a` and `(tail lat)`  
where `a` is `'meat'`  
and

> `(tail lat)` is `['potatoes' 'and' 'meat' 'gravy' ~]`

**Q:** `.=(~ lat)`

**A:** No.  Skip the next line and keep going.

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Return to the restart point  with `a` and `(tail lat)`  
> where  `a` is `'meat'`  
> and  
> > `(tail lat)` is `['and' 'meat' 'gravy' ~]`

**Q:**  `.=(~ lat)`

**A:**  No.  Skip the next line and keep going.

**Q:**
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Return to the restart point  with `a` and `(tail lat)`  
> where  `a` is `'meat'`  
> and  
> > `(tail lat)` is `['meat' 'gravy' ~]`

**Q:**  `.=(~ lat)`

**A:**  No.  Skip the next line and keep going.

KM: Really, explain how `?:` works.

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

**A:** `.=(~ lat)`

**Q:** `.=(~ lat)`

**A:** No.  Skip the next line and keep going

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a) is false.

> Return to the restart point  with `a` and `(tail lat)`  
> where  `a` is `'liver'`  
> and  
> > `(tail lat)` is `['and' 'lox' ~]`

**Q:** `.=(~ lat)`

**A:** No.  Skip the next line and keep going

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a) is false.

> Return to the restart point  with `a` and `(tail lat)`  
> where  `a` is `'liver'`  
> and  
> > `(tail lat)` is `['lox' ~]`

**Q:** `.=(~ lat)`

**A:** No.  Skip the next line and keep going

**Q:** 
```
?|
  .=((head lat) a)
  $(lat (tail lat))
==
```

**A:** `.=((head lat) a) is false.

> Return to the restart point  with `a` and `(tail lat)`  
> where  `a` is `'liver'`  
> and  
> > `(tail lat)` is `~`


**Q:** `.=(~ lat)`

**A:** Yes.

**Q:** What is the value on the next line?

**A:** `%.n`

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

> `lat` is `['lox']`


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

> `lat` is `['and' 'lox']`


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

> `lat` is `['bagels' 'and' 'lox']`


**A:** `%.n`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `'liver'`  
and  

> `lat` is `['bagels' 'and' 'lox' ~]`

**A:** `%.n`


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

## 3. Cons the Magnificent

