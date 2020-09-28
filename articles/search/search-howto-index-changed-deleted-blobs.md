---
title: BLOB modificati ed eliminati
titleSuffix: Azure Cognitive Search
description: Dopo la compilazione di un indice di ricerca iniziale che importa dall'archivio BLOB di Azure, l'indicizzazione successiva può prelevare solo i BLOB modificati o eliminati. Questo articolo illustra i dettagli.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 210a4a352f5130bc67d70f47bdf6305c5a992282
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403780"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Come configurare il rilevamento delle modifiche e dell'eliminazione per i BLOB in Azure ricerca cognitiva indicizzazione

Dopo la creazione di un indice di ricerca iniziale, potrebbe essere necessario configurare i processi dell'indicizzatore successivi per prelevare solo i documenti creati o eliminati dall'esecuzione iniziale. Per il contenuto della ricerca originato dall'archiviazione BLOB di Azure, il rilevamento delle modifiche viene eseguito automaticamente quando si usa una pianificazione per attivare l'indicizzazione. Per impostazione predefinita, il servizio indicizza solo i BLOB modificati, come determinato dal timestamp del BLOB `LastModified` . Diversamente da altre origini dati supportate dagli indicizzatori di ricerca, i BLOB hanno sempre un timestamp, che elimina la necessità di configurare manualmente un criterio di rilevamento delle modifiche.

Sebbene il rilevamento delle modifiche sia specificato, il rilevamento dell'eliminazione non lo è. Se si vuole rilevare i documenti eliminati, assicurarsi di usare un approccio di "eliminazione temporanea". Se si eliminano completamente i BLOB, i documenti corrispondenti non verranno rimossi dall'indice della ricerca.

Esistono due modi per implementare l'approccio di eliminazione temporanea. Entrambi sono descritti di seguito.

## <a name="native-blob-soft-delete-preview"></a>Eliminazione temporanea BLOB nativa (anteprima)

> [!IMPORTANT]
> Il supporto per l'eliminazione temporanea dei BLOB nativi è in anteprima. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità è disponibile nell' [API REST versione 2020-06-30-Preview](./search-api-preview.md) . Attualmente non è disponibile alcun portale o supporto per .NET SDK.

> [!NOTE]
> Quando si usa il criterio di eliminazione temporanea BLOB nativo, le chiavi del documento per i documenti nell'indice devono essere una proprietà BLOB o i metadati del BLOB.

In questo metodo si userà la funzionalità di [eliminazione temporanea BLOB nativa](../storage/blobs/soft-delete-blob-overview.md) offerta dall'archiviazione BLOB di Azure. Se l'eliminazione temporanea del BLOB nativo è abilitata nell'account di archiviazione, l'origine dati include un set di criteri nativi di eliminazione temporanea e l'indicizzatore trova un BLOB che è stato passato a uno stato di eliminazione temporanea, l'indicizzatore rimuoverà tale documento dall'indice. I criteri di eliminazione temporanea BLOB nativi non sono supportati durante l'indicizzazione di BLOB da Azure Data Lake Storage Gen2.

Eseguire i passaggi seguenti:

1. Abilitare l' [eliminazione temporanea nativa per l'archiviazione BLOB di Azure](../storage/blobs/soft-delete-blob-overview.md). È consigliabile impostare i criteri di conservazione su un valore molto superiore rispetto alla pianificazione dell'intervallo dell'indicizzatore. In questo modo, se si verifica un problema durante l'esecuzione dell'indicizzatore o se si dispone di un numero elevato di documenti da indicizzare, l'indicizzatore potrebbe elaborare i BLOB eliminati temporaneamente. Gli indicizzatori di Azure ricerca cognitiva elimineranno un documento dall'indice solo se elabora il BLOB mentre si trova in uno stato di eliminazione temporanea.

1. Configurare i criteri di rilevamento dell'eliminazione temporanea dei BLOB nativi nell'origine dati. Di seguito è illustrato un esempio. Poiché questa funzionalità è in anteprima, è necessario usare l'API REST di anteprima.

1. Eseguire l'indicizzatore o impostare l'indicizzatore per l'esecuzione in base a una pianificazione. Quando l'indicizzatore viene eseguito ed elabora il BLOB, il documento verrà rimosso dall'indice.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>Reindicizzazione di BLOB non eliminati (usando i criteri nativi di eliminazione temporanea)

Se si elimina un BLOB dall'archiviazione BLOB di Azure con l'eliminazione temporanea nativa abilitata nell'account di archiviazione, il BLOB passerà a uno stato eliminato temporaneamente, consentendo di annullare l'eliminazione del BLOB entro il periodo di conservazione. Se si inverte un'eliminazione dopo l'elaborazione dell'indicizzatore, l'indicizzatore non indurrà sempre il BLOB ripristinato. Questo perché l'indicizzatore determina i BLOB da indicizzare in base al timestamp del BLOB `LastModified` . Quando un BLOB eliminato temporaneamente non viene eliminato, il timestamp non viene `LastModified` aggiornato, quindi se l'indicizzatore ha già elaborato BLOB con timestamp più recenti `LastModified` , non verrà reindicizzato il BLOB non eliminato. 

Per assicurarsi che un BLOB non eliminato venga reindicizzato, sarà necessario aggiornare il timestamp del BLOB `LastModified` . Un modo per eseguire questa operazione consiste nel risalvare i metadati del BLOB. Non è necessario modificare i metadati, ma se si risalvano i metadati, il timestamp del BLOB verrà aggiornato in `LastModified` modo che l'indicizzatore sappia che è necessario reindicizzare il BLOB.

## <a name="soft-delete-using-custom-metadata"></a>Eliminazione temporanea mediante metadati personalizzati

In questo metodo verranno utilizzati i metadati di un BLOB per indicare quando è necessario rimuovere un documento dall'indice di ricerca. Questo metodo richiede due azioni separate, eliminando il documento di ricerca dall'indice, seguito dall'eliminazione del BLOB in archiviazione di Azure.

Eseguire i passaggi seguenti:

1. Aggiungere una coppia chiave-valore dei metadati personalizzata al BLOB per indicare ad Azure ricerca cognitiva che è stata eliminata logicamente.

1. Configurare un criterio di rilevamento della colonna di eliminazione temporanea nell'origine dati. Di seguito è illustrato un esempio.

1. Dopo che l'indicizzatore ha elaborato il BLOB ed eliminato il documento dall'indice, è possibile eliminare il BLOB nell'archivio BLOB di Azure.

Il criterio illustrato sotto, ad esempio, considera l'eliminazione di un BLOB se ha una proprietà di metadati `IsDeleted` con il valore `true`:

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>Reindicizzazione di BLOB non eliminati (usando metadati personalizzati)

Dopo l'elaborazione di un BLOB eliminato da parte di un indicizzatore e la rimozione del documento di ricerca corrispondente dall'indice, il BLOB non verrà rivisitato se viene ripristinato in un secondo momento se il timestamp del BLOB `LastModified` è antecedente all'ultima esecuzione dell'indicizzatore.

Se si vuole reindicizzare il documento, modificare per il `"softDeleteMarkerValue" : "false"` BLOB e rieseguire l'indicizzatore.

## <a name="see-also"></a>Vedere anche

* [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
* [Come configurare un indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md)
* [Panoramica dell'indicizzazione BLOB](search-blob-storage-integration.md)