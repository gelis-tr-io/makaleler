Bir bilgisayar dilinin bir alt programa argüman aktarmasının **iki** yolu vardır.

İlk yol **değerle** çağırmadır. (*call by value*)\
İkinci yol ise **referans ile** çağırmadır. (*call by reference*)

Yazının devamının daha net anlaşılması adına, genel bir fikir sahibi olmamız gereken iki kavram bulunuyor. 

* Memory Allocation
* Heap
* Stack

### Memory Allocation

Allocation kelimesi dağıtım anlamına gelmektedir. Memory ise hafıza demektir. Basit bir çeviri ile hafıza dağıtımı anlamına gelen bu kelime bilgisayar biliminin önemli bir konusu olan **Memory Management** ile alakalı farklı yaklaşımları içinde barındırır.


Yüksek seviyeli diller (Java, C# gibi) bu işlemi programcının yapmasını engelleyerek daha güvenli kodların yazılması hedeflemiştir.

Fakat **C** gibi düşük seviyeli diller programcının tüm kavramlara ve dikkat edilmesi gereken noktalara hakim olduğunu varsayıp, *pointer* adını verdiğimiz yapıları kullanmasına müsaade eder.

#### Pointer nedir?

Pointer(Gösterici) basitçe bir değişkenin bir hafıza alanını göstermesi demektir.

```c
int *p;
int value = 10;
p = &a;
```

![pointer-table](pointer.jpg) 

*Ilgili tablo Sadi Evren Şeker hocamızın sitesinden alınmıştır*

**value** adlı integer değişkeninin sakladığı değer 10'dur. Bellekteki adreslemesi A116 adlı noktaya denk gelmektedir.

Programın çalışması sırasında bir başka değişkenin işaret ettiği adres bölgesi A116 olduğu sürece **value** değişkeni içindeki 10' değerine erişebiliriz. Pointer özetle budur.



### Heap

Java dilinde herhangi bir sınıftan bir nesne oluşturmak için *new* operatörü yada 
```java
Class.forName().newInstance()
clone()
readObject()
```
metodları kullanılmaktadır. New operatörü yahut yukarıdaki metotlardan biri ile yeni bir *nesne* oluşturulduğunda, oluşan bu yeni nesne JVM (**Java Virtual Machine**) tarafından bilgisayarın hafızasında *belirli* bir noktaya konuşlandırılır(yerleştirilir).

Konuşlandırılmış olan bu nesnelerin işaret ettikleri bir **referansları** bulunmaktadır.

Bu referansların işaret ettikleri noktada **object** yada diğer adıyla ilgili class'ın bir **instance** ' ı bulunmaktadır.

Referans kavramını pointer gibi düşünebilirsiniz.