---
title: Limiti di Azure Cosmos DB
description: Questo articolo descrive i limiti di Azure Cosmos DB.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2019
ms.openlocfilehash: 28eb7c6a11f71fa87835bcfe78e635753965bac3
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561240"
---
# <a name="limits-in-azure-cosmos-db"></a>Limiti di Azure Cosmos DB

Questo articolo offre una panoramica dei limiti del servizio di Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Archiviazione e velocità effettiva

Dopo aver creato un account Azure Cosmos nella propria sottoscrizione, è possibile gestire i dati nel tuo account entro [creazione di database, contenitori ed elementi](databases-containers-items.md). È possibile eseguire il provisioning della velocità effettiva a livello di contenitore o di un database in termini di [unità (unità richiesta al secondo o UR) richiesta](request-units.md). Nella tabella seguente sono elencate le limitazioni per l'archiviazione e velocità effettiva per ogni contenitore/database.

| Resource | Limite predefinito |
| --- | --- |
| Unità richieste massime per ogni contenitore ([modalità di provisioning della velocità effettiva dedicata](databases-containers-items.md#azure-cosmos-containers)) | 1\.000.000 per impostazione predefinita. È possibile aumentarlo dal [invio di un ticket di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) o contattare Microsoft tramite [chiedere a Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Unità richieste massime per ogni database ([modalità di provisioning di velocità effettiva condiviso](databases-containers-items.md#azure-cosmos-containers)) | 1\.000.000 per impostazione predefinita. È possibile aumentarlo dal [invio di un ticket di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) o contattare Microsoft tramite [chiedere a Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Unità richieste massime per ogni chiave di partizione (logici) | 10,000 |
| Spazio di archiviazione massimo tra tutti gli elementi per chiave di partizione (logici)| 10 GB |
| Numero massimo di chiavi di partizione distinta (logici) | Illimitato |
| Spazio di archiviazione massimo per ogni contenitore | Illimitato |
| Spazio di archiviazione massimo per ogni database | Illimitato |

> [!NOTE]
> Per le procedure consigliate gestire i carichi di lavoro che dispongono di chiavi di partizione che richiedono limiti più elevati per l'archiviazione o velocità effettiva, vedere [progetta per accesso frequente le chiavi di partizione](synthetic-partition-keys.md)
>

Un contenitore Cosmos (o condivisa della velocità effettiva database) deve avere una velocità effettiva minima di 400 UR. Man mano che aumenta il contenitore, anche la velocità effettiva supportata minima dipende dai fattori seguenti:

* La velocità effettiva massima che mai eseguito il provisioning del contenitore. Il servizio supporta la riduzione della velocità effettiva di un contenitore al 10% rispetto al numero massimo di provisioning. Ad esempio, se la velocità effettiva è stata aumentata a 10000 UR, quindi la velocità effettiva con provisioning possibili più bassa sarà 1000 UR
* Il numero totale di contenitori che hanno già creato in un database condiviso della velocità effettiva, misurata in 100 unità richieste per ogni contenitore. Ad esempio, se è stato creato cinque contenitori all'interno di un database condiviso della velocità effettiva, quindi la velocità effettiva necessario almeno 500 UR

La velocità effettiva corrente e minima di un contenitore o un database può essere recuperata dal portale di Azure o gli SDK. Per altre informazioni, vedere [velocità effettiva di provisioning per i contenitori e i database](set-throughput.md). In sintesi, ecco i limiti di unità richiesta con provisioning minima. 

| Resource | Limite predefinito |
| --- | --- |
| Unità minima richiesta per ogni contenitore ([modalità di provisioning della velocità effettiva dedicata](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Unità minima richiesta per ogni database ([modalità di provisioning di velocità effettiva condiviso](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Unità minima richiesta per ogni contenitore all'interno di un database condiviso della velocità effettiva | 100 |

COSMOS DB supporta la scalabilità elastica della velocità effettiva (UR) al contenitore o un database tramite il SDK o il portale. Ogni contenitore è possibile scalare in modo sincrono e immediatamente all'interno di un intervallo di scalabilità di 10 a 100 volte, tra i valori minimi e massimo. Se il valore di velocità effettiva richiesta non rientra nell'intervallo, il ridimensionamento viene eseguito in modo asincrono. Ridimensionamento asincrono può richiedere minuti da ore a seconda della velocità effettiva richiesta e dimensioni di archiviazione di dati nel contenitore.  

## <a name="control-plane-operations"></a>Operazioni del piano di controllo

È possibile [effettuare il provisioning e gestire il proprio account Azure Cosmos](how-to-manage-database-account.md) usando il portale di Azure, Azure PowerShell, CLI di Azure e i modelli Azure Resource Manager. La tabella seguente elenca i limiti per il numero di operazioni di sottoscrizione e account.

| Resource | Limite predefinito |
| --- | --- |
| Account massime del database per ogni sottoscrizione | 50 per impostazione predefinita. È possibile aumentarlo dal [invio di un ticket di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) o contattare Microsoft tramite [chiedere a Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Numero massimo di failover regionali | 1 ora per impostazione predefinita. È possibile aumentarlo dal [invio di un ticket di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) o contattare Microsoft tramite [chiedere a Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> Failover regionali si applicano solo agli account di scrittura in una singola area. Gli account di scrittura in più aree non richiedono né hanno limiti su come modificare l'area di scrittura.

COSMOS DB esegue automaticamente il backup dei dati a intervalli regolari. Per informazioni dettagliate su windows e gli intervalli di conservazione dei backup, vedere [ripristino il backup in linea e i dati on demand in Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-container-limits"></a>Limiti per ogni contenitore

A seconda di quali API utilizzare, un contenitore di Azure Cosmos può rappresentare in una raccolta, una tabella, o del grafico. I contenitori supportano le configurazioni per [vincoli di chiave univoca](unique-keys.md), [stored procedure, trigger e funzioni definite dall'utente](stored-procedures-triggers-udfs.md), e [criteri di indicizzazione](how-to-manage-indexing-policy.md). Nella tabella seguente sono elencate le limitazioni specifiche per le configurazioni in un contenitore. 

| Resource | Limite predefinito |
| --- | --- |
| Lunghezza massima del nome di database o un contenitore | 255 |
| Numero massimo di stored procedure per ogni contenitore | 100 <sup>*</sup>|
| Funzioni definite dall'utente massimo per ogni contenitore | 25 <sup>*</sup>|
| Numero massimo di percorsi dei criteri di indicizzazione| 100 <sup>*</sup>|
| Numero massimo di chiavi univoche per ogni contenitore|10 <sup>*</sup>|
| Numero massimo di percorsi per ogni vincolo di chiave univoca|16 <sup>*</sup>|

<sup>*</sup> È possibile aumentare questi limiti per ogni contenitore di contattare il supporto tecnico di Azure o contattando Microsoft tramite [chiedere a Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="per-item-limits"></a>Limiti per ogni elemento

A seconda di quali API utilizzare, un elemento Cosmos Azure può rappresentare un documento in una raccolta, una riga in una tabella, o in un nodo o edge in un grafico. La tabella seguente illustra i limiti per ogni elemento in Cosmos DB. 

| Resource | Limite predefinito |
| --- | --- |
| Dimensioni massime di un elemento | 2 MB (UTF-8 lunghezza della rappresentazione JSON) |
| Lunghezza massima del valore di chiave di partizione | 2048 byte |
| Lunghezza massima del valore di id | 1024 byte |
| Numero massimo di proprietà per ogni elemento | Nessun limite pratico |
| Profondità massima di annidamento | Nessun limite pratico |
| Lunghezza massima del nome della proprietà | Nessun limite pratico |
| Lunghezza massima del valore della proprietà | Nessun limite pratico |
| Lunghezza massima del valore della proprietà stringa | Nessun limite pratico |
| Lunghezza massima del valore della proprietà numeriche | IEEE754 precisione doppia a 64 bit |

Non sono previste restrizioni sui payload dell'elemento come il numero di proprietà e l'annidamento profondità, fatta eccezione per le restrizioni di lunghezza nella chiave di partizione e i valori di id e complessiva restrizione di 2 MB di dimensioni. È possibile configurare criteri di indicizzazione per i contenitori con elemento di grandi dimensioni o complesse strutture per ridurre il consumo di UR. Visualizzare [modellazione gli elementi in Cosmos DB](how-to-model-partition-example.md) per un esempio reale e i modelli per gestire gli elementi di grandi dimensioni.

## <a name="per-request-limits"></a>Limiti richiesta

COSMOS DB supporta [operazioni CRUD e di query](https://docs.microsoft.com/rest/api/cosmos-db/) sulle risorse come contenitori, gli elementi e i database.  

| Resource | Limite predefinito |
| --- | --- |
| Tempo di esecuzione massimo per una singola operazione (ad esempio, un'esecuzione della stored procedure o il recupero di pagina una sola query)| 5 secondi |
| Dimensione massima della richiesta (stored procedure CRUD)| 2 MB |
| Dimensione massima della risposta (ad esempio, impaginati query) | 4 MB |

Una volta un'operazione, ad esempio query raggiunge il limite di dimensione esecuzione timeout o della risposta, restituisce una pagina di risultati e un token di continuazione per il client possa riprendere l'esecuzione. Non sono previsti limiti pratici sulla durata di che una singola query eseguibili tra pagine/le continuazioni.

COSMOS DB Usa HMAC per l'autorizzazione. È possibile usare entrambi una chiave master, o un [i token delle risorse](secure-access-to-data.md) per il controllo di accesso con granularità fine alle risorse come contenitori, partizionare le chiavi o elementi. Nella tabella seguente elenca i limiti per i token di autorizzazione in Cosmos DB.

| Resource | Limite predefinito |
| --- | --- |
| Massimo master scadenza dei token | 15 min  |
| Ora di scadenza del token risorsa minima | 10 min  |
| Ora di scadenza del token massima di risorse | 24 ore per impostazione predefinita. È possibile aumentarlo dal [invio di un ticket di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) o contattare Microsoft tramite [chiedere a Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Sfasamento di orario massimo per l'autorizzazione di token| 15 min |

COSMOS DB supporta l'esecuzione di trigger durante la scrittura. Il servizio supporta un massimo di una pre-trigger e un post-trigger per ogni operazione di scrittura. 

## <a name="sql-query-limits"></a>Limiti di query SQL

COSMOS DB supporta l'esecuzione di query usando gli elementi [SQL](how-to-sql-query.md). La tabella seguente descrive le restrizioni nelle istruzioni di query, ad esempio in termini di numero di clausole o lunghezza di query.

| Resource | Limite predefinito |
| --- | --- |
| Lunghezza massima di query SQL| 256 KB <sup>*</sup>|
| Join massimo per ogni query| 5 <sup>*</sup>|
| Massimo and per ogni query| 2000 <sup>*</sup>|
| Operatori OR massima per ogni query| 2000 <sup>*</sup>|
| Funzioni definite dall'utente massimo per le query| 10 <sup>*</sup>|
| Argomenti di massimi per ogni nell'espressione| 6000 <sup>*</sup>|
| Numero massimo di punti per ogni poligono| 4096 <sup>*</sup>|

<sup>*</sup> È possibile aumentare questi limiti di query SQL di mediante contattando il supporto tecnico di Azure o contattare Microsoft tramite il [chiedere a Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="mongodb-api-specific-limits"></a>Limiti specifici dell'API MongoDB

COSMOS DB supporta il protocollo di trasmissione MongoDB per le applicazioni scritte per MongoDB. È possibile trovare i comandi supportati e le versioni del protocollo [MongoDB supportate funzionalità e sintassi](mongodb-feature-support.md).

Nella tabella seguente sono elencate le limitazioni specifiche per il supporto di funzionalità di MongoDB. Altri limiti di servizio indicati per le API (core) di SQL si applicano anche all'API di MongoDB.

| Resource | Limite predefinito |
| --- | --- |
| Dimensioni massime della memoria della query MongoDB | 40 MB |
| Tempo di esecuzione massimo per le operazioni di MongoDB| 30 secondi |

## <a name="try-cosmos-db-free-limits"></a>Provare a limiti di Cosmos DB gratuito

La tabella seguente elenca i limiti per il [prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) valutazione.

| Resource | Limite predefinito |
| --- | --- |
| Durata del periodo di valutazione | 30 giorni (può essere rinnovato qualsiasi numero di volte) |
| Numero massimo di contenitori per ogni sottoscrizione (API di tabella SQL, Gremlin) | 1 |
| Numero massimo di contenitori per ogni sottoscrizione (l'API MongoDB) | 3 |
| Velocità effettiva massima per ogni contenitore | 5000 |
| Velocità effettiva massima per ogni database condivisi e velocità effettiva | 20000 |
| Massimo spazio di archiviazione totale per ogni account | 10 GB |

Provare a distribuzione globale di Cosmos DB supporta in solo le aree Stati Uniti centrali, Europa settentrionale e Asia sud-orientale. Non è possibile creare ticket di supporto tecnico di Azure per gli account di prova di Azure Cosmos DB. Tuttavia, il supporto è fornito per i sottoscrittori con piani di supporto.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Cosmos DB i concetti fondamentali [distribuzione globale](distribute-data-globally.md) e [partizionamento](partitioning-overview.md) e [velocità effettiva con provisioning](request-units.md).

Per un'introduzione ad Azure Cosmos DB, fare riferimento alle guide introduttive seguenti:

* [Introduzione all'API SQL di Azure Cosmos DB](create-sql-api-dotnet.md)
* [Come iniziare a usare l'API di Azure Cosmos DB per MongoDB](create-mongodb-nodejs.md)
* [Introduzione all'API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introduzione all'API Gremlin di Azure Cosmos DB](create-graph-dotnet.md)
* [Introduzione all'API Tabelle di Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
