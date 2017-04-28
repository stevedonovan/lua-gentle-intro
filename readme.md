## Values and Expressions

Lua can be started from the command-line and used as a calculator:

```lua
$> lua53
Lua 5.3.0  Copyright (C) 1994-2014 Lua.org, PUC-Rio
> 2*5 + 3
13
> 2*(5 + 2)
14
> 5^2 + 1
26.0
```

Regular numbers are the simplest kind of _value_, and can be combined into
_expressions_ using _operators_.  `+`,`*` and `^` (meaning 'to the power of') are common
arithmetic operators.

It would not be a very good scientific calculator if the standard mathematical functions
were not available:

```lua
> math.sin(0.4) + 1
1.3894183423087
```

This function takes a number value, and calculates the sine of that number in radians.
The conversion from degrees is straightforward, so we will calculate it and give that
value a name:

```lua
> d2r = math.pi/180
> math.sin(90*d2r)
1.0
```

`d2r` is called a _variable_, and giving it a value is called _assignment_. You
should not pronounce `=` "equals" but rather call it "becomes" or something like that; it
does not mean checking for equality!  This is where standard programming notation
is different from mathematics. Otherwise this would make no sense:

```lua
> x = 1
> x = x + 1
> x
2
```

Functions are a kind of value as well, so you can define shortcuts:

```lua
> s = math.sin
> c = math.cos
> s(x)^2 + c(x)^2
1.0
```

Comparisons between values involve another kind of expression involving
_conditional_ operators. Note `==` here does mean 'test for equality':

```lua
> 10 > 12
false
> 42 == 42
true
> 5 < 7
true

```
These are often called 'boolean' operators after the English logician George Boole,
and the resulting value is called 'boolean' in Lua.

## Tables

_Arrays_ are an important kind of value in programming languages. They store
a sequence of values. They have a size which is returned by the `#` operator,
and can be _indexed_ using a integer value:

```lua
> arr = {10,20,30}
> #arr
3
> arr[1]
10
> arr[3]
30
> arr[20]
nil
```

Note that Lua arrays go from 1 to the length of the array. It is _not_ an error
to index outside that range - you will simply get `nil`. This is a special value
meaning 'no value'. This is also the value of an _undefined_ variable:

```lua
> frodo
nil
```

_Maps_ (often called _associative arrays_ or _lookup tables_) are also indexed but by arbitrary
values. They consist of _key/value_ pairs.

```lua
> map = {one=1,two=2,three=3}
> map['one']
1
> map.one
1
> map.four
nil
> map.four = 4
> map.four
4
```

Note an important feature of Lua maps - you can use `[]` as before (except with
strings in this case) but `map.key` is exactly the same as `map['key']`. And if
the key is not present in the map, then the result is (again) `nil`.

In Lua, both maps and arrays are called _tables_.  We used `math.sin` to calculate
the sine of a number, and `math` is **just a table** of functions. Generally
you would use a table either as an array or a map, but they can be mixed.

Tables are a distinct _type_, which is the proper term for 'kind of value'.
It is an error to pass the wrong type to a
function expecting a number:

```
> math.sin(map)
stdin:1: bad argument #1 to 'sin' (number expected, got table)
stack traceback:
	[C]: in function 'math.sin'
	stdin:1: in main chunk
	[C]: in ?
```

## Strings

Strings are quoted text like "hello".  You can quote with either double or single
quotes, Lua does not care. The length operator `#` will give the size in bytes:

```lua
> s = 'hello'
> #s
5
``

By _byte_ I mean a 8-bit value (the smallest addressable unit of memory); for
ASCII text, a byte _is_ a character, but in general a character may be several bytes.
Unlike tables, you cannot index a string. You cannot modify a string either, it
is said that they are _immutable_.

Strings are a distinct type of value, and so you get a type error when using
them in the wrong way. But if they represent a number, then `tonumber` will convert
that string into a number.

```lua
> math.sin(s)
stdin:1: bad argument #1 to 'sin' (number expected, got string)
...
> s = "42"
> tonumber(s)
42
```
Like real-world strings, they can be combined or _concatenated_ using `..`. Or
you can join a table of strings and numbers together using `table.concat`:

```lua
> hello = "hello"
> world = "world"
> hello.." "..world
hello world
>  table.concat({"hello","world",42},' ')
hello world 42
```
You can extract a part of a string using `string.sub`:

```rust
> string.sub(hello,1,3)
hel
```
The original string is not changed in any way, but a 'slice' is copied to a new string.


## What is Programming?

Up to now, Lua is  a convenient calculator. The concepts of value, expression,
variable and assignment are important programming concepts, but we haven't started
programming yet!

  - defining our own functions
  - doing different things based on some condition
  - repeating things until some condition is true

Now it's time to introduce _statements_. An expression always has a value; an
assignment like `x = 2` is a statement and has no value.

To conditionally perform some action, use the `if` statement:

```lua
> x = 2
> if x > 1 then print("ok") end
ok
```

These statements are generally `if EXPRESSION then STATEMENTS end`. The words
`if`,`then` and `end` are _keywords_, and these are reserved words in Lua. You
cannot use keywords for variables.

The `if` statement can also have an `else` clause:

```lua
> if x > 1 then print("ok") else print("nope") end
```

To repeat things with a range of values, use the `for` statement:

```lua
> for i = 1,5 do print(i) end
1
2
3
4
5
```
At this point, it's time to start writing programs. Type this into a text editor:

```lua
-- a comment, ignored by Lua. This is 'hello.lua'
for i = 1,5 do
   print('hello',i)
end
```

and then

```lua
$> lua53 hello.lua
hello	1
hello	2
hello	3
hello	4
hello	5
```

It's important to format code nicely, because it makes it easier to read. Anything
between the `do` and the `end` should be indented. Tabs, spaces, it doesn't matter: just
be consistent. Use a [text editor](http://lua-users.org/wiki/LuaEditorSupport)
that understands this (I'm personally a fan of [Geany](http://www.geany.org/)).

Can now add an `if` statement:

```lua
-- goodbye.lua
for i = 1,5 do
   if i > 2 then
      print('goodbye',i)
   else
      print('hello',i)
   end
end
```

which gives us:

```
hello	1
hello	2
goodbye	3
goodbye	4
goodbye	5
```

Again, indent each statement properly. You _could_ put this all on one line but
people will hate you.

Every Lua program has a predefined table called 'arg' containing the
_command-line arguments_ passed to the program:

```lua
$> cat args.lua
-- args.lua
for i = 1,#arg do
   print(i,arg[i])
end
$> lua53 args.lua one 42 'hello dolly'
1	one
2	42
3	hello dolly
```

You can use `tonumber` to convert the first argument to a number. We will use
`io.write` which works like `print` except it doesn't end the line:

```lua
$> cat for.lua
-- for.lua
n = tonumber(arg[1])
for i = 1,n do
     io.write('hi ')
end
print()
$> lua53 for.lua 5
hi hi hi hi hi
```

There's another function in the `io` table which allows us to easily loop over
all the lines in a file. We assign 1 to `i`, and then _increment_ `i` by one each time.


```lua
-- lines.lua
file = arg[1]
i = 1
for line in io.lines(file) do
   print(i,line)
   i = i + 1
end
```

And the output:

```
$> lua53 lines.lua lines.lua
1	-- lines.lua
2	file = arg[1]
3	i = 1
4	for line in io.lines(file) do
5	   print(i,line)
6	   i = i + 1
7	end
```

This is other form of the `for` statement, which works with _iterators_.
Here's another iterator function `pairs`. It gives all the key/value pairs in
a table - in this case the predefined table `math`:

```lua
> for k,v in pairs(math) do print(k,v) end
min	function: 0x41e1d0
tan	function: 0x41dfb0
modf	function: 0x41e5d0
maxinteger	9223372036854775807
asin	function: 0x41e4a0
ceil	function: 0x41e580
rad	function: 0x41df50
random	function: 0x41e080
mininteger	-9223372036854775808
floor	function: 0x41e690
huge	inf
max	function: 0x41e260
sqrt	function: 0x41dfe0
pi	3.1415926535898
tointeger	function: 0x41e6e0
atan	function: 0x41e440
abs	function: 0x41e760
sin	function: 0x41e020
acos	function: 0x41e4d0
randomseed	function: 0x41e050
log	function: 0x41e2f0
ult	function: 0x41e3a0
cos	function: 0x41e410
fmod	function: 0x41e7d0
type	function: 0x41e500
deg	function: 0x41df80
exp	function: 0x41e3e0
```

(For more information, go to [Mathematical Functions](https://www.lua.org/manual/5.3/manual.html#6.7)
in the Lua manual.)

## Defining Functions

In mathematics, simple functions take a value from a set of all possible inputs,
the _domain_ and output a value from a set called its _range_. Or, we _apply_ a
function to any value from the _domain_ and get a value from the _range_.
So the simplest form of 'sine' goes from all real numbers to the range (-1,+1).

In programming we _call_ a function and _pass_ it an _argument_. It will then
_return_ a value. These words may seem strange (they are not how we talk about
mathematical functions) and come from the early days of programming, when
'calling' a 'subprogram' involved saving your position in memory and jumping to
a new position. The subprogram would do its work, and then would 'return' to
the saved original position.

The keyord `function` defines a function in Lua; it is followed by the name,
and then the names of arguments. Like `for` and `if`, any number
of statements may appear afterwards ending with `end`. The `return` statement
passes the value back explicitly:

```lua
-- sqr.lua
function sqr(x)
    print('x is',x)
    return x * x
end

y = sqr(10)
print('y is',y)
print('x is',x)
-- output
-- x is 10
-- y is 100
-- x is nil
```

The value 10 is assigned to the variable `x` in the function `sqr`, and we get
back 100.

Note that `x` is `nil` outside the function! That's to say, it's undefined.

Consider these lines in the Lua Prompt:

```lua
> sum = 0
> for i = 1,50 do sum = sum + i end
> sum
1275
> = i
nil
```
Again, `i` is only defined inside the loop, between the `do` and `end`.

These are examples of _local_ variables, which are only visible in a particular
part of a program; _global_ variables are visible everywhere.  Life without local
variables would be a mess, honestly. Consider this more readable version:

```lua
function sum_numbers (n)
    sum = 0
    for i = 1,n do
        sum = sum + i
    end
    return sum
end
```

It does the job, sure, but every time we call it, it updates a global variable
called `sum`.  Now there are only so many meaningful names you can give to
variables (without `stupid_long_names_creating_confusion`) and you do not want
the insides of functions 'escaping' like this!
Setting the global variable `sum` is a _side effect_ and is
something to be avoided.

So this is better:

```lua
function sum_numbers (n)
    local sum = 0
    for i = 1,n do
        sum = sum + i
    end
    return sum
end
```
The keyword `local` _declares_ a variable, and usually also gives it an initial
value.  `sum` is now local to `sum_numbers` (like the argument `n`) and we have
a better behaved function. I encourage you to use `local` declarations as much
as possible, even in little programs.

Lua functions may have more than one argument, like `print` - it can take any
number of arguments.  Lua functions may also return _more than one value_. Say
we want the position of some text within a larger body of text - or (properly)
the position of a _substring_ in a _string_:

```lua
> s = "hello dolly"
> string.find(s,"doll")
7	10
> string.sub(s,7,10)
doll
```

`string.find` returns two values: the start _and_ the end position. You can
take these positions and get the substring back using `string.sub`. The
end position is just after the end of the match; all positions are from one.
A common task is to find if a string starts with another string, which we can now
define as

```lua
function starts_with(str,sub)
    return string.find(str,sub) == 0
end

local text = 'hello dolly you're so fine'
local hello = 'hello'

if starts_with(text,hello) then
    print('goodbye')
end
```

In this comparison, the second return value is ignored.

Here is a function that returns both the minimum and the maximum value
of a table of values:

```lua
-- minmax.lua
function minmax(values)
   local vmin = math.huge
   local vmax = -math.huge
   for i = 1,#values do
      vmin = math.min(vmin,values[i])
      vmax = math.max(vmax,values[i])
   end
   return vmin, vmax
end

local values = {10,2,5,20,3,12}
local min,max = minmax(values)
print(min, max)
-- 2    20
```
I've used the constant `math.huge`, which is guaranteed to be larger than any
other number, and also the `min` and `max` functions.  Please note how we
can assign multiple values to several variables at once. This also means
that you can say `local x, y = 1.0, 2.0`.

There are actually two variables called `values` in this program, one local
to the function and the other to the so-called _main chunk_. They are completely
unrelated.

## Operations on Tables

There's no built-in way to print out tables in Lua, but it's easy to write a
function that will turn an array of strings and numbers into a human-readable string:

```lua
-- show.lua
function showa (arr)
    return '{'..table.concat(arr,',')..'}'
end
```
And then you can load it into the Lua prompt using the 'l' (for 'load') flag.
Note that here we don't use the extension '.lua'!

```lua
~/lua/luabuild$ lua53 -l show
Lua 5.3.0  Copyright (C) 1994-2014 Lua.org, PUC-Rio
> arr = {10,20,30,40}
> showa(arr)
{10,20,30,40}
>
```
We can now demonstrate some common operations on tables. We can insert a new value
into a table using a position (which starts from one); values can be removed.
There is a second form of `table.insert` which takes two arguments, not three - it
will _append_ the value to the end of the table.

```lua
> table.insert(arr,1,11)
> showa(arr)
{11,10,20,30,40}
> table.remove(arr,2)
10
> showa(arr)
{11,20,30,40}
> table.insert(arr,50)
> showa(arr)
{11,20,30,40,50}
```
You can also create an array-like table by setting the values using an index:

```lua
> seq = {}
> for i = 1,10 do
>>  seq[i] = i*i
>> end
> showa(seq)
{1,4,9,16,25,36,49,64,81,100}
> #seq
10
> seq[#seq + 1] = 200
> showa(seq)
{1,4,9,16,25,36,49,64,81,100,200}
```

There is no builtin way to find things in arrays either, but it's easy to write.
Just add the following function to `show.lua`:

```lua
function finda (arr, value)
    for i = 1,#arr do
        if arr[i] == value then
            return i
        end
    end
    return nil
end
```

It's perfectly fine to _return early_ from a function - we've found our position, let's
return what we found. If we do **not** find the value, then at the end of the `for`
loop we return `nil` explicitly.

 We have to restart with `lua53 -l show` after this change:

```lua
> arr = {10,20,30,40,50}
> finda(arr,30)
3
> finda(arr,60)
nil
> if finda(arr,70) then
>>  print('found')
>> else
>>  print('not found')
>> end
not found
```
Note that we can directly test whether `finda` returned `nil` or not. The **only** two
values that are considered 'false' are the boolean `false` and the value `nil`.
And that's why I returned `nil`, rather than some out-of-range number like 0.







