---
title: Importare i dati di Cassandra in Azure Cosmos DB | Microsoft Docs
description: Informazioni su come usare il comando COPY di CQL per copiare dati di Cassandra in Azure Cosmos DB.
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 21168d0862cfdaaaced60fa80a2dc04859f49550
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: importare i dati di Cassandra

Questa esercitazione fornisce le istruzioni per l'importazione di dati di Cassandra in Azure Cosmos DB tramite il comando COPY di Cassandra Query Language (CQL). 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Recupero della stringa di connessione
> * Importazione di dati tramite il comando cqlsh COPY
> * Importazione tramite il connettore Spark 

# <a name="prerequisites"></a>prerequisiti

* Installare [Apache Cassandra](http://cassandra.apache.org/download/) e verificare che sia presente *cqlsh*.
* Aumentare la velocità effettiva: la durata della migrazione dei dati dipende dalla velocità effettiva allocata per le tabelle. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Per altre informazioni sull'aumento della velocità effettiva nel [portale di Azure](https://portal.azure.com), vedere [Impostare la velocità effettiva per i contenitori di Azure Cosmos DB](set-throughput.md).
* Abilitare SSL: Azure Cosmos DB ha standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare SSL quando si interagisce con l'account. Quando si usa CQL con SSH, è possibile fornire le informazioni SSL. 

## <a name="find-your-connection-string"></a>Trovare la stringa di connessione

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Cosmos DB** all'estrema sinistra.

2. Nel riquadro **Sottoscrizioni** selezionare il nome dell'account.

3. Fare clic su **Stringa di connessione**. Il riquadro destro contiene tutte le informazioni necessarie per connettersi correttamente all'account.

    ![Pagina Stringa di connessione](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Usare cqlsh COPY

Per importare i dati di Cassandra in Azure Cosmos DB per l'uso con l'API Cassandra, seguire questa procedura:

1. Accedere a cqhsh usando le informazioni di connessione del portale.
2. Usare il [comando COPY di CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) per copiare i dati locali nell'endpoint API di Apache Cassandra. Verificare che l'origine e la destinazione si trovino nello stesso data center per ridurre al minimo i problemi di latenza.

### <a name="guide-for-moving-data-with-cqlsh"></a>Guida per lo spostamento dei dati con cqlsh

1. Creare prima la tabella e ridimensionarla:
    * Per impostazione predefinita, Azure Cosmos DB effettua il provisioning di una nuova tabella di API Cassandra con 1.000 unità richiesta al secondo (UR/s). Il provisioning per la creazione basata su CQL è pari a 400 UR/s. Prima di iniziare la migrazione tramite cqlsh, creare tutte le tabelle dal [portale di Azure](https://portal.azure.com) o da cqlsh. 

    * Nel [portale di Azure](https://portal.azure.com) aumentare la velocità effettiva delle tabelle dal valore predefinito (400 o 1000 UR/s) a 10.000 UR/sec per la durata della migrazione. Con una velocità effettiva più elevata, è possibile evitare la limitazione e completare più rapidamente la migrazione. Con la fatturazione oraria in Azure Cosmos DB, è possibile ridurre la velocità effettiva immediatamente dopo la migrazione per ridurre i costi.

2. Determinare l'addebito delle unità richiesta per un'operazione. A tale scopo è possibile usare l'SDK per le API Cassandra di Azure Cosmos DB di propria scelta. Questo esempio illustra la versione .NET per ottenere gli addebiti delle unità richiesta. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Determinare la latenza dal proprio computer al servizio Azure Cosmos DB. All'interno di un data center di Azure, la latenza deve essere un valore basso a una sola cifra espresso in millisecondi. All'esterno del data center di Azure, è possibile usare psping o azurespeed.com per ottenere la latenza approssimativa dalla posizione.   

4. Calcolare i valori appropriati per i parametri (NUMPROCESS, INGESTRATE, MAXBATCHSIZE o MINBATCHSIZE) che forniscono prestazioni ottimali. 

5. Eseguire il comando di migrazione finale. L'esecuzione di questo comando presuppone che sia stato avviato cqlsh con le informazioni della stringa di connessione.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Usare Spark per importare dati

Per i dati che risiedono in un cluster esistente in macchine virtuali di Azure, è anche possibile importare i dati tramite Spark. Per questa operazione è necessario impostare Spark come intermediario per l'inserimento una sola volta o su base regolare. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come completare le attività seguenti:

> [!div class="checklist"]
> * Recuperare la stringa di connessione
> * Importare dati tramite il comando COPY di CQL
> * Importare dati tramite il connettore Spark 

È ora possibile passare alla sezione Concetti per altre informazioni su Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](../cosmos-db/consistency-levels.md)
