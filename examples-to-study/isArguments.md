# isArguments: [GitHub](https://github.com/lodash/lodash/blob/master/isArguments.js), [Docs](https://lodash.com/docs/#isArguments)

A developer might use this function if they want to check if something is the default "Arguments" object inside of a function execution context.  This is useful if they are trying to use someone else's library, or if they want to make sure their library is safe from mis-use - can never be too careful.

isArgument: Function
* _Args_: 1
  1. value: Any type, that's the point of this function
* _Returns_: Boolean
  * Returns `true` if `value` is an `arguments` object, else `false`.
* _Behavior_: Checks if `value` is likely an `arguments` object.

### Index:
* [Dependencies](#dependencies)
* [Behavior](#behavior)
  * [Input Analysis](#input-analysis)
  * [Test Cases](#test-cases)
* [Implementation](#implementation)
  * [Original Code](#original-code)
  * [Reconstruction](#reconstruction)
* [Notes](#notes)

___

## Dependencies

```js
import getTag from './.internal/getTag.js'
import isObjectLike from './isObjectLike'
```

__getTag__: Not entirely sure what this does and it's not listed in the Documentation, but there was a comment in the file that said this -> "Gets the `toStringTag` of `value`.".  By best guess is that it helps identify very specifically what type of object we're dealing with.  This has one dependency (BaseGetTag), which I did study more closely.  You can check that out in this repo.

__isObjectLike__: Check out the folder where I studied this code.



[TOP](#index)

___

## Behavior

This function does not have very complex behavior, it's just a utility for use in more interesting code.  It's a fancy & specific type check.

### Test Cases

```js
isArguments(function() { return arguments }())
// => true

isArguments([1, 2, 3])
// => false
```



### Test Case Analysis

The most important consideration for the input is whether or not it is an Arguments object.  After that you could either do a lot of conditional checks to rule out invalid types, but it's more straight forward to just check for the right type. (Besides that it might be worth considering whether or not it is valid JavaScirpt.)

These test cases are few and basic, but they illustrate the main point of this function.  For explaining the behavior they are adequate.  But to be convinced that the function works as expected I would want to see some more fringe cases:  What if the argument has all the same properties as an Arguments object?  Is there a strange argument that will break the function and cause it to return the wrong answer?  What if I pass in too many arguments? or no arguments?w



[TOP](#index)

___

## Implementation 

They worked around most of the difficulties in their solution by requiring other utility functions from within their library. 

### Original Code

```js
function isArguments(value) {
  return isObjectLike(value) && getTag(value) == '[object Arguments]'
}
```

_Strategy:_

Their strategy was to decompose the problem into smaller pieces and to DRY (Don't Repeat Yourself).  They broke the problem of determining if something is an Arguments into checking if it's ObjectLike, and checking it's Tag.  Since they already had utilities that do this, they just reused those functions.


_Language Features:_

They did not use many challenging language features, just a Boolean Operator and a Comparison.  All the tricky stuff is hidden in the two other functions the reused.

### Reconstruction

```js
function isArguments(value) {
	let returner = false;

	if ( value.hasOwnProperty('callee') ) {
		if ( value.hasOwnProperty('length')) {
			returner = true;
		}
	}

	return returner;
}
```

_Strategy:_
I chose to go more superficial, my strategy was just to check if the argument has the predefined properties an Arguments object should have.  I wanted to also check their types, but I'm not allowed to in strict mode since I can't access them directly.  

_Language Features:_
* "Object.prototype.hasOwnProperty('prop_name')" -> This checks if an object has a given property.

_Comparison:_
Overall this is a very weak strategy.  It would be far too easy to trick my Function.  In the future I will use theirs, not mine.  They rely on much more robust and deeper checks.


[TOP](#index)

___

## Notes

Things I learned studying this problem:
* [Arguments Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)
* An interesting failed attempt taught me you can't access an Argument Object's native properties directly in string mode:
```js
function isArguments(value) {
	let returner = false;

	if ( typeof value.callee == "Function" ) {
		if ( typeof value.length == "Number") {
			if ( typeof value['Symbol(Symbol.iterator)'] == "Function" ) {
				returner = true;
			}
		}
	}

	return returner;
}
```
* And for some reason "Symbol(Symbol.iterator)" shows up in the inspector but not in PythonTutor or the docs.  Blasted runtimes.

New vocabulary:


Things I struggled with:


Lessons to apply for next time:


[TOP](#index)


___
___
### <a href="http://elewa.education/blog" target="_blank"><img src="https://user-images.githubusercontent.com/18554853/34921062-506450ae-f97d-11e7-875f-6feeb26ad72d.png" width="100" height="40"/></a>
