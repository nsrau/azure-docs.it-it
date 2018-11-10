---
title: Eseguire la migrazione dei dati nell'account dell'API Cassandra di Azure Cosmos DB
description: Informazioni su come usare il comando Copy di CQL e Spark per copiare dati da Apache Cassandra nell'API Cassandra di Azure Cosmos DB.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: 56fc07c6d775ee8015ce244acb7782607bda802a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739788"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Esercitazione: Eseguire la migrazione dei dati nell'account dell'API Cassandra di Azure Cosmos DB

Questa esercitazione fornisce istruzioni su come eseguire la migrazione dei dati Apache Cassandra nell'API Cassandra di Azure Cosmos DB. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Pianificare la migrazione
> * Prerequisiti per la migrazione
> * Eseguire la migrazione dei dati usando il comando cqlsh COPY
> * Eseguire la migrazione dei dati usando Spark 

## <a name="plan-for-migration"></a>Pianificare la migrazione

Prima di eseguire la migrazione dei dati nell'API Cassandra di Azure Cosmos DB, è necessario stimare le esigenze di velocità effettiva del carico di lavoro. In generale, è consigliabile iniziare con la velocità effettiva media necessaria per le operazioni CRUD, quindi includere la velocità effettiva aggiuntiva necessaria per le operazioni picco o di estrazione, trasformazione e caricamento (ETL). Per pianificare la migrazione, sono necessari i dettagli seguenti: 

* **Dimensioni dei dati esistenti o dimensioni dei dati stimate:** definisce i requisiti minimi di dimensione e velocità effettiva del database. Se si sta stimando la dimensione dei dati per una nuova applicazione, è possibile presumere che i dati vengano distribuiti uniformemente tra le righe e stimare il valore moltiplicando la dimensione dei dati. 

* **Velocità effettiva necessaria:** la velocità effettiva approssimativa in lettura (query/get) e in scrittura (aggiornamento/eliminazione/inserimento). Questo valore è obbligatorio per calcolare le unità richiesta necessarie insieme alle dimensioni dei dati con stato stabile.  

* **Ottenere lo schema:** connettersi al cluster Cassandra esistente tramite cqlsh ed esportare lo schema da Cassandra: 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

Dopo avere identificato i requisiti del carico di lavoro esistente, è necessario creare un account, un database e i contenitori Azure Cosmos DB in base ai requisiti di velocità effettiva raccolti.  

* **Determinare l'addebito di unità di richieste per un'operazione:** è possibile determinare le unità di richieste usando l'SDK dell'API Cassandra di Azure Cosmos DB preferito. Questo esempio illustra la versione .NET per ottenere gli addebiti delle unità richiesta.

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

* **Allocare la velocità effettiva necessaria:** Azure Cosmos DB può eseguire automaticamente la scalabilità della risorsa di archiviazione e della velocità effettiva man mano che aumentano i requisiti. È possibile stimare le esigenze di velocità effettiva usando il [calcolatore di unità di richieste di Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

## <a name="prerequisites-for-migration"></a>Prerequisiti per la migrazione

* **Creare le tabelle nell'account dell'API Cassandra di Azure Cosmos DB:** prima di iniziare la migrazione dei dati, creare in anticipo tutte le tabelle dal portale di Azure o da cqlsh. Se si esegue la migrazione a un account Azure Cosmos DB con velocità effettiva a livello di database, assicurarsi di fornire una chiave di partizione quando si creano i contenitori di Azure Cosmos DB.

* **Aumentare la velocità effettiva:** la durata della migrazione dei dati dipende dalla velocità effettiva di cui è stato eseguito il provisioning per le tabelle in Azure Cosmos DB. Aumentare la velocità effettiva per la durata della migrazione. Con una velocità effettiva più elevata, è possibile evitare la limitazione di velocità e completare più rapidamente la migrazione. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Per altre informazioni sull'aumento della velocità effettiva, vedere [impostare la velocità effettiva](set-throughput.md) per i contenitori di Azure Cosmos DB. È consigliabile anche disporre di account Azure Cosmos DB nella stessa area del database di origine. 

* **Abilitare SSL:** Azure Cosmos DB ha standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare SSL quando si interagisce con l'account. Quando si usa CQL con SSH, è possibile fornire le informazioni SSL.

## <a name="options-to-migrate-data"></a>Opzioni per la migrazione dei dati

È possibile spostare dati da carichi di lavoro esistenti di Cassandra in Azure Cosmos DB usando le opzioni seguenti:

* [Usando il comando cqlsh COPY](#using-cqlsh-copy-command)  
* [Usando Spark](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Eseguire la migrazione dei dati usando il comando cqlsh COPY

Il [comando COPY di CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) viene usato per copiare i dati locali nell'account dell'API Cassandra di Azure Cosmos DB. Per copiare i dati, usare la procedura seguente:

1. Ottenere informazioni sulla stringa di connessione dell'account dell'API Cassandra:

   * Accedere al [portale di Azure](https://portal.azure.com) e passare all'account Azure Cosmos DB.

   * Aprire il riquadro **Stringa di connessione** che contiene tutte le informazioni necessarie per connettersi all'account dell'API Cassandra da cqlsh.

2. Accedere a cqlsh usando le informazioni di connessione del portale.

3. Usare il comando COPY di CQL per copiare i dati locali nell'account dell'API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Eseguire la migrazione dei dati usando Spark 

Usare la procedura seguente per eseguire la migrazione dei dati nell'API Cassandra di Azure Cosmos DB con Spark:

- Effettuare il provisioning di [Azure Databricks](cassandra-spark-databricks.md) o un [cluster HDInsight](cassandra-spark-hdinsight.md) 

- Spostare i dati nell'endpoint API Cassandra di destinazione usando l'[operazione di copia di tabella](cassandra-spark-table-copy-ops.md) 

La migrazione dei dati usando i processi spark è un'opzione consigliata se i dati risiedono in un cluster esistente nelle macchine virtuali di Azure o in qualsiasi altro cloud. Per questa opzione è necessario impostare spark come intermediario per l'inserimento una sola volta o su base regolare. È possibile accelerare la migrazione usando la connettività ExpressRoute tra locale e Azure. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come migrare i dati utente in un account dell'API Cassandra di Azure Cosmos DB. È ora possibile passare alla sezione dei concetti per altre informazioni su Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](../cosmos-db/consistency-levels.md)


