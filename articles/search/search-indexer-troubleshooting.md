---
title: Risolvere i problemi comuni relativi all'indicizzatore di ricercaTroubleshoot common search indexer issues
titleSuffix: Azure Cognitive Search
description: Risolvere gli errori e i problemi comuni con gli indicizzatori in Ricerca cognitiva di Azure, tra cui la connessione all'origine dati, il firewall e i documenti mancanti.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190925"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Risoluzione dei problemi comuni degli indicizzatori in Ricerca cognitiva di AzureTroubleshooting common indexer issues in Azure Cognitive Search

Gli indicizzatori possono incorrere in una serie di problemi durante l'indicizzazione dei dati in Ricerca cognitiva di Azure.Indexers can in issues in a number of issues when indexing data into Azure Cognitive Search. Le principali categorie di errore includono:

* [Connessione a un'origine dati o altre risorseConnecting to a data source or other resources](#connection-errors)
* [Elaborazione di documenti](#document-processing-errors)
* [Inserimento di documenti in un indice](#index-errors)

## <a name="connection-errors"></a>Errori di connessione

> [!NOTE]
> Gli indicizzatori hanno un supporto limitato per l'accesso alle origini dati e ad altre risorse protette dai meccanismi di sicurezza di rete di Azure.Indexers have limited support for accessing data sources and other resources that are secured by Azure network security mechanisms. Attualmente, gli indicizzatori possono accedere alle origini dati solo tramite i meccanismi di restrizione dell'intervallo di indirizzi IP o le regole del gruppo di sicurezza di rete, se applicabile. Di seguito sono riportati i dettagli per l'accesso a ogni origine dati supportata.
>
> È possibile scoprire l'indirizzo IP del servizio di ricerca eseguendo il `<your-search-service-name>.search.windows.net`ping del nome di dominio completo (ad esempio, ).
>
> È possibile individuare l'intervallo di indirizzi IP del `AzureCognitiveSearch` [tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) del servizio utilizzando [i file JSON scaricabili](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) o tramite l'API [di individuazione dei tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)del servizio . L'intervallo di indirizzi IP viene aggiornato settimanalmente.

### <a name="configure-firewall-rules"></a>Configurare le regole del firewall

Archiviazione di Azure, CosmosDB e Azure SQL forniscono un firewall configurabile. Non vengono visualizzati messaggi di errore specifici quando il firewall è abilitato. In genere, gli errori `The remote server returned an error: (403) Forbidden` del `Credentials provided in the connection string are invalid or have expired`firewall sono generici e hanno un aspetto o .

Sono disponibili due opzioni per consentire agli indicizzatori di accedere a queste risorse in un'istanza di questo tipo:There are 2 options for allowing indexers to access these resources in such an instance:

* Disattivare il firewall, consentendo l'accesso da **Tutte le reti** (se possibile).
* In alternativa, è possibile consentire l'accesso per l'indirizzo `AzureCognitiveSearch` IP del servizio di ricerca e l'intervallo di indirizzi IP del tag dell'intervallo di [servizi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) nelle regole del firewall della risorsa (restrizione dell'intervallo di indirizzi IP).

I dettagli per la configurazione delle restrizioni dell'intervallo di indirizzi IP per ogni tipo di origine dati sono disponibili nei collegamenti seguenti:Details for configuring IP address range restrictions for each data source type can found from the following links:

* [Archiviazione di AzureAzure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [SQL di AzureAzure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Limitazione:** come indicato nella documentazione precedente per Archiviazione di Azure, le restrizioni dell'intervallo di indirizzi IP funzioneranno solo se il servizio di ricerca e l'account di archiviazione si trovano in aree diverse.

Le funzioni di Azure (che possono essere usate come [competenza Api Web personalizzata)](cognitive-search-custom-skill-web-api.md)supportano anche le restrizioni degli [indirizzi IP.](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions) L'elenco di indirizzi IP da configurare è l'indirizzo IP `AzureCognitiveSearch` del servizio di ricerca e l'intervallo di indirizzi IP del tag del servizio.

I dettagli per l'accesso ai dati in SQL Server in una macchina virtuale di Azure sono descritti [di seguitoDetails](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) for accessing data in SQL server on an Azure VM are outlined here outlined here

### <a name="configure-network-security-group-nsg-rules"></a>Configurare le regole del gruppo di sicurezza di rete

Quando si accede ai dati in un'istanza gestita SQL o quando una macchina virtuale di Azure viene usata come URI del servizio Web per una [competenza Api Web personalizzata,](cognitive-search-custom-skill-web-api.md)i clienti non devono preoccuparsi di indirizzi IP specifici.

In questi casi, la macchina virtuale di Azure o l'istanza gestita SQL può essere configurata per risiedere all'interno di una rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete per filtrare il tipo di traffico di rete che può entrare ed uscire dalle subnet di rete virtuali e dalle interfacce di rete.

Il `AzureCognitiveSearch` tag di servizio può essere utilizzato direttamente nelle regole del gruppo di [sicurezza](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) di rete in ingresso senza dover cercare l'intervallo di indirizzi IP.

Ulteriori dettagli per l'accesso ai dati in un'istanza gestita SQL sono descritti [di seguito](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indicizzazione" non è abilitato

Ricerca cognitiva di Azure ha una dipendenza implicita dall'indicizzazione del database Cosmos.Azure Cognitive Search has an implicit dependency on Cosmos DB indexing. Se si disattiva l'indicizzazione automatica in Cosmos DB, Ricerca cognitiva di Azure restituisce uno stato di esito positivo, ma non riesce a indicizzare il contenuto del contenitore. Per istruzioni su come controllare le impostazioni e attivare l'indicizzazione, vedere [Gestire l'indicizzazione in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

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

* Il documento contiene solo immagini digitalizzate. I BLOB PDF con contenuti non testuali, ad esempio immagini digitalizzate (JPG), non producono risultati in una pipeline di indicizzazione BLOB standard. Se si dispone di contenuto immagine con elementi di testo, è possibile utilizzare la [ricerca cognitiva](cognitive-search-concept-image-scenarios.md) per trovare ed estrarre il testo.
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
* La [query](/rest/api/searchservice/create-data-source) specificata nell'origine dati esclude il documento. Gli indicizzatori non possono indicizzare i documenti che non fanno parte dell'origine dati.
* [I mapping dei campi](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) o l'arricchimento [dell'IA](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) hanno modificato il documento e l'aspetto è diverso da quello previsto.
* Usare l'[API di ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/lookup-document) per trovare il documento.
