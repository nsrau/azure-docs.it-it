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
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686641"
---
# <a name="web-sdk-supported-browsers"></a>Browser supportati dall'SDK Web

Mappe di Azure SDK per Web include una funzione helper denominata [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Questa funzione rileva se un browser web ha il set minimo di funzionalità WebGL necessari per supportare il caricamento e il rendering del controllo mappa. Ecco un esempio di come usare la funzione:

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

Mappe di Azure SDK per Web supporta i seguenti browser desktop:

- Microsoft Edge (versione corrente e quella precedente)
- Google Chrome (versioni correnti e precedenti)
- Mozilla Firefox (versione corrente e quella precedente)
- Apple Safari (Mac OS X) (versione corrente e quella precedente)

Vedere anche [browser legacy come destinazione](#Target-Legacy-Browsers) più avanti in questo articolo.

## <a name="mobile"></a>Mobile

Mappe di Azure SDK per Web supporta i browser per dispositivi mobili seguenti:

- Android
  - Versione corrente di Chrome in Android 6.0 e versioni successive
  - Chrome WebView in Android 6.0 e versioni successive
- iOS
  - Safari per dispositivi mobili per la versione principale corrente e quella precedente di iOS
  - UIWebView e WKWebView nella versione principale corrente e quella precedente di iOS
  - Versione corrente di Chrome per iOS

> [!TIP]
> Se si incorpora una mappa all'interno di un'applicazione per dispositivi mobili usando un controllo WebView, è preferibile usare il [pacchetto npm di mappe di Azure SDK per Web](https://www.npmjs.com/package/azure-maps-control) invece riferimento alla versione del SDK che è ospitato nella distribuzione di contenuti di Azure Rete. Questo approccio riduce i tempi di caricamento perché il SDK è essere già presente nel dispositivo dell'utente e non desidera essere scaricati in fase di esecuzione.

## <a name="nodejs"></a>Node.js

I moduli Web SDK seguenti sono supportati anche in Node. js:

- Modulo Servizi ([documentazione](how-to-use-services-module.md) | [modulo npm di](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Browser legacy di destinazione

Si potrebbe voler browser meno recenti che non supportano WebGL o che hanno solo un supporto limitato per di destinazione. In questi casi, è consigliabile usare servizi di mappe di Azure insieme a un controllo mappa open source, ad esempio [illustrativo](https://leafletjs.com/). Ad esempio:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappe di Azure + illustrativo" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>mappe di Azure + illustrativo</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle mappe di Azure SDK per Web:

> [!div class="nextstepaction"]
> [Controllo mappa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Modulo Servizi](how-to-use-services-module.md)
