# 🛠️ Laravel’e özgü tarama araçları

Laravel’e özgü tarama araçlarının, standart web uygulaması güvenlik araçlarının ötesinde bazı benzersiz avantajları vardır ve bu videoda birkaçını inceleyeceğiz.

İlk araç  **Larastan** . Larastan bir PHPStan extension’ıdır; yani PHPStan işlevselliğini kullanır fakat Laravel’e özel bir eklenti ile genişletir. Bu neden faydalıdır? Çünkü static analysis araçları sadece analiz edebildikleri şeyleri anlayabilirler ve bunlar genellikle açık, takip edilebilir fonksiyonlardır. Laravel’de özellikle PHP’nin magic method yapılarına dayanan işlevler vardır ve bunlar kolay analiz edilemez. PHPStan’in sade bir konfigürasyonu, bazı standart Laravel kodlarının çalışamayacağını düşünür; çünkü bu kodlar çalışma anında oluşturulan dinamik yapılara dayanır.

Static analysis kodu çalıştırmaz, sadece analiz eder.

Larastan ise daha fazla bağlam ekler. Örneğin bir Eloquent model üzerinde, ilişki metoduyla aynı isimde bir public property’e başvuruyorsanız, bu property çalışırken ilgili ilişkiyle doldurulmuş bir collection döndürür. Genel bir analiz aracı bunu anlayamaz çünkü bu davranış PHP’de magic method olan __get üzerinden oluşturulur.

Larastan burada devreye girer: Analiz aracına şu mantığı öğretir — “Bu sınıfta var olmayan bir property bulunduysa, aynı isimde bir method var mı? Eğer varsa bu bir ilişki örneği döndürür ve bu geçerli Laravel kodudur. Yoksa hata raporla.”

Oldukça havalı.

Bir diğer araç  **Laravel Enlightn** . Bu community ve open-source bir projedir; ayrıca bir pro sürümü de vardır. Yani temel özellikleri ücretsiz kullanabilir, ardından ihtiyaç duyarsanız pro sürüme geçebilirsiniz.

Enlightn, Laravel kodunu ve Laravel best practice’lerini anlamak için özel olarak geliştirilmiştir. Sadece klasik bir static analysis tool gibi davranmaz; daha fazlasını yapar.

SQL injection tespiti, XSS kontrolü gibi işlevleri olsa da, Laravel’e özgü şeyleri de anlar:

* N+1 Eloquent sorguları
* Yanlış yapılandırılmış config dosyaları
* Varsayılan ve eksik güvenlik ayarları
* Ölü/işe yaramayan route’lar
* Directory traversal sorunları
* Ve daha fazlası

Kesinlikle incelemeniz gereken bir araç.

Bu videonun ve bu kursun sonunda bir Enlightn demosu göstermek istiyorum. Kurulumu yapacağım, hatalar içeren örnek bir uygulamada çalıştıracağım ve çıkan sorunları çözme sürecini göstereceğim.

Bu demo için, sıfırdan kurulmuş plain bir Laravel projesi ile başlıyorum. Hiçbir ayarı değiştirmedim. Sadece local MySQL erişimi ve local PHP kurulumu yapılmış durumda, environment da development.

İlk adım olarak Enlightn paketini kuruyorum. Bu community sürümü. Eğer tam sürümü satın aldıysanız, dokümantasyondaki yönergelerle kurabilirsiniz.

Kurulumdan sonra artisan ile Enlightn etiketli asset’leri publish ediyorum. Bu aslında sadece bir config dosyasıdır.

Şimdi bu config dosyasına bakalım.

En üstte, dahil etmek istediğimiz analyzer’lar var — yıldız yani “hepsi”.

Ardından iki exclude seçeneği geliyor: Biri genel, diğeri continuous integration (CI) için. Bunları birazdan kullanacağız.

Sonra paths geliyor; analyzer kodlarının bulunduğu yerleri tanımlar.

base_path tarayıcıya analiz edilecek dosyaların nerede olduğunu söyler.

skip_env, environment’a özel ayarları (debug, error display gibi) önemsememesi içindir.

guest_url, oturum gerektirmeyen bir sayfayı işaret eder; header kontrolü için kullanılır.

don’t_report ve ignore_errors bölümleri, belirli hataları raporlamamak veya yok saymak için kullanılır.

license list, projeye dahil olmuş paketlerin lisanslarını tarar.

commercial_packages, lisans taramasında yok sayılacak paketleri belirtir.

Son olarak dosya/dizin izinleriyle ilgili best practice ayarları bulunur.

Yani Enlightn hem kendi ayarlarıyla yapılandırılabilir hem de genişletilebilir. Gerçekten güzel.

Şimdi Enlightn’i ilk kez çalıştırıyorum. Hızlı olduğunu görebilirsiniz. Kod büyüdükçe biraz yavaşlar ama aşırı değil.

Sonuçları inceleyelim:

Altta error yok — güzel.

Bazı non-applicable sonuçlar var — bunlar dev ortamında geçersiz olan şeyler.

Bazı failed sonuçlar var ama çoğu passed.

Failed sonuçlara bakalım:

— MySQL TCP kullanıyor, sorun değil.

— queue driver sync, gelişene kadar bu da sorun değil.

— trust proxies middleware’i kullanılmıyor — bunu düzeltebilirim.

— cache prefix çakışabilir denmiş — pek olası değil, ama bakacağım.

— custom error pages yok — normal, Laravel defaults.

— PHP config local — demo için normal.

— unstable dependencies — bakmam gerekir ama kritik değil.

— backend dependency vulnerability — demo için yok sayıyorum.

— No XSS protection headers — server tarafına koyacağım, uygulamaya değil.

Şimdi bu hatalardan bazılarını çözelim.

Kernel’deki trust proxies middleware’ini kaldırıyorum.

Bu tür önerilerin her zaman size uygun olduğunu varsaymayın — araştırın.

Şimdi cache config’e bakıyorum. prefix kısmını görüyorum, app name üzerinden oluşturuluyor.

App name’i değiştirmediğim için .env’den güncelliyorum.

Sonrasında unstable dependency analyzer’ını exclude ediyorum.

Tekrar Enlightn çalıştırıyorum — 63 kontrol var, yani çıkarma başarılı. Middleware hatası ve cache uyarısı gitti.

Enlightn ayrıca CI mode destekler.

CI mode exit kodu döndürür, CI/CD pipeline bunu kullanır.

Hata varsa exit code non-zero olur ve pipeline durur.

running... exit code = 1.

Ama bazı hataların CI’yi durdurmasını istemeyebilirsiniz. Bunlar bilinen, kabul edilmiş hatalardır. Analyzer silmek istemiyorsanız, baseline tanımlayabilirsiniz.

enlightn baseline komutu bunları don’t_report içine ekler.

Sonra tekrar CI modunda çalıştırırsanız, hatalar aynı olduğu sürece exit code = 0 olur.

Son olarak, Enlightn’in yeni bir hatayı gerçekten yakaladığını göstermek için kasıtlı bir hata oluşturuyorum.

Car model yapıyorum, bir controller ekliyorum.

Controller’da store metodunda mass assignment korumasını bypass edip forceCreate ve request->all kullanıyorum — kesinlikle yapmayın.

Enlightn’i tekrar çalıştırıyorum, ve check 58’de hatayı tespit ettiğini görüyorum. Harika.

Bu demo ve bu kurs burada sona eriyor.

Daha detaylı öğrenmek için diğer eğitim yollarına bakın.

Her tür web uygulaması tarayıcısını kullanın — sadece Laravel’e özel olanları değil.

Ama Laravel’e özel araçların da size büyük avantaj sağlayabileceğini unutmayın.

Güvenli programlayın ve ardından tarayıcılarla kontrol edin.
