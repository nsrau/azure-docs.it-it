---
title: Trovare momenti esatti nei video - Video Indexer
titlesuffix: Azure Cognitive Services
description: Questo argomento illustra come trovare momenti esatti nei video con Video Indexer.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 9806f9c3f22429d61ac765773e4b6971d683a6b1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984509"
---
# <a name="find-exact-moments-within-videos"></a>Trovare momenti esatti nei video

Questo argomento illustra le opzioni di ricerca disponibili per trovare momenti esatti nei video.

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
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
