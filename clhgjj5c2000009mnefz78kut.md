---
title: "Angular ve NX ile kendi otomatik kod üreticimizi yazıyoruz."
datePublished: Tue May 09 2023 17:23:30 GMT+0000 (Coordinated Universal Time)
cuid: clhgjj5c2000009mnefz78kut
slug: angular-ve-nx-ile-kendi-otomatik-kod-ureticimizi-yaziyoruz
tags: angular, nx, nx-plugin, angular-schematics

---

TLDR, bu yazının ana amacı kendini tekrar eden bazı sıkıcı işleri otomatize etme yollarından biri olan code generatorlara örnek vermek. Yazıda örneği basit tutacağım ancak schematic sadece bir code snippettan fazlası. Örneğin tüm rest clientlerinizi swagger bakarak otomatik ürettirebilirsiniz ve fazlası.

## NX nedir?

Nx ([https://github.com/nrwl/nx](https://github.com/nrwl/nx)), bir monorepo yönetim aracıdır (ancak aynı zamanda fazlasıdır). Monorepo, bir projenin tüm kodunun tek bir merkezi depoda (repository) saklandığı ve yönetildiği bir yapıdır. Nx, bu yapının yönetimini kolaylaştırır ve büyük ölçekli projelerde kod kalitesi, verimlilik ve tekrar kullanımı gibi konuları ele alır.

Nx, Angular, React, Node.js,Next (Hatta 16 ile deno) gibi teknolojiler için uygun olan bir araçtır.Angular özgü değildir ve bu teknolojiler için geliştirme yaparken kullanılan araçları bir araya getirerek, geliştirme sürecini hızlandırır. Nx, proje yapısının oluşturulması, modüllerin yönetimi, kod testlerinin yazılması ve çalıştırılması, uygulama dağıtımı gibi birçok süreci otomatikleştirir.

Ayrıca, Nx, projelerde kullanılan paketlerin ve bileşenlerin birbirleriyle nasıl etkileşimde bulunduklarını ve nasıl birbirlerine bağımlı olduklarını belirleyerek, proje bağımlılıklarının yönetimini de kolaylaştırır. Bu sayede, farklı bileşenler arasındaki bağımlılıkların yönetimi daha az hata ile gerçekleştirilir ve proje daha düzenli hale getirilir.

Kısacası, Nx, büyük ölçekli projeler için kullanışlı bir araçtır ve yazılım geliştirme sürecini hızlandırırken, kod kalitesi ve tekrar kullanımı gibi konularda da iyileştirmeler yapar. Seviniz, kullanınız, öğreniniz.

Kendi otomatik kod üreticimizi yazmadan önce nedir bu araç hızlıca açıklamak isterim. Öncelikle nx, angular schematics kullanıyor.

## Angular schematics nedir?

Angular Schematics, Angular CLI'nin bir parçası olarak kullanılan bir araçtır ve proje yapılarını ve kod dosyalarını otomatik olarak oluşturmak, düzenlemek veya değiştirmek için kullanılan bir sistemdir.

Örneğin `ng g component` bu terminal komutunu kullanmısınız. İşte bu bir schematics.  
Kodlarını incelemek isterseniz:[https://github.com/angular/angular-cli/tree/main/packages/schematics/angular/component](https://github.com/angular/angular-cli/tree/main/packages/schematics/angular/component)

## Uygulamamızı olusturalım

Örneğimize dönersek. Öncelikle nx ile bir yeni angular uygulaması oluşturuyoruz. Basit olması için \`Standalone Angular App\` kullanacagım.  
`npx create-nx-workspace@latest`

![ Basıt tutmak adına konumuz olmayan şeyleri es geçtim.](https://cdn.hashnode.com/res/hashnode/image/upload/v1683648054140/1f459ccb-35a4-4303-a0f2-4d181c186634.png align="center")

code generate üretmek de boilerplate bir iş olduğu için sağolsunlar onunda schematics'i var. projemize ekliyoruz.

`npm i @nx/plugin@latest`

Ardindan

`nx g @nx/plugin:plugin my-schematics --directory packages`

`nx generate @nx/plugin:generator my-generator --project=my-schematics`

Bu komut ile *my-schematics* isminde bir library/package olusturmus olduk. Istersek bu paket npm ile dagitabiliriz. lakin bu baska yazinin konusu olsun. Ana dizine değilde *packages* isimli bir klasore koyması için --directory ile bunu belirttik. ikinci komutumuz ise bizim için örnek generator ekledi.

Oluşturduğu örnek kod ile basit bir library ekleyip, içerisine project.json koyuyoruz.

Burada iki önemli dosyamız var.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683650466927/9f528bf8-38a9-4d59-829e-e6dae7c90e7d.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683649841940/1325b9a0-34a7-40b1-8ded-15eafd1eaffb.png align="center")

1- Logiclerimizi içeren generator.ts  
2- Methodumuza vereceğimiz parametreleri(inputları) tanımlayabileceğimiz ve schematicsimizi açıklayabileceğimiz tanım dosyas olan schema.json

Hiç bir şey yapmadan test etmek ve ne yapıyor görmek için:

`npx nx g @contoso-app/my-schematics:my-generator Odin`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683650228377/85a7f4a3-1551-4074-8a62-392ec224fc1b.png align="center")

Komutumuz da 1 ile aldığımız kısım ilk app üretirken verdiğimiz repo adı aynı zamanda namespace. ikinci kısım library olarak verdiğimiz değer. Üçüncü kısım Schematicsimizin adı. Bosluktan sonra verdiğimiz kısım ise schema.jsonda tanımlanmıs olan\`name\` variable. Aşağıdaki tanımlama ile ilk argümanın default value olarak alınmasını sağladık.

```json
 "$default": {
        "$source": "argv",
        "index": 0
      },
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683650794812/5ff1fcf9-e72f-4fc1-8369-ef9d2dd6a3ae.png align="center")

\--VariableName diyerek de değer verebilirdik.

Çıktımız.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683650494125/5bc96c45-f595-4ad8-b44a-edd287345083.png align="center")

Elbette attığımız taş ürküttüğümüz kurbağaya değmedi. Ancak amacımız bu kadarcık bir kod üretmek değil.

Bir generator daha ekleyelim.

`nx generate @nx/plugin:generator service-generator --project=my-schematics`

bu defa iki adet parametre ekleyelim. Ornegin servis adina ek olarak BaseClass da alsin. eger bos degilse o classdan extend etsin.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683652587617/7589d839-70bc-4a91-8298-27dd801aa660.png align="center")

baseClass isimli bir variable alsin, bu alan zorunlu olmasın ve girilmediğinde boş yazı olsun.

Template dosyamıza geldiğimizde ise basit bir kod bizi bekliyor.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683652665317/62e92d43-568f-4292-b9e5-e57551daeb46.png align="center")

Eğer böyle verirseniz  
`nx g @contoso-app/my-schematics:service-generator --name User`

```typescript
export class UserService {}
```

`nx g @contoso-app/my-schematics:service-generator --name User --baseClass MyBaseService`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683652762988/6d5246d6-04fe-4d90-b4d9-ef73db589719.png align="center")

Böyle generate edecektir. Göreceğiniz üzere MyBaseService import edilmemiş. Elbette otomatik olarak bunu da import edebilirdik. Onu da size ödev olarak bırakıyorum. Çok uzun bir yazı oldu. Buraya kadar geldiyseniz, tebrik ederim. Okuduğunuz için teşekkürler. Herhangi bir sorunuz olursa, her zaman bana sosyal media üzerinden ulaşabilirsiniz.

Not: Burada nx generator angular schematics kullanıyor ve onu extend edip mono repoya uygun olarak calıstırıyor. Herhangi bir text generator göre bu yapının oldukça farklı yonleri var. Örneğin MyBaseService bir paketten veya path den import edebilirdik. Create esnasında herhangi bir hata olursa mevcut dosyaları bozmuyor cünkü başarılı olana kadar bir tür sandbox da calısıyor. Bir angular dev olarak kod oluşturucuları öğrenmek size hız kazandıracak, sıkıcı işleri ve tekrar eden işleri ona yaptırabilirsiniz.

Yazının örnek kodları: [https://github.com/mahmut-gundogdu/nx-plugin-code-generator-example](https://github.com/mahmut-gundogdu/nx-plugin-code-generator-example)

Ref:  
[https://nx.dev/plugins/recipes/local-generators](https://nx.dev/plugins/recipes/local-generators)