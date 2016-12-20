---
title: Come creare un processore multimediale | Microsoft Docs
description: Informazioni su come creare un componente del processore di contenuti multimediali per codificare, decodificare, convertire il formato, crittografare o decrittografare contenuti multimediali per Servizi multimediali di Azure. Negli esempi di codice, scritti in C#, viene usato Media Services SDK per .NET.
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc3c3bff6179b634b82884dfcb3f8f8395f6bb89


---
# <a name="how-to-get-a-media-processor-instance"></a>Procedura: Ottenere un'istanza del processore di contenuti multimediali
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Overview
In Servizi multimediali un processore di contenuti multimediali è un componente che gestisce un'attività di elaborazione specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuti multimediali. Un processore multimediale viene generalmente creato durante la creazione di un'attività per la codifica, la crittografia o la conversione di formato di contenuto multimediale.

Nella tabella seguente sono riportati il nome e la descrizione di tutti i processori multimediali disponibili.

| Nome del processore multimediale | Descrizione | Altre informazioni |
| --- | --- | --- |
| Codificatore multimediale standard |Fornisce funzionalità standard per la codifica su richiesta. |[Panoramica e confronto dei codificatori multimediali su richiesta di Azure](media-services-encode-asset.md) |
| Flusso di lavoro Premium del codificatore multimediale |Consente di eseguire attività di codifica usando il flusso di lavoro Premium del codificatore multimediale. |[Panoramica e confronto dei codificatori multimediali su richiesta di Azure](media-services-encode-asset.md) |
| Azure Media Indexer |Consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce e parole chiave per i sottotitoli codificati. |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (anteprima) |Consente di ridurre le imperfezioni del video con la stabilizzazione video. Consente inoltre di velocizzare il contenuto in una clip utilizzabile. |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Azure Media Encoder |Ammortizzato | |
| Storage Decryption |Ammortizzato | |
| Azure Media Packager |Ammortizzato | |
| Azure Media Encryptor |Ammortizzato | |

## <a name="get-media-processor"></a>Ottenere un processore di contenuti multimediali
Il seguente metodo illustra come ottenere un'istanza del processore di contenuti multimediali. Nell'esempio si suppone che si usi una variabile a livello di modulo denominata **_context** per fare riferimento al contesto del server descritto nella sezione [How to: Connect to Media Services Programmatically](media-services-dotnet-connect-programmatically.md) (Procedura: Connettersi a Servizi multimediali a livello di codice).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere ottenuto un'istanza del processore di contenuti multimediali, passare all'argomento [Come codificare un asset](media-services-dotnet-encode-with-media-encoder-standard.md) che illustra come usare Media Encoder Standard per codificare un asset.




<!--HONumber=Nov16_HO3-->


