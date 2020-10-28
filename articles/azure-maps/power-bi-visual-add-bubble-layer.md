---
title: Aggiungere un livello a bolle all'oggetto visivo Power BI mappe di Azure | Mappe Microsoft Azure
description: In questo articolo si apprenderà come usare il livello Bubble nell'oggetto visivo Microsoft Azure Maps per Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896226"
---
# <a name="add-a-bubble-layer"></a>Aggiungere un livello per le bolle

Il **livello Bubble** esegue il rendering dei dati di posizione come cerchi ridimensionati sulla mappa.

> [!div class="mx-imgBorder"]
> ![Mappa che Visualizza i dati del punto utilizzando il livello Bubble](media/power-bi-visual/bubble-layer-with-legend-color.png)

Inizialmente tutte le bolle hanno lo stesso colore di riempimento. Se un campo viene passato nel bucket **Legenda** del riquadro **campi** , le bolle verranno colorate in base alla relativa categorizzazione. Il contorno delle bolle è bianco per impostazione predefinita, ma può essere modificato in un nuovo colore o abilitando l'opzione di contorno a contrasto elevato. L'opzione del **contorno a contrasto elevato** assegna dinamicamente un colore di contorno che è una variante a contrasto elevato del colore di riempimento. Ciò consente di garantire che le bolle siano chiaramente visibili indipendentemente dallo stile della mappa. Di seguito sono riportate le impostazioni primarie nel riquadro **formato** disponibili nella sezione **livello bolla** .

| Impostazione               | Descrizione    |
|-----------------------|----------------|
| Dimensione                  | Dimensioni di ogni bolla. Questa opzione è nascosta quando un campo viene passato nel bucket **dimensioni** del riquadro **campi** . Altre opzioni verranno visualizzate come descritto nell'argomento [ridimensionamento delle dimensioni delle bolle](#bubble-size-scaling) più avanti in questo articolo. |
| Fill color            | Colore di ogni bolla. Questa opzione è nascosta quando un campo viene passato nel bucket **Legenda** del riquadro **campi** e nel riquadro **formato** verrà visualizzata una sezione **colori dati** separata. |
| Trasparenza riempimento     | Trasparenza di ogni bolla. |
| Struttura a contrasto elevato | Consente di confrontare il colore della struttura con il colore di riempimento per una migliore accessibilità utilizzando una variante a contrasto elevato del colore di riempimento. |
| Outline color         | Colore che delinea la bolla. Questa opzione è nascosta quando è abilitata l'opzione **contorno contrasto elevato** . |
| Trasparenza del contorno  | Trasparenza del contorno. |
| Larghezza del contorno         | Larghezza del contorno in pixel. |
| Sfocatura                  | Quantità di sfocatura applicata al contorno. Il valore 1 sfoca le bolle in modo che solo il punto centrale non abbia trasparenza. Il valore 0 applica qualsiasi effetto sfocatura. |
| Allineamento del passo       | Specifica il modo in cui le bolle appaiono quando la mappa è inclinata. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Viewport: le bolle vengono visualizzate sul bordo della mappa rispetto al viewport. (predefinito)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Il rendering delle bolle mappa viene eseguito in un piano sulla superficie della mappa. |
| Scalabilità zoom            | Importo in base al quale le bolle devono essere ridimensionate rispetto al livello di zoom. Una scala di zoom di uno non significa alcun ridimensionamento. Quando si esegue lo zoom avanti, i valori di grandi dimensioni ridurranno le bolle quando si ingrandisce. In questo modo è possibile ridurre il disordine sulla mappa quando si esegue lo zoom avanti, garantendo al tempo stesso la maggiore quantità di punti quando si esegue lo zoom avanti. Il valore 1 non applica alcun ridimensionamento. |
| Zoom minimo              | Sono disponibili riquadri del livello di zoom minimo. |
| Zoom max              | Sono disponibili i riquadri massimi del livello di zoom. |
| Posizione livello        | Specifica la posizione del livello rispetto ad altri livelli della mappa. |

## <a name="bubble-size-scaling"></a>Ridimensionamento dimensioni bolla

Se un campo viene passato nel bucket **dimensioni** del riquadro **campi** , le bolle verranno ridimensionate in modo relativamente al valore della misura di ogni punto dati. L'opzione **size** nella sezione **livello Bubble** del riquadro **formato** scomparirà quando viene passato un campo nel bucket **dimensioni** , in quanto le bolle avranno scalabilità orizzontale tra un valore minimo e massimo. Le opzioni seguenti verranno visualizzate nella sezione **livello bolla** del riquadro **formato** quando viene specificato un campo per un bucket **dimensioni** .

| Impostazione             | Descrizione  |
|---------------------|--------------|
| Dimensioni minime            | Dimensioni minime della bolla durante il ridimensionamento dei dati.|
| Dimensioni massime            | Dimensioni massime della bolla durante il ridimensionamento dei dati.|
| Metodo di ridimensionamento delle dimensioni | Algoritmo di ridimensionamento utilizzato per determinare le dimensioni delle bolle relative.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Intervallo lineare dei dati di input con mapping lineare alle dimensioni minima e massima. (predefinito)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Log: intervallo di dati di input logaritmicamente mappato alla dimensione minima e massima.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier: specificare i valori x1, Y1, X2, Y2 di una curva Cubic-Bezier per creare un metodo di ridimensionamento personalizzato. |

Quando il **metodo di ridimensionamento delle dimensioni** è impostato su **log** , verranno rese disponibili le opzioni seguenti.

| Impostazione   | Descrizione      |
|-----------|------------------|
| Scalabilità log | Scala logaritmica da applicare durante il calcolo delle dimensioni delle bolle. |

Quando il **metodo di ridimensionamento delle dimensioni** è impostato su **cubic-Bezier** , verranno rese disponibili le opzioni seguenti per personalizzare la curva di ridimensionamento.

| Impostazione | Descrizione                           |
|---------|---------------------------------------|
| X1      | Parametro X1 di una curva di Bezier cubica. |
| Y1      | Parametro X2 di una curva di Bezier cubica. |
| X2      | Parametro Y1 di una curva di Bezier cubica. |
| S2      | Parametro Y2 di una curva di Bezier cubica. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) dispone di uno strumento utile per creare i parametri per Cubic-Bezier curve.

## <a name="next-steps"></a>Passaggi successivi

Modificare la modalità di visualizzazione dei dati sulla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello per il grafico a barre](power-bi-visual-add-bar-chart-layer.md)

Aggiungere altro contesto alla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riferimenti](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Visualizzare il traffico in tempo reale](power-bi-visual-show-real-time-traffic.md)

Personalizzare l'oggetto visivo:

> [!div class="nextstepaction"]
> [Suggerimenti e consigli per la formattazione dei colori in Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Personalizzare i titoli, gli sfondi e le legende delle visualizzazioni](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)