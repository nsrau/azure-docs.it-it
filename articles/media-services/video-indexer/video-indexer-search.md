---
title: Cerca i momenti esatti nei video con Video Indexer
titleSuffix: Azure Media Services
description: Informazioni su come cercare i momenti esatti nei video usando Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 1c3326456fefbbef9228cf26b5821f306cd21601
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80127918"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Cerca i momenti esatti nei video con Video Indexer

In questo argomento vengono illustrate le opzioni di ricerca che consentono di cercare i momenti esatti nei video.

1. Accedere al sito Web di [video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Eseguire una ricerca tra tutti i video nel proprio account.

    Nell'esempio seguente vengono cercati tutti i video che parlano di sicurezza e in cui viene visualizzato l'oggetto.

    ![Cerca il video in Video Indexer](./media/video-indexer-search/video-indexer-search01.png)

3. Cercare le informazioni dettagliate riepilogate del video.

    È quindi possibile eseguire la ricerca in un video selezionando **Riproduci** nel video. Quindi, è possibile cercare i momenti esatti nel video selezionando la scheda **Cerca** .

    Nell'esempio seguente viene cercato "Secure" all'interno del video selezionato.

    ![Cerca in un video con Video Indexer](./media/video-indexer-search/video-indexer-search02.png)

    Se si seleziona uno dei risultati, il lettore porta al momento esatto del video. È possibile ottenere la visualizzazione di Player/Insights e la sincronizzazione nell'app. Per altre informazioni, vedere [incorporare video Indexer widget nell'app](video-indexer-embed-widgets.md).

4. Cercare la suddivisione dettagliata del video.

    Se si vuole creare una clip personalizzata in base al video trovato, selezionare il pulsante **modifica** . Questa pagina mostra il video insieme alle informazioni dettagliate come filtri. Per altre informazioni, vedere [Visualizzare e modificare le informazioni dettagliate di Video Indexer](video-indexer-view-edit.md).

    È possibile cercare i momenti esatti nel video per visualizzare solo le righe a cui si è interessati. Usare le informazioni dettagliate sul lato per filtrare le parti che si desidera visualizzare. Al termine, è possibile visualizzare in anteprima il clip e selezionare **pubblica** per creare il nuovo clip visualizzato nella raccolta.

    Nell'esempio seguente è stato cercato il testo "realtà mista". Sono stati anche applicati filtri aggiuntivi, come illustrato nello screenshot di seguito.

    ![Cerca il momento esatto in Video Indexer](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Passaggi successivi

Quando si trova il momento esatto del video che si vuole usare, è possibile continuare a elaborare il video. Per altre informazioni, vedere gli argomenti seguenti:

- [Usare le informazioni dettagliate approfondite dei video](use-editor-create-project.md)
- [Elaborare il contenuto con Video Indexer API REST](video-indexer-use-apis.md)
- [Incorporare i widget visivi nell'applicazione](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Vedere anche

[Panoramica di Video Indexer](video-indexer-overview.md)
