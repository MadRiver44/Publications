# Binomial Heaps a.k.a Priorty Queues
## A different form of Binary Tree

From our earlier disscuion of Binary Search Trees, we learned that storing our data in an ordered manner priovides efficient methods of search, insertion and removal of nodes. 

Suppose we have a have a program that is running several sub-routines in the background. Some of these sub-routines are getting data, some are processing said data, and then finally provide the finished result to the user. What good is presenting the data if we haven't even got the data to process in the first place? What we need is to assign a priority to these values or operations. From there we can create methods to extract the one with the highest priority, re-order the values as items are removed, and then provide an effiecient insertion mechanism. 

A more concrete example is an emergency room where even though there are other patients needing care, if someone is seriously injured, they are given priority. Once that patient is "extracted" from the queue for treatment, the patient order is restored. Another way to think of a priorty queue is your cell phone. In the background, processes are running. Let's say you're playing a game on the phone and you get a phone call, that call is given priority to alert you either visually or aurally over the game screen or sounds.

The Binomial Heap is just like a Binomial Tree except with two additional constraints.

    1. __Shape Property__

        A binomial heap is a complete binary tree where all levels of the tree except the last one is completely filled. If the the last level is not complete, the nodes are filled left to right.

    2. __Heap Property

        A binomial heap's values are stored in an array such that each value is guaranteed to be larger than or equal to the values stored at two other positions. Those positons are the left child and right child.

With respect to the heap property, we are referring to a Parent, left child, and right child, just like a Binomial Search Tree.

For example...

```
// we have an array
const heapExample = [null, 10, 8, 7, 3, 5, 6, 4];
```

We start at heapExample[0]. The value here is null, which makes the math we need easier later.
This array is ordered in such a way to be called a Max Heap, where the largest value is in index 1. 

This largest value, 10, has two children. the left child is located at 2*index, (2 * 1 = heapExample[2] = 8). The right child of 10 is 2*index + 1, ((2 * 1) + 1) = heapExample[3] = 7.

This is why we start our heap at index 1. That aside, since 8 and 7 are children of 10, and hold to heap property #2, what are their children?

For 8, we start at 8's index position, 2, the location of 8. The left child is 2 * 2 = 4, heapExample[4] = 3. The right child of eight is ((2 * 2) + 1) = 5, heapExample[5] = 5.

For 7, we start at index position, 3, the location of 7. The left child is 2 * 3 = 6, heapExample[6] = 6. The right child of 7 is ((2 * 3) + 1) = 7, heapExample[7] = 4.

This explains the relationship in the heap of parents to children. This arragment is called __level order__. 

To find a parent:
    Left child parent = 2i/ 2.
    Right child parent = 2i + 1/2 (rounded down).

Thus to move __UP__ the tree from a[i], from any child, we set i = i /2.
To move __DOWN__ the tree from a[i], we set i = 2i for the left child and i = 2i + 1 for the right child.

### Let's create a Binomial Heap

We start with a constructor and an insert method.

```
function BinomialHeap() {
    this.dataStorage = [null]; // remember we start our array at null;
}

BinomialHeap.prototype.insert = function(value) {
    this.dataStorage.push(value); 
    this.swim(this.dataStorage.length -1) // we pass an index to this.swim()
};
```
Our constructor is easy to understand. Whenever we create an instance of a BinomialHeap, we initialize an array with index 0 set to null. Subsequent values start at index 1 and proceed from there.

For insert we push a value onto the end of the array then we swim. What the heck is swim? Glad you asked! 

To have a heap, we need to maintain it and that involves keeping it in order. So what if we push a larger value on the end? What happens is our ordering is violated. Therefore we implement a method to move this new value up the heap until it finds the right place with respect to the value of its future parent and it's own children.

A common term you may see is "heapify". This simply means restoring order to a heap in one of two ways. 
    
    1. When a priority of a node/value is __increased__ or a new node is added at the bottom of a heap, we have to travel up the heap to restore order.

    2. When a priority of a node/value is __decreased__ (when we replace the root node at heap[1]), with a smaller node/value, we have to travel down the heap to restore order.

### Swim

```
BinaryHeap.prototype.swim = function(index) {
    let parentIndex = Math.floor(index/2);
    let parent = this.dataStorage[parentIndex];
    let child = this.dataStorage[index];
    if(parentIndex < 1 || parent >= child){
        this.dataStorage[index]; // if all is good, this is it's location
    }
    this.swap(parentIndex, index); // if not, we do a swap
    this.swim(parentIndex); // and recursively repeat until order is maintained.
};
```
Swim is not too complicated so let's walk it through. First we define three terms, a parentIndex. The parentIndex evaluates to dividing the index at which we inserted the value. With that we derive the parent value and also the child value. We run a check to compare the parentIndex and the relationship to the child. If the heap order is violated because a node value is larger than its parent, we fix this by swapping the node (value) with its parent. After this swap the parent is then larger than its children (one is the old parent and the other is smaller because it was a child of the old parent). We recursively keep doing this until we achieve proper ordering.

### Sink

What happens if we need to move a value the other way. It sinks in the array until it finds the right place.

```
BinaryHeap.prototype.sink = function(index){
    let maxIndex = index;
    if(2 * index < this.dataStorage.length && 
        this.dataStorage[2 * index] > this.dataStorage[index]) {
        maxIndex = 2 * index;
    }
    if(2 * index + 1 < this.dataStorage.length &&
        this.dataStorage[2 * index + 1] > this.dataStorage[maxIndex]) {
            maxIndex = 2 * index + 1;
    }
    this.swap(index, maxIndex);
    this.sink(maxIndex);
}
```
It looks complicated but we'll break it down step by step. What we are doing here is setting  value to maxIndex and based on a check of its left and right child we reset that value swap, and recursively keep sinking. 

The first if statement says (remembere we are higher up in the heap/ array and we need to move down) two times this index must be less than the length our array AND the value at this position is greater than the value of it, set this index to 2 * index. This is the left child check.

The next if statement is identical to the above except we are checking against the right child.

After that we performa a swap and then keep recursively calling this.sink() until we arrive at the correct location.

### Remmove Max
 
Even though we know where our max value is located, we have to do a few things to do to maintain order. What do we do if we need to reorder a heap if a value at the top is out of order? Yup, sink.

```
BinaryHeap.prototype.removeMax = function() {
    let max = this.dataStorage[1];
    let lastValue = this.dataStorage.pop();
    if(this.dataStorage.length >= 1) {
        this.dataStorage[1] = lastValue;
        this.sink[1];
    }
    return max;
}
```
With removeMax, we store max value in a variable, pop the very last value at the end of the heap, swap it with where that max value was located, index 1, and then sink that value till it finds a proper location relative to it's parent and its left and right child.

At this point we see with swim and sink how we use these methods to maintain the heap properties.

### Remove

```
BinaryHeap.prototype.remove = function(value) {
    for(let i = 1; i < this.dataStorage.length -1; i++) {
        if(this.dataStorage[i] === value) {
            let index = this.dataStorage.indexOf(value);
            this.swap(index, this.dataStorage.length -1);
            let endValue = this.dataStorage.pop();
            this.swim(index);
            this.sink(index);
        }
    }
    return this;
}

```
The remainder of the Binomial Heap API is size, isEmpty, and the helper function swap.

```
BinaryHeap.prototype.size = function(){
  return this.dataStorage.length -1;
};

BinaryHeap.prototype.isEmpty = function() {
  if (this.dataStorage.length - 1 === 0){
    return true;
  }
  return false;
};

BinaryHeap.prototype.swap = function(index1, index2) {
  let temp = this.dataStorage[index1];
  this.dataStorage[index1] = this.dataStorage[index2];
  this.dataStorage[index2] = temp;
}
```

TODO 
time complexity, visuals, resources, 












