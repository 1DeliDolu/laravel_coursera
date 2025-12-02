# 🧹 Kullanıcı verisini görüntüleme ve escape etme

Bu videoda, Laravel’in yerleşik escaping araçlarını kullanarak kullanıcı ve üçüncü taraf verilerini güvenli bir şekilde nasıl görüntüleyeceğimizi konuşacağız. Veriyi escape etmekten bahsettiğimizde aslında özel kontrol karakterlerini, görsel olarak aynı görünen fakat hiçbir şey kontrol etmeyen bir şekilde temsil ediyoruz. Bu, “HTML’de büyüktür/küçüktür işaretlerini göstermek istiyorum ama bunların HTML tag’i olarak yorumlanmasını istemiyorum, o yüzden bunları HTML entity’lerine dönüştüreyim” demek gibidir. Laravel’de bu escaping işlemi Blade içinde gerçekleşir; Blade, içerik ve response üretmek için kullanılan işaretleme dili ve işlevselliğidir.

Burada yalnızca kullanıcı verisi değil, üçüncü taraf verisi de diyorum. Kullanıcının gönderdiği veri olabilir ama JSON ya da RSS feed’leri gibi üçüncü taraflardan alınan bilgiler de olabilir. Kısacası, programcılar olarak doğrudan bizim oluşturmadığımız tüm verilerin escape edilmesi gerekir.

Escape edilmemiş verinin tehlikesine cross-site scripting veya XSS denir. Bu, temelde bir siteden başka bir siteye script enjekte edilmesi anlamına gelir; bu diğer site tam bir [inaudible] linki ya da URL’si olabilir veya sadece gönderilen JavaScript’in sizin sitenizde çalışması beklentisiyle yapılmış bir gönderi olabilir. Bunu detaylıca şöyle özetleyebiliriz: Kullanıcı verisini escape etmek isteriz, çünkü uygulamamız için script çalıştırması veya HTML yazması gereken tek kişi biziz.

Blade’de veri görüntülemenin standart yöntemi, bazen mustache syntax denilen çift süslü parantezdir. Varsayılan olarak Laravel, veriyi HTML’e gidiyormuş gibi escape eder; Blade zaten HTML üretir. Temelde PHP’deki HTML entities metodunun belirli bir versiyonunu/konfigürasyonunu çağırır. Sağdaki örnekte, kullanıcı HTML veya JavaScript gönderse bile bu escape edilerek sadece ekranda temsil edilir, çalıştırılmaz. Bu oldukça havalıdır çünkü bazı PHP framework’lerinde ve WordPress gibi şeylerde bu varsayılan davranış değildir.

Uzun yıllar boyunca veri escape etmek fazladan iş gerektiriyordu. Varsayılan yöntem bu değildi ve bu durum birçok güvenlik açığına yol açıyordu.

Programcıların önceki hatalı bir istekten gelen veriyi enjekte etmelerine olanak veren yöntemlerden biri Blade’deki old metodudur. old metodunun kendisi veriyi escape etmez, onu nasıl kullandığınız belirler. Bu örnekte, önceki istekteki title alanının eski değerini gösteriyorum, fakat çift süslü parantezlerle sarıldığı için escape edilir.

Bazen ham veri (raw data) göstermeniz gerekebilir.

Hayır, bunu yapmayın. Yapmayın. Ama bazen mecbur kalabilirsiniz. Örneğin, bir WYSIWYG editör içerikleri Blade’e HTML olarak gönderebilir ve iş gerekçesiyle bunu desteklemeniz gerekebilir. Bu durumda gerçekten çok iyi filtrelemeli ve sanitize etmelisiniz, yoksa hiç yapmayın; başka bir yol bulun. Birkaç slayt sonra bunun alternatifini anlatacağım.

Eğer escape edilmemiş veri göstermeniz gerekiyorsa, tek süslü parantez ve iki ünlem işaretinden oluşan syntax’ı kullanabilirsiniz. Ya da gerçekten gerekirse PHP directive’leri içeren bir PHP bloğu açıp veriyi saf PHP ile echo edebilirsiniz.

Kullanıcıya geliştirilmiş veri biçimlendirme (bold, italik, hatta görseller) sağlamak zorunda olduğunuz durumlarda Markdown kullanmanızı öneririm. Laravel, GitHub-flavored Markdown için yerleşik işlevselliğe sahiptir. Artık birçok WYSIWYG editör HTML yerine Markdown üretmektedir.

Bu örnekte, evet görünüşte escape edilmemiş veri kullanıyoruz ama bu veri önce markdown parser’dan geçiriliyor. Parser, HTML’i escape ediyor, ardından Markdown formatını onurlandıracak HTML’i render ediyor. Böylece verinin temiz olduğuna güvenebilirsiniz. Bu gerçekten çok iyi.

Güvenli yapılandırmanın çalıştığını görmeyi ve yanlış yapılırsa neler olabileceğini göstermeyi severim. Bu nedenle önce başarılı kurulumla, sonra escape edilmeyen kullanıcı verisi felaketiyle bir demo yapacağım.

Bu demo için diyelim ki “meat” hakkındaki blog yazısındayız. Ziyaretçilerin yorum ekleyebileceği bir yorum bölümü var. Authentication yok, hiçbir şey yok. Bu örnek için basit tutacağım.

Yoruma normal bir metin yazalım. Güzel, sayfaya başarıyla ekleniyor. Peki JavaScript eklersek? Bir alert göndererek JavaScript çalıştırıp çalıştıramayacağımı göstereceğim. Hayır, hiçbir JavaScript çalışmadı çünkü kullanıcı verisi escape ediliyor. Bunun yerine HTML entity’lerine dönüştürülmüş hali gösterildi.

Şimdi arka planda yorumları sıfırlayacağım ve kullanıcı verisini escape etmeyi bırakacağım. Veriyi doğrudan sayfaya echo edeceğim.

JavaScript’i tekrar göndereyim ve submit edeyim: İşte bir alert çıktı. Artık escape edilmediği için JavaScript çalıştı. Yalnızca gönderdiğim anda değil; sayfayı yenileyeyim, yine çalışıyor. Yani herhangi bir ziyaretçi için çalışacaktır. İşte veriyi escape etmemiz bu yüzden önemli. Kullanıcılar sayfamıza script enjekte edebilir.

Web güvenliğini öğrenirken beni en çok geri tutan şeylerden biri, tıpkı burada gösterdiğim alert tabanlı örneklerdi. “Alert çalışsa ne olur ki?” diye düşünüyordum. Size, bunun yerine daha etkili başka bir örnek göstermek istedim. Eğer siz de böyle düşünüyorsanız bu alternatif yardımcı olabilir.

Kodun en üst kısmındaki örnekle blog yazısını oluşturdum. İlk olarak tek bir görsel oluşturuyor. Benim göndermiş olabileceğim şey buydu. “Alert vermekten ne farkı var?” diyebilirsiniz. Farklı bir içerik göndereyim ve bunun neden önemli olduğunu daha iyi görün.

Kodun en üstünde blog gönderisini oluşturduğumu hayal edin. Bu kod, JavaScript içinde yeni bir image nesnesi oluşturuyor.

Evet, script tag’lerini gönderdim. Bu kod o iki script tag arasına yerleşti. JavaScript’te yeni bir image nesnesi oluşturuyor, sonra source niteliğini kötü adamın sitesine bir GET isteği atan bir URL’ye ayarlıyor. Bu URL’de cookie adında bir GET parametresi var ve değeri document.cookie. Bu, asla vermek istemeyeceğimiz bir şeydir.

Alttaki görüntüde, benim Laravel sitemde unescaped olarak render edilen JavaScript’i yüklediğim anın ekran görüntüsü var. Son satırda, escape-data.test isimli sitemin JavaScript’i çalıştırarak kötü adamın web sitesine bir GET isteği yaptığını görebilirsiniz. Üstelik bu istekte X-XSRF token cookie’si ve session ID gibi diğer çerezlerim de gönderiliyor.

Artık kötü adamın sitesi tarayıcımdaki çerezlerin bir kopyasına sahip ve bunları kullanabilir.

İşte XSS bu yüzden büyük bir mesele. XSS ile yapılabilecek daha kötü şeylerin bir sürü örneği var ama sadece bunun rastgele, sıkıcı alert’lerden ibaret olmadığını, gerçekten ciddi bir problem olabileceğini göstermek istedim. Alert sadece XSS’in çalıştığını zarar vermeden kanıtlamanın yaygın bir yoludur.

Kullanıcı veya üçüncü taraf verisi görüntülerken escape ettiğinizden emin olun. Bu oldukça kolaydır ve çoğu zaten Laravel’e yerleşik gelir. Gelişmiş kullanıcı veri formatlaması gereken durumlarda ise unescaped HTML yerine Markdown kullanmayı düşünün.
