# Collections - Paired Iterator Library

A Java library that provides `PairedIterator` and `PairedIterable` utilities for
iterating over two collections of different types simultaneously.

## Motivation

The standard Java Collections Framework and Apache Commons Collections lack
native support for "zipping" iterators of different types. While Apache Commons
Collections provides a [`ZippingIterator`](https://commons.apache.org/proper/commons-collections/apidocs/org/apache/commons/collections4/iterators/ZippingIterator.html),
it only works with iterators of the same type.

There is a [`Streams.zip`](https://guava.dev/releases/23.0/api/docs/com/google/common/collect/Streams.html) 
in Google Guava library which works only with Streams and not with Iterators/Iterables, also lacks
an inbuilt `Pair` class.

This library addresses that limitation by introducing `PairedIterator` and
`PairedIterable`, which allow you to iterate over two collections with
different element types in tandem. This functionality is particularly useful
when:

- Working with parallel data structures (e.g., IDs and names)
- Processing related data from different sources
- Combining heterogeneous collections for transformation or analysis

This implementation is based on my original proposal [COLLECTIONS-795](https://issues.apache.org/jira/browse/COLLECTIONS-795)
and [PR#442](https://github.com/apache/commons-collections/pull/412)

## Features

- **Type-Safe Iteration**: Full generic type support for left and right iterators
- **Flexible Construction**: Create from `Iterator` or `Iterable` sources
- **Stream Support**: Convert to Java streams via `PairedIterable`
- **Early Termination**: Automatically stops when either iterator is exhausted
- **Immutable Results**: Returns immutable `PairedItem` tuples
- **Zero Dependencies**: Requires Java 17+ and Apache Commons Collections 4.5+

## Requirements

- Java 17 or higher
- Apache Commons Collections 4.5.0+

## Installation

### Gradle

Add source dependencies to your Gradle project

In `settings.gradle`:
```gradle
sourceControl {
    gitRepository("https://github.com/anantdamle/collections.git") {
        producesModule("xyz.damle.collections:collections")
    }
}
```

In `build.gradle`:
```gradle
dependencies {
    implementation 'xyz.damle.collections:collections:0.0.1'
}
```

## Usage

### Basic Iterator Usage

```java
import xyz.damle.collections.iterators.PairedIterator;
import xyz.damle.collections.iterators.PairedIterator.PairedItem;

List<Integer> studentIds = List.of(1001, 1002, 1003);
List<String> studentNames = List.of("Alice", "Bob", "Charlie");

PairedIterator<Integer, String> pairedIterator =
    PairedIterator.ofIterables(studentIds, studentNames);

while (pairedIterator.hasNext()) {
    PairedItem<Integer, String> item = pairedIterator.next();
    System.out.println(
        "ID: " + item.leftItem() + ", Name: " + item.rightItem());
}
```

### Using PairedIterable with For-Each

```java
import xyz.damle.collections.PairedIterable;
import xyz.damle.collections.iterators.PairedIterator.PairedItem;

List<Integer> studentIds = List.of(1001, 1002, 1003);
List<String> studentNames = List.of("Alice", "Bob", "Charlie");

for (var item : PairedIterable.of(studentIds, studentNames)) {   
    System.out.println("ID: " + item.leftItem() + ", Name: " + item.rightItem());
}
```

### Stream Support

```java
import xyz.damle.collections.PairedIterable;

List<Integer> studentIds = List.of(1001, 1002, 1003);
List<String> studentNames = List.of("Alice", "Bob", "Charlie");

PairedIterable.of(studentIds, studentNames)
    .stream()
    .filter(item -> item.leftItem() > 1001)
    .forEach(item -> System.out.println(item.rightItem()));
```

### Creating from Iterators Directly

```java
Iterator<Integer> idIterator = ...;
Iterator<String> nameIterator = ...;

PairedIterator<Integer, String> pairedIterator =
    PairedIterator.of(idIterator, nameIterator);
```

## API Overview

### PairedIterator

- `PairedIterator.of(Iterator<L> left, Iterator<R> right)` - Create from two
  iterators
- `PairedIterator.ofIterables(Iterable<L> left, Iterable<R> right)` - Create
  from two iterables
- `hasNext()` - Returns true if both iterators have remaining elements
- `next()` - Returns next `PairedItem<L, R>` containing elements from both
  iterators

### PairedIterable

- `PairedIterable.of(Iterable<L> left, Iterable<R> right)` - Create paired
  iterable
- `iterator()` - Returns a `PairedIterator`
- `stream()` - Returns a `Stream<PairedItem<L, R>>`

### PairedItem

- `leftItem()` - Get the left element
- `rightItem()` - Get the right element
- `toString()` - Returns string representation: `{leftItem, rightItem}`

## Behavior Notes

1. **Early Termination**: Iteration stops when *either* iterator is exhausted
2. **Null Safety**: Constructor parameters are null-checked
3. **Immutability**: `PairedItem` is implemented as a Java record and is
   immutable
4. **No Remove Support**: The `remove()` operation is not supported

## Building from Source

```bash
./gradlew build
```

### Running Tests

```bash
./gradlew test
```

### Code Coverage

```bash
./gradlew jacocoTestReport
```

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for
details.

## Contributing

This library is based on a contribution to Apache Commons Collections. For
issues or improvements, please open an issue on the repository.

## Author

Anant Damle
