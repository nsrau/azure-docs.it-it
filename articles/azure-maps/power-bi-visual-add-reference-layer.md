---
title: Aggiungere un livello di riferimento all'oggetto visivo Power BI mappe di Azure | Mappe Microsoft Azure
description: In questo articolo si apprenderà come usare il livello di riferimento nell'oggetto visivo Microsoft Azure Maps per Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261945"
---
# <a name="add-a-reference-layer"></a>Aggiungere un livello di riferimento

La funzionalità del livello di riferimento consente il caricamento di un set di dati spaziali secondario nell'oggetto visivo e la sovrapposizione della mappa per fornire il contesto di aggiunta. Questo set di dati è ospitato da Power BI e deve essere un [file GeoJSON](https://wikipedia.org/wiki/GeoJSON) con `.json` estensione di `.geojson` file o.

Per aggiungere un file **GeoJSON** come livello di riferimento, passare al riquadro **formato** , espandere la sezione **livello di riferimento** e premere il pulsante **+ Aggiungi file locale** .

Dopo l'aggiunta di un file GeoJSON al livello di riferimento, il nome del file verrà visualizzato al posto del pulsante **+ Aggiungi file locale** con una **X** accanto. Premere il pulsante **X** per rimuovere i dati dall'oggetto visivo ed eliminare il file GeoJSON dal Power bi.

La mappa seguente mostra [2016 tratti di censimento per Colorado](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), colorati per popolazione.

> [!div class="mx-imgBorder"]
> ![Mappa che visualizza 2016 censimento per Colorado, colorato in base alla popolazione come livello di riferimento](media/power-bi-visual/reference-layer-CO-census-tract.png)

Di seguito sono riportate tutte le impostazioni del riquadro **formato** disponibili nella sezione **livello di riferimento** .

| Impostazione              | Descrizione   |
|----------------------|---------------|
| Dati del livello di riferimento | File GeoJSON dei dati da caricare nell'oggetto visivo come livello aggiuntivo all'interno della mappa. Il pulsante **+ Aggiungi file locale** apre una finestra di dialogo file che l'utente può usare per selezionare un file GeoJSON `.json` con `.geojson` estensione o. |

> [!NOTE]
> In questa anteprima dell'oggetto visivo Maps di Azure, il livello di riferimento caricherà solo le prime funzionalità della forma 5.000 nella mappa. Questo limite verrà aumentato in un aggiornamento futuro.

## <a name="styling-data-in-a-reference-layer"></a>Applicazione di stili ai dati in un livello di riferimento

È possibile aggiungere proprietà a ogni funzionalità all'interno del file GeoJSON per personalizzare il modo in cui viene applicato lo stile sulla mappa. Questa funzionalità Usa la funzionalità Simple Data Layer in Azure Maps Web SDK. Per ulteriori informazioni, vedere questo documento sulle [proprietà dello stile supportate](spatial-io-add-simple-data-layer.md#default-supported-style-properties). Le immagini dell'icona personalizzata non sono supportate nell'oggetto visivo Maps di Azure come precauzione di sicurezza.

Di seguito è riportato un esempio di una funzionalità di punto GeoJSON che imposta il colore visualizzato su rosso.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Passaggi successivi

Aggiungere altro contesto alla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostra traffico in tempo reale](power-bi-visual-show-real-time-traffic.md)
