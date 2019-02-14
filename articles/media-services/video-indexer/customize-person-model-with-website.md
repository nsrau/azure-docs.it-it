---
title: Usare il sito Web di Video Indexer per personalizzare un modello delle persone - Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello delle persone con il sito Web di Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 29c32126a5bbe792dd6fe88ae189e09c511d2a22
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997058"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizzare un modello delle persone con il sito Web di Video Indexer

Video Indexer supporta il rilevamento del viso e l'identificazione di celebrità nei contenuti video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. I visi che la funzionalità di identificazione di celebrità non riconosce vengono rilevati, ma lasciati senza nome. Dopo aver caricato il video in Video Indexer e aver ottenuto i risultati, è possibile tornare indietro e assegnare un nome ai visi che non erano stati identificati. Dopo aver etichettato un viso con un nome, il viso e il nome vengono aggiunti al modello delle persone del proprio account. Video Indexer identificherà quindi questo viso in tutti i video futuri e precedenti.

È possibile usare il sito Web di Video Indexer per modificare i visi rilevati in un video, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [Personalizzare il modello delle persone usando le API](customize-person-model-with-api.md).

## <a name="edit-a-face"></a>Modificare un viso

> [!NOTE]
> I nomi sono univoci nei modelli delle persone. Se si assegna lo stesso nome a due visi diversi, Video Indexer visualizza i visi come la stessa persona e li riunisce quando si reindicizza il video. Se sono state rilevate più occorrenze diverse dello stesso viso, assegnare lo stesso nome e reindicizzare il video.
> È possibile aggiornare il nome di un viso identificato in Video Indexer come celebrità. Il nuovo nome assegnato avrà la precedenza sull'identificazione di celebrità predefinita.

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Cercare un video da visualizzare e modificare nell'account.
3. Per modificare un viso nel video, passare alla scheda **Informazioni dettagliate** e fare clic sull'icona a forma di matita nell'angolo in alto a destra della finestra.

    ![Personalizzare il modello delle persone](./media/customize-face-model/customize-face-model.png)

4. Fare clic su uno qualsiasi dei visi rilevati e cambiare i relativi nomi da "Sconosciuto #X" oppure dal nome precedentemente assegnato al viso.
5. Dopo aver digitato il nuovo nome, fare clic sull'icona di controllo accanto al nuovo nome. In questo modo si salverà il nuovo nome e tutte le occorrenze di questo viso verranno identificate e denominate negli altri video attuali e nei video futuri caricati. L'identificazione del viso negli altri video attuali può richiedere alcuni minuti poiché si tratta di un processo batch. 

    ![Salvare l'aggiornamento](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>Eliminare un viso

Per eliminare una viso rilevato nel video, passare al riquadro **Informazioni dettagliate** e fare clic sull'icona a forma di matita nell'angolo in alto a destra del riquadro. Fare clic sull'opzione **Elimina** sotto il nome del viso. In questo modo il viso rilevato verrà rimosso dal video. Il viso sarà ancora rilevato negli altri video in cui è presente, ma è possibile eliminarlo anche da quei video dopo che sono stati indicizzati. Il viso continuerà ad esistere anche nel modello delle persone dell'account, se gli è stato assegnato un nome prima della cancellazione.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello delle persone usando le API](customize-person-model-with-api.md)
