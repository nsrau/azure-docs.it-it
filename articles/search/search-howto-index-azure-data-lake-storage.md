---
title: Ricerca in Azure Data Lake Storage Gen2 (anteprima)Search over Azure Data Lake Storage Gen2 (preview)
titleSuffix: Azure Cognitive Search
description: Informazioni su come indicizzare contenuto e metadati in Azure Data Lake Storage Gen2. Questa funzione è attualmente in anteprima pubblica
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905653"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indicizzazione di documenti in Azure Data Lake Storage Gen2Indexing documents in Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Il supporto di Azure Data Lake Storage Gen2 è attualmente in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). È possibile richiedere l'accesso alle anteprime compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Attualmente non è disponibile alcun supporto per il portale o .NET SDK.


Quando si configura un account di archiviazione di Azure, è possibile abilitare [lo spazio dei nomi gerarchico.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) In questo modo la raccolta di contenuto in un account può essere organizzata in una gerarchia di directory e sottodirectory nidificate. Abilitando lo spazio dei nomi gerarchico, si abilita [Archiviazione di Azure Data Lake Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

Questo articolo descrive come iniziare a usare l'indicizzazione dei documenti disponibili in Azure Data Lake Storage Gen2.This article describes how to get started with indexing documents that are in Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Configurare l'indicizzatore Gen2 di Archiviazione di Azure Data Lake

Per indicizzare il contenuto da Data Lake Storage Gen2, è necessario completare alcuni passaggi.

### <a name="step-1-sign-up-for-the-preview"></a>Passaggio 1: iscriviti all'anteprima

Iscriversi all'anteprima dell'indicizzatore Data Lake Storage Gen2 compilando [questo modulo.](https://aka.ms/azure-cognitive-search/indexer-preview) Riceverai un'e-mail di conferma una volta che sarai stato accettato nell'anteprima.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Passaggio 2: Seguire i passaggi di configurazione dell'indicizzazione dell'archiviazione BLOB di AzureStep 2: Follow the Azure Blob indexing setup steps

Dopo aver ricevuto la conferma che l'iscrizione all'anteprima è stata eseguita correttamente, è possibile creare la pipeline di indicizzazione.

È possibile indicizzare il contenuto e i metadati da Data Lake Storage Gen2 usando [l'API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

L'indicizzazione del contenuto in Data Lake Storage Gen2 è identica all'indicizzazione del contenuto nell'archiviazione BLOB di Azure.Indexing content in Data Lake Storage Gen2 is identical to indexing content in Azure Blob storage. Per tanto informazioni su come configurare l'origine dati, l'indice e l'indicizzatore di Data Lake Storage Gen2, vedere [Come indicizzare](search-howto-indexing-azure-blob-storage.md)documenti in Archiviazione BLOB di Azure con Ricerca cognitiva di Azure. L'articolo Archiviazione BLOB fornisce inoltre informazioni sui formati di documento supportati, sulle proprietà dei metadati BLOB estratte, sull'indicizzazione incrementale e altro ancora. Queste informazioni saranno le stesse per Data Lake Storage Gen2.

## <a name="access-control"></a>Controllo di accesso

Azure Data Lake Storage Gen2 implementa un modello di controllo di [accesso](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) che supporta sia il controllo degli accessi in base al ruolo di Azure che gli elenchi di controllo di accesso (ACL) di tipo POSIX. Quando si indicizza il contenuto da Data Lake Storage Gen2, Ricerca cognitiva di Azure non estrarrà le informazioni sul controllo degli accessi in base al ruolo e degli ACL dal contenuto. Di conseguenza, queste informazioni non verranno incluse nell'indice di Ricerca cognitiva di Azure.As a result, this information will not be included in your Azure Cognitive Search index.

Se il mantenimento del controllo di accesso su ogni documento nell'indice è importante, spetta allo sviluppatore dell'applicazione implementare la limitazione per [motivi di sicurezza.](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)

## <a name="change-detection"></a>Rilevamento delle modifiche

L'indicizzatore Gen2 di Data Lake Storage supporta il rilevamento delle modifiche. Ciò significa che quando l'indicizzatore viene eseguito reindicizza solo `LastModified` i BLOB modificati come determinato dal timestamp del BLOB.

> [!NOTE] 
> Data Lake Storage Gen2 consente di rinominare le directory. Quando una directory viene rinominata, i timestamp per i BLOB in tale directory non vengono aggiornati. Di conseguenza, l'indicizzatore non reindicizzerà tali BLOB. Se è necessario che i BLOB in una directory vengano reindicizzati dopo una ridenominazione `LastModified` della directory perché ora hanno nuovi URL, sarà necessario aggiornare il timestamp per tutti i BLOB nella directory in modo che l'indicizzatore sappia reindicizzarli durante un'esecuzione futura.
