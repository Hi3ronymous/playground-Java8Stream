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

The operation anyMatch returns true as soon as the predicate applies to the given input element. This is true for the second element passed "A2". Due to the vertical execution of the stream chain, map has only to be executed twice in this case. So instead of mapping all elements of the stream, map will be called as few as possible.
