---
title: "React native de Expo Router ile Dosya bazli routing ve Auth flow"
datePublished: Tue May 23 2023 12:32:29 GMT+0000 (Coordinated Universal Time)
cuid: cli09atae000m0al5c8qeez1s
slug: react-native-de-expo-router-ile-dosya-bazli-routing-ve-auth-flow
tags: react-native, expo, turkce, expo-router

---

Merhaba bugun expo + expo router ile RN de Dosya bazli routing nasil yaptigimizi anlatacagim. Oncelikle filebased routing nextjs ile belkide sikca adini duydugumuz bir kavram.

Ornek uygulamizi olusturuyoruz.

```bash
npx create-expo-app my-app
```

expo-router her ne kadar tek paket gibi gorunse de alsinda bunlar aşiret. Hemen bu dostlarimizi projemize cagiriyoruz.

```bash
 npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar react-native-gesture-handler
```

Route islemini expo-router yapabilmesi icin app bootstrap etme gorevini da ona veriyoruz. `package.json` gelip index.js ile degistiryoruz.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1684838189495/78678c0d-34c8-4d31-a994-2f0e69d7ff19.png align="center")

Tabi bir adet projemize index.js ekliyoruz.

```javascript
// index.js
import "expo-router/entry";
```

app.json dosyamiza geliyoruz ve metro+metro-resolver surumunu sabitleyip bir deeplink sema adi veriyoruz.

```json
//app.json
{
 "scheme": "myapp",
    "overrides": {
      "metro": "0.76.0",
      "metro-resolver": "0.76.0"
    },
}
```

Guzide dostumuz babel'a da expo-router pluginimizi verip. Kurulum islemlerimizi bitiyoruz.

```javascript
//babel.config.js
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ["babel-preset-expo"],
    plugins: [require.resolve("expo-router/babel")],
  };
};
```

Router uygulamak icin haziriz.

React Native Router gibi Stack koyup definition yapmamiza gerek yok expo-router bunu bizim adimiza yapacak. O yuzden biz 3 tane dosya olusturuyoruz.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1684841267750/8ef741ff-ee4d-4db5-96b0-36aa5f455251.png align="center")

1- /app/index.js bu bizim uygulamamizin giris noktasi. Home page gibi dusunebilirsiniz.

2- /app/\_layout.js NextJs ve remixjs den asina oldugumuz expo-router kullanma sebebimiz olan yapi dosya bazli routing. Burada \_layout Bizim uygulamamizin ekranlarinda kendini tekrar eden kabuk kismini tuttugumuz kisim.

3- /app/(auth)/login.js ise bizim login ekranimiz. Expo-router da `/app/login/index.js` , `/app/login.js` ve `/app/(herhangiBirSey)/login.js` aslinda ayni sayfayi ifade ediyor yani `/login` bu konu hk elbette dokumanda bolca bilgi mevcut ben burada bunun yapilabildigini ve gruplama amaci ile oldugunu bahsedip ilerleyim.

Simdi bir otomatik state bazli gecis yerine, en ilkel yapi olan butona basayim ve login sayfasina gitsin olarak kodumu kurguladim.

```javascript
export default function App() {
  const router = useRouter();
  const handleClick = () => router.push("/login");

// ya da       <Link href="/login">Go to Login Page</Link>

  return (
    <>
      <Text>Open up App.js to start working on your app!</Text>
      <Button onPress={handleClick} title="Go to Login Page" />
    </>
  );
}
```

useRouter expo-router'dan gelen bir hook. onun push veya replace fonksiyonlarini kullanarak navige edebiliyoruz. Elbette Link de kullanabiliriz. Bunun calistigini test ettikten sonra bir state bazli olarak bu islemi yapmak uzere devam ediyoruz.

Oncelikle elbette State ihtiyacimiz var (hata persist bir state ihtiyacimiz var.)  
bunun icin bir context olusturuyorum.

```javascript
// auth-context.js
import React from "react";
import {useRouteGuard} from "./auth-guard-hook";

const AuthContext = React.createContext(null);

export function useAuth() {
  return React.useContext(AuthContext);
}

export function AuthProvider(props) {
  const [user, setAuth] = React.useState(null);

  // burasi cokomelli
  useRouteGuard(user);

  return (
    <AuthContext.Provider
      value={{
        signIn: () => setAuth({}),
        signOut: () => setAuth(null),
        user,
      }}
    >
      {props.children}
    </AuthContext.Provider>
  );
}
```

olusturdugumuz provide Layout icerisinde koyuyoruz. Boylelikle tum ekranlar Auth state ulasabilecek ve `useRouteGuard` isimli hook da bizim authentication logicimizi iceriyor.

Aslinda flow basit. Kullanici bilgisi varsa anasayfaya gonder, yoksa login sayfasina ilet.

```javascript

export function useRouteGuard(user) {
    const segments = useSegments();
    const router = useRouter();
    const redirect = (path) => router.replace(path);

    React.useEffect(() => {
      const groupName = segments[0];
      const inAuthGroup = groupName === "(auth)";
  
      if(!user && !inAuthGroup) {
        redirect("/login")
      }
      if(user && inAuthGroup) {
        redirect("/")
      }
    }, [user, segments]);
  }
```

effect sagolsun, segments (yani route degistikce) ve user degistikce fonksiyonumuz alisiyor ve login veya anasayfaya yonlendiriyor.

```javascript
// _layout.js
export function Layout() {
  return (
    <>
      <AuthProvider>
        <StatusBar style="auto" />
        <View style={styles.container}>
          <Slot></Slot>
        </View>
      </AuthProvider>
    </>
  );
}
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1684844843412/0ac11bed-c458-422b-851c-c4e9c4c0146b.gif align="center")

Umarim aciklayici olabilmistir. Herhangi bir sorunuz olursa lutfen bana ulasmaktan cekinmeyin. Happy coding.

Uygulamanin tum kodlari.  
[https://github.com/mahmut-gundogdu/expo-router-example-app](https://github.com/mahmut-gundogdu/expo-router-example-app)