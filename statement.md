# Java 8 Stream Tutorial

### Stream operations

* **intermediate**
  * Return: a stream
  * Ex: `filter` `sorted` `map`
* **terminal** 
  * Return: void or non-stream result
  * Ex: `forEach`


Example:

```java runnable
// { autofold
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;
public class Main {public static void main(String[] args) {
// }

List<String> myList =
    Arrays.asList("a1", "a2", "b1", "c2", "c1");

myList
    .stream()
    .filter(s -> s.startsWith("c"))
    .map(String::toUpperCase)
    .sorted()
    .forEach(System.out::println);

//{ autofold
}}
//}
```

### Processing Order

This doesn't have terminal operation so no output will be displayed.
That is because intermediate operations will only be executed when a terminal operation is present.

```java runnable
// { autofold
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;
public class Main {public static void main(String[] args) {
// }

Stream.of("d2", "a2", "b1", "b3", "c")
    .filter(s -> {
        System.out.println("filter: " + s);
        return true;
    });

//{ autofold
}}
//}
```

The above example have teminal operation

```java runnable
// { autofold
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;
public class Main {public static void main(String[] args) {
// }

Stream.of("d2", "a2", "b1", "b3", "c")
    .filter(s -> {
        System.out.println("filter: " + s);
        return true;
    })
    .forEach(s -> System.out.println("forEach: " + s));

//{ autofold
}}
//}
```

To avoid uncessary calls, instead of executing the operations horizontally we can do it vertically:

```java runnable
// { autofold
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;
public class Main {public static void main(String[] args) {
// }

Stream.of("d2", "a2", "b1", "b3", "c")
    .map(s -> {
        System.out.println("map: " + s);
        return s.toUpperCase();
    })
    .anyMatch(s -> {
        System.out.println("anyMatch: " + s);
        return s.startsWith("A");
    });

//{ autofold
}}
//}
```

The operation `anyMatch` returns `true` as soon as the predicate applies to the given input element. 
This is true for the second element passed "A2". 
Due to the vertical execution of the stream chain, `map` has only to be executed twice in this case. 
So instead of mapping all elements of the stream, `map` will be called as few as possible.

### Reusing Streams
Java 8 streams cannot be reused. As soon as you call any terminal operation the stream is closed:
Calling noneMatch after anyMatch on the same stream results in Exeption.
```java runnable
// { autofold
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;
public class Main {public static void main(String[] args) {
// }

Stream<String> stream =
    Stream.of("d2", "a2", "b1", "b3", "c")
        .filter(s -> s.startsWith("a"));

stream.anyMatch(s -> true);    // ok
stream.noneMatch(s -> true);   // exception

//{ autofold
}}
//}
```

To overcome this limitation we have to to create a new stream chain for every terminal operation we want to execute, e.g. we could create a stream supplier to construct a new stream with all intermediate operations already set up:

```java runnable
// { autofold
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;
public class Main {public static void main(String[] args) {
// }

Supplier<Stream<String>> streamSupplier =
    () -> Stream.of("d2", "a2", "b1", "b3", "c")
            .filter(s -> s.startsWith("a"));

streamSupplier.get().anyMatch(s -> true);   // ok
streamSupplier.get().noneMatch(s -> true);  // ok

//{ autofold
}}
//}
```
Each call to `get()` constructs a new stream on which we are save to call the desired terminal operation.

### Collect
### FlatMap
### Reduce

## Parallel Streams
