## Semboller
Semboller tanım olarak: objelerin nitelikleri için (bir çok kaynakta *key* yerine *property* dendiği için yazı boyunca *nitelik* kelimesi kullanacağım) kullanılan tekil (*unique*) ve değişmez (*immutable*) ilkel veri tipidir.

Biraz açalım. Tekil olması aynı şekilde tanımlanmış iki sembolün aynı **olmaması** anlamına gelir.

```javascript
Symbol() === Symbol() //false
```

Aynı durum Array ve Object tiplerinde de görülür.

```javascript
Array() === Array() //false
Object() === Object() //false
// veya
[] === [] //false
{} === {} //false
```

Bu durum *string*, *integer*, *float* ilkel tiplerinde farklıdır. Bu tipler tekil değildir.

```javascript
"merhaba" === "merhaba" //true
5 === 5 //true
5.5 === 5.5 //true
```

Dolayısıyla bu örneklerle tekilliği anlamış olduk. Peki sembollerin kullanım amacı nedir? Objelerin nitelikleri sadece string değerler alabiliyor. Eğer biz bir obje niteliğine integer, float veya obje tipinde bir nitelik girersek Javascript bunu string tipine çevirecek ve gelen çıktıyı nitelik olarak tanımlayacaktır.

```javascript
var keyObj = {a: 1};
var obj = {
  [keyObj]: "helloworld",
  5: "helloworld",
  5.5: "helloworld"
}
obj //{3: "helloworld", [object Object]: "helloworld", 5.5: "helloworld"}
```

Çıktıda niteliklerin *3*, *5.5* olarak integer ve float gibi göründüğüne hemen aldanmayın. `Object.keys` çıktısına bakalım.
```javascript
var keyObj = {a: 1};
var obj = {
  [keyObj]: "merhaba",
  5: "merhaba",
  5.5: "merhaba"
}
Objectt.keys(obj) // ["3", "[object Object]", "5.5"]
```

Dolayısıyla objelerin nitelik olarak sadece *String* tipini kabul ettiğini anlamış olduk. String dışında yeni ilkel tipimiz Symbol tipini de obje niteliklerinde kullanabiliyoruz.

Peki bir objenin niteliğinde (*property*) Symbol kullanmanın amacı nedir? Bunu anlamak için örnekler üzerinden gidelim.

Objelerin niteliklerini (*property*) Symbol ile tanımlarsak, yaptığımız tanıma ancak referans olarak verdiğimiz değişken üzerinden ulaşabiliriz. Çünkü Symbol tipi tekildir!

Bir obje tanımlayalım ve bunun üzerinden örnekleri irdeleyelim.

```javascript
var sym = Symbol();
var obj = {
    [sym]: 1,
    a: 2,
    b: 3
}

obj // Object { a: 2, b: 3, Symbol(): 1 }
obj[sym] // 1
obj[Symbol()] // undefined
```

Bu örnek üzerinden Symbol ile tanımlanmış obje niteliklerini *anonim nitelik* olarak adlandırabiliriz. Çünkü ancak referans değişken üzerinden değere ulaşabiliyoruz. Eğer ki referans değişkenimizi kaybedersek, değere asla ulaşamayız.**\***

> \* *Yalan! Ulaşabiliriz, okumaya devam edelim.*
 
```javascript
var sym = Symbol();
var obj = {
    [sym]: 1,
    a: 2,
    b: 3
}
var sym = "merhaba dünya";
obj[sym] //undefined;
```


Oysaki bu durumda tekil olmayan ilkel tipleriyle tanımlanmış obje niteliklerinde değer kaybolmaz, referansımızı kaybetsek bile nitelik tekil olmadığından değere ulaşabiliriz.

```javascript
var sym = "helloworld";
var obj = {
    [sym]: 1,
    a: 2,
    b: 3
}
var sym = null;
obj[sym] //undefined;
obj["helloworld"] //1;
```

Değer tanımlaması yaptığımız gibi metot da tanımlayabiliriz. *Private function* düşüncesi sembolleri daha cazip kılıyor. Değil mi?

```javascript
var myPrivateFunction = Symbol();
var obj = {
    [myPrivateFunction]: function(){
        return "some secret values";
    }
}
```

### Niteliğe erişmek
Symbol tipi sayılamaz\* (*un-enumerable*) bir tip olduğu için *Object.keys*, *Object.values*, *Object.entries*, *Object.getOwnPropertyNames* gibi gömülü işlevlerin döndürdüğü değerlerde bulunmaz. Sayılamaz tip olduğundan *for-of* döngüsü de okuyamayacaktır.

> \*Sayılamazlık (*enumerable*), bir tipin içindeki verilerin bir sıra halinde olup olmamasıdır. Günlük hayatta düşünürsek sayılar sayılabilirdir, çünkü bir sayıdan önceki ve sonraki sayılar belirlidir, tanımlıdır. Fakat renkleri düşünürsek bu belirsizdir çünkü renklerin bir sırası yoktur. Turuncu mu önce gelir, kırmızı mı? Ancak alfabetik sıralarına veya heksadesimal değerlerine göre, bir sistematik kullanarak renkler için sayılabilir diyebiliriz.
> 
> Kod tarafında düşünürsek string ve array tipleri sayılabilirdir çünkü her bir elemanın bir sıra (*index*) değeri vardır. Fakat objelerde bunu söylememeyiz çünkü hangi niteliğin önce geldiği belirsizdir, dolayısıyla *bir objenin 3. elemanı* şeklinde bir ifade kullanamayız. Bunu `Object.keys` gibi metotlar ile objeleri sayılabilir tiplere çevirerek yapabiliriz.

```javascript
var obj = {
    [Symbol()]: 1,
    a: 2,
    b: 3
}

Object.keys(obj) //[a, b]

for(var i in obj){
    console.log(i)
}

// a
// b
```

Fakat sembol ile tanımlanmış obje niteliklerini alabilmek için *Object.getOwnPropertSymbols* işlevini veya objeye ait tüm nitelikler için *Reflect.ownKeys* işlevlerini kullanabiliriz.

```javascript
var obj = {
    [Symbol()]: 1,
    a: 2,
    b: 3
}
Object.getOwnPropertySymbols(obj) // [Symbol()]
Reflect.ownKeys(obj // [a, b, Symbol()]
```

Dolayısıyla az önce *asla ulaşamayız* dediğimiz değere *Object.getOwnPropertySymbols* işlevini kullanarak ulaşabiliriz.

```javascript
var obj = {
    [Symbol()]: 1,
    a: 2,
    b: 3
}

var sym = Object.getOwnPropertySymbols(obj)[0];
obj[sym] // 1
```

Burada objeye nitelik olarak sembol tanımlamasının yapılması dışarıdan bir müdaheleye karşı bilgiyi koruyacaktır. Dikkat etmemiz gereken konu tanımımızın evrensel kapsam (*global scope*) içinde bulunmaması.

## Evrensel Sembol Tanımlamak
Yukarıda sembollerin evrensel olduğunu ve referans atadığımız değişken üzerinden ulaşabildiğimizi anlattım. Eğer istersek sembolleri `Symbol.for()` ile tanımlayarak sembolümüze bir anahtar tanımlayabilir ve tekrar ulaşabiliriz.

```javascript
var global_sym = Symbol.for("helloworld");
var obj = {
    [global_sym]: "Hello world", 
};
var global_sym = null;
obj[global_sym] //undefined
obj[Symbol.for("helloworld")] //Hello world
```
Burada tekrar `Symbol.for("helloworld")` çağrıldığında runtime üzerinde daha önce bu tanım yapılmış mı diye arıyor. Eğer tanımlıysa o kaydı kullanıyor, aksi halde yeniden tanımlama yapıyor.

Tanımlı bir evrensel sembolün anahtarını okumak için `Symbol.keyFor()` metotunu kullanabiliriz.
```javascript
var sym = Symbol.for("hellovenus")
Symbol.keyFor(sym) //hellovenus
```

Dolayısıyla evrensel semboller için *anonim olmayan semboller* diyebiliriz.

## Açıklama Tanımlamak

Sembol tanımlaması yaparken istersek açıklama da tanımlayabiliriz.
```javascript
var sym = Symbol("herhangibiraciklama")
```

Fakat açıklamalar etkisizdir, yani sembollerin mekanizmasını herhangi bir şekilde etkilemezler. Eğer debug yapıyorsanız tanımladığınız sembollere açıklama girerek takibini kolaylaştırabilirsiniz. Aksi durumda tercih edilmezler.


## Tanınmış Semboller (Well-known Symbols)

Semboller diğer ilkel tipler üzerinde de farklı amaçlar için kullanılmaktadır. Örneğin bir değişkenin yineleyici (*iterator*) fonksiyonu, bir dizinin *concat* kullanımı, etkileşime geçtiği ilkel tiplerdeki davranışları semboller ile tanımlanır.

Bir kaçını inceleyelim.

### Symbol.iterator

Yinelenebilir (*iterable*) olan bir verinin yineleyici (*iterator*) işlevini belirler.

Yinelenebilir bir verinin *Symbol.iterator* işlevini kaldıralım ve sonucu görelim.

```javascript
var arr = [1, 2, 3];
arr[Symbol.iterator] = null;
for(var el of arr){
    console.log(el);
}
//TypeError: arr is not iterable
```

*Bu yolla yeni bir işlev tanımlamayı iteratörleri anlatacağım bir sonraki yazıda değineceğim.*


### Symbol.isConcatSpreadable

Bir dizinin `concat` işlevine karşı davranışını belirler. Eğer `true` olarak belirlenmişse -ki bu varsayılan değerdir- diziye `concat` fonksiyonu ile bir başka dizi bağlanabilir. `false` ise bağlanamaz. Diziyi *spread* etmeden bağlar.

```javascript
var arr = [1, 2, 3];
arr[Symbol.isConcatSpreadable] = false;
arr.concat([4, 5, 6]); // [[1, 2, 3], 4, 5, 6];
```

### Symbol.toPrimitive

Bir objenin dahil olduğu işlemin ilkel tipine dönüşümü için kullanılır. Bir string veya integer değer ile objeyi toplayamayız fakat objeye `Symbol.toPrimitive` niteliğini tanımlayarak dahil olduğu işlemin ilkel tipi için bir metot döndürebiliriz. Şimdi bu anlamlı görünmeye çalışan anlamsız cümleyi anlamlı hale getirelim.

Öncelikle normal durumu görelim.
```javascript
var obj = {a: 1, b: 2, c: 3};
"Object: " + obj // Obj: [object Object]
`Object: ${obj}` // Obj: [object Object]
```

Şimdi `Symbol.toPrimitive` niteliğini tanımlayalım. Tanımlayacağımız metota objenin dahil olduğu işlemin ilkel tipini parametre olarak alacağız ve bunu içeride yazacağımız koşulla farklı ilkel tipler için farklı cevaplar döndüreceğiz.

```javascript
var obj = {
    a: 1, b: 2, c: 3,
    [Symbol.toPrimitive]: function(type){
        if(type === "string"){
            return JSON.stringify(this);
        }else if(type === "number"){
            return Object.values(this).reduce((a, b) => a + b);
        }
    }
};

`Object: ${obj}` //Object: {"a":1,"b":2,"c":3}
"Object: " + obj //Object: {"a":1,"b":2,"c":3}
+obj             //Object: 6
parseInt(obj)    //Object: 6
```

Yaptığımız şey basitçe, obje bir string ile etkileşime geçiyorsa objenin kendisini bir JSON çıktısı olarak vermek, integer ile etkileşime geçiyorsa değerleri toplamak.

Burada `JSON.stringify` ve `Object.values` işlevlerinin `Symbol.toPrimitive` niteliğinin sayılamaz (*un-enumerable*) değer olduğu için okumadığını tekrar hatırlatayım.

İyi güzel de bu gerçek hayatta nerede işimize yarayacak? Hımmm. Mesela `encodeURIComponent` kullanımına bir son verebiliriz.

```javascript
var obj = {
    userId: 5,
    platformName: 5,
    isExist: true
};

obj[Symbol.toPrimitive] = function(type){
    if(type === "string"){
        return Object.entries(obj)
            .map(([key, value]) => `${key}=${value}`)
            .join("&")
    }
};

`http://hello.world/?params=${obj}` // http://hello.world/params?userId=4&platformName=5&isExist=true
```

Diğer tanınmış sembolleri kaynaklar kısmından bulabilirsiniz. Benden bu kadar. Umarım anlaşılır olmuştur.

* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol
* http://exploringjs.com/es6/ch_symbols.html
