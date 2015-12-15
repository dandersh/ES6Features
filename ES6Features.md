<h1>ECMAScript 6 Features</h1>
<h2>Introduction</h2>
<p>The upcoming version of ECMAScript 6, known as "Harmony" or "ES2015" was released in June of 2015. There are many new features that are set to be added that will enhance and improve the language. Here I plan to look at these features in depth.</p>
<h3>How do I get started?</h3>
<p>The first question usually is, "Where can I play these cool toys"? While there is not current widespread browser implementation, there are quite a few playgrounds for the curious programmer to play in...</p>

<p>The most straightforward one is <a href="http://www.es6fiddle.net/">ES6Fiddle</a>. Here you can select a feature (which comes with a preloaded example) to play around with. Very cool.</p>

<p>Another option is to use the Firefox console. Here we have access to almost all of the upcoming features, minus notable ones like <code>Module</code> and <code>class</code>. </p>

<p>There are a few ways for us to use ES6 in NodeJS. First to see what options are available in the currently installed version of Node we use <code> node --v8-options | grep harm </code>. To actually start playing with these features we enable them using the harmony flag like so: <code>node --harmony</code>. Depending on the version of node you're using you may need to use the strict flag to enable block scoping using let: <code>--use_strict</code> </p>

<p>Another easy way to inspect these features is via JSbin. Here we can enable these features in jshint like so: <code>/* jshint esnext:true */ </code> </p>

<div class="row"> 
     <div class="span9 columns">
     	  <h3>Destructuring</h3>
	  <p>Destructuring is the use of patterns to unpack data structures like objects and arrays. This feature is available in languages such as CoffeeScript, Lisp, and Python. Both objects and arrays can be destructured, with the differences being the use of {}/[] and keys/indices to pull out values. We'll look at how this works and some common uses for destructuring. First we'll look at my favorite use and why I'm so excited about destructuring: setting values from objects/arrays passed in as parameters. Very often we need values of data structures that are passed into functions. So what we do is pull each value out and create a reference, adding lines of "setup code" to our functions: </p>

	  <pre><code> 
	  function displayEmployee(employee) {
	      var firstName = employee.firstName;
	      var lastName = employee.lastName;
	      var department = employee.department;
	      // UGH are we done yet?	 
	  }	  
	  </code></pre>

	  <p>Look familiar? Ugly, repetitive code that's not terribly safe (what happens if our employee's department is missing?). Let's see how we would destructure this: </p>
	  
	  <pre><code>
	  function displayEmployee(employee) {
	      let {firstName, lastName, department} = employee;    
	  }
	  </code></pre>

	  <p>Woah! So there's a few things here. The first is that we have our {} on the left hand of the assignment. The next is that we're setting all of references on a single line. Importantly we're expecting each of those references to exist on the object as "firstName", "lastName", and "department". What if we wanted to assign these values to different name? </p>

	   <pre><code>
	  function displayEmployee(employee) {
	      let {firstName: first, lastName: last, department: area} = employee;    
	  }
	  </code></pre>

	  <p>Pretty slick. But wait, didn't I say it was safer? What happens if we don't know if our department key will be on the passed in object? </p>

 	  <pre><code>
	  function displayEmployee(employee) {
	      let {firstName, lastName, ?department} = employee;    
	  }
	  </code></pre>
	  
	  <p>When we preface our reference with a <code>?</code> we're saying that if department does not exist on the employee object, set it to <code>undefined</code>. This matters because if department does not exist on employee we will get a runtime error as we are expected this key to exist and it does not as it is undeclared. Is it possible to set it on all keys without prefacing each one individually? Yep!</p>

 	  <pre><code>
	  function displayEmployee(employee) {
	      let ?{firstName, lastName, department} = employee;    
	  }
	  </code></pre>
 
	<p>So, why wouldn't you preface every destructured object/array with <code>?</code>. Good question. One possible reason would be that we want to know as soon as possible when a value does not exist and not let it propagate through the call stack. </p>

	<p>Now that we've had fun with objects let's take a quick look at arrays. One really cool thing we can do is populate a brand new array with whatever parts we want from an already existing array. Here's a quick example:  </p>
	<pre><code>
	
	var nums = ['1', '2', '3', '4', '5'];
	var [first, second,,four] = nums; // ['1', '2', '4'];

	</code></pre>

	<p>Another cool thing we can do with destructuring is eliminate temporary variables names when reassigning values. These can be messy and distracting, especially for something as straightforward as swapping reference assignments:

	<pre><code>
	var a = 1, b = 2;
	// Say we want to switch the values and have a point to 2 and b point to 1
	var temp = a, a = b, b = temp;
	console.log(a);  // 2
	console.log(b);  // 1

	// Ugh. How pretty can we make it with destructuring?
	[b, a] = [a, b];

	console.log(b); // 2
	console.log(a); // 1

	// Very Nice! [/Borat]

   	</code></pre>

	<p>That's all I have for destructuring. If I missed anything let me know. Let's take a look at some other goodies... </p>
	
	<h3>Let, Const, and block scoping</h3>
	<p>One of the gotchas when people start learning JavaScript is the lack of block scoping. This works as it sounds: a variable declaration exists only inside of a <code>{ } </code> declaration. This is where the new <code>let</code> keyword comes in. With this keyword you can more tightly control scope as the value will exist inside the scope it is defined, and any subsequent sub-scopes (like a loop residing inside of a function) but not a scope above the one that the value was defined in. Let's take a look:</p>

<pre><code>
function test() {
   var x = 'x';
   let y = 'y';
  
  for (let x = 2; x > 1; x--) {
    console.log('inside x is: ' + x) // inside x is: 2;
    let insideY = x;
    console.log('y is: ' + y); // y is: y
  }
  
  console.log('outside x is: ' + x); //outside x is: x
  console.log('insideY is: ' + insideY) // insideY is not defined;
  
}

test()
</code></pre>

	<p>Here we see the y value assigned via <code>let</code> is available in the for loop inside the function, however the insideY value assigned via <code>let</code> is not available outside of the loop. Also pay attention to how inside the loop x is the loop value and not the 'x' assigned at the top of the function. While this is pretty straightforward, there are a few things to be aware of with <code>let</code>: </p>

<p>In strict mode you cannot reassign a variable created with <code>let</code> as you can with <code>var</code>. </p>

<p>Temporal Dead Zone (no not a Halloween party): Unlike a value referenced via <code>var</code> when you attempt to reference a value initialized with <code>let</code> you will see a <code>ReferenceError</code> and not the <code>Undefined</code> that you are used to seeing. For example: </p>

<pre><code>
console.log(n); // Throws ReferenceError
var n = 'Better hope you haven't tried to reference me already!!!';

</code></pre>

<p>So what are we to take away from this? Well first of all, if someone says that <code>let</code> does not hoist (like vars and function declarations do) then they're wrong. The other thing to be aware of is not assigning a value to to a reference declared with <code>let</code> behaves the same as if you did so with <code>var</code>: </p>

<pre><code>
var n; // is Undefined
console.log(n); 'n is Undefined'
</code></pre>

<p>Now what about <code>const</code>? It's important to remember that the scoping rules are the exact same as they are with <code>let</code>. Assign a value to <code>const</code> will prevent it from being reassigned, throwing a syntax error should you attempt to do so: </p>

<pre><code>
var baz = "baz";
const foo = "foo";
var baz = "rawwwwr";
console.log(foo); // foo
console.log(baz); // rawwwwr
const foo = "ohNoes!!!"; // TypeError: redeclaration of const foo
</code></pre>

<p>As you can see we are able to reassign <code>baz</code> but attempting to do so with the value created by <code>const</code> results in a <code>TypeError</code> that helpfully explains that we were attempting to redeclare <code>foo</code>. 

<p>There is also now a more straightforward way to creating block scope. Previously one could manually simulate a block scope like so: </p>

<pre><code>
	if (true) {
	   // true is, well true, so we know we will enter this block and can begin assigning references as we see fit.
	}	
</code></pre>

	<p>Now we have an actual block scope as intended by using <code>let</code>. As pointed out earlier, creating a reference via <code>let</code> in something like a loop will keep that reference (and its evaluated value) in that loop and that loop only. This means no more cycling through the alphabet looking for iterators for <code>for...</code> loops. </p>
     </div>	  
</div>

<div class="row"> 
     <div class="span9 columns">
     	  <h3>Arrow Functions</h3>
	  <p>Arrow Functions, or Fat Arrow Functions, is a feature inspired by CoffeeScript. The important parts of this feature is the syntax that is used (resulting in less verbose code) and that scopes <code>this</code> to the function definition and not the caller.  The use is pretty straightforward, as we can replace the passed params and the expression that is used, resulting in code that looks like so: <code> param => expression</code>. Because code speaks louder than non-code : </p>

<pre><code>
// var fn = function(str) { return 'This is a string: ' + str;}
var fn = str => 'This is a string: ' + str; 
fn('test');

// let test = function(str) { return 'This is a string: ' + str;}
let test = str => 'This is a str: ' + str;
test('rawwwrrr')

</code></pre>

	<p>As expected you are not limited to a single parameter with a single, simple expression. Multiple paramaters are wrapped with parens <code>(param1, param2)</code> and multiple statements are wrapped in braces <code>{param1 + param2}</code>. What if you intend to return an object in your statement? Well you just wrap the braces with parans like so: <code>({ 'key' : val })</code>. What about immediately invocation, perhaps an IIFE? Yep that's doable as well: </p>

<pre><code>
// (function(x) { return x + 3; }) (2);
( x => x + 3 )(2)
</pre></code>

	<p>What are some other useful things to know? The most important is the context of <code>this</code>. As mentioned previously the scope is determined by the definition, not by the caller, eliminating a pain point of the language. This also means that the context cannot be changed via <code>bind, apply, call</code>. These functions can be used, however they are unable to modify the context of <code>this</code>. Using <code>typeof</code> will return <code>function</code>. It's important to point out that the <code>arguments</code> pseudo-array is available, as previously this was not the case and some write-ups on the interwebs reflect this. </p>
     </div>
</div>

<div class="row">
     <div class="span9 columns">
     <h3>Default params, spread, and rest  </h3>
     <p>Something that Pythonistas will be familiar with in ES6 is the use of default arguments. With these we can specify a value that the argument will be set to if one is not passed on invocation. This value will be created as a new object when it is called, instead of being appended to one that previously exits. The default paramater must be trailing in order to be set, otherwise the other paramters will return undefined. Here's a quick exmaple:  </p>

     <pre><code>
	function test(x, y=1) { // code here };  // y will be set as 1 if a value is not set upon invocation
	function test (y=1, x) { // code here }; // 'x' will be undefined as it trails the default paramater
     </code></pre>

     <p>So what will happen if 'undefined' or 'null' are passed when invoking our function? Well undefined will reference the value set as the default paramter, while passing null will override the default value and set null as the value. </p>


     <pre><code>
     function foo(x=1, y=2) { // code here };
     foo(undefined, null); // paramaters will be set as '1, null'
     </code></pre>

     <p>When grabbing the length of the arguments, what can we expect when default paramters are used? So here the arguments.length will return the number of non-defaulted paramters. Here is an example: </p>

     <pre><code>
	(function(a,b,c=5){ // code here }).length; // Will return '2' as 'c=5' is a default argument.
     </code></pre>
     </div>

     <p>When working with the <code>spread</code> operator, the easiest way to think of it is as if you are spreading out array elements so they can be passed individually into functions or other arrays. Unlike <code>rest</code> you can pass it anywhere as an argument. To use you preface your argument with <code>...</code> like so:</p>

<pre><code>
 fn(...iterableObj) [...iterableObj, 1,2,3]
</code></pre>

	<p>So what are some possible use cases? How about combining one defined array into another?</p>

<pre><code>
var cd = ['c', 'd'];
var partialABC = ['a', 'b', ...cd, 'e', 'f', 'g'];
// partialABC = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
</code></pre>

	<p>Another use for <code>spread</code> is to push elements to an array in place of <code>Array.prototype.push.apply(destArr, srcArr)</code>. We need to use <code>apply</code> as just calling plain old <code>push()</code> will add the array instead of each element of the array. Take a look: </p>

<pre><code>
 var test = [1,2,3];
 var blah = ['a','b', 'c'];
 test.push(blah);		// [1,2,3,['a','b','c']]
 test.push(...blah); 	   	// [1,2,3,'a','b','c'];
</code></pre>

	<p>If you guessed that <code>rest</code> would be the opposite of <code>spread</code> than you are correct Instead of dealing with individual elements like with <code>spread</code> we are dealing with an array. In fact it's probably best to think of <code>rest</code> as collecting the "rest" of the elements and placing them into an array. What's really cool is that you can write code for an expected number of arguments. It's important to remember that, like <code>default</code> you will need to use <code>rest</code> as the final argument. If you guessed that we use <code>...</code> for syntax you'd be correct:

<pre><code>
functionlogMyBooks(...books) {
   books.forEach(function(book) { console.log(book); });
};
</code></pre>

<div class="row">
     <div class="span9 columns">
     	  <h3>Promises (for Matthew)  </h3>
     	  <p> Coming soon!!! </p>

     </div>
</div>	


