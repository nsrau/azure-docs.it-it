---
title: Consentire l'accesso agli intervalli IP dell'indicizzatore
titleSuffix: Azure Cognitive Search
description: Configurare le regole del firewall IP per consentire l'accesso ai dati da un indicizzatore di Azure ricerca cognitiva.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0be69b72cc068d017202b0694e24fb4573172dba
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101393"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Configurare le regole del firewall IP per consentire le connessioni all'indicizzatore (ricerca cognitiva di Azure)

Le regole del firewall IP per le risorse di Azure, ad esempio account di archiviazione, account Cosmos DB e server SQL di Azure, consentono solo il traffico originato da intervalli IP specifici per accedere ai dati.

Questo articolo descrive come configurare le regole IP, tramite portale di Azure, per un account di archiviazione in modo che gli indicizzatori di ricerca cognitiva di Azure possano accedere ai dati in modo sicuro. Sebbene sia specifico per archiviazione di Azure, l'approccio funziona anche per altre risorse di Azure che usano regole del firewall IP per la sicurezza dell'accesso ai dati.

> [!NOTE]
> Le regole del firewall IP per l'account di archiviazione sono valide solo se l'account di archiviazione e il servizio di ricerca si trovano in aree diverse. Se il programma di installazione non consente questo problema, è consigliabile utilizzare l' [opzione eccezione servizio attendibile](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Ottenere l'indirizzo IP del servizio di ricerca

Ottenere il nome di dominio completo (FQDN) del servizio di ricerca. L'aspetto sarà simile al seguente `<search-service-name>.search.windows.net` . È possibile trovare il nome di dominio completo cercando il servizio di ricerca nell'portale di Azure.

   ![Ottenere il nome di dominio completo del servizio](media\search-indexer-howto-secure-access\search-service-portal.png "Ottenere il nome di dominio completo del servizio")

È possibile ottenere l'indirizzo IP del servizio di ricerca eseguendo un `nslookup` (o un `ping` ) del nome di dominio completo. Nell'esempio seguente viene aggiunto "10.50.10.50" a una regola in ingresso nel firewall di archiviazione di Azure.

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

Gli indirizzi IP aggiuntivi vengono usati per le richieste originate dall'ambiente di [esecuzione multi-tenant](search-indexer-securing-resources.md#indexer-execution-environment)dell'indicizzatore. È possibile ottenere questo intervallo di indirizzi IP dal tag del servizio.

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

Il modo più semplice per aggiungere gli intervalli di indirizzi IP alla regola del firewall di un account di archiviazione è tramite il portale di Azure. Individuare l'account di archiviazione nel portale e passare alla scheda **firewall e reti virtuali** .

   ![Firewall e reti virtuali](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall e reti virtuali")

Aggiungere i tre indirizzi IP ottenuti in precedenza (1 per il servizio di ricerca IP, 2 per il `AzureCognitiveSearch` tag di servizio) nell'intervallo di indirizzi e selezionare **Salva**.

   ![Regole IP del firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Regole IP del firewall")

Le regole del firewall importano 5-10 minuti per essere aggiornate, dopo le quali gli indicizzatori devono poter accedere ai dati nell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i firewall di archiviazione di Azure](../storage/common/storage-network-security.md)
- [Configurare il firewall IP per Cosmos DB](../cosmos-db/firewall-support.md)
- [Configurare il firewall IP per Azure SQL Server](../azure-sql/database/firewall-configure.md)