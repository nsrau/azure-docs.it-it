---
title: Eseguire la migrazione di dati a un account Azure DocumentDB con supporto del protocollo per MongoDB | Documentazione Microsoft
description: Informazioni su come usare mongoimport e mongorestore per importare dati in un account DocumentDB con supporto del protocollo per MongoDB, ora disponibile in anteprima.
keywords: migrate
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 2dc98400ed4eaa2263d73f0718f5eaee260d48f5
ms.openlocfilehash: 80665bc84948696b05b04536a066ebebb5a81e89


---
# <a name="migrate-data-to-documentdb-with-protocol-support-for-mongodb"></a>Eseguire la migrazione di dati a DocumentDB con supporto del protocollo per MongoDB
Per eseguire la migrazione di dati a un account Azure DocumentDB con supporto del protocollo per MongoDB, è necessario:

* Scaricare *mongoimport.exe* o *mongorestore.exe* dall'[area download MongoDB](https://www.mongodb.com/download-center).
* Ottenere il [supporto DocumentDB per la stringa di connessione MongoDB](documentdb-connect-mongodb-account.md).

## <a name="before-you-begin"></a>Prima di iniziare

* Aumentare la velocità effettiva: la durata della migrazione dei dati dipende dalla quantità di velocità effettiva configurata per le raccolte. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Per altre informazioni sull'aumento della velocità effettiva nel [portale di Azure](https://portal.azure.com), vedere [Livelli di prestazioni e piani tariffari in DocumentDB](documentdb-performance-levels.md).

* Abilitare SSL: DocumentDB ha standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare SSL quando si interagisce con l'account. Le procedure seguenti illustrano come abilitare SSL per *mongoimport* e *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Trovare le informazioni della stringa di connessione (host, porta, nome utente e password)

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com) fare clic sulla voce **NoSQL (DocumentDB)**.
2. Nel riquadro **Sottoscrizioni** selezionare il nome dell'account.
3. Nel pannello **Stringa di connessione** fare clic su **Stringa di connessione**.  
Il riquadro destro contiene tutte le informazioni necessarie per connettersi correttamente all'account.

    ![Pannello "Stringa di connessione"](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongoimport"></a>Importare dati in DocumentDB con supporto del protocollo per MongoDB con mongoimport

Per importare dati nell'account DocumentDB, usare il modello seguente per eseguire l'importazione. Per *host*, *username* e *password* inserire i valori specifici dell'account.  

Template:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Esempio:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongorestore"></a>Importare dati in DocumentDB con supporto del protocollo per MongoDB con mongorestore

Per ripristinare dati nell'account DocumentDB, usare il modello seguente per eseguire l'importazione. Per *host*, *username* e *password* inserire i valori specifici dell'account.

Template:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Esempio:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni, vedere [Esempi di supporto del protocollo DocumentDB per MongoDB](documentdb-mongodb-samples.md).



<!--HONumber=Dec16_HO3-->


