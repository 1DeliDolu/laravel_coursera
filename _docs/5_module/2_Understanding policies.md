# 🎯 Politikaları Anlamak

Laravel'da yetkilendirmenin bir sonraki yöntemi olan  **policies** ’ten bahsedeceğim. Policies, daha ayrıntılıdır; çünkü nasıl oluşturuldukları ve mimarileri gereği, authorization ve gates’e göre çok daha spesifik olan durumlara odaklanırlar. Göreceğimiz üzere, geleneksel olarak  **resource** ’lara bağlıdırlar. Yani bir **resourceful Controller** ve ona ait tüm metodlar olduğunda, policy içinde de create veya update gibi bu aksiyonlarla hizalanan metot isimleri bulunur.

Bu noktada belirtmem gerekir ki bu bir **konvansiyon**dur ve Laravel bu konvansiyonu takip ettiğinizde işlerinizi kolaylaştıran metotlara sahiptir. Ancak teknik olarak bu isimlendirmeyi takip etmek zorunda değilsiniz. Örneğin, bir köpek sahiplenme işlemini kontrol etmek istediğinizde adopt adında bir metot tanımlayabilirsiniz.

Fakat çoğu durumda bu konvansiyon işleri kolaylaştırır. Çünkü policy’ler sınıf olarak tanımlandığı ve **service container** üzerinden çözümlendiği için, kendi içinde de dependency resolution sağlarlar. Yani bir policy tanımladığınızda dependency injection ile başka nesneleri constructor parametresi olarak isteyebilirsiniz. Örneğin, bir şeyi güncelleme iznini üçüncü parti bir aboneliğe göre kontrol etmek istiyorsanız, o API class'ını policy içine inject ederek bu kontrolü uzaktan yapabilirsiniz.

## 🔧 Policy'lerin Kaydedilmesi

Önce policies’leri nasıl kaydedeceğimizi konuşalım. Daha önce gates kaydettiğimiz  **AuthorizationServiceProvider** ’a dönüyoruz, ancak bu kez policy kaydetmek için kullanıyoruz. Parent class içinde modeller ile policy’leri ilişkilendiren bir metot bulunur. Yapmanız gereken tek şey, protected policies özelliği içinde model/resource’u key, policy class’ını value olarak tanımlamaktır.

Policy’ler için **autoregistration** etkinleştirilebilir. Hatta sistemin her model için hangi policy’nin kullanılanacağını tahmin etme biçimini özelleştirebilirsiniz. Ama ben bunları **elle tanımlamanızı** öneriyorum. Çünkü görünürlük çok önemlidir; tek bir yerde tüm policy’lerin kayıtlı olduğunu görmek büyük avantaj sağlar. Aksi hâlde policies klasörünü tek tek kontrol edip modellerinizle eşleşip eşleşmediğini takip etmeniz gerekir.

Ayrıca programlama tarzınıza göre burada yeni policy class'larını kolayca değiştirebilirsiniz; bu, sınıf adlarını değiştirmekten veya dosya silmekten çok daha kolaydır.

## 🚗 Bir Policy Sınıfı Örneği

Bu örnekte Car resource model’i için oluşturulmuş bir policy görüyoruz. Bir trait olan **HandlesAuthorization** kullanılıyor. Bu trait, bazı işlevleri kolaylaştıran yardımcı metotlar içerir. Artisan komutuyla policy oluşturduğunuzda bu trait otomatik eklenir ancak zorunlu değildir.

Buradaki önemli kısım **view** metodudur. Parametre yapısı gate closure’larına benzer: bir user ve bir resource/context alır. Bu metot, kullanıcının o işlemi yapıp yapamayacağını belirlemek için true veya false döner.

Konvansiyon gereği, metot adı policy aksiyonunun adıdır. Yani burada bir kullanıcının belirli bir car için view yetkisi olup olmadığını soruyor olacağız. Policy sistemi user parametresini otomatik olarak ilk argüman olarak geçer. İkinci ve diğer parametreler sizin belirleyebileceğiniz context parametreleridir.

Bu örnekte, car **public** ise veya kullanıcı car’ın sahibi ise görüntüleyebilir.

## 🛠 Daha Kapsamlı Policy Sınıfı

Daha kapsamlı bir policy sınıfında view, update ve delete gibi metotlar bulunabilir. Resourceful Controller metodlarının tamamını policy içinde tanımlayabilirsiniz. Örneğin update dışında edit metodunu da tanımlayabilirsiniz; bu, güncelleme öncesi içeriğin gösterildiği controllera karşılık gelir.

Bu şekilde tüm authorization mantığı tek bir sınıfta toplanmış olur. Ayrıca inject edilen üçüncü parti sınıfları da burada yeniden kullanabilirsiniz.

## 👑 Admin Kullanıcısı Sorunu

Gates’te olduğu gibi burada da admin kullanıcılarını ayrı ele almak isteyebilirsiniz. Policy, **before** adında özel bir metot sunar. Bu metot user ve ability parametrelerini alır. Genellikle ability ile ilgilenmezsiniz; sadece admin mi diye kontrol eder ve true döndürürsünüz. True döndürdüğünüzde policy’nin diğer metotları hiç çalışmaz.

Bu örnekte admin kullanıcılar car üzerinde update yapabilir. Admin değilse, kullanıcı car’ın sahibi olmalıdır.

Before metodu logic’i sadeleştirir ama yine de her policy sınıfına bu metodu yazmanız gerekir. Çoğu uygulamada admin veya staff kullanıcılarının birçok şeyi yapma izni olur. Bu nedenle bu before metodu bir **trait** içine taşınabilir. Böylece ihtiyaç olan her policy sınıfında sadece trait’i kullanırsınız.

## 🧩 Controller İçinde Authorization’ın Kullanımı

Bu resourceful controller örneğinde policies şu şekilde kontrol edilir:

Kullanıcı üzerinden **can** metodu kullanılır. Bu noktada teknik bir ayrıntı olarak, can metoduyla istediğiniz user modeli üzerinde yetkilendirme kontrolü yapabileceğinizi söylemiş olayım. (Gates içinse forUser metodu gerekir.)

Genelde current user, request içinden alınır ve can metodu şu şekilde çağrılır:

can('update', car)

Update metodunda eğer kullanıcı güncelleyemiyorsa 403 döneriz. Delete örneğinde cannot metodu da gösteriliyor.

Yetkilendirmeyi manuel kontrol etmek istemiyorsanız, controller içinde authorize metodunu kullanabilirsiniz. Bu metot başarısız olursa Http unauthorized exception fırlatır.

## 🔗 Resourceful Controller ile Entegre Authorization

Route dosyasında resource metoduyla controller tanımladığınızda, predictable metot adları oluşur: create, update, store, delete vb. Bunlar policy metotları ile uyumludur.

Bu durumda controller’ın constructor’ına authorizeResource metodu ekleyebilirsiniz. İlk parametre model sınıfı, ikinci parametre ise route parametresinin adıdır. Bu metot, tüm resourceful metotlara göre otomatik policy kontrolü oluşturur.

Bu sistem, policy tanımlamadığınız durumlarda hata vererek güvenliği artırır. Eksik yetkilendirilmiş şeylerin yanlışlıkla açık kalmasını engeller. Adeta bir **allow list** mantığıyla çalışır.

## 🔒 Middleware ile Authorization

Bir diğer yol da **can** middleware’idir. Route üzerinde ability adı ve route parametresi geçilerek kullanılabilir.

## 🖥 Blade İçinde Authorization

Blade’de authorization kontrolü için **@can** direktifi kullanılabilir. Örneğin güncelleme yetkisi olan bir kullanıcıya car’ı düzenle butonu gösterilebilir.

## 📌 Özet

Policies, daha granüler ve resource odaklı authorization sağlar. Sınıf bazlıdır, dependency injection destekler ve esnek yetki kontrolü sunar. Gates daha basit olsa da policies ayrıntılı kontrollerde idealdir.

Peki organizasyonunuzdaki farklı roller ve izin seviyeleriyle çalışmak isterseniz? İşte bunun için güvenilir bir üçüncü parti pakete ihtiyaç duyarız — bir sonraki videoda buna bakacağız.
