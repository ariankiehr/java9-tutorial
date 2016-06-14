# A Guide to Java 9

This tutorial guides you through all the new features Java 9 has and it explain it with code snippets. 

## Table of Contents

* [New Collections APIs](#new-collections-apis)
  * [Populating structures](#populating-structures)
  * [Arrays new methods](#arrays-new-methods)
  * [New Streams operations](#new-streams-operations)


## New Collections APIs

### Populating structures

Java 9 comes with A LOT of static methods defined in the interfaces of the structures like `List`, `Set` and `Map`. These static methods offers a easy way to populate the structures, lets see some examples compared to the Java 8 way.

```java
  //Java 8
  List<String> list = Arrays.asList("a", "b", "c");
  //Java 9
  List<String> listJava9 = List.of("a", "b" , "c");
	
  //Java 8
  Set<String> setJava8 = new HashSet<String>(Arrays.asList("a", "b", "c"));
  //Java 9
  Set<String> setJava9 = Set.of("a", "b", "c");

  //Java 8
  Map<Integer, String> mapJava8 = new HashMap<Integer, String>();
  mapJava8.put(1, "a");
  mapJava8.put(2, "b");
  mapJava8.put(3, "c");

  //Java 8 (sorter)
  Map<Integer, String> mapJava8Sorter = new HashMap<Integer, String>() {
    {
      put(1, "a");
      put(2, "b");
      put(3, "c");
    }
  };
  
  //Java 9
  Map<Integer, String> mapJava9 = Map.of(1, "a", 2, "b", 3, "c");
	
  //Java 9 (If you want to put more than 10 elements)
  Map<Integer, String> mapJava9MoreThan10 = Map.ofEntries(
    Map.entry(1, "a"), 
    Map.entry(2, "b"), 
    Map.entry(3, "c")
  );
```

So the method `of` seems a good alternative the populate structures more handy. I said that this new APIs had a lot a new methods but only show you `of` (and `ofEntries`), the thing is, it exist one method of for each number or arguments you pass to it, and up to ten elements the varargs are used, but in the case of `Map`, the varargs are not a solution, so that's why we need to use the method `ofEntries`.

### Arrays new methods

The utility class `Arrays` now comes with four new methods: `equals`, `compare`, `compareUnsigned` and `mismatch`. While those names sounds familiar the parameters are different, here's what they do.

```java
  int[] a = {1,2,-7,6,8};
  int[] b = {1,9,7,1,2};

  System.out.println( Arrays.equals(a, 0, 1, b, 3, 4) );
  // true because the first two elements of a are equals to the last two of b
  
  System.out.println( Arrays.compare(a, 0, 1, b, 3, 4) );
  // 0 because the specified ranges are equals
  
  System.out.println( Arrays.compare(a, b) );
  // -1 because 1 is lesser than 6
  
  System.out.println( Arrays.compareUnsigned(a, 2, 2, b, 2, 2) );
  // 0 because unsigned -7 is equals to 7
  
  System.out.println( Arrays.mismatch(a, b) );
  // 1 because the index of the first mismatch (2 and 9) you can specify ranges
```

### New Streams operations

Streams added in Java 8 now had new operators. These are `takeWhile`, `dropWhile`, `ofNullable` and `iterate`.

`takeWhile` and `dropWhile` are well known operations in other programming languages like Haskell. `takeWhile` Returns a stream consisting of the longest prefix of elements from the original stream that match a given predicate.

```java
  List<Integer> list = List.of(0, 1, 2, -1, -2);
  list.stream().takeWhile(x -> x <= 1).forEach(System.out::println);
  // 0 1
```

As you can see, the `forEach` operator only applied for the first and second element of the stream, because the third element is greater than 1 and not match the `takeWhile` predicate (`x <= 1`), also, because of that, numbers like -1 are ignored since `takeWhile` returns only the longest prefix. This change in unordered streams, in this case `takeWhile` returns a subset of elements that match the given predicate.

```java
  Set<Integer> set = Set.of(0, 1, 2, -1, -2);
  set.stream().takeWhile(x -> x <= 1).forEach(System.out::println);
  // 0 -1 1 -2
```

`dropWhile` works similary to `takeWhile`. For ordered streams, it returns a stream consisting of the remaining elements of this stream after dropping the longest prefix of elements that match the given predicate, and for unordered streams, a stream consisting of the remaining elements of this stream after dropping a subset of elements that match the given predicate.

```java
  List<Integer> list = List.of(0, 1, 2, -1, -2);
  list.stream().dropWhile(x -> x <= 1).forEach(System.out::println);
  // 2 -1 -2
  
  Set<Integer> set = Set.of(0, 1, 2, -1, -2);
  set.stream().dropWhile(x -> x <= 1).forEach(System.out::println);
  // 2
```

`ofNullable` returns a stream containing a single non-null element, if the element is null then returns an empty stream.

```java
  List<Integer> indexes = List.of(0, 1, 2, -1, -2);
  
  //I hope you already read about the "of" method
  Map<Integer, List<String>> map = Map.of(
    1, List.of("bar", "foo"), 
    2, List.of("foo", "baz"), 
    3, List.of("baz", "bar")
  );

  indexes.stream().flatMap(index -> Stream.ofNullable(map.get(index))).forEach(System.out::println);

  //[bar, foo]
  //[foo, baz]
```

Notice how `map.get(index)` with index != 1 or 2 creates a empty stream that doesn't affect the execution of our program.

The `iterate` operator already exist in Java 8, but in Java 9 we can pass a Predicate to limit the execution of the `iterate` operator in a similar fashion of `takeWhile` and `dropWhile` works.

```java
	//Java 8
	Stream.iterate(1, n -> n+1).limit(10).forEach(System.out::print); // 1 2 3 4 5 6 7 8 9 10
	//Java 9
	Stream.iterate(1, n -> n <= 10, n -> n + 1).forEach(System.out::print); // 1 2 3 4 5 6 7 8 9 10
```
