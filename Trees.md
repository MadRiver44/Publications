# Data Structures 101: Binary Search Tree
## Combining the efficiency of insertion of a Linked List and the fast search of an ordered array

### What is a Binary Search Tree?

Let's start with basic terminology so we may share the same language and investigate related concepts. First, what are the priciples that define a Binary Search Tree (from here on out I will  use BST for brevity)?

A BST is considered a data structure made up of __nodes__, like linked lists, these nodes are either null or have a references (links) to other nodes. These 'other' nodes are child nodes, called a left node and right node. Nodes have __values__. These values determine where they are placed within the BST.

Similarly to a linked list, each node is referenced by __only__ one other node, its parent (except for the root node). So we can say that each node in a BST is in itself a BST because further down the tree, we reach another node and that node has a left and a right, then depending on which way we go, that node has a left and a right and so on. 

    
    1. The left node is always smaller than its parent.
    2. The right node is always greater than its parent.
    3. A BST is considered balanced if every level of the tree is fully filled with the exception of the last level. On the last level, the tree is filled left to right
    4. A Perfect BST is one in which it is both full and complete (all child nodes are on the same level and each node has a left and a right child node).

----add pics for full tree, unbalanced, parts of the tree, etc, -----

Why would we use this?

Speed. As mentioned earlier, the BST is an ordered data structure. Upon insertion, the nodes are placed in an orderly fashion. This inherent order, makes searching fast. Similar to binary search (with an array that is sorted), we cut the amount of data to sort through by half on each pass. For example, suppose we are looking for a small node value. On each pass, we keep moving along the left most node, this eliminates half the greater values automatically!

Also, unlike an array, the data is stored by reference, thus as we add to the data structure, we create a new chunk in memory and link to it. This is faster than creating a new array with more space and then inserting the data from the smaller array to the new, larger one.

In short, inserting, deleting and searching are the all stars for a BST

Now that we understand the principles, the benefits, and the basic components of a BST, let's implement one in javascript.

The API for a BST consists of the following: Insert, Contains, Get Min, Get Max, Remove Node, Check if Full, Is Balanced, and the types of Search - Depth First (preOrder, inOrder, postOrder), Breadth First Search, and lastly Get Height. That's a big api, just take it one section at a time.

### Implementation

#### The constructor

The BST is made up of nodes. 

```
function Node(value){
  this.value = value;
  this.left = null;
  this.right = null;
}
```

The BST constructor is made up if a root node.

```
function BinarySearchTree() {
  this.root = null;
}

let bst = new BST();
let node = new Node();
console.log(node, bst); // Node { value: undefined, left: null, right: null } BST { root: null }
```

... so far so good.

#### Insertion

```
BinarySearchTree.prototype.insert = function(value){
  let node = new Node(value);
  if(!this.root) this.root = node;
  else{
    let current = this.root;
    while(!!current){
      if(node.value < current.value){
        if(!current.left){
          current.left = node;
          break;
        }
        current = current.left;
      } else if(node.value > current.value){
        if(!current.right){
          current.right = node;
          break;
        }
        current = current.right;
        } 
        else {
          break;      
      }
    }
  }
  return this;
};

let bst = new BST();
bst.insert(25); // BST { root: Node { value: 25, left: null, right: null } }
```

Let's add some more values.

```
bst.insert(40).insert(20).insert(9).insert(32).insert(15).insert(8).insert(27)

BST {
  root: 
   Node {
     value: 25,
     left: Node { value: 20, left: [Object], right: null },
     right: Node { value: 40, left: [Object], right: null } } }
```
 ----- for a cool visualisation ()  add link here ------

 Let's unpack this. 

    1. first we pass a value and create a new node
    2. check if there is a root, if not, set this newly created node to the root node
    3. if there is a root node, we create a variable declared "current", and set its value to the root node
    4. if the newly created node.value is smaller that the root node, we will move left
    5. we keep comparing this node.value to left nodes.
    6. if the value is small enough and we reach a point where there is no more left nodes, we place this item here.
    7. If the node.value is greater we repeat the same steps as above except we move along the right.
    8. we need the break statements,because there is no count step to terminate the while loop.

Contains

This is a pretty straight forward approach. We use a switch/ case.

```
BinarySearchTree.prototype.contains = function(value){
  let current = this.root;
  while(current){
    if(value === current.value) return true;
    if(value < current.value) current = current.left;
    if(value > current.value) current = current.right;
  }
  return false;
};
```

#### Get Min and Get Max. 

Keep traversing left to the smallest value or right for the largest.

```
BinarySearchTree.prototype.getMin = function(node){
  if(!node) node = this.root;
  while(node.left) {
    node = node.left;
  }
  return node.value
};

BinarySearchTree.prototype.getMax = function(node){
  if(!node) node = this.root;
  while(node.right) {
    node = node.right;
  }
  return node.value;
};
```
#### Removal
Removing a node is the most tricky operation, because nodes have to be reordered to maintain the properties of a BST. There is a case if a node has only one child and a case if there is both a left and a right node. We use the larger helper function to do the heavy lifting.

```
BinarySearchTree.prototype.removeNode = function(node, value){
  if(!node){
    return null;
  }
  if(value === node.value){
    // no children
    if(!node.left && !node.right) return null;
    // one child and it's the right
    if(!node.left) node.right;
    // one child and it's the left
    if(!node.right) node.left;
    
    // two kids
    const temp = this.getMin(node.right);
    node.value = temp;
    node.right = this.removeNode(node.right, temp);
    return node;
  }else if(value < node.value){
    node.left = this.removeNode(node.left, value);
    return node;
  }else{
    node.right = this.removeNode(node.right, value);
    return node;
  }
};

BinarySearchTree.prototype.remove = function(value){
  this.root = this.removeNode(this.root, value);
};
```

It works like this...

Unlike deleteMin and deleteMax, where we can just traverse all the way left or all the way right and pick off the last value, we have to take out a node and then replace it with something. This solution was developed in 1962 by T. Hibbard. We account for the case where we can delete a node with only one child or none, that's minor, if no children, no problem, if a child is present, that child just moves up one. But with a node scheduled to be removed that has two children, which child takes its place? Certainly we can't move a larger node down. So what we do is replace it with its successor, the next kingpin. We have to find the smallest right child on the right that is larger than the left child.

    1. create a temp value and store the smallest node on its right. What this does is satify fay the property that values to the left are still smaller and values to the right are still greater.
    2. reset the node's value to this temp variable
    3. remove the right node
    4. then we compare values on the left and the right and determine the assigned value.

--- this is best explained with a picture. -----

### Searching

There are two types of search, Depth First and Breadth First. Breadth First is simply stoping at each level on the way down. It looks like this, we start at the root, then the left child, then the right child. Move to the next level, left child then right child, think of this moving horizontally. We employ, I should say simulate, a queue to help order the process. We pass a function because many times we want to operate on a value.

```
BinarySearchTree.prototype.traverseBreadthFirst = function(fn) {
    let queue = [];
    queue.push(this.root);
    while(!!queue.length) {
        let node = queue.shift();
        fn(node);
        node.left && queue.push(node.left);
        node.right && queue.push(node.right);
    }
}

```  
Depth First Search involves moving down the BST in a specified manner, either, preOrder, inOrder, or postOrder. I'll explain the difference shortly. 

In the spirit of concise code, we have a basic traverseDepthFrist function and we pass a function and a method. Again the function implies that we want to do something to the values along the way, the method is the type of search we wish to perform. In the traverseDFS, we have a fallback, preOrder search in place.

Now, how is each one different? First, let's dispatch inOrder, that should be self explanitory but it isn't. Do we mean in order of insertion, in order of highest to lowest or lowest to highest? I just wanted you to consider things beforehand. In this case, yes, it does mean lowest to hightest. 

preOrder can be thought of as Parent, Left Child, then Right child.

postOrder as Left Child, Right Child, Parent.


```
BinarySearchTree.prototype.traverseDFS = function(fn, method){
  let current = this.root;
  if(!!method) this[method](current, fn);
  else this._preOrder(current, fn);
};

BinarySearchTree.prototype._inOrder = function(node, fn){
  if(!!node){
    this._inOrder(node.left, fn);
    if(!!fn) fn(node);
    this._inOrder(node.right, fn);
  }
};

BinarySearchTree.prototype._preOrder = function(node, fn){
  if(node){
    if(fn) fn(node);
    this._preOrder(node.left, fn);
    this._preOrder(node.right, fn);
  }
};

BinarySearchTree.prototype._postOrder = function(node, fn){
  if(!!node){
    this._postOrder(node.left, fn);
    this._postOrder(node.right, fn);
    if(!!fn) fn(node);
  }
};
```

#### Check if the BST is full. 
Remember from earlier, a BST is full if every node has Zero or Two children.

```
// a BST isfull if every node has zero two children (no nodes have one child)
BinarySearchTree.prototype.checkIfFull = function(fn){
  let result = true;
  this.traverseBFS = (node) => {
    if(!node.left && !node.right) result = false;
    else if(node.left && !node.right) result = false;
  }
  return result;
};
```
#### Get Height of BST

What does it mean to get the height of a tree? Why is this important? This is where __Time Complexity__ (aka Big O) comes into play. Basics operations are proportional to the height of a tree. So as we alluded to earlier, if we search for a particluar value, the number of operations we have to do is halved on each step. That means if we have a loaf of bread and cut it in half, then cut that half in half, and keep doing that till we get the exact piece of bread we want. In computer science, this is called O(log n). We start with an input size of some sort and over time that size gets smaller (kind of flattening out). A straight linear search is denoted as O(n), as the input size increases so does the time it takes to run operations. O(n) conceptually is a 45 degree line starting at origin zero on a chart and moving right. The horizontal scale represents the size of an input and the vertical scale represents the time it takes to complete. Constant time is O(1). No matter how large or small the input size is, the operation takes a place in the same amount of time. For example, push() and pop() off of an array are constant time. Looking up at value in a Hash Table, constant time. I will explain more about this in a future article, but I wanted to arm you with this knowledge for now.

Back to height.

We have a recursive function, see my other article here, and our base case is: 'if we have no node then we start at this.root'. This implies that we can start at values lower in the tree and get tree sub-heights. So if we pass in this.root to start, we resurively move down the tree and add the function calls to the execution stack (other article here). When we get to the bottom,the stack is filled and then the calls get executed and we compare the heights of the left and the heights of the right and increment by one.

```
BinarySearchTree.prototype._getHeights = function(node){
  if(!node) return -1;
  let left = this._getHeights(node.left);
  let right = this._getHeights(node.right);
  return Math.max(left, right) + 1;
};

BinarySearchTree.prototype.getHeight = function(node){
  if(!node) node = this.root;
  return this._getHeights(node);
};
```
#### Lastly, Is Balanced.

What we are doing is checking if the tree is filled at every level and on the last level, if it is filled left to right.

```
BinarySearchTree.prototype._isBalanced = function(node){
  if(!node) return true;
  let heightLeft = this._getHeights(node.left);
  let heightRight = this._getHeights(node.right);
  let diff = Math.abs(heightLeft - heightRight);
  if(diff > 1) return false;
  else return this._isBalanced(node.left) && this._isBalanced(node.right);
};

BinarySearchTree.prototype.isBalanced = function(node){
  if(!node) node = this.root;
  return this._isBalanced(node);
};
```

#### Print

Use this to visualize all the methods you see, especially depth first and breadth first traversals.

```
BinarySearchTree.prototype.print = function() {
  if(!this.root) {
    return console.log('No root node found');
  }
  let newline = new Node('|');
  let queue = [this.root, newline];
  let string = '';
  while(queue.length) {
    let node = queue.shift();
    string += node.value.toString() + ' ';
    if(node === newline && queue.length) queue.push(newline); 
    if(node.left) queue.push(node.left);
    if(node.right) queue.push(node.right);
  }
  console.log(string.slice(0, -2).trim());
};
```

### Time Complexity

1. Insertion O(log n)
2. Removal O(log n)
3. Search O(log n)

Wow, that is indeed a lot of information. I hope the explanations were as clear and as intoductory as possible. Again, writing helps me solidfy concepts and as Ricard Feynman said, "When one person teaches, two learn.".

### Resources

Probably the best resource, definitely use!!!
[Visualization!!!](http://btv.melezinek.cz/binary-search-tree.html)

[Big O cheetsheet](http://bigocheatsheet.com/)
[Algorithms by Robert Sedgewick](https://algs4.cs.princeton.edu/home/)
[Wikipedia](https://en.wikipedia.org/wiki/Binary_search_tree)









