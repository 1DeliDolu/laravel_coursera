# 🧨 Tripwire’ların uygulanması

Bu kursta **tripwire** ve **honeypot** tekniklerini ele alacağız. Bu videoda ise kötü davranışları tespit edip durdurmak için tripwire’lara odaklanıyoruz.

Tripwire nedir?

Gerçek hayattaki halinin elektronik bir versiyonu olarak düşünebilirsiniz. Filmlerde koridor boyunca gerilmiş bir tel olur; kötü adam içeri girince teli tetikler ve bir uyarı veya patlama gerçekleşir. Burada da bunun elektronik bir versiyonunu yapacağız — tabii ki patlamasız ve zararsız hâliyle.

Tripwire’lar, belli  **istenmeyen bir senaryo gerçekleştiğinde tetiklenen tuzak noktalarıdır** . Bunların önemli olmasının sebebi, güvenlik açıklarının çoğunun kaynağı olan bir senaryoya hitap etmeleridir:

**“İstenmeyen bir şey oldu ama nasıl olduğunu bilmiyoruz.”**

Farklı bir şekilde ifade edelim:

Programlama yaparken, bir davranışı engellemek istiyorsanız bir `if` yazarsınız; yani kötü davranışı tanımladınız, o davranışa nasıl ulaşılabileceğini anladınız ve bu yolu kesen bir kod yazdınız.

Tripwire ise farklıdır:

**Olmasını istemediğiniz bir durumu tanımlarsınız ama bu duruma bir kullanıcının nasıl ulaşabileceğini bilmek zorunda değilsiniz.**

Koridorda biri yürüyor ve o koridorda kimsenin olmaması gerekiyor. Nasıl geldiğini bilmiyorsunuz ama geldiği anda tuzak tetikleniyor.

Bazı tripwire uygulamaları rate limiting gibi genel davranışlara odaklanabilir; belirli türde çok sayıda istek gelirse devreye girerler. Bu videoda daha hedeflenmiş (targeted) tripwire'lardan bahsedeceğiz.

Ek olarak, mümkünse bu tür tripwire’ları **web application firewall** (WAF) veya **IDS** (intrusion detection system) içinde konfigüre etmek daha iyidir. Kötü aktörleri Laravel katmanına gelmeden durdurmak en iyisidir.

Ama diyelim ki Laravel içinde uygulamak istiyoruz.

---

## 🎯 Laravel içinde tripwire uygulamak

İlk olarak, kullanabileceğimiz yerleşik event’lere bakabiliriz.

Saldırganların en sevdiği şeylerden biri admin hesabını ele geçirmeye çalışmaktır. Admin hesaplarının e-posta adresi veya ID’si kolay tahmin edilir:

`1`, `admin@product.com`, vb.

Bu örnekte saldırganların `admin@myproduct.com` adresine musallat olabileceğini varsayıyorum.

Laravel’in auth sistemi, başarılı girişlerde **authenticated** event’ini tetikler.

Dolayısıyla bir saldırgan şifreyi tahmin ettiyse veya bir şekilde sistemimizi kandırıp admin kullanıcısı olarak giriş yaptıysa, bu event tetiklenir.

Ben burada gerçek admin hesabımı değil, bilerek kolay tahmin edilebilir ayrı bir admin hesabı kullanıyorum — böylece kendi girişlerimde tetiklenme yaşamam.

Bu senaryo gerçekleştiğinde (admin e-postası ile login olduğunda), o IP adresini fail2ban yapılandırmamda engellenmiş IP listesine eklemek istiyorum. Siz başka bir aksiyon da yapabilirsiniz.

---

## 🧩 Kod örneği

Bir listener oluşturuyorum.

authenticated event’ini dinliyor.

Eğer event’teki user’ın email’i belirlediğim e-posta ise:

* Kullanıcıyı hemen logout ediyorum.
* IP adresini alıyorum.
* Bir fail2ban komutu oluşturuyorum (IP’yi shell argument olarak escape ediyorum).
* system() ile komutu çalıştırıyorum.

Son olarak bu listener'ı **EventServiceProvider** içindeki `listen` dizisine ekliyorum.

Bu noktadan sonra, tüm login denemeleri izlenecek.

Belirtilen admin hesabıyla giriş olursa saldırgan IP’si engellenecek.

Not:

Ben örnek için email’e göre kontrol yaptım.

Siz ID’ye göre kontrol yapabilirsiniz.

Kendi admin hesabınızı da izleyebilir ve tanımadığınız bir cihazdan giriş olursa kendinize alarm verebilirsiniz.

---

## 🔍 Observer kullanarak tripwire

Şimdi daha ağır bir örnek düşünelim:

ID’si 1 olan kullanıcı **asla** yüklenmemeli.

Bu, SQL injection saldırılarında çok yaygın bir tekniktir:

`OR 1 = 1` ile ilk kullanıcıyı çekmeye çalışırlar — ki bu genellikle admin’dir.

Burada bir observer kullanacağız.

`User` modelinde ID 1 her retrieve edildiğinde tripwire tetiklenecek.

Observer içinde `retrieved` metodunu hedefliyorum.

Model hydrate edildiğinde bu method çağrılır.

Kod:

ID’si 1 olan user yüklenmişse bir aksiyon alıyorum.

Saldırganı asla karşı saldırıya uğratmayın; etik değildir.

Sadece savunma yapın.

Ben örnek olarak IP’yi log’luyorum.

EventServiceProvider içinde observer’ı kaydediyorum.

Log’un boş olduğunu doğruluyorum, ardından:

* Tinker’da User 42’yi yüklüyorum → log boş kalıyor.
* Tinker’da User 1’i yüklüyorum → log doluyor → tripwire tetiklendi.

---

## 🛡️ Sonuç

Tripwire’lar güçlü bir savunma aracıdır.

Mümkünse bunları WAF veya IDS katmanında yapılandırın.

Ama Laravel içinde de **event** ve **observer** kullanarak etkili tripwire’lar oluşturabilirsiniz.

Tripwire tetiklendiğinde:

* saldırganı engelleyebilir,
* loglayabilir,
* alarm üretebilir,
* rate limit artırabilir,
* veya başka bir savunma eylemi gerçekleştirebilirsiniz.

Peki ya engellemek istemiyor, sadece saldırgan davranışı hakkında bilgi toplamak istiyorsanız?

O zaman **honeypot** kullanırsınız — ve bunu bir sonraki videoda ele alacağız.
