---
title: Usare mongoimport e mongorestore con l&quot;API di Azure Cosmos DB per MongoDB | Microsoft Docs
description: Informazioni su come usare mongoimport e mongorestore per importare dati in un account dell&quot;API per MongoDB
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
ms.date: 05/22/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ab4fb39f8784a2d6aa956056a30649aab1d08487
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---

# <a name="azure-cosmos-db-how-to-import-mongodb-data"></a>Azure Cosmos DB: procedura per l'importazione dei dati di MongoDB 

Per eseguire la migrazione di dati da MongoDB in un account Azure Cosmos DB per l'uso con l'API di MongoDB, è necessario eseguire queste operazioni:

* Scaricare *mongoimport.exe* o *mongorestore.exe* dall'[area download MongoDB](https://www.mongodb.com/download-center).
* Ottenere l'[API per la stringa di connessione di MongoDB](connect-mongodb-account.md).

Se si importano dati da MongoDB e si prevede di usarli con l'API di DocumentDB, è necessario usare l'Utilità di migrazione dati per importare i dati. Per altre informazioni, vedere [Utilità di migrazione dati](import-data.md).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Recupero della stringa di connessione
> * Importazione di dati di MongoDB tramite mongoimport
> * Importazione di dati di MongoDB tramite mongorestore

## <a name="prerequisites"></a>Prerequisiti

* Aumentare la velocità effettiva: la durata della migrazione dei dati dipende dalla quantità di velocità effettiva configurata per le raccolte. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Per altre informazioni sull'aumento della velocità effettiva nel [portale di Azure](https://portal.azure.com), vedere [Livelli di prestazioni e piani tariffari in Azure Cosmos DB](performance-levels.md).

* Abilitare SSL: Azure Cosmos DB ha standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare SSL quando si interagisce con l'account. Le procedure seguenti illustrano come abilitare SSL per *mongoimport* e *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Trovare le informazioni della stringa di connessione (host, porta, nome utente e password)

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com) fare clic sulla voce **Azure Cosmos DB**.
2. Nel riquadro **Sottoscrizioni** selezionare il nome dell'account.
3. Nel pannello **Stringa di connessione** fare clic su **Stringa di connessione**.  
Il riquadro destro contiene tutte le informazioni necessarie per connettersi correttamente all'account.

    ![Pannello "Stringa di connessione"](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>Eseguire l'importazione di dati nell'API per MongoDB con mongoimport

Per importare dati nell'account Azure Cosmos DB, usare il modello seguente per eseguire l'importazione. Per *host*, *username* e *password* inserire i valori specifici dell'account.  

Template:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Esempio:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>Eseguire l'importazione di dati nell'API per MongoDB con mongorestore

Per ripristinare i dati nell'API per l'account MongoDB, usare il modello seguente per eseguire l'importazione. Per *host*, *username* e *password* inserire i valori specifici dell'account.

Template:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Esempio:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Recupero della stringa di connessione
> * Importazione di dati di MongoDB tramite mongoimport
> * Importazione di dati di MongoDB tramite mongorestore

È ora possibile passare all'esercitazione successiva per ottenere informazioni su come eseguire query sui dati di MongoDB tramite Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Procedura per l'esecuzione di query sui dati di MongoDB](../cosmos-db/tutorial-query-mongodb.md)

