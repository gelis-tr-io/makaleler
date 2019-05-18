# Unit Test Nedir ?

Unit test, software testing(yazılım test etme) metodlarından sadece birisidir. Unit birim anlamına gelir. Matematikteki anlamı bir çokluğu oluşturan varlıkların her biri ya da bir kümenin her elemanı. Yazılımda, kümeye yazdığımız program elemenlarına da fonksiyonlarımız diyebiliriz. Bu fonksiyonları parçaları test etmeye Unit Test denir.

## Unit Test Amacı Nedir ? 

Unit testin amacı projemizin gelişim aşamasında iken parçaların doğru çalıştığından emin? olmamızı sağlar.

## Unit Test Nasıl Yazılmalı ?

    1- Unit test yazarken IO(dosya okuma, database, network) gibi zaman alabilecek donanım erişimlerini sahteleri ile simüle etmeliyiz. Aksi taktirde unit testiniz belki networkten dolayı bir sorundan dolayı başarısız olabilir. Başka etmenler (side-effect) devreye giriyor ve bu unit yani birim testinin tanımına ters düşer.
    2- Public fonksiyonlarınızı ve metodlarınızı test edebilirsiniz.
    4- Testleriniz izole olmalıdır database, network gibi bağımlılıklardan.

## Unit Test Avantajları Nelerdir ?

    1- Kompleks olmayan parçaları test ettiğimiz için yazılması kolay ve hızlıdır.
    2- Unit testler hızlı çalışırlar. Bunun sebebi yukarıda anlattığım gibi io işlemlerini fakeleri ile simüle etmemiz.
    3- Development yaparken çalıştırabilirsiniz.
    4- İzole ortamda çalıştığı için ister kendi bilgisayarınızda ister build servicede çalıştırabilirsiniz. 

## Biraz Örnek Lütfen

Şimdi bir NodeJS uygulamamdaki bir utilty sınıfının testlerini nasıl yazdığımı göstereyim.


```javascript
const bcrypt = require('bcrypt');
const mongoose = require('mongoose');

const SALT = process.env.SALT || 10;

exports.hashTheUserPassword = async password => {
  const hashedPass = await bcrypt.hash(password, SALT);
  return hashedPass;
};

exports.comparePlainPassWithHashedPass = async (plainPass, hassedPass) => {
  const isValid = await bcrypt.compare(plainPass, hassedPass);
  return isValid;
};

exports.createObjectForUser = (email, password) => {
  return {
    _id: mongoose.Types.ObjectId(),
    email,
    password
  };
};
```

Örnek aslında çok basit ama aşina olmayanlar için kısa bir özet geçeyim. 
 * hashTheUserPassword metodu kullanıcının girdiği şifreyi hashliyor.
 * comparePlainPassWithHashedPass metodu kullanıcının girdiği şifre ile veritabanındaki şifresini kıyaslıyor.
 * createObjectForUser bu metod zaten logic içermeyen basit bir şekilde nesne dönderiyor.


Şimdi test yazma kısmına geçmeden bu metodları incelemek istiyorum. hashTheUserPassword metodu içinde 3.parti bir kütüphanenin fonksiyonunu kullanılıyor hash algoritması için. Burda eğer ben metodu direk teste sokarsam benim testim bcrypt kütüphanesinin hash metodunu da test etmiş olacak. Unit test kavramına ters bir durum oluşuyor ve eğer o 3.parti fonksiyonun içinde bir hata var ise benim testimin fail olmasına yol açacak. Biz bunun testini unit test aşamasında yapmak istemiyoruz. Onlar başka test aşamalarında test ediliyor ancak konumuz bu değil.

Peki biz bu side-effect'i nasıl yok edebiliriz. Bu hash methodunu fake bir metodla değiştirip düzgün çalıştığından emin olabiliriz. Bunları yapmak için 2 yöntem var.

    1- Stub: Fake edeceğiniz bağımlılık için basit bir sınıf ve fonksiyonlar yazıp bunları kullanmanız. Dezavantajı ise ekstra kod, sınıf yazmak.
    2- Mock: Bir çeşit proxy, sahte nesne demektir ve orjinal nesne gibi davranır.

Bu yöntemlerden genellikle mock tercih edilir bende bu makalede mock kullanacağım.
Basit bir şekilde test sınıfımızı yazmaya başlıyalım.

```javascript
jest.mock('bcrypt');

test('hashTheUserPassword_withGivenPassword_shouldReturnHashedPassword', async () => {
  hash.mockReturnValue(Promise.resolve('hashedPass'));
  const hashedPass = await hashTheUserPassword('123456');
  expect(hash).toHaveBeenCalledTimes(1);
  expect(hash).toHaveBeenCalledWith('123456', 10);
  expect(hashedPass).toBe('hashedPass');
});
```

* İlk satırda bcrypt modülünü mockluyoruz. Bu kütüphanenin sahtesinin oluşturulmasını istiyoruz yani.
* Daha sonra testimizin ilk satırında fonksiyonumuzun içinde kullanılan hash metodunu mockluyoruz. Bize bir logic yapmadan 'hashedPass' stringini dönderecek bir metod oluşturuyor. Ve artık bcrypt modülünün hash metodu bizim kontrolümüzde ve istediğimiz değeri return edecek.

Gördüğünüz gibi test metodu çok basit sade ve okunabilir. İngilizceniz iyiyse bir düz yazıyı okumak kadar basit.

Test sınıfının tamamı şu şekilde:

```javascript
jest.mock('bcrypt');
jest.mock('mongoose');

const { hash, compare } = require('bcrypt');
const { ObjectId } = require('mongoose').Types;

const {
  hashTheUserPassword,
  comparePlainPassWithHashedPass,
  createObjectForUser
} = require('../../utils');

process.env.SALT = 10;

test('hashTheUserPassword_withgivenpassword_shouldreturnhashedpassword', async () => {
  hash.mockReturnValue(Promise.resolve('hashedPass'));
  const hashedPass = await hashTheUserPassword('123456');
  expect(hash).toHaveBeenCalledTimes(1);
  expect(hash).toHaveBeenCalledWith('123456', 10);
  expect(hashedPass).toBe('hashedPass');
});

test('comparePlainPassWithHashedPass_withGivenPlainAndHashedPass_shouldReturnTrue', async () => {
  compare.mockReturnValue(Promise.resolve(true));
  const isValid = await comparePlainPassWithHashedPass('pass', 'hashedPass');
  expect(compare).toHaveBeenCalledTimes(1);
  expect(compare).toHaveBeenCalledWith('pass', 'hashedPass');
  expect(isValid).toBe(true);
});

test('createObjectForUser_withGivenEmailAndPassword_shouldReturnUserObject', () => {
  const userObj = {
    _id: 1,
    email: 'test@test.com',
    password: 'hashedPass'
  };

  ObjectId.mockReturnValue(1);

  const result = createObjectForUser(userObj.email, userObj.password);
  expect(ObjectId).toHaveBeenCalledTimes(1);
  expect(result).toMatchObject(userObj);
});

```

Burdan inceleyebilirsiniz geriye kalan test metodlarını.Ben burda 2 tane 3.parti modül kullandım ve test içinde Jest frameworkünü kullandım. Çok basit ve güçlü bir tool. Web sayfasından kolayca öğrenebilirsiniz. Burda dilin ve toolların bir önemi yok. Anlatmak istediğim meseleyi anladıysanız burdaki diğer 3.parti şeyleri anlamanıza gerek yok.Şimdi test sonuçlarına gelelim.

```zsh
 PASS  ./utils.unit.test.js
  ✓ hashTheUserPassword_withgivenpassword_shouldreturnhashedpassword (5ms)
  ✓ comparePlainPassWithHashedPass_withGivenPlainAndHashedPass_shouldReturnTrue (1ms)
  ✓ createObjectForUser_withGivenEmailAndPassword_shouldReturnUserObject (1ms)

----------|----------|----------|----------|----------|-------------------|
| File       | % Stmts    | % Branch   | % Funcs    | % Lines    | Uncovered Line #s   |
| ---------- | ---------- | ---------- | ---------- | ---------- | ------------------- |
| All files  | 100        | 100        | 100        | 100        |                     |
| utils.js   | 100        | 100        | 100        | 100        |                     |
| ---------- | ---------- | ---------- | ---------- | ---------- | ------------------- |
Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        1.598s, estimated 2s

```

Gördüğünüz gibi testlerimiz başarılı bir şekilde tamamlandı. Burdaki tabloyu code coverage'ı bilmeyenler için anlatayım. Dosyanın içinde test edilen fonksiyon, satır oranını gösterir. Testimizde sonuçlar %100 yani testimiz sonucunda bütün kod satırlarını ve fonksiyonları test etmiş olduk. Eğer vscode kullanıyorsanız henüz test edilmeyen satırları ve fonksiyonları gösterebilen extensionlar var. Ordan takip edebilirsiniz test aşamanızı.

Elimden geldiğince unit test konusunda bildiklerimi anlatmaya çalıştım. Umarım aktarabilmişimdir. Öğrenirken yardımcı olacak diğer bazı kaynakları aşağıya yazdım.

Kaynaklar:

    * https://www.youtube.com/playlist?list=PLZYKO7600KN89x_tdqGhP7n8b0KXPC8EY
    * https://www.youtube.com/watch?v=b27h-7rsf6E&t=2322s
    * https://www.softwaretestinghelp.com/types-of-software-testing/