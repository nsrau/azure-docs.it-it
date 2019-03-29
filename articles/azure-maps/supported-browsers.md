---
title: Web SDK supportato browser - mappe di Azure | Microsoft Docs
description: Informazioni sui browser supportati per Azure SDK per Web mappe
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577340"
---
# <a name="web-sdk-supported-browsers"></a>Web browser supportato dal SDK

Mappe di Azure SDK per Web include una funzione helper [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) per rilevare se un web browser è il requisito minimo necessario WebGL caratteristiche per supportare il caricamento e il rendering del controllo mappa. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

Mappe di Azure SDK per Web supporta i seguenti browser desktop.

- La versione corrente e quella precedente di Microsoft Edge 
- La versione corrente e quella precedente di Chrome 
- La versione corrente e quella precedente di Firefox 
- La versione corrente e quella precedente di Safari (Mac OS X) 

Vedere anche [browser legacy come destinazione](#Target-Legacy-Browsers).

## <a name="mobile"></a>Cellulare

Mappe di Azure SDK per Web supporta i seguenti browser per dispositivi mobili.

-  Android
    * Versione corrente di Chrome in Android 6.0 +
    * Chrome WebView in Android 6.0 +
- iOS
    * Safari per dispositivi mobili per la versione principale corrente e quella precedente di iOS
    * UIWebView e WKWebView nella versione principale corrente e quella precedente di iOS
    * Versione corrente di Chrome per iOS

> [!TIP]
> Se si intende incorporare una mappa all'interno di un'applicazione per dispositivi mobili usando un controllo WebView, si potrebbe essere preferibile usando il [pacchetto npm di mappe di Azure SDK per Web](https://www.npmjs.com/package/azure-maps-control) invece riferimento alla versione ospitata della rete CDN di SDK. Ciò riduce il tempo di caricamento come il SDK sarà essere già presente nel dispositivo dell'utente e non devono essere scaricati in fase di esecuzione.

## <a name="nodejs"></a>Node.js

I moduli Web SDK seguenti sono supportati anche in Node. js:

- Modulo Servizi ([documentazione](how-to-use-services-module.md) | [modulo npm di](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Browser legacy di destinazione

Se è necessario usare browser meno recenti che potrebbero non supportare o hanno un supporto limitato per WebGL, è consigliabile usare i servizi di mappe di Azure in combinazione con un controllo mappa open source, ad esempio [illustrativo](https://leafletjs.com/). 


<iframe height="500" style="width: 100%;" scrolling="no" title="Mappe di Azure + illustrativo" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>mappe di Azure + illustrativo</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle mappe di Azure SDK per Web.

> [!div class="nextstepaction"]
> [Controllo mappa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Modulo Servizi](how-to-use-services-module.md)
