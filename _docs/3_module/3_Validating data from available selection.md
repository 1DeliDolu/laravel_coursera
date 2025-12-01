# 🧪 Mevcut seçimden veri doğrulama

Alan değerlerinin kullanılabilirliğini doğrulamaktan bahsedelim. Öncelikle şunu söylemeliyim ki, bu doğrulama kuralı gruplarını bir araya getirmenin en iyi yolunu bulmak gerçekten zordu. Bunları kapsayacak mükemmel bir sıralama var mı emin değilim çünkü burada görebileceğiniz gibi, zorunlu ve isteğe bağlı alan varlığından bahsedeceğiz.

Dolayısıyla, doğrulama kurallarını hangi sırayla kullanmanız gerektiği sorusunun cevabı “duruma göre değişir”, fakat temel olarak önce bir şeyin gerekli olup olmadığını doğrulayın, sonra veri tipini, ardından o tipte olup olmadığını ve ardından sınırlarını.

Ama biri çıkıp “Belki önce sınırları kontrol etmeliyiz, çünkü kullanılabilirliği kontrol etmek daha karmaşık olabilir, neden önce bunu yapalım?” diyebilir. Yani yine cevap: duruma göre değişir. Neyse, slayda geri dönelim.

Bir alanın nasıl var olması gerektiğini belirleyen üç doğrulama kuralı vardır.  **Required** , alanın mutlaka var olmasını ve değerinin boş olmamasını gerektirir. **Filled** biraz farklıdır; gelen payload içinde alan tanımlıysa boş olmamalıdır. **Nullable** ise bu alanın var olabileceği, bir şey içerebileceği, boş veya null olabileceği ya da hiç bulunmayabileceği anlamına gelir.

Bu örnekte name ve spouse names oldukça açık örneklerdir. Ancak children names, filled’ın ilginç bir kullanımına sahiptir; temelde sıfır veya daha fazla çocuk ismini bir array olarak girebilirsiniz ama eğer array key’i varsa, string ile doldurulmalıdır.

Bu, düzensiz front end programlamasının bir sürü boş array göndermesini engeller; aksi halde bunları sonradan filtrelemeniz gerekir ya da programlamanızda hataya neden olabilirler. Düşüncelerimi düzenledim ve çok fazla zıplamaya çalışmıyorum, gerçekten. Ama doğrulama doğrusal olarak takip edilmesi zor bir konu.

Doğrulama yığını hakkında tekrar konuşalım. Kullanılabilirliği değerlendirirken yararlı bir kural **bail** kuralıdır. Bu, bir hata olur olmaz doğrulamayı durdurur.

Bu neden önemlidir?

Bir numeric ID bekliyorsanız ve bunu veritabanına karşı kontrol edecekseniz, kullanıcı size bir dizi harf göndermişse veritabanı sorgusu yapmak istemezsiniz.

Eğer integer kontrolünü önce yapan bir kural yığını yazarsanız ve bunu bail ile öne koyarsanız, gereksiz sorgu yapmazsınız. Laravel’in yerleşik kuralları bağlama duyarlı hâle getirme konusunda büyük ilerleme kaydettiğini söylemeliyim. Yeni Laravel sürümleri veri tiplerine göre bazı kontrolleri çıkarabiliyor veya bazı kuralları atlayabiliyor.

Bu da veri tipi doğrulamasını ilk sıraya koyma nedenlerimden biri. Ama yine de en iyisi emin olmak ve hatayı erken kesmektir.

Bu, ileride custom validation’dan bahsederken daha da önemli hâle gelecek. Son aşamada uzak bir üçüncü tarafla doğrulama yapıyorsanız, onlara çöp veri göndermek istemezsiniz.

Tamam, değer kullanılabilirliğine geri dönelim.

Bir değerin öngörülebilir bir değer kümesi içinde olup olmadığını kontrol etmek için **in** kuralını kullanabilirsiniz. Bu, select dropdown’lar veya önceden tanımlı listeler için faydalıdır.

Ancak in kuralının gücünü keşfetmeye başladığınızda abartmamaya dikkat edin. Veriyi önce veritabanından çekip hydrate edip sonra bu kurala göndermeye çalışmayın. Bunun için **exists** kuralı vardır.

Exists kuralı en basit hâliyle, alanı aynı isimdeki veritabanı tablosu sütunuyla karşılaştırır. Bu örnekte gönderilen ID alanı users tablosunun id sütununda var olmalıdır.

Muhtemelen bununla ilgili bazı problemlerin farkına vardınız bile. Mesela, ya o sütun farklı bir isimdeyse? Ya da izinler gibi hesaba katmamız gereken başka kısıtlamalar varsa?

Rule sınıfındaki **exists** metodunu bu örnekteki gibi kullandığımızda (bu arada okunabilirlik için tamamen namespace’li hâliyle yazıyorum, kendi kodunuzda bunları sınıfın üstünde import etmenizi öneririm), burada assigned_user_id alanını kontrol ediyoruz.

Users tablosunu ama özellikle id sütununu kontrol etmek istiyorum. Ayrıca atanmış kullanıcının bana yani mevcut giriş yapmış kullanıcıya ait olduğundan emin olmak istiyorum. Bu kullanıcıların her birinin parent_id alanı vardır ve burada benim ID’min olması gerekir.

Bu biraz gelişmiş bir örnek, o yüzden neden önemli olduğunu bir demo ile daha iyi görelim.

Bu çok basit uygulamada, çalışanlarıma görev atayan bir supervisor’ım. Diğer ekiplerin başka çalışanları vardır ama ben yalnızca kendi çalışanlarıma görev atayabilirim.

İlk görev atanmış değil, o yüzden çalışanlarımdan birine atayacağım. Dropdown’da iki çalışanımı da görebiliyorum.

Bu görevi Guy Smiley’e atayacağım.

Ama diyelim ki ekibimi kayırmak istiyorum. Daha fazla görev almamalarını istiyorum ki biraz daha boş zamanları olsun; belki benimle takılırlar, bana ne kadar harika olduğumu söylerler.

Bu yüzden assignee alanını güncelleyeceğim ve bunu tarayıcının araçlarıyla düzenleyeceğim. Bu örnekte başka bir supervisor’a ait olan user ID’sinin 5 olduğunu biliyorum. Bunu burada düzenleyeceğim ve gösterim için adını da belirgin bir şekilde değiştireceğim (gerçekte sadece value’yu düzenlemek yeterli).

Bunu gönderdim.

Gönderdiğimde bana bunu yapamayacağımı söyledi. İşte hata burada. Sorgu çalıştı ve evet, ID bir kullanıcıya aitti ama parent_id benim değilmiş. Yani benim çalışanım değil.

Bu yüzden bu hata mesajıyla reddedildi.

Sanırım bunu Bert’e atayacağım.

Bu, Laravel doğrulamasında alan kullanılabilirliğinin temellerini kapsıyor. Temel doğrulama üçlemesinin son kısmı ise  **boundary validation** , ki bu da sırada.
