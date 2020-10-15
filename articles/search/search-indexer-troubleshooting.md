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
ms.openlocfilehash: 7eadc9121c54b636fa8b42579284d4018043e1c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355126"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Risoluzione dei problemi comuni dell'indicizzatore in Azure ricerca cognitiva

Gli indicizzatori possono rientrare in una serie di problemi durante l'indicizzazione dei dati in Azure ricerca cognitiva. Le principali categorie di errore includono:

* [Connessione a un'origine dati o ad altre risorse](#connection-errors)
* [Elaborazione di documenti](#document-processing-errors)
* [Inserimento di documenti in un indice](#index-errors)

## <a name="connection-errors"></a>Errori di connessione

> [!NOTE]
> Gli indicizzatori hanno un supporto limitato per l'accesso alle origini dati e ad altre risorse protette dai meccanismi di sicurezza di rete di Azure. Attualmente, gli indicizzatori possono accedere alle origini dati solo tramite meccanismi di restrizione dell'intervallo di indirizzi IP corrispondenti o regole NSG quando applicabile. I dettagli per accedere a ogni origine dati supportata sono disponibili di seguito.
>
> È possibile trovare l'indirizzo IP del servizio di ricerca eseguendo il ping del nome di dominio completo (ad esempio, `<your-search-service-name>.search.windows.net` ).
>
> È possibile trovare l'intervallo di indirizzi IP del `AzureCognitiveSearch` [tag di servizio](../virtual-network/service-tags-overview.md#available-service-tags) usando [file JSON scaricabili](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) o tramite l' [API di individuazione dei tag di servizio](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). L'intervallo di indirizzi IP viene aggiornato settimanalmente.

### <a name="configure-firewall-rules"></a>Configurare le regole del firewall

Archiviazione di Azure, CosmosDB e SQL di Azure forniscono un firewall configurabile. Non vengono visualizzati messaggi di errore specifici quando il firewall è abilitato. In genere, gli errori del firewall sono generici e hanno un aspetto simile a `The remote server returned an error: (403) Forbidden` o `Credentials provided in the connection string are invalid or have expired` .

Sono disponibili 2 opzioni per consentire agli indicizzatori di accedere a queste risorse in un'istanza di questo tipo:

* Disabilitare il firewall consentendo l'accesso da **tutte le reti** (se possibile).
* In alternativa, è possibile consentire l'accesso per l'indirizzo IP del servizio di ricerca e l'intervallo di indirizzi IP del `AzureCognitiveSearch` [tag di servizio](../virtual-network/service-tags-overview.md#available-service-tags) nelle regole del firewall della risorsa (restrizione intervallo di indirizzi IP).

Per informazioni dettagliate sulla configurazione delle restrizioni dell'intervallo di indirizzi IP per ogni tipo di origine dati, vedere i collegamenti seguenti:

* [Archiviazione di Azure](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [SQL di Azure](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitazione**: come indicato nella documentazione precedente per archiviazione di Azure, le restrizioni per l'intervallo di indirizzi IP funzioneranno solo se il servizio di ricerca e l'account di archiviazione si trovano in aree diverse.

Le funzioni di Azure (che potrebbero essere usate come [competenze personalizzate dell'API Web](cognitive-search-custom-skill-web-api.md)) supportano anche le [restrizioni degli indirizzi IP](../azure-functions/ip-addresses.md#ip-address-restrictions). L'elenco di indirizzi IP da configurare corrisponde all'indirizzo IP del servizio di ricerca e all'intervallo di indirizzi IP del `AzureCognitiveSearch` tag di servizio.

Per informazioni dettagliate sull'accesso ai dati in SQL Server in una macchina virtuale di Azure, vedere [qui](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configurare le regole del gruppo di sicurezza di rete (NSG)

Quando si accede ai dati in un'istanza gestita di SQL o quando si usa una macchina virtuale di Azure come URI del servizio Web per una [competenza API Web personalizzata](cognitive-search-custom-skill-web-api.md), i clienti non devono preoccuparsi di indirizzi IP specifici.

In questi casi, la macchina virtuale di Azure o l'istanza gestita di SQL può essere configurata in modo che risieda in una rete virtuale. Quindi, è possibile configurare un gruppo di sicurezza di rete per filtrare il tipo di traffico di rete che può fluire in entrata e in uscita dalle subnet della rete virtuale e dalle interfacce di rete.

Il `AzureCognitiveSearch` tag di servizio può essere usato direttamente nelle regole del [NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) in ingresso senza dover cercare l'intervallo di indirizzi IP.

Ulteriori dettagli sull'accesso ai dati in un'istanza gestita di SQL sono illustrati di [seguito](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>L'indicizzazione di CosmosDB non è abilitata

Il ricerca cognitiva di Azure ha una dipendenza implicita dall'indicizzazione Cosmos DB. Se si disattiva l'indicizzazione automatica in Cosmos DB, Azure ricerca cognitiva restituisce uno stato di esito positivo, ma non riesce a indicizzare il contenuto del contenitore. Per istruzioni su come controllare le impostazioni e attivare l'indicizzazione, vedere [Gestire l'indicizzazione in Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## <a name="document-processing-errors"></a>Errori di elaborazione dei documenti

### <a name="unprocessable-or-unsupported-documents"></a>Documenti non elaborabili o non supportati

L'indicizzatore BLOB [registra i formati di documento supportati in modo esplicito](search-howto-indexing-azure-blob-storage.md#SupportedFormats). In alcuni casi, un contenitore di archiviazione BLOB contiene documenti non supportati. In altri casi potrebbero essere presenti documenti problematici. È possibile evitare di arrestare l'indicizzatore su questi documenti [modificando le opzioni di configurazione](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
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
* L'indicizzatore BLOB è configurato solo per i metadati dell'indice. Per estrarre il contenuto, l'indicizzatore BLOB deve essere configurato per [estrarre sia il contenuto che i metadati](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Errori di indice

### <a name="missing-documents"></a>Documenti mancanti

Gli indicizzatori trovano i documenti da un'[origine dati](/rest/api/searchservice/create-data-source). In alcuni casi un documento dell'origine dati che dovrebbe essere stato indicizzato non è presente in un indice. Esistono alcuni motivi comuni per cui questi errori possono verificarsi:

* Il documento non è stato indicizzato. Cercare nel portale un'esecuzione dell'indicizzatore riuscita.
* Controllare il valore di [rilevamento delle modifiche](/rest/api/searchservice/create-data-source#data-change-detection-policies) . Se il valore del limite massimo è una data impostata su un'ora futura, eventuali documenti con una data inferiore a questa verranno ignorati dall'indicizzatore. È possibile comprendere lo stato di rilevamento delle modifiche dell'indicizzatore usando i campi ' initialTrackingState ' è finalTrackingState ' nello [stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Il documento è stato aggiornato dopo l'esecuzione dell'indicizzatore. Se l'indicizzatore fa parte di una [pianificazione](/rest/api/searchservice/create-indexer#indexer-schedule), a un certo punto verrà eseguito nuovamente e selezionerà il documento.
* La [query](/rest/api/searchservice/create-data-source) specificata nell'origine dati esclude il documento. Gli indicizzatori non possono indicizzare i documenti che non fanno parte dell'origine dati.
* I [mapping dei campi](/rest/api/searchservice/create-indexer#fieldmappings) o l' [arricchimento di intelligenza artificiale](./cognitive-search-concept-intro.md) hanno modificato il documento e hanno un aspetto diverso dal previsto.
* Usare l'[API di ricerca documenti](/rest/api/searchservice/lookup-document) per trovare il documento.