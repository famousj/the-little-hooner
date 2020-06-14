# The Little Hooner

## Adapted from The Little Schemer (Fourth Edition) by Daniel P. Friedman and Matthias Felleisen
## Adaptation by ~ribben-donnyl (AKA J LeBlanc)

## The Ten Commandments

KM

## The <strike>Five</strike> Four Rules

###  The Law of Head
> The primitive `head` is defined only for lists.

### The Law of Tail
> The primitive `tail` is defined only for lists. The `tail` of any
list is always another list.

### The Law of Colhep
> The rune `:-` takes two arguments.  The second argument to `:-` must be a 
list or null.  The result is a list.

### The Law of Dottis</center>
> The rune `.=` takes two arguments.  Each must be an atom.

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

- I am accepting pull requests!

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

**Q:** Is it true that this is a list?

> `['atom' 'turkey' ~]`

**A:** Yes,

> because `['atom' 'turkey']` is two atoms enclosed by a left square
> bracket (which is called a '`sel`') and a right square bracket (which is 
> called a '`ser`'), ending in the null value (`~`, pronounced '`sig`')

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

- JL: TLS talks about the empty lists in this section, but Hoon doesn't allow empty lists.  This might be a good spot to talk about how lists can't really be empty.

**A:** Yes,

> the null value is an atom

**Q:** What is the `head` of `l` where `l` is the argument

> `['a' 'b' 'c' ~]`

- JL: TLS calls this `car`.  You could do the same with tree addresses, but
  I think using `head` and `tail` is way easier to understand conceptually.

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

> The null value is an atom and you cannot ask for the `head` of an atom.

- TLS then asks about `car` of an empty list.  As noted above Hoon
  doesn't support empty list.

---

###  The Law of Head
#### The primitive `head` is defined only for lists.

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

> the null value

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

> The null value is an atom and you cannot ask for the `tail` of an
> atom.

---

###  The Law of Tail
#### The primitive `tail` is defined only for lists. The `tail` of any list is always another list.

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

**A:** It takes any list.

**Q:** What does `tail` take as an argument?

**A:**  It takes any list.

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

- For this question, TLS has the empty list.  In Hoon, the null has more
  or less the same purpose as the empty list, especially when doing recursion. 

**A:** `[['a' 'b' ['c' ~] ~] ~]`

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

- JL - The next section of TLS is specific to the empty list which, as
  mentioned above, is not possible in Hoon.  So I'm cutting this discussion.
  At some point before digging into recursion, I'll discuss `?~`.

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

> because the null value, `~`, is an atom.

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

JL - TLS has `()` and `(strawberry)`, but there are no empty lists in
Hoon.

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



