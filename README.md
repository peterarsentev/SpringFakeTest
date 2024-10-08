# SpringFakeTest

`SpringFakeTest` is a testing utility library designed to simplify testing Spring-based applications by providing fake implementations of Spring Data repositories. This library allows you to create in-memory repositories that can be used for testing your services without the need for a real database.

## Features

- **In-Memory CRUD Operations:** Perform CRUD operations using in-memory fake repositories.
- **Easy Integration:** Seamlessly integrate with your Spring services for testing.
- **Mock Repository Behavior:** Create and use fake repositories to test services that rely on Spring Data repositories.

## Installation

To use `SpringFakeTest` in your project, add the following Maven repository and dependency to your `pom.xml`:

```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>

<dependency>
    <groupId>com.github.peterarsentev</groupId>
    <artifactId>SpringFakeTest</artifactId>
    <version>v.1.0.3</version>
</dependency>
```

## Usage

To use `SpringFakeTest`, extend the provided `CrudRepositoryFake` class in your custom repository implementation. Here's an example of how to use the fake repository in a test case:

### Example

```kotlin
class FilterFakeRepository : CrudRepositoryFake<Filter, Int>(), FilterRepository
```

```kotlin
class KeyFakeRepository : CrudRepositoryFake<Key, Int>(), KeyRepository {
   override fun findByFilterId(filterId: Int): List<Key>
           = memory.filter { it.value.filter.id == filterId }
      .map { it.value }
      .toList()
}
```

```kotlin
class KValueFakeRepository : CrudRepositoryFake<KValue, Int>(), KValueRepository {
   override fun findByKeyIdIn(keys: List<Int>): List<KValue>
           = memory.filter { keys.contains(it.value.key.id) }
      .map { it.value }
      .toList()

   override fun update(value: String, id: Int) {
      TODO("Not yet implemented")
   }

   override fun findByKeyFilterId(filterId: Int): List<KValue>
           = memory.filter { it.value.key.filter.id == filterId }
      .map { it.value }
      .toList()

   override fun findByKeyId(keyId: Int): List<KValue>
           = memory.filter { it.value.key.id == keyId }
      .map { it.value }
      .toList()
}
```

```kotlin
@Test
fun whenEnglishTextIsSpamThenTrue() {
    val filterRepository = FilterFakeRepository()
    val filterService = FilterService(filterRepository)
    val keyRepository = KeyFakeRepository()
    val keyService = KeyService(keyRepository)
    val kvalueRepository = KValueFakeRepository()
    val kvalueService = KValueService(kvalueRepository)
    
    val filter = filterRepository.save(Filter(1))
    val keyJob = keyRepository.save(Key(1, filter))
    kvalueRepository.save(KValue(1, keyJob, "trading"))
    val keyMessage = keyRepository.save(Key(2, filter))
    kvalueRepository.save(KValue(2, keyMessage, "interested"))
    
    val text = ", I saw a post about your platform and I took a step to try if it’s going to work you changed my life financially \n" +
               "Why not use your 5 to 6 hours to learn how  forxe trading market , work while waiting for your salary \n" +
               "5 people are needed in this group to come and  learn forxes trading market online , to earn \$5,520 in 2 business days if interested ask how ⬇\uFE0F⬇\uFE0F\n" +
               "CLICK ON LINK (http://t.me/+qXa2EeAOVUw0ZDY0)";
    
    assertThat(SpamAnalysis(filterService, keyService, kvalueService).isSpam(text).spam).isTrue()
}
```

### Steps:

1. **Create a Fake Repository:**
   Extend `CrudRepositoryFake` to create a fake implementation of your repository interface.

2. **Use in Tests:**
   Create instances of your fake repositories and use them to instantiate the services you want to test.

3. **Perform Assertions:**
   Use the fake repository to save entities and verify the behavior of your service methods.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a pull request or open an issue for any bugs or feature requests.
