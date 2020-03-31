---
title: Browser supportati da Web SDK Mappe di Microsoft Azure
description: In questo articolo verranno fornite informazioni sui browser supportati per Microsoft Azure Maps Web SDK e su come verificare se un browser è un browser supportato.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988788"
---
# <a name="web-sdk-supported-browsers"></a>Browser supportati dall'SDK Web

Azure Maps Web SDK fornisce una funzione di supporto denominata [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Questa funzione rileva se un browser Web dispone del set minimo di funzionalità WebGL necessarie per supportare il caricamento e il rendering del controllo mappa. Ecco un esempio di come usare la funzione:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

Il Web SDK di Azure Maps supporta i browser desktop seguenti:

- Microsoft Edge (versione corrente e precedente)
- Google Chrome (versione corrente e precedente)
- Mozilla Firefox (versione corrente e precedente)
- Apple Safari (Mac OS X) (versione corrente e precedente)

Vedere anche [Target dei browser legacy](#Target-Legacy-Browsers) più avanti in questo articolo.

## <a name="mobile"></a>Cellulare

Il Web SDK di Azure Maps supporta i browser per dispositivi mobili seguenti:

- Android
  - Versione corrente di Chrome su Android 6.0 e versioni successive
  - Chrome WebView su Android 6.0 e versioni successive
- iOS
  - Safari per dispositivi mobili sulla versione principale corrente e precedente di iOS
  - UIWebView e WKWebView sulla versione principale corrente e precedente di iOS
  - Versione corrente di Chrome per iOS

> [!TIP]
> Se si incorpora una mappa all'interno di un'applicazione per dispositivi mobili usando un controllo WebView, è preferibile usare il [pacchetto npm di Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) anziché fare riferimento alla versione dell'SDK ospitata nella rete per la distribuzione di contenuti di Azure. Questo approccio riduce il tempo di caricamento perché l'SDK è già nel dispositivo dell'utente e non deve essere scaricato in fase di esecuzione.

## <a name="nodejs"></a>Node.js

I seguenti moduli Web SDK sono supportati anche in Node.js:

- Modulo Servizi[(documentation](how-to-use-services-module.md) | [npm module](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Browser legacy di destinazione

Potresti voler scegliere come target i browser meno recenti che non supportano WebGL o che hanno solo un supporto limitato per esso. In questi casi, è consigliabile usare i servizi di Azure Maps con un controllo mappa open source come [Leaflet](https://leafletjs.com/). Ad esempio:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappe di Azure - Opuscolo" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il foglio illustrativo Pen Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps - <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Leaflet</a> by Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Controllo mappa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Modulo Servizi](how-to-use-services-module.md)
