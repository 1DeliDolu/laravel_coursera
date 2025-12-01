# 🔒 Kimlik Doğrulamanın Yönetimi

Kimlik doğrulamadan devam etmeden önce değinmemiz gereken birkaç küçük ama önemli nokta var. Bunlar, **zaten doğrulanmış** kullanıcıların durumlarını yönetmek ve bu durumu değiştirmekle ilgilidir. Eğer session kullanan bir doğrulama sistemi kullanıyorsanız — ki bahsettiğimiz kitlerin çoğu böyle çalışır — zaman zaman **session ID’yi yeniden oluşturmanız (regenerate)** gerekebilir.

Session ID yenilemek, güvenlik açısından çok önemlidir. Bir şekilde session ID’ye erişim sağlamış bir saldırganın, mevcut kullanıcı dışında bu oturumu sürdürememesini sağlar. Güzel haber: Laravel’in authentication kitlerinden birini kullanıyorsanız, bunlar süreç içinde session ID’yi otomatik olarak yeniler.

Ancak iki durumda bunu sizin yapmanız gerekir:

---

## 🆙 1. Kullanıcı yetki seviyesini yükselttiğinde

Örneğin normal bir kullanıcı oturum açmıştır ancak daha yüksek yetki gerektiren bir işlem yapmak istiyordur. Komut satırındaki *sudo* gibi düşünebilirsiniz. Böyle bir durumda kullanıcıdan parolasını doğrulamasını istersiniz.

Bu doğrulama başarılı olduğunda, **session ID mutlaka yenilenmelidir.**

---

## 👤 2. Yönetici olarak bir kullanıcı hesabını devraldığınız özel durumlar

Bu pek sık görülmez, ama admin’in başka bir kullanıcı hesabını “devralarak” (impersonate) giriş yapabildiği sistemlerde geçerlidir.

Admin, başka bir kullanıcı olarak oturum açtığında session ID yenilenmelidir.

Daha sonra admin kendi hesabına döndüğünde yine session ID yenilemek gerekir.

---

## 🚪 Oturumu kapatma ve diğer oturumları geçersiz kılma

Kullanıcılar, mevcut parolalarını bildikleri sürece şu işlemleri yapabilir:

* Çıkış yapabilir (logout)
* **Diğer tüm cihazlarda oturumu kapatabilir**

Bunu yapmak için Auth facade üzerinden şu işlem çağrılır:

**logoutOtherDevices(parola)**

Bu, kullanıcının parolasının yeni bir hash’ini yazar ve diğer tüm session’ları geçersiz kılar.

---

Bu açıklamalarla birlikte kimlik doğrulama konusunun sonuna geliyoruz.

Fakat unutmayın:  **Authentication** , güvenli bir Laravel uygulamasında erişim stratejisinin yalnızca ilk yarısıdır.

Şimdi ikinci yarıya geçiyoruz:

## 🛡️ Authorization (Yetkilendirme)
