---
title: Browser supportati da Web SDK | Mappe Microsoft Azure
description: Informazioni su come verificare se Azure Maps Web SDK supporta un browser. Visualizzare un elenco dei browser supportati. Informazioni su come usare i servizi map con i browser legacy.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 43bf70d66c42bc0ecd5e26e4cc724456bd4bf84e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896923"
---
# <a name="web-sdk-supported-browsers"></a>Browser supportati dall'SDK Web

Azure Maps Web SDK fornisce una funzione helper denominata [Atlas. è supportata](/javascript/api/azure-maps-control/atlas#issupported-boolean-). Questa funzione rileva se un Web browser dispone del set minimo di funzionalità WebGL necessarie per supportare il caricamento e il rendering del controllo mappa. Ecco un esempio di come usare la funzione:

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

Azure Maps Web SDK supporta i seguenti browser desktop:

- Microsoft Edge (versione corrente e precedente)
- Google Chrome (versione corrente e precedente)
- Mozilla Firefox (versione corrente e precedente)
- Apple Safari (Mac OS X) (versione corrente e precedente)

Vedere anche [browser legacy di destinazione](#Target-Legacy-Browsers) più avanti in questo articolo.

## <a name="mobile"></a>Mobile

Azure Maps Web SDK supporta i seguenti browser per dispositivi mobili:

- Android
  - Versione corrente di Chrome in Android 6,0 e versioni successive
  - WebView Chrome in Android 6,0 e versioni successive
- iOS
  - Mobile Safari nella versione principale corrente e precedente di iOS
  - UIWebView e WKWebView nella versione principale corrente e precedente di iOS
  - Versione corrente di Chrome per iOS

> [!TIP]
> Se si incorpora una mappa all'interno di un'applicazione per dispositivi mobili usando un controllo WebView, potrebbe essere preferibile usare il [pacchetto NPM di Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) anziché fare riferimento alla versione dell'SDK ospitata nella rete per la distribuzione di contenuti di Azure. Questo approccio riduce il tempo di caricamento perché l'SDK è già presente nel dispositivo dell'utente e non è necessario scaricarlo in fase di esecuzione.

## <a name="nodejs"></a>Node.js

In Node.js sono supportati anche i moduli Web SDK seguenti:

- Modulo dei servizi[documentation](how-to-use-services-module.md)(  |  [modulo NPM](https://www.npmjs.com/package/azure-maps-rest)della documentazione)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Browser legacy di destinazione

È possibile scegliere come destinazione browser meno recenti che non supportano WebGL o con supporto limitato. In questi casi, è consigliabile usare i servizi di Azure Maps insieme a un controllo mappa open source, ad esempio un [opuscolo](https://leafletjs.com/). Ecco un esempio:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappe di Azure + volantino" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>volantino sulle mappe di Azure e il volantino</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Scopri di più su Azure Maps Web SDK:

[Controllo mappa](how-to-use-map-control.md)

[Modulo Servizi](how-to-use-services-module.md)