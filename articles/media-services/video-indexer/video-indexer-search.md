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
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030533"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Cerca i momenti esatti nei video con Video Indexer

In questo argomento viene illustrato come utilizzare il sito Web di Video Indexer per cercare istanti esatti nei video.

1. Accedere al sito Web di [video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
1. Specificare le parole chiave di ricerca e la ricerca verrà eseguita tra tutti i video nella libreria dell'account. 

    È possibile filtrare la ricerca selezionando **filtri**. Nell'esempio seguente viene cercato "Microsoft" che viene visualizzato come solo testo su schermo (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filter, solo testo":::
1. Premere **Cerca** per visualizzare il risultato.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Risultato ricerca video":::

    Se si seleziona uno dei risultati, il lettore porta al momento esatto del video.
1. Per visualizzare e cercare le informazioni dettagliate riepilogative del video, fare clic su **Riproduci** nel video o selezionare uno dei risultati della ricerca originali. 

    È possibile visualizzare, cercare, modificare le **informazioni dettagliate**. Quando si seleziona una delle informazioni dettagliate, il lettore porta al momento esatto del video.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Visualizza, Cerca e modifica le informazioni dettagliate del video":::

    Se si incorpora il video tramite Video Indexer widget, è possibile ottenere la visualizzazione del lettore/informazioni dettagliate e la sincronizzazione nell'app. Per altre informazioni, vedere [incorporare video Indexer widget nell'app](video-indexer-embed-widgets.md).
1. È possibile visualizzare, cercare e modificare le trascrizioni facendo clic sulla scheda **sequenza temporale** . 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Visualizza, Cerca e modifica le trascrizioni del video":::

    Per modificare il testo, selezionare **modifica** nell'angolo superiore destro e modificare il testo in base alle esigenze. 

    È anche possibile tradurre e scaricare le trascrizioni selezionando l'opzione appropriata nell'angolo superiore destro. 

## <a name="embed-download-create-projects"></a>Incorporare, scaricare, creare progetti

È possibile incorporare il video selezionando **</>incorporare** nel video. Per informazioni dettagliate, vedere [incorporare i widget visivi nell'applicazione](video-indexer-embed-widgets.md).

È possibile scaricare il video di origine, informazioni dettagliate del video, trascrizioni facendo clic su **Scarica** sotto il video.

È possibile creare una clip basata sul video di righe e istanti specifici facendo clic su **Apri nell'editor**. Modificare quindi il video e salvare il progetto. Per informazioni dettagliate, vedere [usare approfondimenti sui video](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Incorporare, scaricare e creare progetti del video":::

## <a name="next-steps"></a>Passaggi successivi

[Elaborare il contenuto con Video Indexer API REST](video-indexer-use-apis.md)
