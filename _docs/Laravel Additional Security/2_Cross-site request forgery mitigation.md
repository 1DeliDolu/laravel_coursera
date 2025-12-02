# 🛡️ Cross-site request forgery azaltma

CSRF, yani Cross-Site Request Forgery hakkında konuşalım. Önce, CSRF’nin tehlikeleri nelerdir? Neden bunu konuşuyoruz? Eminim formlarımız authentication ve authorization arkasındadır, öyleyse ihtiyacımız olan tek şey bu değil mi? İsim, istediğimden biraz daha süslü. Gerçekte konu, bir form isteğinin başka bir siteden sizin sitenize gönderilmesidir; yani cross-site, başka bir yerden gelen, sizin web uygulamanız gibi davranarak gönderilen bir submission. Sanırım forgery kısmı da buradan geliyor. Yani kısaca, siteniz kendi içinden gelen submission’ları bekler, başka bir yerin bunu sizin sitenizmiş gibi yapmasını değil.

Form alanlarınız ve kullanıcı deneyiminiz başka birinin kontrolüne girebilir.

Tehlikeli olan, bir CSRF isteğinin form submission’ını herhangi bir kullanıcı müdahalesi olmadan ve işlemden hiçbir geri bildirim olmadan gerçekleştirebilmesidir. Güzel bir sitede gezindiğinizi düşünün, örneğin [inaudible].com, ve arka planda, her linke tıkladığınızda ya da bir sayfayı yüklediğinizde benim sizin Gmail hesabınıza bir form gönderdiğimi hayal edin; bir sayfa dolusu mesaj bırakıyorum. Bu işe yarar çünkü tarayıcınız sizin giriş yaptığınızı bilir ve çerezleri Gmail domain’i olduğu için gönderir. Öyleyse neden her şeyi gönderemesin? İşte CSRF bu yüzden büyük bir mesele. Tarayıcınızı, isteğiniz olmadan sitenizde form submit etmeye kandırabiliriz.

Bu hiç iyi değil.

Peki bundan nasıl korunuruz? İlk savunma hattı, önceki videoda konuştuğumuz gibi doğru istek metotlarını kullanmaktır. Bu tek başına tüm CSRF saldırılarını engellemez ama gerçekleştirmeyi biraz daha zorlaştırır. Kullanıcı için oturumda benzersiz bir token oluştururuz ve sonra kullanıcının bu token’ı her istekle birlikte göndermesini isteriz. Bu, onlara sitemizde verdiğimiz bir şey olduğu için geri almayı bekleriz. Böylece kullanıcının sitemizi ziyaret ettiğini veya hâlâ sitemizde olduğunu doğrulamış oluruz.

Kısa bir not: Bunların hiçbiri kusursuz değildir. Hâlâ çok karmaşık yöntemlerle CSRF verisini okumaya çalışıp başka bir siteden gönderme girişimleri olabilir; fakat bunlar çok sınırlı ve cores ile diğer güvenlik yapılandırmaları sayesinde son derece zordur. Yine de olabilir diyelim. Eğer CSRF token eksikse veya oturum süresi dolduysa ve token eskiyse, form gönderildiğinde 419 HTTP hatası veririz. Temelde “istek süresi doldu, tekrar denemelisin” diyoruz. İşte form isteği, benzersiz token ve her istekte bu token’ı zorunlu kılma kombinasyonu bizi CSRF’e karşı korur. Oldukça basit ama dahiyane bir çözüm.

Laravel bu işlevselliği, CSRF token mekanizması ve biraz middleware kullanarak yerleşik olarak sunar. Bir token üretir ve bunu oturumda saklar. Oturum geçerli olduğu sürece token da geçerlidir. Bir blade template oluşturuyorsanız, CSRF blade directive’ini kullanabilirsiniz ve Laravel gerisini halleder. İlk örnek, blade template’ime yazmam gereken şeyi gösteriyor. Fark ederseniz ikinci satır, bu işlevselliği almak için gereken tek şeydir. Oldukça basit.

Aşağıdaki ikinci blok ise blade’in gerçekte ne render ettiğini gösteriyor. Temelde, belirli bir adı olan ve oturumumuzda saklanan rastgele bir string içeren gizli bir input’tur. Karşılaştırma da bununla yapılır. Yapmanız gereken tek şey bu.

Eğer Laravel içinde Javascript ile çalışıyor ve react, view veya varsayılan kurulum gibi bir scaffolding kullanıyorsanız, Axios HTTP kütüphanesi sizin için hazır gelir. Varsayılan olarak, JavaScript isteklerinizde doğru CSRF token’ını gönderecek şekilde yapılandırılmıştır; hiçbir şey yapmanız gerekmez. Eğer Laravel uygulamanızda başka bir JavaScript karışımı kullanıyorsanız, önerilen yol sağda gösterilendir. Önce CSRF token’ı içeren bir meta tag oluşturabilirsiniz. Temelde csrf_token metodu sadece rastgele string’i üretir ve siz bunu HTML’nize enjekte edersiniz.

Sonrasında, jQuery ile yapılmış bir örnek görebilirsiniz. Burada tüm Ajax isteklerinin x-CSRF-token adında bir header göndermesi için bir yapılandırma yapılmıştır ve bu değeri jQuery ile oluşturduğumuz meta tag’den alırız. Böylece blade içinde JavaScript yazmamıza gerek kalmaz; ayrı JavaScript dosyalarımızda kalabilir. Veri, meta tag’den veya blade’in HTML çıktısından alınır.

Peki bu işlevsellik gerçekte nasıl görünür ve rastgele oturum zaman aşımı durumlarının kullanıcı deneyimine zarar vermesini nasıl önleyebiliriz? Bir göz atalım. Daha önce gördüğünüz ve CSRF token directive’i içeren bir form yükleyelim. İki saatten uzun süre bekleyeceğim, sonra geri dönüp SKU’yu yazıp gönderiyorum.

Bir 419 hata mesajı aldığınızı görüyorsunuz. Buna izin verilmiyor. Geri tıklayıp yenileyeyim. Şimdi iki saatlik süre içinde submit ettiğimde başarılı olacaktır. Bu yerleşik işlevsellik.

Ama insanların bu deneyimi yaşamasını istemiyorsak ne olur? Oturum sürelerinin dolduğunu bildirmek ve sayfanın yenilenmek üzere olduğunu göstermek için bir zamanlayıcı kurabilirsiniz. Burada CSRF directive’i olan bir sayfa var ve bir JS zamanlayıcısı ekledim. Oturum süresi bittiğinde bir pop-up oluşturuyor. Ürün ekibimizle konuşup daha dostça bir mesaj gösterebiliriz ama her neyse, pop-up çıkıyor ve “Tamam”a basıyoruz, sayfa yenileniyor.

Artık yeni bir CSRF token’ımız var. Pencere içinde sayfayı submit ettiğimizde her şey beklendiği gibi çalışır.

CSRF token’ının süresinin dolmasını yönetmek için kullanabileceğimiz birçok JavaScript yöntemi vardır. Sağ tarafta, uyarıyı döndüren demo için kullandığım kodu görebilirsiniz. Basitçe iki saat sonra çalışacak bir timeout’tur. Uygulamayı normal kullanımda farklı sayfalar arasında gezindiğiniz için zamanlayıcı genelde hiç dolmaz. Ancak yeterince uzun süre beklerse uyarı mesajını alırsınız ve sayfa yenilenir; o andan itibaren yeni bir token üretilir.

Bu pek iyi bir kullanıcı deneyimi değildir. Bazen insanlar ekranda bilgiyi sonsuza kadar tutmak ister. Bu durumda bir doğrulama mesajı oluşturabilirsiniz ya da bu tür kodu sadece form içeren sayfalarda çalıştırabilirsiniz. Sayfayı formla birlikte yeniden yükleyecekseniz, kullanıcı verisini kaybetmemek için localStorage’a form verisini yazıp geri getirerek sayfayı yenilemek isteyebilirsiniz. Ya da arkada sessizce çalışan bir Ajax ping ile oturumu canlı tutabilirsiniz.

Tüm bunları product owner ile konuşup CSRF’yi uygularken kullanıcıya iyi bir deneyim sunmanın yolunu belirlemelisiniz.

Özetle, uygulamanızı güvenli tutmak için atmanız gereken birçok adımdan biri olarak CSRF token’ını kullanarak cross-site request forgery saldırılarına karşı kendinizi koruyun.
