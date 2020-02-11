---
title: Iscriversi a Video Indexer e caricare il primo video - Azure
titleSuffix: Azure Media Services
description: Informazioni su come effettuare l'iscrizione e caricare il primo video tramite il portale di Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 1f3d442ea7cf6d95427aaa9c072410119e2735af
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989808"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Avvio rapido: Come iscriversi e caricare il primo video

Questa esercitazione introduttiva illustra come iscriversi a Video Indexer e come caricare il primo video.

Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuita) o un'opzione a pagamento (in cui non si è limitati dalla quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2400 minuti di indicizzazione gratuita per gli utenti di API. Con l'opzione a pagamento, si crea un account di Video Indexer [collegato alla sottoscrizione di Azure e un account di Servizi multimediali di Azure](connect-to-azure.md). Il pagamento viene effettuato per i minuti di indicizzazione, nonché in base ai costi correlati all'account di Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Iscriversi a Video Indexer

Per iniziare a eseguire attività di sviluppo con Video Indexer, visitare il sito Web di [Video Indexer](https://www.videoindexer.com) e iscriversi.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Caricare un video usando il sito Web di Video Indexer

> [!NOTE]
> Il nome del video non può contenere più di 80 caratteri.

### <a name="supported-file-formats-for-video-indexer"></a>Formati di file supportati per Video Indexer

Per un elenco dei formati di file che è possibile usare con Video Indexer, vedere l'articolo [Contenitore di input/formati di file](../latest/media-encoder-standard-formats.md#input-containerfile-formats).

### <a name="upload-a-video"></a>Caricare un video

1. Accedere al sito Web di [Video Indexer](https://www.videoindexer.ai/).
2. Per caricare un video, premere il pulsante o il collegamento **Upload** (Carica).

    ![Caricamento](./media/video-indexer-get-started/video-indexer-upload.png)

    Dopo il caricamento del video, Video Indexer avvia l'indicizzazione e l'analisi.

    ![Caricato](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Al termine dell'analisi si riceverà una notifica con un collegamento al video e una breve descrizione di ciò che è stato trovato nel video. Ad esempio: persone, argomenti, OCR.

## <a name="see-also"></a>Vedere anche

Per altre informazioni, vedere [Caricare e indicizzare video](upload-index-videos.md).

Dopo aver caricato e indicizzato un video, è possibile iniziare a usare il sito Web di [Video Indexer](video-indexer-use-apis.md) o il [portale per sviluppatori di Video Indexer](video-indexer-view-edit.md) per visualizzare le informazioni dettagliate del video. 

[Iniziare a usare le API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione dettagliata, vedere il [lab introduttivo](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Al termine del workshop si avrà una buona conoscenza del tipo di informazioni che possono essere estratte da contenuti video e audio, si sarà più preparati a identificare le opportunità correlate all'intelligence dei contenuti, sfruttare le tecnologie di intelligenza artificiale video in Azure e valutare diversi scenari in Video Indexer.

