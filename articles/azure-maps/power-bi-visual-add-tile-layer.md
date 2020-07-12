---
title: Aggiungere un livello sezione all'oggetto visivo Power BI mappe di Azure | Mappe Microsoft Azure
description: In questo articolo si apprenderà come usare il livello sezione nell'oggetto visivo Microsoft Azure Maps per Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8a0d930d1c0fd30c48d97b0d1d4b94548077fbca
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261942"
---
# <a name="add-a-tile-layer"></a>Aggiungere un livello per i riquadri

La funzionalità del livello sezione, come la funzionalità del livello di riferimento, consente di sovrapporre dati aggiuntivi sulla mappa per fornire un contesto maggiore. I livelli di sezione consentono di sovrapporre immagini sopra le tessere mappa di base di Azure maps. Questo è un ottimo modo per sovrapporre set di dati di grandi dimensioni o complessi, ad esempio immagini da droni o milioni di righe di dati.

> [!div class="mx-imgBorder"]
> ![Mappa che visualizza un livello di bolla sopra un livello sezione che mostra i dati meteo infrarossi correnti delle mappe di Azure](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Un livello sezione viene caricato in riquadri da un server. Queste immagini possono essere pre-renderizzate o sottoposte a rendering dinamico. Le immagini con rendering preliminare vengono archiviate come qualsiasi altra immagine in un server utilizzando una convenzione di denominazione riconosciuta dal livello sezione. Le immagini sottoposte a rendering dinamico usano un servizio per caricare le immagini quasi in tempo reale. I livelli di sezione sono un ottimo modo per visualizzare set di impostazioni di grandi dimensioni sulla mappa. Non solo è possibile generare un livello sezione da un'immagine. anche i dati vettoriali possono essere visualizzati come livello sezione.

Il rettangolo di selezione e l'intervallo di zoom di in cui è disponibile un servizio affiancato possono essere passati come impostazioni per limitare la posizione in cui vengono richiesti i riquadri. Si tratta di un miglioramento delle prestazioni sia per l'oggetto visivo che per il servizio affiancato. Di seguito è riportata una panoramica di tutte le impostazioni disponibili nel riquadro **formato** disponibili nella sezione **livello** sezione.

| Impostazione        | Descrizione   |
|----------------|---------------|
| URL            | URL formattato che punta a un servizio affiancato.  |
| Dimensioni del riquadro      | Valore intero che specifica le dimensioni di larghezza e altezza dei riquadri.   |
| Con associazione a nord    | Latitudine nordica del rettangolo di delimitazione in cui sono disponibili i riquadri. |
| Limite sud    | Latitudine meridionale del rettangolo di delimitazione in cui sono disponibili i riquadri. |
| Limite est     | Longitudine orientale del rettangolo di delimitazione in cui sono disponibili i riquadri.  |
| Limite ovest     | Longitudine occidentale del rettangolo di delimitazione in cui sono disponibili i riquadri.   |
| Trasparenza   | Trasparenza del livello sezione.   |
| TMS         | Affianca i servizi di mapping, una specifica che inverte l'asse delle coordinate Y del livello sezione. |
| Zoom minimo       | Sono disponibili riquadri del livello di zoom minimo. |
| Zoom max       | Sono disponibili i riquadri massimi del livello di zoom.  |
| Posizione livello | Specifica la posizione del livello rispetto ad altri livelli della mappa. |

## <a name="tile-url-formatting"></a>Formattazione URL riquadro

Sono disponibili tre diverse convenzioni di denominazione del servizio affiancate dall'oggetto visivo Maps di Azure:

-   **X, y, notazione zoom** -x è la colonna, y è la posizione della riga del riquadro nella griglia affiancata e la notazione di zoom un valore in base al livello di zoom.
-   **Notazione quadkey** : combina le informazioni x, y e zoom in un unico valore stringa. Questo valore stringa diventa un identificatore univoco per un singolo riquadro.
-   Rettangolo di **delimitazione** : specificare un'immagine nel formato delle coordinate del rettangolo di delimitazione: `{west},{south},{east},{north}` . Questo formato viene comunemente usato da [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

URL del riquadro un URL HTTPS a un modello di URL riquadro che usa i parametri seguenti:

-   `{x}` - posizione X del riquadro. Necessita inoltre di `{y}` e `{z}`.
-   `{y}` - posizione Y del riquadro. Necessita inoltre di `{x}` e `{z}`.
-   `{z}` - Livello di zoom del riquadro. Necessita inoltre di `{x}` e `{y}`.
-   `{quadkey}`- `quadkey` Identificatore del riquadro basato sulla convenzione di denominazione del sistema dei riquadri di Bing Maps.
-   `{bbox-epsg-3857}`-Una stringa del rettangolo di delimitazione con il formato `{west},{south},{east},{north}` nel sistema di riferimento spaziale EPSG 3857.

Ad esempio, di seguito è riportato un URL di riquadro formattato per il [servizio del riquadro del radar meteorologico](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) in mappe di Azure. Si noti che `[subscription-key]` è un segnaposto per la chiave di sottoscrizione di Azure maps.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Per altre informazioni sul sistema di affiancamento delle mappe di Azure, vedere [livelli di zoom e griglia dei riquadri](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Passaggi successivi

Aggiungere altro contesto alla mappa:

> [!div class="nextstepaction"]
> [Mostra traffico in tempo reale](power-bi-visual-show-real-time-traffic.md)
