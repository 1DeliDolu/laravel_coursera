# 📘 Laravel Sözlüğü

## Authentication

Authentication, bir istek veya işlem sırasında nesnenin ya da “öznenin” kimliğinin belirlenmesi sürecini ifade eder. Ziyaretçiler giriş yaptığında belirli bir kullanıcı olarak authenticate edilirler.

## Authorization

Authorization, belirli bir aktöre bir eyleme erişim izni verme sürecidir. Bir özne bir “eylem” gerçekleştirmeye çalıştığında bunu yapmasına izin var mı? Tüm authorization işlemleri authenticate bir nesne gerektirmez, ancak çoğu gerektirir. Bir kullanıcı giriş yaptığında ve authenticate olduğunda, sistemdeki çeşitli kaynaklara erişme veya onları değiştirme konusunda authorize edilir.

## Form requests

Form requests, Laravel request nesnesinin bir uzantısıdır. Belirli bir istek için doğrulama kuralları ve authorization sağlayan metodlar içerirler. Genelde oluşturma veya güncelleme isteklerinde kullanılır, ancak herhangi bir metot ile de kullanılabilirler.

## Gates

Bir şeyi “gate etmek”, bu iş akışına veya kod bloğuna erişime izin vermek ya da erişimi engellemek anlamına gelen genel bir programlama terimidir. Laravel’de gates, bir ziyaretçinin bir kaynağa erişmeye yetkili olup olmadığını belirlemek için kullanılan kaba bir authorization aracıdır.

## Middleware

Middleware, bir isteğin ortasında dönüşümler uygulamak veya bir işlem gerçekleştirmek için yapılandırılmış herhangi bir kod parçasıdır. Laravel, authentication ve authorization işlemleri yapan middleware’ler içerir.

## Permissions

Laravel bağlamında permissions, authorization için kullanılan bir eylem veya eylem grubunu temsil eden özel etiketlerdir. Bir kullanıcıya veya role, belirli bir işlemi yapmasına veya kodun bir bölümüne erişmesine izin veren permission verilebilir. Permissions, Laravel’de genellikle policies ile birlikte kullanılır.

## Policies

Gates nasıl kaba bir araç ise, policies Laravel’de ince ayarlı bir authorization aracıdır. Belirli bir nesneye veya onun koleksiyonuna karşı yapılabilecek tüm işlemleri authorize etmek için düzenli, kendi içinde tutarlı bir metodoloji sağlarlar.

## Roles

Roles, nesnelere onların sorumluluklarını belirtmek için verilen etiketler veya tanımlayıcılardır. Çoğu zaman bir veya daha fazla role, user nesnesine uygulanır (ancak yalnızca user nesneleriyle sınırlı değildir). Permissions daha sonra bir role altında toplanır. Gating işlemi ise kullanıcıya ait tüm rollerin permission’larının birleşik koleksiyonuyla gerçekleştirilir.
