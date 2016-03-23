## Defining a function
A function definition is just a regular variable definition where the value given to the variable happens to be a function
A function can have multiple parameters or no parameters at all. In the following example, `makeNoise` does not list any parameter names

```html
<script>
var makeNoise = function() { 
	console.log("Pling!");
};

makeNoise();
// → Pling!
</script>
```

Some functions produce a value, such as power and square, and some don’t, such as makeNoise, which produces only a side effect. A return statement determines the value the function returns. When control comes across such a statement, it immediately jumps out of the current function and gives the returned value to the code that called the “function. The return keyword without an expression after it will cause the function to return undefined 


```html
<script>
var power = function(base, exponent) {
  var result = 1;
  for (var count = 0; count < exponent; count++)
    result *= base;
  return result;
};

console.log(power(2, 10));
// → 1024” 
</script>
```

#### Parameters and scopes
The parameters to a function behave like regular variables, but their initial values are given by the caller of the function, not the code in the function itself.

An important property of functions is that the variables created inside of them, including their parameters, are local to the function. This means, for example, that the result variable in the power example will be newly created every time the function is called, and these separate incarnations do not interfere with each other. “This “localness” of variables applies only to the parameters and to variables declared with the var keyword inside the function body. Variables declared outside of any function are called global, because they are visible throughout the program. It is possible to access such variables from inside a function, as long as you haven’t declared a local variable with the same name.


#### Nested scope
JavaScript distinguishes not just between global and local variables. Functions can be created inside other functions, producing several degrees of locality.

For example, this rather nonsensical function has two functions inside of it:

```html
<script>
var landscape = function() {
  var result = "";
  var flat = function(size) {
    for (var count = 0; count < size; count++)
      result += "_";
  };
  var mountain = function(size) {
    result += "/";
    for (var count = 0; count < size; count++)
      result += "'";
    result += "\\";
  };
  flat(3);
  mountain(4);
  flat(6);
  mountain(1);
  flat(1);
  return result;
};

console.log(landscape());
// → ___/''''\______/'\_”
</script>
```
## Functions as values

Function variables usually simply act as names for a specific piece of the program. Such a variable is defined once and never changed. This makes it easy to start confusing the function and its name.

But the two are different. A function value can do all the things that other values can do—you can use it in arbitrary expressions, not just call it. It is possible to store a function value in a new place, pass it as an argument to a function, and so on. Similarly, a variable that holds a function is still just a regular variable and can be assigned a new value, like so:

```html
<script>
var launchMissiles = function(value) {
  missileSystem.launch("now");
};
if (safeMode)
  launchMissiles = function(value) {/* do nothing */};
</script>
```

#### Function Declaration

Function declarations are not part of the regular top-to-bottom flow of control. They are conceptually moved to the top of their scope and can be used by all the code in that scope. This is sometimes useful because it gives us the freedom to order code in a way that seems meaningful, without worrying about having to define all functions above their first use.

```html
<script>
console.log("The future says:", future());

function future() {
  return "We STILL have no flying cars.";
}
</script>
```
This code works, even though the function is defined below the code that uses it.

## Optional Arguments
The following code is allowed and executes without any problem:
```html
<script>
alert("Hello", "Good Evening", "How do you do?");
</script>
```
The function alert officially accepts only one argument. Yet when you call it like this, it doesn’t complain. It simply ignores the other arguments and shows you “Hello”.

JavaScript is extremely broad-minded about the number of arguments you pass to a function. If you pass too many, the extra ones are ignored. If you pass too few, the missing parameters simply get assigned the value undefined.
The downside of this is that it is possible—likely, even—that you’ll accidentally pass the wrong number of arguments to functions and no one will tell you about it.
The upside is that this behavior can be used to have a function take “optional” arguments. For example, the following version of power can be called either with two arguments or with a single argument, in which case the exponent is assumed to be two, and the function behaves like square.

```html
<script>
function power(base, exponent) {
  if (exponent == undefined)
    exponent = 2;
  var result = 1;
  for (var count = 0; count < exponent; count++)
    result *= base;
  return result;
}
console.log(power(4));
// → 16”
</script>
```


## Closure

The ability to treat functions as values, combined with the fact that local variables are “re-created” every time a function is called, brings up an interesting question. What happens to local variables when the function call that created them is no longer active?
The following code shows an example of this. It de nes a function, wrapValue, which creates a local variable. It then returns a function that accesses and returns this local variable.

```html
<script>
function wrapValue(n) {
  var localVariable = n;
  return function() { 
    return localVariable;
  };
}
var wrap1 = wrapValue(1); 
var wrap2 = wrapValue(2); 
console.log(wrap1());
// → 1 
console.log(wrap2());
// → 2
</script>
```

This is allowed and works as you’d hope—the variable can still be accessed. In fact, multiple instances of the variable can be alive at the same time, which is another good illustration of the concept that local variables really are re-created for every call—di erent calls can’t trample on one another’s local variables.
This feature—being able to reference a speci c instance of local vari- ables in an enclosing function—is called closure. A function that “closes over” some local variables is called a closure. This behavior not only frees you from having to worry about lifetimes of variables but also allows for some creative use of function values.
With a slight change, we can turn the previous example into a way to create functions that multiply by an arbitrary amount.

```html
<script>
function multiplier(factor) { 
  return function(number) { 
    return number * factor;
  }; 
}
var twice = multiplier(2); 
console.log(twice(5));
// → 10
</script>
```


The explicit localVariable from the wrapValue example isn’t needed since a parameter is itself a local variable.
Thinking about programs like this takes some practice. A good mental model is to think of the function keyword as “freezing” the code in its body and wrapping it into a package (the function value). So when you read return function(...){...}, think of it as returning a handle to a piece of computation, frozen for later use.
In the example, multiplier returns a frozen chunk of code that gets stored in the twice variable. The last line then calls the value in this variable, causing the frozen code (return number * factor;) to be activated. It still has access to the factor variable from the multiplier call that created it, and in addition it gets access to the argument passed when unfreezing it, 5, through its number parameter.

## Recursion

It is perfectly okay for a function to call itself, as long as it takes care not to overflow the stack. A function that calls itself is called recursive. Recursion allows some functions to be written in a different style.

```html
<script>
function power(base, exponent) {
  if (exponent == 0)
    return 1;
  else
    return base * power(base, exponent - 1);
}

console.log(power(2, 3));
// → 10
</script>
```

But this implementation has one important problem: in typical JavaScript implementations, it’s about 10 times slower than the looping version.
But recursion is not always just a less-efficient alternative to looping. Some problems are much easier to solve with recursion than with loops.
Consider this puzzle: by starting from the number 1 and repeatedly either adding 5 or multiplying by 3, an infinite amount of new numbers can be produced. How would you write a function that, given a number, tries to find a sequence of such additions and multiplications that produce that number?


```html
<script>
function findSolution(target) {
  function find(start, history) {
    if (start == target)
      return history;
    else if (start > target)
      return null;
    else
      return find(start + 5, "(" + history + " + 5)") ||
             find(start * 3, "(" + history + " * 3)");
  }
  return find(1, "1");
}

console.log(findSolution(24));
// → (((1 * 3) + 5) * 3)”
</script>
```
Note that this program doesn’t necessarily find the shortest sequence of operations. It is satisfied when it finds any sequence at all.
The inner function find does the actual recursing. It takes two arguments—the current number and a string that records how we reached this number—and returns either a string that shows how to get to the target or null.
To better understand how this function produces the effect we’re looking for, let’s look at all the calls to find that are made when searching for a solution for the number 13.

## Growing Functions

There are two more or less natural ways for functions to be introduced into programs.
The first is that you find yourself writing very similar code multiple times. We want to avoid doing that since having more code means more space for mistakes to hide and more material to read for people trying to understand the program. So we take the repeated functionality, find a good name for it, and put it into a function.
The second way is that you find you need some functionality that you haven’t written yet and that sounds like it deserves its own function.

We want to write a program that prints two numbers, the numbers of cows and chickens on a farm, with the words Cows and Chickens after them, and zeros padded before both numbers so that they are always three digits long.

```html
007 Cows
011 Chickens
```

That clearly asks for a function of two arguments

Let’s get coding.

```html
<script>
function printFarmInventory(cows, chickens) {
  var cowString = String(cows);
  while (cowString.length < 3)
    cowString = "0" + cowString;
  console.log(cowString + " Cows");
  var chickenString = String(chickens);
  while (chickenString.length < 3)
    chickenString = "0" + chickenString;
  console.log(chickenString + " Chickens");
}
printFarmInventory(7, 11);
</script>
```

Adding .length after a string value will give us the length of that string. Thus, the while loops keep adding zeros in front of the number strings until they are at least three characters long.

Mission accomplished! But just as we are about to send the farmer the code (along with a hefty invoice, of course), he calls and tells us he’s also started keeping pigs, and couldn’t we please extend the software to also print pigs?

We sure can. But just as we’re in the process of copying and pasting those four lines one more time, we stop and reconsider. There has to be a better way. Here’s a first attempt:

```html
<script>
function printZeroPaddedWithLabel(number, label) {
  var numberString = String(number);
  while (numberString.length < 3)
    numberString = "0" + numberString;
  console.log(numberString + " " + label);
}
</script>
```


```html
<script>
function printFarmInventory(cows, chickens, pigs) {
  printZeroPaddedWithLabel(cows, "Cows");
  printZeroPaddedWithLabel(chickens, "Chickens");
  printZeroPaddedWithLabel(pigs, "Pigs");
}

printFarmInventory(7, 11, 3);

</script>
```

It works! But that name, printZeroPaddedWithLabel, Label is a little awkward. It conflates three things—printing, zero-padding, and adding a label—into a single function.

Instead of lifting out the repeated part of our program wholesale, let’s try to pick out a single concept.


```html
<script>
function zeroPad(number, width) {
  var string = String(number);
  while (string.length < width)
    string = "0" + string;
  return string;
}


function printFarmInventory(cows, chickens, pigs) {
  console.log(zeroPad(cows, 3) + " Cows");
  console.log(zeroPad(chickens, 3) + " Chickens");
  console.log(zeroPad(pigs, 3) + " Pigs");
}

printFarmInventory(7, 16, 3);

</script>
```

A function with a nice, obvious name like zeroPad makes it easier for someone who reads the code to figure out what it does. And it is useful in more situations than just this specific program. For example, you could use it to help print nicely aligned tables of numbers.”














