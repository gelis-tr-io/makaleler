# git mantığı
git işleyiş (akış) mantığını kavrama rehberi

## git nedir? 

git, versiyon konrtol sistemidir.

## neleri kolaylaştırır?
- proje klasörü üzerinde yapılan değişiklikleri adım adım kaydetmeyi sağlar
- yapılan değişiklikler arasında hızlıca geçiş yapmayı sağlar
- bireylerin birbirinden bağımsız ortamlarda geliştirme yapabilmesi esnekliğine sahiptir
- bağımsız geliştirme yapısı ile aynı anda geliştirme yapmaya olanak verir ve böylelikle gelişim sürecinde tasarruf sağlar

## nerelerde kulanılır?
genel olarak yazılım yapıları geliştirmede kullanılsa da kullanım alanı oldukça geniştir.

dijital klasör içinde üretilebilen her şeyin gelişim sürecini takip etme esnekliğine sahiptir. kitap yazılabilir, resim yapılabilir, şarkı bile üretilebilir.

kısaca birlikte bir ürün geliştirmenin en pratik ve sağlıklı yoludur.

## nasıl çalışır?

git, tanımlaması yapılan klasör içeriside "**.git**" adında bir klasör oluşturur.
bu klasör içerisinde git kendi dosyalarını barındırır. 

tanımlaması yapılmış olan klasör **workspace** (*ˈwərkˌspās*) veya **working area** (*ˈwərkiNG ˈe(ə)rēə*) adı verilen **çalışma alanıdır** ve bu alan içerisinde geliştirme yapılır.

git, klasör içerisindeki değişimleri takip eder.

belirlenen değişiklikler arasından projeye işlenmek istenen değişiklikler seçilir ve git üzerinde **staging area** (*ˈstājiNG ˈe(ə)rēə*) adı verilen **aşamalar alanına** aktarılır.

çalışmadaki aşamalar **commit** *(kəˈmit)* adı verilen **işlem kaydı** ile kayıt altına alınır.

proje dosyaları ve işlem kayıtları **repository** (*riˈpäzəˌtôrē*) adı verilen **depo**larda barındırılır.

işlem kaydı yapılan aşamalar **local repository** (*ˈlōkəl riˈpäzəˌtôrē*) adı verilen **yerel depo** içine aktarılır.

işlem kayıtları bütünü işlem akışını oluşturur. bu akış gelişim sürecini görmeyi sağlar. gelişimin zaman çizgisi **branch** (*branCH*) adı verilen **dal**larla gösterilir. 

üzerinde çalıştığımız dal **master** (*ˈmastər*) adı verilen **ana dal**dır.

tercihe göre ek dallar çıkartılabilir, her gelişimin kendi iş akışında ilerleyebilir, bu dallar daha sonra birleştirilebilir.

projeler, git sunucusu üzerinde **remote repository** adı verilen **uzak depo**larda da barınabilir.

uzak deponun varsayılan ana dalı **origin** adı verilen **uzak ana dal**dır. depo içeriğine göre dallar değişebilir.

yerel depo ile uzak depo arasında eşitleme yapılabilir.

eşitleme ile başkasına ait depo üzerinde de geliştirme yapılabilir.

geliştirme yapılacak uzak deponun seçilen dalından **fork** adı verilen **çatal çıkar**ma yöntemi ile çıkarılan dal yerel depoya kopyalanır.

bu dal üzerinde yapılan güncellemeler **pull request** adı verilen **çekme isteği** yöntemi ile uzak deponun sahibine geri gönderilir.

uzak depo sahibi çekme isteklerini denetleyebilir, düzenleyebilir, ana dal ile birleştirebilir.

bu döngü sürdüğü sürece gelişim devam eder.

## github nedir?
`çok yakında...`
## github desktop nedir?
`çok yakında...`
## ide üzerinde git kullanımı
`çok yakında...`

#### dış kaynaklar
- [Github kullanarak kolayca nasıl başkalarının kodlarına katkılar yapılır ve sosyalleşilir - Tarık Güney](https://www.youtube.com/watch?v=_AAax7iQ6VE)
- [Sıfırdan Git Dersleri - GitHub Kullanımı - Kadir Kasım](https://www.youtube.com/watch?v=uncrCoLiq-g&list=PLHN6JcK509bOrevTCFrSMeAfBtuib4Gpg)
- [git - basit rehber - tr](http://rogerdudler.github.io/git-guide/index.tr.html)
- [Git (Yazılım) - vikipedi](https://tr.wikipedia.org/wiki/Git_(yazılım))[ - (neden '0.wikipedia.org'a yönlendiriliyorum?)](https://www.google.com/search?q=wikipedia+erişim+engeli)
