# 🔍 Sorgu güvenliği ve performansı

Bu derste, Laravel’in veritabanı araçlarını ve Laravel ile veritabanına güvenli biçimde erişmek için hangi adımların atılması gerektiğini konuşacağız. Doğrudan veritabanı erişimine ve ayrıca Laravel’in Eloquent adı verilen nesne-iliskisel eşleyici (ORM) aracına değineceğiz. Bu videoda, Laravel uygulamanızda veritabanına erişirken güvenlik ve performans etkilerine odaklanacağız.

İyi haber şu: Eğer geçmişte PHP ile veya herhangi bir web programlama diliyle çalıştıysanız, SQL injection saldırılarının her zaman ilk akla gelen tehdit olduğunu biliyorsunuzdur. Veriyi düzgün biçimde sanitize etmek, doğrulamak ve filtrelemek her zaman bir endişe kaynağıydı. Elbette hâlâ öyle, buna sonra değineceğim.

Ama iyi haber şu ki, prepared statements kullanarak SQL injection’ı engellemenin yollarından biri Laravel’de varsayılan çalışma yöntemidir. Hem database façade hem de Eloquent varsayılan olarak prepared statements kullanır. Yani elbette verileri veritabanına erişmeden önce zaten filtreleyip doğruluyorsunuz, fakat bu da derinlemesine savunmanın başka bir katmanı olarak sizin için yerleşik biçimde gelir. Bu durum kodunuzu da basitleştirir. Prepared statements **kullanmamak** için ekstra çaba göstermeniz gerekir.

Bundan söz etmişken, gerçekten isterseniz database façade üzerinde unprepared yöntemini kullanarak hazırlanmış olmayan bir sorguyu çalıştırabilirsiniz. Ama bunu yapmamaya çalışın. Muhtemelen buna ihtiyacınız olmayacaktır.

Bazen raw query kullanmanız gerekebilir. Örneğin, bazı dinamik gelen verileriniz olabilir. Belki frontend kullanıcıların kolon seçmesine izin veriyordur ve tamam, bunu söylerken bunun zaten uydurma bir örnek olduğunu belirtmeliyim, çünkü bunu yapmanın daha doğru yolu yalnızca izin verilen kolon sayısını sınırlamak olurdu. Laravel ile ilgili güzel olan şey şu: Güvenli bir çerçeve üzerine kuruludur, çünkü vereceğim çoğu örnek aslında uydurma.

Kötüye kullanılabilecek kod yazmak aslında pek kolay değildir. Neyse, raw statements kullanmanız gerekiyorsa DB façade’ın raw metodunu kullanabilirsiniz, fakat mutlaka doğrulama yapmalısınız. Ayrıca selectRaw, whereRaw vb. yöntemleri kullanarak raw ifadeleri mümkün olduğunca dar kapsamda tutabilirsiniz. Bu örnekte, daha önce bahsetmeye başladığım gibi gelen verilere kolon 2 ile 4 arasında sıralama yapma izni veriyorum. Teknik olarak orderByRaw burada bile gerekli değil. Raw statements büyük ihtimalle sadece rapor oluşturma gibi özel durumlarda kullanılır. Ama sanırım bu uydurma örnek, bunları nasıl kullanabileceğinizi az çok gösteriyor — elbette doğrulamayla birlikte.

Birçok geliştiriciden “SQL injection’ın kötü olduğunu biliyorum ama neden olduğunu bilmiyorum” veya “hiç gerçekleştiğini görmedim” gibi yorumlar duydum. Bu tehdit teorik geliyorsa, ona fazla önem vermiyoruz; bu iyi değil. O yüzden başka bir uydurma örnek oluşturdum. Bu kötü koddur, **bunu yazmayın**. Ama ne olabileceğini göstermek istedim. Bu örnekte e-posta ve şifre ile giriş yapacağız. Bir şekilde bu bilgilerle raw query yapacağız ve eğer giriş başarılıysa dashboard’a yönlendireceğiz.

Bu kodun sorunu, gelen kullanıcı verisini — belki doğrulanmış olabilir ama ne kadar güvenli doğrulandığını bilmiyoruz — ham olarak kullanmasıdır. Diyelim ki yalnızca alanların zorunlu olduğu doğrulanmış olsun, başka hiçbir şey yok. Şimdi bununla oluşturulmuş bir sistemin demosuna bakalım. Bu giriş formunda kendi hesabımın e-posta adresi ve bilinen parolamla giriş yapıyorum. Bu çalışıyor ve sağ üst köşede adımı görebiliyorum.

Ve kullanıcı numaramın 12 olduğunu keşfetmiş bulunuyorum. Şimdi çıkış yapıyorum. Şimdi sistemde kullanıcı numarası 1 olarak oturum açmak istiyorum, çünkü birçok kişi ilk kullanıcıyı admin olarak oluşturur. Sorgunun arka planda e-posta ve şifre kontrol ettiğini tahmin etmek hiç zor değil. Bazı saldırganlar doğru sorgu yapısını bulmadan önce birçok kombinasyon denemek zorunda kalabilir ama ben bunu biliyorum.

Bu yüzden var olmayan bir e-posta adresi giriyorum ve ardından tek tırnak ekleyerek e-posta sorgusunu kapatıyorum ve “or 1 = 1” yazıyorum, ardından yorum işareti ekliyorum. Sonra herhangi bir şifre girip giriş yapabiliyorum.

İşte böyle, artık admin’im.

Durumu netleştirmek için veritabanı sorgu kaydını açtım. İlk giriş normal girişti. İkinci girişte, admin olarak giriş yaptığımda, girdinin doğrudan sorguya nasıl dahil edildiğini görebiliyoruz. “select ID from users where email is guy smiley or column one is one” — burada column one ID oluyor — ve sonra kodun geri kalanını çalıştırmamak için yorum satırı kullanılıyor. İşte böyle çalışıyor ve bu yüzden yerleşik prepared statements kullanmak önemli.

Prepared statements, gönderdiğim girdinin tamamını bir bütün olarak değerlendirir, bu örnekteki gibi birbirine ekleyerek değil. Prepared statements ile ilgili daha çok şey konuşulabilir ama bu dersin kapsamı dışında.

Wildcard içeren like sorgularından da bahsetmeliyim. Bazı kişilerin raw select query ve yüzde işareti kullanmaya çalıştığını gördüm, fakat bunda özel bir şey yok. Yüzde işaretini değerin parçası olarak koyabilirsiniz. Wildcard karakterleri prepared bound parametrelerinin içinde olması tamamen normaldir ve bu şekilde çalışır.

Uygulamamız için kimsenin bir denial of service oluşturamamasını sağlamalıyız. Bu yüzden veri sınırlandırması önemlidir. Bu sadece iyi kullanıcı deneyimi için değil; doğru kullanılmazsa sitenizin yavaşlamasına veya yanıt vermemesine neden olabilir. Database veya Eloquent sorgularınızda paginate veya doğrudan limit metodunu kullanabilirsiniz.

Bu durumun neden önemli olduğuna bakalım. Bu basit users controller’a bakalım. Kullanıcı listesini alıp bir görünüme gönderiyor. Request parametrelerinden bir limit alıyor ve bunu pagination metoduna geçiriyor. Varsayılan olarak 10. Apache bench testi çalıştırıyorum. 100 isteği göndermek yaklaşık 4.6 saniye sürdü ve ortalama istek süresi 46 ms, fena değil.

Peki limit olarak 10.000 gönderirsem ne olur? Kod 10.000 kullanıcıyı alıp görünüme gönderecek. Hiç doğrulama veya sınırlama yapmadığım için hâlâ oldukça uzun sürüyor. Videoyu hızlandırıyorum. 138 saniye sürdü ve ortalama istek süresi 1300 ms’ye çıktı. Binlerce istek yüksek limitlerle gönderilseydi site büyük ihtimalle çökerdi.

Son olarak, bilerek çok fazla veriye erişiyorsanız, veriyi database cursor ile almak isteyebilirsiniz; bu, verinin lazy olarak alınmasını sağlar. Ya da chunking yapmak isteyebilirsiniz. Bu, Eloquent veya database façade’a verilerin yalnızca belirli bir bölümünü almasını ve callback’i uyguladıktan sonra daha fazlasını getirmesini söyler. Bu, hafıza kullanımını düşürür.

Standart sorguları chunk edebilirsiniz veya tüm verileri ID’ye göre sıralayıp alıyorsanız chunkById kullanabilirsiniz ki bu en verimli yöntemdir.

Laravel veritabanı güvenliğine oldukça önem verdiği için daha güvenli uygulamalar geliştirmek programcı için kolaylaşmaktadır. Sadece şunu unutmayın: Raw veya unprepared sorguları, **gerçekten zorunda olmadıkça** kullanmayın. Gerekirse önce meslektaşlarınıza veya Stack Overflow’a sorun. Laravel’in varsayılan prepared statement mekanizması muhtemelen en iyi seçeneğinizdir.
