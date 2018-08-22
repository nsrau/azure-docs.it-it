---
title: Usare Video Indexer di Azure per trovare momenti esatti nei video | Microsoft Docs
description: Questo argomento illustra come trovare momenti esatti nei video.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 1cffa067d8028adab4dbcc82c529f77d980ce6be
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397544"
---
# <a name="find-exact-moments-within-videos"></a>Trovare momenti esatti nei video

Questo argomento illustra le opzioni di ricerca disponibili per trovare momenti esatti nei video.

1. Accedere all'account di [Video Indexer](https://api-portal.videoindexer.ai/).
2. Eseguire una ricerca tra tutti i video nel proprio account.

    Nell'esempio seguente sono stati cercati tutti i video creati da Channel9 con Scott Hanselman.

    ![Ricerca](./media/video-indexer-search/video-indexer-search01.png)
    
3. Cercare le informazioni dettagliate riepilogate del video.

    È poi possibile eseguire ricerche all'interno di un video facendo clic su **Riproduci** sul video. Si può quindi cercare all'interno del video selezionando la scheda **Ricerca**. Nell'esempio vengono cercate tutte le posizioni in cui viene usato il testo "identity protection". 

    ![Ricerca](./media/video-indexer-search/video-indexer-search02.png)

    Se si fa clic su uno dei risultati, il lettore si posiziona in quel momento nel video. È possibile implementare la visualizzazione e la sincronizzazione del lettore e delle informazioni dettagliate nella propria applicazione. Per altre informazioni, vedere [Incorporare i widget di Video Indexer nelle applicazioni](video-indexer-embed-widgets.md). 

    
4. Cercare la suddivisione dettagliata del video.

    Se si vuole creare una propria suddivisione basata sul video trovato, premere il pulsante **Modifica**. Questa pagina mostra tutte le informazioni dettagliate sul video. È possibile cercare all'interno della suddivisione per visualizzare solo le righe a cui si è interessati. Per altre informazioni, vedere [Visualizzare e modificare le informazioni dettagliate di Video Indexer](video-indexer-view-edit.md).

    In questo esempio è stato cercato il testo "identity protection". Sono stati anche applicati filtri aggiuntivi, come illustrato nello screenshot di seguito.

    ![Ricerca](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Passaggi successivi 

Dopo aver individuato il video che si vuole usare, è possibile continuare a elaborarlo come descritto in uno degli argomenti seguenti: 

- [Creare informazioni dettagliate video da video esistenti](video-indexer-create-new.md)
- [Elaborare contenuti con l'API REST Video Indexer](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione)

## <a name="see-also"></a>Vedere anche 

[Panoramica di Video Indexer](video-indexer-overview.md)
