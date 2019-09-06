
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

### Junit Parameter

Bir test metodunu farklı test parametreleri ile tekrar terkra test etmek isteyebiliriz. 
Bu durumda aynı metodun farklı veri parametresi alan hallerini yazmak yerine şu kütüphaneden yararlanabiliriz.


- http://pragmatists.github.io/JUnitParams 

- https://github.com/Pragmatists/JUnitParams 

```java
@Parameters("a","b","c") yazımı ile  metoda gönderilerek parametreleri tek noktadan yönetebiliyoruz.

```

### Junit Exception

Bir işlem içerisinden istediğimiz bir istisna (exception) durumunu oluşturup test edebiliriz.

Örneğin listede olmayan bir değeri çağırdığımızda bu durumu anlamak ve yönetmek istiyoruz.

- https://github.com/junit-team/junit4/wiki/Exception-testing#expectedexception-rule

```java
@Rule
public ExpectedException thrown = ExpectedException.none();

@Test
public void shouldTestExceptionMessage() throws IndexOutOfBoundsException {
    List<Object> list = new ArrayList<Object>();
 
    thrown.expect(IndexOutOfBoundsException.class);
    thrown.expectMessage("Index: 0, Size: 0");
    list.get(0); // execution will never get past this line
}

```

### Junit Suite

Yazdığımız testlerin tek noktadan çalıştırılmaya başlamasını sağlayabiliriz.

**@Suite** ifadesi ile birden fazla test sınıfını koşturabiliriz.
    
```java
  @RunWith(Suite.class)
  @SuiteClasses({ UnitTest.class, SeviceTest.class })
  public class AllTestsForProduct {
  }
```

**@Ignore** ifadesi yazılmış fakat çalıştırılmasını istemediğimiz test metotlarının işaretlenerek çalışmasını engeller.
Devre dışı bırakır

```java
  @Ignore(value=" Bu metot örnek olsun diye çalıştırılmadı.")
  @Test
  public void testPrintMessage() {
    System.out.println("hello");
  }
```

# Hamcrest

Birçok dildeki test sınıfları içindeki metotlara yardımcı olarak daha okunabilir hallerini yazabilmemize yardım eder.

- http://hamcrest.org/

Bir eşleştirme,karşılaştırma kütüphanesidir. 

Hamcrest kütüphane olarak projenize eklenip kullanabiliriz. Test metotlarımızı yazarken sadece Hamcrest e ait olan 
metotları kullarak yazabiliriz ya da JUnit ile birlikte harmanlayarak kullanabiliriz.

Örneğin

Elimizdeki liste üzerinde işlemler yaparken,

Assert.assertThat() ile JUnit ten , CoreMatchers.hasItem() ile Hamcrest ten yararlanabiliriz.

```java
@Test
public void testNumber() {
  List<Integer> list = new ArrayList<>();
  list.add(1);
  list.add(2);

  list.forEach(item -> System.out.println(item.intValue()));

  Assert.assertThat(list, CoreMatchers.hasItem(1));
  Assert.assertThat(list, CoreMatchers.hasItem(2));
}
@Test
public void testNumberString() {
  List<String> list = new ArrayList<>();
  list.add("bir");
  list.add("iki");

  list.forEach(item -> System.out.println(item));

  Assert.assertThat(list.get(0), CoreMatchers.startsWith("b"));
  Assert.assertThat(list.get(1), CoreMatchers.is("iki"));
  Assert.assertThat(list.get(1), CoreMatchers.containsString("k"));
}

```

Kullanılması çok tercih edilen metotlara bakalım

```java
 equalTo()     -->  Eşitlik kontrolü
 containsString() --> Beklenen değer içinde istenilen değer var mı kontrolü.
 anyOf()       --> or durumudur. içindeki parametrelerin herhangi birinin doğruluğunda doğru sonuç verir.
 allOf()       --> and durumur. içindeki parameterlerinin hepsinin doğru olmasını bekler.
 either().or() --> bu veya bu karşılaştırması için kullanılır.
 hasItem()     --> liste karşılaştırılmalarında içerisinde bir elemanın olup olmadığı kontrolünü yapar

```

# AssertJ

Test metotlarına yardımcı olacak kütüphanelerden biridir. 
Metotlar içindeki beklenen değerin üzerine kontroller ekleyebilmemizi sağlar.

Örneğin 

Bir cümle üzerindeki bazı kontrolleri görelim.

```java

	@Test
	public void testSampleValue() { 
		Assertions.assertThat("sample value")
		.startsWith("sa")
		.endsWith("e")
		.containsOnlyOnce("v");
	}
```

Bir başka güzel yanı ise istediğimiz durumları metotlar haline getirip tekrar tekrar kullanabiliriz.

Örneğin

Elimizdeki listenin tek sayıları olmasını bekleyen bir metot içeriyor. 

Eğer liste içerisinde çift olan bir sayı var ise uyarı veriyoruz.


```java

 @Test
	public void testEvenOddNumbers() {
		
		List<Integer> sayilar1 = new  ArrayList<>(Arrays.asList(1,3,5));
		List<Integer> sayilar2 = new  ArrayList<>(Arrays.asList(1,3,4));
		
		Assertions.assertThat(sayilar1)
		.describedAs("UYARI - Hata olustugunda bu uyariyi veriyorum - Cift sayi var")
		.have(oddNumber());		
		
		Assertions.assertThat(sayilar1)
		.describedAs("UYARI - Hata olustugunda bu uyariyi veriyorum - Liste içinde 3 tane tek sayı bulunamadı")
		.haveExactly(3,oddNumber());
		

		Assertions.assertThat(sayilar2)
		.describedAs("UYARI - Hata olustugunda bu uyariyi veriyorum - Cift sayi var")
		.have(oddNumber());		
		
		Assertions.assertThat(sayilar2)
		.describedAs("UYARI - Hata olustugunda bu uyariyi veriyorum - Liste içinde 3 tane tek sayı bulunamadı")
		.haveExactly(3,oddNumber());
		
	}
	
	private Condition<? super Integer> oddNumber() {
		return new Condition<Integer>() {
			@Override
			public boolean matches(Integer value) {
				if(value % 2 == 1 ) {
					System.out.println("tek sayı");
					return true;
				}
				return false;
			}
		};
	}

```

Projelerimizdeki sınıflarımızın özellikleri kontrol edebilmemizi sağlayan metotları vardır.


``` java
	public class Product {
	    	private String name;
	    	private int type;
	    
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public int getType() {
			return type;
		}
		public void setType(int type) {
			this.type = type;
		}
	}
	
	@Test
	public void testListValue() {
		
		 
		Product p1 = new Product();
		p1.setType(1);
		p1.setName("product1");
		
		Product p2 = new Product();
		p2.setType(2);
		p2.setName("product2");

		List<Product> products = new ArrayList<>();
		products.add(p1);
		products.add(p2);


		Assertions.assertThat(Product.class).hasDeclaredFields("name");
		Assertions.assertThat(Product.class).hasDeclaredFields("type");
		
		Assertions.assertThat(Product.class).hasOnlyDeclaredFields("name","type");
		

		Assertions.assertThat(products)
		.extracting("name", "type")
		.contains(
				Assertions.tuple("product1",1),
				Assertions.tuple("product2",2)
		);
		
	}

```
