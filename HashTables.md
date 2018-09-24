# Data Structures 101: HashTables -- Draft(todo -add graphics)
## The weapon of choice for constant time Get, Set, and Delete operations

Before we start, let's separate the word HashTable into two parts, the hash and the table.
What is a Table? We can classify it as a data type to store information based on a key/ value relationship. Think of a dictionary. You know the word, that's the key, and you use that key to quickly find a value, in our case a definition.

Why do we need or use hashtables? In attempts to solve sertain problems, we often need to make a Time or Space tradeoff. If we had unlimited Time, we can conduct a linear search in an unordered array. If we had unlimited memory, we could hold everything in a massive array and conduct any type of search with only one memory access by using the key. Hashtables provide this compromise.

An array is a type of lookup table. When the keys are simple integers, 0, 1, 2, etc ..., the keys are interchangeable with indexes and at those indexes we store values. Access in this manner is fast because if we have the key, we can quickly find the location in the array. 

What is a hash? A hash is a result of a function that takes an input and transforms it to an array index. Why do we need to do that if we can just use numbers?

The particular index we calculate is mapped to a value. Sometimes, the hash function creates the same key for two or more different inputs. To resolve this "collision" of keys we use two techniques to remedy the conflict. The first is __seperate chaining__ and the second is __linear probing__. We'll touch on those in a minute.

Back to the hash function. The hash function depends on the type of key we pass it. There are many different hash functions, some are best for integers, some for strings, and others are best for crytographic applications. So what makes a good hash function?

The first requirement is to have keys that are evenly spread across the table. This even distribution equates to a uniform probabilty distribution. Over an array of size S, we should be able to have keys from index 0 to index S - 1, to be equally likey. For simplicity, suppose we have an array of size 10, imagine if all of our keys, 10 of them were clustered in the first 4 indexes? We would have many collisions, with that the cost of resolving these collisions increases. By that I mean, we have a key, we use that key to get to a location in the table, surprise!!!, instead of one value at that location, we need to sift through other values to get the one we want.

A perfect hash function would be such that there are no collisions and one value at each location.

What else makes a good hash function?  

### A simple hash function
```
function simpleHash(str, tableSize){
  let hash = 0;
  for (let i = 0; i < str.length; i++) {
    hash += str.charCodeAt(i) * (i + 1);
  }
  return hash % tableSize;
}
```

### HashTable constructor
```
const HashTable = function(tableSize) {
  this.size = tableSize;
  this.dataStorage = [];
  this._count = 0;
};
```

### A helper function to find keys
```
HashTable.prototype.find = function(key) {
  let hash = simpleHash(key, this.size);
  this.dataStorage[hash] = this.dataStorage[hash] || [];
  let bucket = this.dataStorage[hash];
  let match;
  let matchIndex;
  bucket.forEach(function(item, index) {
    if (item.hasOwnProperty(key)) {
      match = item;
      matchIndex = index;
    }
  });
  return {match: match, bucket: bucket, matchIndex: matchIndex};
};
```

### Set a value
``` 
HashTable.prototype.set = function(key, value) {
  let match = this.find(key).match;
  let bucket = this.find(key).bucket;
  if(match) {
    match[key] = value;
  }else{
    let newItem = {};
    newItem[key] = value;
    this._count++;
    bucket.push(newItem);
    if(this._count > 0.75 * this.size) {
      this.resize(2 * this.size);
    }
  }
  return this;
};
```

### Get a value
```
HashTable.prototype.get = function(key) {
  let match = this.find(key).match;
  return match && match[key];
};

```
### Contains
```
HashTable.prototype.has = function(key) {
  return !!this.find(key).match;
};
```

### Delete
```
HashTable.prototype.delete = function(key) {
  let match = this.find(key).match;
  if(match) {
    let bucket = this.find(key).bucket;
    let matchIndex = this.find(key).matchIndex;
    bucket.splice(matchIndex, 1);
    this._count--;
    if(this._count < 0.25 * this.size) {
      this.resize(0.5 * this.size);
    }
  }
  return !!match;
};
```

### Iterating over values
```
HashTable.prototype.forEach = function(callback) {
  this.dataStorage.forEach(function(bucket) {
    bucket = bucket || [];
    bucket.forEach(function(item) {
      callback(item);
    });
  });
};
```

### Resize an array
```
HashTable.prototype.resize = function(newSize) {
  let oldStorage = this.dataStorage;
  this.size = newSize;
  this._count = 0;
  this.dataStorage = [];
  oldStorage.forEach((bucket) => {
    bucket.forEach((item) => {
      let key = Object.keys(item)[0];
      this.set(key, item[key]);
    });
  });
};
```

### Count
```
HashTable.prototype.count = function() {
  return this._count;
};
```
