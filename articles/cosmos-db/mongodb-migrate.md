---
title: Usare mongoimport e mongorestore con l'API di Azure Cosmos DB per MongoDB | Microsoft Docs
description: Informazioni su come usare mongoimport e mongorestore per importare dati in un account dell'API per MongoDB
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 13422434e6392ec7681ec4478533c45a84f40c9a
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706977"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-mongodb-api-account"></a>Esercitazione: Eseguire la migrazione dei dati nell'account dell'API per MongoDB di Azure Cosmos DB

Questa esercitazione fornisce istruzioni su come eseguire la migrazione di dati archiviati in MongoDB all'account dell'API per MongoDB di Azure Cosmos DB. Se si importano dati da MongoDB e si prevede di usarli con l'API SQL di Azure Cosmos DB, è consigliabile usare lo [strumento di migrazione dei dati](import-data.md) per importare i dati.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Pianificare la migrazione
> * Prerequisiti per la migrazione
> * Eseguire la migrazione dei dati con mongoimport
> * Eseguire la migrazione dei dati con mongorestore

Prima della migrazione dei dati all'account dell'API di MongoDB, assicurarsi di avere alcuni dati campione di MongoDB. Se non si dispone di un database di MongoDB di esempio, è possibile scaricare e installare il [server MongoDB Community](https://www.mongodb.com/download-center), creare un database di esempio e usare mongoimport.exe o mongorestore.exe per caricare dati di esempio. 

## <a name="plan-for-migration"></a>Pianificare la migrazione

1. Creare in anticipo le raccolte e ridimensionarle:
        
   * Per impostazione predefinita, Azure Cosmos DB effettua il provisioning di una nuova raccolta MongoDB con 1.000 unità richiesta al secondo (UR/sec). Prima di iniziare la migrazione tramite mongoimport o mongorestore, creare tutte le raccolte dal [portale di Azure](https://portal.azure.com) o da strumenti e driver di MongoDB. Se le dimensioni dei dati superano 10 GB, assicurarsi di creare una [raccolta partizionata](partition-data.md) con una chiave di partizione appropriata. MongoDB consiglia di archiviare i dati entità in raccolte. È possibile condividere il percorso di entità di dimensioni e velocità effettiva di provisioning simili a livello del database Azure Cosmos.

   * Nel [portale di Azure](https://portal.azure.com) aumentare la velocità effettiva delle raccolte da 1.000 unità richiesta/secondo per una singola raccolta a partizione e da 2.500 unità richiesta/secondo per una raccolta partizionata solo per la durata della migrazione. Con una velocità effettiva più elevata, è possibile evitare la limitazione di velocità e completare più rapidamente la migrazione. È possibile ridurre la velocità effettiva immediatamente dopo la migrazione per ridurre i costi.

   * Oltre a impostare le unità richiesta/secondo a livello di raccolta, è possibile eseguire il provisioning di unità richiesta al secondo per un set di raccolte a livello di database padre. Ciò presuppone che vengano prima creati il database e le raccolte e che venga definita una chiave di partizione per ogni raccolta.

   * È possibile creare raccolte partizionate usando lo strumento, il driver o l'SDK che si preferisce. In questo esempio, per creare una raccolta partizionata viene usata la shell di Mongo:

        ```bash
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        Risultati:

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

1. Calcolare l'addebito approssimativo delle unità richiesta per la scrittura di un singolo documento:

   a. Connettersi all'account dell'API MongoDB di Azure Cosmos DB dalla shell di MongoDB. È possibile trovare istruzioni in [Connettere un'applicazione MongoDB ad Azure Cosmos DB](connect-mongodb-account.md).
    
   b. Eseguire un comando di inserimento di esempio usando uno dei documenti di esempio dalla shell di MongoDB:
   
      ```bash
      db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
      ```
        
   c. Eseguire ```db.runCommand({getLastRequestStatistics: 1})```. Viene restituita una risposta simile alla seguente:
     
      ```bash
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
      ```
        
    d. Prendere nota dell'addebito della richiesta.
    
1. Determinare la latenza dal proprio computer al servizio cloud Azure Cosmos DB:
    
    a. Abilitare la registrazione dettagliata dalla shell di MongoDB usando questo comando: ```setVerboseShell(true)```
    
    b. Eseguire una semplice query sul database: ```db.coll.find().limit(1)```. Viene restituita una risposta simile alla seguente:

       ```bash
       Fetched 1 record(s) in 100(ms)
       ```
        
1. Rimuovere il documento inserito prima della migrazione per verificare che non siano presenti documenti duplicati. È possibile rimuovere i documenti usando questo comando: ```db.coll.remove({})```

1. Calcolare i valori *batchSize* e *numInsertionWorkers* approssimativi:

    * Per *batchSize* dividere le unità richiesta totali di cui è stato effettuato il provisioning per le unità richiesta consumate dalla scrittura di un singolo documento nel passaggio 3.
    
    * Se la dimensione calcolata *batchSize* <= 24, usare questo numero come valore di *batchSize*.
    
    * Se la dimensione calcolata *batchSize* > 24, impostare il valore di *batchSize* su 24.
    
    * Per *numInsertionWorkers* usare questa equazione: *numInsertionWorkers = (velocità effettiva con provisioning * latenza in secondi) / (dimensioni del batch * unità richiesta utilizzate per una singola operazione di scrittura)*.
        
    |Proprietà|Valore|
    |--------|-----|
    |batchSize| 24 |
    |Unità richiesta con provisioning | 10000 |
    |Latenza | 0,100 s |
    |Unità richiesta addebitate per la scrittura di 1 documento | 10 UR |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 UR x 0,1 s) / (24 x 10 UR) = 4,1666*

1. Eseguire il comando di migrazione. Le opzioni per la migrazione dei dati sono descritte nelle prossime sezioni.

   ```bash
   mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Oppure con mongorestore (assicurarsi che per tutte le raccolte la velocità effettiva sia impostata su un numero di unità richiesta uguale o maggiore rispetto a quello usato nei calcoli precedenti):
   
   ```bash
   mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="prerequisites-for-migration"></a>Prerequisiti per la migrazione

* **Aumentare la velocità effettiva:** la durata della migrazione dei dati dipende dalla velocità effettiva configurata per una singola raccolta o un set di raccolte. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Per altre informazioni sull'aumento della velocità effettiva nel [portale di Azure](https://portal.azure.com), vedere [Livelli di prestazioni e piani tariffari in Azure Cosmos DB](performance-levels.md).

* **Abilitare SSL:** Azure Cosmos DB ha standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare SSL quando si interagisce con l'account. Le procedure seguenti illustrano come abilitare SSL per mongoimport e mongorestore.

* **Creare le risorse di Azure Cosmos DB:** prima di iniziare la migrazione dei dati, creare in anticipo tutte le raccolte dal portale di Azure. Se si esegue la migrazione a un account Azure Cosmos DB con velocità effettiva a livello di database, assicurarsi di fornire una chiave di partizione quando si creano le raccolte di Azure Cosmos DB.

## <a name="get-your-connection-string"></a>Ottenere una stringa di connessione 

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com) fare clic sulla voce **Azure Cosmos DB**.
1. Nel riquadro **Sottoscrizioni** selezionare il nome dell'account.
1. Nel pannello **Stringa di connessione** fare clic su **Stringa di connessione**.

   Il riquadro destro contiene tutte le informazioni necessarie per connettersi correttamente all'account.

   ![Pannello Stringa di connessione](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="migrate-data-by-using-mongoimport"></a>Eseguire la migrazione dei dati con mongoimport

Per importare dati nell'account Azure Cosmos DB, usare il modello seguente. Per *host*, *username* e *password* inserire i valori specifici dell'account.  

Template:

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Esempio:  

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="migrate-data-by-using-mongorestore"></a>Eseguire la migrazione dei dati con mongorestore

Per ripristinare i dati nell'API per l'account MongoDB, usare il modello seguente per eseguire l'importazione. Per *host*, *username* e *password* inserire i valori specifici dell'account.

Template:

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Esempio:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="next-steps"></a>Passaggi successivi

È possibile passare all'esercitazione successiva per ottenere informazioni su come eseguire query sui dati di MongoDB tramite Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Procedura per l'esecuzione di query sui dati di MongoDB](../cosmos-db/tutorial-query-mongodb.md)
