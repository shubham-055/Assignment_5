# Assignment_5

## Q1. What is the output of this code snippet?Why?

```js
console.log('start');
const promise1 = new Promise((resolve, reject) => {
console.log(1)
resolve(2)
console.log(3)
})
promise1.then(res => { console.log(res) }) 
console.log('end');

// Output:
// start
// 1
// 3
// end
// 2
```
- The console.log('start') statement is executed first
- Inside the Promise constructor, '1' is logged, then '3' is logged, and the Promise is resolved with the value 2.
- 'end' is logged before 2 is logged because `.then` handler execution are asynchronous, so they won't block the main thread and allow the synchronous code to run first.

## Q2. What is the output of this code snippet?Why?

```js
console.log("start");
setTimeout(() => {
  console.log("setTimeout");
});
Promise.resolve().then(() => {
  console.log("resolve");
});
console.log("end");

// Output: 
// start
// end
// resolve
// setTimeout
```
- 'start' is logged.
- setTimeout and Promise.resolve().then(...) are asynchronous operations, so they will be scheduled to run in the event loop while the synchronous code continues.
- 'end' is logged.
- the setTimeout callback is logged after the resolve because the setTimeout function executes through callback queue (in JS runtime environment) Whereas All the callback functions that come through promises go in microtask Queue and Microtask Queue task always has higher priority than callback queue. 


## Q3. What is callback hell? Write an example to convert callback hell to promise.
```js
function getDataFromServer(callback) {
  // Simulate an asynchronous operation to fetch data from the server
  setTimeout(() => {
    const data = { id: 1, name: 'Shubham Jain' };
    callback(data);
  }, 1000);
}

function processData(data, callback) {
  // Simulate another asynchronous operation to process data
  setTimeout(() => {
    const processedData = { ...data, age: 21 };
    callback(processedData);
  }, 1000);
}

function saveDataToDB(data, callback) {
  // Simulate yet another asynchronous operation to save data to the database
  setTimeout(() => {
    const savedData = { ...data, status: 'Saved' };
    callback(savedData);
  }, 1000);
}

getDataFromServer((data) => {
  processData(data, (processedData) => {
    saveDataToDB(processedData, (savedData) => {
      console.log('Final Result:', savedData);
    });
  });
});
```

Converted to promises.

```js

function getDataFromServer() {
  return new Promise((resolve) => {
    // Simulate an asynchronous operation to fetch data from the server
    setTimeout(() => {
      const data = { id: 1, name: 'Shubham jain' };
      resolve(data);
    }, 1000);
  });
}

function processData(data) {
  return new Promise((resolve) => {
    // Simulate another asynchronous operation to process data
    setTimeout(() => {
      const processedData = { ...data, age: 21 };
      resolve(processedData);
    }, 1000);
  });
}

function saveDataToDB(data) {
  return new Promise((resolve) => {
    // Simulate yet another asynchronous operation to save data to the database
    setTimeout(() => {
      const savedData = { ...data, status: 'Saved' };
      resolve(savedData); // Resolve the Promise with the saved data
    }, 1000);
  });
}

function processDataAndSave() {
  return getDataFromServer()
    .then((data) => processData(data))
    .then((processedData) => saveDataToDB(processedData))
    .catch((error) => console.error('Error:', error));
}

processDataAndSave().then((result) => {
  console.log('Final Result:', result);
});


```
## Q4. What is the output of this code snippet?Why?

```js
function createIncrement() {
  let count = 0;
  function increment() {
    count++;
  }
  let message = `Count is ${count}`;
  function log() {
    console.log(message);
  }
  return [increment, log];
}
const [increment, log] = createIncrement();
increment();
increment();
increment();
log();

// Output: 
// Count is 0
```

- We define a function called createIncrement which has two inner functions: increment and log.
- we declare a variable count and initialize it to 0 and The increment function is defined to increment the count variable by 1.
- message variable, which holds the string "Count is ${count}" (count has value 0 till this point) so message has a string "Count is 0";
- The log function logs the value of the message variable to the console.
- createIncrement function returns an array containing the increment and log functions. We use array destructuring to assign the functions increment and log to separate variables outside the createIncrement function.
- We then call the increment function three times. Each time it is called, it increments the count variable, which now becomes 3.
- Finally, we call the log function, which logs the value of message to the console. However, please note that the message variable was defined when count was 0, and it doesn't update as count changes. So, even though count is now 3, the message variable still holds the string "Count is 0", and that's what gets logged to the console.


## Q5. Extract the data from the API https://jsonplaceholder.typicode.com/users and print name, email id, phone number and company name from the extracted data.

```js
//Using Promises

// Fetch data from the given URL
fetch("https://jsonplaceholder.typicode.com/users")
  // Convert the response to a JSON format
  .then(response => response.json())
  // Process the JSON data
  .then(value => {
    // Loop through each user in the data
    value.forEach((user) => {
      // Print user information in a formatted way
      console.log(`${user.id} Name : ${user.name}\tEmail : ${user.email}\tPhone Number : ${user.phone}\tCompany : ${user.company.name}`);
    });
  })
  // Handle any errors that occur during the process
  .catch(err => console.log(err.message));
```

## Q6. Explain and write a code snippet for the following:-
  ```js

  const p1 = new Promise((resolve, reject) => {
  resolve('P1 resolved');
  });

const p2 = new Promise((resolve, reject) => {
  resolve('P2 resolved');
});

const p3 = new Promise((resolve, reject) => {
  setTimeout(() => resolve('P3 resolved'), 3000);
});

const p4 = new Promise((resolve, reject) => {
  reject('P4 Failed');
});
```

  ### a). Promise.all()

  Promise.all() takes an iterable of promises and return a promise which resolves to an array of all the resolved promises values if all fullfilled and rejects with the reason of the first promise that rejects.

```js
Promise.all([p1, p2, p3])
  .then(values => {
    console.log(values); // Output: ['P1 resolved', 'P2 resolved', 'P3 resolved']
  })
  .catch(error => {
    console.error(error); // This will not be called as all promises are resolved successfully.
  });
```

  ### b). Promise.allSettled()
  Promise.all() takes an iterable of promises and return a promise which resolves to an array of objects which has status of each promise in the input iterable, whether fulfilled or rejected.

  ```js
  Promise.allSettled([p1, p2, p3,p4])
  .then(values => {
    console.log(values);
    /* Output:
    [
      { status: 'fulfilled', value: 'P1 resolved' },
      { status: 'fulfilled', value: 'P2 resolved' },
      { status: 'fulfilled', value: 'P3 resolved' },
      { status: 'rejected', reason: 'P4 Failed' }

    ]
    */
  })
  ```

  ### c). Promise.any()

  Promise.any() takes an iterable of promises and returns a new promise that resolves with the value of the first fulfilled promise in the input iterable. If all the promises are rejected, it will return a Promise rejection with an AggregateError containing an array of all the rejection reasons.

  ```js
  
Promise.any([p1, p2, p3, p4])
  .then(value => {
    console.log(value); // Output: 'P1 resolved' (The first promise that fulfills)
  })
  .catch(errors => {
    console.error(errors); // This will not be called since at least one promise is fulfilled.
  });

  ```

  ### d). promise.race()
  Promise.race() takes an iterable of promises and returns a new promise that resolves or rejects as soon as one of the promises in the input iterable resolves or rejects, with the value(if fullfilled) or reason(if rejected) of that promise.

```js
Promise.race([p4, p2, p3, p1])
  .then(value => {
    console.log(value); // This will not be called as the first promise p4 is rejected.
  })
  .catch(error => {
    console.error(error); // Output: 'P4 Failed' (The first promise that settles, which is rejected)
  });
```


## Q7. What are closures? Explain the output of below code snippet?

```js
"use strict";
function outer() {
  const a = 10;
  let b = 100;
  function inner() {
    let c = 20;
    console.log(`a=${a}, b=${b}, c=${c}`);
    b++;
    c++;
  }
  return inner;
}
const fnFirst = outer();
const fnSecond = outer();
console.log(fnFirst);
fnFirst();
fnFirst();
fnFirst();
fnSecond();

// Output: 
// ƒ inner() {
//     let c = 20;
//     console.log(`a=${a}, b=${b}, c=${c}`);
//     b++;
//     c++;
//   }
// a=10, b=100, c=20
// a=10, b=101, c=20
// a=10, b=102, c=20
// a=10, b=100, c=20
```
Closures is the concept in Javascript where a function is bundled together (enclosed) with references to its surrounding state (the lexical environment).It can access and manipulate those variables(outer scope) even after the outer function has completed execution.

- outer() function is invoked and it returns the inner function(closure) which was assigned to fnFirst.
- Similarily, We again invoke the outer function and assign the returned inner function to the variable fnSecond. Note that this creates a new closure with its own set of local variables independent of the first closure(This is also a main Advantage of closures also).
- When fnFirst is called it initalize c with 20 and console log values of a=10,b=100 and c=20 and increments both b and c.
- We call fnFirst again. Since it is the same closure, the values of a=10, b=101(incremented last time), and c will remain the same as before because c has defined in that scope only and destroyed when function has executed. we will see the output: a=10, b=101, c=20.
- We call fnFirst once more, and the output will be: a=10, b=102(b is incremented), c=20.
- We call fnSecond, which is a different closure with its own set of local variables. The values of a and b in this closure are independent of the first closure. The output will be: a=10, b=100, c=20

## Q8. What are generator functions? Explain it with the help of an example.

Generator functions are function in JavaScript that can be paused and resumed during their execution. They are defined using the function* syntax. It enables the use of the `yield` keyword to produce values and control the flow of the function execution.The key characteristic of generator functions is that they can yield values one at a time using the yield keyword and then resume execution from where they left off when `next()` is called on the iterator returned by the generator function.

```js
// Generator function definition
function* My_generator(){
  // First `yield`, yields the value `10` to the caller and pauses
  let x = yield 10;
  // Resumes from the first `yield`, assignes value passed to `next()` (100) to x and logs x
  console.log(x);
  // Second `yield`, yields the value `20` to the caller and pauses
  return yield 20;
}

// Initialize the generator function and get the iterator
const itr = My_generator();

// Variable to control the loop iterations
let n = 3;

// Loop for 4 iterations
while (n--) {
  // Call the generator's `next()` method with the argument 100
  console.log(itr.next(100));
}


// Output: 
// { value: 10, done: false }
// 100
// { value: 20, done: false }
// { value: 100, done: true }
```

## Q9. Create a prototype of the reduce method.


  ```js
  Array.prototype.Reduce = function (cb, initialValue) {
    if (this.length === 0 && initialValue === undefined) {
      throw new TypeError("Reduce of empty array with no initial value");
    }

    let accumulator = initialValue !== undefined ? initialValue : this[0];

    for (let i = 0; i < this.length; i++) {
      accumulator = cb(accumulator, this[i], i, this);
    }

    return accumulator;
  };

  const arr = [1, 2];
  console.log(
    arr.Reduce((acc, item) => {
      console.log(acc, item);
      acc.push(item+10)
      return acc;
    },[])
  );

  // Output: 
  // [] 1
  // [ 11 ] 2
  // [ 11, 12 ]

  ```
- The custom Reduce prototype is added to Array.prototype, taking a callback function (cb) and initial value (initialValue). It iterates over the array, applying the callback to each element, and returns the final accumulated value. 

- The custom Reduce method is called on the array [1, 2].

- The callback function takes the accumulator and the current array element as parameters. It logs the current accumulator and the array element, then pushes the current array element + 10 into the accumulator.

- During the first iteration, the accumulator is an empty array ([]) and the current element is 1. After pushing 1 + 10 into the accumulator, it becomes [11].

- During the second iteration, the accumulator is [11], and the current element is 2. After pushing 2 + 10 into the accumulator, it becomes [11, 12].
