---
title: Informazioni sui livelli nelle mappe di Azure Power BI Visual | Mappe Microsoft Azure
description: In questo articolo vengono illustrati i diversi livelli disponibili nell'oggetto visivo Microsoft Azure Maps per Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261845"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Informazioni sui livelli nell'oggetto visivo Power BI mappe di Azure

Nell'oggetto visivo Maps di Azure sono disponibili due tipi di livelli. Il primo tipo è incentrato sui dati di rendering passati nel riquadro **campi** dell'oggetto visivo ed è costituito dai livelli seguenti, che chiameremo i livelli di rendering dei dati.

:::row:::
    :::column span="":::
        **Livello bolle**

        Esegue il rendering di punti come cerchi ridimensionati sulla mappa.

        ![Livello bolla sulla mappa](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Livello grafico a barre**

        Esegue il rendering di punti come barre 3D sulla mappa.
        
        ![Livello grafico a barre sulla mappa](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

Il secondo tipo di livello connette le origini di dati esterne di addizione alla mappa per fornire un contesto più contenuto ed è costituito dai livelli seguenti.

:::row:::
    :::column span="":::
        **Livello di riferimento**

        Sovrapporre un file GeoJSON caricato nella parte superiore della mappa.

        ![Livello di riferimento sulla mappa](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Livello sezione**

        Sovrapporre un livello affiancato personalizzato all'inizio della mappa.
        
        ![Livello sezione sulla mappa](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Livello di traffico**

        Sovrapporre le informazioni sul traffico in tempo reale sulla mappa.
        
        ![Livello di traffico sulla mappa](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Tutti i livelli di rendering dei dati, nonché il **livello sezione**, includono opzioni per i livelli di zoom min e Max usati per specificare un intervallo di livelli di zoom in cui devono essere visualizzati questi livelli. Questo consente di usare un tipo di livello di rendering a un livello di zoom e una transizione a un altro livello di rendering a un altro livello di zoom.

Questi livelli hanno anche un'opzione da posizionare rispetto ad altri livelli della mappa. Quando vengono usati più livelli di rendering dei dati, l'ordine in cui vengono aggiunti alla mappa determina il relativo ordine di sovrapposizione quando hanno lo stesso valore di **posizione del livello** .

## <a name="general-layer-settings"></a>Impostazioni livello generale

La sezione livello generale del riquadro **formato** sono impostazioni comuni che si applicano ai livelli connessi al set di dati Power bi nel riquadro **campi** (livello Bubble, grafico a barre).

| Impostazione     | Descrizione   |
|-------------|---------------|
| Trasparenza non selezionata | Trasparenza delle forme non selezionate, quando vengono selezionate una o più forme.  |
| Mostra zeri              | Specifica se i punti che hanno un valore di dimensioni pari a zero devono essere visualizzati sulla mappa utilizzando il raggio minimo. |
| Mostra negativi          | Specifica se è necessario tracciare il valore assoluto dei valori delle dimensioni negative.   |
| Valore minimo dati          | Valore minimo dei dati di input su cui applicare la scalabilità. Adatto per ritagliare gli outlier.  |
| Valore massimo dati          | Valore massimo dei dati di input su cui applicare la scalabilità. Adatto per ritagliare gli outlier.  |

## <a name="next-steps"></a>Passaggi successivi

Modificare la modalità di visualizzazione dei dati sulla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello grafico a barre](power-bi-visual-add-bar-chart-layer.md)

Aggiungere altro contesto alla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello di riferimento](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostra traffico in tempo reale](power-bi-visual-show-real-time-traffic.md)
