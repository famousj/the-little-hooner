# The Little Hooner

## Adapted from The Little Schemer (Fourth Edition) by Daniel P. Friedman and Matthias Felleisen
## Adaptation by ~ribben-donnyl (AKA J LeBlanc)

## The Ten Commandments

###  The First Commandment
> When recurring on a list of atoms, always ask if a list is null as the first question in expressing any function.  
> When recurring on a number, ask if the number is zero.

###  The Second Commandment
> Use `:-` to build lists.

###  The Third Commandment
> When building a list, describe the first typical element, then 'cons' it onto the natural recursion.

###  The Fourth Commandment
> Always change one argument when recurring.  It must be changed to be closer to termination.  The changing argument must be tested in the termination condition:  
> when using `tail`, test termination with `.=(~ l)  
> when using `sub1`, test termination with `.=(0 l)  


## The <strike>Five</strike> Four Rules

###  The Law of Head
> The primitive 'head' is defined only for cells and non-empty lists.

### The Law of Tail
> The primitive 'tail' is defined only for cells and non-empty lists.

### The Law of Colhep
> The rune `:-` takes two arguments.  To create a new list with `:-`, the 
> second argument must be a list or null.

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

### Preface

> "Tlön is surely a labyrinth, but it is a labyrinth devised
> by men, a labyrinth destined to be deciphered by men."
> - Jorge Luis Borges, "Tlön, Uqbar, Orbis Tertius"

> "Don't Panic!"
> - Douglas Adams, _The Hitchhiker's Guide to the Galaxy_

I first got on the Internet when I was in college in the 90s.  In those
benighted days, this often involved a dial-up modem.  If you only had 
one phone line and your roommate picked up the phone to make a phone call, 
you would get what's called line noise.

The email you were reading went from being regular text to being filled
with weird characters, and the email suddenly stopped making sense.  
It was really disorienting.

The first time I looked at Hoon, the programming language for Urbit, 
I had a similar feeling.

For instance, here's code to decrement an integer:

```
|=  a=@ud
^-  @ud
=/  b=@ud  0
|-
?:  .=  a  .+  b
  b
%=  $
  b  .+  b
==
```

Hoon is a member of the family of languages called [Lisps](https://en.wikipedia.org/wiki/Lisp_(programming_language).  

Conceptually, it works more or less like any other Lisp, but it looks totally different.  For starters, Hoon has no keywords.  Instead it uses what's called "runes", which are a set of two ASCII characters.  This helps contribute to its
austere "line-noise" look.

To help with this, each ASCII character in Hoon has a standard way of
pronoucing it, which this book will introduce as we go along.
This somehow makes programming in Hoon even weirder.  But if you're an 
auditory person (like me), you'll find this helps a lot to
make all these runes memorable.

Also, most languages in the Lisp family make use of parentheses.  Lots and lots of parentheses.  So many parentheses.

Hoon does away with the parentheses, but in a way that adds a bit more
conceptual overhead to really understand how things work.

Because it's a Lisp, Hoon is a functional programming language.
Programming in functional programming languages works differently than
you might be used to.

This book is an adaptation of the book The Little Schemer, which uses
the language Scheme, another Lisp and therefore a cousin of Hoon.  It goes 
very slowly, using very small programs to introduce and illustrate concepts 
that you'll need to understand to do programming in Hoon.

I assume zero experience in Hoon, or in any other programming
language for that matter.

It should be considered a starting point.  It won't teach you all
the Hoon you need to know to do anything on Urbit you'd like, but it
will give you the conceptual framework you need.

Enjoy!

- J LeBlanc

### Text Notes

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

- Scheme has a somewhat muddied distinction between code and data that Hoon
  doesn't have.  
  So in Scheme you can have a list, like `(a b c)` and prepend a soq, like 
  `'(a b c)`, to tell the compiler "everything in these parentheses are 
  data".  

  This isn't possible in Hoon.  Either that or I'm not aware of someway to 
  do it.  So I've had to make all the words into cords or terms.  This is 
  not ideal, but I think it works.

- Conceptually cells in Hoon act as binary trees.  I am more or less
  ignoring this fact.  I'm going to discuss atoms, cells, and lists.
  Also tuples when appropriate.

- Given the choice between brevity or clarity of code, I usually opt for
  clarity.  There are shorter ways of writing some things, irregular forms 
  that do away with the runes.  On most of these, I leave the rune in.

  At some point, I might make an appendix to note the other ways of
  doing things.

- If you see any egregious errors, DM me at ~ribben-donnyl. Also, pull 
requests are welcome!

- This has taken a non-trivial amount of time, and will require much more
time before it's done.


### How to Read This Book

This book is written in a question-and-answer format.  Go slowly!  Make sure you really understand a chapter before you go onto the next chapter.

To get started, you'll need a [running ship on Urbit](https://urbit.org/using/install/).  Developing on your own ship is Considered Harmful, so we recommend setting up a [development ship](https://urbit.org/using/develop/#creating-a-development-ship).

Once you're runnning, you'll have a dojo prompt.

If you want to assign a variable, you can do that like so:

```
> =a 42
> =l [%ham %and %eggs ~]
```

When you are finished with a variable, you can unbind it by running
something like:
```
> =a
> =l
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

> This kind of atom is called a 'cord'

**Q:** Is it true that this is an atom?

> `'u'`

**A:** Yes,

> because `'u'` is a string of one character, which is a letter.

**Q:** Is it true that this is an atom?

> `'*abc$🙃'`

**A:** Yes,

> because `'*abc$🙃'` is a string of characters.

**Q:** Is it true that this is an atom?

> `42`

**A:** Yes,

> because `42` is a number 

**Q:** Is it true that this is an atom?

> `%atom`

**A:** Yes,

> because `%atom` is a string of lower-case letters beginning with a `%`
> (pronounced 'cen')

> This kind of atom is called a 'term'

**Q:** Is it true that this is an atom?

> `%this-is-an-atom-2`

**A:** Yes,

> because `%atom` is a string of lower-case letters beginning with a
> `%`, along with numbers and the `-` (pronounced 'hep').

**Q:** Is it true that this is an atom?

> `%-atom`

**A:** No answer,

> because a term must start with the 'cen' and then a lower-case letter

**Q:** Is it true that this is an atom?

> `%🙃`

**A:** No answer,

> because a term cannot have special characters, only lower-case
> letters, numbers and `-`.

**Q:** Is it true that this is a cell?

> `[%grilled %cheese]`

**A:** Yes,

> because it is a pair of two atoms, the terms %grilled and %cheese, surrounded
> by a left square bracked (which is pronounced 'sel') and a right square
> bracket, which is pronounced 'ser'.

**Q:** Is it true that this is a cell?

> `%tomato %soup`

**A:** No,

> because cells must be enclosed in a 'sel' and a 'ser'.

**Q:** Is it true that this is a cell?

> `[%atom %turkey] 'or'`

**A:** No,

> these are actually two nouns, not enclosed by a 'sel' and a
> 'ser'.  The first one is a cell with two terms, and the second one is
> a cord.

**Q:** Is it true that this is a cell?

> `[[%atom %turkey] 'or']`

**A:** Yes,

> because the two nouns are now enclosed by square brackets.

**Q:** Is it true that this is a cell?

> `[%atom %turkey 'or']`

**A:** Yes,

> because this is another way to write

> > `[%atom [%turkey 'or']]`

> This is a cell containing an atom and another cell.

**Q:** Is it true that this is a cell?

> `[%two %all %beef %patties]`

**A:** Yes,

> because this is another way to write

> > `[%two [%all [%beef %patties]]]`

> This is a cell containing an atom and another cell.  That cell also 
> contains an atom and another cell.

**Q:** Is it true that this is a cell?

> `[%cheese]`

**A:** No,

> A cell must have two nouns

**Q:** Is it true that this is a noun?

> `'xyz'`

**A:** Yes,

> because a cord is an atom, and all atoms are nouns.

**Q:** Is it true that this is a noun?

> `[%x %y %z]`

**A:** Yes,

> because it is a cell.

**Q:** Is it true that this is a noun?

> `[[%x %y] %z]`

**A:** Yes,

> because all cells are nouns.

### Heads and Tails

**Q:** What is the 'head' of the cell `c` where `c` is

> `[%a %b]`

**A:** `%a`

> because `%a` is the first item in the cell.

**Q:** What is the 'head' of `c` where `c` is 

> `[[%a %b %c] %x %y %z]`

**A:**  `[%a %b %c]`

> because `[%a %b %c]` is the first noun in the cell.

**Q:** What is the 'head' of `a` where `a` is `%hotdog`

**A:** No answer.

> You cannot ask for the 'head' of an atom.

---

###  The Law of Head
##### (preliminary)
#### The primitive 'head' is defined only for cells.

---

**Q:** What is the 'head' of `c`

where

> `c` is `[[%hotdogs %and] [%pickle %relish]]`

**A:** `[%hotdogs %and]`

> because `[%hotdogs %and]` is the first noun of `c`

**Q:** What is `(head c)`

where

> `c` is `[[%hotdogs %and] [%pickle %relish]]`

**A:** `[%hotdogs %and]`

> because `(head c)` is another way to ask for "the 'head' of the cell
> `c`"

**Q:** What is `(head (head c))`

where

> `c` is `[[%hotdogs %and] [%pickle %relish]]`

**A:** `%hotdogs`

**Q:** What is the `tail` of `c`

where

> `c` is `[%a %b]`


**A:** `%b`

> because `%b` is the second item in the cell `c`

**Q:** What is the `tail` of `c`

where

> `c` is `[%a %b %c]`


**A:** `[%b %c]`

> because `[%a %b %c]` is another way to write `[%a [%b %c]]`.

And

> `[%b %c]` is the cell `c` without `(head c)`.

**Q:** What is the `tail` of the cell `c`

where

> `c` is `[[%a %b %c] %x %y %z]`

**A:** `[%x %y %z]`

**Q:** What is `(tail c)`

where

> `c` is `[[%a %b %c] %x %y %z]`

**A:** `[%x %y %z]`

> becuase `(tail c)` is just another way to ask for "the `tail` of the
> cell `c`."

**Q:** What is `(tail a)`

where

> `a` is `%hotdogs`

**A:** No answer.

> You cannot ask for the `tail` of an atom.

**Q:** What is `(head (tail c))`

where

> `c` is `[[%poached %eggs] [%cheese %grits]]`

**A:** `%cheese`

> because `[%cheese %grits]` is `(tail c)` and `%cheese` is the
> 'head' of `(tail c)`

**Q:** What is `(tail (tail c))`

where

> `c` is `[[%poached %eggs] [%cheese %grits]]`

**A:** `%grits`

> because `[%cheese %grits]` is `(tail c)` and `%grits` is the
> `tail` of `(tail c)`.

**Q:** What is `(tail (head c))`

where

> `l` is `[%a [%b %c] %d]`

**A:** No answer,

> since `(head c)` is an atom, and `tail` does not take an atom as an
> argument.

**Q:** What does 'head' take as an argument?

**A:** It takes any cell.

**Q:** What does 'tail' take as an argument?

**A:**  It takes any cell.

### Cons

**Q:** What is the 'cons' of `%a` and `%b`?

**A:** `[%a %b]`

JL: Scheme (and TLS) calls this operation "cons".  It's apparently short
for "construct" but everyone just calls it "cons".  I think this works,
but I suspect another word would work better here.

**Q:** What is the 'cons' of the atom `a` and the cell `c`

where `a` is `%peanut`

and

> `c` is `[%butter %and %jelly]`

> You can read this as: "'cons' the atom `a` onto the cell `c`"

**A:** `[%peanut %butter %and %jelly]`

> Using the 'cons' operstaion to make  a new cell with `a` and `c` results in:

> > `[%peanut [%butter %and %jelly]]`

> This is another way of writing

> > `[%peanut %butter %and %jelly]` 

**Q:** What is the 'cons' of `s` and `c`

where `s` is `[%banana %and]`

and

> `c` is `[%peanut %butter %and %jelly]`

**A:** `[[%banana %and] %peanut %butter %and %jelly]`

> because 'cons' can add any noun to the 'head' of a cell.

**Q:** What is the 'cons' of `c` and `a`

where `c` is `[%ice %cream]`

and

> `a` is `%sundae`

**A:** `[[%ice %cream] %sundae]`

> because 'cons' can add any noun to the 'tail' of a cell.

**Q:** What is `:-(%a %b)`?  

**A:** `[%a %b]`

> because this is another way to ask for the 'cons' of `%a` and `%b`.

**Q:** What is `:-`

**A:** `:-` is a 'rune', a two-character function.

> Specifically it's the rune to do the 'cons' operation.

**Q:** How do you pronounce `:-`

**A:** It's pronounced 'colhep'

**Q:** What does the `:-` rune take as its arguments?

**A:**  `:-` takes two arguments.  The arguments can be any noun.

**Q:** What is `:-(s (head c))`

where `s` is `4'`

and

> `c` is `[2 1 0]`

**A:** `[4 2]`

> Why?

**Q:** What is `:-(s l)`

where

> `s` is `[[%help %this] %is]`

and 

> `l` is `[%very [[%hard %to] %learn]`

**A:** `[[%help %this] %is %very [[%hard %to] %learn]`

### Atoms?  Cells?

**Q:** Is it true or false that `s` is an atom

where `s` is `%harry`

**A:** True,

> because `%harry` is a term.

**Q:** Is `?=(@ s)` true or false

where

> `s` is `%harry`

**A:** True,

> because `?=(@ s)` is another way to ask "Is `s` an atom?"

**Q:** What does `?=` do?

**A:** `?=` (pronounced 'wuttis') asks if a noun is of a particular type.

**Q:** How many arguments does `?=` take and what are they?

**A:** It takes two arguments.  The first argument is a type symbol.
The second argument is the noun you want to test.

**Q:** What is the `@` in 

> `?=(@ s)`

**A:** `@` (pronounced 'pat') is the type symbol for an atom.

**Q:** Is `?=(^ s)` true or false

where

> `s` is `%harry`

**A:** False,

> because `?=(^ s)` is another way to ask "Is `s` a cell?"

**Q:** What is the `^` in 

> `?=(^ s)`

**A:** `^` (pronounced 'ket') is the type symbol for a cell.

**Q:** Is `?=(* s)` true or false

where

> `s` is `%harry`

**A:** True,

> because `?=(* s)` is another way to ask "Is `s` a noun?"

**Q:** What is the `*` in 

> `?=(* s)`

**A:** `*` (pronounced 'tar') is the type symbol for a cell.

**Q:** Is `?=(@ s)` true or false

where

> `s` is `[%harry %had %a %heap %of %apples]`

**A:** False,

> since `s` is not an atom.

**Q:** Is `?=(^ s)` true or false

where

> `s` is `[%harry %had %a %heap %of %apples]`

**A:** True,

> since `s` is a cell.

**Q:** Is `?=(@ (head l))` true or false

where

> `l` is `[%harry %had %a %heap %of %apples]`

**A:** True,

> because `(head l)` is `%harry`, and `%harry` is an atom.

**Q:** Is `?=(@ (tail l))` true or false

where

> `l` is `[%harry %had %a %heap %of %apples]`

**A:** False

**Q:** Is `?=(^ (head (tail l)))` true or false

where

> `l` is `[%swing %low %sweet %cherry %oat]`

**A:** False

> because `(tail l)` is `[%low %sweet %cherry %oat]`
> and `(head (tail l))` is `%low`, which is not a cell.

**Q:** Is `?=(^ (head (tail l)))` true or false

where

> `l` is `[%swing [%low %sweet] %cherry %oat]`

**A:** True,

> since `(tail l)` is `[[%low %sweet] %cherry %oat]`
> and `(head (tail l))` is `[%low %sweet]`, which is a cell.

### Equality

**Q:** True or false, `a1` and `a2` are the same atom

where `a1` is `%harry`

and

> `a2` is `%harry`

**A:** True,

> because `a1` is the atom `%harry` and `a2` is the atom `%harry`.

**Q:** Is `.=(a1 a2)` true or false

where `a1` is `%harry`

and

> `a2` is `%harry`

**A:** True,

> because `.=(a1 a2)` is just another way to ask, "Are a1 and a2 the
> same value?"

**Q:** How do you pronounce `.=`

**A:** `.=` is pronounced 'dottis'

**Q:** Is `.=(a1 a2)` true or false

where `a1` is `%margarine`

and

> `a2` is `%butter`

**A:** False,

> since `a1` and `a2` are different atoms.

**Q:** Is `.=(c1 c2)` true or false

where `c1` is `[%olive %loaf]`

and

> `c2` is `[%olive %loaf]`

**A:** True,

> since `c1` and `c2` are the same cells, and 'dottis' works with atoms
> and cells.

**Q:** Is `.=(c1 c2)` true or false

where `c1` is `[%olive %loaf]`

and

> `c2` is `[%pickle %loaf]`

**A:** False,

> since `c1` and `c2` are different cells.

**Q:** How many argumenst does `.=` take and what are they?

**A:** It takes two arguments.  The arguments can be any noun.

**Q:** Is `.=((head c) a)` true or false

where

> `c` is `[%mary %had %a %little %lamb %chop]`

and

> `a` is `%mary`

**A:** True,

> because `(head c)` is the atom `%mary`, and the argument `a` is also
> the atom `%mary`.

**Q:** Is `.=((tail c) a)` true or false

where

> `c` is `[%soured %milk]`

and

> `a` is `%milk`.

**A:** True

**Q:** Is `.=((head l) (head (tail l)))` true or false

where

> `l` is `[%beans %beans %we %need %jelly %beans]`

**A:** True

> because it compares the first and second atoms in the list

### Lists

**Q:** Is it true that this is a list?

> `[%atom ~]`

**A:** Yes,

> because `[%atom ~]` is a cell ending in the null value, `~`,
> which is pronounced ('sig' or 'null').

**Q:** Is it true that this is a list?

> `[%atom %bacon ~]`

**A:** Yes,

> because `[%atom %bacon ~]` is a cell where the last item is `~`,
> i.e. the null value or 'sig'.

**Q:** Is it true that this is a list?

> `[%atom [%bacon ~]]`

**A:** Yes,

because `[%atom [%bacon ~]]` is another way of writing

> `[%atom %bacon ~]`

**Q:** Is it true that this is a list?

> `[%tomato %soup]`

**A:** No,

> because the last value of `[%tomato %soup]` is not the null value, `~`.

**Q:** Is it true that this is a list?

> `~[%atom %bacon %or]`

**A:** Yes

> Because `~[%atom %turkey %or]` is another way to write

> > `[%atom %turkey %or ~]`

JL: Do we want to introduce the shorthand for making lists now, or save it for later, or skip it in this doc?

**Q:** Is it true that this is a list?

> `[[%x %y ~] %z ~]`

**A:** Yes,

> This is a list whose first item is another list.

This is called a "list of lists".

**Q:** Is it true that this is a noun?

> `[%x %y %z ~]`

**A:** Yes,

> because non-empty lists are cells, and all cells are nouns

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

**Q:**  Is it true that this is a list

> `[%how %are %you %doing %so %far ~]`

**A:** Yes,

> because it is a collection of nouns enclosed by a 'sel' and a 'ser',
> ending in null.

**Q:**  How many item are in the list?

> `[%how %are %you %doing %so %far ~]`

and what are they?

**A:** Six,

> `%how`, `%are`, `%you`, `%doing`, `%so`, and `%far`.

**Q:** Is it true that this is a list?

> `[[[%how ~] %are ~] [[%you ~] [%doing %so ~] ~] %far ~]`

**A:** Yes,

> because it is a collection of nouns enclosed by a 'sel' and a 'ser',
> ending in null.

**Q:** How many items are in the list

> `[[[%how ~] %are ~] [[%you ~] [%doing %so ~] ~] %far ~]`

and what are they?

**A:** Three,

> `[[%how ~] %are ~]`, `[[%you ~] [%doing %so ~] ~]`, and `'far'`.

**Q:** What is a list with one item?

**A:** Here's one:

> `[%item ~]`

**Q:** What is a cell with one item?

**A:** No answer.

> A cell must have two nouns.

**Q:** What is a list with zero items?

**A:** `~`

**Q:** What is a cell with zero items?

**A:** No answer.

**Q:** What is the advantages of using lists versus using cells?

**A:** A cell can only have two or more items. A list can have zero
items, one item, as many or as few items as you want, as long as you
terminate it with a `~`.

> `[%this %list %has %a %lot %of %items %and %ends %with %a ~]`

**Q:** What is the 'head' of the list `l` where `l` is

> `[%a ~]`

**A:** `%a`

> because non-empty lists are cells, and `%a` is the first item in the cell.

**Q:** What is the 'head' of `l` where `l` is 

> `[[%a %b %c ~] %x %y %z ~]`

**A:**  `[%a %b %c ~]`

> because `[%a %b %c ~]` is the first noun in the cell.

**Q:** What is the 'head' of `l` where `l` is `~`

**A:** No answer.
 
> You cannot ask for the 'head' of `~`, since `~` is not a cell.

---

###  The Law of Head
##### (final version)
#### The primitive 'head' is defined only for cells and non-empty lists.

---

**Q:** What is the 'head' of `l` 

where

> `l` is `[[['hotdogs' ~] ~] ['and' ~] ['pickle' ~] 'relish' ~]`

**A:** `[['hotdogs' ~] ~]`

read as:

> "The list of the list of 'hotdogs'."

because `[['hotdogs' ~] ~]` is the first noun of `l`

**Q:** What is the `tail` of `l`

where

> `l` is `[[%x ~] %t %r ~]`

**A:** `[%t %r ~]`

because `[[%x ~] %t %r ~]` is another way to write

> `[[%x ~] [%t %r ~]]`

The head of this cell is `[%x ~]`.  And

> `[%t %r ~]` is the second part of this cell.

**Q:** What is the `tail` of `l`

where 

> `l` is `['hamburger' ~]`

**A:** `~`

> the null value, in this case acting as an empty list.

**Q:** What is `(tail l)`

where `l` is `~`

**A:** No answer.

> You cannot ask for the 'tail' of `~`, since `~` is not a cell.

---

###  The Law of Tail
##### (final version)
#### The primitive 'tail' is defined only for cells and non-empty lists.

---

**Q:** What is the 'cons' of the atom `a` and the list `l`

where `a` is `%peanut`

and

> `l` is `[%butter %and %jelly ~]`

**A:** `[%peanut %butter %and %jelly ~]`

> because the 'cons' operation can add an atom to the front of the list
> and make a new list.

**Q:** Is this a list?

**A:** Yes, because it ends in `~`

**Q:** What is the 'cons' of the atom `a` and `~`

where `a` is `%pumpkin`

**A:** `[%pumpkin ~]`

**Q:** Is this a list?

**A:** Yes, because it ends in `~`

**Q:** What is the 'cons' of the list `l` and `c`

where 

> `l` is `[%hot %ham ~]`

and 

> `c` is `[%and cheese]`

**A:** `[[%hot %ham ~] [%and %cheese]]`

**Q:** Is this a list?

**A:** No, because it does not end in `~`

**Q:** What is the 'cons' of `c` and `a`

where 

> `c` is `[%cinnamon %and]`

and 

> `a` is `%sugar`

**A:** `[%cinnamon %and %sugar]`

**Q:** Is this a list?

**A:** No

**Q:** How can we make a list using the `:-` rune?

**A:**  To make a list using `:-` you need two arguments:

> the first one is any noun;  
> the second one is either a list or null.

---

### The Law of Colhep
#### The rune `:-` takes two arguments.  To create a new list with `:-`, the second argument must be a list or null.

---

**Q:** What is `:-(s (head l))`

where `s` is `%a`

and

> `l` is `[[%b ~] %c %d ~]`

**A:** `[%a %b ~]`

> Why?

**Q:** What is `:-(s (tail l))`

where `s` is `%a`

and

> `l` is `[[%b ~] %c %d ~]`

**A:** `[%a %c %d ~]`

> Why?

**Q:** Is `?=(@ l)` true or false

where

> `l` is `[%harry ~]`

**A:** `%.n`,

> because non-empty lists are not atoms.

**Q:** Is `?=(^ l)` true or false

where

> `l` is `[%harry ~]`

**A:** `%.y`,

> because non-empty lists are cells.

**Q:** Is `?=(@ (tail l))` true or false

where

> `l` is `[%harry ~]`

**A:** True,

> because the null value, `~`, acts as an atom here.

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
  they're very new.  I could also make a core and add `is-lat` as an arm
  for it.  I'll stick with generators for now.

**Q:** True or false: `+is-lat l`

where

> `l` is `[%jack %sprat %could %eat %no %chicken %fat ~]`

**A:** True,

> because each noun in `l` is an atom.

**Q:** True or false: `+is-lat l`

where

> `l` is `[[%jack ~] %sprat %could %eat %no %chicken %fat ~]`

**A:** False,

> since `(head l)` is a list.

**Q:** True or false: `+is-lat l`

where

> `l` is `[%jack [%sprat %could ~] %eat %no %chicken %fat ~]`

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

> `head`, `tail`, `:-`, `?=`, `.=`

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
^-  ?
|-
?:  .=(~ l)
  %.y
?:  ?=(^ (head l))
  %.n
%=($ l (tail l))
```

What is the value of `+is-lat l`

where

> `l` is the argument `[%bacon %and %eggs ~]`

<sup>1</sup> Try adding the code to a file called `gen/is-lat.hoon`.  Then try 
running it in the dojo.
```
> |commit %home
> =l [%bacon %and %eggs ~]
> +is-lat l
```

JL: The original version in the book was not tall-recursive.  This
one is.  I don't know if I'll explain what "tail-recursion" is here, but
I thought I'd write it this way to demonstrate good style.

**A:** `%.y`

> The application of `+is-lat l`  
> where  

> > `l` is `[%bacon %and %eggs ~]`

> has the value `%.y`&mdash;true&mdash;because `l` is a lat.

**Q:** How do we determine the answer `%.y` for the function 

> `+is-lat l`

**A:** You were not expected to know this one either.  The answer is
determined by answering the questions asked by `+is-lat`

> Hint: Write down the contents of `is-lat.hoon` and refer to
> it for the next group of questions.

**Q:** What does the first line of `+is-lat` do?

> `|=  l=(list)`

**A:** The rune `|=` (pronounced 'bartis') declares a gate, which is a
kind of function that takes a 'sample', i.e. the arguments for the
function.

JL: An earlier version skipped the term "gate" and just called this a
"function".  I'm leaving the "gate" terminology to distinguish from the 
"trap" below.

**Q:** What is after the `|=`

**A:** Two spaces.  The parts of functions in Hoon need to be separated
by two or more spaces, and/or a newline.  This is called a 'gap'.

**Q:**  What is after the 'gap'?

**A:** `l=(list)`

**Q:** What is the meaning of

> `l=(list)`

**A:** The gate we are creating has one argument, named `l`.  Its type is 
`list`.

**Q:** What does the next line do?

> `^-  ?`

**A:** The `^-` rune (pronounced 'kethep') is used to change something from 
one type to another type.

In this case, we aren't changing types.  We are saying what type the result of
this function should be.

**Q:** What type should the result of this function be?

**A:**  `?`

**Q:** What is type is `?`

**A:** `?` is 'loobean'.  It is an answer to a question.

It has two possible value:

> `%.y`, which means "true"  
> `%.n`, which means "false"

**Q:** What does the next line do?

> `|-`

**A:** `|-` creates a 'trap', which is another type of function.  Unlike a
gate, a trap does not have a sample.  The function you are creating is
called `$` (which is pronounced 'buc').  When you use the `|-` rune, the 
function `$` starts running automatically.

**Q:** What is the next line?

**A:** `?:  .=(~ l)`

**Q:** What does `?:` do?

**A:** `?:` (pronounced 'wutcol') asks a question.  If the answer is
"true", it performs the next action.  If the answer is "false", it skips
the next action, and keep going.

**Q:** What the question that `?:` is asking?

**A:** `.=(~ l)`

**Q:** What is the meaning of

> `.=(~ l)`

where

> `l` is `[%bacon %and %eggs ~]`

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

**A:** `?:  ?=(^ (head l))`

**Q:** What is the question for `?:`

**A:** `?=(^ (head l))`

**Q:** What is the meaning of

> `?=(^ (head l))`

where

> `l` is `[%bacon %and %eggs ~]`

**A:** `?=(^ (head l))` asks if the first noun of the list `l` is a cell.
In this case, `(head l)` is an atom, so the answer if false.

**Q:** So what do we do?

**A:** Per the rules of `?:`, if the answer to the question is true, we 
evaluate the next action, which is 

> `%.n`

on the next line, and the value of the function is false.  If the answer 
to the question is false, we keep going.

Since the answer is false, and `(head l)` is an atom, we want to 
find out if the rest of the list is composed only of atoms.  

So we skip the `%.n` and keep going.

**Q:** What comes next?

**A:** 
%=($ l (tail l))

**Q:** What is the meaning of `%=`

**A:** The rune `%=` (pronounced 'centis') means to call a function with
some data changed.

**Q:** What arguments does `%=` take?

**A:** `%=` takes the name of the function to call and a list of data
changes.

**Q:** What function are we calling?

**A:** `$`

**Q:** What is `$`

**A:** This is the name of the function for the 'trap' we declared with `|-`.  
After we change our data, we will return to that point and restart.

**Q:** What is the meaning of

> `l (tail l)`

**A:** For the next time we call `$`, replace the value of `l` with `(tail l)`,
which is

> `[%and %eggs ~]`

**Q:** Why do we use `(tail l)`?

**A:** We know that `(head l)` is an atom.  We want to find out if the
rest of the list `l` is composed only of atoms.  

**Q:** What is after our restart point, `|-`

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `[%and %eggs ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.  
Per the rules of `?:`, if the answer to the question is true, we 
evaluate the next action, which is 

> `%.y`

on the next line, and the value of the function is true.  If the answer 
to the question is false, we keep going.

In this case, l is not the null value, so we skip the `%.y` and keep going.

**Q:** What is next?

**A:** `?:  ?=(^ (head l))`

**Q:** What is the meaning of

> `?:  ?=(^ (head l))`

where

> `l` is now `[%and %eggs ~]`

**A:** `?=(^ (head l))` asks if `(head l)` is a cell.
Per the rules of `?:`, if the answer to the question is true, we 
evaluate the next action, which is 

> `%.n`

on the next line, and the value of the function is false.  If the answer 
to the question is false, we keep going.

In this case, `(head l)` is an atom and not a cell, so we skip the `%.n` 
and keep going.

**Q:** What is the meaning of 

> `%=($ l (tail l))`

**A:** Find out if the rest of the list `l` is composed only of atoms, by 
calling `$` and returning to our restart point `|-` with new values.
The new value for `l` is `(tail l)` or `[%eggs ~]`.

**Q:** What is the after `|-`

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `[%eggs ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.  
Per the rules of `?:`, since l is not the null value, so we skip the `%.y` 
and keep going.

**Q:** What is next?

**A:** `?:  ?=(^ (head l))`

**Q:** What is the meaning of

> `?:  ?=(^ (head l))`

where

> `l` is now `[%eggs ~]`

**A:** `?=(^ (head l))` asks if `(head l)` is a cell.  Per the rules of `?:`, 
since `(head l)` is not a cell, we skip the `%.n` and keep going.

**Q:** What is the meaning of 

> `%=($ l (tail l))`

**A:** Find out if the rest of the list `l` is composed only of atoms, by 
calling `$` and returning to our restart point `|-` with new values.
The new value for `l` is `(tail l)`.

**Q:** Now, what is the new value for `l`?

**A:** `~`

**Q:** What is the line after `|-`?

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `~`

**A:** `.=(~ l)` asks if the argument `l` is null.  
Per the rules of `?:`, since l is the null value, we evaluate the next action, 
which is the value `%.y`, or true.  Therefore, the value of

> `+is-lat l`  

where

> `l` is `[%bacon %and %eggs ~]` is `%.y`&mdash;true

**Q:** Do you remember the question about

> `+is-lat l`

**A:** Probably not.  `+is-lat l` has the value of `%.y`
if the list `l` is a list of atoms where 

> `l` is `[%bacon %and %eggs ~]`

**Q:** Can you describe what the generator `+is-lat` does in your own
words?

**A:** Here are our words:

> "`+is-lat` looks at each noun in a list, in turn, and asks if each
> noun is not a cell (i.e. if it is an atom), until it gets to the 
> null value.  If it runs out without encountering a cell, the value 
> is `%.y`.  If it finds a cell, the value is `%.n`&mdash;false."

To see how we could arrive at a value of "false", consider the next few
questions.

**Q:** Here are the contents of `+is-lat` again.
```
|=  l=(list)
^-  ?
|-
?:  .=(~ l)
  %.y
?:  ?=(^ (head l))
  %.n
%=($ l (tail l))
```

What is the value of `+is-lat l`

where

> `l` is now `[%bacon [%and %eggs ~] ~]`

**A:** `%.n`

> since the list `l` contains a noun that is not an atom, i.e a list.

**Q:** What is the first line after the restart point, `|-`

**A:** `?:  .=(~ l)`

**Q:** What is the meaning of

> `?:  .=(~ l)`

where

> `l` is `[%bacon [%and %eggs ~] ~]`

**A:** `.=(~ l)` asks if the argument `l` is equal to null.  
Per the rules of `?:`, since `l` is not the null value, we skip the
`%.y` and keep going.

**Q:** What is next?

**A:** `?:  ?=(^ (head l))`

**Q:** What is the meaning of

> `?:  ?=(^ (head l))`

where

> `l` is `[%bacon [%and %eggs ~] ~]`

**A:** `?=(^ (head l))` asks if the first noun of the list `l` is a cell.
Per the rules of `?:`, since `(head l)` is an atom and not a cell, we skip 
the `%.n` and keep going.

**Q:** What is the meaning of 

> %=($ l (tail l))

**A:** Find out if the rest of the list `l` is composed only of atoms, by 
calling `$` and returning to our restart point `|-` with new values.
The new value for `l` is `(tail l)`.

**Q:** What is the meaning of 

> `?:  .=(~ l)`

where

> `l` is now `[[%and %eggs ~] ~]`

**A:** `.=(~ l)` asks if the argument `l` is null.
Per the rules of `?:`, since `l` is not null, we skip the `%.y` and keep
going.

**Q:** What is next?

**A:** `?:  ?=(^ (head l))`

**Q:** What is the meaning of

> `?:  ?=(^ (head l))`

where

> `l` is now `[[%and %eggs ~] ~]`

**A:** `?=(^ (head l))` asks if the first noun of the list `l` is a list.
Per the rules of `?:`, since `(head l)` is a list, we evaluate the next
action, which is `%.n`.  

So the answer is `%.n`&mdash;false.

**Q:** Can you describe how we determined the value `%.n` for

>  `+is-lat l`

where

> `l` is `[%bacon [%and %eggs ~] ~]`

**A:** Here is one way to say it:

> "`+is-lat l` looks at each item in its argument to see if it is a
> cell.  If it runs out of items before it finds a cell, then all the
> items are atoms, so the value of `+is-lat l` is `%.y`.  If it 
> finds a cell, as it did in the example `[%bacon [%and %eggs ~] ~]`, 
> the value of `+is-lat l` is `%.n`."

**Q:** What does the first line of `+is-lat` again?

**A:** `|=  l=(list)`

**Q:** What is the meaning of

> `l=(list)`

**A:** The function has one argument,  named `l`.  It is of type `list`.

**Q:** What would be the result if you called `+is-lat l`  
where

> `l` is `%cheeseburger`

**A:** No answer,

Because the argument `l` must be a list.

**Q:** What would be the result if you called `+is-lat l`  
where

> `l` is `[%cheese %burger]`

**A:** No answer,

Because the argument `[%cheese %burger]` does not end in `~`
and is not a list.

**Q:** What would be the result if you called `+is-lat l`  
where

> `l` is `~`

**A:** It would return `%.y`

Because `~` is the only atom that is also a list, in this case it is a list of zero nouns. 
And since an empty list does not contain any cells, it returns true, or `%.y`.

Hint: Look at the code one last time and make sure this is true.

JL: This is a deep concept which kind of muddies the waters here.  I'm seeing 
now why TLS made a scheme function called `atom?`, which explicitly ruled 
out `~`.  I'm okay with how we describe things here, but I'm open to
hearing about why this is wrong.

**Q:** Is `?|(.=(~ l1) .=(~ l2))` true or false
where `l1` is `~`   
and  

> `l2` is `[%d %e %f %g ~]`

**A:** True,

> because `.=(~ l1)`is true where `l1` is `~`.

**Q:** Is `?|(.=(~ l1) .=(~ l2))` true or false
where 

> `l1` is `[%a %b %c ~]`   

and  

> `l2` is `~`

**A:** True,

> because `.=(~ l2)`is true where `l2` is `~`.

**Q:** Is `?|(.=(~ l1) .=(~ l2))` true or false
where 

> `l1` is `[%a %b %c ~]`   

and  

> `l2` is `[%atom ~]`

**A:** False,

> because neither `.=(~ l1)` nor `.=(~ l2)` are true where

> `l1` is `[%a %b %c ~]`

and 

> `l2` is `[%atom ~]`

**Q:** Is this true or false:

```
?|
  .=(~ l1)
  .=(~ l2)
==
```

where 

> `l1` is `[%a %b %c ~]`   

and  

> `l2` is `[%atom ~]`


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
where `a` is `%tea`  
and

> `lat` is `[%coffee %tea %or %milk ~]`

**A:**  True,

> because one of the atoms of the lat,

> > `[%coffee %tea %or %milk ~]`

> is the same as the atom `a`&mdash;tea.

**Q:** Is `+is-member [a lat]` true or false  
where `a` is `%poached`  
and

> `lat` is `[%fried %eggs %and %scrambled eggs% ~]`

**A:** False,

> since `a` is not one of the atoms of the lat.

**Q:**   

Here are the contents of the generator `is-member.hoon`<sup>1</sup>
```
|=  [a=@ lat=(list @)]
^-  ?
|-
?:  .=(~ lat)
  %.n
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

What is the value of `+is-member [a lat]`

where `a` is `%meat`   
and

> `lat` is `[%mashed %potatoes %and %meat %gravy ~]`

<sup>1</sup> Add this code to a file called `gen/is-member.hoon` inside your
`home` directory.  Don't forget to run `|commit %home` in the dojo!

**A:** `%.y`,

> because the atom `%meat` is one of the atoms of `lat`,
> > `[%mashed %potatoes %and %meat %gravy ~]`

**Q:** How do we determine the value `%.y` for the calling
`+is-member [a lat]`?

**A:** The value is determined by asking the questions in `+is-member
[a lat]`.

> Hint: Write down the contents of `is-member.hoon` and refer to
> it while you work on the next group of questions.

**Q:** What does the first line of `+is-member` do?

> `|=  [a=@ lat=(list @)]`

**A:** The `|=` declares a gate, which is a function with arguments,
also known as the sample.  This particular gate has a pair of
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

**A:** The second argument is named `lat`. `(list @)` means its type is a lat or list of atoms.

**Q:** What does the next line do?

> `^-  ?`

**A:** The `^-` rune says that the result of this gate will be `?`, which is the 
type symbol for a loobean, i.e. an answer to a question.

**Q:** What does the third line do?

> `|-`

**A:** It creates a trap, a function `$` with no arguments, and calls it
immediately.  It acts as the restart point when we call the function
`$`.

**Q:** What is after `|-`

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

> `lat` is `[%mashed %potatoes %and %meat %gravy ~]`

**A:** `.=(~ lat)` asks if lat is the null value, `~`.  
Per the rules of `?:`, since `lat` is not null, we skip the `%.n` and keep going.

**Q:** What is the meaning of:
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** Now that we know that `lat` is not `~`, we have to find out
whether the `head` of `lat` is the same atom as `a`, or whether `a` is
somewhere in the rest of `lat`.  The answer to
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

does this.

**Q:** True or false
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

where `a` is `%meat`  
and

> `lat` is `[%mashed %potatoes %and %meat %gravy ~]`

**A:** We will find out by looking at each question in turn.

**Q:** Is `.=((head lat) a)` true or false  
where `a` is `%meat`  
and

> `lat` is `[%mashed %potatoes %and %meat %gravy ~]`

**A:** False,

because `%meat` is not equal to `%mashed`, which is the `head` of

> `[%mashed %potatoes %and %meat %gravy ~]`

**Q:** What is the second question of `?|`

**A:** `%=($ lat (tail lat))`

> This means we should call the function `$` with `lat` replaced by
> `(tail lat)`.

**Q:** Where do we go from here?

**A:** Back to `|-`

**Q:** Now what are the two arguments?

**A:** `a` is `%meat`  

and

> `lat` is now `(tail lat)`, specifically  
> `[%potatoes %and %meat %gravy ~]`

**Q:** What comes next?

**A:** `?:  .=(~ lat)`

> Remember The First Commandment

**Q:** is `.=(~ lat)` true or false  
where

> `lat` is `[%potatoes %and %meat %gravy ~]`

**A:** `%.n`&mdash;false

**Q:** What do we do now?

**A:** Skip the `%.n` and keep going

**Q:** What's the meaning of 
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```
> 

**A:** 
This finds out if `a` is equal to the `head` of `lat` or if `a` is a member
of the `tail` of `lat` by calling `$` and returning to the restart point.

**Q:** Is `a` equal to the `head` of `lat`?

**A:** No, because `a` is `%meat` and the `head` of `lat` is
`%potatoes`.

**Q:** What do we do next?

**A:** We call `$` and return to our restart point.

**Q:** Now, what are our arguments?

**A:** `a` is `%meat`, and  
`lat` is `[%and %meat %gravy ~]`

**Q:**  What comes next?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Skip over the `%.n` and keep going, since `.=(~ lat)` is false.

**Q:** What is the value of

```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** The value of `%=($ lat (tail lat))`.

**Q:** Why?

**A:** Because `.=((head lat) a)` is false.

**Q:** What do we do now?

**A:** Recur&mdash;call `$` with new arguments.

**Q:** What are the new arguments?

**A:** `a` is `%meat`, and  
`lat` is `[%meat %gravy ~]`

**Q:** What comes next?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Since `.=(~ lat)` is false, we skip over the `%.n` and keep going

**Q:** What is the value of
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** `%.y`,
> because `(head lat)`, which is `%meat`, and `a`, which is `%meat`,
> are the same atom.  
> Therefore, `?|` answers with `%.t`.

**Q:** What is the value of `$`

when `a` is `%meat`  
and   

> `lat` is `[%meat %gravy ~]`

**A:** `%.t`,

> because we have found that `%meat` is a member of `[%meat %gravy ~]`.

**Q:** What is the value of `$`

where `a` is `%meat`  
and   

> `lat` is `[%and %meat %gravy ~]`

**A:** `%.t`,

> because `%meat` is also a member of the `lat` `[%and %meat %gravy ~]`

**Q:** What is the value of `$`  
where `a` is `%meat`  
and   

> `lat` is `[%potatoes %and %meat %gravy ~]`

**A:** `%.t`,

> because `%meat` is also a member of the `lat` `[%potatoes %and %meat %gravy ~]`

**Q:** What is the value of `$`

where `a` is `%meat`  
and   

> `lat` is `[%mashed %potatoes %and %meat %gravy ~]`

**A:** `%.t`,

> because `%meat` is also a member of the `lat` is `[%mashed %potatoes %and %meat %gravy ~]`  
> Of course, this is our original `lat`.

**Q:** Just to make sure you have it right, let's quickly run through it
again.  What is the value of `+is-member [a lat]`  
where

> `a` is `%meat`

and

> `lat` is `[%mashed %potatoes %and %meat %gravy ~]`

**A:** `%.y`.

> Hint: Write down the contents of `is-member.hoon` and its arguments
> and refer to them as you go through the next group of questions.

**Q:** `?:  .=(~ lat)`

**A:**  No.  Skip the `%.n` and keep going.

**Q:** 
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** Perhaps

**Q:** `.=((head lat) a)`

**A:** No.  Ask the next question.

**Q:** What next?

**A:** Recur using `(tail lat)` for `lat`

So `a` is `%meat`  

and

> `lat` is `[%potatoes %and %meat %gravy ~]`

**Q:** `?:  .=(~ lat)`

**A:** No.  Skip the `%.n` and keep going.

**Q:** 
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

Recur using `(tail lat)` for `lat` 

> So now `lat` is `[%and %meat %gravy ~]`

**Q:**  `?:  .=(~ lat)`

**A:**  No.  Skip the `%.n` and keep going.

**Q:**
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

Recur using `(tail lat)` for `lat` 

> So now `lat` is `[%meat %gravy ~]`

**Q:**  `?:  .=(~ lat)`

**A:**  No.  Skip the `%.n` and keep going.

**Q:** `.=((head lat) a)`

**A:** Yes, the value is `%.y`.

**Q:**
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** `%.y`

**Q:** What is the value of `$`  
when `a` is `%meat`  
and  

> `lat` is `[%meat %gravy ~]`

**A:** `%.y`

**Q:** What is the value of `$`  
when `a` is `%meat`  
and  

> `lat` is `[%and %meat %gravy ~]`

**A:** `%.y`

**Q:** What is the value of `$`  
when `a` is `%meat`  
and  

> `lat` is `[%potatoes %and %meat %gravy ~]`

**A:** `%.y`

**Q:** What is the value of `$`  
when `a` is `%meat`  
and  

> `lat` is `[%mashed %potatoes %and %meat %gravy ~]`

**A:** `%.y`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `%liver`  
and  

> `lat` is `[%bagels %and %lox ~]`

**A:** `%.n`

**Q:** Let's work out why it is `%.n`.  What's the first question
`+is-member` asks?

**A:** `?:  .=(~ lat)`

**Q:** `?:  .=(~ lat)`

**A:** No.  Skip the `%.n` and keep going

**Q:** 
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Recur with `(tail lat)`

> > So now `lat` is `[%and %lox ~]`

**Q:** `?: .=(~ lat)`

**A:** No.  Skip the `%.n` and keep going

**Q:** 
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Recur with `(tail lat)`

> > So now `lat` is `[%lox ~]`

**Q:** `?:  .=(~ lat)`

**A:** No.  Skip the `%.n` and keep going

**Q:** 
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```

**A:** `.=((head lat) a)` is false.

> Recur with `(tail lat)`

> > So now `lat` is `~`

**Q:** `?:  .=(~ lat)`

**A:** Yes.

**Q:** What do we do now?

**A:** Since `.=(~ lat)` is true, we return the next value, which is
`%.n`.

**Q:** What is the value of `$`
where `a` is `%liver`  
and  

> `lat` is `~`

**A:** `%.n`

**Q:** What is the value of
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```
where  

> `a` is `%liver`

and

> `lat` is `[%lox ~]`

**A:** `%.n`

**Q:** What is the value of
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```
where  

> `a` is `%liver`

and

> `lat` is `[%and %lox ~]`


**A:** `%.n`

**Q:** What is the value of
```
?|
  .=((head lat) a)
  %=($ lat (tail lat))
==
```
where  

> `a` is `%liver`

and

> `lat` is `[%bagels %and %lox ~]`


**A:** `%.n`

**Q:** What is the value of `+is-member [a lat]`  
where `a` is `%liver`  
and  

> `lat` is `[%bagels %and %lox ~]`

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

## 3. Colhep the Magnificent

JL: TLS calls this "Cons the Magnificent".  I decided to use "colhep",
since this is the rune we're focused on, but since "cons" is the 
name of the  operation we're doing here, I could be convinced that this
should be "Cons the Magnificent". 

**Q:** What is `+rember [a lat]`  
where `a` is `%mint`  
and  

>`lat` is `[%lamb %chops %and %mint %jelly ~]`

**A:** `[%lamb %chops %and %jelly ~]`

> `rember` stands for `rem`ove  mem`ber`.

**Q:** `+rember [a lat]`  
where `a` is `%mint`  
and

> `lat` is `[%lamb %chops %and %mint %flavored %mint %jelly ~]`

**A:** `[%lamb %chops %and %flavored %mint %jelly ~]`

**Q:** `+rember [a lat]`  
where `a` is `%cup`  
and  

> `lat` is `[%coffee %cup %tea %cup %and %hick %cup ~]`

**A:** `[%coffee %tea %cup %and %hick %cup ~]`

**Q:** What does `+rember [a lat]` do?

**A:** It takes an atom and a lat as its arguments, and makes a new lat
with the first occurrence of the atom in the old lat removed.

**Q:** What is the the type symbol for an atom?

**A:** `@`

**Q:** What is `@t`

**A:** This is the type symbol for an atom that is text.

**Q:** Is a `'cord'` an atom that is text?

**A:** Yes

**Q:** Is a `%term` an atom that is text?

**A:** Yes

**Q:** What is 

> `[%a %list %of %terms ~]`

**A:** This is a list of text 

**Q:** What is 

> `<|a list of terms|>`

**A:** This is how 

> `[%a %list %of %terms ~]`

is printed out if its type is set to (list @t)

**Q:** What steps should we use to write `rember` if we are using lists
of text?

**A:** First we need to declare a function with two arguments, `a`,
which is a text atom, and `lat`, which is a list of text atoms.

**Q:** How would we do that?

**A:** `|=  [a=@t lat=(list @t)]`

**Q:** Then what?

**A:** We use `^-` to specify that the type of value we will return is a
list of text atoms.

**Q:** How would we do that?

**A:** `^-  (list @t)`

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

**Q:** What would be the value of `+rember [a lat]` if a were the same
as `(head lat)`?

**A:** `(tail lat)`

**Q:** What do we do if the `a` is not the same as `(head lat)`

**A:** We want to keep `(head lat)`, but also find out if `a` is
somewhere in the rest of the lat.

**Q:** How could we remove the first occurrance of `a` in the rest of `lat`

**A:** Ask the questions again, using `(tail lat)` for `lat`

**Q:** Is there any other question we should ask?

**A:** No.

**Q:** Since we are going to ask the questions again with different
values, what do we need to add?

**A:** We need `|-`, our restart point, right before we start asking
questions.

**Q:** Now, let's write down what we have so far<sup>1</sup>:

```
|=  [a=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) a)
  (tail lat)
%=($ lat (tail lat))
```

What is the value of `+rember [a lat]` where  

> `a` is `%bacon`

and

> `lat` is `[%bacon %lettuce %and %tomato ~]`

<sup>1</sup> You can put this code in a file `gen/rember.hoon` in your home
directory to try it out (don't forget to `|commit %home`).  

**A:** `[%lettuce %and %tomato ~]`

or

> `<|lettuce and tomato|>`

> Hint: write down the code for `+rember` and its arguments, and refer
> to them as you go through the next sequence of questions.

**Q:** Now, let's see if this function works.  What is the first
question?

**A:** `?:  .=(~ lat)`

**Q:**  What do we do now?

**A:** Skip the `~` and ask the next question.

**Q:** What is the next question?

**A:** `?:  .=((head lat) a)`

**Q:** So what do we do?

**A:** `.=((head lat) a)` is true, so the value is `(tail lat)`  In this
case, it is the list

> `[%lettuce %and %tomato ~]`

**Q:**  Is this the correct value?

**A:** Yes, because it is the original list without the atom `%bacon`

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
where `a` is `%and`  
and  

> `lat` is `[%bacon %lettuce %and %tomato ~]`

**A:** `[%bacon %lettuce %tomato ~]`

or

> `<|bacon lettuce tomato|>`

**Q:** Let us see if our function `rember` works.  
What is the first question asked by `rember`?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Skip the `~`, and ask the next question.

**Q:** `?:  .=((head lat) a)`

**A:** No, so skip over `(tail lat)` and keep going.

**Q:** What is the meaning of  
`%=($ lat (tail lat))`

**A:** Call `$` and return to the `|-` with `lat` set to 
`(tail lat)` or `[%lettuce %and %tomato ~]`

**Q:** `?:  .=(~ lat)`

**A:** No, skip over `~` and continue

**Q:** `?:  .=((head lat) a)`

**A:** No, skip over `(tail lat)` and keep going.

**Q:** What is the meaning of  
`%=($ lat (tail lat))`

**A:** Recur  
setting `lat` to `(tail lat)` or `[%and %tomato ~]`

**Q:** `?:  .=(~ lat)`

**A:** No, skip over `~` and continue

**Q:** `?:  .=((head lat) a)`

**A:** Yes.  The result is the value of the next line.

**Q:** So what is the result?

**A:** `(tail lat)`&mdash;`[%tomato ~]`

**Q:** Is this correct?

**A:** No, since `[%tomato ~]` is not the list  

> `[%bacon %lettuce %and %tomato ~]`

with just `a`&mdash;`%and`&mdash;removed.

**Q:** What did we do wrong?

**A:** We dropped `%and`, but we also lost all the atoms preceding
`%and`.

**Q:** How can we keep from losing the atoms

> `%bacon` `%and` `%lettuce`

**A:** We use Colhep the Magnificent.  Remember `:-` from chapter 1?

---

###  The Second Commandment
#### Use `:-` to build lists.

---

**Q:** Let's see what happens when we use `:-`
```
|=  [a=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) a)
  (tail lat)
:-  (head lat)
%=($ lat (tail lat))
```

What is the value of `+rember [a lat]`  
where `a` is `%and`  
and  

> `lat` is `[%bacon %lettuce %and %tomato ~]`

**A:** `[%bacon %lettuce %tomato ~]`

or 

> `<|bacon lettuce tomato|>`

**Q:** What is the first question?

**A:** `?:  .=(~ lat)`

**Q:** What do we do now?

**A:** Skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Skip `(tail lat)` and continue

**Q:** What is the meaning of
```
:-  (head lat)
%=($ lat (tail lat))
```

where

> `a` is `%and`

and

> `lat` is `[%bacon %lettuce %and %tomato ~]`

**A:** It says to 'cons' the `head` of `lat`&mdash;`%bacon`&mdash; onto
the value of  

> `$`, using `(tail lat)` as the value for `lat`

But since we don't know the value of `$` using `(tail lat)` yet,
we must find out before we can 'cons' `(tail lat)` onto it.

**Q:** What is the meaning of `%=($ lat (tail lat))`

**A:** Call `$` and go back to the restart point with `lat` replaced by  
`(tail lat)`&mdash;`[%lettuce %and %tomato ~]`

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((car lat) a)`

**A:** No, so skip `(tail lat)` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
%=($ lat (tail lat))
```

**A:** It says to 'cons' the `head` of
`lat`&mdash;`%lettuce`&mdash;onto the value of 

> `$`, using `(tail lat)` as the value for `lat`

But since we don't know the value of `$` using `(tail lat)` yet,
we must find out before we can 'cons' `(tail lat)` onto it.

**Q:** What is the meaning of `%=($ lat (tail lat))`

**A:** Call `$` and go back to the restart point with `lat` replaced by  
`(tail lat)`, that is, `[%and %tomato ~]`.

**Q:** `?:  .=(~ lat)`

**A:** No, so skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Yes, so return the next value.

**Q:** What is the next value?

**A:** `(tail lat)`&mdash;`[%tomato ~]`

**Q:** Are we finished?

**A:** Certainly not!  We know what `$` is when `lat` is
`[%and %tomato ~]`, but we don't yet know what it is when `lat` is

> `[%lettuce %and %tomato ~]`

or

> `[%bacon %lettuce %and %tomato ~]`

**Q:** We now have a value for 

> `$`

where `a` is `%and`  
and

> `(tail lat)` is `[%and %tomato ~]`

This value is `[%tomato ~]`  
What next?

**A:** Recall that we wanted to 'cons' `%lettuce` onto the value of
`$`  
where  

> `(tail lat)` was `[%and %tomato ~]`

Now that we have this value, which is `[%tomato ~]`, we can 'cons'
`%lettuce` onto it.

**Q:** What is the result when we 'cons' `%lettuce` onto `[%tomato ~]`

**A:** `[%lettuce %tomato ~]`

**Q:** What does `[%lettuce %tomato ~]` represent?

**A:** It represents the value of
```
:-  (head lat)
%=($ lat (tail lat))
```

when

> `lat` is `[%lettuce %and %tomato ~]`

and

> `%=($ lat (tail lat))` is `[%tomato ~]`

**Q:** Are we finished yet?

**A:** Not quite.  So far we know what `$` is when

> `lat` is `[%lettuce %and %tomato ~]`,

but we don't yet know what it is when

> `lat` is `[%bacon %lettuce %and %tomato ~]`

i.e. the first time `$` was called, when we first ran `+rember [a lat]`

**Q:** Now we have a value for `$` 
where  

> `(tail lat)` is `[%lettuce %and %tomato ~]`

This value is `[%lettuce %tomato ~]`  
This is not the final value, so what must we do again?

**A:** Recall that, at one time, we wanted to 'cons' `%bacon` onto the
value of `$`
where

> `(tail lat)` was `[%lettuce %and %tomato ~]`

Now that we have this value, which is `[%lettuce %tomato ~]`,  
we can 'cons' `%bacon` onto it.

**Q:** What is the result when we 'cons' `%bacon` onto 

> `[%lettuce %tomato ~]`

**A:** `[%bacon %lettuce %tomato ~]`

**Q:** What does `[%bacon %lettuce %tomato ~]` represent?&dagger;

&dagger; Lunch?

**A:** It represents the value of
```
:-  (head lat)
%=($ lat (tail lat))
```

when

> `lat` is `[%bacon %lettuce %and %tomato ~]`

and

> `$` using `(tail lat)` was `[%lettuce %tomato ~]`

**Q:** Are we finished yet?

**A:** Yes.

**Q:** Can you put into your own words how we determined the final value

> `[%bacon %lettuce %tomato ~]`

**A:** In our words:

> "The function `rember` checked each atom of the lat, one at a time, to
> see if it was the same atom `%and`.  If the `head` was not the same as
> the atom, we saved it to be 'cons'-ed to the final value later.  When
> `rember` found the atom  `%and`, it dropped it, and 'cons'-ed the
> previous atoms back onto the rest of the lat."

JL: At this point TLS has a digression where they refactor the function
they'd just been working through,  Fewer if-then-else and one big
`cond`.  This doesn't make sense for what we're doing here.

**Q:**  Now that we have completed `rember`, try this example: `+rember
[a lat]` where `a` is `%sauce`  
and  

> `lat` is `[%soy %sauce %and %tomato %sauce ~]`

**A:**  `+rember [a lat]` is `[%soy %and %tomato %sauce ~]`

**Q:** What is `+firsts l`  
where  `l` is 
```
[[%apple %peach %pumpkin ~]
 [%plum %pear %cherry ~]
 [%grape %raisin %pea ~]
 [%bean %carrot %eggplant ~] ~]
```

**A:**  `[%apple %plum %grape %bean ~]`

**Q:** What is `+firsts l`  
where  

> `l` is `[[%a %b ~] [%c %d ~] [%e %f ~] ~]`

**A:** `[%a %c %e ~]`

**Q:** What is `+firsts l`  
where `l` is `~`

**A:** `~`

**Q:** What is `+firsts l`  
where `l` is 
```
[[%five %plums ~]
 [%four ~]
 [%eleven %green %oranges ~]]
```

**A:** `[%five %four %eleven ~]`

**Q:** What is `+firsts l`  
where `l` is
```
[[[%five %plums ~] %four ~]
  [%eleven %green %oranges ~]
  [[%no ~] %more ~] ~]
```

**A:** `[[%five %plums ~] %eleven [%no ~] ~]`

**Q:** In your own words, what does `+firsts l` do?

**A:** We tried the following:

> "The functions `firsts` takes one argument, which is either a
> null list or a list containing non-empty lists.  It builds another 
> list composed of the first noun of each internal list"

KM: See [1] in the TODOs below.
KM: tl;dr is that a list like `[~ ~ ~]` will cause problems.

**Q:** What is a `lest`?

**A:** A non-empty list

**Q:** What is  

> `(list (lest))`

**A:** This is the type for a list of non-empty lists.

**Q:** Would `~` work if the type is `(list (lest))`?

**A:** Yes, because it's a valid list type.

**Q:** With that in mind, see if you can write the function `firsts`

> Remember the Commandments!

**A:**  This much is easy:
```
|=  l=(list (lest))
^-  (list)
|-
?:  .=(~ l)
  !!
:-  !!
%=($ l (tail l))
```

**Q:** Why 
```
|=  l=(list (lest))
```

**A:** Because we need to create a function, and the argument is a list
of non-empty lists.

**Q:** Why
```
^-  (list)
```

**A:** Because our function returns a list.

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

**Q:** Why `%=($ l (tail l))`

**A:** Because at this point, we need to look at the rest of the list.

**Q:** What about the `!!` 

**A:** `!!` (pronounced 'zapzap') is a command that causes the program
to crash.

**Q:** Why do we want the program to crash?!

**A:** We don't!  When you are developing a program, you can use `!!`
as shorthand for "fill in this part of the program later".

Since `!!` is valid Hoon, you can test out a program that is 
only partially finished.  If you get to an unfinished part, the program
stops.

**Q:** Keeping in mind the definition of `+firsts l`  
what is the typical element of the value of `+firsts l`  
where  

> `l` is `[[%a %b ~] [%c %d ~] [%e %f ~] ~]`

**A:**  `%a`

**Q:** What is another typical element?

**A:** `%c` or even `%e`

**Q:** Consider the function `+seconds`  
What would be a typical element of the value of `+seconds l`
where  

> `l` is `[[%a %b ~] [%c %d ~] [%e %f ~] ~]`

**A:** `%b`, `%d`, or `%f`.

**Q:** How do we describe a typical element for `+firsts l`

**A:** As the `head` of an element of `l`&mdash;(head (head l)).

> See chapter 1

**Q:** When we find a typical element of `firsts l` what do we do with
it?

**A:** 'cons' it onto the recursion&mdash;`%=($ l (tail l))`

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
%=($ l (tail l))     :: natural recursion
```

Note: `::`  (pronounced 'colcol') is a way to make a "comment", a note
to yourself or whoever else might read your code

**Q:** What does `+firsts l` do
```
|=  l=(list (lest))
^-  (list)
|-
?:  .=(~ l)
  !!
:-  (head (head l))
%=($ l (tail l))
```

where `l` is `[[%a %b ~] [%c %d ~] [%e %f ~] ~]`
 
**A:** Nothing yet.  We are still missing one important ingredient in
our recipe.  The line `?:  .=(~ l)` needs a value for the case where `l`
is the null list.  We can, however, proceed without it for now.

**Q:** `?:  .=(~ l)` where `l` is 

> `[[%a %b ~] [%c %d ~] [%e %f ~] ~]`

**A:** No, so skip the `!!` and keep going.

**Q:** What is the meaning of
```
:-  (head (head l))
%=($ l (tail l))
```

**A:** It saves `(head (head l))` to 'cons' onto `%=($ l (tail l))`.  To
find `%=($ l (tail l))`, we call `$` and return to our restart point with 
the new value for `l`,  `(tail l)`

**Q:** `?:  .=(~ l)`  
where

> `l` is `[[%c %d ~] [%e %f ~] ~]`

**A:** No, so skip `!!` and keep going.

**Q:** What is the meaning of
```
:-  (head (head l))
%=($ l (tail l))
```

**A:** Save `(head (head l))`, and recur with `l` set to  `(tail l)`.

**Q:** `?:  .=(~ l)`  
where

> `l` is `[[%e %f ~] ~]`

**A:** No, so skip `!!` and keep going.

**Q:** What is the meaning of
```
:-  (head (head l))
%=($ l (tail l))
```

**A:** Save `(head (head l))`, and recur with `l` set to  `(tail l)`.

**Q:** `?:  .=(~ l)`

**A:** Yes.

**Q:** Now, what is the value of the line
```
!!
```

**A:** Crash!  This is where we need to add our value.

**Q:** What do we need to `con` atoms onto to make a list?

**A:** A list.

> Remember the Law of Colhep.

**Q:** For the purposes of 'cons'-ing, what value can we give when  

> `.=(~ l)` is true?

**A:** Since the final value must be a list, we cannot use `%.y` or
`%.n`.  Let's try `~`.

**Q:** With `~` as a value, we now have three 'cons' steps to go back
and pick up.  We need to:

> I. either

> > 1. 'cons' `%e` onto `~` 
> > 2. 'cons' `%c` onto the value of 1
> > 3. 'cons' `%a` onto the value of 2

> II. or

> > 1. 'cons' `%a` onto the value of 2
> > 2. 'cons' `%c` onto the value of 3
> > 3. 'cons' `%e` onto `~`

> III. or

> > 'cons' `%a` onto
> > > the 'cons' of `%c` onto
> > > > the 'cons' of `%e` onto
> > > > > `~`

In any case, what is the value of `+firsts l`

**A:** `[%a %c %e ~]`

**Q:** With which of the three alternatives do you feel most
comfortable?

**A:** Correct!  Now you should use that one.

**Q:** What is `+insert-r [new old lat]`  
where

> `new` is `%topping`  
> `old` is `%fudge`

and

> `lat` is `[%ice %cream %with %fudge %for %dessert ~]`

**A:** `[%ice %cream %with %fudge %topping %for %dessert ~]`

**Q:** `+insert-r [new old lat]`  
where

> `new` is `%jalapeño`  
> `old` is `%and`

and 

> `lat` is `[%tacos %tamales %and %salsa ~]`

**A:** `[%tacos %tamales %and %jalapeño %salsa ~]`

**Q:** `+insert-r [new old lat]`  
where  

> `new` is `%e`  
> `old` is `%d`

and

> `lat` is `[%a %b %c %d %f %g %d %h ~]`

**A:** `[%a %b %c %d %e %f %g %d %h ~]`

**Q:** In your own words, what does  
`+insert-r [new old lat]` do?

**A:** In our words:

> "`+insert-r` takes three arguments: the atoms `new` and `old`, and a lat.  
> It builds a lat with `new` inserted to the right of the first occurrence 
> of `old`."

**Q:** See if you can write the first three lines of `insert-r.hoon`

Note: We will only use lists of text.

**A:**
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
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

**Q:** Which questions do we ask about the first element?

**A:** We ask `.=((head lat) old)`.  Then we continue because there are
no other interesting case.

**Q:** Now see if you can write the code for `insert-r.hoon`
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  !!
  !!
?:  !!
  !!
!!
```

Replacing `!!` with code.

**A:** Here's our first attempt.
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  (tail lat)
:-  (head lat)
%=($ lat (tail lat))
```

**Q:** What is the value of the generator

> `+insert-r [new old lat]`

that we just determined  
where

> `new` is `%topping`
> `old` is `%fudge`

and

> `lat` is `[%ice %cream %with %fudge %for %dessert ~]`

**A:** `[%ice %cream %with %for %dessert ~]`

**Q:** So far this is the same as `rember`  What do we do in `insert-r` when `.=((head lat) old)` is true?

**A:** When `(head lat)` is the same as `old`, we want to insert `new`
to the right.

**Q:** How is this done?

**A:** Instead of returning `(tail lat)`, let's try 'cons'-ing `new` onto `(tail lat)`

**Q:** Now we have
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  (tail lat)
:-  (head lat)
%=($ lat (tail lat))
```

**A:** Yes.

**Q:** So what is `+insert-r [new old lat]` now
that we just determined  
where

> `new` is `%topping`
> `old` is `%fudge`

and

> `lat` is `[%ice %cream %with %fudge %for %dessert ~]`

**A:** `[%ice %cream %with %topping %for %dessert ~]`

**Q:** Is this the list we wanted?

**A:** No, we have only replaced `%fudge` with `%topping`.

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
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  old
  :-  new
  (tail lat)
:-  (head lat)
%=($ lat (tail lat))
```

When `new` is `%topping`, `old` is `%fudge`, and `lat` is `[%ice
%cream %with %fudge %for %dessert ~]`, the value of the generator
`+insert-r [new old lat]`, is 

> `[%ice %cream %with %fudge %topping %for %dessert ~]`

If you got this right, have one.

**Q:** Now try `+insert-l`

Hint: `+insert-l` inserts the atom `new` to the left of the first
occurrence of the atom `old` in `lat`

**A:** This much is easy, right?
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  :-  old
  (tail lat)
:-  (head lat)
%=($ lat (tail lat))
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

> > `new` is `%topping`  
> > `old` is `%fudge`

> and

> > `lat` is `[%ice %cream %with %fudge %for %dessert ~]`

> the value is

> > `[%ice %cream %with %topping %for %dessert ~]`

> Now you have the idea.

**A:** Obviously,
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  (tail lat)
:-  (head lat)
%=($ lat (tail lat))
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

> > `new` is `%vanilla`  
> > `o1` is `%chocolate`
> > `o2` is `%banana`

> and

> > `lat` is `[%banana %ice %cream %with %chocolate %topping ~]`

> the value is

> > `[%vanilla %ice %cream %with %chocolate %topping ~]`

**A:** 
```
|=  [new=@t o1=@t o2=@t lat=(list @t)]
^-  (list @t)
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
%=($ lat (tail lat))
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
```

If you do this, you `subst2.hoon` will look like this:

```
|=  [new=@t o1=@t o2=@t lat=(list @t)]
^-  (list @t)
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
%=($ lat (tail lat))
~
```

---

<center>**If you got the last function, go and repeat the cake-consing.**
</center>

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
|=  [a=@t lat=(list @t)]
^-  (list @t)
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
where `a` is `%cup`  
and  

> `lat` is `[%coffee %cup %tea %cup %and %hick %cup ~]`
 

**A:** 
```
|=  [a=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) a)
  %=($ lat (tail lat))
:-  (head lat)
%=($ lat (tail lat))
```

> After the first occurrence of `a`, we now recur with `lat` set to
> `(tail lat)`, in order to remove the other occurrences.

The value of this generator is

> `[%coffee %tea %and %hick ~]`

**Q:** Can you see how `+multirember` works?

**A:** Possibly not, so we will go through the steps necessary to arrive
at the value

> `[%coffee %tea %and %hick ~]`

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** No, skip `%=($ lat (tail lat))` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
%=($ lat (tail lat))
```

**A:** Save `(head lat)`&mdash;`%coffee`&mdash;to be 'cons'-ed onto the
result when we call `$` and return to our restart point with `lat` set to 
`(tail lat)`.  

Now determine

> `%=($ lat (tail lat))`

**Q:** `?:  .=(~ lat)`

**A:** No, so skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Yes, so forget `(head lat)`, and return to the restart point with
`lat` set to `(tail lat)`.

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** No, so skip `%=($ lat (tail lat))` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
%=($ lat (tail lat))
```

**A:** Save `(head lat)`&mdash;`%tea`&mdash;to be 'cons'-ed onto the
result when we return to our restart point with `lat` set to `(tail lat)`.  
Now determine

> `%=($ lat (tail lat))`

**Q:** `?:  .=(~ lat)`

**A:** No, so skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** Yes, so forget `(head lat)`, and return to the restart point with
`lat` set to `(tail lat)`.

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** No, so skip `%=($ lat (tail lat))` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
%=($ lat (tail lat))
```

**A:** Save `(head lat)`&mdash;`%and`&mdash;to be 'cons'-ed onto the
result when we return to our restart point with `lat` set to `(tail lat)`.  
Now determine

> `%=($ lat (tail lat))`

**Q:** `?:  .=(~ lat)`

**A:** No, skip `~` and continue.

**Q:** `?:  .=((head lat) a)`

**A:** No, so skip `%=($ lat (tail lat))` and continue.

**Q:** What is the meaning of
```
:-  (head lat)
%=($ lat (tail lat))
```

**A:** Save `(head lat)`&mdash;`%hick`&mdash;to be 'cons'-ed onto the
result when we return to our restart point with `lat` set to `(tail lat)`.  
Now determine

> `%=($ lat (tail lat))`

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
have&mdash;`%hick`&mdash;onto `~`.

**Q:** What do we do next?

**A:** We 'cons' `%and` onto `[%hick ~]`

**Q:** What do we do next?

**A:** We 'cons' `%tea` onto `[%and %hick ~]`

**Q:** What do we do next?

**A:** We 'cons' `%coffee` onto `[%tea %and %hick ~]`

**Q:** Are we finished now?

**A:** Yes.

**Q:** Write the generator `+multiinsert-r`
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  !!
  !!
?:  !!
  !!
!!
```

**A:** 
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  (head lat)
  :-  new
  %=($ lat (tail lat))
:-  (head lat)
%=($ lat (tail lat))
```

It would also be correct to use `old` in place of `(head lat)` in the
first `:-` on line 6 because we know that `.=((car lat) old)`

**Q:** Now try writing the generator `+multiinsert-l`:
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  !!
  !!
?:  !!
  !!
!!
```

**A:** 
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  :-  old
  %=($ lat (tail lat))
:-  (head lat)
%=($ lat (tail lat))
```

---

###  The Fourth Commandment
##### (preliminary)
#### When recurring, always change one argument to be closer to termination.  The changing argument must be tested in the termination condition:
#### when using `(tail l)`, test termination with `.=(~ l)

---

**Q:** Now write the generator `multisubst`
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  !!
  !!
?:  !!
  !!
!!
```

**A:** 
```
|=  [new=@t old=@t lat=(list @t)]
^-  (list @t)
|-
?:  .=(~ lat)
  ~
?:  .=((head lat) old)
  :-  new
  %=($ lat (tail lat))
:-  (head lat)
%=($ lat (tail lat))
```

<a name="chap4"></a>

## 4. Numbers Game

JL: This section is under construction!

**Q:** Is `14` an atom?

**A:** Yes, because all numbers are atoms.

**Q:** is `?=(@ n)` true or false  
where

> `n` is `14`

**A:** True  
because `?=(@ n)` tests if `n` is an atom.

**Q:** Is `14` of type `@ud`

**A:** Yes  
because `@ud` is the type for "unsigned decimal"

**Q:** Is `-3` of type `@ud`

**A:** No,  
because it's a signed decimal, whose type is `@sd`

**Q:** What is `.~3.14159`

**A:** This is a way to write the number `3.14159` in Hoon

**Q:** Is `.~3.14159` of type `@ud`

**A:** No,  
because it's a floating-point number, whose type is `@rd`

**Q:** Is `0xbeef` of type `@ud`

**A:** No,  
because it's a hexidecimal number.  Its type is `@ux`

**Q:** What is `+add1 n`<sup>1</sup>
where `n` is `67`


<sup>1</sup> Run this in the dojo:
```
=add1 |=(n=@ud +(n))
```

KM: We can either introduce the idea of a core here (which will
seriously complicate things) or just assign these things in the dojo.
Let's try creating add1 and sub1 in the dojo and see how far that takes
us.

**A:** `68`

**Q:** What is `+add1 67`

**A:** Also `68`,  

> because we don't need to say "where `n` is `67`" when the argument is a
> number.

**Q:** What is the code for `add1.hoon`

**A:**
```
|=  n=@ud
^-  @ud
.+(n)
```

**Q:** What does the rune `.+` do?

**A:** `.+` (pronounced 'dotlus') increments a number, i.e. it add one.

**Q:** What is `+sub1 n`<sup>1</sup>  
where `n` is `5`

**A:** `4`

**Q:** What is `+sub1 0`

**A:** No answer.  `+sub1` will only work for unsigned decimals (`@ud`s)

**Q:** What is the code for `sub1.hoon`

**A:**
```
|=  n=@ud
^-  @ud
(sub n 1)
```

Note: `sub` here is from the Hoon standard library.  There's a way to
write `sub1` using only `add1`.  We will do that, but not right now.

## A brief digression about cores and libraries

**Q:** What is a library?

**A:** It's a place to keep handy pieces of code.

**Q:** Would you say that `add1` and `sub1` are handy?

**A:** They look handy to me.

**Q:** How would we create a library math operations?

**A:** Let's make a file in our `home` called `lib/math.hoon`

**Q:** What are the contents of `lib/math.hoon` that let us reuse
`add1` and `sub1`

**A:**
```
|%
++  add1
  |=  n=@ud
  ^-  @ud
  .+(n)
++  sub1
  |=  n=@ud
  ^-  @ud
  (sub n 1)
--
```

**Q:** What does the first rune, `|%` do?

**A:** `|%` (pronounced 'barcen')  makes a 'core'.

**Q:** What is a core?

**A:** A core is a structure that has 'legs' (i.e. data) and/or
'arms' (i.e. code that we can run).  

In this case, we are making a core that has one arm.

**Q:** What does the next line do:
```
++  add1
```

**A:** `++` (pronounced 'luslus') defines an arm named `add1`

**Q:** What's between
```
++  add1
```

and

```
++  sub1
```

**A:** The code from our `+add1` generator.

**Q:** What's between
```
++  sub1
```

and

```
--
```

**A:** The code from our `+sub1` generator.

**Q:** What does `--` do?

**A:** `--` (pronounced 'hephep') indicates that we are finished making
our core.

## Plus and Minus

**Q:** Is `.=(0 n)` true or false  
when `n` is `0`?

**A:** True

**Q:** Is `.=(0 n)` true or false  
when `n` is `1066`?

**A:** False

**Q:** What is +plus [46 12]

**A:** `58`

**Q:** Try to write the generator `plus.hoon`

> Hint: It uses `.=(0 n)`, `add1`, and `sub1`

KM: Some description of using a `lib` file here

**A:** 
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  .=(0 m)
  n
(add1 %=($ m (sub1 m)))
```

Wasn't that easy?

**Q:** Let's break this down.  What does the first line do:
```
/+  math
```

**A:** The rune `/+` (pronounced 'faslus') imports `math.hoon` from the
`lib` directory.

So if we want to run `add1` on `n` we can call

> `(add1.math n)`


**Q:** What does `add1.math` mean?

**A:** It means "Call `add1` inside `math`".

**Q:** What does the next line do:
```
=,  math
```

**A:** The rune `=,` imports the namespace `math`.

If we do this, instead of writing

> `(add1.math n)`

we can just write

> `(add1 n)`

**Q:** What the first line after our recursion point `|-`?

**A:** `?:  .=(0 m)`

**Q:** Doesn't that violate The First Commandment?

**A:** Yes, but we can treat `.=(0 n)` like `.=(~ n)` since the former
asks if a number is empty and the latter asks if a list is empty.

**Q:** If `.=(0 n)` is like `.=(~ n)`  
is `add1` like `:=`

**A:** Yes! `:=` builds lists and `add1` builds numbers.

**Q:** What is `+minus [14 3]`

**A:** 11

**Q:** What is `+minus [17 9]`

**A:** 8

**Q:** What is `+minus [18 25]`

**A:** No answer.  We are not using negative numbers here.

**Q:** Try to write the generator `minus.hoon`

> Hint: Use `sub1`

**A:** How about this:
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  .=(0 m)
  n
(sub1 %=($ m (sub1 m)))
```

**Q:** Can you describe how `+minus [n m]` works?

**A:** It takes two numbers as arguments, and reduces the second until
it hits zero.  It subtracts one from the result as many times as it did
to cause the second one to reach zero.

Note: Add `plus` and `minus` to `lib/math.hoon` because we'll use these
to create more math operations.  So you'll have two new arms:
> `++  plus`

and 

> `++  minus`

Only add code from `|=` to the end.  Don't add the lines
```
/+  math
=,  math
```

**Q:** What is `+add-numbers l`  
where

> `l` is `~[3 5 2 8]`

Reminder: `~[3 5 2 8]` is another way to write the list `[3 5 2 8 ~]`

**A:** 18

**Q:** What is `+add-numbers l`  
where

> `l` is `~[15 6 7 12 3]`

**A:** 43

**Q:** What does `+add-numbers l` do?

**A:** It builds a number by totaling all the numbers in its argument.

**Q:** What is the natural way to build numbers from a list?

**A:** Use `plus` in place of `:=`  
`plus` builds numbers in the same way that `:=` builds lists.

**Q:** When building with `:=` the value of the terminal condition is
`~`  
What should be the value of the terminal condition when building numbers
with `plus`

**A:** 0

**Q:** What is the natural terminal condition for a list?

**A:** `.=(~ l)`

KM: This is about where I was considering mentioning that `~` is a type.
However `0` (even if it's a `@ud`) fits this type, so this isn't as useful 
as one might hope.

**Q:** When we build a number from a list of numbers, what should the
terminal condition look like?

**A:** 
```
?:  .=(~ l)
  0
```

just as
```
?:  .=(~ l)
  ~
```

is often the terminal condition line for lists.

**Q:** What is the terminal condition of `+add-numbers`

**A:** 
```
?:  .=(~ l)
  0
```

**Q:** What is the type for `l` in `+add-numbers`

**A:** `(list @ud)`

**Q:** What type of value will `+add-numbers` return?

**A:** `@ud`

**Q:** How is `l` defined, where `l` is of type `(list @ud)`?

**A:** It is either an empty list, or it contains an unsigned decimal,
`(head l)`, and the rest, `(tail l)`, that is also a `(list @ud)`

**Q:** What is used in the natural recursion on a list?

**A:** `(tail l)`

**Q:** How many questions do we need to ask about a list?

**A:** One.  It is either the empty list `~` or it is not.

**Q:** How is a number defined?

**A:** It is either zero or it is one added to the rest, where the rest is
again a number.

**Q:** What is the natural terminal condition for numbers?

**A:** `.=(0 n)`

**Q:** What is the natural recursion on a number?

**A:** `(sub1 n)`

**Q:** How many questions do we need to ask about a number?

**A:** One.

---

###  The First Commandment
##### (first revision)
#### When recurring on a list of atoms, always ask if a list is null as the first question in expressing any function.  
#### When recurring on a number, ask if the number is zero.

---

**Q:** What does `:-` do?

**A:** It builds lists

**Q:** What does `+add-numbers` do?

**A:** It builds a number by totalling all the numbers in a list.

**Q:** What is the terminal condition of `+add-numbers`

**A:**
```
?:  .=(~ l)
  0
```

**Q:** What is the natural recursion for `+add-numbers`

**A:** `%=($ l (tail l))`

**Q:** What does `+add-numbers` use to build a number?

**A:** It uses `plus`, because `plus` builds numbers too!

**Q:** Replace the `!!` in the following definition:
```
/+  math
=,  math

|=  l=(list @ud)
^-  @ud
|-
?:  .=(~ l)
  0
!!
```

**A:** Here is what we filled in:
```
(plus (head l) %=($ l (tail l))
```

Notice the similarity between this line, and the last lines of the
generator `rember`:
```
:-  (head lat)
%=($ lat (tail lat))
```

**Q:** What is `+times [5 3]`

**A:** 15

**Q:** What is `+times [13 4]`

**A:** 52

**Q:** What does `+times [n m]` do?

**A:** It builds up a number by adding `n` up `m` times.

**Q:** What is the terminal condition for `+times`

**A:** 
```
?:  .=(0 m)
  0
```

because n * 0 = 0

**Q:** Since `.=(0 m)` is the terminal condition, `m` must eventually be
reduced to zero.  What function is used to do this?

**A:** `sub1`

---

###  The Fourth Commandment
##### (first revision)
#### Always change one argument when recurring.  It must be changed to be closer to termination.  The changing argument must be tested in the termination condition:
#### when using `tail`, test termination with `.=(~ l)
#### when using `sub1`, test termination with `.=(0 l)

---

**Q:** What is another name for
```
%=($ m (sub1 m))
```

in this case?

**A:** It is the natural recursion.

**Q:** Try to write the code for `times.hoon`

**A:** 
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  .=(0 m)
  0
(plus n %=($ m (sub1 m)))
```

**Q:** What is `+times [12 3]`

**A:** `36`,

> but let's follow through the function one time to see how we get this
> value.

**Q:** `.=(0 m)`

**A:** No.

**Q:** What is the meaning of 
```
(plus n %=($ m (sub1 m)))
```

**A:** It adds `n` (where `n` = 12) to the natural recursion.  If this
is correct, then `$` where `m` is set to `(sub1 3)` should be `24`.

**Q:** What are the new values for `n` and `m`

**A:** `n` is still `12` and `m` is now `2`

**Q:** `.=(0 m)`

**A:** No.

**Q:** What is the meaning of 
```
(plus n %=($ m (sub1 m)))
```

**A:** It adds `n` (where `n` = 12) to  `$` where `m` is set to `(sub1 m)`

**Q:** What is the new value for `m`

**A:** `m` is now 1

**Q:** `.=(0 m)`

**A:** No.

**Q:** What is the meaning of 
```
(plus n %=($ m (sub1 m)))
```

**A:** It adds `n` (where `n` = 12) to  `$` where `m` is set to `(sub1 m)`

**Q:** `.=(0 m)`

**A:** Yes, so return `0`

**Q:** Are we finished yet?

**A:** No.

**Q:** Why not?

**A:** Because we still have three `$`s to pick up.

**Q:** What is the value of the original application?

**A:** Add `12` to `12` to `12` to `0` yielding `36`,

> Notice that `n` has been `plus`-ed `m` times.

**Q:** Argue, using equations, that `+times` is the conventional
multiplication of nonnegative integers, where `n` is `12` and `m` is
`3`.

**A:** 
```
+times [12 3] = 12 + (+times [12 2])
              = 12 + 12 + (+times [12 1])
              = 12 + 12 + 12 + (+times [12 0])
              = 12 + 12 + 12 + 12 + 0
```
which is as we expected.  This technique works for all recursive
functions, not just those that use numbers.  You can use this approach
to write functions as well as to argue their correctness.

**Q:** Again, why is `0` the value for the terminal condition line in
`+times`

**A:** Because `0` will not affect `+plus`.  That is, `n` + 0 = `n`

---

###  The Fifth Commandment
#### When building a value with `plus`, always use `0` for the terminating value, for adding `0` does not change the value of an addition.
#### When building a value with `times`, always use `1` for the terminating value, for multiplying by `1` does not change the value of a multiplication.
#### When building a value with `cons`, always consider `~` for the terminating value.

---

Note: make another arm called `times` in `lib/math.hoon` and add your
code to it.  

**Q:** What is `+add-number-lists l1 l2`  
where  

> `l1` is `~[3 6 9 11 4]`

and

> `l2` is `~[8 5 2 0 7]`

**A:** `~[11 11 11 11 11]`

**Q:** What is `+add-number-lists  [l1 l2]`
where  

> `l1` is `~[2 3]`

and

> `l2` is `~[4 6]`

**A:**  `~[6 9]`

**Q:** What does `+add-number-lists [l1 l2]` do?

**A:** It adds the first number of `l1` to the first number of `l2`,
then it adds the second number of `l1` to the second number of `l2`, and
so on, building a list of the answers, for two lists of the same length.

**Q:** What is unusual about `+add-number-lists`

**A:** It looks at each element of two lists at the same time, or in
other words, it recurs on two lists.

**Q:** If you recur on one list, how many questions do you have to ask?

**A:** One.  `.=(~ l)`

**Q:** When recurring on two lists, how many questions need to be asked?

**A:** Three: both lists are empty, if only the first list is empty, if
only the second list is empty.

**Q:** Do you mean the questions:

> `?&(.=(~ l1) .=(~ l2))`
> `.=(~ l1)`
> `.=(~ l2)`

**A:** Yes.

**Q:** What does the `?&` in first question mean?
> `?&(.=(~ l1) .=(~ l2))`

**A:** The `?&` rune (pronounced 'wutpam') asks questions.  If any of
the questions have the answer `%.n`&mdash;false&mdash;then the result is
`%.n`.  Otherwise, the result is `%.y`.

This is sometimes called the 'AND' operation.  If you recall `?|` (or
'wutbar') this works similarly to that.

**Q:** Can the answer to this question
> `?&(.=(~ l1) .=(~ l2))`

be `~` at the same time as the second is other than `~`.

**A:** No, because the lists must have the same length

**:** Does this mean

> `?&(.=(~ l1) .=(~ l2))`

is the only question we need to ask?

**A:** Yes,

> because `.=(~ l1)` is true exactly when `.=(~ l2)` is true.

**Q:** How do we recur on two lists at the same time?

**A:** The `%=` rune can take several updates:

> `%=(% l1 (tail l1), l2 (tail l2))`

**Q:** That's short form.  How would that look in tall form?

**A:**
```
%=  $
  l1  (tail l1)
  l2  (tail l2)
==
```

**Q:** Now write the code for `add-number-lists.hoon`.

**A:** 
```
/+  math
=,  math

|=  [l1=(list @ud) l2=(list @ud)]
^-  (list @ud)
|-
?:  ?&(.=(~ l1) .=(~ l2))
  ~
:-  (plus (head l1) (head l2))
%=  $
  l1  (tail l1)
  l2  (tail l2)
==
```

**Q:** What are the arguments for `plus`?

**A:** `(head l1)` and `(head l2)`

**Q:** What are the arguments for `:-`

**A:** `(plus (head l1) (head l2)`  
and  
the result of calling `$` with `l1` set to `(tail l2)` and `l2` set to
`(tail l2)`

**Q:** What is `+add-number-lists [l1 l2]`  
where  

> `l1` is `~[3 7]`

and

> `l2` is `~[4 6]`

**A:** `~[7 13]`

> But let's see how it works.

**Q:** `.=(~ l1)`

**A:** No.

**Q:** 
```
:-  (plus (head l1) (head l2))
%=  $
  l1  (tail l1)
  l2  (tail l2)
==
```

**A:** 'cons' `7` onto the natural recursion: 

> `$` with `l1` set to `(tail l1)` and `l2` set to `(tail l2)`.

**Q:** Why does the natural recursion include the `tail` of both
arguments?

**A:** Because the typical element of the final value uses the `head` of
both lists, so now we are ready to consider the rest of both lists.

**Q:** `.=(~ l1)`  
where

> `l1` is now `~[7]`

and

> `l2` is now `~[6]`

**A:** No

**Q:** 
```
:-  (plus (head l1) (head l2))
%=  $
  l1  (tail l1)
  l2  (tail l2)
==
```

**A:** 'cons' `13` onto the natural recursion.

**Q:** `.=(~ l1)`

**A:** Yes.

**Q:** Then, what must be the value?

**A:** `~` because `.=(~ l2)` must be true.

**Q:** What is the value of the application?

**A:** `~[7 13]`.  That is, the 'cons' of `7` onto the 'cons' of `13`
onto `~`.

**Q:** What problem arises when we want  
`+add-number-lists [l1 l2]`  
where

> `l1` is `~[3 7]`

and

> `l2` is `~[4 6 8 1]`

**A:** No answer, since `l1` will become null before `l2`.

> See The First Commandment: We did not ask all the necessary questions!

But, we would like the final value to be

> `~[7 13 8 1]`

**Q:** Can we still write `+add-number-lists` if the lists are not the
same length?

**A:** Yes!

**Q:** What new terminal condition can we add to get the correct final
value?

**A:** Add
```
?:  .=(~ l1)
  l2
```

**Q:** What is `+add-number-lists [l1 l2]`  
where  

> `l1` is `~[3 7 8 1]

and

> `l21 is `~[4 6]`

**A:** No answer, since `l2` will become `~` before `l1`.

> See The First Commandment: We did not ask all the necessary questions!

**Q:** What do we need to include in our function?

**A:** We need to ask two more questions:

> `.=(~ l1)` and `.=(~ l2)`

**Q:** What does the second new question look like?

**A:**
```
?:  =.(~ l2)
  l1
```

**Q:** Here is code for `add-number-lists.hoon` that works for any two
lists.
```
/+  math
=,  math

|=  [l1=(list @ud) l2=(list @ud)]
^-  (list @ud)
|-
?:  ?&(.=(~ l1) .=(~ l2))
  ~
?:  .=(~ l1)
  l2
?:  .=(~ l2)
  l1
:-  (plus (head l1) (head l2))
%=  $
  l1  (tail l1)
  l2  (tail l2)
==
```

Can you simplify it?

**A:** 
```
/+  math
=,  math

|=  [l1=(list @ud) l2=(list @ud)]
^-  (list @ud)
|-
?:  .=(~ l1)
  l2
?:  .=(~ l2)
  l1
:-  (plus (head l1) (head l2))
%=  $
  l1  (tail l1)
  l2  (tail l2)
==
```

**Q:** Does the order of the two terminal conditions matter?

**A:** No.

**Q:** Are those the only two questions we need to ask?

**A:** Yes, because if neither `.=(~ l1)` nor `.=(~ l2)` is true, then both lists contain at least one number.

**Q:** What is `+gt [12 133]`

> Note: `gt` is short for "greater than"

**A:** `%.n`&mdash;false.

**Q:** What is `+gt [120 11]`

**A:** `%.y`&mdash;true.

**Q:** On how many numbers do we have to recur?

**A:** Two, `n` and `m`.

**Q:** How do we recur?

**A:** With `(sub1 n)` and `(sub1 m)`.

**Q:** When do we recur?

**A:** When we know neither number is equal to 0.

**Q:** How many questions do we have to ask about `n` and `m`

**A:** Two: `.=(0 n)` and `.=(0 m)` 

**Q:** Can you write the code for the generator `gt.hoon`

**A:** How about
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  .=(0 m)
  %.y
?:  .=(0 n)
  %.n
%=  $
  n  (sub1 n)
  m  (sub1 m)
==
```

**Q:** Is the way we wrote `gt.hoon` correct?

**A:** No, try `+gt [n m]` when `n` and `m` are the same number.  Let
`n` and `m` be `3`.

**Q:** `.=(0 3)`

**A:** No, skip `%.y`

**Q:** `.=(0 3)`

**A:** No, skip `%.n`

**Q:** What is the meaning of
```
%=  $
  n  (sub1 n)
  m  (sub1 m)
==
```

**A:** Recur, but with both arguments reduced by one.

**Q:** `.=(0 2)`

**A:** No, skip `%.y`

**Q:** `.=(0 2)`

**A:** No, skip `%.n`

**Q:** What is the meaning of
```
%=  $
  n  (sub1 n)
  m  (sub1 m)
==
```

**A:** Recur, but with both arguments reduced by one.

**Q:** `.=(0 1)`

**A:** No, skip `%.y`

**Q:** `.=(0 1)`

**A:** No, skip `%.n`

**Q:** What is the meaning of
```
%=  $
  n  (sub1 n)
  m  (sub1 m)
==
```

**A:** Recur, but with both arguments reduced by one.

**Q:** `.=(0 0)`

**A:** Yes, so the value of `+gt [n m]` is `%.y`

**Q:** Is that correct?

**A:** No, because `3` is not greater than `3`

**Q:** Does the order of the two terminal conditions matter?

**A:** Think about it.

**Q:** Does the order of the two terminal conditions matter?

**A:** Try it out!

**Q:** Does the order of the two terminal conditions matter?

**A:** Yes.  Think first, then try.

**Q:** How can we change the code for `gt.hoon` to take care of this
subtle problem?

**A:** Switch the first two tests
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  .=(0 n)
  %.n
?:  .=(0 m)
  %.y
%=  $
  n  (sub1 n)
  m  (sub1 m)
==
```

**Q:** What is `+lt [4 6]`

> Note: `lt` is short for, you guessed it, "less than"

**A:** `%.y`

**Q:** `+lt [8 3]`

**A:** `%.n`

**Q:** `+lt [ 6 6]`

**A:** `%.n`

**Q:** Now try to write `lt.hoon`

**A:**
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  .=(0 m)
  %.n
?:  .=(0 n)
  %.y
%=  $
  n  (sub1 n)
  m  (sub1 m)
==
```

Note: you will need to move the code for `lt.hoon` and `gt.hoon` into
`lib/math.hoon` for this next question.

**Q:** Here is the code for `eq.hoon`
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  .=(0 m)
  .=(0 n)
?:  .=(0 n)
  %.n
%=  $
  n  (sub1 n)
  m  (sub1 m)
==
```

Rewrite `eq.hoon` using `lt` and `gt`

**A:**
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  (gt n m)
  %.n
?:  (lt n m)
  %.n
%.y
```

**Q:** Do we need another function for testing equality?

**A:** No, but it's a good exercise to write one.

**Q:** `+exp [1 1]`

KM: Almost alll the functions we're creating here have stdlib equivalents.  
Make an appendix to this chapter saying what the real versions are

**A:** `1`

**Q:** `+exp [2 3]`

**A:** `8`

**Q:** `+exp [5 3]`

**A:** `125`

**Q:** Now write the code for `exp.hoon`

> Hint: see The First and Fifth Commandments.

**A:**
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  .=(0 m)
  1
(times n %=($ m (sub1 m)))
```

**Q:** What is a good name for this?
```
/+  math
=,  math

|=  [n=@ud m=@ud]
^-  @ud
|-
?:  (lt n m)
  0
(add1 %=($ n (minus n m)))
```

**A:** We have never seen this kind of definition before; the natural
recursion also looks strange.

**Q:** What does the first question check?

**A:** It determines whether the first argument is less than the second
one.

**Q:** And what happens if it is not?

**A:** We recur with the first argument from which we subtract the
second argument.  When the function returns, we add `1` to the result.

**Q:** So what does the function do?

**A:** It counts how many times the second argument fits into the first
one.

**Q:** And what do we call this?

**A:** Division.

**Q:** If we add this code to a generator, `divide.hoon`, what is

>  `+divide [15 4]`

**A:** Easy, it is `3`.

**Q:** How do we get there?

**A:** Easy, too:

> +divide [15 4] = 1 + (+divide [11 4])
>                = 1 + (1 + (+divide [7 4]))
>                = 1 + (1 + (1 + (+divide [3 4])))
>                = 1 + (1 + (1 + 0))

Note: Add the code for `divide.hoon` to `lib/math.hoon`

---

<center>Wouldn't a `~[ham and cheese on rye]` be good right now?</center>
<center>Don't forget the mustard!</center>

---

**Q:** What is the value of `+length l`  
where

> `l` is `~[%hotdogs %with %mustard %sauerkraut %and %pickles]`

**A:** `6`

**Q:** What is `+length l`  
where

> `l` is `~[%ham %and %cheese %on %rye]`

**A:** `5`

**Q:** Now try to write the code for `length.hoon`

**A:**
```
/+  math
=,  math

|=  l=list
^-  @ud
|-
?:  .=(~ l)
  0
(add1 %=($ l (tail l)))
```

**Q:** What is `+pick [n l]`  
where `n` is `4`  
and 

> `l` is `~[%lasagna %spaghetti %ravioli %macaroni %meatball]`

**A:** `%meatball`

JL: TLS does 1-based indexing.  Since Hoon is not FORTRAN, I'm going
with 0-based indexing.

**Q:** What is `+pick [0 l]`  
where `l` is `~[%a]`

**A:** `%a`

**Q:** Try to write the code for `pick.hoon`

**A:**
```
/+  math
=,  math

|=  [n=@ud l=(list @t)]
^-  @t
|-
?:  .=(0 n)
  (head l)
%=  $ 
  n  (sub1 n)
  l  (tail l)
==
```

**Q:** What is `+rempick [n l]`  
where `n` is `2`  
and

> `l` is `~[%hotdogs %with %hot %mustard]`

**A:** `~[%hotdogs %with %mustard]`

**Q:** Now try to write the code for `rempick.hoon`

**A:**
```
/+  math
=,  math

|=  [n=@ud l=(list @t)]
^-  (list @t)
|-
?:  .=(0 n)
  (tail l)
:-  (head l)
%=  $
  n  (sub1 n)
  l  (tail l)
==
```

**Q:** What is the type for an unsigned decimal?

**A:** `@ud`

KM: TLS at this point does something called "no-nums" which removes all
the numbers from a list.  Again, this is possible in Scheme because
numbers are different from non-numbers.  In Hoon, everything is a
number.  Something like this is possibly possible with a wet gate.  I
need to investigate.

(And then once I decide if it's possible, I should decide if I want to
introduce the idea of a wet gate now or ever.)

KM: And also TLS has `eqan` which does something special based on
whether the parameters are numbes.  Again, Hoon doesn't doesn't do this.

**Q:** Now write the code for `occur.hoon` which counts the number of
times an atom `a` appears in a list `l`
```
/+  math
=,  math

|=  [a=!! l=!!]
^-  !!
|-
?:  !!
  !!
?:  !!
  !!
!!
```

(Replace the `!!`s with your code.)

**A:**
```
/+  math
=,  math

|=  [a=@ l=(list @)]
^-  @ud
|-
?:  .=(~ n)
  0
?:  .=((head l) a)
  (add1 %=($ l (tail l)))
%=($ l (tail l))
```

**Q:** Write the code for `is-zero.hoon` where `+is-zero n` is `%.y` if `n` is
`0` and `%.n` (i.e. false) otherwise.

**A:**
```
|=  n=@ud
^-  ?
?:  .=(1 n)
  %.y
%.n
```

**Q:** Guess how we can further simplify this, making it a one-liner.

**A:** By removing the `?:`
```
|=(n=@ud ^-(? .=(1 n)))`
```

We could also remove the `^-` and make it even simpler:

```
|=(n=@ud .=(1 n))`
```

**Q:** Now rewrite the code for `rempick.hoon` that removes the
n<sup>th</sup> atom from a list (starting at `0`).  For example,  
where  

> `n` is `2`

and

> `l` is `~[%lemon %meringue %salty %pie]`

the value of `+rempick [n l]` is

> `~[%lemon %meringue %pie]`

Add the code for `is-zero` to `lib/math.hoon` and use it in your answer.

**A:**
```
/+  math
=,  math

|=  [n=@ud l=(list @t)]
^-  (list @t)
|-
?:  (is-zero n)
  (tail l)
:-  (head l)
%=  $
  n  (sub1 n)
  l  (tail l)
==
```

### TODO

- Right now this is a Markdown file. It'd be pretty sweet to make some
  kind of interactive version where it doesn't give the answer until you
  hit the spacebar or something.

- Introduce tree addressing at some point.  Possibly a new, original
  chapter, since Scheme doesn't have anything like that.

- In re: The Law of Null?, Hoon doesn't exactly have a function `null?`.
  What it does have is `?~`, but this has a built in if-then-else
  structure.  We get into the if-then-else in chapter 2.  In chapter 4,
  we start using numbers, so it'll be important to distinguish between 0
  and ~, so introduce it then.

- Introduce more tall form

- At some point explain what a 'core' is (much later)

- Come up with an entirely new non-food-based theme and rewrite the
  whole thing.

- Add a discussion about faces?

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

I note the latest dojo is returning the later version, so to avoid
confusion, I should make sure I mention this.  I'll keep using the long
version, because this matches the data as it is.

- [1] TLS notes that the list argument only contains non-empty lists.

The code for firsts would not work on something like:
```
[~ ~ %atom ~ ~]
```
Even though Hoon calls this a valid list.

(For that matter, none of the previous examples would work with this.)

Once you know what you're doing, it's pretty straightforward to work
with these kinds of lists, possibly using the `i` and `t` faces.  The 
problem is, this is written for people who don't know what they're 
doing.

Anyway, should consider probably noting that any list with a null
anywhere but the end is going to cause problems.

- I've tried to avoid irregular forms, but I've also decided that %-
  (the rune for calling functions like `head`) makes things too wordy
  for my taste.  (Maybe it's reading a book on Scheme, but this is how
  function calls are _supposed_ to look.)

- Tidy up terminology.  Make sure we don't refer to a "function" when we
  should be referring to a "gate" or maybe a "trap".

- Review the original TLS preface and pull in ideas, etc.

- Scheme has no types.  Hoon has types.  I've shoehorned types in where
  it makes sense, but this could probably be improved.  There are two books 
  to look into: Little MLer (since ML has types) and Little Typer (which is 
  all about tpes).

- Actually make some exercises at the end of chapters!  It's easy enough
  to follow along with someone and assume you "get" it, only to struggle
  when you actually have to do something yourself.

- Terms can be used as types, and I ought to mention this somewhere.

- Scheme has seven primitives: `quote`, `atom`, `eq , `car`, `cdr`,
  `cons`, and `cond`.  Hoon doesn't really have the concept of a
  "primitive".  Ought to remove that term.

- I'm using generators that expect only one argument.  This simplifies
  things, but we might want generators a bit more explicit.

- Make code files so they can be opened in another tab/window or
something.  This isn't a book and we have options.

- As an exercise, do the classic "decrement using increment".  The first
  question for that breaks The First Commandment so do that a bit later.

- You can call functions like so:
```
%-  times
[n %=($ m (sub1 m))]
```

It's a bit more clear what's going on done this way and chap 4 might
benefit from that.

If you get anything out of it, if you'd like to show your appreciation,
and encourage me to keep at it until we get to the part where you
write the Y combinator in Hoon, feel free to drop a tip in the tip jar.

Bitcoin:

![bitcoin wallet](wallet.png)

1Lp2hDwt4xBvySUyFSNA56w95dAaCYDZry

Cash app: [cash.app/$jleblanc](https://cash.app/$jleblanc)

Venmo: [venmo.com/jrl314](https://venmo.com/jrl314)

PayPal: [paypal.me/jleblanc314](https://www.paypal.me/jleblanc314)

