---
title: Quote del servizio Azure Cosmos DBAzure Cosmos DB service quotas
description: Quote del servizio Azure Cosmos DB e limiti predefiniti per tipi di risorse diversi.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: bd384f2b4dd879c30fafd2b765dd9ba0efb12447
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348400"
---
# <a name="azure-cosmos-db-service-quotas"></a>Quote del servizio Azure Cosmos DBAzure Cosmos DB service quotas

Questo articolo fornisce una panoramica delle quote predefinite offerte a risorse diverse in Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Archiviazione e velocità effettiva

Dopo aver creato un account Azure Cosmos nella sottoscrizione, è possibile gestire i dati nell'account [creando database, contenitori ed elementi.](databases-containers-items.md) È possibile eseguire il provisioning della velocità effettiva a livello di contenitore o di database in termini di unità di [richiesta (RU/s o RU).](request-units.md) Nella tabella seguente sono elencati i limiti per l'archiviazione e la velocità effettiva per contenitore/database.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di RU per contenitore[(modalità di provisioning della velocità effettiva dedicata)](databases-containers-items.md#azure-cosmos-containers) | 1.000.000 per impostazione predefinita. È possibile aumentarlo [archiviando un ticket](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) di supporto di AzureYou can increase it by filing an Azure support ticket |
| Numero massimo di RU per database[(modalità](databases-containers-items.md#azure-cosmos-containers)di provisioning della velocità effettiva condivisa) | 1.000.000 per impostazione predefinita. È possibile aumentarlo [archiviando un ticket](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) di supporto di AzureYou can increase it by filing an Azure support ticket |
| Numero massimo di RU per chiave di partizione (logica) | 10,000 |
| Spazio di archiviazione massimo in tutti gli elementi per chiave di partizione (logica) Maximum storage across all items per (logical) partition key| 20 GB |
| Numero massimo di chiavi di partizione distinte (logiche) | Nessuna limitazione |
| Spazio di archiviazione massimo per contenitoreMaximum storage per container | Nessuna limitazione |
| Spazio di archiviazione massimo per databaseMaximum storage per database | Nessuna limitazione |
| Dimensione massima degli allegati per account (la funzione Allegato è in fase di ammortamento) | 2 GB |
| Minimo RU richiesto per 1 GB | 10 RU/s |

> [!NOTE]
> Per informazioni sulle procedure consigliate per la gestione di carichi di lavoro con chiavi di partizione che richiedono limiti più elevati per l'archiviazione o la velocità effettiva, vedere Creare una chiave di [partizione sintetica.](synthetic-partition-keys.md)
>

Un contenitore Cosmos (o database con velocità effettiva condivisa) deve avere una velocità effettiva minima di 400 RU. Con l'aumentare del contenitore, la velocità effettiva minima supportata dipende anche dai fattori seguenti:As the container grows, the minimum supported throughput also depends on the following factors:

* La velocità effettiva minima che è possibile impostare in un contenitore dipende dalla velocità effettiva massima mai sottoposta a provisioning nel contenitore. Ad esempio, se la velocità effettiva è stata aumentata a 10000 RU, la velocità effettiva con provisioning più bassa possibile sarà 1000 RUFor example, if the throughput was increased to 10000 RUs, then the lowest possible provisioned throughput would be 1000 RU
* La velocità effettiva minima in un database della velocità effettiva condivisa dipende anche dal numero totale di contenitori creati in un database con velocità effettiva condivisa, misurato in 100 unità di ruper per contenitore. Ad esempio, se sono stati creati cinque contenitori all'interno di un database della velocità effettiva condivisa, la velocità effettiva deve essere di almeno 500 RU

La velocità effettiva corrente e minima di un contenitore o di un database può essere recuperata dal portale di Azure o dagli SDK. Per ulteriori informazioni, vedere [Effettuare il provisioning della velocità effettiva in contenitori e database](set-throughput.md). 

> [!NOTE]
> In alcuni casi, potrebbe essere possibile ridurre la velocità effettiva a un valore inferiore al 10%. Usare l'API per ottenere le RU minime esatte per ogni contenitore.
>

In sintesi, di seguito sono riportati i limiti RU minimi di cui è stato eseguito il provisioning. 

| Risorsa | Limite predefinito |
| --- | --- |
| RUs minimo per contenitore[(modalità di provisioning della velocità effettiva dedicata)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| RUs minime per database[(modalità](databases-containers-items.md#azure-cosmos-containers)di provisioning della velocità effettiva condivisa) | 400 |
| Numero minimo di RU per contenitore all'interno di un database di velocità effettiva condivisaMinimum RUs per container within a shared throughput database | 100 |

Cosmos DB supporta il ridimensionamento elastico della velocità effettiva (RU) per contenitore o database tramite gli SDK o il portale. Ogni contenitore può essere ridimensionato in modo sincrono e immediato in un intervallo di scalabilità compreso tra 10 e 100 volte, tra i valori minimo e massimo. Se il valore della velocità effettiva richiesta non è compreso nell'intervallo, il ridimensionamento viene eseguito in modo asincrono. Il completamento del ridimensionamento asincrono può richiedere da minuti a ore a seconda della velocità effettiva richiesta e delle dimensioni di archiviazione dei dati nel contenitore.  

## <a name="control-plane-operations"></a>Controllare le operazioni dei piani

È possibile [eseguire il provisioning e gestire l'account Azure Cosmos](how-to-manage-database-account.md) usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure e i modelli di Azure Resource Manager.You can provision and manage your Azure Cosmos account using the Azure portal, Azure PowerShell, Azure CLI, and Azure Resource Manager templates. Nella tabella seguente sono elencati i limiti per sottoscrizione, account e numero di operazioni.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di account di database per sottoscrizioneMaximum database accounts per subscription | 50 per impostazione predefinita. È possibile aumentarlo [archiviando un ticket](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) di supporto di AzureYou can increase it by filing an Azure support ticket|
| Numero massimo di failover regionali | 1/ora per impostazione predefinita. È possibile aumentarlo [archiviando un ticket](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) di supporto di AzureYou can increase it by filing an Azure support ticket|

> [!NOTE]
> I failover regionali si applicano solo agli account di scrittura di singole aree. Gli account di scrittura in più aree non richiedono o hanno limiti per modificare l'area di scrittura.

Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. Per informazioni dettagliate sugli intervalli di conservazione dei backup e sulle finestre, vedere Backup in linea e ripristino dei [dati su richiesta in Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).

## <a name="per-account-limits"></a>Limiti per account

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di database | Nessuna limitazione |
| Numero massimo di contenitori per database con velocità effettiva condivisaMaximum number of containers per database with shared throughput |25 |
| Numero massimo di contenitori per database o account con velocità effettiva dedicataMaximum number of containers per database or account with dedicated throughput  |senza limiti |
| Numero massimo di regioni | Nessun limite (tutte le aree di Azure)No limit (All Azure regions) |

## <a name="per-container-limits"></a>Limiti per contenitore

A seconda dell'API usata, un contenitore Cosmos di Azure può rappresentare una raccolta, una tabella o un grafico. I contenitori supportano configurazioni per [vincoli di chiave univoca,](unique-keys.md) [stored procedure, trigger e funzioni definite dall'utente](stored-procedures-triggers-udfs.md)e [criteri di indicizzazione.](how-to-manage-indexing-policy.md) Nella tabella seguente sono elencati i limiti specifici delle configurazioni all'interno di un contenitore. 

| Risorsa | Limite predefinito |
| --- | --- |
| Lunghezza massima del nome del database o del contenitoreMaximum length of database or container name | 255 |
| Numero massimo di stored procedure per contenitoreMaximum stored procedures per container | 100<sup>*</sup>|
| Numero massimo di funzioni definite dall'utente per contenitore | 25<sup>*</sup>|
| Numero massimo di percorsi nei criteri di indicizzazioneMaximum number of paths in indexing policy| 100<sup>*</sup>|
| Numero massimo di chiavi univoche per contenitoreMaximum number of unique keys per container|10<sup>*</sup>|
| Numero massimo di percorsi per vincolo di chiave univoca|16<sup>*</sup>|

<sup>*</sup>È possibile aumentare uno qualsiasi di questi limiti per contenitore contattando il supporto di Azure.You can increase any of these per-container limits by contacting Azure Support.

## <a name="per-item-limits"></a>Limiti per elemento

A seconda dell'API usata, un elemento Cosmos di Azure può rappresentare un documento in una raccolta, una riga in una tabella o un nodo o un bordo in un grafico. La tabella seguente mostra i limiti per elemento in Cosmos DB. 

| Risorsa | Limite predefinito |
| --- | --- |
| Dimensione massima di un elemento | 2 MB (lunghezza UTF-8 della rappresentazione JSON) |
| Lunghezza massima del valore della chiave di partizioneMaximum length of partition key value | 2048 byte |
| Lunghezza massima del valore id | 1023 byte |
| Numero massimo di proprietà per elemento | Nessun limite pratico |
| Profondità di nidificazione massima | Nessun limite pratico |
| Lunghezza massima del nome della proprietà | Nessun limite pratico |
| Lunghezza massima del valore della proprietà | Nessun limite pratico |
| Lunghezza massima del valore della proprietà stringaMaximum length of string property value | Nessun limite pratico |
| Lunghezza massima del valore della proprietà numerica | IEEE754 a precisione doppia a 64 bit |

Non esistono restrizioni sui payload degli elementi come il numero di proprietà e la profondità di annidamento, ad eccezione delle restrizioni di lunghezza sui valori di chiave di partizione e id e la restrizione di dimensione complessiva di 2 MB. Potrebbe essere necessario configurare i criteri di indicizzazione per i contenitori con strutture articoli di grandi dimensioni o complesse per ridurre il consumo di RU. Vedere [Modellazione di elementi in Cosmos DB](how-to-model-partition-example.md) per un esempio reale e modelli per gestire elementi di grandi dimensioni.

## <a name="per-request-limits"></a>Limiti per richiesta

Il database Cosmos di Azure supporta il [CRUD e le operazioni](https://docs.microsoft.com/rest/api/cosmos-db/) di query su risorse come contenitori, elementi e database. Supporta inoltre [le richieste batch transazionali](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) su più elementi con la stessa chiave di partizione in un contenitore.

| Risorsa | Limite predefinito |
| --- | --- |
| Tempo massimo di esecuzione per una singola operazione,ad esempio l'esecuzione di una stored procedure o il recupero di una singola pagina di query| 5 secondi |
| Dimensione massima richiesta (ad esempio, stored procedure, CRUD)| 2 MB |
| Dimensione massima della risposta (ad esempio, query impaginata) | 4 MB |
| Numero massimo di operazioni in un batch transazionale | 100 |

Quando un'operazione come query raggiunge il timeout di esecuzione o il limite di dimensione della risposta, restituisce una pagina di risultati e un token di continuazione al client per riprendere l'esecuzione. Non esiste alcun limite pratico alla durata di esecuzione di una singola query tra pagine/continuazioni.

Cosmos DB utilizza HMAC per l'autorizzazione. È possibile usare una chiave master o un token di [risorsa](secure-access-to-data.md) per un controllo di accesso con granularità fine a risorse come contenitori, chiavi di partizione o elementi. Nella tabella seguente sono elencati i limiti per i token di autorizzazione in Cosmos DB.

| Risorsa | Limite predefinito |
| --- | --- |
| Tempo massimo di scadenza del token masterMaximum master token expiry time | 15 min  |
| Tempo minimo di scadenza del token di risorsa | 10 min  |
| Tempo massimo di scadenza del token di risorsaMaximum resource token expiry time | 24 h per impostazione predefinita. È possibile aumentarlo [archiviando un ticket](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) di supporto di AzureYou can increase it by filing an Azure support ticket|
| Asimmetria massima dell'orologio per l'autorizzazione dei tokenMaximum clock skew for token authorization| 15 min |

Cosmos DB supporta l'esecuzione di trigger durante le scritture. Il servizio supporta un massimo di un pre-trigger e un post-trigger per ogni operazione di scrittura. 

## <a name="autopilot-mode-limits"></a>Limiti della modalità Autopilot

Vedere l'articolo [Autopilot](provision-throughput-autopilot.md#autopilot-limits) per i limiti di velocità effettiva e archiviazione in modalità pilota automatico.

## <a name="sql-query-limits"></a>Limiti delle query SQL

Cosmos DB supporta l'esecuzione di query su elementi tramite [SQL](how-to-sql-query.md). Nella tabella seguente vengono descritte le restrizioni nelle istruzioni di query, ad esempio in termini di numero di clausole o lunghezza della query.

| Risorsa | Limite predefinito |
| --- | --- |
| Lunghezza massima della query SQL| 256 KB |
| Numero massimo di VALORI DI ANR per query| 5<sup>*</sup>|
| Numero massimo di funzioni definite dall'utente per query| 10<sup>*</sup>|
| Numero massimo di punti per poligono| 4096 |

<sup>*</sup>È possibile aumentare questi limiti di query SQL contattando il supporto di Azure.You can increase these SQL query limits by contacting Azure Support.

## <a name="mongodb-api-specific-limits"></a>Limiti specifici dell'API MongoDB

Cosmos DB supporta il protocollo wire MongoDB per le applicazioni scritte su MongoDB. I comandi e le versioni dei protocolli supportati sono disponibili in [Funzionalità e sintassi MongoDB supportate](mongodb-feature-support.md).

Nella tabella seguente sono elencati i limiti specifici del supporto delle funzionalità MongoDB. Altri limiti del servizio menzionati per l'API SQL (core) si applicano anche all'API MongoDB.

| Risorsa | Limite predefinito |
| --- | --- |
| Dimensione massima memoria query MongoDB | 40 MB |
| Tempo massimo di esecuzione per le operazioni MongoDBMaximum execution time for MongoDB operations| 30 secondi |
| Timeout di connessione inattiva per la chiusura della connessione sul lato server | 30 minuti |

\*È consigliabile che le applicazioni client impostino il timeout di connessione inattiva nelle impostazioni del driver su 2-3 minuti perché il [timeout predefinito per Azure LoadBalancer è 4 minuti.](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)  Questo timeout garantirà che le connessioni inattive non vengano chiuse da un servizio di bilanciamento del carico intermedio tra il computer client e Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Prova i limiti gratuiti di Cosmos DB

Nella tabella seguente sono elencati i limiti per il server [Try Azure Cosmos DB per](https://azure.microsoft.com/try/cosmosdb/) la versione di valutazione gratuita.

| Risorsa | Limite predefinito |
| --- | --- |
| Durata del periodo di prova | 30 giorni (può essere rinnovato un numero qualsiasi di volte) |
| Numero massimo di contenitori per sottoscrizione (SQL, Gremlin, Table API) | 1 |
| Numero massimo di contenitori per sottoscrizione (API MongoDB)Maximum containers per subscription (MongoDB API) | 3 |
| Velocità effettiva massima per contenitoreMaximum throughput per container | 5000 |
| Velocità effettiva massima per database con velocità effettiva condivisaMaximum throughput per shared-throughput database | 20000 |
| Spazio di archiviazione totale massimo per accountMaximum total storage per account | 10 GB |

Prova Cosmos DB supporta la distribuzione globale solo nelle regioni degli Stati Uniti centrali, dell'Europa settentrionale e del sud-est asiatico. Non è possibile creare ticket di supporto di Azure per gli account Try Azure Cosmos DB. Tuttavia, viene fornito il supporto per gli abbonati con piani di supporto esistenti.

## <a name="free-tier-account-limits"></a>Limiti dell'account di livello gratuito
Nella tabella seguente sono elencati i limiti per gli account di livello gratuito di [Azure Cosmos DB.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di livello gratuito per ogni sottoscrizione di AzureNumber of free tier accounts per Azure subscription | 1 |
| Durata dello sconto di livello libero | Durata dell'account. Deve acconsentire esplicitamente durante la creazione dell'account. |
| RU/s massimo gratis | 400 UR/sec |
| Massimo spazio di archiviazione gratuito | 5 GB |
| Numero massimo di database della velocità effettiva condivisa | 5 |
| Numero massimo di contenitori in un database con velocità effettiva condivisaMaximum number of containers in a shared throughput database | 25 <br>Negli account di livello gratuito, la RU/s minima per un database di velocità effettiva condivisa con un massimo di 25 contenitori è 400 RU/s.In free tier accounts, the minimum RU/s for a shared throughput database with up to 25 containers is 400 RU/s. |

  Oltre a quanto sopra, i [limiti per account](#per-account-limits) si applicano anche agli account di livello gratuito.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui concetti fondamentali di Cosmos DB [riguardano la distribuzione](distribute-data-globally.md) globale e il [partizionamento](partitioning-overview.md) e la velocità effettiva di [cui è stato eseguito](request-units.md)il provisioning.

Per un'introduzione ad Azure Cosmos DB, fare riferimento alle guide introduttive seguenti:

* [Introduzione all'API SQL di Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introduzione all'API di Azure Cosmos DB per MongoDB](create-mongodb-nodejs.md)
* [Introduzione all'API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introduzione all'API Gremlin di Azure Cosmos DB](create-graph-dotnet.md)
* [Introduzione all'API Tabelle di Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Prova Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
