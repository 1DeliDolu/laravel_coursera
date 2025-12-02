# 🧰 En yaygın araçlar

Bu kursta kod güvenliği tarama araçlarından bahsedeceğiz. Peki bu araçlar neden güvenli Laravel kodlama öğrenme yolculuğunun bir parçası? Şöyle ki: Laravel’de en iyi ve en güvenli konfigürasyonu oluşturmaya, kullanıcı girdisini doğrulamaya, escapinge, SQL injection’dan korunmaya ne kadar dikkat ederseniz edin… Her programcı hata yapar. Ben bile kariyerim boyunca çok ciddi hatalar yaptığımı itiraf etmeliyim.

Bu hatalar bazen code review’larda, bazen otomasyon araçlarıyla veya nazik kullanıcı raporlarıyla bulunur; bazen de hackerlar ve saldırganlar tarafından keşfedilir ve kullanıcılarınıza karşı exploit oluşturmak veya sunucunuza saldırmak için kullanılır. İşte beni en çok endişelendiren bu sonuncusudur.

Kötü niyetli kişiler bazen sitenize denk gelip bir açık bulabilir, fakat çoğunlukla uygulamanızı **otomatik araçlarla** tarayıp en kolay avları ararlar. Çok fazla uygulama olduğundan, spesifik hataları tek tek aramalarına gerek yoktur; siteleri tarar, ne bulurlarsa bakarlar ve yeterince açık ya da yanlış yapılandırma bulabilirlerse hedef haline gelirsiniz.

Laravel’de güvenli kod yazmayı öğrenerek zaten çok iyi işler yapıyorsunuz ama bu gevşeyebileceğiniz anlamına gelmez. Hem **kodunuza erişimi olan araçları** hem de saldırganların dışarıdan kullanabileceği araçları kullanmak istersiniz. Sitenizi bu araçlarla tararsanız, umarız bir başkası sizden önce bulmadan zayıflıkları keşfeder ve düzeltebilirsiniz.

Bu videoda bazı kavramlardan ve web uygulamaları ile PHP uygulamaları için genel tarama araçlarından bahsedeceğiz. Bu kursın amacı bu araçların her birine detaylı biçimde girmek değil. Bunun için ayrı öğrenme yolları ve kurslar var.

Ama en azından terimlere yabancı kalmamanız için bunları yüksek seviyede tanıtmak istiyorum. Çünkü bir şey aradığınızda bilmediğiniz onlarca araçla, terimle karşılaşmak sinir bozucu olabilir. Bu kelimeleri anlamadan neye tıklayacağınızı nasıl bileceksiniz?

Hızlıca listeye bakalım:

**Server security scanning tools**

Bunlar yazılım ve donanım sunucunuzun yapılandırmasına odaklanır. Eski yazılım versiyonlarını veya saldırganların sunucuya sızmasına yol açabilecek yanlış yapılandırmaları bulur.

**Network scanning tools**

Programcılar genelde buna ihtiyaç duymadıklarını düşünürler ama bazı exploitler yanlış yapılandırılmış ağları veya uygulama kodunu kandırabilecek istekleri kullanır. Bu araçlar ağ yapılandırmanızı tarar ve istekleri güvenlik sorunları için izler.

**Static analysis tools**

Bu araçlar kod tabanını okur, kodun yapısını analiz eder ve olası açıkları tespit eder. Kodun derin seviyedeki yapısına odaklanırlar, fakat kodu çalıştırmazlar.

**Linting ve code styling tools**

Genelde kalite araçları olarak görülür ama ben buna katılmıyorum: bunlar da güvenlik aracıdır. Kod kalitesi araçları, doğru kontrol yapıları, küçük metotlar gibi en iyi uygulamalara uyup uymadığınızı kontrol eder.

Linting ise daha genel olup yanlış yapılandırmaları veya doğrudan hataları bulur.

İkisi birlikte, kırılabilecek veya karmaşık bölümleri bulmanıza yardımcı olur. Bildiğiniz gibi, daha fazla karmaşıklık = daha fazla saldırı vektörü.

**SQL injection tools**

Bu araçlar uygulamanızın kullanıcı girdisini nasıl kullandığını anlamaya çalışır ve SQL injection tetikleyebilecek yaygın veri kalıplarını dener. Bunu hızlıca ve otomatik olarak yaparlar.

**XSS scanners**

Bu araçlar uygulamaya farklı türde script enjeksiyonları yapmayı dener. Değişik encoding’ler, farklı yöntemler, farklı sayfalar…

Başarılı XSS olur da sayfanın davranışını değiştirirse bunu raporlarlar.

**Fuzzers**

Bu araçlar belirli veriyi alıp çok sayıda permütasyon dener (fuzzing). Tüm sınırları test edip sitenin fonksiyonelliğini bozmaya çalışırlar.

Bu nedenle pagination’ın bir güvenlik aracı olduğunu konuşmuştuk; çünkü fuzzers 10, 100, 1000 öğe yüklemeyi dener ve nerede kırılacağını görmek ister.

Bu, mevcut tüm araçların kapsamlı bir listesi değil ama uygulamanızı taramaya nereden başlayabileceğiniz konusunda genel bir fikir verir.

Kesinlikle bu araçların her biri için özel eğitimlere göz atmanız gerekir. Bu araçları saldırganların kullanacağı şekilde kullanmayı öğrenin ki taramaları onlardan önce siz yapın ve sorunları siz fark edip çözün.

Laravel harika bir framework ve uygulama altyapısı oluşturdu. PHP’nin önceki framework ve araçlarına göre pek çok şey artık çok daha kolay. Bu, geliştiricileri yeniden motive etti ve PHP’yi canlandırdı.

Ama tüm bu harika araçlar ve doğrusu harika pazarlama sayesinde, bazı programcılar tek bir balonun içinde yaşıyor:  **Laravel balonu** .

Aramalarını sadece “Laravel support” etiketi taşıyan araçlarla sınırlıyorlar; bu kötü olabilir.

Unutmayın: Laravel dışında da bir dünya var.

Laravel, Symfony bileşenleri üzerine kuruludur; PHP ve biraz JavaScript’ten oluşur. Güvenlik konuşurken sadece Laravel’e özgü araçlara bakmayın. Laravel, Symfony, PHP, JavaScript, Vue… Hepsi için tarama araçları kullanılabilir.

Temelde herhangi bir web uygulama tarama aracı Laravel projenize de uygulanır.

Şimdi birkaçı hakkında kısaca konuşalım:

**Static analysis tools**

– **Psalm** (Vimeo tarafından geliştirilmiş)

– **PHPStan** (topluluk destekli)

– **Phan** (topluluk tabanlı)

Her birinin kendi metodolojisi vardır. Örneğin Phan false positive’leri azaltmayı hedeflerken PHPStan genişletilebilirlik üzerine odaklanır. Bunlardan biri veya birkaçı ile Laravel kodunuzu potansiyel açıklar için tarayabilirsiniz.

**Code quality tools**

Bu araçlar kod yapısına bakarak daha karmaşık veya riskli bölgeleri gösterir.

Hepsi host edilen araçlardır; kodunuza erişir, analiz eder ve CI platformlarıyla entegre olur. Bazıları açık kaynak için ücretsizdir, kapalı kaynak için ücretlidir.

— Şu anda favorim  **Codacy** , çünkü dahili araçların yanı sıra farklı araçların konfigürasyonunu destekler.

—  **PHP Insights** , karmaşıklık, kod satırı sayısı gibi metriklerle çok şey gösterir.

—  **PHPUnit** , sadece test aracı değildir; karmaşıklık ölçer.

— **PHPQA** sitesi, QA ve test araçlarının yanı sıra diğer güvenlik araçlarına da bağlantılar sunar.

**Yerel (local) çalıştırılabilir code quality araçları**

— **PHP CodeSniffer**

— **PHP Coding Standards Fixer (PHP CS Fixer)**

İkisi benzer araçlardır ama farklı yaklaşımları vardır. Ben CodeSniffer’ı tercih ederim çünkü hataları gösterir, isterseniz düzeltmenize izin verir veya düzeltmeyi önerir. CS Fixer ise hataları otomatik düzeltme odaklıdır.

Bu tamamen kişisel tercihtir, önemli olan ikisinin de kalite ve potansiyel güvenlik hatalarını bulmaya yardımcı olmasıdır.

**SQL injection tool önerisi**

— **SQLMap**

Piyasada birçok SQL injection aracı var ama çoğu temelde SQLMap’in üzerine kuruludur. Bu yüzden direkt kaynağı öğrenmek en iyisidir.

Böylece Laravel uygulamanız için genel güvenlik ve tarama araçlarına kısa bir bakış atmış olduk.

Laravel’e özel araçlara takılıp kalmamalısınız, ama elbette Laravel için özel geliştirilmiş araçlara bakmanın da avantajları var.

Bir sonraki videoda bunlardan bazılarına bakacağız ve ne kadar güçlü ve kullanışlı olabileceklerini göreceğiz.
