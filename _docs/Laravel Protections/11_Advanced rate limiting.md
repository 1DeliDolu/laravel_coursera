# 🚦 Gelişmiş hız sınırlaması

Temel rate limiting’i konuştuk, ancak bu videoda segmented ve conditional rate limiting konularına biraz daha derinlemesine gireceğiz ve bir demo içinde örnek kodlarla göstereceğiz. Bu kod örneklerinin tamamı RouteServiceProvider içinde ve önceki videoda import edilen RateLimiter sınıfını kullanıyor.

Bu örnekte rastgele gelen ziyaretçileri dakikada 60 istekle sınırlamak istiyorum, ancak authenticated kullanıcıların herhangi bir limiti olmamalı. Bunun için bir ternary oluşturdum: İlk olarak, request içinde authenticated user olup olmadığını kontrol ediyor. Eğer varsa, no limit döndürüyoruz. Eğer authenticated user yoksa — yani user metodu null dönerse — dakikada 60 isteklik bir limit konfigürasyonu döndürüyoruz. Request’e erişebildiğimiz için burada olduğu gibi gelen veriye göre istediğimiz kararları verebiliriz.

Limit sınıfı, bir rate limit olmadığını belirtmemize izin verdiği için bu mantığı tamamen closure içine koymak oldukça kolaydır. Bu örnekte rate limit’i IP bazında segmentlemek istiyoruz. Önceki genel rate limiting bir endpoint’e doğrudan uygulanıyordu. Şimdi aynı endpoint’e segmentlerle limit uygulayacağız. Her IP adresine özel ayrı bir limit uygulamak istiyoruz. Bu yüzden “dakikada 60 istek / IP adresi” şeklinde bir konfigürasyon döndürüyoruz. Buy metodunu kullanmazsak, herhangi bir IP’den gelen 61. istek limitlenirdi; ancak şimdi aynı IP’den gelen 61. istek limitlenecek.

Hem conditional hem de segmented rate limit’leri birleştirebiliriz. Bir sonraki örnekte voting sistemim için tek bir rate limiter tanımlamak istiyorum. Sistemimdeki GET isteklerine daha yüksek bir limit koymak istiyorum. Sistemim çok güçlü değil, bu yüzden bu alanı saran GET isteklerini dakikada 1.000 istekle sınırlandırıyorum — bu fazlasıyla yeterli. Daha fazlasına çıkarsam sistem zorlanabilir.

Eğer gelen istek bir POST ise, yani biri oy gönderiyorsa, bunu günde 13 gönderime indiriyorum. Belki her 2 saatte bir oy kullanabilsinler ve bunu request üzerindeki fingerprint metoduyla segmente ediyorum. Fingerprint, Laravel’in yarı benzersiz ziyaretçi tanımlama yöntemidir. Request verilerini, route verilerini ve IP adresini birleştirir. Böylece aynı kişi iki saatte bir oy kullanabilir ve fazladan bir oy hakkı daha olabilir.

Peki rate limiting pratikte nasıl çalışıyor? Bir demo’ya bakalım.

Önce RouteServiceProvider’a bakalım. configureRateLimiting fonksiyonu içinde coupon request limiter adlı bir limit tanımladım. İçinde, her IP için saatte bir kez istek yapılmasına izin veriyorum.

Bunun nerede uygulandığına bakalım. İlk route’a hiçbir şey uygulamadım; bunu rate limit etmeme gerek yok. Herkes kupon formunu görebilir. İkinci route ise bir POST isteği ve burada throttle middleware ile coupon request limiter’ı kullandım.

Tarayıcıya geçelim.

Rate limit uygulanmamış formu açtım. E-mail adresimi girdim ve kupon istedim — başarılı. Şimdi birkaç saniye sonra yeniden kupon istemek istiyorum. Formu tekrar gönderiyorum ve *boom* — standart 429 hata ekranını gördüm. Çünkü aynı IP’den bir saat içinde ikinci isteği gönderdim ve limit aşılmış oldu.

Laravel rate limiting ile ilgili son bir not: Breeze veya Jetstream gibi authentication kitleri kullanıyorsanız, bu kitler zaten güvenli bir şekilde yapılandırılmış rate limiting sistemleriyle gelir.

Rate limiting kursu bu kadar. Unutmayın: Eğer uygulama verisine ihtiyaç duymayan bir rate limiter gerekiyorsa, bunu uygulamanın dış katmanlarında (load balancer gibi) yapmak her zaman daha iyidir. Aksi hâlde Laravel’de genel, condition-based veya segmentlere ayrılmış rate limiter’lar tanımlayabilirsiniz ve tek bir definition içinde birden fazla limit davranışı oluşturabilirsiniz.
