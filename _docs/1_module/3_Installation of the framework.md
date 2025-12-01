# 🚀 Framework’ün kurulumu

Bu videoda, Laravel framework’ü için güvenli kurulum seçimlerinden bahsedeceğiz. Peki Laravel’i nereden edinirim ve hakkında nasıl bilgi sahibi olurum? Bir numaralı en iyi yer Laravel dokümantasyonudur; bunları laravel.com/docs adresinde bulabilirsiniz. Dokümanlar açılır menüsünden kullandığınız Laravel sürümünü seçmeyi unutmayın.

Bir Laravel projesinin iki parçası vardır: Öncelikle framework’ün kendisi, bunu github.com/laravel/framework adresinde bulabilir ve kaynağını denetleyebilirsiniz. Framework temel yapı taşlarıdır ve size bir uygulama oluşturmanız için gerekli tüm araçları sağlar, ancak bundan fazlasını sağlamaz. Asıl Laravel projesi ise github.com/laravel/laravel adresinde bulunabilir.

Bu, framework’ün etrafına bir iskelet oluşturur ve uygulamanıza başlamak için tüm dosyaları ve bazı yapılandırma nesnelerini sağlar. Bu öğrenim yolu boyunca Laravel 8.41 kullanacağız.

Artık dokümantasyonu ve kaynağı nerede bulacağımızı bildiğimize göre Laravel’i gerçekten kurmanın yollarından bahsedelim. İlk yöntem Composer create-project kullanmaktır. Önce Composer’ın kurulu olduğundan ve PATH içinde bulunduğundan emin oluruz. Ardından Composer create-project komutunu çalıştırır, Laravel projesini kullanır ve my app adlı bir proje oluştururuz. Burada izlediğiniz gibi bir dizi kurulum işlemi yapacak, Composer bağımlılıklarını çıkaracak ve kuracaktır.

Ve ardından kurulum tamamlanır. Doğrulamak için my app dizinine girer ve artisan komutunu çalıştırarak çalıştığını görürüz. Sürümü de kontrol edebiliriz. Başarılı. Bu, GitHub’dan doğrudan denetlediğiniz kodu aldığı için Laravel’i kurmak için önerdiğim yöntemdir: Composer create-project komutunu kullanmak.

Ekosistemde ayrıca Laravel Sail adı verilen, docker tabanlı bir yapılandırma sistemi vardır. Başlamak için şu önerilir: Dokümanlar, uygulamanıza göre özelleştirilmiş bir bash betiğini sitelerinden doğrudan almanızı söyler. Ancak benim gibiyseniz bunu yapmak istemezsiniz. Bunun yerine o özelleştirilmiş betiği indirip çalıştırmadan önce yerel bilgisayarıma getirip incelemeyi tercih ederim.

Tamam, burada korkutucu veya kötü bir şey görünmüyor, bu yüzden Sail’i çalıştırmak için bunu kullanabilirim. Laravel tek başına birçok şey yapabilir, ancak bir sonraki videoya geçelim; burada Laravel’i diğer Composer paketleriyle güvenli şekilde nasıl genişleteceğimizi öğreneceğiz.
