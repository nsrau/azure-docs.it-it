---
title: Aggiungere un livello grafico a barre all'oggetto visivo Power BI mappe di Azure | Mappe Microsoft Azure
description: In questo articolo si apprenderà come usare il livello grafico a barre nell'oggetto visivo mappa Microsoft Azure per Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6a686f6915e332893f619e66944591999063a07c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288167"
---
# <a name="add-a-bar-chart-layer"></a>Aggiungere un livello per il grafico a barre

Il **livello grafico a barre** è utile per l'acquisizione dei dati nella dimensione successiva, consentendo la visualizzazione dei dati della posizione come barre o cilindri 3D sulla mappa. Analogamente al livello Bubble, il grafico a barre in un secondo momento può visualizzare facilmente due metriche contemporaneamente usando il colore e l'altezza relativa. Affinché le barre abbiano altezza, è necessario aggiungere una misura al bucket **dimensioni** del riquadro **campi** . Se non viene specificata alcuna misura, le barre senza altezza vengono visualizzate come quadrati o cerchi, a seconda dell'opzione della **forma a barre** .

> [!div class="mx-imgBorder"]
> ![Mappa che Visualizza i dati punto utilizzando il livello grafico A barre](media/power-bi-visual/bar-chart-layer-styled.png)

Gli utenti possono inclinare e ruotare la mappa per visualizzare i dati da prospettive diverse. La mappa può essere inclinata o sottoposto a pitch usando uno dei metodi seguenti.

-   Attivare l'opzione **controlli di spostamento** nelle **impostazioni della mappa** del riquadro **formato** . Verrà aggiunto un pulsante per inclinare la mappa.
-   Premere il pulsante destro del mouse e trascinare il mouse verso l'alto o verso il basso.
-   Con il touchscreen, toccare la mappa con due dita e trascinarle insieme verso l'alto o verso il basso.
-   Con lo stato attivo della mappa, tenere premuto il tasto **MAIUSC** e premere i tasti freccia **su** o **giù** .

È possibile ruotare la mappa utilizzando uno dei metodi seguenti.

-   Attivare l'opzione **controlli di spostamento** nelle **impostazioni della mappa** del riquadro **formato** . Verrà aggiunto un pulsante per ruotare la mappa.
-   Premere il pulsante destro del mouse e trascinare il mouse verso sinistra o verso destra.
-   Con un touchscreen, toccare la mappa con due dita e ruotare.
-   Con lo stato attivo della mappa, tenere premuto il tasto **MAIUSC** e premere i tasti freccia **sinistra** o **destra** .

Di seguito sono riportate tutte le impostazioni del riquadro **formato** disponibili nella sezione **livello grafico a barre** .

| Impostazione              | Descrizione      |
|----------------------|------------------|
| Forma a barre            | Forma della barra 3D.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box: barre visualizzate come caselle rettangolari.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cilindri: barre visualizzate come cilindri. |
| Altezza               | Altezza di ogni barra. Se un campo viene passato nel bucket **dimensioni** del riquadro **campi** , le barre verranno ridimensionate rispetto a questo valore di altezza. |
| Altezza scala sullo zoom | Specifica se l'altezza delle barre deve essere ridimensionata rispetto al livello di zoom. |
| Larghezza                | Larghezza di ogni barra.  |
| Ridimensiona larghezza nello zoom  | Specifica se è necessario ridimensionare la larghezza delle barre rispetto al livello di zoom.  |
| Fill color           | Colore di ogni barra. Questa opzione è nascosta quando un campo viene passato nel bucket **Legenda** del riquadro **campi** e nel riquadro **formato** verrà visualizzata una sezione **colori dati** separata. |
| Trasparenza         | Trasparenza di ogni barra. |
| Zoom minimo             | Sono disponibili riquadri del livello di zoom minimo. |
| Zoom max             | Sono disponibili i riquadri massimi del livello di zoom. |
| Posizione livello       | Specifica la posizione del livello rispetto ad altri livelli della mappa. |

> [!NOTE]
> Se le barre hanno un valore di larghezza ridotto e l'opzione **Ridimensiona larghezza su zoom** è disabilitata, potrebbero scomparire quando si esegue lo zoom avanti di un numero di dimensioni inferiore rispetto alla larghezza di cui è stato eseguito il rendering. Tuttavia, quando è abilitata l'opzione **Ridimensiona larghezza su zoom** , vengono eseguiti calcoli aggiuntivi quando si modifica il livello di zoom che può influisca sulle prestazioni di set di dati di grandi dimensioni.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere altro contesto alla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riferimenti](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Visualizzare il traffico in tempo reale](power-bi-visual-show-real-time-traffic.md)

Personalizzare l'oggetto visivo:

> [!div class="nextstepaction"]
> [Suggerimenti e consigli per la formattazione dei colori in Power BI](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Personalizzare i titoli, gli sfondi e le legende delle visualizzazioni](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
