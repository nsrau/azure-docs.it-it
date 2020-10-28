---
title: Mostrare il traffico su una mappa | Mappe di Microsoft Azure
description: Informazioni su come aggiungere i dati di traffico a maps. Informazioni sui dati del flusso e su come usare Azure Maps Web SDK per aggiungere dati degli eventi imprevisti e dati di flusso a maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: ec7459a356221fb7b599cbbc02f1cb825920b5b3
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890667"
---
# <a name="show-traffic-on-the-map"></a>Mostrare il traffico sulla mappa

In Mappe di Azure sono disponibili due tipi di dati di traffico:

- Dati di eventi: dati basati su linee e punti per segnalare cantieri, chiusure stradali e incidenti.
- Flussi di dati: metriche sul flusso di traffico sulle strade. I dati relativi al flusso di traffico vengono spesso usati per colorare le strade. I colori dipendono dalla misura in cui il traffico sta rallentando il flusso stradale, rispetto al limite di velocità o a un'altra metrica. I dati relativi al flusso di traffico in Mappe di Azure si basano su tre diverse metriche di misurazione:
    - `relative` - misura relativa alla velocità in condizioni di traffico libero sulla strada.
    - `absolute` - velocità assoluta di tutti i veicoli sulla strada.
    - `relative-delay` - aree più lente rispetto al ritardo medio previsto.

Il codice seguente illustra come visualizzare i dati relativi al traffico sulla mappa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Mostrare il traffico su una mappa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Visualizzare il traffico su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opzioni di sovrapposizione del traffico

Lo strumento seguente consente di alternare tra varie impostazioni di sovrapposizione del traffico per vedere come cambia il rendering. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni di sovrapposizione del traffico" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere le <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Opzioni di sovrapposizione del traffico</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Aggiungere controlli di traffico

Sono disponibili due diversi controlli di traffico che è possibile aggiungere alla mappa. Il primo controllo, `TrafficControl` , aggiunge un interruttore che può essere usato per attivare e disattivare il traffico. Le opzioni per questo controllo consentono di specificare quando usare le impostazioni del traffico quando viene visualizzato il traffico. Per impostazione predefinita, questo controllo visualizzerà il flusso di traffico relativo e i dati degli eventi imprevisti. Tuttavia, è possibile modificarlo per visualizzare il flusso di traffico assoluto e nessun evento imprevisto, se necessario. Il secondo controllo, `TrafficLegendControl` , aggiunge una legenda del flusso del traffico alla mappa che consente agli utenti di comprendere il significato della strada del codice colori. Questo controllo verrà visualizzato sulla mappa solo quando i dati del flusso di traffico vengono visualizzati sulla mappa e verranno nascosti in tutti gli altri momenti.

Il codice seguente mostra come aggiungere i controlli di traffico alla mappa.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Controlli di traffico" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere i <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>controlli del traffico</a> di penna da Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

Migliorare le esperienze utente:

> [!div class="nextstepaction"]
> [Interazione della mappa con gli eventi del mouse](map-events.md)

> [!div class="nextstepaction"]
> [Compilazione di una mappa accessibile](map-accessibility.md)

> [!div class="nextstepaction"]
> [Pagina dell'esempio di codice](https://aka.ms/AzureMapsSamples)