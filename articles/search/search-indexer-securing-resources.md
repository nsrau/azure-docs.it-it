---
title: Accesso dell'indicizzatore alle risorse protette
titleSuffix: Azure Cognitive Search
description: Panoramica concettuale delle opzioni di sicurezza a livello di rete per l'accesso ai dati di Azure da parte degli indicizzatori in Azure ricerca cognitiva.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503488"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Accesso dell'indicizzatore al contenuto protetto dalle funzionalità di sicurezza di rete di Azure (Azure ricerca cognitiva)

Gli indicizzatori di Azure ricerca cognitiva possono eseguire chiamate in uscita a varie risorse di Azure durante l'esecuzione. Questo articolo illustra i concetti alla base dell'accesso dell'indicizzatore ai contenuti protetti da firewall IP, endpoint privati o altri meccanismi di sicurezza a livello di rete di Azure. Un indicizzatore esegue chiamate in uscita in due situazioni: la connessione a origini dati durante l'indicizzazione e la connessione a codice incapsulato tramite un skillt. Nella tabella seguente è riportato un elenco di tutti i possibili tipi di risorse a cui un indicizzatore può accedere in una tipica esecuzione.

| Risorsa | Scopo nell'esecuzione dell'indicizzatore |
| --- | --- |
| Archiviazione di Azure (BLOB, tabelle, ADLS gen 2) | Origine dati |
| Archiviazione di Azure (BLOB, tabelle) | Skillsets (memorizzazione nella cache di documenti arricchiti e archiviazione delle proiezioni dell'archivio informazioni) |
| Azure Cosmos DB (varie API) | Origine dati |
| database SQL di Azure | Origine dati |
| SQL Server in Macchine virtuali di Azure | Origine dati |
| Istanza gestita di SQL | Origine dati |
| Funzioni di Azure | Host per le competenze personalizzate dell'API Web |
| Servizi cognitivi | Allegato a skillt che verrà usato per fatturare l'arricchimento oltre il limite di 20 documenti gratuiti |

> [!NOTE]
> La risorsa servizio cognitivo associata a un skillt viene utilizzata per la fatturazione, in base agli arricchimenti eseguiti e scritti nell'indice di ricerca. Non viene utilizzato per accedere al API Servizi cognitivi. L'accesso dalla pipeline di arricchimento di un indicizzatore a API Servizi cognitivi avviene tramite un canale di comunicazione protetto interno, in cui i dati vengono crittografati in transito e non vengono mai archiviati inattivi.

I clienti possono proteggere queste risorse tramite diversi meccanismi di isolamento della rete offerti da Azure. Fatta eccezione per una risorsa di servizio cognitivo, gli indicizzatori hanno la possibilità di accedere a tutte le altre risorse anche se sono isolate dalla rete, descritte nella tabella seguente.

| Risorsa | Restrizione IP | Endpoint privato |
| --- | --- | ---- |
| Archiviazione di Azure (BLOB, tabelle, ADLS gen 2) | Supportato solo se l'account di archiviazione e il servizio di ricerca si trovano in aree diverse | Supportato |
| API Azure Cosmos DB-SQL | Supportato | Supportato |
| Azure Cosmos DB-Cassandra, Mongo e Gremlin API | Supportato | Non supportato |
| database SQL di Azure | Supportato | Supportato |
| SQL Server in Macchine virtuali di Azure | Supportato | N/D |
| Istanza gestita di SQL | Supportato | N/D |
| Funzioni di Azure | Supportato | Supportato, solo per determinati livelli di funzioni di Azure |

> [!NOTE]
> Oltre alle opzioni elencate in precedenza, per gli account di archiviazione di Azure protetti da rete, i clienti possono sfruttare il fatto che Azure ricerca cognitiva è un [servizio Microsoft attendibile](../storage/common/storage-network-security.md#trusted-microsoft-services). Questo significa che un servizio di ricerca specifico può ignorare le restrizioni di rete virtuale o IP nell'account di archiviazione e può accedere ai dati nell'account di archiviazione, se il controllo degli accessi in base al ruolo appropriato è abilitato nell'account di archiviazione. Per altre informazioni, vedere [connessioni indicizzatore con l'eccezione del servizio attendibile](search-indexer-howto-access-trusted-service-exception.md). Questa opzione può essere usata al posto della route di restrizione IP, nel caso in cui non sia possibile spostare l'account di archiviazione o il servizio di ricerca in un'area diversa.

Quando si sceglie quale meccanismo di accesso sicuro deve essere usato da un indicizzatore, tenere presenti i vincoli seguenti:

- Un indicizzatore non è in grado di connettersi a un [endpoint del servizio rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md). Gli endpoint pubblici con credenziali, endpoint privati, servizio attendibile e indirizzi IP sono le uniche metodologie supportate per le connessioni dell'indicizzatore.
- Non è possibile eseguire il provisioning di un servizio di ricerca in una rete virtuale specifica, in esecuzione in modalità nativa in una macchina virtuale. Questa funzionalità non verrà offerta da Azure ricerca cognitiva.
- Quando gli indicizzatori utilizzano endpoint privati (in uscita) per accedere alle risorse, possono essere applicati [addebiti](https://azure.microsoft.com/pricing/details/search/) aggiuntivi per i collegamenti privati.

## <a name="indexer-execution-environment"></a>Ambiente di esecuzione dell'indicizzatore

Gli indicizzatori di Azure ricerca cognitiva sono in grado di estrarre in modo efficiente il contenuto dalle origini dati, aggiungendo arricchimenti al contenuto estratto, generando facoltativamente le proiezioni prima di scrivere i risultati nell'indice di ricerca. A seconda del numero di responsabilità assegnate a un indicizzatore, può essere eseguito in uno dei due ambienti seguenti:

- Ambiente privato per un servizio di ricerca specifico. Gli indicizzatori in esecuzione in tali ambienti condividono risorse con altri carichi di lavoro, ad esempio altri carichi di lavoro di indicizzazione o query avviati dal cliente. In genere, solo gli indicizzatori che eseguono l'indicizzazione basata su testo (ad esempio, non usano un skillt) vengono eseguiti in questo ambiente.
- Ambiente multi-tenant che ospita indicizzatori che richiedono un utilizzo intensivo delle risorse, ad esempio quelli con skillsets. Questo ambiente viene utilizzato per l'offload dell'elaborazione a elevato utilizzo di calcolo, lasciando disponibili risorse specifiche per il servizio per le operazioni di routine. Questo ambiente multi-tenant è gestito e protetto da Microsoft, senza costi aggiuntivi per il cliente.

Per ogni esecuzione dell'indicizzatore, Azure ricerca cognitiva determina l'ambiente migliore in cui eseguire l'indicizzatore. Se si usa un firewall IP per controllare l'accesso alle risorse di Azure, la conoscenza degli ambienti di esecuzione consentirà di configurare un intervallo di indirizzi IP compreso tra loro.

## <a name="granting-access-to-indexer-ip-ranges"></a>Concessione dell'accesso agli intervalli IP dell'indicizzatore

Se la risorsa a cui l'indicizzatore tenta di accedere è limitata a un determinato set di intervalli IP, è necessario espandere il set per includere i possibili intervalli IP da cui può provenire una richiesta dell'indicizzatore. Come indicato in precedenza, esistono due possibili ambienti in cui vengono eseguiti gli indicizzatori e da cui possono provenire le richieste di accesso. Per il corretto funzionamento dell'indicizzatore, sarà necessario aggiungere gli indirizzi IP di **entrambi** gli ambienti.

- Per ottenere l'indirizzo IP dell'ambiente privato specifico del servizio di ricerca, `nslookup` o `ping` il nome di dominio completo (FQDN) del servizio di ricerca. Ad esempio, il nome di dominio completo (FQDN) di un servizio di ricerca nel cloud pubblico è `<service-name>.search.windows.net` . Queste informazioni sono disponibili nella portale di Azure.
- Gli indirizzi IP degli ambienti multi-tenant sono disponibili tramite il `AzureCognitiveSearch` tag del servizio. I [tag dei servizi di Azure](../virtual-network/service-tags-overview.md) hanno un intervallo di indirizzi IP pubblicato per ogni servizio, disponibile tramite un'API di [individuazione (anteprima)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) o un [file JSON scaricabile](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). In entrambi i casi, gli intervalli IP sono suddivisi in base all'area. è possibile selezionare solo gli intervalli IP assegnati per l'area in cui viene effettuato il provisioning del servizio di ricerca.

Per alcune origini dati, è possibile usare direttamente il tag del servizio anziché enumerare l'elenco di intervalli IP (l'indirizzo IP del servizio di ricerca deve comunque essere usato in modo esplicito). Queste origini dati limitano l'accesso tramite la configurazione di una [regola del gruppo di sicurezza di rete](../virtual-network/network-security-groups-overview.md), che supporta in modo nativo l'aggiunta di un tag di servizio, a differenza delle regole IP, ad esempio quelle offerte da archiviazione di azure, Cosmos DB, Azure SQL e così via. Le origini dati che supportano la possibilità di utilizzare il `AzureCognitiveSearch` tag di servizio direttamente oltre all'indirizzo IP del servizio di ricerca sono:

- [SQL Server in macchine virtuali di Azure](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [Istanze gestite SQL](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Per altre informazioni su questa opzione di connettività, vedere [connessioni a indicizzatore tramite un firewall IP](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Concessione dell'accesso tramite endpoint privati

Gli indicizzatori possono usare [endpoint privati](../private-link/private-endpoint-overview.md) per accedere alle risorse. l'accesso a è bloccato per selezionare le reti virtuali o non è abilitato l'accesso pubblico.
Questa funzionalità è disponibile solo nei servizi di ricerca fatturabili, con limiti sul numero di endpoint privati da creare. Per altre informazioni, vedere [limiti del servizio](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Passaggio 1: creare un endpoint privato per la risorsa protetta

I clienti devono chiamare l'operazione di gestione di ricerca, l' [API CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) su una **risorsa di collegamento privato condiviso**, per creare una connessione all'endpoint privato alla propria risorsa protetta, ad esempio un account di archiviazione. Il traffico che supera questa connessione all'endpoint privato (in uscita) verrà originato solo dalla rete virtuale presente nell'ambiente di esecuzione dell'indicizzatore "privato" specifico del servizio di ricerca.

Azure ricerca cognitiva convaliderà che i chiamanti di questa API hanno le autorizzazioni RBAC di Azure per approvare le richieste di connessione all'endpoint privato alla risorsa protetta. Se ad esempio si richiede una connessione a un endpoint privato a un account di archiviazione con autorizzazioni di sola lettura, la chiamata verrà rifiutata.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Passaggio 2: approvare la connessione all'endpoint privato

Quando l'operazione (asincrona) che crea una risorsa di collegamento privato condivisa viene completata, viene creata una connessione all'endpoint privato in stato "in sospeso". Non viene ancora trasmesso alcun traffico sulla connessione.
Il cliente deve quindi individuare la richiesta sulla risorsa protetta e "approvarla". In genere, questa operazione può essere eseguita tramite il portale di Azure o tramite l' [API REST](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Passaggio 3: forzare l'esecuzione degli indicizzatori nell'ambiente "privato"

Un endpoint privato approvato consente le chiamate in uscita dal servizio di ricerca a una risorsa che ha una forma di restrizioni di accesso a livello di rete (ad esempio, un'origine dati dell'account di archiviazione configurata per l'accesso solo da determinate reti virtuali) per avere esito positivo.
Ciò significa che qualsiasi indicizzatore in grado di raggiungere tale origine dati sull'endpoint privato avrà esito positivo.
Se l'endpoint privato non è approvato o se l'indicizzatore non usa la connessione all'endpoint privato, l'esecuzione dell'indicizzatore verrà terminata in `transientFailure` .

Per consentire agli indicizzatori di accedere alle risorse tramite connessioni a endpoint privati, è obbligatorio impostare la `executionEnvironment` dell'indicizzatore su `"Private"` per garantire che tutte le esecuzioni dell'indicizzatore siano in grado di utilizzare l'endpoint privato. Questo perché gli endpoint privati vengono sottoposti a provisioning all'interno dell'ambiente specifico del servizio di ricerca privato.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Questi passaggi sono descritti in modo più dettagliato nelle [connessioni dell'indicizzatore tramite un endpoint privato](search-indexer-howto-access-private.md).
Quando si dispone di un endpoint privato approvato per una risorsa, gli indicizzatori impostati come *privati* tentano di ottenere l'accesso tramite la connessione all'endpoint privato.

### <a name="limits"></a>Limiti

Per garantire prestazioni e stabilità ottimali del servizio di ricerca, vengono imposte restrizioni (dal livello di servizio di ricerca) sulle dimensioni seguenti:

- Tipi di indicizzatori che possono essere impostati come *privati*.
- Il numero di risorse di collegamento privato condivise che è possibile creare.
- Numero di tipi di risorse distinti per i quali è possibile creare risorse di collegamento privato condivise.

Questi limiti sono documentati in [limiti di servizio](search-limits-quotas-capacity.md).

## <a name="next-steps"></a>Passaggi successivi

- [Connessioni di indicizzatore tramite firewall IP](search-indexer-howto-access-ip-restricted.md)
- [Connessioni dell'indicizzatore con l'eccezione del servizio attendibile](search-indexer-howto-access-trusted-service-exception.md)
- [Connessioni dell'indicizzatore a un endpoint privato](search-indexer-howto-access-private.md)