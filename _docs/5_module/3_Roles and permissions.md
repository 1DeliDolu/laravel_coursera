# 🎭 Roller ve İzinler

Bu videoda **Roles and Permissions** konusundan bahsedeceğim. Bunun için toplulukta çok güvenilen bir paket olan **Laravel Permissions** üzerinde duracağım. Bu paket, popüler bir Laravel geliştirici firması olan **Spatie** tarafından geliştirilmiş. Soyadı sık sık yanlış telaffuz edilen biri olarak, bunu yanlış söylüyorsam şimdiden özür dilerim.

Hadi başlayalım.

İlk olarak, yaygın roller ve izin yapılarından ve bu paketin de benimsediği mantıktan bahsedelim. Öncelikle roller, bir kullanıcı için 0’dan çoğa olacak şekilde uygulanabilir. Aslında herhangi bir nesne için de olabilir. Biz bu videoda kullanıcılar üzerinden gideceğiz ama aynı mantık bir company, partnership hatta fish için bile geçerlidir.

Bir kullanıcının hiç rolü olmaması mümkündür. Bu, kullanıcının doğrulanmamış bir ziyaretçi gibi düşünüldüğü durumlarda olabilir. Ancak çoğu kullanıcı en az bir role sahip olur. Bazıları birden fazlasına da sahip olabilir.

Permissions ise 0’dan çoğa olacak şekilde bir role bağlanır. Teknik olarak izinleri doğrudan kullanıcıya veya nesneye de verebilirsiniz fakat bunlar çok nadir durumlardır ve ben kesinlikle önermiyorum. Permissions, role’lere uygulanmalı.

Bir role birden fazla permission eklenebilir. Farklı roller aynı permission setlerine veya karışımlarına sahip olabilir.

Peki roller ve permission'lar açısından neyi kontrol etmeliyiz? Paket hem rol hem de permission kontrolü şansı verir ancak ben sadece **permissions** kontrol etmenizi öneriyorum. Tıpkı önceki videoda authentication yerine authorization kontrol edilmesini önerdiğim mantık gibi.

Roller değişebilir. Ek izinler alabilir, çıkarılabilir. Ama roller sadece bir permission koleksiyonu olmalıdır. Permission ise gerçekte bir şey üzerinde kontrolü belirleyen yapıdır. Her zaman permission kontrol edin.

Ben bunu şöyle düşünüyorum: Bir konserin backstage girişinde bir güvenlik görevlisi var. Band members — bu bir roldür — içeri girebilir. Press görevi olan photographers da içeri girebilir. Burada role odaklanıyoruz gibi görünse de aslında permission'ı kontrol ediyoruz. Çünkü rollerden türetilmiş bir permission var: backstage'e giriş izni.

## 📦 Paketin Kurulumu

Bu paketin kurulumu oldukça basit. Ancak küçük bir uyarı: yüklemeden önce mutlaka kaynağı inceleyin. Evet, paket çok kapsamlı ama güvenli programlama yaparken açık kaynak olmasının avantajını kullanmak gerekir. Ben iyi ve güvenilir olduğunu söylüyorum ama **trust but verify** her zaman geçerlidir.

Kurulum için composer paketi yüklenir ve ardından config ile migrations yayınlanabilir. Varsayılan yapı genelde kullanıma hazırdır. Ancak cache sistemi kullanır, bu nedenle config’i cache’liyorsanız değişiklik yaptığınızda cache’i temizlemeniz gerekir. Son olarak migrations çalıştırılır ve gerekli tablolar oluşturulur.

## 🧬 Role ve Permission Kullanımı

Bir nesne üzerinde bu yapıyı kullanmak için **HasRoles** trait'ini eklememiz gerekir. Bu benim user modelim, burada HasRoles trait’ini ekliyorum. Bu trait user’a roller ve permissions ile ilgili ek metotlar kazandırır, ilişkileri de içerir.

Şimdi seed dosyasında bazı roller ve permissions oluşturacağım. Bir admin rolü ve bir support staff rolü tanımlıyorum. Uygulamamda sadece iki tip kullanıcı var ve farklı permission’lara sahipler.

Sonra permission’ları tanımlıyorum: edit user permission ve view sales data permission. Gerçek uygulamada çok daha büyük bir permission set’iniz olacaktır.

Admin’e tüm permission’ları veriyorum. Support staff ise sadece edit users iznine sahip olacak. Sales data görmemeli, buna ihtiyacı yok.

Bu noktada aklınıza şu gelebilir: admin rolünü kontrol etsem ve gerektiğinde permission’ı da kontrol etsem olmaz mı? **Hayır, yapmayın.**

Admin’e her zaman tüm permission’ları verin.

Neden?

Birincisi, kontrolleriniz çok karmaşıklaşır. Hem admin hem permission kontrolü yapmak mantıklı değildir.

İkincisi — bunu uydurmuyorum — çalıştığım projelerde admin yanında super admin, regional admin, developer admin gibi seviyeler geldi. Admin her zaman en üst seviye kalmıyor. Öyle olacağını varsaymayın.

## 🔍 Permission Kontrolü

HasRoles trait’i user modeline **hasPermissionTo** metodunu ekler. Bu örnekte kullanıcının view sales data izni olup olmadığını kontrol ediyoruz. Yoksa 403 döneriz.

Bu size tanıdık gelebilir. Permission'lar daha sade yapılardır, roller üzerinden hiyerarşi kazanırlar. Aslında Laravel içinde bunlar  **gates olarak kaydedilir** . Bu nedenle can metodu ile permission kontrolü yapılabilir. Gate kontrolü yapılan her yerde permission kontrolü de yapılabilir.

Ayrıca policy içinde de bu permission’ları kullanabilirsiniz. Ben policy’leri çok sevdiğim için bu benim favorim. Policy’nin gücünü permission sistemi ile birleştirmiş olursunuz.

Son olarak permission adında bir middleware da vardır. Middleware parametresi olarak permission adını gönderebilirsiniz.

## 🧱 Permission İsimlerini Yönetme

Kod içinde string constant’lar beni rahatsız eder. IDE autocomplete ile uyumlu değildir, birebir doğru yazmak gerekir. Bazen bir geliştirici saçma bir isim koyabilir. Kodda bunları kullanmak zorunda kalırsınız.

Benim tavsiyem: Permission class’ını extend edip kendi class’ınızı oluşturun veya bir interface yapın. İçine public PHP constants tanımlayın. Bu örnekte üç constant tanımladım. Bunları permission adları olarak kodda kullanabilirim.

Bu refactoring süreci ile isimleri normalize etmiş oluruz. Ayrıca IDE autocomplete desteği kazanırız. Permission listesini de tek noktada görürüz.

Middleware üzerinde bu constant’ları kullanabiliriz. Birden fazla permission kontrol etmek istersek array gönderebiliriz.

Tek sevmediğim şey string birleştirme. Okuması güç, karmaşık. Bu nedenle küçük bir helper fonksiyonu yazdım. Permission middleware’i array halinde veriyorum, helper metot formatlıyor.

Bu şekilde güvenli çözümü tercih ederken kod okunabilirliğini de artırmış oldum.

## 📌 Özet

Laravel Permissions paketi hakkında söyleyeceklerim bunlar. Gates gibi kullanabilirsiniz, policies ile entegre edebilirsiniz. Ve unutmayın:

**Her zaman role değil, permission kontrol edin.**

Sırada bu authorization araçlarını kullanırken dikkat edilmesi gereken best practices var.
