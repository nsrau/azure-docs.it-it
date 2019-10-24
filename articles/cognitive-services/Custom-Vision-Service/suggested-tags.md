---
title: Etichetta le immagini più velocemente con i tag consigliati
titleSuffix: Azure Cognitive Services
description: In questa guida si apprenderà come usare i tag consigliati per etichettare un numero elevato di immagini più rapidamente durante il training di Visione personalizzata modelli.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: pafarley
ms.openlocfilehash: 06735240729fb2bfd21b87f592e143e9ceabb390
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753480"
---
# <a name="label-images-faster-with-suggested-tags"></a>Etichetta le immagini più velocemente con i tag consigliati

In questa guida si apprenderà come usare la funzionalità tag suggeriti per etichettare un numero elevato di immagini più rapidamente durante il training di un modello di Visione personalizzata. 

Quando si contrassegnano le immagini per un modello di Visione personalizzata, il servizio utilizza l'iterazione più recente con training del modello per stimare le etichette delle immagini senza tag. Vengono quindi visualizzate le stime come tag suggeriti, in base alla soglia di confidenza selezionata e all'incertezza della stima. È quindi possibile confermare o modificare i suggerimenti, velocizzando il processo di assegnazione manuale di tag alle immagini per il training.

## <a name="when-to-use-suggested-tags"></a>Quando usare i tag consigliati

Tenere presenti le limitazioni seguenti:

* È consigliabile richiedere solo tag suggeriti per le immagini il cui contenuto è già stato sottoposto a training una volta. Non ricevere suggerimenti per un nuovo tag che si sta appena iniziando a eseguire il training.

> [!IMPORTANT]
> La caratteristica dei tag suggeriti usa lo stesso [modello di determinazione dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) delle stime regolari. La prima volta che si attivano i tag suggeriti per un set di immagini, l'addebito sarà uguale a quello delle chiamate di stima. Successivamente, il servizio archivia i risultati per le immagini selezionate in un database per 30 giorni ed è possibile accedervi in qualsiasi momento gratuitamente entro tale periodo. Dopo 30 giorni, verranno addebitati i costi se si richiedono di nuovo i tag suggeriti.

## <a name="suggested-tags-workflow"></a>Flusso di lavoro dei tag suggerito

Nei passaggi seguenti viene illustrato come utilizzare la funzionalità tag suggerita:

1. Caricare tutte le immagini di training nel progetto Visione personalizzata.
1. Etichettare parte del set di dati, scegliendo un numero uguale di immagini per ogni tag.
    > [!TIP]
    > Assicurarsi di usare tutti i tag per i quali si desiderano i suggerimenti in un secondo momento.
1. Avviare il processo di training.
1. Al termine del training, passare alla visualizzazione senza **tag** e selezionare il pulsante **Ottieni tag suggeriti** nel riquadro sinistro.
    > [!div class="mx-imgBorder"]
    > ![The pulsante Tag suggerito viene visualizzato nella scheda immagini senza tag. ](./media/suggested-tags/suggested-tags-button.png)
1. Selezionare il set di immagini per cui si desiderano i suggerimenti. È consigliabile ottenere solo i suggerimenti dei tag iniziali per una parte delle immagini senza tag. Quando si scorre questo processo, si otterranno suggerimenti per i tag migliori.
1. Confermare i tag suggeriti, correggendo quelli non corretti.
    > [!TIP]
    > Le immagini con tag suggeriti sono ordinate in base all'incertezza della stima (i valori più bassi indicano una maggiore confidenza). È possibile modificare l'ordinamento con l'opzione **Ordina per l'incertezza** . Se si imposta l'ordine su **alto in basso**, è possibile correggere prima le stime ad alta incertezza e quindi confermare rapidamente le incertezze.
    * Nei progetti di classificazione delle immagini è possibile selezionare e confermare i tag in batch. Filtrare la visualizzazione in base a un tag suggerito specificato, deselezionare le immagini contrassegnate in modo errato, quindi confermare il resto in un batch.
        > [!div class="mx-imgBorder"]
        > ![Suggested tag vengono visualizzati in modalità batch per IC con i filtri. ](./media/suggested-tags/ic-batch-mode.png)

        È anche possibile usare i tag consigliati in modalità immagine singola selezionando un'immagine dalla raccolta.

        ![I tag suggeriti vengono visualizzati in modalità immagine singola per IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * Nei progetti di rilevamento oggetti, le conferme batch non sono supportate, ma è comunque possibile filtrare e ordinare in base ai tag suggeriti per un'esperienza di etichettatura più organizzata. Nelle anteprime delle immagini senza tag sarà visualizzata una sovrapposizione di caselle di delimitazione che indicano le posizioni dei tag suggeriti. Se non si seleziona un filtro di Tag suggerito, tutte le immagini senza tag verranno visualizzate senza sovrapporre i rettangoli di delimitazione.
        > [!div class="mx-imgBorder"]
        > ![Suggested tag vengono visualizzati in modalità batch per OD con i filtri. ](./media/suggested-tags/od-batch-mode.png)

        Per confermare i tag di rilevamento degli oggetti, è necessario applicarli a ogni singola immagine nella raccolta.

        ![I tag suggeriti vengono visualizzati in modalità immagine singola per OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Avviare di nuovo il processo di training.
1. Ripetere i passaggi precedenti fino a quando non si è soddisfatti della qualità del suggerimento.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a creare ed eseguire il training di un progetto di Visione personalizzata, seguire una guida introduttiva.

* [Compilare un classificatore](getting-started-build-a-classifier.md)
* [Compilare un rilevatore di oggetti](get-started-build-detector.md)