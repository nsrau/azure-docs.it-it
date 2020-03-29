---
title: Etichettare le immagini più velocemente con Smart Labeler
titleSuffix: Azure Cognitive Services
description: In questa guida verrà illustrato come utilizzare Smart Labeler per generare i tag suggeriti per le immagini. Ciò consente di etichettare un numero elevato di immagini più rapidamente durante il training di un modello di visione personalizzata.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647760"
---
# <a name="label-images-faster-with-smart-labeler"></a>Etichettare le immagini più velocemente con Smart Labeler

In questa guida verrà illustrato come utilizzare Smart Labeler per generare i tag suggeriti per le immagini. Ciò consente di etichettare un numero elevato di immagini più rapidamente durante il training di un modello di visione personalizzata.

Quando si contrassegnano le immagini per un modello di visione personalizzata, il servizio usa l'iterazione con training più recente del modello per stimare le etichette delle immagini senza tag. Mostra quindi queste stime come tag suggeriti, in base alla soglia di confidenza selezionata e all'incertezza di stima. È quindi possibile confermare o modificare i suggerimenti, accelerando il processo di assegnazione manuale di tag alle immagini per l'allenamento.

## <a name="when-to-use-smart-labeler"></a>Quando utilizzare Smart Labeler

Tenere presenti le seguenti limitazioni:

* Devi richiedere solo i tag suggeriti per le immagini il cui contenuto è già stato addestrato una volta. Non ricevere suggerimenti per un nuovo tag che stai appena iniziando ad allenare.

> [!IMPORTANT]
> La funzionalità Smart Labeler utilizza lo stesso modello di [determinazione dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) delle stime regolari. La prima volta che attivi i tag suggeriti per un set di immagini, ti verranno addebitati gli addebiti per le chiamate di stima. Dopo di che, il servizio memorizza i risultati per le immagini selezionate in un database per 30 giorni, ed è possibile accedervi in qualsiasi momento gratuitamente entro tale periodo. Dopo 30 giorni, ti verranno addebitati addebiti se richiedi di nuovo i tag suggeriti.

## <a name="smart-labeler-workflow"></a>Flusso di lavoro Smart Labeler

La procedura seguente illustra come utilizzare Smart Labeler:

1. Carica tutte le tue immagini di formazione sul tuo progetto Vision personalizzata.
1. Etichetta una parte del tuo set di dati, scegliendo un numero uguale di immagini per ogni tag.
    > [!TIP]
    > Assicurati di utilizzare tutti i tag per i quali desideri suggerimenti in un secondo momento.
1. Avviare il processo di formazione.
1. Al termine della formazione, passare alla visualizzazione **Senza tag** e selezionare il pulsante Ottieni **tag suggeriti** nel riquadro sinistro.
    > [!div class="mx-imgBorder"]
    > ![Il pulsante dei tag suggeriti viene visualizzato nella scheda Immagini senza tag.](./media/suggested-tags/suggested-tags-button.png)
1. Nella finestra popup visualizzata, impostare il numero di immagini per cui si desidera inserire i suggerimenti. Dovresti ricevere suggerimenti di tag iniziali solo per una parte delle immagini senza tag. Si otterrà migliori suggerimenti di tag come si scorre questo processo.
1. Confermare i tag suggeriti, correggendo quelli che non sono corretti.
    > [!TIP]
    > Le immagini con tag suggeriti vengono ordinate in base all'incertezza di stima (i valori più bassi indicano una maggiore confidenza). È possibile modificare l'ordinamento con l'opzione **Ordina per incertezza.** Se si imposta l'ordine su **alto su basso**, è possibile correggere prima le previsioni ad alta incertezza e quindi confermare rapidamente quelle a bassa incertezza.
    * Nei progetti di classificazione delle immagini, è possibile selezionare e confermare i tag in batch. Filtrare la visualizzazione in base a un determinato tag suggerito, deselezionare le immagini contrassegnate in modo non corretto e quindi confermare il resto in un batch.
        > [!div class="mx-imgBorder"]
        > ![I tag suggeriti vengono visualizzati in modalità batch per IC con filtri.](./media/suggested-tags/ic-batch-mode.png)

        È inoltre possibile utilizzare i tag suggeriti in modalità immagine singola selezionando un'immagine dalla raccolta.

        ![I tag suggeriti vengono visualizzati in modalità immagine singola per IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * Nei progetti di rilevamento degli oggetti, le conferme batch non sono supportate, ma è comunque possibile filtrare e ordinare i tag in base ai tag suggeriti per un'esperienza di etichettatura più organizzata. Le miniature delle immagini senza tag mostreranno una sovrapposizione di rettangoli di selezione che indicano le posizioni dei tag suggeriti. Se non selezionate un filtro tag suggerito, tutte le immagini senza tag verranno visualizzate senza sovrapposizioni.
        > [!div class="mx-imgBorder"]
        > ![I tag suggeriti vengono visualizzati in modalità batch per OD con filtri.](./media/suggested-tags/od-batch-mode.png)

        Per confermare i tag di rilevamento degli oggetti, è necessario applicarli a ogni singola immagine nella raccolta.

        ![I tag suggeriti vengono visualizzati in modalità immagine singola per OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Avviare di nuovo il processo di formazione.
1. Ripetere i passaggi precedenti fino a quando non si è soddisfatti della qualità del suggerimento.

## <a name="next-steps"></a>Passaggi successivi

Segui una guida introduttiva per iniziare a creare e formare un progetto Vision personalizzato.

* [Creare un classificatore](getting-started-build-a-classifier.md)
* [Creare un rilevatore di oggetti](get-started-build-detector.md)