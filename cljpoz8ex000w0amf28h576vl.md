---
title: "How to get internet status in Angular App"
datePublished: Wed Jul 05 2023 12:25:19 GMT+0000 (Coordinated Universal Time)
cuid: cljpoz8ex000w0amf28h576vl
slug: how-to-get-internet-status-in-angular-app

---

Web applications need to be aware of the user's internet connectivity. By knowing whether the user is online or offline, you can provide a better user experience and handle network-related tasks accordingly. In this article, we'll explore how to get the internet status in an Angular app using a service file.

```javascript
import { Injectable } from '@angular/core';
import { of, fromEvent, merge, map } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class NetworkStatusService {
  status$ = merge(
    of(navigator.onLine),
    fromEvent(window, 'online').pipe(map(() => true)),
    fromEvent(window, 'offline').pipe(map(() => false))
  );
}
```

The `NetworkStatusService` class contains a `status$` property that is an Observable representing the internet status. It combines three sources of information to determine the status:

1. `of(`[`navigator.onLine`](http://navigator.onLine)`)`: This creates an Observable that emits the current online status of the browser. It will emit `true` if the browser is online and `false` if it's offline.
    
2. `fromEvent(window, 'online')`: This creates an Observable that listens for the `online` event on the `window` object. When the browser goes online, it emits `true`.
    
3. `fromEvent(window, 'offline')`: Similarly, this creates an Observable that listens for the `offline` event on the `window` object. When the browser goes offline, it emits `false`.
    

By merging these three sources, the `status$` Observable will emit the current internet status and update whenever the status changes.

the Example app:  
[https://stackblitz.com/edit/stackblitz-starters-thscwg?file=src%2Fnetwork-status.service.ts](https://stackblitz.com/edit/stackblitz-starters-thscwg?file=src%2Fnetwork-status.service.ts)