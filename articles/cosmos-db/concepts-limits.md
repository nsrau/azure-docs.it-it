---
title: Quote del servizio Azure Cosmos DB
description: Quote del servizio Azure Cosmos DB e limiti predefiniti per diversi tipi di risorse.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 2d14207bada52c937875bc62bdbde63d788c1aca
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653588"
---
# <a name="azure-cosmos-db-service-quotas"></a>Quote del servizio Azure Cosmos DB

Questo articolo fornisce una panoramica delle quote predefinite offerte a risorse diverse in Azure Cosmos DB.

## <a name="storage-and-database-operations"></a>Operazioni di archiviazione e database

Dopo aver creato un account Azure Cosmos nella propria sottoscrizione, è possibile gestire i dati nell'account [creando database, contenitori ed elementi](databases-containers-items.md).

### <a name="provisioned-throughput"></a>Velocità effettiva con provisioning

È possibile effettuare il provisioning della velocità effettiva a livello di contenitore o di database in termini di [unità richiesta (UR/sec o UR)](request-units.md). La tabella seguente elenca i limiti relativi all'archiviazione e alla velocità effettiva per ogni contenitore o database.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di UR per ogni contenitore ([modalità di provisioning con velocità effettiva dedicata](databases-containers-items.md#azure-cosmos-containers)) | 1\.000.000 per impostazione predefinita. È possibile aumentarlo [presentando un ticket di supporto di Azure](create-support-request-quota-increase.md) |
| Numero massimo di UR per ogni database ([modalità di provisioning con velocità effettiva condivisa](databases-containers-items.md#azure-cosmos-containers)) | 1\.000.000 per impostazione predefinita. È possibile aumentarlo [presentando un ticket di supporto di Azure](create-support-request-quota-increase.md) |
| Numero massimo di ur per partizione (logica) | 10,000 |
| Archiviazione massima in tutti gli elementi per ogni partizione (logica) | 20 GB |
| Numero massimo di chiavi di partizione (logica) distinte | Nessuna limitazione |
| Dimensione massima di archiviazione per ogni contenitore | Nessuna limitazione |
| Dimensione massima di archiviazione per ogni database | Nessuna limitazione |
| Dimensioni massime allegati per account (la funzionalità degli allegati verrà deprecata) | 2 GB |
| Numero minimo di UR richieste per 1 GB | 10 UR/sec |

> [!NOTE]
> Per informazioni sulle procedure consigliate per la gestione dei carichi di lavoro con chiavi di partizione che richiedono limiti più elevati per l'archiviazione o la velocità effettiva, vedere [Creare una chiave di partizione sintetica](synthetic-partition-keys.md).

Un contenitore Cosmos (o un database con velocità effettiva condivisa) deve avere una velocità effettiva minima di 400 UR/sec. Man mano che aumenta la dimensione del contenitore, la velocità effettiva minima supportata dipende anche dai fattori seguenti:

* La velocità effettiva massima di cui è stato effettuato il provisioning per il contenitore. Se, ad esempio, la velocità effettiva viene aumentata a 50.000 UR/sec, la velocità effettiva con provisioning più bassa possibile sarà 500 UR/sec.
* Archiviazione corrente in GB nel contenitore. Se, ad esempio, il contenitore ha 100 GB di spazio di archiviazione, la velocità effettiva con provisioning più bassa possibile sarà 1000 UR/sec.
* La velocità effettiva minima in un database con velocità effettiva condivisa dipende dal numero totale di contenitori creati in un database con velocità effettiva condivisa, misurata in 100 UR/sec per ogni contenitore. Se, ad esempio, sono stati creati cinque contenitori all'interno di un database con velocità effettiva condivisa, la velocità effettiva deve essere almeno di 500 UR/sec.

La velocità effettiva corrente e minima di un contenitore o di un database può essere recuperata dal portale di Azure o dagli SDK. Per altre informazioni, vedere [Effettuare il provisioning della velocità effettiva per i contenitori e i database](set-throughput.md). 

> [!NOTE]
> In alcuni casi, potrebbe essere possibile ridurre la velocità effettiva a un valore inferiore al 10%. Usare l'API per ottenere il numero minimo esatto di UR per ogni contenitore.

In sintesi, di seguito sono riportati i limiti minimi di provisioning delle UR. 

| Risorsa | Limite predefinito |
| --- | --- |
| Numero minimo di UR per ogni contenitore ([modalità di provisioning con velocità effettiva dedicata](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Numero minimo di UR per ogni database ([modalità di provisioning con velocità effettiva condivisa](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Numero minimo di UR per ogni contenitore all'interno di un database con velocità effettiva condivisa | 100 |

Cosmos DB supporta il ridimensionamento elastico della velocità effettiva (UR) per ogni contenitore o database tramite gli SDK o il portale. Ogni contenitore può essere ridimensionato in modo sincrono e immediatamente entro un intervallo di scala compreso tra 10 e 100 volte, tra i valori minimo e massimo. Se il valore della velocità effettiva richiesta non rientra nell'intervallo, il ridimensionamento viene eseguito in modo asincrono. Il ridimensionamento asincrono può richiedere minuti o ore, a seconda della velocità effettiva richiesta e della dimensione di archiviazione dei dati nel contenitore.  

### <a name="serverless"></a>Senza server

Senza [Server](serverless.md) è possibile usare le risorse di Azure Cosmos DB in modo basato sul consumo. La tabella seguente elenca i limiti per l'archiviazione e la velocità effettiva di espansione per contenitore/database.

| Risorsa | Limite |
| --- | --- |
| Numero massimo di ur/sec per contenitore | 5\.000 |
| Massima partizione ur/s per (logica) | 5\.000 |
| Archiviazione massima in tutti gli elementi per ogni partizione (logica) | 20 GB |
| Numero massimo di chiavi di partizione (logica) distinte | Nessuna limitazione |
| Dimensione massima di archiviazione per ogni contenitore | 50 GB |

## <a name="control-plane-operations"></a>Operazioni del piano di controllo

È possibile [eseguire il provisioning dell'account Azure Cosmos e gestirlo](how-to-manage-database-account.md) usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure e i modelli di Azure Resource Manager. La tabella seguente elenca i limiti per ogni sottoscrizione, account e numero di operazioni.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di account di database per ogni sottoscrizione | 50 per impostazione predefinita. È possibile aumentarlo [presentando un ticket di supporto di Azure](create-support-request-quota-increase.md)|
| Numero massimo di failover a livello di area | 1 all'ora per impostazione predefinita. È possibile aumentarlo [presentando un ticket di supporto di Azure](create-support-request-quota-increase.md)|

> [!NOTE]
> I failover a livello di area si applicano solo ad account di scrittura su singola area. Gli account di scrittura su più aree non richiedono o non hanno limiti per la modifica dell'area di scrittura.

Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. Per informazioni dettagliate sugli intervalli e sulle finestre di conservazione del backup, vedere [Backup online e ripristino dei dati su richiesta in Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limiti per account

### <a name="provisioned-throughput"></a>Velocità effettiva con provisioning

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di database | Nessuna limitazione |
| Numero massimo di contenitori per ogni database con velocità effettiva condivisa |25 |
| Numero massimo di contenitori per ogni database o account con velocità effettiva dedicata  |senza limiti |
| Numero massimo di aree | Nessun limite (tutte le aree di Azure) |

### <a name="serverless"></a>Senza server

| Risorsa | Limite |
| --- | --- |
| Numero massimo di database | Nessuna limitazione |
| Numero massimo di contenitori per account  | 100 |
| Numero massimo di aree | 1 (qualsiasi area di Azure) |

## <a name="per-container-limits"></a>Limiti per contenitore

A seconda dell'API usata, un contenitore di Azure Cosmos può rappresentare una raccolta, una tabella o un grafo. I contenitori supportano le configurazioni per [vincoli di chiave univoca](unique-keys.md), [stored procedure, trigger e UDF](stored-procedures-triggers-udfs.md) e [criteri di indicizzazione](how-to-manage-indexing-policy.md). La tabella seguente elenca i limiti specifici delle configurazioni all'interno di un contenitore. 

| Risorsa | Limite predefinito |
| --- | --- |
| Lunghezza massima del nome del database o del contenitore | 255 |
| Numero massimo di stored procedure per ogni contenitore | 100 <sup>*</sup>|
| Numero massimo di UDF per ogni contenitore | 25 <sup>*</sup>|
| Numero massimo di percorsi nei criteri di indicizzazione| 100 <sup>*</sup>|
| Numero massimo di chiavi univoche per ogni contenitore|10 <sup>*</sup>|
| Numero massimo di percorsi per ogni vincolo di chiave univoca|16 <sup>*</sup>|
| Valore TTL massimo |2147483647|

<sup>*</sup> È possibile aumentare i limiti per ogni contenitore creando un [richiesta di supporto di Azure](create-support-request-quota-increase.md).

## <a name="per-item-limits"></a>Limiti per elemento

A seconda dell'API usata, un elemento Azure Cosmos DB può rappresentare un documento in una raccolta, una riga in una tabella oppure un nodo o un arco in un grafo. La tabella seguente mostra i limiti per ogni elemento in Cosmos DB. 

| Risorsa | Limite predefinito |
| --- | --- |
| Dimensione massima di un elemento | 2 MB (lunghezza UTF-8 della rappresentazione JSON) |
| Lunghezza massima del valore della chiave di partizione | 2048 byte |
| Lunghezza massima del valore ID | 1023 byte |
| Numero massimo di proprietà per ogni elemento | Nessun limite pratico |
| Profondità massima di annidamento | Nessun limite pratico |
| Lunghezza massima del nome della proprietà | Nessun limite pratico |
| Lunghezza massima del valore della proprietà | Nessun limite pratico |
| Lunghezza massima del valore della proprietà stringa | Nessun limite pratico |
| Lunghezza massima del valore della proprietà numerica | IEEE754 a precisione doppia (64 bit) |
| Valore TTL massimo |2147483647|

Non sono previste restrizioni per i payload dell'elemento come il numero di proprietà e la profondità di annidamento, ad eccezione delle limitazioni di lunghezza per la chiave di partizione e i valori ID e la restrizione della dimensione complessiva di 2 MB. Potrebbe essere necessario configurare i criteri di indicizzazione per i contenitori con strutture di elementi grandi o complesse per ridurre il consumo di UR. Per un esempio reale e modelli per la gestione di elementi di grandi dimensioni, vedere [Modellazione di elementi in Cosmos DB](how-to-model-partition-example.md).

## <a name="per-request-limits"></a>Limiti per richiesta

Azure Cosmos DB supporta [operazioni di query e CRUD](/rest/api/cosmos-db/) su risorse quali contenitori, elementi e database. Supporta inoltre [richieste batch transazionali](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) rispetto a più elementi con la stessa chiave di partizione in un contenitore.

| Risorsa | Limite predefinito |
| --- | --- |
| Tempo massimo di esecuzione per una singola operazione (ad esempio, l'esecuzione di una stored procedure o un recupero di pagine con singola query)| 5 secondi |
| Dimensione massima della richiesta (ad esempio, stored procedure, CRUD)| 2 MB |
| Dimensione massima della risposta (ad esempio, query impaginata) | 4 MB |
| Numero massimo di operazioni in un batch transazionale | 100 |

Non appena un'operazione come una query raggiunge il timeout di esecuzione o il limite della dimensione della risposta, restituisce al client una pagina di risultati e un token di continuazione per riprendere l'esecuzione. Non esiste un limite pratico per la durata di esecuzione di una singola query tra pagine/continuazioni.

Cosmos DB usa HMAC per l'autorizzazione. È possibile usare una chiave primaria o un token di [risorsa](secure-access-to-data.md) per il controllo di accesso con granularità fine a risorse quali contenitori, chiavi di partizione o elementi. La tabella seguente elenca i limiti relativi ai token di autorizzazione in Cosmos DB.

| Risorsa | Limite predefinito |
| --- | --- |
| Tempo di scadenza massimo token primario | 15 min  |
| Tempo di scadenza minimo del token di risorsa | 10 min  |
| Tempo di scadenza massimo del token di risorsa | 24 ore per impostazione predefinita. È possibile aumentarlo [presentando un ticket di supporto di Azure](create-support-request-quota-increase.md)|
| Massimo sfasamento di orario per l'autorizzazione con token| 15 min |

Cosmos DB supporta l'esecuzione di trigger durante le operazioni di scrittura. Il servizio supporta al massimo un pre-trigger e un post-trigger per ogni operazione di scrittura.

## <a name="metadata-request-limits"></a>Limiti delle richieste di metadati

Azure Cosmos DB gestisce i metadati di sistema per ogni account. Questi metadati consentono di enumerare le raccolte, i database, altre risorse Azure Cosmos DB e le relative configurazioni gratuitamente.

| Risorsa | Limite predefinito |
| --- | --- |
|Velocità massima di creazione raccolta al minuto| 5|
|Velocità massima di creazione database al minuto|   5|
|Velocità massima di aggiornamento velocità effettiva con provisioning al minuto| 5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Limiti per la velocità effettiva con provisioning a scalabilità automatica

Per una spiegazione più dettagliata della velocità effettiva e dei limiti di archiviazione con la scalabilità automatica, vedere l'articolo relativo alla [scalabilità automatica](provision-throughput-autoscale.md#autoscale-limits) e le [domande frequenti](autoscale-faq.md#lowering-the-max-rus).

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di UR/sec in base al quale può essere ridimensionato il sistema |  `Tmax`, il numero massimo di UR/sec per la scalabilità automatica impostato dall'utente|
| Numero minimo di UR/sec in base al quale può essere ridimensionato il sistema | `0.1 * Tmax`|
| Numero di UR/sec correnti in base al quale viene ridimensionato il sistema  |  `0.1*Tmax <= T <= Tmax`, in base all'utilizzo|
| Numero minimo di UR/sec fatturabili all'ora| `0.1 * Tmax` <br></br>La fatturazione viene effettuata su base oraria. Viene addebitato il numero massimo di UR/sec in base al quale viene ridimensionato il sistema nel corso dell'ora, oppure `0.1*Tmax`, a seconda del valore più alto. |
| Numero massimo di UR/sec per la scalabilità automatica minima per un contenitore  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` arrotondato al migliaio di UR/sec più vicino |
| Numero massimo di UR/sec per la scalabilità automatica minima per un database  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, arrotondato al migliaio di UR/sec più vicino. <br></br>Nota: se il database ha più di 25 contenitori, il sistema incrementa di 1000 UR/sec il numero massimo di UR/sec per la scalabilità automatica minima per ogni contenitore in più. Se, ad esempio, sono presenti 30 contenitori, il numero massimo di UR/sec per la scalabilità automatica minima che è possibile impostare è 9000 UR/sec (con scalabilità compresa tra 900 e 9000 UR/sec).

## <a name="sql-query-limits"></a>Limiti per le query SQL

Cosmos DB supporta l'esecuzione di query sugli elementi tramite [SQL](how-to-sql-query.md). Nella tabella seguente vengono descritte le restrizioni relative alle istruzioni di query, ad esempio in termini di numero di clausole o di lunghezza della query.

| Risorsa | Limite predefinito |
| --- | --- |
| Lunghezza massima della query SQL| 256 KB |
| Numero massimo di JOIN per ogni query| 5 <sup>*</sup>|
| Numero massimo di UDF per ogni query| 10 <sup>*</sup>|
| Numero massimo di punti per ogni poligono| 4096 |
| Numero massimo di percorsi inclusi per ogni contenitore| 500 |
| Numero massimo di percorsi esclusi per ogni contenitore| 500 |
| Numero massimo di proprietà in un indice composto| 8 |

<sup>*</sup> È possibile aumentare i limiti di query SQL creando un Richiesta di supporto di [Azure](create-support-request-quota-increase.md).

## <a name="mongodb-api-specific-limits"></a>Limiti specifici dell'API MongoDB

Cosmos DB supporta il protocollo di collegamento MongoDB per le applicazioni scritte in MongoDB. È possibile trovare i comandi e le versioni del protocollo supportati in [Funzionalità e sintassi di MongoDB supportate](mongodb-feature-support.md).

La tabella seguente elenca i limiti specifici del supporto delle funzionalità MongoDB. Altri limiti del servizio indicati per l'API SQL (Core) si applicano anche all'API MongoDB.

| Risorsa | Limite predefinito |
| --- | --- |
| Dimensione massima della memoria per query MongoDB (questa limitazione è solo per la versione del server 3.2) | 40 MB |
| Tempo massimo di esecuzione per le operazioni MongoDB| 30 secondi |
| Timeout della connessione inattiva per la chiusura della connessione lato server* | 30 minuti |

\* È consigliabile che le applicazioni client impostino il timeout della connessione inattiva nelle impostazioni del driver su 2-3 minuti perché il [timeout predefinito per Azure LoadBalancer è di 4 minuti](../load-balancer/load-balancer-tcp-idle-timeout.md).  Questo timeout garantisce che le connessioni inattive non siano chiuse da un servizio di bilanciamento del carico intermedio tra il computer client e Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Limiti della versione di valutazione gratuita di Azure Cosmos DB

La tabella seguente elenca i limiti relativi alla versione di valutazione gratuita di [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/).

| Risorsa | Limite predefinito |
| --- | --- |
| Durata della versione di valutazione | 30 giorni (è possibile richiedere una nuova versione di valutazione dopo la scadenza) <br> Dopo la scadenza, le informazioni archiviate vengono eliminate. |
| Numero massimo di contenitori per ogni sottoscrizione (SQL, Gremlin, API Tabella) | 1 |
| Numero massimo di contenitori per ogni sottoscrizione (API MongoDB) | 3 |
| Velocità effettiva massima per ogni contenitore | 5000 |
| Velocità effettiva massima per ogni database con velocità effettiva condivisa | 20000 |
| Dimensione massima di archiviazione totale per ogni account | 10 GB |

La versione di valutazione gratuita di Cosmos DB supporta la distribuzione globale solo nelle aree Stati Uniti centrali, Europa settentrionale e Asia sud-orientale. Non è possibile creare ticket di supporto di Azure per gli account della versione di valutazione gratuita di Azure Cosmos DB. Viene tuttavia fornito il supporto per i sottoscrittori con piani di supporto esistenti.

## <a name="free-tier-account-limits"></a>Limiti dell'account di livello gratuito
La tabella seguente elenca i limiti relativi agli [account Azure Cosmos DB di livello gratuito](optimize-dev-test.md#azure-cosmos-db-free-tier).

| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di livello gratuito per ogni sottoscrizione di Azure | 1 |
| Durata dello sconto per il livello gratuito | Durata dell'account. È necessario garantire il consenso esplicito durante la creazione dell'account. |
| Numero massimo di UR/sec gratuiti | 400 UR/sec |
| Dimensione massima di archiviazione gratuita | 5 GB |
| Numero massimo di database con velocità effettiva condivisa | 5 |
| Numero massimo di contenitori in un database con velocità effettiva condivisa | 25 <br>Negli account di livello gratuito, il numero minimo di UR/sec per un database con velocità effettiva condivisa contenente fino a 25 contenitori è 400 UR/sec. |

  Oltre a quanto sopra riportato, i [limiti per account](#per-account-limits) si applicano anche agli account di livello gratuito.

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sui concetti di base di Cosmos DB per la [distribuzione globale](distribute-data-globally.md), il [partizionamento](partitioning-overview.md) e il [provisioning della velocità effettiva](request-units.md).

Per un'introduzione ad Azure Cosmos DB, fare riferimento alle guide introduttive seguenti:

* [Introduzione all'API SQL di Azure Cosmos DB](create-sql-api-dotnet.md)
* [Come iniziare a usare l'API di Azure Cosmos DB per MongoDB](create-mongodb-nodejs.md)
* [Introduzione all'API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introduzione all'API Gremlin di Azure Cosmos DB](create-graph-dotnet.md)
* [Introduzione all'API Tabelle di Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
