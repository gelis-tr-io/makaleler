# Özetleme Fonksiyonları


Kriptografik özetleme fonksiyonları aldıkları girdinin boyutundan bağımsız olarak belirli uzunluğa sahip bir çıktı üreten tek yönlü fonksiyonlardır. Bu özelliğinden dolayı özetlenen veriden asıl veri elde edilemez. Özet fonksiyonlarının anahtar genişlikleri ne kadar büyük olursa güvenilirlikleri de bir o kadar artar.

Özetleme fonksiyonları;

* Sayısal imza yönteminde ya da mesaj doğrulama gibi doğrulama yöntemlerinde,
* Blockchain teknolojisinde (Proof-of-Work(PoW) yapısında),
* Güvenli şekilde veri indirme işlemlerinde,
* Şifre doğrulama

gibi çeşitli alanlarda kullanılır.


Bir özetleme fonksiyonununu aşağıdaki özelliklere sahip olması gerekmektedir.

* Deterministik olmalı yani aynı mesaj her zaman aynı hash ile sonuçlanmalı.
* Hash değerinden mesaj değerine gitmek mümkün değildir.
* Herhangi bir mesajın hash değerini hesaplamak hızlıdır.Ama çok hızlı değildir.
* Özetleme fonksiyonları kelebek etkisi davranışını sergilemelidir. Yani girdi üzerinde en ufak 1 bitlik değişiklik bile girdi özetini tamamen değiştirmelidir. Bu da özeti tamamen tahmin edilemez ve rastgele yapar.
* Aynı hash değerine sahip iki farklı mesaj bulmak mümkün değildir.

### 1. MD5 (Message-Digest algorithm 5)

Girilen verinin boyutundan bağımsız olarak, 128-bit özet değeri üretir. MD5 ilk olarak kriptografik özet fonksiyonu olarak tasarlanmış olmasına rağmen geniş çaplı güvenlik açıkları tespit edilmiştir. MD5 , Ron Rivest tarafından 1991 yılında, daha önceki versiyon olan MD4 yerine kullanılması amacıyla tasarlanmıştır. "MD" kısaltması "Mesaj Özeti (Message Digest)" anlamına gelmektedir. 

MD5'ın güvenilirliği ciddi olarak sarsılmıştır.  İçerdiği güvenlik zafiyetleri sahada da kullanılmıştır, bunlardan en önemlisi 2012'deki Flame kötücül yazılımıdır.

### 2. SHA-1 (Secure Hash Algorithm 1)

Girilen verinin boyutundan bağımsız olarak, 160-bit özet değeri üretir. SHA-1 TLS, SSL, PGP, SSH, S/MIME ve IPsec gibi birçok güvenlik uygulaması ve protokolünün bir parçası olarak kullanılmaktadır. Bu uygulamarda SHA-1 yerine MD5 da kullanılabilemektedir.

SHA-1 artık güvenli bir algoritma olarak düşünülmemektedir. Kriptanalistlerin 2005'te yaptığı bir saldırıyla SHA-1'in devam eden kullanım için yeterince güvenli olmadığını ispatladılar. Bu yüzden 2010'dan beri SHA-1 yerine daha güvenli olan SHA-2 veya SHA-3 öneriliyor.

### 3. SHA-2 (Secure Hash Algorithm 2)

SHA-2, kendinden önce gelen SHA-1’den önemli farklılıklar içermektedir. SHA-2 ailesi basamakları (hash değerleri) 224, 256, 384 veya 512 bit olan altı hash fonksiyonundan oluşur:

*	SHA-224
*	SHA-256
*	SHA-384
*	SHA-512
*	SHA-512/224
*	SHA-512/256

SHA-256 ve SHA-512 sırasıyla 32-bit ve 64-bitlik kelimelerle hesaplanmış orijinal özet fonksiyonlarıdır. Farklı kaydırma miktarları ve toplama sabitleri kullanırlar ancak yapıları neredeyse aynıdır. 

SHA-224 ve SHA-384 ilk ikisinin farklı başlangıç değerleri ile hesaplanmış, basitçe kesilmiş versiyonlarıdır.
SHA-512/224 ve SHA-512/256 da SHA-512’nin kesilmiş versiyonlarıdır ama başlangıç değerleri Federal Bilgi İşleme Standardı (FIPS) PUB 180-4’te tanımlanmış metot kullanılarak üretilmiştir.

Java Security kütüphanesinden yararlanılarak desteklenen özetleme fonksiyonlarının kodları aşagıdaki gibidir.

```java

import java.security.MessageDigest;

public class MessageHash 
{
	static String hashFunction(String message,String hashAlgorithm) throws Exception
	{
		//MessageDigest md = MessageDigest.getInstance("MD5"); 		//Ürettiği Özet Değeri(bit) ----> 128
		//MessageDigest md = MessageDigest.getInstance("SHA-1");        //Ürettiği Özet Değeri(bit) ----> 160
    		//MessageDigest md = MessageDigest.getInstance("SHA-224");      //Ürettiği Özet Değeri(bit) ----> 224
		//MessageDigest md = MessageDigest.getInstance("SHA-256"); 	//Ürettiği Özet Değeri(bit) ----> 256
		//MessageDigest md = MessageDigest.getInstance("SHA-384"); 	//Ürettiği Özet Değeri(bit) ----> 384
		//MessageDigest md = MessageDigest.getInstance("SHA-512"); 	//Ürettiği Özet Değeri(bit) ----> 512
		
		MessageDigest md = MessageDigest.getInstance(hashAlgorithm);	
		md.update(message.getBytes());
	
		byte[] digest = md.digest();
				
		StringBuffer strBuffer = new StringBuffer();
		for(int a : digest)
			strBuffer.append(Integer.toString((a & 0xFF) + 0x100,16).substring(1));
				
		return strBuffer.toString();
	}
	
	public static void main(String[] args) throws Exception
	{
		String message="Your message.";
		String hashAlgorithm = "SHA-256";
		String messageDigest = hashFunction(message,hashAlgorithm);
		System.out.println(messageDigest);
	}
}
```



Şu an Java Security kütüphanesinde SHA-512/224 VE SHA-512/256 hash fonksiyonları desteklenmemektedir. JDK 13 ile bu fonksiyonların ekleneceği açıklandı. İlgilenmek isteyenler bu <a href="https://seanjmullan.org/blog/2019/08/05/jdk13" target="_blank">linki </a> inceleyebilir.
