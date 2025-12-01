# 🔐 Kimlik Doğrulama Kitleri / Scaffolding

Bu derste Laravel’de kullanılabilen kimlik doğrulama (authentication) sistemlerine bakacağız. Kimlik doğrulama ve bir sonraki kursta ele alınacak olan yetkilendirme (authorization), derin ve karmaşık konulardır. Bu videoda yaygın senaryoları çözen araçlara ve daha az yaygın durumlar için birkaç ipucuna değineceğiz. Amacımız; Laravel’de en sık kullanılan doğrulama yöntemlerini, denenmiş ve güvenilir kimlik doğrulama kitleriyle tanıtmak.

Biliyorum, “Benim kullanım senaryom farklı” diye düşünüyorsun. Çoğu durumda bu Laravel authentication kit’lerinden biri uygulamanızın ihtiyaçlarını karşılayacaktır. Eğer gerçekten benzersiz bir doğrulama ihtiyacınız varsa, bunu da ileride ele alacağız.

Laravel’de kimlik doğrulamasını nasıl yapıyoruz? Laravel gibi araçlar olmadan uzun süre program yazdıysanız, yeni bir projeye başlarken hissettiğiniz o endişeyi bilirsiniz: “Giriş yap, şifremi unuttum, çıkış yap… Projenin havalı kısımlarına başlamadan önce bir hafta bunlarla uğraşacağım.”

Artık öyle değil.

Laravel **authentication kits** sağlar. Bunlar framework’ün ve projenin dışında olan ama uygulamaya kolayca ve hızlıca doğrulama fonksiyonelliği ekleyen paketlerdir.

Laravel, kimlik doğrulama için birçok yerleşik işlev sunar; fakat framework’ün amacı araçlar sağlamaktır, sizin için tam bir uygulama yazmak değil. Teknik olarak bu mekanizmaları kendi istediğiniz şekilde kullanabilirsiniz ama bu tekerleği yeniden icat etmek olur. Bu kitler ise yerleşik işlevleri yapılandırmanın standart ve düzenli yollarıdır; bazen birkaç yeni fonksiyon da ekleyerek doğrulama sisteminizi tamamlar.

Ben sebepsiz paket kurmayı sevmem. Kod şişirir, yavaşlatabilir. Fakat bu doğrulama kitlerinde öyle bir sorun yok. Laravel core ekibi tarafından korunuyorlar ve adeta projenin birinci sınıf vatandaşları gibiler. Ayrı olmalarının sebebi, küçük uygulamaların tüm auth sunucusu kodunu yanında taşımak zorunda kalmamasıdır.

Bir başka sebep: **Test edilmiş olmaları.** Kitlerin birim testleri vardır ve binlerce uygulama bunları kullanır. Güvenlikte bazen gizlilik işe yarar ama kimlik doğrulama sistemi bunun yeri değildir. Ayrıca zaman kazandırırlar. İlk haftayı kullanıcı sistemi yazarak geçirmenize gerek kalmaz. Üstelik endüstri standartlarını uygularlar. Örneğin session tabanlı sistemler oturum kimliğini döndürür, OAuth server güvenli imzalama kullanır. Laravel ile entegrasyonları hazır ve pürüzsüzdür.

Özel doğrulama sistemi yazabilirsiniz, fakat gerçekten gerek yok. Gelecekteki videoda göreceğiniz gibi, Laravel’de doğrulama sisteminin yalnızca bazı bölümlerini özelleştirip tamamını sıfırdan yazmadan kendi ihtiyacınıza uyarlayabilirsiniz.

Devam etmeden önce authentication’un ne olduğundan bahsedelim. Laravel’de authentication genelde bir user modeliyle ilişkilidir, çoğunlukla Eloquent model üzerinde çalışır. Bu böyle olmak zorunda değildir; teknik olarak şirketleri, takımları veya başka varlıkları doğrulayabilirsiniz. Eğer team authentication istiyorsanız Laravel Jetstream paketine bakmalısınız; birazdan bundan bahsedeceğim.

Benim de bazı projelerde user yerine başka nesneleri doğruladığım oldu. Geriye dönüp baktığımda en iyi yaklaşımın user doğrulayıp user objesine role eklemek olduğunu görüyorum. Ama bazen projeye sonradan gelir veya tasarımla boğuşurken bazı şeyleri öğrenirsiniz. Yine de alternatif yoldan gitmek isterseniz, standart user modeline bakabilirsiniz. Laravel auth user modelini extend eder. Bu model birçok contract veya interface ve trait’ten oluşur. Benim en çok ilgilendiğim ise authenticatable contract’tır. Buraya girerseniz uygulanması gereken tüm metodları görürsünüz. Bu metodları uygulayan bir obje oluşturursanız, tamamen kendinize ait bir doğrulama sistemi yazmış olursunuz. Tavsiye etmem ama mümkündür.

Şimdi authentication kit’lere geri dönelim.

---

## 🌬️ Laravel Breeze

Laravel Breeze, düşünmeniz gereken ilk doğrulama kitidir.

Basit bir sistemdir ve:

* login
* registration
* password reset
* email confirmation

işlevlerini sunar. Route’lar, controller’lar ve Tailwind CSS ile Blade dosyaları oluşturur. Bunlar proje dizinine yayınlandığı için istediğiniz gibi özelleştirebilirsiniz.

Standart bir web uygulaması başlangıcı için idealdir. Laravel Auth sistemiyle uyumludur, varsayılan user modelini kullanır, bildiğiniz route ve işlemleri içerir ama özelleştirmeye de oldukça açıktır. Birkaç komutla kurulur: bir haftalık işi bir saate indirir. Üstelik test edilmiş ve güvenlidir.

Kurulum adımları:

1. Breeze paketini Composer ile kurun.
2. `php artisan breeze:install`
3. NPM bağımlılıklarını yükleyin ve derleyin.
4. Migration çalıştırın.
5. Tarayıcıda test edin.

Kayıt, giriş, yanlış şifre, olmayan kullanıcı ve şifre sıfırlama gibi tüm temel özellikler hazırdır.

---

## 🧱 Laravel Fortify

Breeze gibi ama  **UI olmadan** .

Kendi frontend kodunuzu kullanmak istiyorsanız mantıklıdır. Ancak çoğu durumda Breeze kurup Blade dosyalarını düzenlemek daha kolaydır.

---

## 🚀 Laravel Jetstream

Jetstream, Breeze'in gelişmiş halidir.

Şunları ekler:

* Two-factor authentication
* API authentication
* Team management

Tailwind CSS ile gelir. Inertia JS veya Livewire ile kurulabilir.

API doğrulaması için Sanctum kullanır (birazdan bahsedeceğim).

Jetstream güçlüdür ama gereksiz özellikler saldırı yüzeyini artırabilir. Bu yüzden önce Breeze ile başlayın, sonra ihtiyaç duyarsanız Jetstream’e geçin.

---

## 🌐 Laravel Socialite

Socialite, OAuth client desteği ekleyen doğrulama kitidir. Yani kullanıcılarınız uygulamanıza şu hesaplarla giriş yapabilir:

* Facebook
* Twitter
* LinkedIn
* Google
* GitHub
* GitLab
* Bitbucket

Daha fazlası için Socialite Providers topluluğu vardır (Apple, Okta, Reddit, vb.).

Socialite, Breeze veya Jetstream ile birlikte kullanılabilir.

Provider’a göre OAuth 1 veya OAuth 2 destekler.

Token ve secret değerlerini saklarken Laravel encryption kullanmanızı öneririm.

---

## 🔑 Laravel Sanctum (API Authentication)

Sanctum, API doğrulaması için hafif bir çözüm sunar.

Özellikle SPA’lar (Single Page Application) için uygundur; tüm bir OAuth sistemi kurmak aşırı olabilir.

Sanctum iki doğrulama yöntemi sağlar:

1. **API token’ları**

   Uzun süreli fakat gerektiğinde iptal edilebilir token’lardır.

   Authorization header üzerinden gönderilir.
2. **SPA session authentication**

   Kendi frontend uygulamanızla çalışırken mükemmeldir.

Sanctum, her token için **ability** tanımlamanıza izin verir; ancak bu yola girecekseniz bence zaten Sanctum’u aşmaya başlamışsınız demektir. Sanctum’un sunduğu diğer mekanizma ise Laravel web uygulamalarında yer alan  **standart session cookie authentication** ’dır. Bu oldukça kullanışlıdır çünkü şöyle düşünün: Blade dosyaları ve birkaç Vue bileşeni içeren hibrit bir uygulama yaptınız diyelim. Bu bileşenler, özelleştirilmiş veri almak için bir endpoint’e istek atabilirler; bu endpoint hem token ile hem de mevcut site kullanıcısı olmanız sayesinde session cookie ile doğrulanabilir. Böylece hem web siteniz hem de kontrol ettiğiniz başka uygulamalar — örneğin bir mobil uygulama — arasında API endpoint’lerini tekrar kullanmak çok daha kolay olur.

Son kimlik doğrulama kiti  **Laravel Passport** ’tır. Passport, Laravel uygulamanız için tam bir **OAuth 2 server** sağlar. Yani doğrulama, token’lar, OAuth scopes ve daha fazlasını sağlar. Ancak dürüst olmam gerekirse, ben projelerimin %95’inde Passport kullanma ihtiyacı duymuyorum. Genellikle başka bir yoldan gidiyorum. Yanlış anlaşılmasın, araçları oldukça iyi ve kullanımı kolaydır, fakat karmaşıktır, çok fazla yapılandırma ister ve bakım maliyeti yüksektir.

Benim genel kuralım şudur:

**Kendi uygulamam dışında kimse için doğrulama yapmıyorsam, authentication provider rolünü üstlenmem.**

Aksi hâlde, doğrulamayı zaten bu işi profesyonelce yapan bir servise devretmek en iyisidir.

Bununla birlikte, en çok dikkat etmeniz gereken şey  **anahtarlarınızın güvenliğidir** . Anahtarlarınızı bağımsız dosyalar yerine environment ve config sistemine eklemenizi öneririm. Böylece tüm gizli bilgileri yönetmek için tek bir merkezi yeriniz olur.

Bir diğer dikkat edilmesi gereken konu, **personal access token** oluşturmaktır. Bunlar kullanıcıların tüm süreci yaşamadan token oluşturmasına yarayan token’lardır. Eğer bir kullanıcı adına bu işlemi sık sık kendiniz yapıyorsanız, gerçekten OAuth server’a ihtiyacınız olup olmadığını değerlendirin — belki de Sanctum kullanmanız daha doğru olabilir.

Laravel’de kullanabileceğiniz kimlik doğrulama kitlerini böylece tamamladık. Şimdi muhtemelen şöyle düşünüyorsunuz:

“Tamam bunlar güzel ama benim giriş yaptırmak için veritabanındaki dört alanı kontrol etmem gerekiyor ya da erişim vermek için dahili CRM servisimize istek atmam gerek. O zaman Laravel’in yerleşik kitlerini kullanamam ki!”

Aslında kullanabilirsiniz — ve bunu bir sonraki videoda konuşacağız.
