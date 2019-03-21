---
title: "Esercitazione: eseguire la migrazione dei dati in un account dell'API Cassandra in Azure Cosmos DB"
description: Questa esercitazione illustra come usare il comando Copy di CQL e Spark per copiare dati da Apache Cassandra a un account dell'API Cassandra in Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
Customer intent: As a developer, I want to migrate my existing Cassandra workloads to Azure Cosmos DB so that the overhead to manage resources, clusters, and garbage collection is automatically handled by Azure Cosmos DB.
ms.openlocfilehash: c9f7ec5009c9299e317d9b10f857e326d25fa005
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120109"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Esercitazione: eseguire la migrazione dei dati in un account dell'API Cassandra in Azure Cosmos DB

Gli sviluppatori potrebbero avere carichi di lavoro Cassandra esistenti in esecuzione in locale o nel cloud e volerne eseguire la migrazione in Azure. È possibile eseguire la migrazione di questi carichi di lavoro in un account dell'API Cassandra in Azure Cosmos DB. Questa esercitazione fornisce istruzioni sulle diverse opzioni disponibili per eseguire la migrazione dei dati Apache Cassandra nell'account dell'API Cassandra di Azure Cosmos DB.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Pianificare la migrazione
> * Prerequisiti per la migrazione
> * Eseguire la migrazione dei dati usando il comando cqlsh COPY
> * Eseguire la migrazione dei dati usando Spark

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites-for-migration"></a>Prerequisiti per la migrazione

* **Elaborare una stima delle esigenze di velocità effettiva:** prima di eseguire la migrazione dei dati nell'account dell'API Cassandra in Azure Cosmos DB, è necessario elaborare una stima delle esigenze di velocità effettiva del carico di lavoro. In generale, è consigliabile iniziare con la velocità effettiva media necessaria per le operazioni CRUD, quindi includere la velocità effettiva aggiuntiva necessaria per le operazioni picco o di estrazione, trasformazione e caricamento (ETL). Per pianificare la migrazione, sono necessari i dettagli seguenti: 

  * **Dimensioni dei dati esistenti o stimate:** definisce le dimensioni minime del database e i requisiti di velocità effettiva. Se si sta stimando la dimensione dei dati per una nuova applicazione, è possibile presumere che i dati vengano distribuiti uniformemente tra le righe e stimare il valore moltiplicando la dimensione dei dati. 

  * **Velocità effettiva richiesta:** la velocità effettiva approssimativa in lettura (query/get) e in scrittura (update/delete/insert). Questo valore è obbligatorio per calcolare le unità richiesta necessarie insieme alle dimensioni dei dati con stato stabile.  

  * **Schema:** connettersi al cluster Cassandra esistente tramite cqlsh ed esportare lo schema da Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Dopo avere identificato i requisiti del carico di lavoro esistente, è necessario creare un account, un database e i contenitori Azure Cosmos in base ai requisiti di velocità effettiva raccolti.  

  * **Determinare gli addebiti di UR per un'operazione:** è possibile determinare le UR usando uno degli SDK supportati dall'API Cassandra. Questo esempio illustra la versione .NET per ottenere gli addebiti delle unità richiesta.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         string value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Allocare la velocità effettiva richiesta:** Azure Cosmos DB può ridimensionare automaticamente l'archiviazione e la velocità effettiva man mano che aumentano i requisiti. È possibile stimare le esigenze di velocità effettiva usando il [calcolatore di unità di richieste di Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Creare tabelle nell'account dell'API Cassandra:** prima di iniziare la migrazione dei dati, creare tutte le tabelle dal portale di Azure o da cqlsh. Se si esegue la migrazione a un account Azure Cosmos con velocità effettiva a livello di database, assicurarsi di fornire una chiave di partizione quando si creano i contenitori di Azure Cosmos.

* **Aumentare la velocità effettiva:** la durata della migrazione dei dati dipende dalla velocità effettiva di cui è stato eseguito il provisioning per le tabelle in Azure Cosmos DB. Aumentare la velocità effettiva per la durata della migrazione. Con una velocità effettiva più elevata, è possibile evitare la limitazione di velocità e completare più rapidamente la migrazione. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. È consigliabile anche disporre di account Azure Cosmos nella stessa area del database di origine. 

* **Abilitare SSL:** Azure Cosmos DB presenta standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare SSL quando si interagisce con l'account. Quando si usa CQL con SSH, è possibile fornire le informazioni SSL.

## <a name="options-to-migrate-data"></a>Opzioni per la migrazione dei dati

È possibile spostare dati da carichi di lavoro esistenti di Cassandra in Azure Cosmos DB usando le opzioni seguenti:

* [Usando il comando cqlsh COPY](#migrate-data-using-cqlsh-copy-command)  
* [Usando Spark](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Eseguire la migrazione dei dati usando il comando cqlsh COPY

Il [comando COPY di CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) viene usato per copiare i dati locali nell'account dell'API Cassandra in Azure Cosmos DB. Per copiare i dati, usare la procedura seguente:

1. Ottenere informazioni sulla stringa di connessione dell'account dell'API Cassandra:

   * Accedere al [portale di Azure](https://portal.azure.com) e passare all'account Azure Cosmos.

   * Aprire il riquadro **Stringa di connessione** che contiene tutte le informazioni necessarie per connettersi all'account dell'API Cassandra da cqlsh.

2. Accedere a cqlsh usando le informazioni di connessione del portale.

3. Usare il comando COPY di CQL per copiare i dati locali nell'account dell'API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Eseguire la migrazione dei dati usando Spark 

Usare questa procedura per eseguire la migrazione dei dati nell'account dell'API Cassandra con Spark:

- Effettuare il provisioning di un [cluster di Azure Databricks](cassandra-spark-databricks.md) o un [cluster HDInsight](cassandra-spark-hdinsight.md) 

- Spostare i dati nell'endpoint API Cassandra di destinazione usando l'[operazione di copia di tabella](cassandra-spark-table-copy-ops.md) 

La migrazione dei dati usando i processi Spark è un'opzione consigliata se i dati risiedono in un cluster esistente nelle macchine virtuali di Azure o in qualsiasi altro cloud. Per questa opzione è necessario impostare Spark come intermediario per l'inserimento una sola volta o su base regolare. È possibile accelerare la migrazione usando la connettività Azure ExpressRoute tra locale e Azure. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più è possibile eliminare il gruppo di risorse, l'account Azure Cosmos e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina** e quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come migrare i dati utente in un account dell'API Cassandra in Azure Cosmos DB. È ora possibile passare all'articolo seguente per apprendere altri concetti di Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](../cosmos-db/consistency-levels.md)


