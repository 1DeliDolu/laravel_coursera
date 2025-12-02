# 🛡️ Eloquent property security

Eloquent modelleriyle çalışırken bazı güvenlik konuları göz önünde bulundurulmalıdır. Bu, değerlerin **mass assignment** ile atanması ve görünürlük (visibility) şeklinde karşımıza çıkar.

Mass assignment, bir değerler dizisini tek bir metod ile Eloquent modelinin özelliklerine topluca atama uygulamasıdır. Bu işlem soyut olduğundan, Eloquent istenmeyen değer atamalarını sınırlamaya yardımcı bazı mekanizmalar sunar. Aslında iki yöntem sunar:

- **guarded** — bir *block list*
- **fillable** — bir *allow list*

**guarded** dizisinde değer belirttiğinizde, yalnızca bu alanlar mass assignment ile atanamaz. Bu alanlara atama yapılmaya çalışılırsa exception fırlatılır ve işlem durdurulur.

Buna karşılık **fillable**, yalnızca toplu atamaya izin verilen özellikleri belirtir. Bu listenin dışındaki herhangi bir alan gönderilirse yine exception fırlatılır ve işlem durdurulur.

Programcıların iki farklı tarafta toplandığını görürüz: fillable’cılar ve guarded’cılar.
Guarded yanlıları, listenin güncel tutulmasının uzun sürdüğünü, verimsiz olduğunu ve zaten verilerin doğrulanması gerektiğini söyler.
Fillable savunucuları ise —benim gibi— bir diziye yeni bir eleman eklemenin o kadar da uzun sürmediğini ve kullanılan değerlerin her zaman bilinmesi gerektiğini savunur. Ayrıca **defense in depth** yaklaşımını severim: doğrulama ve sınırlama fillable ile birlikte kullanılmalıdır ve bunu kullanmamak için bir sebep yoktur.

Şimdi fillable veya guarded doğru yapılandırılmadığında neler olabileceğine bakalım.

Bu örnekte bir **dog** modeli var ve bir kullanıcıya user owner ID alanıyla bağlı. ID alanını guarded yapıyorum çünkü hiç kimse ID’yi değiştirmeye çalışmamalı. Diğer alanlar —köpeğin adı, cinsi vb.— değiştirilebilir olmalı.

Kullanıcının sadece köpeğin adını değiştirmesine izin veren bir form yapıyorum. İşte kullanacağım doğrulama. Temelde, name alanının zorunlu ve belirli sınırlar içinde olduğundan emin oluyorum.

Controller’ın update metodu burada. Dog modeli update metodu ile güncelleniyor ve ardından kaydediliyor. Dikkatli izleyenler request all kullanıldığını fark etmiştir; validated metodunun yerine. Bazen böyle olur. Bu durumda programcı doğrulamayı gerekli görmemiş ya da unutmuştur.

Peki bu nasıl sonuçlanıyor görelim.

Burada sahipleri ile birlikte köpeklerin bir listesi var ve herhangi bir köpeği düzenleyebilirim. Bu köpeğin adını Mister olarak değiştireyim. Düzenlediğimi görebilirsiniz. Ama bu köpek başka bir adama ait ve ben bunun bana —Captain Aaron’a— ait olmasını istiyorum.

Inspect aracını açıp fazladan veri gönderebilir miyim? Gizli bir input ekliyorum. İsmi userID olacak ve ID’min 42 olduğunu biliyorum. Bunu gönderiyorum. Adı da tekrar değiştiriyorum ki görünür olsun. İşte böyle, artık köpek bana ait.

Doğru bir authorization ve validation sistemi olsaydı bu kadar kolay olmazdı. Ama yine de defense in depth…

Bu durumda —ve aslında tüm durumlarda— fillable kullanmanızı öneriyorum. Eğer fillable tanımı bu şekilde olsaydı, gönderdiğim owner user ID başarısız olur ve exception fırlatılırdı. Demo çalışmazdı.

Bazen Eloquent modellerinde bazı verileri görüntülenmekten veya endpoint’lere serialized edilmekten korumak isteriz. Bu durumda bu özellikleri **hidden** listesine ekleyebiliriz. Eloquent model Json’a dönüştürüldüğünde (örneğin bir API endpoint’i için) bu alanlar gizlenir.

Bu örnekte timestamps’leri kapatılmış bir company modeli var. Bu tanıma göre modelin tüm özellikleri serialized edilip görüntülenecektir. Company endpoint’ini çağırdığımda tüm verilerin gönderileceğini varsayalım. Laravel, koleksiyon ile dönerken modellerin toJson metodunu otomatik çağırır.

Curl ile endpoint’i test ettim ve JQ ile görüntüyü kolaylaştırdım. Tüm özellikler görünüyor.

Biraz ilerleyelim: her şirkete authentication için bir API key ekledik. Bunun görünmesini istemeyiz. Endpoint’i tekrar çağırıyorum ve çıktı içinde API key’in olduğunu görüyorum. Bu doğru değil.

Bu gizli anahtarın çıktı içinde görünmesini engellemek için, onu hidden listesine böyle ekleyebilirim. Artık Json çıktısında bu secret key görünmez.
