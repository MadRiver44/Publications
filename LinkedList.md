# Data Structures 101: Linked Lists

Like stacks and queues, Linked Lists are a form of a sequential collection. It does not nessecarily have to be in order. A Linked list is made up of independent nodes that may contain any type of data and has a reference to the next node in the link.

We can emulate stacks and queues with linked lists as well as use it as a base to create or augment other data structures. With linked lists our main concerns are with fast insertions and deletions which are more performant over arrays.

The building block of this structure is a Node.
```
const Node = function(value) {
  this.value = value;
  this.next = null;
};
```
Our Node is built with two properties, a value to hold data, and next, a reference, initially set to null. The next property will be used to "point" to the next Node in the linking. One of the disadvantages of linked lists are that each reference requires a larger memory overhead than an array.

### Implementation
```
const LinkedList = function(headvalue) {
  // !! coerces a value to a Boolean
  if (!!headvalue) {
    return "Must provide and initial value for the first node"
  } else {
    this._head = new Node(headvalue);
    this._tail = this.head;
  }
};
```
In our second constructor, we test for a value to provide for the initial Node. If true, we proceed to create a new Node with the value passed and set the head to tail initially.

#### Insertion
```
LinkedList.prototype.insertAfter = function(node, value) {
  let newNode = new Node(value);
  let oldNext = node.next;
  newNode.next = oldNext;
  node.next = newNode;
  if (this._tail === node) {
    this._tail = newNode;
  }
  return newNode;
};
```
For this method, we create a new Node and adjust the references. The former next reference of the original node is now directed to newNode. The newNode's next reference is "pointed" to what the former node's next was refering to. Finally, we check and reset the tail property.
```
LinkedList.prototype.insertHead = function(value) {
  let newHead = new Node(value);
  let oldHead = this._head
  newHead.next = oldHead;
  this._head = newHead;
  return this._head;
};
```
```
LinkedList.prototype.appendToTail = function(value) {
  let newTail = new Node(value);
  this._tail.next = newTail;
  this._tail = newTail;
  return this._tail;
};
```
Insertion at the beginning or end of a linked list is fast, operating in constant time. For this we create a new node with a value and rearrange our reference variables, and reset which node is now the head for insertHead or tail for appendToTail. These operations represent fast insertions for collections, push for stacks and enqueue for queues. It may come to mind that unshift for arrays is the same. No, because with unshift all members of the collection must be moved one index over, which makes it a linear time operation.

#### Deletion
```
LinkedList.prototype.removeAfter = function(node) {
  let removedNode = node.next;
  if (!!removedNode) {
    return "Nothing to remove"
  } else {
    let newNext = removedNode.next
    node.next = newNext;
    removedNode.next = null; // dereference to null to free up memory
    if (this._tail === removedNode) {
      this._tail = node;
    }
  }
  return removedNode;
};
```
Starting with a test for a node to remove, we proceed to adjust the references. Dereferencing the removedNode and setting it to null is important so not only do we free up memory but we avoid having multiple references to the same object.
```
LinkedList.prototype.removeHead = function() {
  let oldHead = this._head;
  let newHead = this._head.next;
  this._head = newHead;
  oldHead.next = null;
  return this._head;
};
```
Deletion of a head and of a specified node in, removeAfter, are constant time removals. In addition, if the value of the tail is known, then tail removal can be done in O(1), else we have to move linearly to the end to remove it, O(N);

#### Looping and forEach
We use the following to iterate through a linked list or to operate on each node value.
```
LinkedList.prototype.findNode = function(value) {
  let node = this._head;
  while(node) {
    if (node.value === value) {
      return node;
    }
    node = node.next;
  }
  return `No node with ${value} found`;
};

LinkedList.prototype.forEach = function(callback) {
  let node = this._head;
  while(node) {
    callback(node.value);
    node = node.next;
  }
};

LinkedList.prototype.print = function() {
  let results = [];
  this.forEach(function(value) {
    result.push(value);
  });
  return result.join(', ');
};
```
The main advantage of Linked Lists are fast insertions and deletions without rearranging items or reallocation of space. When we use an array, the memory space is contiguous, meaning we keep it all together. With linked lists, we can have memory spaces all over the place, non-contiguous storage through the use of references. For arrays, that locality of references means that arrays have better caching of values for faster lookup. With linked lists, caching is not optimized and access time takes longer.

Another aspect of linked lists are different types of configuration. Two main examples are __circularly__ linked, where the tail has a reference to the head and the head to the tail. __Doubly__ linked is when, in addition to the node having a reference to the next node, also has a reference looking back to the previous node.

### Time Complexity
1. Insertion
  * insertHead, appendToTail - O(1)
  * if specific node is known, insertAfter - O(1)
2. Deletion
  * removeHead - O(1);
  * if specific node is known, removeAfter - O(1)
  * if node is not known - O(N)
3. Traversing
 * findNode, forEach, print - O(N)

### Resources
[Locality of Reference](https://en.wikipedia.org/wiki/Locality_of_reference)
[Great Answers here](https://stackoverflow.com/questions/166884/array-versus-linked-list)
[and here](https://stackoverflow.com/questions/166884/array-versus-linked-list)
[Linked List](https://en.wikipedia.org/wiki/Linked_list)





