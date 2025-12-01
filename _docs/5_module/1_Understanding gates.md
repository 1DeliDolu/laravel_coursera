

# 🧩 Gate’leri Anlamak

Artık kimlik doğrulamayı tamamladığımıza göre yetkilendirme hakkında konuşma zamanı. Bu ilk videoda, Laravel’in Gate sistemini ele alacağız. Bunlar daha üst seviye yetkilendirme mekanizmalarıdır. Aslında başlamadan önce seni bu ders için hazırlamak istiyorum. Çok fazla kod odaklı videonun olacağı bir bölüm geliyor. Eğer bir framework olmadan bir şey programladıysan ve herhangi bir yetkilendirme ya da izin kontrolü yaptıysan, bunun milyon farklı şekilde yapılabileceğini biliyorsundur. Laravel de bu esnekliği koruyor, yani yetkilendirmeyi uygulamanın birçok farklı yolu var.

Ama sonunda sonuç oldukça basit: Bir şey bir şeyi yapabilir mi, true ya da false. Bundan çok gösterilecek bir şey yok. Ama sevmediğim şey, yetkilendirme gibi bir kod bloğu yazıldığında altına “geri kalan kodunu buraya yaz” gibi bir yorum eklemek oluyor. Özellikle de yetkilendirme gibi önemli bir konuyu yeni öğrenirken bunu tanıdık kodun arasında görmek çok önemlidir. Bu yüzden örneklerim tam akışları gösterecek.

Devam edelim. Authentication bir isimdir. Kimden bahsediyoruz? Kimi tanıyoruz? Bu isim nasıl tanımlanır? Authorization ise bir “yapabilir mi” fiilidir ya da bir yetenektir. Hangi şeyi yapmaya çalışıyoruz ve bunu tanımlamak için hangi fiili kullanıyoruz? Kim, authentication; ne yapabilir, authorization. Kim bu şeyi yapma iznine sahip?

Bu farkı bilmek önemlidir. Burada gerçekten odaklandığım konu da bu. Çünkü yaygın bir hata şudur: Sadece authenticated bir kullanıcıyı kontrol ederek bir şey yapıp yapamayacağını belirlemek. Bunu birçok uygulamada gördüm ve ben de zamanında yaptım. “Kullanıcı bu listeyi görebilir mi?” Eh, giriş yaptıysa elbette görebilir. Ama sorman gereken soru bu değil.

Bu, gereğinden fazla sorumluluğu kimlik doğrulama sistemine yükler. Authentication’ın tek yapması gereken, bir eylemden kimin sorumlu olduğunu söylemektir. Authorization ise kişinin neyi yapmaya çalıştığıyla ilgili izin sistemidir.

Daha pratik bir örneğe çevirelim: Bir emlak sitemizin üyeleri için satıştaki evlerin bir listesi olsun. Giriş yaptıklarında herkes bu evlerin listesini görebilir, ama üye olmaları gerekir. Giriş yaptıysa ayrıca detay butonuna basıp evin detaylarını da görebilir. Tüm bunlarda sadece authenticated kullanıcıyı kontrol ediyorsun.

Sonra iş modeli değişiyor. Artık bir evin detayını görebilmek için pro aboneliğin olması gerekiyor. Normal giriş yapan kullanıcılar tüm evleri görebiliyor ama detay görmek için abonelik şart. Bu durumda, tüm detay butonlarının kodunu bulup abonelik kontrolü eklemen gerekiyor. Ayrıca route’lar ve controller içinde de “authenticated user has subscription” kontrolü yazman gerekiyor.

Birkaç gün sonra gereksinimler yeniden değişiyor ve biraz esniyor. Artık herhangi bir ziyaretçi ev listesini görebiliyor ve authenticated kullanıcılar detayları görebiliyor ama pro abonelik gerekmeden. Şimdi tüm kodu tekrar bulup önceki değişiklikleri geri alman gerekiyor.

İşte authenticated kullanıcıyı kontrol ettiğinde olan budur. Başta kolay görünür ama zamanla baş ağrısına dönüşür. Ayrıca güvenlik açısından da yeterince hassas değildir. Güvenli programlama yaparken daha hassas olmalıyız. Şimdi bunu böyle düşünalım: Bunun yerine iki permission tanımladığımızı varsayalım — “list houses” ve “see houses details”. Bunlar uygulamanın her yerinde kontrol edilir. Laravel Gates ile bu izinleri tanımlarsın.

Senaryolar değiştiğinde kod içinde hiçbir yeri aramana gerek kalmaz. Sadece izin sistemindeki tek bir yeri açıp gereksinimi değiştirirsin: auth’tan non-auth’a, subscription’dan non-subscription’a geçersin. Hepsi biter. Yani sonuç şu: Bir şey için permission kontrolü yapıyorsan authenticated kullanıcı kontrolü yapma. Başta büyük gibi görünse de authorization yapısını kullanmak daha kolay, daha sağlam ve daha güvenlidir.

Şimdi konuya geri dönelim. Laravel’de ilk yetkilendirme aracı olan Gates’ten bahsediyoruz. Bunlar üst seviye kontrollerdir, oldukça basittirler. Bir şeyi tanımlar ve bir şeyi kontrol ederler. Neredeyse basit bir if cümlesi gibidirler. Bağlam alabilirler elbette, ancak yaptıkları en hızlı kontroldür. Tümü auth service provider içinde closure olarak tanımlanır. Sanki onları küçümsüyormuşum gibi görünebilir ama öyle değil. Sadece çok basitler ve daha karmaşık durumlara uygun daha güçlü araçlar da var — Policies gibi. Onlardan sonraki videoda bahsedeceğim. Ama yine de Gate bilmek gerekir, çünkü bazı basit durumlarda ihtiyacın olan tek şey bu olabilir. Hatta bir önceki örnekteki ev senaryosunda sadece Gate kullanmak yeterli olabilir.

Burada bir tane köpeği görüntüleme yetkisi için Gate tanımlıyorum. Authenticated user’ı alıyorum ve bir Dog örneğini alıyorum. Gate, bu kullanıcı bu işlemi yapabilir mi diye bir Boolean döndürmelidir. Mantığım şu: Dog’un owner ID’si, kullanıcının ID’siyle aynı olmalı. Bu kadar basit.

Ama burada büyük bir açık olduğunu görebilirsin. Sahibi kontrol etmek yeterli ama admin ne olacak? Admin tüm köpeklerin sahibi olmayacak elbette. Bu yüzden view dog Gate’ini biraz daha geliştirdim. Önce kullanıcının admin olup olmadığına bakıyorum. Eğer adminse true döner ve Gate yetkiyi verir. Değilse köpeğin public olup olmadığına ya da kullanıcının sahibi olup olmadığına bakarım. Bunlardan biri true ise kullanıcı bu köpeği görebilir.

Sonuç olarak update dog için de aynı mantık geçerli. Eğer adminsem güncelleyebilirim, değilsem sadece sahibi olduğum köpeği güncelleyebilirim. Bu çok fazla tekrar eden koddur. Diyelim ki 10 Gate var ve her biri admin kontrolü yapmalı. İşte burada before intercept metodu devreye giriyor.

Before metodu Gate’e şunu söyler: “Diğer tüm kontrollerden önce bunu çalıştır.” Eğer admin hakkında bir şey dönüyorsa Gate bunu sonuç olarak alır, dönmüyorsa normal Gate çalışır. Burada yapılan yaygın hata şudur: Admin olsun ya da olmasın true ya da false döndürmek. Oysa before metodu herhangi bir şey döndürürse Gate’in sonucu o olur. Bu yüzden benim örneğimde adminsem true döndürürüm, Gate durur. Admin değilsem hiçbir şey döndürmem; diğer Gate’ler kendi kontrolünü yapar. Böylece delete dog gibi ek kontrollerde admin kontrolü tek yerden yapılmış olur.

Gate tanımlamışsak bunları nasıl kontrol edeceğiz? Birkaç yolu var. Gate facade kullanarak allows metodunu çağırabilirsin. İlk parametre Gate’in adı, ikinci parametre bağlamdır. Bu örnekte kullanıcının köpeği adopt edip edemeyeceğini kontrol ediyorum. Gate facade, authenticated user’ı otomatik olarak alır. Laravel’in authentication sistemini neden kullanman gerektiğine bir örnek daha. Allows metodu Boolean döner. Eğer adopt edemiyorsam abort helper ile 403 döndürürüm. Pek çok Laravel metodunda olduğu gibi bunun tersi de vardır: denies. Bazı senaryolarda denies okumak daha anlamlı olabilir.

Bu örnekte “see available dogs” Gate’ini kontrol ediyoruz. Bunun bir bağlamı yok, sadece basit bir eylem. Eğer Gate denies dönerse kullanıcıyı index sayfasına yönlendiriyorum ve bir hata mesajı gösteriyorum. Yoksa kullanılabilir köpekleri gösteriyorum. Umarım bu videodan sonra köpek sahiplenme isteği duymazsın. Aslında umarım duyarsın. 🙂

Gate sonuçlarını kontrol etmenin bir başka yolu authorize metodudur. Bu bir dog resource controller içinde delete işlemini kontrol eder. Authorized metodu, kullanıcı bu köpeği silebilir mi diye kontrol eder. Eğer silebiliyorsa kod devam eder. Silemezse bir authorization HTTP exception fırlatılır. Kullanıcı deneyimini özelleştirmek istemiyorsan bu çok hızlı bir yöntemdir.

Sayfa görüntüleme gibi durumlarda önceki yöntemleri kullanmayı tercih ederim çünkü kullanıcılar yanlışlıkla erişemeyecekleri sayfaların linklerini paylaşabilirler. Ama delete gibi bir işlem kolayca paylaşılabilir bir link değildir, bu yüzden özel bir kullanıcı deneyimi oluşturmam.

Middleware’i burada sadece diğer yetkilendirme yöntemlerine bağlamak için anıyorum. Laravel, Gate sonuçlarını kontrol eden bir middleware ile gelmez. Çünkü Gates basittir ve küçük senaryolar içindir. Kendi middleware’ini yazabilirsin ama middleware düşünecek kadar ileri gittiysen daha uygun araçlar var.

Gate’ler bu kadar. Kötü oldukları için değil, sadece çok basit oldukları için. Birçok fonksiyon eklendikçe kod karmaşık, tekrarlı ve test edilmesi zor hale gelebilir. Bu yüzden bir sonraki videoda Laravel’in yetkilendirme alanındaki en güçlü aracı olan Policies’e geçiyoruz.
