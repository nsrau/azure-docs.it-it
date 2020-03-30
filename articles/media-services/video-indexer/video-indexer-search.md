---
title: Cerca momenti esatti nei video con Video Indexer
titleSuffix: Azure Media Services
description: Scopri come cercare i momenti esatti nei video utilizzando Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 1c3326456fefbbef9228cf26b5821f306cd21601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127918"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Cerca momenti esatti nei video con Video Indexer

Questo argomento illustra le opzioni di ricerca che consentono di cercare i momenti esatti nei video.

1. Accedi al sito Web [Indicizzatore video.](https://www.videoindexer.ai/)
2. Eseguire una ricerca tra tutti i video nel proprio account.

    Nell'esempio seguente, cerchiamo tutti i video che parlano di sicurezza e in cui viene visualizzato Satya.

    ![Cercare video in Video Indexer](./media/video-indexer-search/video-indexer-search01.png)

3. Cercare le informazioni dettagliate riepilogate del video.

    È quindi possibile cercare in un video selezionando **Riproduci** sul video. Quindi, è possibile cercare i momenti esatti nel video selezionando la scheda **Cerca.**

    Nell'esempio seguente, cerchiamo "sicuro" all'interno del video selezionato.

    ![Cercare in un video con Video Indexer](./media/video-indexer-search/video-indexer-search02.png)

    Se si seleziona uno dei risultati, il lettore ti porta a quel momento esatto nel video. Puoi ottenere la visualizzazione e la sincronizzazione del lettore/insights nella tua app. Per ulteriori informazioni, consultate [Incorporare widget Indicizzatore video nell'app.](video-indexer-embed-widgets.md)

4. Cercare la suddivisione dettagliata del video.

    Se si desidera creare una clip personalizzata in base al video trovato, selezionare il pulsante **Modifica.** Questa pagina mostra il video insieme alle sue informazioni dettagliate come filtri. Per altre informazioni, vedere [Visualizzare e modificare le informazioni dettagliate di Video Indexer](video-indexer-view-edit.md).

    Puoi cercare i momenti esatti all'interno del video per mostrare solo le linee che ti interessano. Usa le informazioni dettagliate laterali per filtrare le parti che desideri visualizzare. Al termine, è possibile visualizzare l'anteprima della clip e selezionare **Pubblica** per creare il nuovo clip visualizzato nella galleria.

    Nell'esempio seguente è stato cercato il testo "realtà mista". Sono stati anche applicati filtri aggiuntivi, come illustrato nello screenshot di seguito.

    ![Cerca il momento esatto nell'indicizzatore video](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Passaggi successivi

Una volta trovato il momento esatto nel video con cui vuoi lavorare, puoi continuare l'elaborazione del video. Per altre informazioni, vedere gli argomenti seguenti:

- [Usa le informazioni approfondite sui tuoi video](use-editor-create-project.md)
- [Elaborare contenuti con l'API REST Video Indexer](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione)

## <a name="see-also"></a>Vedere anche

[Panoramica di Video Indexer](video-indexer-overview.md)
