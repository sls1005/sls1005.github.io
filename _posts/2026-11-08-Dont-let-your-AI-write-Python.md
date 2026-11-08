---
title: "Don't Let Your AI Write Python"
layout: post
lang: en
categories: [ai, programming, python, "vibe coding"]
---
There is a trend to utilize LLM for coding these days, in Python or  some other programming languages. This article isn't about whether doing so is moral, lawful or secure, but what would be effective and efficient and what is not. And if you decide to use LLM this way, you should at least know what you are doing.

### About LLM

First, let's talk about LLM. They are a result of a technology known as machine learning, based on statistics. They seem to be able to do many things recently, but this doesn't change their nature: they are based on *statistics*, and what they can do is merely to predict the next word in a sentence.

For example, given three random words, A, B and C; if B is more likely to appear after A than C or A in most texts, then an LLM would inferably be likely to produce B after A.

This makes them suitable for writing in natural languages, but not programming languages. Why? That's because programming languages are historically designed for efficiency.

According to information theory, if a symbol usually appears after the other symbol, then it carries very little information, but if an uncommon symbol appears instead, it would carry much more information; for example, in English spelling, the letter after Q is usually U, and if it isn't (such as in qi), it carries more information, because this letter combination almost never appears.

Programming languages utilize this: Rare symbols are used for important meanings. Escape sequences, for example, start with backslashes (`\`), because the backslash is a rare symbol. (If you don't know about escape sequences, they are like codewords—using existing symbols to represent meanings that would otherwise be unrepresentable.) If a rare symbol combination is defined as correct, a programmer could type less letters to mean more.

The problem is, LLMs don't know to produce rare symbol combinations, and they are more likely to produce *common* symbol combinations, even if incorrect.

### About Python

Among programming languages, Python is very special for its syntax: it uses indentation, or the lack of indentation, to signal the end of a block.

What's indentation? It means there are more spaces at the start of these lines than the lines before and after them. These lines starting with more spaces are considered *indented*, thus forming a block in Python.

Consider the following code:

```python
a = 1
b = 2
if a < b:
  c = b - a
  print(c)

```

If you don't know Python, the meaning of this snippet is: “Define `a` as 1; define `b` as 2, and if `a` is less than `b`, define `c` as what `b` minus `a` is and display `c`.”

See? There are two spaces at the start of the fourth line (`  c = b - a`) as well as at the start of the fifth line (`  print(c)`), meaning they form a block, as two spaces are more than zero, and there are zero spaces at the starts of lines before and after them, and they are after the line with a keyword `if` and a colon (`:`), meaning they form an if-block, a conditional block that executes if and only if the condition is fulfilled by the time this line is encountered by the program that interprets this script.

The Python community recommends using four space for each level of indentation (whereas the first example used two), but their interpreter and language specification are more flexible: You may use any number of spaces, or tabs, for indentation, as long as there are more space(s)/tab(s) than the lines before and after the lines in the block, and each line in that block starts with an equal number of spaces.

Other programming languages that use indentation for similar purposes include [Nim](https://nim-lang.org/), [Genie](https://docs.vala.dev/genie/) and [YAML](https://yaml.org/).

However, if you write:

```python
a = 1
b = 2
if a < b:
c = b - a
print(c)

```

Your programming teacher would say it is wrong, because the interpreter cannot tell where the block ends. Is it because the interpreter is stupid? No, it isn't. Even humans cannot know for sure whether the if-block ends by the end of the fourth or by the fifth line. Yes, they can tell from the context, but not reliably without indentation.

Why? It's because the language, Python, doesn't have (and doesn't need) a single symbol to mean “the end of the block” just as other languages do; instead, it is determined by the numbers of spaces at the starts of the lines in the block, the lines before them, and the lines after them.

Remember when I said, “If a symbol usually appears after the other symbol, then it carries very little information, but if an uncommon symbol appears instead, it would carry much more information”? Python utilizes this (even if most programmers might not ever think of it this way); the space is a common character, and when it appears, it carries little information (that the block has not ended), but if it doesn't, and a character that is not space nor newline (as blank lines might be ignored) appears instead, or if EOF is reached, it carries more information (that the block has ended).

### You Are a Tokenizer

Imagine that you are a tokenizer, namely, someone or something that breaks things into tokens. Now you have to parse the following Python code:

```python
a = 1
b = 2
if a >= b:
    c = a + b
    print(c - a)
else:
    c = b - a
    print(c + a)
```

It should be kept in mind that a compiler, interpreter or tokenizer program doesn't have eyes; text is one-dimensional data to them, so the first step is to view the code as one-dimensional (`<br>` means linebreak):

```txt
a = 1<br>b = 2<br>if a >= b:<br>    c = a + b<br>    print(c - a)<br>else:<br>    c = b - a<br>    print(c + a)
```

Spaces that don't separate words and aren't used for indentation are irrelevant, so if the tokenizer is smart, you can ignore them:

```txt
a=1<br>b=2<br>if a>=b:<br>    c=a+b<br>    print(c-a)<br>else:<br>    c=b-a<br>    print(c+a)
```

And then, we know that the number of spaces used for indentation is determined by the first indented line in the block. In this case, four spaces are used. So (`<ind>` means indentation here):

```txt
a=1<br>b=2<br>if a>=b:<br><ind>c=a+b<br><ind>print(c-a)<br>else:<br><ind>c=b-a<br><ind>print(c+a)
```

So the result is likely (`|` separates tokens here):

```txt
a|=|1|<br>|b|=|2|<br>|if|a|>=|b|:|<br>|<ind>|c|=|a|+|b|<br>|<ind>|print|(|c|-|a|)|<br>|else:|<br>|<ind>|c|=|b|-|a|<br>|<ind>|print|(|c|+|a|)
```

Note that there's only one level of indentation here (i.e., no nested indentation), and you already spent 4 tokens on indentation, or 16 if the tokenizer doesn't find the four-spaces pattern. How many tokens would we spend if the language were with an explicit block end instead of indentation? Likely one or two, because there is only one block-end here.

Seeing this, one might think Python is a very inefficient programming language, but if it were, it wouldn't have dominated the field of machine learning. So what's wrong? The answer is simple: LLMs don't have eyes.

### LLMs Don't Have Eyes

This doesn't mean that LLMs are not capable of processing visual information; it just means that they will not use their GPU to process the picture of a Python source code file, and they are more likely to process it as one-dimensional data, whereas eyed human programmers would more likely process it as a 2D image: you see all lines at the same time, even if you read them one by one, and if there's indentation or outdentation, human eyes could recognize it easily: The visible part of an indented line starts at a different geometric position; and when reading code, indentation will not trouble these humans, because it is just a blank area.

That's the secret. Python is a language designed for humans (or more exactly, beings capable of processing visual information efficiently), in a way that makes it unsuitable for non-humans (or beings not capable of processing visual information efficiently). It uses a repeatedly occurring character (space or tab) to signal a meaning, and the non-occurrence of this character signals that meaning.

The character we used to ignore now became a token. This is why asking an LLM to code in Python might be inefficient.

### What Languages to Use Then?

My answer would be Ruby, Lua and Rust, while Dart, Java and Kotlin are also not bad choices. Many of these languages are what programmers used to hate—too verbose, too explicit—inefficient for humans, but it turns out that LLMs seem to be much better at handling explicit things than implicit things.

Ruby and Lua, on the other hand, are scripting languages designed to be close to natural languages but don't require indentation, and instead use explicit words to signal block ends.

Python isn't useless. It is just too efficient for humans (or those that can process visual information efficiently and understand implication easily) but not for others.
