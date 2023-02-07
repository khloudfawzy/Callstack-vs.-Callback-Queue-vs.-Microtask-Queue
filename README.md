Javascript has only one thread of execution, but It still can handle asynchronous operations. How is that?
By the use of CallStack, Callback queue and microtask queue

**CallStack:**

it's used by javascript to keep track of multiple function calls, synchronous code is pushed into the callStack first
call stack is a real data structure stack that follows the (Last-In-First-Out) LIFO principle

**Example:**

    function function1() {
      console.log('this is the first function!');
    }
      
    function function2() {
      f1();
      console.log('this is the second function!');
    }
      
    function2();
    

**Output:**

    this is the first function!
    this is the second function!

**Explanation:**

when the app loads in memory

    1- the global execution context is pushed to the call stack
    2- then function2 gets called, and it's also pushed to the call stack
    3- while calling function2, function1 is called inside, so it's pushed to the call stack
    4- following the LIFO principle, function1 will be executed first and the concole.log statement will run and print 'this is the first function!'
    5- after function1 is done it's removed from the stack, and the execution of function2 continues and prints 'this is the second function!'
    6- function1 will be removed from the stack

before talking about callback queue and microtask queue, we need to mention something very important in javascript
that's **Event Loop**

**Event Loop**

The event loop is what helps javascript to checks whether the call stack is empty or not

**Why is that important?**

Because if the callstack is done with executing all synchronous code, and it's now empty, the functions waiting to be executed in the callback queue and microtask queue get pushed to the callstack.


**Callback queue:**
It is a data structure that operates on the (first-in-first-out) FIFO principle.
Asynchronous callback functions are pushed into the callback queue, then it's pushed to the callstack by the event loop when the callstack is empty

**Example:**

    function printFirst(){
        console.log("First!");
    };
    function printSecond(){
        setTimeout(() => {
        console.log("Second!");
    }, 100);
    };
    function printThird(){
        console.log("Third");
    };
    
    printFirst();
    printSecond();
    printThird();
    
**Output:**
  
    First!
    Third!
    Second!
    
**Explanation:**

    1- when printFirst is called, it's pushed to the callstack, then it print 'First!' to the screen
    2- printFirst is done executing, so it's removed from the callstack
    3- then printSecond is called, it's pushed to the callstack
    4- printSecond makes a call to web API (setTimeout) 
    5- after 100 ms the web API is done executing the setTimeout, so it'll place it in Callback queue, which will be pushed to the callstack once it's empty
    6- printSecond is removed from the callstack
    7- printThird is pushed to the callstack and it prints 'Third!', then it's removed from the callstack
    8- now the callstack is empty and the setTimeout will be moved from the Callback queue to the callstack by the event loop
    9- it prints 'Second!', then it'll be removed from the callstack

**Microtask queue:**

microtask queue handles promise operations.
when a peomise is ready, its .then/catch/finally handlers are put into the queue
but it's not executed, yet. tha tasks in the microtask queue is executed **after** callstack is done with all synchronous code and **before** executing the tasks in the callback queue

**So the otder is like this:**

    1- callstack
    2- microtask queue
    3- Callback queue


**Example:**

    console.log('start');

    setTimeout(function() {
       console.log('setTimeout');
    }, 0);

    Promise.resolve().then(function() {
       console.log('promise resolve');
    });

    console.log('end');

**Output:**
  
    start
    end
    promise resolve
    setTimeout
    
**Explanation:**

     1- console.log('start') is added to the callstack, after execution and printing 'start' it's removed from the callstack
     2- setTimeout creates a call to the web API, then after 0 ms it's addedd to the Callback queue
     3- Promise.resolve is added to the microTask queue
     4- console.log('end') is added to the callstack and it prints 'end'
     5- now the callstack is empty, so the event loop will move console.log('promise resolve') from the microTask queue into the callstack and print 'promise resolve'
     6- now after both the callstack and the microTask queue are empty, the event loop will move console.log('setTimeout') from the Callback queue into the callstack and print 'setTimeout'
