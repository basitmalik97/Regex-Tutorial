# 17 Regex Tutorial or: How I Learned to Stop Worrying and Love Regular Expressions

  Look, I give up, okay? Dr. Strangelove popped into my mind and I thought it would be fun. I'm all about fun! You know whats not fun though? When you are trying to make an app for people but you just can't trust them to enter, say, a valid email into an input field. So, being new to the game of web dev, you create a masterfully crafted and intricate series of if statements becuase you're really getting good at those and they will do the job and do the job right... except that well, now that you are testing, your stupid app thinks that "john cena @email.co" is a valid email. If anyone is allowed to have a space in their email it would be him but unfortunately, it just doesn't work like that so you go back and add yet another if statement before you silently turn off the computer and, with great ceremony, deposit it out the window, preferably very high up so it makes a good crash with lots of little bits on the ground below. I'm here to save you from that fate. I'm here to save your computer from that fate. I'm here to demonstrate how easily this kind of problem can be solve with my friend the Regular Expression.

## Summary

Here is the regular expression, or regex, for matching an email:

```/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/``` 

I know it looks daunting but at least it's only on one line right? Just kidding. Trust me and I promise I'll take you through what each symbol is doing and break it down so that it's clear and easy to understand. If you are here becuase you don't know what the heck is going on in a regex, don't worry. We'll get through it together. If you are here becuase you need a regex wizard, feel free to reach out. I'm very hireable And I can absolutely cast regex spells at all of your problems until they go away. Refer to the auther section at the bottom of the scroll- I mean gist. Now, my apprentice(s), let's get into it! 

## Table of Contents

- [17 Regex Tutorial or: How I Learned to Stop Worrying and Love Regular Expressions](#17-regex-tutorial-or-how-i-learned-to-stop-worrying-and-love-regular-expressions)
  - [Summary](#summary)
  - [Table of Contents](#table-of-contents)
  - [Regex Components](#regex-components)
    - [Anchors](#anchors)
    - [Bracket Expressions](#bracket-expressions)
    - [Grouping Constructs](#grouping-constructs)
    - [Quantifiers](#quantifiers)
    - [Character Escapes](#character-escapes)
    - [Character Classes](#character-classes)
    - [Literal Characters](#literal-characters)
  - [Conclusion](#conclusion)
## Regex Components

### Anchors

The first anchor we can see in our example is ```^```. This lets the computer know that a match must occur at the beginning of the string. In other words, if you're looking at a string and there was even one character that didn't match the terms in your regex at the beginning of an otherwise beautiful email address, that string would not be an email address. without the ```^```, this: ```[email@email.com``` would be a valid email. But with it, you need to remove the square bracket before it is valid.

here's an example:

```
const str1 = 'email@email.com';

const str2 = '[email@email.com';

// with ^ anchor
const regex = new RegExp(/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/);

console.log(regex.test(str1));
// expected output: true 

console.log(regex.test(str2));
// expected output: false 
```

However, without the ```^``` anchor, both would return true: 

```
const str1 = 'email@email.com';

const str2 = '[email@email.com';

// without ^ anchor
const regex2 = new RegExp(/([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/);

console.log(regex2.test(str1));
// expected output: true 

console.log(regex2.test(str2));
// expected output: true 
```

the other anchor is the ```$``` at the end of our regular expression. The ```$``` means that we are matching a string that ends with whatever terms are before it. simply put, any character at the end of a potential matching string that is not valid, as defined by our regex, disqualifies the string from being a match. 

I whipped up another example for you: 

```
const str1 = 'email@email.com';

const str2 = 'email@email.com]';

// with $ anchor
const regex = new RegExp(/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/);

console.log(regex.test(str1));
// expected output: true 

console.log(regex.test(str2));
// expected output: false 

// without $ anchor
const regex2 = new RegExp(/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})/);

console.log(regex2.test(str1));
// expected output: true 

console.log(regex2.test(str2));
// expected output: true 
```
### Bracket Expressions

Next up in the anatomy of our regex, we have bracket expressions. where the anchor was like the skin of our regex, bracket expressions are like the meat. Bracket expressions are also called positive character groups. They basically define what our valid characters are when matching a string. The first bracket expression is ```[a-z0-9_\.-]```. Let's break this down a bit. first, let's look at the term ```a-z```. This is defining a range of characters from "a" through to "z". So, we are claiming that the entire alphabet is valid. If we wanted to only include 'a' through 'c', we could write ```a-c```. That would include "a", "b", and "c". So, "-" allows us to define a range. We could also decide to define a specific set. As an example, let's say we wanted to only include "a", "c" and "e" in our set. To do this, we'd write ```ace``` within our square brackets. this is literally defining "a" or "c" or "e" as valid. Now, regular expressions match case-sensitively so, if you wanted to include upper and lowercase characters with the range of the entire alphabet, you'd write your bracket expression thusly: ```[a-zA-Z]```.

Now, if we wanted to include numbers as well (and we do), And we were feeling generous enough to allow all numbers (and we are), we would include them by adding a range of ```0-9``` (that's 0 through 9) to our bracket expression (and we have). 

next up in our first pcg (positive character group) is a list of special characters. we have ```_\.-```, which is to say an underscore ```_```, a hyphen ```-``` and a dot ```\.```. A quick note about the dot. I know that, to the unenlightened, it looks like a backslash has snuck in there but this is actually something called escaping a character. There is a section about it below.

Okay! one down, two to go. Next pcg is ```[\da-z\.-]```. Bassically all old territory. The only new thing here is the ```\d```. This just means that any "Arabic numeral digit" is a match. last time you saw it written as ```0-9```. Today, it's this ```\d```. Está claro? Good!

There is a third pcg but it's made up of stuff we've covered so we're on to

### Grouping Constructs

In the case of our regex, we have used grouping constructs to create different sections, known as subexpression, within our regex: ```([a-z0-9_\.-]+)```,``` ([\da-z\.-]+)```, and ```([a-z\.]{2,6})```. To create a subexpression, simply wrap it in parentheses.

### Quantifiers

Quantifiers limit the string, or section of the string that the regex matches. In our case, let's look at the subexpression at the end of our regex: ```.([a-z\.]{2,6})```. The quantifier is the ```{2,6}``` part. What this means is that this part of the string must be between 2 and 6 characters in langth. This essentially allows strings that end with things like ".com", ".co" ".net" to be valid emails and strings that end with things like ".thismywebsite" are not.

There are other quantifiers as well. In our regex, we are using a ```+``` at the end of our other two subexpressions. This means that our regex matches a pattern one or more times. Essentially, anything before the "@" of an email has to be 1 or more characters long to create a match. Same with the middle subexpression.

### Character Escapes

Let's look again at that weird thing going on with our dot: ```\.```
Bassically, without the backslash, it'll do something else. In the case of regex, a naked dot matches any character except the newline character. When it is escaped it just matches a dot. That may seem a little circular but don't worry about it.


### Character Classes

Character classes are a set of characters that will result in a match. examples of these in our regex are ```\d```, which is a set of all numbers; as well as bracket expressions, which are like custom character classes. we also talked about ```.```. As I mentioned, an unescaped dot matches any character except the newline character.

### Literal Characters

Lastly, between the first and second subexpression, there lives a ```@```. This is a literal character. It's just there to make sure no strings get the green light without an '@'.

## Conclusion

That's It! There are many regex out there for matching different patterns. Heck, there are many regex out there for matching an email. Some are definitely better then this one and some are definitely worse. Remember, when you think you could use a regex to solve a problem, google is your friend! you do not need to reinvent the wheel. If you really can't find what you seek, you may be able to fine tune an already existing regex to suit your needs.

Anyway, I hope this was helpful in giving you the basics of regex. take care and good luck out there.
