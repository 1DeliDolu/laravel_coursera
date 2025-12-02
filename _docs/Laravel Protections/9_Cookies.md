# 🍪 Cookies

Bu videoda cookies konusundan bahsedeceğim. Ve diğer güvenlik videosu yapanların aksine, bu video boyunca hiçbir cookie esprisi yapmayacağıma söz veriyorum. “Coda’s Delicious”, “isteğin teslimi çok lezzetliydi” ya da “cookie expiration → bayatlama” gibi şeyler söylemeyeceğim. Gerçi çoğunu söyleyip içimden atmış oldum bile.

Hızlıca başlayalım: Cookies aslında nedir, bunu anlayalım. Kullanıcıya gönderilen küçük veri parçalarıdır; “bits” derken gerçekten bit ve byte anlamında değil, küçük veri yapıları anlamında kullanıyorum. Genellikle key–value şeklinde saklanırlar.

Bunlar kullanıcının tarayıcısında depolanır ve her HTTP isteğinde header olarak otomatik şekilde sunucuya geri gönderilir. Basit aslında — HTTP state tutmadığı için cookies bir tür state taşıyıcısı gibi düşünülebilir. Ayrıca kullanıcıya ait bazı bilgilerin daha sonra sunucu tarafından kullanılmak üzere saklanması için de işlevseldir.

Laravel, oldukça güvenli bir cookie yönetim sistemi sağlar, fakat hangi framework’ü kullanırsanız kullanın PHP seviyesinde bazı best practice’leri bilmek önemlidir. Cookies küçük olmalıdır; her istekte gönderildikleri için mümkün olan en az veri saklanmalıdır. Ne kadar çok veri gönderilirse isteğin süresi de o kadar uzar. Tarayıcılar bu cookies’leri her zaman verildikleri domain için göndereceğinden, cookie gönderilmesini istemediğiniz statik kaynakları farklı bir subdomain veya sunucudan sunmayı düşünebilirsiniz.

Bu, görüntüler veya state gerektirmeyen statik dosyalar için iyi bir yaklaşım olur. Evet, ekstra bir DNS lookup olabilir ama bu bir kez olur. Çok daha fazlası ise her istekte cookie’nin gereksiz yere gönderilmesidir.

Cookies şifrelenmelidir — varsayılan olarak clear text’tir, bu yüzden verileri şifrelemek iyi bir alışkanlıktır. Zaten içinde gizli ya da hassas bir şey saklamamalısınız, çünkü kullanıcıya veriyorsunuz. Ama şifreleme, userside cookie yapısına bakanların kod hakkında tahmin yürütmesini veya potansiyel güvenlik açıklarını araştırmaya başlamasını zorlaştırır. Ayrıca cookies içinde hiçbir zaman erişimi artırabilecek ya da değiştirebilecek bir bilgi tutulmamalıdır. Yani cookie’deki açık bir değere göre authorization seviyeleri değişmemelidir. Cookies tercih veya seçim gibi kullanıcı tarafından değiştirilmesi doğal olan bilgiler içerebilir.

Şimdi Laravel cookie sistemine geçelim. Laravel’de cookies varsayılan olarak şifrelenmiştir — bu güzel bir best practice. EncryptCookies middleware’i web routes içinde varsayılan olarak tanımlanmıştır. Ancak içinde accept adlı bir array vardır; şifrelenmesini istemediğiniz cookie isimlerini burada belirtebilirsiniz. Üçüncü parti bir kodun şifrelenmemiş bir cookie’ye ihtiyaç duyduğu durumlarda bu işe yarar. Ya da bir honeypot veya tripwire kurgusu yapıyorsanız, şifrelenmemiş bir cookie bilgiyi toplamak için iyi bir yol olabilir. Değerler asla değişmemeli, değişiyorsa uygun aksiyon alınmalıdır. Honeypot ve tripwire konularına ileride değineceğiz.

Bir cookie set etmek için response üzerinde with cookie metodunu çağırabilirsiniz. Örnekte dünyada birçok link içeren bir landing page controller’ım var. Her link benzersiz bir ref query parametresi barındırıyor. Bu parametreyi URL’den alıp bir gün boyunca saklayacak bir cookie olarak ekliyorum — 1444 dakika. Sonra landing page view’ını render eden response’u döndürüyorum.

Bu, landing page’e farklı kaynaklardan gelen kullanıcıların signup sürecine geçtiğinde ilk nereden geldiklerini gün içinde takip etmek için kullanılabilir.

Bir response’a erişiminiz yoksa cookie facade’ını kullanabilirsiniz. Cookie set etmek için kullanılan metodun adı queue’dur çünkü cookie’yi request–response lifecycle’ı için sıraya alır. Laravel, arka planda Symfony’nin cookie yönetim kodunu kullanır veya PHP’nin setcookie metoduyla aynı parametreleri kabul eder.

Bir cookie değerini almak için request instance’ı üzerindeki cookie metodunu kullanabilirsiniz. Örnekte uygulamam için bir beta test signup formu saklıyorum. Veriyi alıyorum ve bir önceki örnekte landing page’de sakladığım cookie’den referans bilgisini alıyorum. Sonra veriyi kaydedip bir teşekkür sayfası gösteriyorum. Request’e erişiminiz yoksa cookie facade’ının get metodunu kullanabilirsiniz.

Bu bir Secure Laravel kursu olduğu için web teknolojilerinin en derin detaylarına girmiyorum — bildiklerinizi Laravel ile güvenli ve etkili şekilde nasıl kullanacağınızı anlatıyorum. Fakat bazı kritik noktaları hatırlatmam gerekiyor; örneğin tarayıcının cookies’i alma, gönderme ve expire etme süreçlerini kontrol ettiği gerçeği gibi.

Cookies’in çalışabilmesi için sunucunun onları göndermesi yeterli değildir; tarayıcının da onları alabilmesi ve her istekte geri gönderebilmesi gerekir. Sunucu bir cookie’nin expired olduğunu belirttiğinde, tarayıcının bunu doğru şekilde işleyeceğine güveniriz. Bu yüzden cookie expiration, session destroy ile aynı şey değildir. Tarayıcı expiration’ı yok sayabilir ve cookie’yi göndermeye devam edebilir. Cookies üzerine kurulu mekanizmalar tasarlarken bunu düşünmelisiniz.

Laravel ayrıca cookie expire etmeyi kolaylaştıran bir yöntem sağlar. Düz PHP’de geçmiş bir zaman damgası ayarlamak zorundasınız. Laravel’de response üzerinde without cookie metodunu çağırarak cookie’nin expire olacağını söyleyebilirsiniz; Laravel geri kalanını halleder. Bir önceki beta signup örneğinde redirect’i without cookie ile göndermeliydim ki tracking bilgisi silinsin. Artık gerekli değildi ve her istekte gönderilmesini istemem.

Response’a erişiminiz yoksa cookie facade’ındaki expire metodunu kullanabilirsiniz.

Bu video ve ziyaretçi takibi konusuna ilişkin bu kurs böylece tamamlanıyor. Laravel, sessions ve cookies yönetimini çok kolay hale getirir ve varsayılan olarak oldukça güvenli şekilde yapılandırılmıştır. Yine de session ID’lerini döndürmeyi unutmayın, authorization’ı cookie değerlerine güvenerek yapmayın, sessions ve cookies içindeki veriyi küçük tutun.
