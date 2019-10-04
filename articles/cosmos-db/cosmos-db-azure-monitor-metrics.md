---
title: Ottenere Azure Cosmos DB metriche da monitoraggio di Azure
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 762c910336fa2b50a46eda23cf66d8a7aa383c52
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241230"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorare ed eseguire il debug di metriche di Azure Cosmos DB da monitoraggio di Azure

È possibile visualizzare Azure Cosmos DB metriche dall'API di monitoraggio di Azure. Monitoraggio di Azure offre diversi modi per interagire con le metriche, inclusi i portale di Azure, l'accesso tramite l'API REST o l'esecuzione di query su di essi tramite PowerShell o l'interfaccia della riga di comando. Azure Cosmos DB metriche sono valori numerici a bassa latenza, raccolti con una frequenza di un minuto per impostazione predefinita, è anche possibile aggregare queste metriche. Queste metriche sono in grado di supportare scenari in tempo reale.  

Questo articolo descrive le metriche di Azure Cosmos DB diverse che è possibile visualizzare da monitoraggio di Azure usando portale di Azure. Se si è interessati ai casi d'uso comuni e al modo in cui vengono usate Azure Cosmos DB metriche per analizzare ed eseguire il debug di questi problemi, vedere [monitorare ed eseguire il debug con le metriche in Azure Cosmos DB](use-metrics.md) articolo. Si userà uno degli account Azure Cosmos esistenti e si visualizzano le diverse metriche a livello di database, contenitore, area, richiesta o operazione. Assicurarsi quindi di avere un account Azure Cosmos con dati di esempio ed eseguire operazioni CRUD su tali dati.

## <a name="view-metrics-from-azure-portal"></a>Visualizzare le metriche da portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **monitoraggio** nella barra di spostamento a sinistra e selezionare **metrica**.

   ![Riquadro metriche in monitoraggio di Azure](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Dal riquadro **metriche** > **selezionare una risorsa** > scegliere la **sottoscrizione**e il **gruppo di risorse**richiesti. Per il **tipo di risorsa**selezionare **account Azure Cosmos DB**, scegliere uno degli account Azure Cosmos esistenti e selezionare **applica**. 

   ![Scegliere un account Cosmos DB per visualizzare le metriche](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. È quindi possibile selezionare una metrica dall'elenco delle metriche disponibili. È possibile selezionare metriche specifiche per unità richiesta, archiviazione, latenza, disponibilità, Cassandra e altre. Per informazioni dettagliate su tutte le metriche disponibili nell'elenco, vedere la sezione [metrica per categoria](#metrics-by-category) di questo articolo. In questo esempio, selezionare **unità richiesta** e **AVG** come valore di aggregazione. 

   Oltre a questi dettagli, è anche possibile selezionare l' **intervallo di tempo** e la **granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche negli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   ![Scegliere una metrica dalla portale di Azure](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Aggiungere filtri alle metriche

È anche possibile filtrare le metriche e il grafico visualizzato da uno specifico **CollectionName**, **DatabaseName**, **OperationType**, **Region**e **statusCode**. Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà obbligatoria, ad esempio **OperationType** , e selezionare un valore, ad esempio **query**. Il grafico Visualizza quindi le unità richiesta utilizzate per l'operazione di query per il periodo selezionato. Le operazioni eseguite tramite la stored procedure non vengono registrate in modo che non siano disponibili nella metrica OperationType.

![Aggiungere un filtro per selezionare la granularità della metrica](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

È possibile raggruppare le metriche usando l'opzione **applica suddivisione** . È ad esempio possibile raggruppare le unità richiesta per tipo di operazione e visualizzare il grafico per tutte le operazioni in una sola volta, come illustrato nell'immagine seguente: 

![Aggiungi filtro di suddivisione applica](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metriche per categoria

### <a name="request-metrics"></a>Metriche per le richieste
            
|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione) |Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Totale richieste) | Conteggio (conteggio) | Numero di richieste eseguite| DatabaseName, CollectionName, Region, StatusCode| Tutti | Totale richieste, Http 2xx, Http 3xx, Http 400, Http 401, Errore server interno, Servizio non disponibile, Richieste limitate, Media richieste al secondo | Utilizzato per monitorare le richieste per codice di stato, contenitore a una granularità di minuti. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60. |
| MetadataRequests (richieste di metadati) |Conteggio (conteggio) | Conteggio delle richieste di metadati. Azure Cosmos DB gestisce il contenitore dei metadati di sistema per ogni account, che consente di enumerare le raccolte, i database e così via e le relative configurazioni, gratuitamente. | DatabaseName, CollectionName, Region, StatusCode| Tutti| |Usata per monitorare le limitazioni dovute a richieste di metadati.|
| MongoRequests (richieste Mongo) | Conteggio (conteggio) | Numero di richieste Mongo eseguite | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Rate (Frequenza richieste di query Mongo), Mongo Update Request Rate (Frequenza richieste di aggiornamento Mongo), Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo), Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo), Mongo Count Request Rate (Frequenza richieste di conteggio Mongo)| Usata per monitore gli errori delle richieste Mongo, utilizzi per tipo di comando. |

### <a name="request-unit-metrics"></a>Metriche delle unità richiesta

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|DESCRIZIONE|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (costo richiesta Mongo) | Conteggio (totale) |Unità richiesta Mongo utilizzate| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Charge (Addebito richieste di query Mongo), Mongo Update Request Charge (Addebito richieste di aggiornamento Mongo), Mongo Delete Request Charge (Addebito richieste di eliminazione Mongo), Mongo Insert Request Charge (Addebito richieste di inserimento Mongo), Mongo Count Request Charge (Addebito richieste di conteggio Mongo)| Usata per monitorare le unità richiesta Mongo in un minuto.|
| TotalRequestUnits (Totale unità richiesta)| Conteggio (totale) | Unità richiesta utilizzate| DatabaseName, CollectionName, Region, StatusCode |Tutti| TotalRequestUnits| Usata per monitorare l'utilizzo delle unità richiesta totali con una granularità di un minuto. Per ottenere la media delle unità richiesta utilizzate al secondo, usare il tipo di aggregazione Totale al minuto e dividere per 60.|
| ProvisionedThroughput (velocità effettiva con provisioning)| Conteggio (massimo) |Velocità effettiva con provisioning in granularità del contenitore| DatabaseName, ContainerName| 5M| | Usato per monitorare la velocità effettiva con provisioning per ogni contenitore.|

### <a name="storage-metrics"></a>Metriche di archiviazione

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (archiviazione disponibile) |Byte (totale) | Totale spazio di archiviazione disponibile segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M| Spazio di archiviazione disponibile| Usata per monitorare la capacità di archiviazione disponibile (applicabile solo per le raccolte di archiviazioni fisse). La granularità minima deve essere di 5 minuti.| 
| DataUsage (utilizzo dati) |Byte (totale) |Utilizzo totale dei dati segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M |Dimensioni dei dati | Usato per monitorare l'utilizzo totale dei dati in un contenitore e in un'area, la granularità minima dovrebbe essere di 5 minuti.|
| IndexUsage (utilizzo indici) | Byte (totale) |Totale utilizzo di indici restituiti a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M| Dimensioni dell'indice| Usato per monitorare l'utilizzo totale dei dati in un contenitore e in un'area, la granularità minima dovrebbe essere di 5 minuti. |
| DocumentQuota (quota documento) | Byte (totale) | Quota di archiviazione totale restituita a una granularità di 5 minuti per area.| DatabaseName, CollectionName, Region| 5M |Capacità di archiviazione| Usato per monitorare la quota totale nel contenitore e nell'area, la granularità minima dovrebbe essere di 5 minuti.|
| DocumentCount (Conteggio documenti) | Conteggio (totale) |Numero totale di documenti segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M |Conteggio documenti|Usato per monitorare il numero di documenti nel contenitore e nell'area, la granularità minima dovrebbe essere di 5 minuti.|

### <a name="latency-metrics"></a>Metriche di latenza

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Utilizzo |
|---|---|---|---| ---| ---|
| ReplicationLatency (latenza di replica)| MilliSecondi (minimo, massimo, medio) | Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica| SourceRegion, TargetRegion| Tutti | Usata per monitorare la latenza di replica P99 tra due aree per un account abilitato per la replica geografica. |


### <a name="availability-metrics"></a>Metriche di disponibilità

|Metrica (nome visualizzato della metrica) |Unità (tipo di aggregazione)|Descrizione| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---|
| ServiceAvailability (disponibilità del servizio)| Percentuale (minima, massima) | Disponibilità delle richieste di account con una granularità di un'ora| 1H | Disponibilità del servizio | Rappresenta la percentuale delle richieste passate totali. Una richiesta viene considerata non riuscita a causa di un errore di sistema se il codice di stato è 410, 500 o 503. Usata per monitorare la disponibilità dell'account con una granularità di un'ora. |


### <a name="cassandra-api-metrics"></a>Metriche dell'API Cassandra

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|DESCRIZIONE|Dimensioni| Granularità temporali| Utilizzo |
|---|---|---|---| ---| ---|
| CassandraRequests (richieste Cassandra) | Conteggio (conteggio) | Numero di richieste dell'API Cassandra| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tutti| Usata per monitorare le richieste di Cassandra con una granularità di un minuto. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60.|
| CassandraRequestCharges (addebiti per le richieste Cassandra) | Conteggio (Sum, min, Max, AVG) | Unità richiesta utilizzate dalle richieste dell'API Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Tutti| Usata per monitorare le UR usate al minuto da un account dell'API Cassandra.|
| CassandraConnectionClosures (chiusure della connessione Cassandra) |Conteggio (conteggio) |Numero di connessioni a Cassandra chiuse| ClosureReason, Region| Tutti | Usata per monitorare la connettività tra i client e l'API Cassandra di Azure Cosmos DB.|

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare e monitorare le metriche dal riquadro metrica account Azure Cosmos DB](use-metrics.md)

* [Registrazione diagnostica in Azure Cosmos DB](logging.md)
