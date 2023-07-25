---
title: "Expo router v2  "Attempted to navigate before mounting the Root Layout component. Ensure the Root Layout component is rendering a Slot"  hatası çözümü"
datePublished: Tue Jul 25 2023 20:47:04 GMT+0000 (Coordinated Universal Time)
cuid: clkirpimr00050alh8c47eqiz
slug: expo-router-v2-attempted-to-navigate-before-mounting-the-root-layout-component-ensure-the-root-layout-component-is-rendering-a-slot-hatasi-cozumu
tags: expo, expo-router

---

Expo 49, Expo router v2 kullandıgımızda \_layout.js içerisinde oturum kontrolü yapıp, session yoksa login ekranını açmak gibi bir standart kodum var.  
Expo'nun kendi dökümanında\* örnek auth flow kodu mevcut. Ancak çalışmıyor \*[https://docs.expo.dev/router/reference/authentication/](https://docs.expo.dev/router/reference/authentication/)

ERROR Error: Attempted to navigate before mounting the Root Layout component. Ensure the Root Layout component is rendering a Slot, or other navigator on the first render.

hatası fırlatıyor. Aslında kendini açıklayan bir hata. Daha navigator hazır değil iken redirect etmeye çalışıyorsun diyor.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690317666136/a84f1e29-61a2-4351-9d2e-c5b8da529725.png align="center")

Çözüm basit: Redirect etmek için navigator beklemek.

```typescript
  const navigationState = useRootNavigationState();

  React.useEffect(() => {
    if (!navigationState?.key) return;
//...
    redirect('/what do you want');
    
  }, [user, segments,navigationState]);
}
```

Örnek dökümanı da güncelleyip, PR gönderdim. Isterseniz oradan da bakabilirsiniz.  
[https://github.com/expo/expo/pull/23722/files](https://github.com/expo/expo/pull/23722/files)  
  
SetTimeout ile de bir çözüm önerilmiş ancak bu bana biraz hacky geldi.

```typescript
   if (Platform.OS === "ios") {
        setTimeout(() => {
          router.replace("/");
        }, 1)
      } else {
        setImmediate(() => {
          router.replace("/");
        });
      }
```

ilgili issue

[https://github.com/expo/router/issues/740](https://github.com/expo/router/issues/740)