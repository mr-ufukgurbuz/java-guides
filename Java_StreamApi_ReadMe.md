# Java Stream API Rehberi

Java Stream API, Java 8 ile birlikte gelen ve koleksiyonlar üzerinde fonksiyonel programlama stilinde işlemler yapmamızı sağlayan bir API'dir. Stream API, koleksiyonları işlemek için bir dizi yöntem sunar ve bu yöntemler zincirleme olarak kullanılabilir. Bu rehberde, Stream API'nin temel metodlarını ve her bir metodun nasıl kullanıldığını örneklerle açıklayacağız.

---

## 1. Stream Oluşturma

Stream API'yi kullanabilmek için öncelikle bir `Stream` nesnesi oluşturmamız gerekiyor. Bu, bir koleksiyon, dizi veya başka bir kaynaktan yapılabilir.

### 1.1. `Stream.of()`
Bu metod, belirli elemanlardan bir `Stream` oluşturur. Özellikle küçük veri setleri için kullanışlıdır.

```java
Stream<String> stream = Stream.of("a", "b", "c");
stream.forEach(System.out::println); // Çıktı: a b c
```

### 1.2. `Collection.stream()`
Bir koleksiyon (örneğin, `List`, `Set`) üzerinde `stream()` metodu çağrılarak bir `Stream` oluşturulabilir.

```java
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream();
stream.forEach(System.out::println); // Çıktı: a b c
```

### 1.3. `Arrays.stream()`
Bir dizi üzerinde `Arrays.stream()` metodu kullanılarak `Stream` oluşturulabilir.

```java
String[] array = {"a", "b", "c"};
Stream<String> stream = Arrays.stream(array);
stream.forEach(System.out::println); // Çıktı: a b c
```

### 1.4. `Stream.iterate()`
Bu metod, belirli bir başlangıç değeri ve bir fonksiyon kullanarak sonsuz bir `Stream` oluşturur. `limit()` ile sınırlandırılabilir.

```java
Stream<Integer> stream = Stream.iterate(0, n -> n + 2).limit(5);
stream.forEach(System.out::println); // Çıktı: 0 2 4 6 8
```

### 1.5. `Stream.generate()`
Bu metod, bir `Supplier` fonksiyonu kullanarak sonsuz bir `Stream` oluşturur. `limit()` ile sınırlandırılabilir.

```java
Stream<Double> stream = Stream.generate(Math::random).limit(5);
stream.forEach(System.out::println); // Çıktı: Rastgele 5 sayı
```

---

## 2. Ara İşlemler (Intermediate Operations)

Ara işlemler, bir `Stream` üzerinde işlem yapıp yeni bir `Stream` döndürür. Bu işlemler "lazy"dir, yani terminal işlemi çağrılana kadar çalışmazlar.

### 2.1. `filter()`
Belirli bir koşulu sağlayan elemanları filtreler.

```java
List<String> list = Arrays.asList("apple", "banana", "cherry", "date");
list.stream()
    .filter(s -> s.startsWith("a"))
    .forEach(System.out::println); // Çıktı: apple
```

### 2.2. `map()`
Her elemanı başka bir forma dönüştürür.

```java
List<String> list = Arrays.asList("a", "b", "c");
list.stream()
    .map(String::toUpperCase)
    .forEach(System.out::println); // Çıktı: A B C
```

### 2.3. `flatMap()`
İç içe koleksiyonları düzleştirir (flatten).

```java
List<List<String>> listOfLists = Arrays.asList(
    Arrays.asList("a", "b"),
    Arrays.asList("c", "d")
);
listOfLists.stream()
    .flatMap(List::stream)
    .forEach(System.out::println); // Çıktı: a b c d
```

### 2.4. `distinct()`
Yinelenen elemanları kaldırır.

```java
List<String> list = Arrays.asList("a", "b", "a", "c");
list.stream()
    .distinct()
    .forEach(System.out::println); // Çıktı: a b c
```

### 2.5. `sorted()`
Elemanları doğal sıralamasına göre sıralar.

```java
List<String> list = Arrays.asList("c", "a", "b");
list.stream()
    .sorted()
    .forEach(System.out::println); // Çıktı: a b c
```

### 2.6. `peek()`
Her eleman üzerinde bir işlem yapar, ancak `Stream`'i değiştirmez. Genellikle debug amaçlı kullanılır.

```java
List<String> list = Arrays.asList("a", "b", "c");
list.stream()
    .peek(System.out::println) // Ara işlemde elemanları yazdırır
    .map(String::toUpperCase)
    .forEach(System.out::println); // Çıktı: A B C
```

### 2.7. `limit()`
Belirli sayıda elemanı sınırlar.

```java
Stream<Integer> stream = Stream.iterate(0, n -> n + 1);
stream.limit(5)
    .forEach(System.out::println); // Çıktı: 0 1 2 3 4
```

### 2.8. `skip()`
Belirli sayıda elemanı atlar.

```java
Stream<Integer> stream = Stream.iterate(0, n -> n + 1);
stream.skip(5)
    .limit(5)
    .forEach(System.out::println); // Çıktı: 5 6 7 8 9
```

---

## 3. Terminal İşlemler (Terminal Operations)

Terminal işlemleri, bir `Stream` üzerinde işlem yapıp sonuç üretir. Terminal işlemi çağrıldıktan sonra `Stream` tüketilir ve artık kullanılamaz.

### 3.1. `forEach()`
Her eleman üzerinde bir işlem yapar.

```java
List<String> list = Arrays.asList("a", "b", "c");
list.stream()
    .forEach(System.out::println); // Çıktı: a b c
```

### 3.2. `collect()`
Elemanları bir koleksiyona toplar.

```java
List<String> list = Arrays.asList("a", "b", "c");
List<String> result = list.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
System.out.println(result); // Çıktı: [A, B, C]
```

### 3.3. `toArray()`
Elemanları bir diziye dönüştürür.

```java
List<String> list = Arrays.asList("a", "b", "c");
String[] array = list.stream()
    .toArray(String[]::new);
System.out.println(Arrays.toString(array)); // Çıktı: [a, b, c]
```

### 3.4. `reduce()`
Elemanları birleştirerek tek bir sonuç üretir.

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4);
Optional<Integer> result = list.stream()
    .reduce((a, b) -> a + b);
result.ifPresent(System.out::println); // Çıktı: 10
```

### 3.5. `min()`
En küçük elemanı bulur.

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4);
Optional<Integer> min = list.stream()
    .min(Integer::compareTo);
min.ifPresent(System.out::println); // Çıktı: 1
```

### 3.6. `max()`
En büyük elemanı bulur.

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4);
Optional<Integer> max = list.stream()
    .max(Integer::compareTo);
max.ifPresent(System.out::println); // Çıktı: 4
```

### 3.7. `count()`
Eleman sayısını döndürür.

```java
List<String> list = Arrays.asList("a", "b", "c");
long count = list.stream()
    .count();
System.out.println(count); // Çıktı: 3
```

### 3.8. `anyMatch()`
Herhangi bir eleman koşulu sağlıyorsa `true` döndürür.

```java
List<String> list = Arrays.asList("a", "b", "c");
boolean anyMatch = list.stream()
    .anyMatch(s -> s.startsWith("a"));
System.out.println(anyMatch); // Çıktı: true
```

### 3.9. `allMatch()`
Tüm elemanlar koşulu sağlıyorsa `true` döndürür.

```java
List<String> list = Arrays.asList("a", "b", "c");
boolean allMatch = list.stream()
    .allMatch(s -> s.startsWith("a"));
System.out.println(allMatch); // Çıktı: false
```

### 3.10. `noneMatch()`
Hiçbir eleman koşulu sağlamıyorsa `true` döndürür.

```java
List<String> list = Arrays.asList("a", "b", "c");
boolean noneMatch = list.stream()
    .noneMatch(s -> s.startsWith("z"));
System.out.println(noneMatch); // Çıktı: true
```

### 3.11. `findFirst()`
İlk elemanı döndürür.

```java
List<String> list = Arrays.asList("a", "b", "c");
Optional<String> first = list.stream()
    .findFirst();
first.ifPresent(System.out::println); // Çıktı: a
```

### 3.12. `findAny()`
Herhangi bir elemanı döndürür (genellikle paralel stream'lerde kullanılır).

```java
List<String> list = Arrays.asList("a", "b", "c");
Optional<String> any = list.stream()
    .findAny();
any.ifPresent(System.out::println); // Çıktı: a (veya b veya c)
```

---

## 4. Özel Stream Türleri

### 4.1. `IntStream`
```java
IntStream.range(1, 5)
    .forEach(System.out::println); // Çıktı: 1 2 3 4
```

### 4.2. `LongStream`
```java
LongStream.range(1, 5)
    .forEach(System.out::println); // Çıktı: 1 2 3 4
```

### 4.3. `DoubleStream`
```java
DoubleStream.of(1.0, 2.0, 3.0)
    .forEach(System.out::println); // Çıktı: 1.0 2.0 3.0
```

---

## 5. Paralel Stream

Stream API, paralel işlemler yapmak için `parallel()` metodunu sunar.

```java
List<String> list = Arrays.asList("a", "b", "c");
list.parallelStream()
    .forEach(System.out::println); // Çıktı: a, b, c (sıralama garanti değil)
```

---

## 6. Örnek Uygulama

Aşağıda, Stream API'nin birçok metodunu bir arada kullanan bir örnek bulunmaktadır:

```java
List<String> list = Arrays.asList("apple", "banana", "cherry", "date", "elderberry");

List<String> result = list.stream()
    .filter(s -> s.length() > 5)
    .map(String::toUpperCase)
    .sorted()
    .collect(Collectors.toList());

System.out.println(result); // Çıktı: [BANANA, CHERRY, ELDERBERRY]
```

---

Bu rehber, Java Stream API'nin temel metodlarını ve kullanım örneklerini kapsamlı bir şekilde açıklamaktadır. Stream API'yi etkili bir şekilde kullanarak, kodunuzu daha okunabilir ve bakımı kolay hale getirebilirsiniz.