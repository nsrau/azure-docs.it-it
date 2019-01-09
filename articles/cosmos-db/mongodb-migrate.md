---
title: Eseguire la migrazione dei dati di MongoDB in Azure Cosmos DB tramite mongoimport e mongorestore
description: Verrà illustrato come usare mongoimport e mongorestore per importare dati in Cosmos DB.
keywords: mongoimport, mongorestore
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: rimman
ms.custom: mvc
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 4cd30c7981cd6807113729292db403a80cbddef0
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793751"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>Eseguire la migrazione dei dati di MongoDB in Azure Cosmos DB

 Questa esercitazione fornisce istruzioni su come eseguire la migrazione di dati archiviati in MongoDB in Azure Cosmos DB configurato per uso dell'API di Azure Cosmos DB per MongoDB. Se si importano dati da MongoDB e si prevede di usarli con l'API SQL di Azure Cosmos DB, è consigliabile usare lo [strumento di migrazione dei dati](import-data.md) per importare i dati.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare un piano di migrazione.
> * Eseguire la migrazione dei dati con mongoimport.
> * Eseguire la migrazione dei dati con mongorestore.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di avviare la migrazione, esaminare e completare i prerequisiti seguenti.

### <a name="plan-for-the-migration"></a>Pianificare la migrazione

Questa sezione descrive come pianificare la migrazione dei dati. Verranno stimati gli addebiti delle unità richiesta, determinata la latenza dal computer al servizio cloud e calcolati le dimensioni del batch e il numero di ruoli di lavoro di inserimento.


#### <a name="pre-create-and-scale-your-collections"></a>Creare in anticipo le raccolte e ridimensionarle

Prima di eseguire la migrazione tramite mongoimport o mongorestore, creare tutte le raccolte dal [portale di Azure](https://portal.azure.com) o da strumenti e driver di MongoDB. 

Dal [portale di Azure](https://portal.azure.com) aumentare la velocità effettiva delle raccolte per la migrazione. Con una velocità effettiva più elevata, è possibile evitare la limitazione di velocità e completare più rapidamente la migrazione. È possibile ridurre la velocità effettiva immediatamente dopo la migrazione per ridurre i costi.

Oltre eseguire il provisioning della velocità effettiva a livello di raccolta, è anche possibile eseguire il provisioning della velocità effettiva a livello di database per un set di raccolte, in modo da condividere la velocità effettiva sottoposta a provisioning. È necessario creare in anticipo il database e le raccolte e definire una chiave di partizione per ogni raccolta nel database di velocità effettiva condiviso.

È possibile creare raccolte partizionate usando lo strumento, il driver o l'SDK che si preferisce. In questo esempio, per creare una raccolta partizionata viene usata la shell di Mongo:

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Il comando restituisce i risultati seguenti:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>Calcolare l'addebito approssimativo delle unità richiesta per la scrittura di un singolo documento

Dalla shell MongoDB connettersi all'account Cosmos configurato per l'uso dell'API di Cosmos DB per MongoDB. È possibile trovare istruzioni in [Connettere un'applicazione MongoDB a Cosmos DB](connect-mongodb-account.md).

Eseguire quindi un comando di inserimento di esempio usando uno dei documenti:
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
Eseguire il comando `db.runCommand({getLastRequestStatistics: 1})`.

Viene restituita una risposta simile all'output seguente:
     
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
        
Prendere nota dell'addebito della richiesta.
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>Determinare la latenza dal proprio computer a Cosmos DB
    
Abilitare la registrazione dettagliata dalla shell di MongoDB con il comando `setVerboseShell(true)`.
    
Eseguire una query di base sul database con il comando `db.coll.find().limit(1)`.

Viene restituita una risposta simile all'output seguente:

```bash
Fetched 1 record(s) in 100(ms)
```
        
Prima di eseguire la migrazione, rimuovere il documento inserito per verificare che non siano presenti documenti duplicati. È possibile rimuovere i documenti con il comando `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>Calcolare i valori approssimativi per le proprietà batchSize e numInsertionWorkers

Per la proprietà **batchSize** dividere la velocità effettiva con provisioning totale (UR/sec) per le UR usate per la scrittura di un singolo documento, come specificato nella sezione "Determinare la latenza dal proprio computer a Cosmos DB". Se il valore calcolato è minore o uguale a 24, usare tale numero come valore della proprietà. Se il valore calcolato è maggiore di 24, impostare il valore della proprietà su 24.
    
Per il valore della proprietà **numInsertionWorkers**, usare questa equazione:

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

È possibile usare i valori seguenti per calcolare un valore per la proprietà **numInsertionWorkers**:

| Proprietà | Valore |
|--------|-----|
| **batchSize** | 24 |
| UR con provisioning | 10.000 |
| Latenza | 0,100 s |
| UR usate | 10 UR |
| **numInsertionWorkers** | (10.000 UR x 0,100 s) / (24 x 10 UR) = **4,1666** |

Eseguire il comando di migrazione **mongoimport**. I parametri del comando sono descritti più avanti in questo articolo.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

È anche possibile usare il comando **mongorestore**. Assicurarsi che per tutte le raccolte la velocità effettiva sia impostata su un numero di unità richiesta uguale o maggiore rispetto a quello usato nei calcoli precedenti.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>Completare i prerequisiti

Dopo aver pianificato la migrazione, completare i passaggi seguenti: 

* **Ottenere dati di esempio**: Assicurarsi di disporre di alcuni dati di esempio prima di avviare la migrazione. 

* **Aumentare la velocità effettiva**: la durata della migrazione dei dati dipende dalla velocità effettiva di cui viene effettuato il provisioning per una singola raccolta o un database. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. 

* **Abilitare SSL:**  Cosmos DB presenta standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare SSL quando si interagisce con l'account Cosmos. Le procedure seguenti illustrano come abilitare SSL per i comandi mongoimport e mongorestore.

* **Creare le risorse di Cosmos DB**: prima di iniziare la migrazione, creare tutte le raccolte dal portale di Azure. Se si esegue la migrazione a un account Cosmos con velocità effettiva con provisioning a livello di database, assicurarsi di specificare una chiave di partizione quando si creano le raccolte.

* **Ottenere una stringa di connessione**: Nel [portale di Azure](https://portal.azure.com) selezionare la voce **Azure Cosmos DB** sulla sinistra. In **Sottoscrizioni** selezionare il nome dell'account. In **Stringa di connessione** selezionare **Stringa di connessione** . Sul lato destro del portale vengono visualizzate le informazioni necessarie per la connessione all'account:

    ![Informazioni sulla stringa di connessione](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Usare mongoimport

Per importare dati nell'account Cosmos, usare il modello seguente.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Sostituire i parametri \<your_hostname>, \<your_username> e \<your_password> con i valori specifici per il proprio account. Nell'esempio seguente usare **sampleDB** come valore per \<your_database> e **sampleColl** come valore per \<your_collection>:

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Usare mongorestore

Per ripristinare i dati nell'account Cosmos configurato con l'API di Cosmos DB per MongoDB, usare il modello seguente per eseguire l'importazione.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Sostituire i parametri \<your_hostname>, \<your_username> e \<your_password> con i valori specifici per il proprio account. Nell'esempio seguente viene usato **./dumps/dump-2016-12-07** come valore per \<path_to_backup>:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse non servono più, è possibile eliminare il gruppo di risorse, l'account Cosmos e tutte le risorse correlate. Per eliminare il gruppo di risorse, completare i passaggi seguenti:

1. Passare al gruppo di risorse in cui si è creato l'account Cosmos.
1. Selezionare **Elimina gruppo di risorse**.
1. Confermare il nome del gruppo di risorse da eliminare, quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per ottenere informazioni su come eseguire query sui dati tramite l'API di Azure Cosmos DB per MongoDB. 

> [!div class="nextstepaction"]
> [Procedura per l'esecuzione di query sui dati di MongoDB](../cosmos-db/tutorial-query-mongodb.md)
