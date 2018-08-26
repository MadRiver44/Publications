# Data Structures 101: Queues

### Starting with a Queue

When you go the Shake Shack, most often there are other people on the line waiting to be served. The customers are arranged in a particular order, First In, First Out. Other real life scenarios are toll booths or wedding chapels in Vegas.  This method of ordering data for service, in our case, people, is what Queues are all about.

Queues are very similar to Stacks in terms of interface, with the difference being Stacks process data Last In, First Out.

So we have differences in the order of processing, why? We need a different method of processing data that preserves the order. For example, suppose we have a stream of data in node. As it comes in, we need to do something to it and then write it to a file to read later. For simplicity sake, let's say we need to capitalize every letter streamed. What would happen if we used a LIFO, or stack data structure?

```
* graphic
If we have "free beer", we would get "REEB EERF". With a queue, "free beer" is "FREE BEER".

```
The main reason is queues process data fairly and preserve the order of the collection. This also happens when we iterate over items with a for or while loop, forEach(), or map() method. Each item in the array get processed in the order it was inserted, from index 0 to index.length - 1.

__In Queues, items are processed in the order they are inserted.__

* graphic for Queue, people in line

### Implementation
A simple implementaion using arrays are with the methods shift() to remove from the front, and unshift() to add the the front.

Similar to my post on Stacks, we will describe the API for a Queue and then start with an implementation using the psuedoclassical method and a base object.

When an item is inserted into a queue, it called __enqueued__. When an item is removed, it is __dequeued__. Other methods include, peek, contains, until, and count.

* graphic for enqueue and dequeue

To track our items, we use head for the front of the queue and tail for the back. The difference between the two gives the queue size.

Our Storage mechanism is as follows:
```
// _underscores indicate "private variables" to other engineers

const Queue = function(capacity) {
  this.storage = {};
  this.capacity = capacity || Infinity;
  this._head = 0;
  this._tail = 0
}

let q = new Queue();
q; // Queue { storage: {}, capacity: Infinity, _head: 0, _tail: 0 }
```

To Enqueue:
```
Queue.prototype.enqueue = function(value) {
  if (this.count() < capacity) {
    this.storage[this._tail++] = value;
    return this.count();
  }
  return "Max capacity reached, please remove a value before enqueuing"
}
```
To Dequeue:
```
Queue.prototype.dequeue = function() {
    if (this.count() === 0) {
      return "Nothing in the queue";
    }
    else {
      let element = this.storage[this._head];
      delete this.storage[this._head];
      if (this._head < this._tail) {
        this._head++;
      }
      return element;
    }
}
```
The remaining API:
```
Queue.prototype.peek = function() {
  return this.storage[this._head]
}

Queue.prototype.contains = function(value) {
  for (let i = this._head; i < this._tail; i++) {
    if (this.storage[i] === value) {
      return true;
    }
  }
  return false;
}

Queue.prototype.until = function(value) {
  for (let i = this._head; i < this._tail; i++) {
    if (this.storage[i] === value){
      return i - this._head + 1;
    }
  }
  return null;
}

Queue.prototype.count = function() {
  return this._tail - this._head;
}

let q = new Queue();
q.enqueue('ww');
q.enqueue('aa');
q; // Queue {capacity: Infinity, storage: { 0: 'ww', 1: 'aa' }, _head: 0, _tail: 2 }
q.enqueue('bb');
q.peek(); // 'ww'
q.dequeue(); // 'ww'
q; //Queue {capacity: Infinity, storage: { 1: 'aa', 2: 'bb' }, _head: 1, _tail: 3 }
q.contains('bb'); // true
q; //Queue {capacity: Infinity,storage: { 1: 'aa', 2: 'bb' }, _head: 1, _tail: 3 }
q.until('bb'); // 2
q.count(); // 2
```
Under the hood, we learned in my post on Stacks, that any time a function is called it creates an execution context and is allocated a stack frame on the execution stack. Is there anything similar in JavaSrcipt that utilizes Queues? Yes, the event loop.

### The Event Loop and Queues

Before we get to what the event loop is, we need to understand a few terms first.

__concurrency__ - In computer science, it is the ability for parts of a computer program to run out of order without affecting the final outcome. In the context of Javascript, it refers to the event loop's ability to execute callback functions after completing other work.

__runtime__ - the time in which a program is running.

__non-blocking vs. blocking__ - blocking is when the execution of a Javascript program must wait until another part of the program is completed, sometimes non-JavaScript operations. Essentially, synchronous, do one thing at a time. Non-blocking operations on the other hand work asynchronously. They employ callbacks that allow operations to continue and when the work is completed, the callback asscociated with that particular function or event fires.

__system kernal__ - is the central part of an Operating System. It manages the operations of the computer and memory and hardware, speciifcally the CPU. To be more efficient, the event loop offloads certain operations to the kernal.

#### Now to the event loop.

Javascript is a single threaded language. This means the flow of execution is in order and it does one thing at a time. Node.js is a built off of the [Chrome V8 engine](https://developers.google.com/v8/intro) and it employs a constantly spinning loop waiting for incoming connections. When an asynchronous function executes it enters the event loop. A message associated with this function enters the __message queue__ in the order it was recieved. When the message is dequeued, the callback function executes and is placed on the execution stack. All the while, the event loop keeps spinning waiting for more connections. This is how queues are used behind the scenes in Javascript.


### Time complexity
Queue operations are very efficient. Enqueue, Dequeue, Peek, and Count are fastest working in constant time. Contains and Until take longer as our input size increases operating in linear time O(N);

Enqueue O(1);
Dequeue O(1);
Peek O(1);
Count O(1);
Contains O(N);
Until O(N);




