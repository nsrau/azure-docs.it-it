---
title: Consentire l'accesso agli intervalli IP dell'indicizzatore
titleSuffix: Azure Cognitive Search
description: Guida che descrive come configurare le regole del firewall IP in modo che gli indicizzatori possano avere accesso.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463784"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Impostazione delle regole del firewall IP per abilitare l'accesso all'indicizzatore

Le regole del firewall IP per le risorse di Azure, ad esempio account di archiviazione, account Cosmos DB e server SQL di Azure, consentono solo il traffico originato da intervalli IP specifici per accedere ai dati.

Questo articolo descrive come configurare le regole IP, tramite portale di Azure, per un account di archiviazione in modo che gli indicizzatori di ricerca cognitiva di Azure possano accedere ai dati in modo sicuro. Per quanto riguarda l'archiviazione, questa guida può essere convertita direttamente in altre risorse di Azure che offrono anche le regole del firewall IP per la protezione dell'accesso ai dati.

> [!NOTE]
> Le regole del firewall IP per l'account di archiviazione sono valide solo se l'account di archiviazione e il servizio di ricerca si trovano in aree diverse. Se il programma di installazione non consente questo problema, è consigliabile utilizzare l' [opzione eccezione servizio attendibile](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Ottenere l'indirizzo IP del servizio di ricerca

Ottenere il nome di dominio completo (FQDN) del servizio di ricerca. L'aspetto sarà simile al seguente `<search-service-name>.search.windows.net` . È possibile trovare il nome di dominio completo cercando il servizio di ricerca nell'portale di Azure.

   ![Ottenere il nome di dominio completo del servizio](media\search-indexer-howto-secure-access\search-service-portal.png "Ottenere il nome di dominio completo del servizio")

È possibile ottenere l'indirizzo IP del servizio di ricerca eseguendo un `nslookup` (o un `ping` ) del nome di dominio completo. Si tratta di uno degli indirizzi IP da aggiungere alle regole del firewall.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Ottenere gli intervalli di indirizzi IP per il tag di servizio "AzureCognitiveSearch"

Gli intervalli di indirizzi IP per il `AzureCognitiveSearch` tag di servizio possono essere ottenuti tramite l' [API di individuazione (anteprima)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) o il [file JSON scaricabile](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Per questa procedura dettagliata, supponendo che il servizio di ricerca sia il cloud pubblico di Azure, è necessario scaricare il [file JSON pubblico di Azure](https://www.microsoft.com/download/details.aspx?id=56519) .

   ![Scarica file JSON](media\search-indexer-howto-secure-access\service-tag.png "Scarica file JSON")

Dal file JSON, supponendo che il servizio di ricerca si trovi negli Stati Uniti centro-occidentali, l'elenco di indirizzi IP per l'ambiente di esecuzione dell'indicizzatore multi-tenant è riportato di seguito.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Per gli indirizzi IP/32, eliminare il "/32" (52.253.133.74/32-> 52.253.133.74), mentre altri possono essere usati come Verbatim.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Aggiungere gli intervalli di indirizzi IP alle regole del firewall IP

Il modo più semplice per aggiungere gli intervalli di indirizzi IP alla regola del firewall di un account di archiviazione è tramite il portale di Azure. Individuare l'account di archiviazione nel portale e passare alla scheda "**firewall e reti virtuali**".

   ![Firewall e reti virtuali](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall e reti virtuali")

Aggiungere i tre indirizzi IP ottenuti in precedenza (1 per il servizio di ricerca IP, 2 per il `AzureCognitiveSearch` tag di servizio) nell'intervallo di indirizzi e fare clic su "**Salva**".

   ![Regole IP del firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Regole IP del firewall")

Le regole del firewall impostano 5-10 minuti per l'aggiornamento dopo gli indicizzatori che saranno in grado di accedere ai dati nell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come ottenere i due set di indirizzi IP per consentire l'accesso agli indici, usare i collegamenti seguenti per aggiornare le regole del firewall IP per alcune origini dati comuni.

- [Configurare i firewall di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [Configurare il firewall IP per CosmosDB](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Configurare il firewall IP per il server SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)