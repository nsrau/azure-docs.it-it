---
title: Iscriversi a Video Indexer e caricare il primo video - Azure
titleSuffix: Azure Media Services
description: Informazioni su come effettuare l'iscrizione e caricare il primo video tramite il portale di Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: juliako
ms.openlocfilehash: 6b154db25f1f13b60e4110d1b1866308ee952487
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219791"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Guida introduttiva: Come iscriversi e caricare il primo video

Questo argomento di avvio rapido introduttivo illustra come accedere al sito Web Video Indexer e come caricare il primo video.

Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuita) o un'opzione a pagamento (in cui non si è limitati dalla quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2400 minuti di indicizzazione gratuita per gli utenti di API. Con l'opzione a pagamento, si crea un account di Video Indexer [collegato alla sottoscrizione di Azure e un account di Servizi multimediali di Azure](connect-to-azure.md). Il pagamento viene effettuato per i minuti di indicizzazione, nonché in base ai costi correlati all'account di Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Iscriversi a Video Indexer

Per iniziare a eseguire attività di sviluppo con Video Indexer, visitare il sito Web di [Video Indexer](https://www.videoindexer.ai/) e iscriversi.

> [!NOTE]
> Quando si inizia a usare Video Indexer, tutti i dati archiviati e i contenuti caricati inattivi vengono crittografati con una chiave gestita da Microsoft.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Caricare un video usando il sito Web di Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Formati di file supportati per Video Indexer

Per un elenco dei formati di file che è possibile usare con Video Indexer, vedere l'articolo [Contenitore di input/formati di file](../latest/media-encoder-standard-formats.md#input-containerfile-formats).

### <a name="upload-a-video"></a>Caricare un video

1. Accedere al sito Web di [Video Indexer](https://www.videoindexer.ai/).
1. Per caricare un video, premere il pulsante o il collegamento **Upload** (Carica).

    > [!NOTE]
    > Il nome del video non può contenere più di 80 caratteri.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Upload":::
1. Dopo il caricamento del video, Video Indexer avvia l'indicizzazione e l'analisi. Viene visualizzato lo stato di avanzamento. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Upload":::
1. Al termine dell'analisi si riceverà una notifica con un collegamento al video e una breve descrizione del relativo contenuto, ad esempio persone, parole pronunciate e scritte, argomenti ed entità denominate.
1. In un secondo momento è possibile trovare il video nell'elenco di librerie ed eseguire operazioni diverse, ad esempio ricerca, reindicizzazione, modifica.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Upload":::
 
## <a name="see-also"></a>Vedere anche

Per altre informazioni, vedere [Caricare e indicizzare video](upload-index-videos.md).

Dopo aver caricato e indicizzato un video, è possibile visualizzare le relative informazioni tramite il sito Web di [Video Indexer](video-indexer-use-apis.md) o il [portale per sviluppatori di Video Indexer](video-indexer-view-edit.md). 

[Iniziare a usare le API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione dettagliata, vedere il [lab introduttivo](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Al termine del workshop si avrà una buona conoscenza del tipo di informazioni che possono essere estratte da contenuti video e audio, si sarà più preparati a identificare le opportunità correlate all'intelligence dei contenuti, sfruttare le tecnologie di intelligenza artificiale video in Azure e valutare diversi scenari in Video Indexer.

