# 🔐 Hashing

Laravel’de parola hashing ve şifreleme hakkında konuşalım. Bu videoda hashing konusuna değineceğiz. Bu bağlamda hash fonksiyonları, kriptografik hash üreten yöntemlerdir. Bunlar **tek yönlüdür**. İki yönlü kriptografiden bir sonraki videoda bahsedeceğiz.

Laravel iki hash algoritması sağlar: **bcrypt** ve **Argon2**.
Argon2 için iki seçenek sunar: standart Argon seçeneği olan **argon2i** ve **argon2id**.

`config` klasöründe uygulamanızın hashing algoritmasını `hashing.php` dosyasında ayarlayabilirsiniz. Buraya `bcrypt`, `argon`, veya `argon2id` yazabilirsiniz.

Aynı dosyanın alt kısımlarında bcrypt kullanıyorsanız *rounds* gibi seçenekleri ayarlayabilirsiniz.
Argon hashing seçtiyseniz *memory*, *threads* ve *time* gibi parametreleri belirleyebilirsiniz.

Bu ayarlar daha sonra Hash façade kullanıldığında uygulanır. Hash façade iki önemli metoda sahiptir:

**1. make**
Make metodu, düz metin (plain text) bir ifadeden hash oluşturur.
Örneğin bir kullanıcının parolasını güncellerken yeni seçilen parolayı make metoduna gönderirsiniz.

Parolalardan bahsedilirken hashing konusunun sadece parola ile sınırlıymış gibi anlatılmasından rahatsızım. Başka birçok kullanım alanı vardır. Benim örneklerimde bu yüzden “workstationID” kavramını da kullanacağım.

Bir banka sitesine eriştiğinizde “Bu bilgisayarı tanımak ister misiniz?” der ya, işte onlar tarayıcıdaki bağlantınıza dair bazı fingerprint verilerini alır ve bunları bir hash içinde saklar. Bu verileri make metoduna böyle gönderebilirler.

**2. check**
Check metodu, düz metin bir string’i mevcut bir hash ile karşılaştırır.

Bu örnekte, gelen post edilmiş parolayı kullanıcı kaydında saklanan parola hash’i ile karşılaştırabilirsiniz. Aynı mekanizma tanınan workstation için de kullanılabilir.

Gelen isteğin fingerprint’i alınır, hash’lenir ve veritabanındaki hash ile karşılaştırılarak bu workstation’ın tanınıp tanınmadığı anlaşılır.

Parola hashing konusunu zaten ele aldık, ancak sonraki videoda parolalarla ilgili bazı özel endişelerden biraz daha bahsedeceğiz.
