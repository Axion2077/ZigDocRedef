# Chapter 0: Syntax
This section will be used to explain the syntax of Zig. 
Otherwise known as ```grammar.peg``` which is used by the devs to explain how lines of code must be formmated.
I will be explaining this in great detail so that if this guide ever becomes outdated, you could read the grammer.peg for yourself.
Note that i am not going to be explaining universal coding concepts or anything you can google for yourself.
Also, I will be reffering to zigs ```grammer.peg``` as PEG, but zig uses a custom parser therefore its PEG is not standard, so I should be calling it zig PEG, but im not going to do that.

# Rudimentary concepts.
\n: In zig and most other programming languages ```\n``` is used to represent a new line.
`\n` is an escape sequence, more on that later.
Also it is essentially considered one symbol, rather than a ```\``` and a ```n```, This is important for later.

[]: In PEG ```[]``` is a character class. It's purpose is to match with one character. (this has a different meaning when coding, but that isn't important for right now)
This is used to "consume" characters then move the input cursor forward.
Example: If you imagine the word ```chicken``` the parser is simply checking a character, in this case, "c" then running some code and then checking "h" and so on.

*: In PEG `*` this is used to indicate a function is done an indefinite amount of times, meaning either zero or any number of times. 1, 2, 3, etc.
+: Simmilar to the `*` except "+" plus must run atleast once. It can run any number of times, but it has a minimum of one, instead of zero.

' ': In PEG single quotations ```'``` are used to check if a string is equal to another string. 
Note that they do not consume, i.e. iterate through text. 
Going back to our ```chicken``` example, if `'ch'` is run, the parser would only check if the ch in chicken is equal to ch and would not move on to "i."

With respect to ```[]``` there are two symbols that change how the parser matches with characters.
![]: this is a negative lookup. Its purpose is to check if a character is NOT in the character class ```[]``` It also STOPS the character class from consuming. 

[^]: this is a negated character class. Its purpose is to consume any characters that are not in the character class. 
Example: [^c] this would consume everything except lowercase "c"

Two more minor concepts before I begin explaining the actual syntax.

<-: this arrow sign ```<-``` is used as an equal sign in PEG, for clarity, i will just be using "=" instead of "<-"

/: when ```/``` is by itself in PEG, it has the same function as "else." I.e. if this is not true, do this instead.
I will replace ```/``` with ```|``` for clarity, but remember this if you want to read the PEG. 

This brings us to our first line of syntax in PEG.
I would recommend you try to read it for yourself rather than looking at my explanation if you can.
```
line_comment = '//' ![!/][^\n]* | '////' [^\n]*
```
this translates to, if the string is ```//``` 
check if the next character is NOT ```!``` or ```/``` 
if so consume all characters except for a new line aka ```\n``` 
else check if the string is ```////``` then consume all characters except for a new line aka ```\n```

Hopefully that was simple enough, if not you may need to reread this a few times.
Again, \n is the new line symbol, it is considered one character not two. 
Thats why it is checked individually while, ```[!/]``` checks for ```!``` and ```/``` seperately.
Also, * causes [^\n] to be repeated until it reaches a new line. instead of it consuming a single character.

If you managed to figure out the purpose of this, congragulations, if you didn't that's ok.
It's used to skip past any comments in a text, hence the name. "//" is the syntax used to declare a comment.
```
skip = ([ \n] | line_comment)*
```
First it tries to consume a space or a newline, if it is unable to do that, it runs line_comment.
The parenthesis are used in conjuction with the * to indicate that it will run until it finds a character that is not a comment.
Its purpose is to ignore all white space (spaces or new lines) and comments in code.

This is the final syntax having to do with comments, then we will move on to some more complicated topics.
```
doc_comment = ('///' [^\n]* [ \n]* skip)+
```
It checks for '///' if it doesnt find it, then it does nothing.
if it does find it, it consumes anything that isn't a newline, then it consumes spaces and new lines until it reaches a character. Then it runs skip.
the + is used to indicate that it can either fail, aka not find ```///``` or consume an entire line of code.

# Operators.
This is section is relating to the syntax of operators In code, not in PEG, there will be more PEG later though.
An operator is something like ```=``` ```+``` ```^``` or ```!``` etc. 
Basically symbols that denote a certain operation.
I will use `//` to give an explanation of each one.
This is a list of all the simple ones.

`+` // If you need me to explain this, you may want to be learning something easier.
`-` // can be used to subtract numbers `1 = 2 - 1`, it is also a negation.
    Meaning that if var a = 10, then we later write `-a` a becomes -10. The value of a is negated.
`*` // This is an Asterisk, used to multiply values together. 9 = 3 * 3
`/` // This is a Slash, used to divide values. 3 = 9 / 3 
`%` // This is a Percent, it is used to find the remainder of a division.
    A Remainder is what is left after dividing a number. `13 / 3` is 4 with a remainder of one.
    Doing `13 % 3` would return 1, which is the remainder. `1 = 13 % 3`

# Operator: Boolean
`and` // checks if two or more conditions or variables are true.
    This short circuits, meaning that if the first condition is false, it doesn't check the second.
`or` // checks if one out of two or more conditions are true.
    This also short circuits, meaning that if the first condition is true, it doesn't check the second.
Make sure to put the value that is more likely to be false first with `and` and last with `or` to optimize speed. This is not required.
`!` // When used on a condition or variable that is true or false, it returns the opposite.
    i.e. if `a = true`, then `a!` is false and vice versa.
`==` // Checks if a value or condition is equal to another value or condition, 
    i.e. if so it outputs true, otherwise it outputs false.
`!=` // Checks if a value or condition is NOT equal to another value or condition.
`<` // less than | `<=` // less than or equal to | `>` // greater than | `>=` // greater than or equal to
    If you took a math class you should probably be fammiliar with these terms.



# Operator: Assignment
These are your is equal to or ```=```, operators. They are used when defining a variable or constant.
Ex: ```var x = 1```
variables and constants will be explained later.
There are multiple types of Assignment Operators, I will go through all of them before explaing the other types of operators.

# Operator: Plain Assignment
This is reserved soley for the ```=``` operator.
Simply used to define that something is equal to something else.

# Operator: Normal Arithmetic Assignment
These are just simplifications of typical Assignments that include arithmetical operations.
They are written like `var1` `operator` `is equal to` `var2`
These are the logical equivalent of `var1` `is equal to` `var1` `operator` `var2`
Example: `i += 1` is the same as `i = i + 1`

I will be using `a` to represent var1 and `b` to represent var2.
After the comment `//` i will give the logical equivalent and explain its use.

`a += b` // `a = a + b` adds `b` to `a`
`a -= b` // `a = a - b` subtracts `b` from `a`
`a *= b` // `a = a * b` multiplys `a` by `b`
`a /= b` // `a = a / b` divides `a` by `b`
`a %= b` // `a = a % b` sets `a` equal to the remainder of `a` divided by `b`


```zig
pub fn main() void {
    // Hello
}
```
