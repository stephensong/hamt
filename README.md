# HAMT
Javascript Hash Array Mapped Trie

### Overview
The [hash array mapped trie][hash-array-mapped-trie] is a [persistent][persistent]
map data structure with good lookup and update performance. This
Javascript implementation is based on [exclipy's Haskell port][pdata].

### Important Note
Unless you are interested in hash array mapped trie implementation, you probably
want to use [hashtrie][hashtrie] instead. Hashtrie is a drop in replacement for HAMT,
has a richer api, and [will perform better for updates and gets][benchmarks].

Hash array mapped tries are a rather pointless space optimization in a high level
scripting language like Javascript. And this HAMT implementation actually uses
more memory than hashtrie.

The big exception is fold operations, which HAMT is significantly faster than 
hashtrie. HAMT index nodes are stored in a dense array, while hashtrie's sparse
arrays have [a lot of overhead for folds][http://jsperf.com/sparse-array-reduce-overhead].

## Install

### Node
Node source is in `dist_node/hamt.js`

```
$ npm install hamt
```

### Amd
Amd source is in `dist/hamt.js`

```
requirejs.config({
    paths: {
        'hamt': 'dist/hamt'
    }
});

require([
    'hamt'],
function(hamt) {
    ...
});
```

## Usage

```
var hamt = require('hamt');

// empty table
var h = hamt.empty;

// Set 'key' to 'value'
h = hamt.set('key', 'value', h);

// get 'key'
hamt.get('key', h); // 'value'


// The data structure is persistent so the original is not modified.
var h1 = hamt.set('a', 'x', hamt.empty);
var h2 = hamt.set('b', 'y', h1);

hamt.get('a', h1); // 'x'
hamt.get('b', h1); // null
hamt.get('a', h2); // 'x'
hamt.get('b', h2); // 'y'


// modify an entry
h2 = hamt.modify('b', function(x) { return x + 'z'; }, h2);
hamt.get('b', h2); // 'yz'

// remove an entry
h2 = hamt.remove('y', h2);
hamt.get('a', h2); // 'x'
hamt.get('b', h2); // null


// Custom hash Function
// The main Hamt API expects all keys to be strings. Versions of all API functions
// that take a `hash` parameter are also provided so custom hashes and keys can be used.

// Collisions are correctly handled
var h1 = hamt.setHash(0, 'a', 'x', hamt.empty);
var h2 = hamt.setHash(0, 'b', 'y', h1);

hamt.get('a', h2); // 'x'
hamt.get('b', h2); // 'y'
```


[hashtrie]: https://github.com/mattbierner/hashtrie
[benchmarks]: http://github.com/mattbierner/js-hashtrie-benchmark
[pdata]: https://github.com/exclipy/pdata
[hash-array-mapped-trie]: http://en.wikipedia.org/wiki/Hash_array_mapped_trie
[persistent]: http://en.wikipedia.org/wiki/Persistent_data_structure