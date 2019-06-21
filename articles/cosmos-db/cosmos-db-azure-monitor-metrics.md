---
title: Ottenere le metriche di Azure Cosmos DB da monitoraggio di Azure
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276795"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorare ed eseguire il debug delle metriche di Azure Cosmos DB da monitoraggio di Azure

È possibile visualizzare le metriche di Azure Cosmos DB dall'API di monitoraggio di Azure. Monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui il portale di Azure, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o CLI. Metriche di Azure Cosmos DB sono valori numerici a bassa latenza, raccolti in corrispondenza di frequenza di un minuto per impostazione predefinita, è anche possibile aggregare le metriche. Queste metriche sono in grado di supportare scenari in tempo reale.  

Questo articolo descrive diverse metriche in Azure Cosmos DB che è possibile visualizzare da monitoraggio di Azure usando il portale di Azure. Se si è interessati in comune casi d'uso e modo in cui le metriche di Azure Cosmos DB re usate per analizzare ed eseguire il debug di questi problemi, vedere [monitoraggio ed eseguire il debug con le metriche in Azure Cosmos DB](use-metrics.md) articolo. Verrà usare uno degli account di Azure Cosmos esistenti e visualizzare le metriche diverse richiesta, il livello di operazione, contenitore, area o database. Pertanto, assicurarsi che si dispone di un account Azure Cosmos con dati di esempio ed eseguire operazioni CRUD su tali dati.

## <a name="view-metrics-from-azure-portal"></a>Visualizzare le metriche dal portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Monitor** dalla barra di spostamento a sinistra della barra, quindi selezionare **metriche**.

   ![Riquadro metriche in Monitoraggio di Azure](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Dal **metriche** riquadro > **selezionare una risorsa** > scegliere obbligatorio **sottoscrizione**, e **gruppo di risorse**. Per il **tipo di risorsa**, selezionare **account Azure Cosmos DB**, scegliere uno degli account di Azure Cosmos esistente e selezionare **Apply**. 

   ![Scegliere un account Cosmos DB per visualizzare le metriche](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Successivamente è possibile selezionare una metrica dall'elenco delle metriche disponibili. È possibile selezionare metriche specifiche per unità di richiesta, archiviazione, latenza, disponibilità, Cassandra e ad altri utenti. Per ulteriori dettagli su tutte le metriche disponibili in questo elenco, vedere la [le metriche in base alla categoria](#metrics-by-category) sezione di questo articolo. In questo esempio selezioniamo **unità richiesta** e **Avg** uguale al valore di aggregazione. 

   Oltre a queste informazioni dettagliate, è anche possibile selezionare i **intervallo di tempo** e **granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche per gli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   ![Scegliere una metrica dal portale di Azure](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Aggiungere filtri alle metriche

È anche possibile filtrare le metriche e il grafico visualizzato da uno specifico **CollectionName**, **DatabaseName**, **OperationType**, **area**, e **StatusCode**. Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere, ad esempio la proprietà richiesta **OperationType** e selezionare un valore, ad esempio **Query**. Il grafico visualizza quindi le unità richiesta utilizzate per l'operazione di query per il periodo selezionato. Le operazioni eseguite tramite Stored procedure non vengono registrate in modo che non sono disponibili sotto la metrica OperationType.

![Aggiungere un filtro per selezionare la granularità delle metriche](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

È possibile raggruppare le metriche usando il **applica la suddivisione** opzione. Ad esempio, è possibile raggruppare le unità richiesta per ogni tipo di operazione e visualizzare il grafico per tutte le operazioni in una sola volta come illustrato nell'immagine seguente: 

![Aggiungere Applica filtro suddivisione](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metriche per categoria

### <a name="request-metrics"></a>Metriche per le richieste
            
|Metrica (nome visualizzato per la metrica)|Unità (tipo di aggregazione) |Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Uso |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Totale richieste) | Count (conteggio) | Numero di richieste eseguite| DatabaseName, CollectionName, Region, StatusCode| Tutti | Totale richieste, Http 2xx, Http 3xx, Http 400, Http 401, Errore server interno, Servizio non disponibile, Richieste limitate, Media richieste al secondo | Usata per monitorare le richieste per codice di stato, raccolta con una granularità di un minuto. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60. |
| MetadataRequests (richieste di metadati) |Count (conteggio) | Conteggio delle richieste di metadati. Azure Cosmos DB mantiene insieme i metadati di sistema per ogni account, che consente di enumerare le raccolte, database e così via, e le relative configurazioni gratuitamente. | DatabaseName, CollectionName, Region, StatusCode| Tutti| |Usata per monitorare le limitazioni dovute a richieste di metadati.|
| MongoRequests (richieste Mongo) | Count (conteggio) | Numero di richieste Mongo eseguite | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Rate (Frequenza richieste di query Mongo), Mongo Update Request Rate (Frequenza richieste di aggiornamento Mongo), Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo), Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo), Mongo Count Request Rate (Frequenza richieste di conteggio Mongo)| Usata per monitore gli errori delle richieste Mongo, utilizzi per tipo di comando. |

### <a name="request-unit-metrics"></a>Metriche delle unità richiesta

|Metrica (nome visualizzato per la metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Uso |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (addebito richiesta Mongo) | Conteggio (totale) |Unità richiesta Mongo utilizzate| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Charge (Addebito richieste di query Mongo), Mongo Update Request Charge (Addebito richieste di aggiornamento Mongo), Mongo Delete Request Charge (Addebito richieste di eliminazione Mongo), Mongo Insert Request Charge (Addebito richieste di inserimento Mongo), Mongo Count Request Charge (Addebito richieste di conteggio Mongo)| Usata per monitorare le unità richiesta Mongo in un minuto.|
| TotalRequestUnits (unità richiesta totali)| Conteggio (totale) | Unità richiesta utilizzate| DatabaseName, CollectionName, Region, StatusCode |Tutti| TotalRequestUnits| Usata per monitorare l'utilizzo delle unità richiesta totali con una granularità di un minuto. Per ottenere la media delle unità richiesta utilizzate al secondo, usare il tipo di aggregazione Totale al minuto e dividere per 60.|
| ProvisionedThroughput (velocità effettiva con provisioning)| Conteggio (massimo) |Velocità effettiva sottoposta a provisioning con granularità per raccolta| DatabaseName, CollectionName| 5M| | Usata per monitorare la velocità effettiva sottoposta a provisioning per raccolta.|

### <a name="storage-metrics"></a>Metriche di archiviazione

|Metrica (nome visualizzato per la metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Uso |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (spazio di archiviazione disponibile) |Byte (totale) | Archiviazione totale segnalati con una granularità di 5 minuti per ogni area| DatabaseName, CollectionName, Region| 5M| Spazio di archiviazione disponibile| Usata per monitorare la capacità di archiviazione disponibile (applicabile solo per le raccolte di archiviazioni fisse). La granularità minima deve essere di 5 minuti.| 
| DataUsage (utilizzo dei dati) |Byte (totale) |Utilizzo dati totale segnalati con una granularità di 5 minuti per ogni area| DatabaseName, CollectionName, Region| 5M |Dimensioni dei dati | Usata per monitorare l'utilizzo dei dati totale per raccolta e area. La granularità minima deve essere di 5 minuti.|
| IndexUsage (utilizzo dell'indice) | Byte (totale) |Totale utilizzo dell'indice segnalato con una granularità di 5 minuti per ogni area| DatabaseName, CollectionName, Region| 5M| Dimensioni dell'indice| Usata per monitorare l'utilizzo dei dati totale per raccolta e area. La granularità minima deve essere di 5 minuti. |
| DocumentQuota (Quota documento) | Byte (totale) | Quota di archiviazione totale segnalati con una granularità di 5 minuti per ogni area.| DatabaseName, CollectionName, Region| 5M |Capacità di archiviazione| Usata per monitorare la quota totale per raccolta e area. La granularità minima deve essere di 5 minuti.|
| DocumentCount (numero di documenti) | Conteggio (totale) |Numero totale di documenti segnalato con una granularità di 5 minuti per ogni area| DatabaseName, CollectionName, Region| 5M |Conteggio documenti|Usata per monitorare il conteggio dei documenti per raccolta e area. La granularità minima deve essere di 5 minuti.|

### <a name="latency-metrics"></a>Metriche di latenza

|Metrica (nome visualizzato per la metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Uso |
|---|---|---|---| ---| ---|
| ReplicationLatency (latenza di replica)| Millisecondi (minimo, massimo, medio) | Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica| SourceRegion, TargetRegion| Tutti | Usata per monitorare la latenza di replica P99 tra due aree per un account abilitato per la replica geografica. |


### <a name="availability-metrics"></a>Metriche di disponibilità

|Metrica (nome visualizzato per la metrica) |Unità (tipo di aggregazione)|Descrizione| Granularità temporali| Mapping metrica legacy | Uso |
|---|---|---|---| ---| ---|
| ServiceAvailability (disponibilità servizio)| % (Minimo, massimo) | Disponibilità delle richieste di account con una granularità di un'ora| 1H | Disponibilità del servizio | Rappresenta la percentuale del totale passato richieste. Una richiesta viene considerata non riuscita a causa di un errore di sistema se il codice di stato è 410, 500 o 503. Usata per monitorare la disponibilità dell'account con una granularità di un'ora. |


### <a name="cassandra-api-metrics"></a>Metriche dell'API Cassandra

|Metrica (nome visualizzato per la metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Uso |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra richieste) | Count (conteggio) | Numero di richieste dell'API Cassandra| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tutti| Usata per monitorare le richieste di Cassandra con una granularità di un minuto. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60.|
| CassandraRequestCharges (Cassandra gli addebiti richiesta) | Conteggio (Sum, Min, Max, Avg) | Unità richiesta utilizzate dalle richieste dell'API Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Tutti| Usata per monitorare le UR usate al minuto da un account dell'API Cassandra.|
| CassandraConnectionClosures (Cassandra connessione chiusure) |Count (conteggio) |Numero di connessioni a Cassandra chiuse| ClosureReason, Region| Tutti | Usata per monitorare la connettività tra i client e l'API Cassandra di Azure Cosmos DB.|

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare e monitorare le metriche dal riquadro metriche dell'account Azure Cosmos DB](use-metrics.md)

* [Registrazione diagnostica in Azure Cosmos DB](logging.md)
