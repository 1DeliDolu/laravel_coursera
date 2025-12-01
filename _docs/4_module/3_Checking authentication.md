# 🛠️ Özel Kimlik Doğrulama (Custom Authentication)

Bu videoda, Laravel’in yerleşik kimlik doğrulama sistemini kullanarak **özel kimlik doğrulaması** yapmaktan bahsedeceğiz. Bir projede özel doğrulama yazmam gerektiğini düşündüğümde daima şu kuralı hatırlarım:

**Özel doğrulama yazma.**

İşte bu kadar. Video bitti.

Tabii ki gerçekten özel bir şey gerektiği durumlar vardır, bunu anlıyorum. Bu gibi durumlarda bile Laravel’in kendi kimlik doğrulama sisteminden faydalanmak isteriz. Bunu, özel bir user authentication provider, özel bir auth provider, middleware ve authorization ile birleştirerek yapabiliriz. Laravel içinde bulunan tüm bu mekanizmalarla neredeyse karşınıza çıkacak her durumu çözebilirsiniz.

Bir örnek düşünelim. Diyelim ki kullanıcıyı üçüncü bir taraf üzerinden doğrulamamız gerekiyor. Bu üçüncü taraf şöyle çalışıyor: user email adlı bir alan ve password adlı bir parola alanı içeren bir POST isteği gönderiyorsunuz. Eğer doğrulama başarılıysa true, değilse false döndürüyor. Oldukça basit. İsim, roller veya başka kullanıcı bilgilerini tutmuyor; elimizde sadece bir Boolean sonuç var.

Bu durumda sistemi ikiye ayırmak mantıklı olur.

* Yerel veritabanımda bir user modeli tutarım: e-posta adresi, roller, isim vb. uygulamaya özgü alanlar orada bulunur.
* Parola ise veritabanında tutulmaz; kullanıcıyı doğrulamak için üçüncü taraf API’ye başvururuz.

Amaç şu: email’e göre yerel user modelimi bulacağım, fakat parola kontrolünü harici sisteme yaptıracağım.

Bunu nasıl uygularız?

İlk iş olarak **remote_auth** isminde yeni bir configuration dosyası oluşturur ve doğrulama yapacağım URL’yi buraya eklerim. İleride environment değişkenine taşırım veya production/staging/test ortamları için farklı URL’ler eklerim. Önemli olan, tek bir yerden yönetilmesi.

Ardından **özel user provider** yazacağız. Laravel’in varsayılan provider’ı olan eloquent user provider aslında benim tüm ihtiyaçlarımı karşılıyor. Kullanıcıyı buluyor, hatırlama token’larını yönetiyor vb.

Bu benim için işe yarıyor çünkü sistemim "split system" — yani kullanıcı modeli yerelde var, sadece parolasız.

Eğer uzaktaki sistemden daha fazla bilgi almam gerekseydi provider’daki daha fazla metodu ezmem gerekirdi. Laravel dokümantasyonu bu metodların her birinin ne yaptığını açıklar.

Fakat benim bu örnekte tek yapmam gereken şey **validateCredentials** metodunu değiştirmek.

Auth yaşam döngüsünde bu aşamaya geldiğimizde, email ile eşleşen kullanıcı yerel veritabanından zaten getirilmiş ve Eloquent modeli olarak hydrate edilmiştir. credentials dizisi içinde email ve password vardır. Bu email ve password ile remote_auth URL’sine HTTP facade üzerinden bir POST isteği gönderirim. Bu örnekte üçüncü taraf alan adları email ve password değildir, bu yüzden burada onları eşliyorum.

Sonuç olarak JSON döner ve içinde Boolean bir result anahtarı vardır. Normalde bu metot bir password hash ile eşleştirme yapıp true veya false döner. Ben aynı şekilde Boolean sonucu döndürebilirim.

Göstermesi kolay olsun diye bu örneği basit tuttum. Gerçek bir sistemde 500 ve 404 gibi hatalara karşı try/catch kullanır, gerektiğinde özel hatalar fırlatır veya log’layıp false dönerdim.

Sonra AppServiceProvider içinde auth provider’ı kaydederiz. Bu metot bir app instance ve auth config’inden provider’ın config dizisini gönderir. Eloquent user provider bir hasher ve model adı ister; biz de extend ettiğimiz için bunları iletiriz. Buradaki en kritik kısım, auth provider metodunun ilk parametresine verdiğimiz isimdir. Çünkü bunu auth config dosyasında provider driver olarak kullanırız.

Artık Laravel’in doğrulama sistemi normal şekilde çalışacak, tek farkı email/parola doğrulamasını üçüncü taraf API’ye yapıyor olmasıdır.

Şimdi bunu Laravel Breeze kurulumunda test edelim.

Önce geçersiz email ile deneyelim — kullanıcı modeli bulunamadı.

Sonra kullanıcı var ama parola yanlış — kullanıcı bulundu fakat üçüncü taraf doğrulaması başarısız oldu.

Son olarak doğru parola ile doğrulama başarılı ve giriş yapılmış durumda.

Bu şekilde kullanıcı, sistemin farklı çalıştığını bile fark etmez.
