### Deadlock

Evvela anlamamız gereken ilk önemli kavram, *Monitör* kavramıdır.

#### Monitör

İki ayrı kanalın (Thread'in) haberleşmesini ve bağlı liste gibi karmaşık bir veri yapısını paylaşmasını isterseniz, birbiriyle çakışmamalarını sağlayacak bir yola ihtiyacınız olabilir. 

Basit bir **I/O** (Input Output) işlemi örneği olarak, bir dosyadan veri okumayı düşünelim.

- İlk kanal **veri.txt**'nin içeriğini okuyorken, eşzamanlı olarak ikincil bir kanal
 **veri.txt**'ye yazamaz. Dolayısı ile bu durum önlenmek *zorundadır*.
 
Java bu amaçla işlemler arası senkronizasyonu **monitor** kavramı ile sağlamıştır.

Monitör'ü *sadece bir* kanalı tutabilen, çok küçük bir kutu olarak düşünebilirsiniz.

Bir kanal(thread) monitöre girdi mi; diğer tüm kanallar girmek için bu kanalın çıkmasını beklemek **zorundadır.** Bu yöntemle birden fazla kanal tarafından paylaşılan herhangi obje/nesnenin aynı anda farklı işleme sokulması engellenir. Çok kanallı (multi-thread) sistemlerin çoğu, monitörleri programınız tarafından açıkça edinilmesi ve yönlendirilmesi gereken bir nesne olarak kullanıma sunar.

Oysa Java bu konuya daha açık bir çözüm getirmiştir. Bir monitör sınıfı yoktur, bunun yerine her nesnenin senkronize(eşzamanlı) edilmiş bir metodu çağrıldığında(Not : Bu konu synchorized kavramı ile alakalı. Talep gelirse bu konuyu daha ayrıntılı açıklayabilirim. @enesusta üzerinden bana ulaşabiliriniz) otomatik olarak girilen kendine has saklı bir monitörü vardır. Bir kanal senkronize bir metotta iken, başka hiçbir kanal aynı nesne üzerindeki senkronize metodu **çağıramaz.**

Deadlock kavramının anlaşılması adına monitör kavramını kısaca bu şekilde özetleyebiliriz.

### Çıkmaz (Deadlock) Nedir?

Özellikle çok görevliliği(multi-threading) 'i ilgilendiren, kaçınılması gereken özel tip bir hatadır. Bu hata iki kanal bir çift *senkronize* nesne üzerinde dairesel bir bağımlılığa sahip olduğunda ortaya çıkar.

Basit bir örnekle açıklamak gerekirse ;

- Bir kanal **X** nesnesindeki monitöre, bir başka kanalda **Y** nesnesi üzerindeki monitöre girmiş olsun. X'teki kanal, Y'deki herhangi bir senkronize metodu çağırdığında, beklenildiği gibi bunu **bloke** edecektir. Çünkü o an için X nesnesinin içinde bulunduğu kanalın monitöründe X nesnesi vardır. Monitör içinde aynı anda sadece tek bir obje bulunabilir. 
- Buna karşın eğer Y'deki kanal daha sonra X'teki senkronize bir metodu çağırmaya çalışırsa, sonsuza kadar beklemesi gerekir. Çünkü X'e erişmek için, Y üzerindeki kendi kilidini kaldırmak zorundadır ki, ilk kanal çalışmasını tamamlayabilsin.

Deadlock iki sebepten dolayı ayıklanması zor bir hatadır.
* Genelde, nadir olarak meydana gelir. Çünkü iki kanalın tam olarak doğru bir biçimde aynı zaman diliminde bulunması gerekir.
* Çıkmaz iki kanal ve iki senkronize nesneden fazlasını içerebilir. Yani Çıkmaz, açıklanandan çok daha karmaşık olay yada olaylar dizisinden meydana gelmiş olabilir.

İşin teorisi bu olsada, pratik üzerinde nasıl işlediğini anlamak adına aşağıdaki kodları lütfen dikkatlice inceleyin.

```java

class Deadlock implements Runnable {

    X x = new X();
    Y y = new Y();

    Deadlock() {
        
        Thread.currentThread().setName("Ana Kanal"); // Runnable interface'i yeni bir Thread yaratir. 

        Thread thread = new Thread(this, "Bir diger kanal");
        thread.start(); // kanal calismaya baslar.
        x.foo(y);
        System.out.println("Program ana kanala dondu");

    }

    @Override // Runnable 'in tek metodu.
    public void run() {
        y.bar(x);
        System.out.println("Program ikincil kanala dondu");
    }
}

class X {

    synchronized void foo(Y y) {

        String threadName = Thread.currentThread().getName();
        System.out.println(threadName + " Y.foo()'a girildi");

        try {

            Thread.sleep(1000); // Kanal 1 saniye uyuyacaktir(bekleyecektir).

        } catch (InterruptedException e) {
            System.out.println("X engellendi"); // InterruptedException, iki kanaldan bir digeri calismasina engel oldugu durumlarda olusur.
        }

        System.out.println(threadName + " Y.last()'i cagirmayi deniyor.");
        y.last();

    }

    synchronized void last() {
        System.out.println("X.last()' cagirildi");
    }

}

class Y {

    synchronized void foo(X x) {

        String threadName = Thread.currentThread().getName();
        System.out.println(threadName + " X.foo()' ya girildi");

        try {

            Thread.sleep(1000); // Kanal 1 saniye uyuyacaktir(bekleyecektir).

        } catch (InterruptedException e) {
            System.out.println("Y engellendi"); // InterruptedException, iki kanaldan bir digerinin, oteki kanalin calismasina engel oldugu durumlarda olusur.
        }

        System.out.println(threadName + " X.last()'i cagirmayi deniyor.");
        y.last();

    }

    synchronized void last() {
        System.out.println("Y.last()' cagirildi");
    }

}

```

Umarım faydalı olmuştur. İyi günler.