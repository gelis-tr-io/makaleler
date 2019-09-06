
# Java Dilinde Test Yazmak


Test yazmak yazılımlarımızdaki en küçük işlem parçalarının beklenen yetenekleri sergileyip sergilemediğini kontrol etmemizi sağlayan yöntemdir.

Basitçe, test yazmayı öğrenip uygulamalarımızda kullanabilmeyi bu yazı ile amaçlıyoruz.


- Junit    [https://junit.org](https://junit.org)
- Hamcrest [http://hamcrest.org](http://hamcrest.org/)
- AssertJ  [http://joel-costigliola.github.io/assertj/](http://joel-costigliola.github.io/assertj/)
- Mockito  [https://mockito.org](https://mockito.org)

Java dilinde yazdığımız kodları test etmek için kullanabileceğimiz bir çatıyı öğrenmeye çalışalım.

# JUnit Framework

JUnit, Java dili ile geliştirilen uygulamalarda  kullanabileceğimiz yazılımda en küçük birimleri test etmemizi sağlayan  bir kütüphanedir.

- https://junit.org/

JUnit testlerinin amacı Java sınıfını ve sahip olduğu tüm bağımlılıkları test etmek değildir.
JUnit testlerinde Java sınıfları izole edilmiş olarak düşünülür ve sınıfların işlevleri test edilir.


Bir örnek ile ilerleyelim.

- https://github.com/junit-team/junit4/wiki/Getting-started#create-a-test

Bir sınıfımız var ve içerisinde bir metot var.

```java
public class Calculator {
  public int evaluate(String expression) {
    int sum = 0;
    for (String summand: expression.split("\\+"))
      sum += Integer.valueOf(summand);
    return sum;
  }
}
```

Sınıfımızın içindeki metodun işlevini test etmek için başka bir sınıf oluşturuyoruz.

```java
import static org.junit.Assert.assertEquals;
import org.junit.Test;

public class CalculatorTest {
  @Test
  public void evaluatesExpression() {
    Calculator calculator = new Calculator();
    int sum = calculator.evaluate("1+2+3");
    assertEquals(6, sum);
  }
}

```

Test eden metodu belirtmek için metodumuz üzerine **@Test** ifadesi ile belirtiyoruz.

Bu ifadeden başka test sınıfları içinde metotlarda kullanılacak başka ifadeler mevcuttur.

```java
 @BeforeClass    sınıf için bir kez ve ilk olarak çalışır
 @Before         her test metodudan önce  
 @Test           test metodunun kendisindir
 @After          her test metodundan sonra
 @AfterClass     sınıf için bir kez ve en son çalışır
```

### Junit Assertion
Yazdığımız test metodumuzun içinde o metottan beklediğimiz durumu kontrol etmek isteriz.
O metot içindeki iş mantıklarını parçalayarak sonuçlarını test etmek isteriz.

- https://github.com/junit-team/junit4/wiki/Assertions 

Kullanılması çok tercih edilenlere bakalım.

```java
 assertEquals()  		--> karşılaştırılan nesne örneğini içlerindeki equals() metodu ile test eder.
 assertSame()  			--> karşılaştırılan nesne örneğini içlerindeki equals() metodu kullanmasan test eder.
 assertNull()  			--> parantez içindeki değerin null olmasını bekler
 assertNotNull()  		--> parantez içindeki değerin null olmamasını bekler
 assertTrue() 			--> parantez içindeki değerin true olmasını bekler 
 assertFalse() 			--> parantez içindeki değerin false olmasını bekler
 assertArrayEquals()    --> parametre olarak verilen dizi karşılaştırılır
```
