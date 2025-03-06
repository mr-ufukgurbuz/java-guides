# Java Optional Kullanımı - Kapsamlı Rehber

Java 8 ile birlikte gelen `Optional<T>` sınıfı, `null` değerlerden kaynaklanan hataları önlemek ve daha güvenli kod yazmak için kullanılır. Bu rehberde `Optional` ile ilgili tüm detayları ele alacağız.

## 1. Optional Nedir?

`Optional<T>` sınıfı, bir değişkenin değer içerip içermediğini belirlemek için kullanılır. `null` değerlerden kaynaklanan `NullPointerException` hatalarını önlemek için `if (obj != null)` gibi kontroller yerine daha fonksiyonel bir yaklaşım sunar.

---

## 2. Optional Nasıl Oluşturulur?

### 2.1. `Optional.of()`

- **Null Kabul Etmez:** Bu metot, kesinlikle `null` olmayan bir değer almak için kullanılır. Eğer `null` bir değer geçilirse `NullPointerException` fırlatır.

- **Dolu Optional Oluşturur:** Geçirilen değer null değilse, dolu bir Optional nesnesi oluşturur.

```java
// Örnek Kullanım
Optional<String> doluOptional = Optional.of("Merhaba Dünya"); // Null değilse dolu Optional döndürür

Optional<String> nullOptional = Optional.of(null); // NullPointerException fırlatır
```

### 2.2. `Optional.ofNullable()`

- **Null Kabul Eder:** Bu metot, eğer değer `null` ise boş bir `Optional` döndürür, değilse değeri içeren bir `Optional` döndürür.

- **Boş Optional Oluşturur:** Geçirilen değer null ise, boş bir Optional nesnesi oluşturur. Null değilse, dolu bir Optional döndürür.


```java
// Örnek Kullanım
Optional<String> doluOptional = Optional.ofNullable("Merhaba Dünya"); // Null değilse dolu Optional döndürür
Optional<String> bosOptional = Optional.ofNullable(null); // Boş Optional döndürür
```

### 2.3. `Optional.empty()`

Bu metot, boş bir `Optional` nesnesi oluşturur.

```java
Optional<String> emptyOptional = Optional.empty();
System.out.println(emptyOptional.isPresent()); // false
```

---

## 3. Optional Metodları

### 3.1. `map()` ve `flatMap()`

- **`map()`**: Optional içindeki değeri dönüştürmek için kullanılır. Ancak dönüşüm sonrası bir `Optional<T>` değil, doğrudan `T` döndürür.
- **`flatMap()`**: Eğer dönüşüm sonrası zaten bir `Optional<T>` dönüyorsa, iç içe `Optional` oluşmasını engelleyerek düz bir yapı sağlar.

**`map()` Örneği:**
```java
Optional<String> optional = Optional.of("Merhaba");
Optional<Integer> lengthOptional = optional.map(String::length);
System.out.println(lengthOptional.get()); // 7
```

**`flatMap()` Örneği:**
```java
Optional<Optional<String>> nestedOptional = Optional.of(Optional.of("İç içe"));
Optional<String> flattened = nestedOptional.flatMap(opt -> opt);
System.out.println(flattened.get());
```

**Fark:** Eğer `map()` kullanılsaydı, `Optional<Optional<String>>` gibi iç içe bir yapı oluşurdu. `flatMap()` bunu düzleştirerek doğrudan `Optional<String>` döndürür.

### 3.2. `stream()`

`Optional` içindeki değeri bir `Stream` olarak kullanmak için `stream()` metodu kullanılır. Eğer değer varsa tek elemanlı bir `Stream` döner, yoksa boş bir `Stream` döndürülür. Bu, `Stream` işlemlerinde `null` kontrollerini azaltarak kodun daha temiz olmasını sağlar.

```java
Optional<String> optional = Optional.of("Java");
optional.stream().forEach(System.out::println); // "Java"
```

**Örnek: Bir Liste İçindeki `Optional` Değerleri Filtrelemek**
```java
List<Optional<String>> optionalList = List.of(
    Optional.of("Değer 1"),
    Optional.empty(),
    Optional.of("Değer 2")
);

List<String> values = optionalList.stream()
    .flatMap(Optional::stream) // Boş olanları filtreler
    .collect(Collectors.toList());

System.out.println(values); // ["Değer 1", "Değer 2"]
```

Bu örnekte, `Optional::stream` kullanarak boş `Optional` değerleri filtreleyip sadece dolu olanları topluyoruz.

---

## 4. Metotlarda Optional Kullanımı

### 4.1. Parametre Olarak Optional Kullanımı

Genellikle metot parametresi olarak `Optional` kullanılmaz. Bunun yerine, doğrudan `null` kontrolü yaparak method overloading tercih edilmelidir.

**Yanlış Kullanım:**
```java
public void printName(Optional<String> name) {
    System.out.println(name.orElse("Bilinmiyor"));
}
```

**Doğru Kullanım:**
```java
public void printName(String name) {
    System.out.println(name != null ? name : "Bilinmiyor");
}
```

### 4.2. Dönüş Tipi Olarak Optional Kullanımı

Metotların dönüş tipi olarak `Optional<T>` kullanmak `null` dönüşlerini önler ve çağrıcıya değerin olup olmadığını bildirmek için iyi bir yaklaşımdır.

**Doğru Kullanım:**
```java
public Optional<String> findUserById(int id) {
    return id == 1 ? Optional.of("Kullanıcı") : Optional.empty();
}
```

**Kullanımı:**
```java
Optional<String> user = findUserById(1);
user.ifPresent(System.out::println);
```

Eğer bir metot `Optional<T>` döndürüyorsa, çağıran taraf `get()` kullanmak yerine `orElse()`, `orElseGet()`, veya `ifPresent()` gibi güvenli metodları tercih etmelidir.

---

## 5. Hatalı Optional Kullanımları

`Optional`, her yerde kullanılmamalıdır. Özellikle şunlara dikkat edilmelidir:
- Alan değişkenleri (instance variables) için kullanılmamalıdır.

Yanlış kullanım örneği:
```java
class User {
    private Optional<String> name; // Yanlış kullanım!
}
```

- `Optional` parametre olarak kullanılmamalıdır. Bunun yerine doğrudan `null` kontrolleri veya method overloading tercih edilmelidir.
- `get()` metodu doğrudan kullanılmamalıdır; bunun yerine `orElse()`, `orElseGet()` veya `orElseThrow()` tercih edilmelidir.
- `Optional` koleksiyon içinde kullanılmamalıdır. Bunun yerine koleksiyonun kendisi boş olup olmadığı kontrol edilmelidir.

```java
List<Optional<String>> list = List.of(Optional.of("Test"), Optional.empty());
// Bunun yerine boş olmayan değerleri içeren bir liste kullanılmalıdır.
```

---
## 6. Optional ve Serialization

`Optional` nesneleri doğrudan serialize edilemez çünkü `Optional` `Serializable` arayüzünü implement etmez. Eğer bir sınıfta `Optional` kullanılıyorsa, bu alanlar serialize edilmeyecek şekilde ayarlanmalıdır veya `Optional`'ın içindeki değerin serialize edilmesi sağlanmalıdır.

Örnek:
```java
class Person implements Serializable {
    private transient Optional<String> name; // Optional serileştirilemez
    
    private String getName() {
        return name.orElse("");
    }
}
```
---
## 7. Sonuç

`Optional`, `null` kontrollerini azaltarak daha okunaklı ve güvenli kod yazmamızı sağlar. Ancak yanlış kullanımı performans ve bakım açısından sorunlar yaratabilir. `Optional`, dönüş tipi olarak kullanıldığında oldukça faydalı olurken, parametre olarak kullanımı genellikle önerilmez.
