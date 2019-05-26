# fuzzy-patricia #

**Documentation**: [GoDoc](http://godoc.org/github.com/ozeidan/fuzzy-patricia/patricia)<br />

## About ##
A generic patricia trie (also called radix tree) implemented in Go.

This is a fork of [go-patricia](https://github.com/tchap/go-patricia),
which is optimized for memory consumption and also has slightly better
performance. It additionally provides the functionality for fuzzy and substring
searching on the inserted keys.

The patricia trie as implemented in this package enables fast visiting of items
in the following ways:

1. Visit all items saved in the tree,
2. Visit all items matching particular prefix (visit subtree).
3. Visit items by fuzzy matching a query to the nodes keys.
3. Visit items by searching for a query inside the nodes keys.
3. Given a string, visit all items matching some prefix of that string.

`[]byte` type is used for keys, `interface{}` for values.
`Trie` is not thread safe. Synchronize the access yourself.

## Usage ##

Import the package from GitHub first.

```go
import "github.com/ozeidan/fuzzy-patricia/patricia"
```

You can also import from gopkg.in, recommended when using go modules:

```go
import "gopkg.in/ozeidan/fuzzy-patricia.v3/patricia"
```

Then you can start having fun.

```go
printItem := func(prefix patricia.Prefix, item patricia.Item) error {
	fmt.Printf("%q: %v\n", prefix, item)
	return nil
}

// Create a new default trie (using the default parameter values).
trie := NewTrie()

// Create a new custom trie.
trie := NewTrie(MaxPrefixPerNode(16), MaxChildrenPerSparseNode(10))

// Insert some items.
trie.Insert(Prefix("Pepa Novak"), 1)
trie.Insert(Prefix("Pepa Sindelar"), 2)
trie.Insert(Prefix("Karel Macha"), 3)
trie.Insert(Prefix("Karel Hynek Macha"), 4)

// Just check if some things are present in the tree.
key := Prefix("Pepa Novak")
fmt.Printf("%q present? %v\n", key, trie.Match(key))
// "Pepa Novak" present? true
key = Prefix("Karel")
fmt.Printf("Anybody called %q here? %v\n", key, trie.MatchSubtree(key))
// Anybody called "Karel" here? true

// Walk the tree in alphabetical order.
trie.Visit(printItem)
// "Karel Hynek Macha": 4
// "Karel Macha": 3
// "Pepa Novak": 1
// "Pepa Sindelar": 2

// Walk a subtree.
trie.VisitSubtree(Prefix("Pepa"), printItem)
// "Pepa Novak": 1
// "Pepa Sindelar": 2

// Modify an item, then fetch it from the tree.
trie.Set(Prefix("Karel Hynek Macha"), 10)
key = Prefix("Karel Hynek Macha")
fmt.Printf("%q: %v\n", key, trie.Get(key))
// "Karel Hynek Macha": 10

// Walk prefixes.
prefix := Prefix("Karel Hynek Macha je kouzelnik")
trie.VisitPrefixes(prefix, printItem)
// "Karel Hynek Macha": 10

// Delete some items.
trie.Delete(Prefix("Pepa Novak"))
trie.Delete(Prefix("Karel Macha"))

// Walk again.
trie.Visit(printItem)
// "Karel Hynek Macha": 10
// "Pepa Sindelar": 2

// Delete a subtree.
trie.DeleteSubtree(Prefix("Pepa"))

// Print what is left.
trie.Visit(printItem)
// "Karel Hynek Macha": 10
```

## License ##
MIT, check the `LICENSE` file.
