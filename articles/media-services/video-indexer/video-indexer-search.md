---
title: Trovare momenti esatti nei video - Video Indexer
titleSuffix: Azure Media Services
description: Questo argomento illustra come trovare momenti esatti nei video con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 8ebf7606d1a0932cc2254e14a40a2fff550fd35f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833788"
---
# <a name="find-exact-moments-within-videos"></a>Trovare momenti esatti nei video

Questo argomento illustra le opzioni di ricerca disponibili per trovare momenti esatti nei video.

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Eseguire una ricerca tra tutti i video nel proprio account.

    Nell'esempio seguente vengono cercati tutti i video in cui viene illustrata la sicurezza e in cui viene visualizzato l'oggetto.

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
3. Cercare le informazioni dettagliate riepilogate del video.

    È poi possibile eseguire ricerche all'interno di un video facendo clic su **Riproduci** sul video. Si può quindi cercare all'interno del video selezionando la scheda **Ricerca**. 

    Nell'esempio seguente viene cercato "Secure" all'interno del video selezionato.

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    Se si fa clic su uno dei risultati, il lettore si posiziona in quel momento nel video. È possibile implementare la visualizzazione e la sincronizzazione del lettore e delle informazioni dettagliate nella propria applicazione. Per altre informazioni, vedere [Incorporare i widget di Video Indexer nelle applicazioni](video-indexer-embed-widgets.md). 
4. Cercare la suddivisione dettagliata del video.
    
    Se si vuole creare una clip personalizzata in base al video trovato, fare clic sul pulsante **modifica** . Questa pagina mostra i video insieme alle informazioni dettagliate come filtri. Per altre informazioni, vedere [Visualizzare e modificare le informazioni dettagliate di Video Indexer](video-indexer-view-edit.md). 

    È possibile eseguire ricerche nel video per visualizzare solo le righe a cui si è interessati e usare le informazioni dettagliate sul lato per filtrare le parti che si desidera visualizzare. Al termine, è possibile visualizzare in anteprima il clip e premere **pubblica** per creare il nuovo clip visualizzato nella raccolta.
    
    Nell'esempio seguente è stato cercato il testo "realtà mista". Sono stati anche applicati filtri aggiuntivi, come illustrato nello screenshot di seguito.
    
    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Passaggi successivi 

Dopo aver individuato il video che si vuole usare, è possibile continuare a elaborarlo come descritto in uno degli argomenti seguenti: 

- [Usare le informazioni dettagliate approfondite dei video](use-editor-create-project.md)
- [Elaborare contenuti con l'API REST Video Indexer](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione)

## <a name="see-also"></a>Vedere anche

[Panoramica di Video Indexer](video-indexer-overview.md)
