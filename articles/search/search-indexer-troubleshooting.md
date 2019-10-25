---
title: Risolvere i problemi comuni dell'indicizzatore di ricerca
titleSuffix: Azure Cognitive Search
description: Correzione di errori e problemi comuni con gli indicizzatori in Azure ricerca cognitiva, tra cui la connessione all'origine dati, il firewall e i documenti mancanti.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793618"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Risoluzione dei problemi comuni dell'indicizzatore in Azure ricerca cognitiva

Gli indicizzatori possono rientrare in una serie di problemi durante l'indicizzazione dei dati in Azure ricerca cognitiva. Le principali categorie di errore includono:

* [Connessione a un'origine dati](#data-source-connection-errors)
* [Elaborazione di documenti](#document-processing-errors)
* [Inserimento di documenti in un indice](#index-errors)

## <a name="data-source-connection-errors"></a>Errori di connessione all'origine dati

### <a name="blob-storage"></a>Archiviazione BLOB

#### <a name="storage-account-firewall"></a>Firewall dell'account di archiviazione

Archiviazione di Azure fornisce un firewall configurabile. Per impostazione predefinita, il firewall è disabilitato in modo che Azure ricerca cognitiva possa connettersi all'account di archiviazione.

Non vengono visualizzati messaggi di errore specifici quando il firewall è abilitato. Gli errori del firewall in genere sono simili a `The remote server returned an error: (403) Forbidden`.

È possibile verificare che il firewall sia abilitato nel [portale](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). L'unica soluzione alternativa supportata consiste nel disabilitare il firewall scegliendo di consentire l'accesso da ["tutte le reti"](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Se per l'indicizzatore non è associato un valore di competenze, è _possibile_ tentare di [aggiungere un'eccezione](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) per gli indirizzi IP del servizio di ricerca. Tuttavia, questo scenario non è supportato e non è garantito che funzioni correttamente.

È possibile trovare l'indirizzo IP del servizio di ricerca eseguendo il ping del relativo FQDN (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>L'indicizzazione non è abilitata

Il ricerca cognitiva di Azure ha una dipendenza implicita dall'indicizzazione Cosmos DB. Se si disattiva l'indicizzazione automatica in Cosmos DB, Azure ricerca cognitiva restituisce uno stato di esito positivo, ma non riesce a indicizzare il contenuto del contenitore. Per istruzioni su come controllare le impostazioni e attivare l'indicizzazione, vedere [Gestire l'indicizzazione in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Errori di elaborazione dei documenti

### <a name="unprocessable-or-unsupported-documents"></a>Documenti non elaborabili o non supportati

L'indicizzatore BLOB [registra i formati di documento supportati in modo esplicito](search-howto-indexing-azure-blob-storage.md#SupportedFormats). In alcuni casi, un contenitore di archiviazione BLOB contiene documenti non supportati. In altri casi potrebbero essere presenti documenti problematici. È possibile evitare di arrestare l'indicizzatore su questi documenti [modificando le opzioni di configurazione](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Contenuto del documento mancante

L'indicizzatore BLOB [trova ed estrae il testo dai BLOB in un contenitore](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). I problemi relativi all'estrazione del testo includono i seguenti:

* Il documento contiene solo immagini digitalizzate. I BLOB PDF con contenuti non testuali, ad esempio immagini digitalizzate (JPG), non producono risultati in una pipeline di indicizzazione BLOB standard. Se si dispone di contenuto di immagine con elementi di testo, è possibile usare la [ricerca cognitiva](cognitive-search-concept-image-scenarios.md) per trovare ed estrarre il testo.
* L'indicizzatore BLOB è configurato solo per i metadati dell'indice. Per estrarre il contenuto, l'indicizzatore BLOB deve essere configurato per [estrarre sia il contenuto che i metadati](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Errori di indice

### <a name="missing-documents"></a>Documenti mancanti

Gli indicizzatori trovano i documenti da un'[origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source). In alcuni casi un documento dell'origine dati che dovrebbe essere stato indicizzato non è presente in un indice. Esistono alcuni motivi comuni per cui questi errori possono verificarsi:

* Il documento non è stato indicizzato. Cercare nel portale un'esecuzione dell'indicizzatore riuscita.
* Il documento è stato aggiornato dopo l'esecuzione dell'indicizzatore. Se l'indicizzatore fa parte di una [pianificazione](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), a un certo punto verrà eseguito nuovamente e selezionerà il documento.
* La [query](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) specificata nell'origine dati esclude il documento. Gli indicizzatori non possono indicizzare i documenti che non fanno parte dell'origine dati.
* I [mapping dei campi](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) o l' [arricchimento di intelligenza artificiale](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) hanno modificato il documento e hanno un aspetto diverso dal previsto.
* Usare l'[API di ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/lookup-document) per trovare il documento.
