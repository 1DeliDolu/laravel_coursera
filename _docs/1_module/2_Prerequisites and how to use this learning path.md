# 📘 Ön Gereksinimler ve bu öğrenme yolu nasıl kullanılır

Merhaba ve Laravel’de secure coding’e, yani verimli bir PHP framework’ü için etkili güvenliğe hoş geldiniz. Benim adım Aaron Saray ve bir security consultant ile web programcısıyım. Beni Aarons.com’da bulabilir veya bana e-posta gönderebilirsiniz: [aaron@aaronsaray.com](mailto:aaron@aaronsaray.com) — burada küçük bir fotoğrafım da var. Merhaba.

Bu kurs, Laravel’in installation süreci için bir giriş ve bazı best practice’leri içerecek. Hadi ilk videoya geçelim: Laravel’de security.

Laravel’in ün kazandığı şey güzelliği, hızı ve sadeliğidir.

Diğer programcılardan sık sık şunu duyuyorum: “Security insanları bana sürekli framework’ümün veya dilimin tüm güzel özelliklerinden vazgeçmem gerektiğini söylüyor.” Kodumdaki hız, sadelik ve estetikten hoşlanıyorum. Ancak security her zaman yavaşlamak demek değildir. Laravel’i güvenli kullanmak biraz touch typing öğrenmek ya da unit test yazmak gibidir. İlk başta sizi biraz yavaşlatabilir, ama alışkanlık hâline geldiğinde oldukça hızlıdır. Workflow’unuza kusursuz şekilde uyar ve başka türlü yapmak istemezsiniz. Secure coding çirkin olmak zorunda değildir.

Laravel’de security’den bahsederken çoğu zaman bir metodu diğeri yerine seçmek kadar basit olacaktır. Örneğin, request class içinde all metodunu kullanmak yerine validated metodunu kullanabiliriz. Kod neredeyse aynı görünür, ancak daha güvenlidir. Programcılar olarak biliyoruz ki business process’ler oldukça hoş, güzel, sade ve şık bir şekilde tasarlanabilir. Aynı şey security işlemlerinin çoğu için de geçerlidir — aslında sürecinizin içine tam olarak oturur. Bu iki konu birbirinden tamamen ayrı değildir.

Peki bu öğrenme yolunu kullanmak için ne bilmeniz gerekiyor?

Öncelikle biraz PHP aşinalığınız olmalı. PHP3 ile çok eski zamanlardan başlamanız veya destructor içinde exception fırlatıldığında ne olduğu gibi karmaşık konuları bilmeniz gerekmiyor. Ancak PHP’nin akış kontrolüne, nasıl çalıştığına ve bir sunucuda nasıl çalıştırıldığına dair temel bir aşinalık gerekli. Aynı şey Laravel için de geçerli. Bu dersi anlatacak kadar uzman olmanıza gerek yok ama az çok tanıdık olmalısınız.

Neden faydalı olduğu, hangi özellikleri sunduğu gibi temel kavramları bilmelisiniz.

Development environment veya production environment konularına değinmeyeceğiz. PHP’yi local makinenizde Engine X veya Apache üzerinde çalıştırabilecek kadar aşina olduğunuzu varsayıyorum. Ya da kodunuzu çalıştırmak için VirtualBox ve Ansible ya da Docker container’ları kullanıyor olabilirsiniz.

Son olarak Git hakkında biraz bilginiz olmalı ve kullanıyor olmalısınız. Dosyalarımızdaki bazı değişiklikleri kontrol etmek için zaman zaman Git kullanacağız.

Bu kursu nasıl kullanmalısınız?

Öncelikle bu kursu documentation ile birlikte kullanmanız gerektiğini bilmelisiniz. Laravel documentation harikadır; gördüğüm en iyi open source dokümantasyonlarından biridir. Ancak tüm seçenekleri sunar ve nadiren opinion veya best practice ekler. Yani oraya gidip nasıl çalıştığını öğrenebilirsiniz; bu kursta ise o seçenekler arasından uygulamanızı en güvenli şekilde yapılandırmanın hangisi olduğunu söyleyeceğiz.

Bu kurs, var olan bir app’i güvenli hâle getirmek veya yol boyunca seçim yapmanıza yardımcı olmak içindir. Baştan sona bir app yazmakla ilgili değildir.

Son olarak, size uygulamanızı güvenli hâle getirme konusundaki tüm objektif gerçekleri vermek için elimden geleni yapacağım; ancak güçlü bazı fikirlerim var ve bunların da büyük ihtimalle yansıyacağını söylemeliyim.

Evet, söyleyeceklerim bu kadar. Hadi bir sonraki videoya geçelim.
