# ⚙️ Exception Handler Yapılandırması

Bazen monolog instance’ını kullanarak logları filtrelemek biraz aşırı bir çözüm gibi gelebilir. Özellikle de secret’ın yalnızca belirli bir exception’ın context’inde yer aldığını biliyorsanız. Uygulamanızdaki ve üçüncü taraf paketlerdeki custom exception’lar, context metodunu kullanarak exception’a bir context değişkeni ekleyebilir. Bu context daha sonra logların context bölümünde yakalanır. Eğer exception sınıfını kontrol ediyorsanız, context metodunu düzenleyerek secrets’leri dahil etmemek oldukça kolaydır — hatta zaten böyle yapmalısınız.

Peki ya üçüncü taraf bir paket her istediğinizi yapıyor ama secrets’lerini exception context’ine ekliyorsa? Bunun nasıl göründüğüne bakalım. Burada örnek bir komut çalıştırıyorum ve bu bir exception fırlatacak: third party cool package auth exception. Şimdi logladıkları şeye bakalım. Gördüğünüz gibi API key’i context içine koymuş ve bu da loglara eklenmiş — kesinlikle istemediğimiz bir şey.

Logları başka yollarla filtreleyemiyorsak, uygulamamızın exception handler’ında context’i değiştirme çözümümüz var. Ancak bu çözüm biraz çekiç etkisi olan bir yaklaşım. Yani belirli bir exception için değil, tüm exception loglarına uygulanır. Başka bir deyişle, artık her log mesajında API key adlı bir context alanı filtrelenmiş olacaktır.

Burada exception handler içine protected context metodunu ekliyoruz. Anahtarın “API key” olduğunu bildiğimiz için array’leri birleştirip bu alanı filtreliyoruz. Şimdi logların nasıl göründüğüne bakalım. Hata üreten komutu tekrar çalıştırıyorum. Şimdi loglara baktığımızda API key’in context’ten filtrelendiğini görebiliyoruz.

Bu öğrenme yolunda bazen bu tarz örnekler göreceksiniz; çözüm birebir sizin probleminize uygun olmayabilir veya en iyi uygulama olmayabilir. Peki bu en iyi yol mu? Duruma göre değişir. Bir şeyin mümkün olduğu tüm yöntemleri göstermemin sebebi, her projenin yeni bir Laravel kurulumu kadar temiz olmaması ve bazen mükemmel çözümün diğer özel durumlar yüzünden uygulanamamasıdır.
