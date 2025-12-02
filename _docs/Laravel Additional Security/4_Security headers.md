# 🛡️ Güvenlik başlıkları

Bu videoda, bu kursla tam bir çemberi tamamlayarak istek yaşam döngüsünün **response** tarafını konuşacağız. Özellikle de uygulama cevaplarımızla birlikte göndermek isteyebileceğimiz **security headers** konusunu ele alacağız.

Korkulan mavi slayt. Güvenlik başlıklarından bahsettiğimizde kısa bir ara verme zamanının geldiğini biliyorsunuz. Bu başlıklar, en iyi web sunucunuzda ayarlanır — Apache, NGINX/IIS, Caddy, her ne kullanıyorsanız. Bunlar çok basit ve hafif yapılandırmalardır ve büyük olasılıkla tüm response’lar için standarttır. Laravel uygulamasında bu başlıkları üretip projeye fazladan yük bindirmenize gerek yok; bunları sunucu yapılandırmasına koyun, sunucu bu başlıkları kendisi yönetsin.

Bu iş için tasarlanmıştır ve daha verimlidir. Ama her zaman sunucu yapılandırmasına erişiminizin olmayacağını biliyorum; o yüzden bu kursta Laravel içinde nasıl yapılacağını da anlatıyorum.

Peki güvenlik başlıkları dediğimde neyi kastediyorum? Bunlar  **X-Frame-Options** , **Content-Security-Policy** gibi başlıklardır. Bu başlıklara derinlemesine dalmak genel web güvenliği konusudur, Laravel özelinde değildir. Bu yüzden her bir başlığın ne anlama geldiğine tek tek girmeyeceğim. Üstelik sürekli değişiyorlar; geçen baktığımda kullandığım bir header artık deprecated olmuştu ve tarayıcılar yenisini bekliyordu. Biz bu öğrenme yolunda onlara yetişmeye çalışmıyoruz, sadece Laravel içinde nasıl ele alacağımızı gösteriyoruz.

Bunu yapmak için başlıkları **middleware** içinde ayrı olarak göndereceğiz. index.php dosyasını bu işle kirletmek istemeyiz, ayrıca her controller’a eklemeyi hatırlamak da istemeyiz. Bunun yerine, istek yaşam döngüsüne bir şey eklediğimiz için middleware doğru yerdir.

Bu örnekte X-Frame-Options başlığını **deny** olarak ayarlamak istiyorum. Temelde, uygulamamın asla bir frame veya iframe içinde yüklenmemesi talimatını tarayıcılara iletmek istiyorum.

Bu yüzden security headers middleware adında bir middleware oluşturdum. handle metodunda next closure’ı kullanarak request’i işler ve bir response alırım. Bunun bir response tabanlı middleware olduğundan emin olmalıyız; yani request işlendikten sonra çalışır. Response’u aldıktan sonra header metodunu çağırıp ayarlamak istediğimiz başlığı ve değerini veririz. Sonra response’u diğer middleware’lere ve Laravel’e geri döndürürüz.

Oldukça basit, ama yine de bunu sunucu yapılandırmasına eklemek kadar kolay değil.

Tamam, devam edelim. Şimdi kernel.php dosyamı açacağım. protected middleware özelliği, tüm istekler için kaydedilmiş middleware’leri içerir. security headers middleware’i buradaki stack’in en altına ekledim ve gördüğünüz gibi artık işimiz bitti. Her istek bu başlığı alacak.

Laravel uygulamasının varsayılan kurulumunu kullanıyorum ve welcome view açık. Chrome’da inspector’ı açıp index sayfasına bir istekte bulundum. Response headers kısmında vurgulanmış satırı görebilirsiniz. X-Frame-Options header’ını deny değeriyle gönderiyor.

Ve böylece istek yapılandırmaları, CSRF ve XSS korumalarıyla ilgili bu kursu bitirmiş oluyoruz.

Unutmayın:

* Doğru istek metotlarını kullanın.
* CSRF token’larını kullanın.
* Verinizi escape edin.
* Güvenlik başlıklarını web sunucusu yapılandırmasında ayarlayın; ama yapamıyorsanız global middleware kullanın.
