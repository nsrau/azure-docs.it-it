---
title: Usare mongoimport e mongorestore con l'API di Azure Cosmos DB per MongoDB | Microsoft Docs
description: Informazioni su come usare mongoimport e mongorestore per importare dati in un account dell'API per MongoDB
keywords: mongoimport, mongorestore
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: importare i dati di MongoDB 

Per eseguire la migrazione di dati da MongoDB in un account Azure Cosmos DB per l'uso con l'API di MongoDB, è necessario eseguire queste operazioni:

* Scaricare *mongoimport.exe* o *mongorestore.exe* dall'[area download MongoDB](https://www.mongodb.com/download-center).
* Ottenere l'[API per la stringa di connessione di MongoDB](connect-mongodb-account.md).

Se si importano dati da MongoDB e si prevede di usarli con Azure Cosmos DB, è necessario usare lo [strumento di migrazione dei dati](import-data.md) per importare i dati.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Recupero della stringa di connessione
> * Importazione di dati di MongoDB tramite mongoimport
> * Importazione di dati di MongoDB tramite mongorestore

## <a name="prerequisites"></a>prerequisiti

* Aumentare la velocità effettiva: la durata della migrazione dei dati dipende dalla quantità di velocità effettiva configurata per le raccolte. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Per altre informazioni sull'aumento della velocità effettiva nel [portale di Azure](https://portal.azure.com), vedere [Livelli di prestazioni e piani tariffari in Azure Cosmos DB](performance-levels.md).

* Abilitare SSL: Azure Cosmos DB ha standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare SSL quando si interagisce con l'account. Le procedure seguenti illustrano come abilitare SSL per mongoimport e mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Trovare le informazioni della stringa di connessione (host, porta, nome utente e password)

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com) fare clic sulla voce **Azure Cosmos DB**.
2. Nel riquadro **Sottoscrizioni** selezionare il nome dell'account.
3. Nel pannello **Stringa di connessione** fare clic su **Stringa di connessione**.  
Il riquadro destro contiene tutte le informazioni necessarie per connettersi correttamente all'account.

    ![Pannello Stringa di connessione](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Eseguire l'importazione di dati nell'API per MongoDB con mongoimport

Per importare dati nell'account Azure Cosmos DB, usare il modello seguente. Per *host*, *username* e *password* inserire i valori specifici dell'account.  

Template:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Esempio:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Eseguire l'importazione di dati nell'API per MongoDB con mongorestore

Per ripristinare i dati nell'API per l'account MongoDB, usare il modello seguente per eseguire l'importazione. Per *host*, *username* e *password* inserire i valori specifici dell'account.

Template:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Esempio:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Guida per una migrazione corretta

1. Creare in anticipo le raccolte e ridimensionarle:
        
    * Per impostazione predefinita, Azure Cosmos DB effettua il provisioning di una nuova raccolta MongoDB con 1.000 unità richiesta (UR). Prima di iniziare la migrazione tramite mongoimport, mongorestore o mongomirror, creare tutte le raccolte dal [portale di Azure](https://portal.azure.com) o da strumenti e driver di MongoDB. Se le dimensioni della raccolta superano 10 GB, assicurarsi di creare una [raccolta partizionata](partition-data.md) con una chiave di partizione appropriata.

    * Dal [portale di Azure](https://portal.azure.com) aumentare la velocità effettiva delle raccolte da 1.000 unità richiesta per una raccolta a partizione singola e da 2.500 unità richiesta per una raccolta partizionata al solo scopo di migrazione. Con una velocità effettiva più elevata, è possibile evitare la limitazione e completare più rapidamente la migrazione. Con la fatturazione oraria in Azure Cosmos DB, è possibile ridurre la velocità effettiva immediatamente dopo la migrazione per ridurre i costi.

2. Calcolare l'addebito approssimativo delle unità richiesta per la scrittura di un singolo documento:

    a. Connettersi al database Azure Cosmos DB MongoDB dalla shell di MongoDB. È possibile trovare istruzioni in [Connettere un'applicazione MongoDB ad Azure Cosmos DB](connect-mongodb-account.md).
    
    b. Eseguire un comando di inserimento di esempio usando uno dei documenti di esempio dalla shell di MongoDB:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Eseguire ```db.runCommand({getLastRequestStatistics: 1})``` e si riceverà una risposta simile alla seguente:
     
        ```
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
    
3. Determinare la latenza dal proprio computer al servizio cloud Azure Cosmos DB:
    
    a. Abilitare la registrazione dettagliata dalla shell di MongoDB usando questo comando: ```setVerboseShell(true)```
    
    b. Eseguire una semplice query sul database: ```db.coll.find().limit(1)```. Si riceverà una risposta simile alla seguente:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Rimuovere il documento inserito prima della migrazione per verificare che non siano presenti documenti duplicati. È possibile rimuovere i documenti usando questo comando: ```db.coll.remove({})```

5. Calcolare i valori *batchSize* e *numInsertionWorkers* approssimativi:

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

6. Eseguire il comando di migrazione finale:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Passaggi successivi

È possibile passare all'esercitazione successiva per ottenere informazioni su come eseguire query sui dati di MongoDB tramite Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Procedura per l'esecuzione di query sui dati di MongoDB](../cosmos-db/tutorial-query-mongodb.md)
