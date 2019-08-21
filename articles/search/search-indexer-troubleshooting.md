---
title: Risolvere i problemi comuni degli indicizzatori di ricerca - Ricerca di Azure
description: Correggere gli errori e i problemi comuni con gli indicizzatori in Ricerca di Azure, inclusi connessione all'origine dati, firewall e documenti mancanti.
author: mgottein
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.openlocfilehash: 4692be287e9b38cf116107d2e7c1043f23a6b34b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640604"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Risoluzione dei problemi comuni degli indicizzatori in Ricerca di Azure

Durante l'indicizzazione dei dati in Ricerca di Azure possono verificarsi diversi problemi relativi agli indicizzatori. Le principali categorie di errore includono:

* [Connessione a un'origine dati](#data-source-connection-errors)
* [Elaborazione di documenti](#document-processing-errors)
* [Inserimento di documenti in un indice](#index-errors)

## <a name="data-source-connection-errors"></a>Errori di connessione all'origine dati

### <a name="blob-storage"></a>Archiviazione BLOB

#### <a name="storage-account-firewall"></a>Firewall dell'account di archiviazione

Archiviazione di Azure fornisce un firewall configurabile. Per impostazione predefinita, il firewall è disabilitato, quindi Ricerca di Azure può connettersi all'account di archiviazione.

Non vengono visualizzati messaggi di errore specifici quando il firewall è abilitato. Gli errori del firewall in genere sono simili a `The remote server returned an error: (403) Forbidden`.

È possibile verificare che il firewall sia abilitato nel [portale](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). L'unica soluzione alternativa supportata consiste nel disabilitare il firewall scegliendo di consentire l'accesso da ["tutte le reti"](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Se per l'indicizzatore non è associato un valore di competenze, è _possibile_ tentare di [aggiungere un'eccezione](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) per gli indirizzi IP del servizio di ricerca. Tuttavia, questo scenario non è supportato e non è garantito che funzioni correttamente.

È possibile trovare l'indirizzo IP del servizio di ricerca eseguendo il ping del relativo FQDN (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>L'indicizzazione non è abilitata

Ricerca di Azure ha una dipendenza implicita dall'indicizzazione di Cosmos DB. Se si disattiva l'indicizzazione automatica in Cosmos DB, Ricerca di Azure restituisce uno stato di esito positivo, ma non riesce a indicizzare i contenuti dei contenitori. Per istruzioni su come controllare le impostazioni e attivare l'indicizzazione, vedere [Gestire l'indicizzazione in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Errori di elaborazione dei documenti

### <a name="unprocessable-or-unsupported-documents"></a>Documenti non elaborabili o non supportati

L'indicizzatore BLOB [registra i formati di documento supportati in modo esplicito](search-howto-indexing-azure-blob-storage.md#supported-document-formats). In alcuni casi, un contenitore di archiviazione BLOB contiene documenti non supportati. In altri casi potrebbero essere presenti documenti problematici. È possibile evitare di arrestare l'indicizzatore su questi documenti [modificando le opzioni di configurazione](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

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
* I [mapping dei campi](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) o la [ricerca cognitiva](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) hanno modificato il documento e hanno un aspetto diverso da quello previsto.
* Usare l'[API di ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/lookup-document) per trovare il documento.
