---
title: Moderazione video con lo strumento di revisione-Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare la moderazione video assistita da computer e lo strumento di revisione per moderare il contenuto non appropriato
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 392cc06c6e0bce7ec2304da61033fc508d940bbb
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93143770"
---
# <a name="video-moderation-with-the-review-tool"></a>Moderazione video con lo strumento di Revisione

USA [lo strumento](Review-Tool-User-Guide/human-in-the-loop.md) di [moderazione](video-moderation-api.md) e revisione video assistito da computer di content moderator per moderare video e trascrizioni per contenuti per adulti (espliciti) e di velocità (indicativa) per ottenere i risultati migliori per la tua azienda.

## <a name="view-videos-under-review"></a>Visualizza i video in Revisione

Nel Dashboard selezionare una delle code di verifica all'interno del tipo di contenuto video. Verrà avviata una verifica e verrà aperta la pagina moderazione contenuto video.

> [!div class="mx-imgBorder"]
> ![Visualizzazione dettagliata della moderazione video nello strumento di Revisione](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Review count

Utilizzare il dispositivo di scorrimento in alto a destra per impostare il numero di revisioni che si desidera visualizzare nella pagina.

### <a name="view-type"></a>Tipo di vista

È possibile visualizzare le diverse voci di contenuto come riquadri o in una visualizzazione dettagliata. La visualizzazione **Dettagli** consente di visualizzare i fotogrammi chiave e altre informazioni sul video selezionato. 

> [!NOTE]
> Invece di produrre fotogrammi a intervalli regolari, il servizio di moderazione video identifica e restituisce solo i fotogrammi potenzialmente completi (validi). Questa funzionalità consente di generare in modo efficiente i frame per l'analisi a livello di frame e per adulti.

La visualizzazione **affiancata** visualizzerà ogni video come un unico riquadro. Selezionare il pulsante Espandi sopra un fotogramma video per ingrandire il video e nascondere gli altri.

### <a name="content-obscuring-effects"></a>Effetti sul contenuto oscurato

Usare gli interruttori **sfoca tutto** e **nero e bianco** per impostare questi effetti sul contenuto. Sono attivate per impostazione predefinita. Nella visualizzazione **affiancata** è possibile impostare gli effetti singolarmente per ogni video.

## <a name="check-video-details"></a>Controllare i dettagli del video

Nella visualizzazione **Dettagli** , nel riquadro destro vengono visualizzate diverse schede che forniscono informazioni dettagliate sul video.

* Selezionare la scheda **Note** per aggiungere note personalizzate ai video.
* Selezionare la scheda **trascrizione** per visualizzare la trascrizione video &mdash; il servizio estrae automaticamente una trascrizione di qualsiasi voce del video. Quando si seleziona una sezione di testo, il lettore video passa a tale parte del video.
* Selezionare la scheda **meta-data** per visualizzare i metadati del file video.
* Selezionare la scheda **cronologia** per visualizzare la cronologia della revisione, ad esempio quando è stata creata e come è stata modificata.

> [!div class="mx-imgBorder"]
> ![Pulsante Tag bulk per moderazione video](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Applica tag di moderazione

L'attività principale di una revisione video consiste nell'applicare o rimuovere tag di moderazione nei video o nelle parti dei video.

### <a name="bulk-tagging"></a>Assegnazione di tag bulk

La barra degli strumenti **tag bulk** consente di aggiungere tag a più video selezionati contemporaneamente. Selezionare uno o più video, quindi selezionare i tag che si desidera applicare e fare clic su **Invia** . 

> [!div class="mx-imgBorder"]
> ![Pulsante Tag bulk per moderazione video](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Tag del fotogramma chiave

È anche possibile aggiungere tag di moderazione a fotogrammi chiave specifici. Selezionare i frame dal riquadro riquadro fotogramma chiave e quindi selezionare i **tag dei fotogrammi chiave +** per applicare i tag desiderati.

> [!NOTE]
> Se il servizio non è stato in grado di estrarre i fotogrammi chiave, nel riquadro della sezione del fotogramma chiave **non sarà disponibile alcun frame** e l'opzione per selezionare i fotogrammi chiave sarà disabilitata. In questo caso, è possibile applicare i tag al video nel suo complesso (usando i **tag video +** pulsante).

> [!div class="mx-imgBorder"]
> ![Visualizzazione dettagliata della moderazione video nello strumento di Revisione](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Inserire una recensione in attesa

Il pulsante **Mantieni** nella parte inferiore del riquadro video consente di inserire una recensione in attesa per poterla recuperare e completarla in un secondo momento. Questa operazione può essere eseguita per una revisione che richiede un consulto da un altro membro del team o da un responsabile che attualmente non è disponibile. 

È possibile visualizzare i video in attesa facendo clic sul pulsante di **attesa** nella parte superiore della schermata. Il riquadro di attesa viene visualizzato a destra. Da qui è possibile selezionare più verifiche in attesa e rilasciarle nella coda o impostare la data di scadenza. Dopo il periodo di tempo preconfigurato, le verifiche in attesa vengono rilasciate nella coda. Selezionare **Save (Salva** ) per avviare il conteggio a partire dall'ora di scadenza attualmente selezionata.

> [!div class="mx-imgBorder"]
> ![Visualizzazione dettagliata della moderazione video nello strumento di Revisione](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Invia una recensione

Dopo aver applicato i tag, selezionare il pulsante **Submit (Invia** ) nella parte inferiore del riquadro del video. Se sono stati contrassegnati più video, è possibile inviarli in un'unica recensione o come revisioni separate.

## <a name="limbo-state"></a>Stato limbo

Dopo aver inviato una recensione, il video viene spostato nello stato **limbo** , che è possibile visualizzare selezionando il pulsante **limbo** nella parte superiore della schermata. I video rimangono nello stato limbo per un periodo di tempo preconfigurato (che è possibile modificare nel menu in basso) o fino a quando non vengono rivisti o inviati manualmente.

Una volta che i video scadono da limbo, le revisioni vengono contrassegnate come complete.

## <a name="next-steps"></a>Passaggi successivi

- Apprendere le nozioni fondamentali con la [guida introduttiva alla moderazione video](video-moderation-api.md).
- Imparare a generare [revisioni di video](video-reviews-quickstart-dotnet.md) dall'output moderato per i revisori umani.
- Aggiungere [revisioni di trascrizioni video](video-transcript-reviews-quickstart-dotnet.md) alle revisioni di video.
- Fare riferimento all'esercitazione dettagliata su come sviluppare una [soluzione completa di moderazione video](video-transcript-moderation-review-tutorial-dotnet.md).