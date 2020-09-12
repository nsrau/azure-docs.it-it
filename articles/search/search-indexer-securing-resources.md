---
title: Accedere in modo sicuro alle risorse dell'indicizzatore
titleSuffix: Azure Cognitive Search
description: Panoramica concettuale delle opzioni di sicurezza a livello di rete per l'accesso ai dati di Azure da parte degli indicizzatori in Azure ricerca cognitiva.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 5075c4858f9584cb19442e19d9009d46d0e00ff8
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463713"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>Accesso dell'indicizzatore alle origini dati usando le funzionalità di sicurezza di rete di Azure

Gli indicizzatori di Azure ricerca cognitiva possono eseguire chiamate in uscita a varie risorse di Azure durante l'esecuzione. Questo articolo illustra i concetti alla base dell'accesso dell'indicizzatore alle risorse quando tali risorse sono protette da firewall IP, endpoint privati e altri meccanismi di sicurezza a livello di rete. I tipi di risorsa possibili a cui un indicizzatore può accedere in un'esecuzione tipica sono elencati nella tabella seguente.

| Risorsa | Scopo nell'esecuzione dell'indicizzatore |
| --- | --- |
| Archiviazione di Azure (BLOB, tabelle, ADLS gen 2) | Origine dati |
| Archiviazione di Azure (BLOB, tabelle) | Skillsets (memorizzazione nella cache di documenti arricchiti e archiviazione delle proiezioni dell'archivio informazioni) |
| Azure Cosmos DB (varie API) | Origine dati |
| Database SQL di Azure | Origine dati |
| SQL Server in macchine virtuali IaaS di Azure | Origine dati |
| Istanze gestite SQL | Origine dati |
| Funzioni di Azure | Host per le competenze personalizzate dell'API Web |
| Servizi cognitivi | Allegato a skillt che verrà usato per fatturare l'arricchimento oltre il limite di 20 documenti gratuiti |

> [!NOTE]
> La risorsa servizio cognitivo associata a un skillt viene utilizzata per la fatturazione, in base agli arricchimenti eseguiti e scritti nell'indice di ricerca. Non viene utilizzato per accedere al API Servizi cognitivi. L'accesso dalla pipeline di arricchimento di un indicizzatore a API Servizi cognitivi avviene tramite un canale di comunicazione protetto, in cui i dati vengono crittografati in modo sicuro in transito e non vengono mai archiviati inattivi.

I clienti possono proteggere queste risorse tramite diversi meccanismi di isolamento della rete offerti da Azure. Fatta eccezione per la risorsa del servizio cognitivo, gli indicizzatori hanno la possibilità di accedere a tutte le altre risorse anche se sono isolate in rete, come descritto nella tabella seguente.

| Risorsa | Restrizione IP | Endpoint privato |
| --- | --- | ---- |
| Archiviazione di Azure (BLOB, tabelle, ADLS gen 2) | Supportato solo se l'account di archiviazione e il servizio di ricerca si trovano in aree diverse | Funzionalità supportata |
| API Azure Cosmos DB-SQL | Funzionalità supportata | Funzionalità supportata |
| Azure Cosmos DB-Cassandra, Mongo e Gremlin API | Supportato | Non supportato |
| Database SQL di Azure | Funzionalità supportata | Funzionalità supportata |
| SQL Server in macchine virtuali IaaS di Azure | Funzionalità supportata | N/D |
| Istanze gestite SQL | Funzionalità supportata | N/D |
| Funzioni di Azure | Funzionalità supportata | Supportato, solo per determinati SKU di funzioni di Azure |

> [!NOTE]
> Oltre alle opzioni elencate in precedenza, per gli account di archiviazione di Azure protetti da rete, i clienti possono sfruttare il fatto che Azure ricerca cognitiva è un [servizio Microsoft attendibile](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services). Questo significa che un servizio di ricerca specifico può ignorare le restrizioni di rete virtuale o IP nell'account di archiviazione e può accedere ai dati nell'account di archiviazione, se il controllo degli accessi in base al ruolo appropriato è abilitato nell'account di archiviazione. I dettagli sono disponibili nella [Guida alle procedure](search-indexer-howto-access-trusted-service-exception.md). Questa opzione può essere usata al posto della route di restrizione IP, nel caso in cui non sia possibile spostare l'account di archiviazione o il servizio di ricerca in un'area diversa.

Quando si sceglie quale meccanismo di accesso sicuro deve essere usato da un indicizzatore, tenere presenti i vincoli seguenti:

- Gli [endpoint di servizio](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) non saranno supportati per le risorse di Azure.
- Non è possibile eseguire il provisioning di un servizio di ricerca in una rete virtuale specifica. questa funzionalità non verrà offerta da Azure ricerca cognitiva.
- Quando gli indicizzatori utilizzano endpoint privati (in uscita) per accedere alle risorse, possono essere applicati [addebiti](https://azure.microsoft.com/pricing/details/search/) aggiuntivi per i collegamenti privati.

## <a name="indexer-execution-environment"></a>Ambiente di esecuzione dell'indicizzatore

Gli indicizzatori di Azure ricerca cognitiva sono in grado di estrarre in modo efficiente il contenuto dalle origini dati, aggiungendo arricchimenti al contenuto estratto, generando facoltativamente le proiezioni prima di scrivere i risultati nell'indice di ricerca. A seconda del numero di responsabilità assegnate a un indicizzatore, può essere eseguito in uno dei due ambienti seguenti:

- Ambiente privato per un servizio di ricerca specifico. Gli indicizzatori in esecuzione in tali ambienti condividono risorse con altri carichi di lavoro, ad esempio l'indicizzazione o l'esecuzione di query di altri clienti. In genere, solo gli indicizzatori che non necessitano di molte risorse (ad esempio, non usano un skillt) vengono eseguiti in questo ambiente.
- Ambiente multi-tenant che ospita indicizzatori con risorse affamate, ad esempio quelli con un insieme di competenze. Le risorse affamate di risorse vengono eseguite in questo ambiente per offrire prestazioni ottimali garantendo al tempo stesso la disponibilità delle risorse del servizio di ricerca per altri carichi di lavoro. Questo ambiente multi-tenant è gestito e protetto da Azure ricerca cognitiva, senza costi aggiuntivi per il cliente.

Per ogni esecuzione dell'indicizzatore, Azure ricerca cognitiva determina l'ambiente migliore in cui eseguire l'indicizzatore.

## <a name="granting-access-to-indexer-ip-ranges"></a>Concessione dell'accesso agli intervalli IP dell'indicizzatore

Se la risorsa a cui l'indicizzatore tenta di accedere è limitata a un determinato set di intervalli IP, è necessario espandere il set per includere i possibili intervalli IP da cui può provenire una richiesta dell'indicizzatore. Come indicato in precedenza, esistono due possibili ambienti in cui vengono eseguiti gli indicizzatori e da cui possono provenire le richieste di accesso. Per il corretto funzionamento dell'indicizzatore, sarà necessario aggiungere gli indirizzi IP di __entrambi__ gli ambienti.

- Per ottenere l'indirizzo IP dell'ambiente privato specifico del servizio di ricerca, `nslookup` o `ping` il nome di dominio completo (FQDN) del servizio di ricerca. Il nome di dominio completo (FQDN) di un servizio di ricerca nel cloud pubblico, ad esempio, sarebbe `<service-name>.search.windows.net` . Queste informazioni sono disponibili nella portale di Azure.
- Gli indirizzi IP degli ambienti multi-tenant sono disponibili tramite il `AzureCognitiveSearch` tag del servizio. I [tag dei servizi di Azure](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) hanno un intervallo di indirizzi IP pubblicato per ogni servizio, disponibile tramite un'API di [individuazione (anteprima)](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) o un [file JSON scaricabile](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). In entrambi i casi, gli intervalli IP sono suddivisi in base all'area. è possibile selezionare solo gli intervalli IP assegnati per l'area in cui viene effettuato il provisioning del servizio di ricerca.

Per alcune origini dati, è possibile usare direttamente il tag del servizio anziché enumerare l'elenco di intervalli IP (l'indirizzo IP del servizio di ricerca deve comunque essere usato in modo esplicito). Queste origini dati limitano l'accesso tramite la configurazione di una [regola del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview)che supporta in modo nativo l'aggiunta di un tag di servizio, a differenza delle regole IP, ad esempio quelle offerte da archiviazione di Azure, CosmosDB, Azure SQL e così via, le origini dati che supportano la possibilità di usare il tag di `AzureCognitiveSearch` servizio direttamente oltre all'indirizzo IP del servizio di ricerca sono:

- [SQL Server in macchine virtuali IaaS](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers#restrict-access-to-the-azure-cognitive-search)

- [Istanze gestite di SQL](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers#verify-nsg-rules)

I dettagli sono descritti nella [Guida alla procedura](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Concessione dell'accesso tramite endpoint privati

Gli indicizzatori possono usare [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per accedere alle risorse. l'accesso a è bloccato per selezionare le reti virtuali o non è abilitato l'accesso pubblico.
Questa funzionalità è disponibile solo per i servizi a pagamento, con limiti per il numero di endpoint privati da creare. I dettagli sui limiti sono documentati nella [pagina limiti di ricerca di Azure](search-limits-quotas-capacity.md).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Passaggio 1: creare un endpoint privato per la risorsa protetta

I clienti devono chiamare l'operazione di gestione di ricerca, [creare o aggiornare l'API per le *risorse di collegamento privato condiviso* ](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) per creare una connessione di endpoint privato alla propria risorsa protetta, ad esempio un account di archiviazione. Il traffico che supera questa connessione all'endpoint privato (in uscita) verrà originato solo dalla rete virtuale presente nell'ambiente di esecuzione dell'indicizzatore "privato" specifico del servizio di ricerca.

Azure ricerca cognitiva convaliderà che i chiamanti di questa API dispongono delle autorizzazioni per approvare le richieste di connessione all'endpoint privato alla risorsa protetta. Se ad esempio si richiede una connessione a un endpoint privato a un account di archiviazione a cui non si ha accesso, questa chiamata verrà rifiutata.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Passaggio 2: approvare la connessione all'endpoint privato

Quando l'operazione (asincrona) che crea una risorsa di collegamento privato condivisa viene completata, viene creata una connessione all'endpoint privato in stato "in sospeso". Non viene ancora trasmesso alcun traffico sulla connessione.
Il cliente deve quindi individuare la richiesta sulla risorsa protetta e "approvarla". In genere, questa operazione può essere eseguita tramite il portale o tramite l' [API REST](https://docs.microsoft.com/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

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

Questi passaggi sono descritti in modo più dettagliato nella [Guida alle procedure](search-indexer-howto-access-private.md).
Quando si dispone di un endpoint privato approvato per una risorsa, gli indicizzatori impostati come *privati* tentano di ottenere l'accesso tramite la connessione all'endpoint privato.

### <a name="limits"></a>Limiti

Per garantire prestazioni e stabilità ottimali del servizio di ricerca, vengono imposte le restrizioni (mediante lo SKU del servizio di ricerca) sulle dimensioni seguenti:

- Tipi di indicizzatori che possono essere impostati come *privati*.
- Il numero di risorse di collegamento privato condivise che è possibile creare.
- Numero di tipi di risorse distinti per i quali è possibile creare risorse di collegamento privato condivise.

Questi limiti sono documentati in [limiti di servizio](search-limits-quotas-capacity.md).
