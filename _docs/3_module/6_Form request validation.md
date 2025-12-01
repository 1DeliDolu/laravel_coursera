# 📝 Form Request Doğrulaması

Bu videoda **form request sınıflarından** bahsedeceğiz. Kullanıcı isteklerine doğrulama uygulamak için en sevdiğim yöntemlerden biridir. Form request sınıfları, **Illuminate\Foundation\Http\FormRequest** sınıfını genişleten sınıflardır. Normalde Controller metodlarında type-hint yaptığınızda standart bir request nesnesi enjekte edilir; ancak özel request sınıfınızı yazarsanız onun enjekte edilmesini sağlarsınız. Bunu doğrulama kurallarını, özel mesajları, yetkilendirmeyi vb. sunan bir request sarmalayıcısı olarak düşünebilirsiniz. Laravel, bir form request sınıfı varsa ve rules metoduna sahipse, bu istek için doğrulayıcıdan geçirilmesi gereken kuralların bunlar olduğunu otomatik olarak bilir.

Kısaca hatırlayalım, doğrulamanın üç yolu vardır:

Birincisi, validator facade ile bir instance oluşturmak.

İkincisi, Controller içinde standart request sınıfı metodlarını kullanmak.

Üçüncüsü ve tavsiye ettiğim yöntem ise bir  **form request sınıfı kullanmak** .

Ek olarak, bu sınıfların tamamen benzersiz olması gerekmez. Okunabilirliği artırmak için öyle yapabilirsiniz, fakat bazen store request ve update request tamamen aynı olabilir. Böyle bir durumda update sınıfının store sınıfından extend edilmesini sağlarım; bu şekilde gerektiğinde update için özelleştirme yapabilirim ama aksi hâlde store doğrulamasıyla aynı kalır.

Doğrulama harika ama veriyi de almamız gerekir. Request instance’larında get metodu ile belirli bir anahtarı alabilir, all metodu ile tüm isteği çekebilirsiniz. Ancak **validated** metodunu kullanmanızı tavsiye ederim. Bu size temiz ve doğrulanmış bir veri kopyası verir. Çünkü biri fazladan veri gönderirse, all metodunu çağırdığınızda doğrulanmamış veriyi de alabilirsiniz. validated ise yalnızca doğrulanmış ve doğrulamayı geçen verileri döndürür. exclude doğrulama kuralını kullandığınızda, o veri request'te bulunsa bile validated çıktısında yer almaz. Bu oldukça kullanışlıdır.

Şimdi bir form request sınıfının bölümlerine bakalım. İlk olarak authorization gelir. Bunun üzerinde çok durmayacağım çünkü yetkilendirmeyi başka bir kursta detaylı ele alacağız. Form request içinde yetkilendirme yapmanızı pek önermem, yalnızca gelen verinin ve kullanıcının izinleriyle çok alakalı spesifik durumlarda kullanılmalıdır. Örneğin burada gösterildiği gibi, yalnızca admin’lerin diğer admin’leri yükseltmeye yetkili olup olmadığını kontrol edebilirsiniz; fakat “kullanıcı düzenleme yetkisi var mı?” gibi genel yetkileri burada kontrol etmezsiniz.

En önemli kısım **rules** metodudur. Validator facade’ın ikinci parametresi bir kural dizisi alır ve bu metod da bir dizi döndürür. Önceki videolarda gösterdiğim kural örnekleri muhtemelen bu metoddan dönen kurallardı. Bu metod bir array döndürüyorsa, bu kurallar o request için uygulanır.

Son olarak messages metodu vardır. Laravel doğrulama kuralları için harika varsayılan hata mesajları sağlar, ama isterseniz belirli bir alan ve kural için özelleştirilmiş bir hata mesajını burada tanımlayabilirsiniz. Bu güvenlikten çok kullanıcı deneyimiyle ilgili bir şeydir. Elbette tamamen gizlilik yaklaşımına inanıyorsanız o başka, ama burada yeri değil.

Küçük bir sır — bu fazla iddialı oldu — sadece küçük bir ipucu: Form request sınıflarında filtreleme mantığı da yazabilirsiniz. Doğrulama yaşam döngüsü, doğrulamadan önce filtrelemeyi çalıştırır. Bu, çok hassas doğrulama kurallarınız varsa ama kullanıcı için bazı verileri manipüle etmek veya bazı potansiyel sorun çıkarabilecek verileri kaldırmak istiyorsanız faydalı olabilir.

Şimdi bu kodun form request içinde nasıl göründüğüne bakalım. Bu örnekte forum mesajlarını depolamak için request doğrulaması yapıyorum. Önce kurallara bakalım. Forum gönderisinde bir title ve bir message olabilir. Message örneğin BBCode veya Markdown destekleyebilir. Mesajın görüntülenmesi muhtemelen uygun şekilde filtreleniyordur, fakat bazen insanlar HTML formatlama koymaya çalışır çünkü bazı formlar buna izin verir veya tarayıcı eklentileri bunu otomatik ekler. Pek çok şey gördüm.

Şimdi prepareForValidation metoduna çıkalım. Bu, doğrulamadan önce çalışır. Bu kod request’ten message parametresini alır. Eğer değer boş bir string değilse ve null değilse, merge ederiz, yani message alanını strip_tags uygulanmış versiyonuyla değiştiririz.

Form request sınıfları benzersiz işlevselliklere sahiptir ve tüm bunları güzel bir kapsüllenmiş yapıda toplar. Kullanıcı tarafından gönderilen verilerle Controller’larda çalışırken form request kullanmanızı kesinlikle öneririm.
