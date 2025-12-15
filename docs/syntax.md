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
the + is used to indicate that it can either fail, aka not find ```///``` or consume atlease one entire line of code.

# Operators
This is section is relating to the syntax of operators In code, not in PEG, there will be more PEG later though.
An operator is something like ```=``` ```+``` ```^``` or ```!``` etc. 
Basically symbols that denote a certain operation.
I will use `//` to give an explanation of each one.
This is a list of all the simple ones.

`+` // If you need me to explain this, you may want to be learning something easier.
`-` // can be used to subtract numbers `1 = 2 - 1`, it is also a negation.
    Meaning that if `a = 10`, then we later write `-a`, `a` becomes `-10`. The value of `a` is negated.
`*` // This is an Asterisk, used to multiply values together. `9 = 3 * 3`
`/` // This is a Slash, used to divide values. `3 = 9 / 3`
`%` // This is a Percent, it is used to find the remainder of a division.
    A Remainder is what is left after dividing a number. `13 / 3` is 4 with a remainder of one.
    Doing `13 % 3` would return 1, which is the remainder. `1 = 13 % 3`

# Operator: Boolean
`and` // checks if two or more conditions or variables are true.
    This short circuits, meaning that if the first condition is false, it doesn't check the second.
`or` // checks if one or more of two or more conditions are true.
    This also short circuits, meaning that if the first condition is true, it doesn't check the second.
Make sure to put the value that is more likely to be false first with `and` and last with `or` to optimize speed. This is not required.
`!` // When used on a condition or variable that is true or false, it returns the opposite.
    i.e. if `a = true`, then `a!` is false and vice versa.
`==` // Checks if a value or condition is equal to another value or condition, 
    i.e. if so it outputs true, otherwise it outputs false.
`!=` // Checks if a value or condition is NOT equal to another value or condition.
    This is the logical equivalent of an `xor` operator, Zig does not have an `xor` operator.
`<` // less than | `<=` // less than or equal to | `>` // greater than | `>=` // greater than or equal to
    If you took a math class you would probably be fammiliar with these terms.

# Operator: Assignment
These are your is equal to or ```=```, operators. They are used when defining a variable or constant.
Ex: ```var x = 1```
variables and constants will be explained later.
There are multiple types of Assignment Operators, I will go through all of them down below.

I'll briefly explain the Assignment Syntax, then begin. (I'll go into more detail later.)
`(const | var) Identifier(: TypeExpr)? ByteAlign? AddrSpace? LinkSection?`
`(const | var)` means you must either have const or var, at the beginning of assignment.
Const, means constant, used to declare a constant unchanging value.
Var, means variable, used to declare a changing value.
I will be using the word variable to talk about both const and var, confusing I know, sorry not sorry.

The Identifier is a string of characters. i.e. `Chicken`
There are a few rules for what can and cannot be an Identifier, Thats for later though.

This section `(: TypeExpr)?`
the `()?` Indicates that TypeExpr is optional, most of the time TypeExpr is not optional. (I'll get into why later)
TypeExpr is basically the type of value that you are using. 
If you want the value to be an 8bit unsigned integer, thats u8. (u8 Has no negative numbers, only positive.)
If you want it to be signed thats i8. (about half of i8 is negative)
When adding a type to an identifier you must add a colon before it.
Ex: `const chicken: u8 = 1`

# Operator: Assignment: Plain
This is reserved soley for the ```=``` operator.
Simply used to define that something is equal to something else.

# Operator: Assignment: Normal Arithmetic
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

# Operator: Wrapping Arithmetic
You may note that the title doesn't contain "assignment" and that is because I need to explain a few more details before continuing. 
If you have been in the programming world for a time, you may have heard about values in programs that somehow go from 0 to 255, because 1 was subtracted from zero.

This is known as wrapping, 8 byte integers can only store 255 digits, going above that number is impossible, so to avoid overflow and causing the program to crash. 
They would have the variable wrap around, going from 0 to 255 or 255 to 0.

The operator for wrapping, is `%` but it must be added onto another operator in order for it to have this effect.

`+%` // if an addition would overflow, it wraps around.
`-%` // if a subtraction would overflow, it wraps around.
`*%` // if a multiplication would overflow, it wraps around.

Note that division can not go below or above the integer limit, (unless your dividing by a fraction, in which case just multiply instead) and remainders can never go above or below the integer limit so they are ignored.
Stupidly enough, you can wrap negations. i.e.

`-%a` // you may think this is completely useless, but i8, has 128 negative numbers and 127 positive numbers, so if -128 were to be negated, it would cause an integer overflow. so there is one tiny usecase for it.
I am not going to ignore anything out of princepal, sorry if you don't care about info like this.

# Operator: Assignment: Wrapping Arithmetic
This is pretty self explanatory.

`a +%= b` // `a = a +% b` adds `b` to `a` and wraps if the integer would overflow.
`a -%= b` // `a = a -% b` subtracts `b` from `a` and wraps if the integer would overflow.
`a *%= b` // `a = a *% b` multiplies `b` to `a` and wraps if the integer would overflow.

# Operator: Saturation Arithmetic
The bigger brother to Wrapping, if having a value reset once it reaches the integer limit was annoying, you can use the saturation operators to have the value do nothing instead.
In other words, if you were to add 1 to 255, it would stay 255, and if you were to subtract 1 from 0, it would stay zero.

`+|` // if an addition would overflow, it caps instead.
`-|` // if a subtraction would overflow, it caps instead.
`*|` // if a multiplication would overflow, it caps instead.

Unfortunately Saturation doesn't have a negation operator built in to zig, mainly because of how useless it would be.
If you want to annoy the devs, asking them to add that would be a great place to start.

# Operator: Assignment: Saturation Arithmetic
Another self explanatory section.

`a +|= b` // `a = a +| b` adds `b` to `a` and caps if the integer would overflow.
`a -|= b` // `a = a -| b` subtracts `b` from `a` and caps if the integer would overflow.
`a *|= b` // `a = a *| b` multiplies `b` to `a` and caps if the integer would overflow.

# Operator: Bitwise Boolean
These operators are for very low level computations. They quite literally operate on a binary level, aka zeros and ones.
That isn't to say they are meant for kernel or os level code.
Bitwise operators can be used to do multiple `and` `or` or `xor` opertations all at once. Much faster than doing multiple normal `and` `or` or `xor` operations.
Note, do not use Bitwise Operators like Normal Operators, they are much different.

`&` \\ bitwise `and` operator, if the bit on both are one, the bit is set to one. 
`|` \\ bitwise `or` operator, if the bit on either or both is one, the bit is set to one.
`^` \\ bitwise `xor` operator. if the bit on either only is one, the bit is set to one.
`~a` \\ bitwise negation operator. for every bit of `a`, if bit is 1, bit becomes zero and vice versa.
    Ex: ~0b0101 = 0b1010


# Small demonstration of Bitwise Boolean Operators:
In Zig, binary numbers can be written as `0b00`
In this case `0b` literally just means binary, and the `00` means that it is a two bit binary number.
`0b000` is a three bit binary number, etc.

Because binary is a base-2 number system, the previous number is always 2 times bigger than the next number.
`0b10` is equal to `2` while `0b01` is equal to `1`
`0b100 = 4` and `0b11 = 3` and so on.
if we did
```
const a = 0b01; // this is equal to one
const b = 0b10; // this is equal to two
const c = a | b;
```
Because `|` allows either to be 1, `c = 0b11` which is 3.

This starts to get pretty confusing at first, once we use bigger numbers.
```
const a = 0b10001; //this is equal to 17
const b = 0b10011; //this is equal to 19
const c = a | b;
```
Because most of the 0 are zero on both sides, they don't change. 
Meaning `c = 0b10011` which is 19.

Although I said ealier that bitwise operators operate on a binary level, you can actually use any type of number with them.
```
const a = 15; //this is equal to 0b1111
const b = 12; //this is equal to 0b1100
const c = a | b;
```
This will result in, `c = 15` because bitwise operators only care about the binary value of a number.

Why this is important:
lets say you wanted a function to run only if four conditions were true.
How would you write it without making a massive chain of ands.
```
const a = 0b01; // 1
const b = 0b10; // 2
const c = a | b; // 3
const d = 0b01; //1
const e = 0b10; // 2
const f = d | e; // 3
if ((c & f) == 3) {run code} // c and f both have the exact same bits, so neither change.
```
for `(c & f) == 3` using `0b11` instead of `3` also works.
With this method you can write a long chain of conditional arguments fairly simply.
This is also useful for confusing the crap out of Zig programmers who don't know core details like this.
i.e. 
```
const a = 7; //0b0111
const b = 9; //0b1001
if ((a | b) == 15) {code here} //0b1111 = 15
```
if you wrote something like this, without adding the bit number comments, they would probably have no idea what is happening. 

# Operator: Assignment: Bitwise Boolean
Very straightforward.

`a &= b` // `a = a & b` 
`a |= b` // `a = a | b`
`a ^= b` // `a = a ^ b`

# Operator: Bitshift
These operators can shift bits left or right.
Example: 0b11 >> 1 = 0b01 // 3 >> 1 = 1
The bit is shifted to the right, which means that the value decreases by a power of 2. 
If a bit is shifted to the right beyond the integerlimit, it is removed. 
but if it is shifted left beyond the integer limit, there is an overflow.

`a >> b` Right Bitshift, a decreases by a factor of b, per bit
`a<< b` Left Bitshift, a increases by a factor of b, per bit.

`3 << 1 = 6` // `0b0011` becomes `0b0110` 
`3 << 2 = 12` // `0b0011` becomes ` 0b1100`

For both left and right shift, Zig requires that b is known at comptime, or that the type of b is equal to Log2(Number of bits in a)
The reason for this is because, shifting too far left will cause an overflow, so Zig prevents this by limiting the byte size of b, or by requiring it to be known during comptime.

Worthless Rant: {
This is pretty silly to me because, shifting right will not cause a overflow (Maybe if you shift right a negative amount, but that pretty stupid)
So this restriction shouldn't be neccesary for right shifting.
Also if a was something like 0b1000, shifting it one to the left would cause it to overflow anyway. the restriction doesn't really remove any foot guns.

They have Saturated Left BitShift, from my perspective, they might aswell just force that instead.
But, I am definitely not a 10x programmer and a large amount of Zig's Documentation is nondescript, most of my information is coming from third party sources. 
Maybe they have a reason for doing this, I couldn't see one though.
}
Log2(Number of bites in a) looks pretty complicated, but it is simple enough in practice.
For example, if you were to put Log2(8) into your calculator, you would get aproximately 1.24011

This is not what the Devs mean by Log2(Number of bites in a)
What they actually meant is,
`2` `to the power of` `b's type` `is equal to` `the number of bits in a`
Because of that we know that the number if bits in `a` is 8, then `b's type` is 3
because 2 to the power of 3, is equal to 8.
i.e. 2 * 2 * 2 = 8

This is important because the maximum number a 3 bit digit can store is 7. i.e. 0b111 = 7
And the maximum number of bit shifts that can be done to an 8 bit binary number is 7.
i.e. 0b00000001 << 7 = 0b10000000

So in practice this would look like.
```
const a = 0b00000001
const b: u3 = 5;
const c = a << b // 0b00100000 = 32
```
I want to point out that to a developer unfamilliar with Zig, this is a very cursed solution.
Because Most programming languages dont allow you to set a number to an arbitrary number of bits like 3.
They must be a power of 2. i.e. 2, 4, 8, 16, 32 etc.

I dont really understand the purpose of bitshifting so i will just give you a potential usage then move on.

Say for example you didn't want to make a bunch of variables when using bitwise boolean. And you wanted to simplify that code further.
You could use bitshifting to create something like this.
```
var a: u4 = 0 // variable is declared outside of function
fn function() { //ignore this for now
a <<= 1; // shifts a to the left,
a += 1  // adds 1 to a
} 
```
(I haven't covered functions yet so that will have to wait.)
This code shifts `a` to the left then adds `1`.  // 0b0001
When the function is called for a second time, 
`a` gets shifted by 1 // 0b0010
then 1 is added to `a` // 0b0011
Ex: first iteration: 0b0001, second iteration: 0b0011, third iteration: 0b0111, fourth iteration: 0b1111 and so on.

# Operator: Assignment: Bitshift


