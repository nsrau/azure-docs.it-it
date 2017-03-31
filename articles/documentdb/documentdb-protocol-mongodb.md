---
title: 'Informazioni sull&quot;API DocumentDB: per MongoDB | Microsoft Docs'
description: "Informazioni sull&quot;API DocumentDB: per MongoDB e sul modo in cui è possibile eseguire facilmente applicazioni MongoDB esistenti nel cloud di Azure"
keywords: "che cos&quot;è MongoDB"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f173fa709f2a7a21042752ba4b5ac936d01fe300
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>Informazioni sull'API DocumentDB: per MongoDB

I database DocumentDB ora possono essere usati come archivio dati per le app scritte per MongoDB. Di conseguenza, usando i [driver](https://docs.mongodb.org/ecosystem/drivers/) esistenti per i database MongoDB, l'applicazione scritta per MongoDB ora può comunicare con DocumentDB e usare i database DocumentDB invece dei database MongoDB. In molti casi, per passare da MongoDB a DocumentDB e viceversa, è sufficiente modificare una stringa di connessione. Usando questa funzionalità, i clienti possono sviluppare ed eseguire con facilità applicazioni di database MongoDB nel cloud di Azure, sfruttando i vantaggi dei database NoSQL scalabili e completamente gestiti di DocumentDB, continuando al tempo stesso a usare competenze e strumenti familiari per MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>Quali sono i vantaggi offerti dall'uso dell'API DocumentDB: per MongoDB?
**Nessuna gestione server**: DocumentDB è un servizio completamente gestito e quindi non è necessario gestire manualmente alcuna infrastruttura o macchina virtuale. DocumentDB è disponibile in più di 30 [aree di Azure](https://azure.microsoft.com/regions/services/).

**Scalabilità illimitata**: è possibile ridimensionare la velocità effettiva e lo spazio di archiviazione in modo indipendente ed elastico. È possibile aggiungere la capacità necessaria per gestire milioni di richieste al secondo con facilità.

**Classe enterprise**: DocumentDB supporta più repliche locali per garantire il 99,99% della disponibilità e la protezione dati nonostante gli errori locali e a livello di area. DocumentDB ha [certificazioni di conformità](https://www.microsoft.com/trustcenter) e funzionalità di sicurezza di classe enterprise. 

**Compatibilità con MongoDB** - DocumentDB: l'API per MongoDB è stata progettata per assicurare la compatibilità con MongoDB. È possibile usare il codice, le applicazioni, i driver e gli strumenti esistenti per lavorare con DocumentDB. 

Per altre informazioni, guardare questo video di Azure Friday con Scott Hanselman e Kirill Gavrylyuk, responsabile principale della progettazione per DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>Come iniziare?
Creare un account dell'API DocumentDB: per MongoDB nel [portale di Azure](https://portal.azure.com) e usare il nuovo account per la connessione. 

*Non sono necessarie altre operazioni.*

Per istruzioni più dettagliate, vedere [Creare un account](documentdb-create-mongodb-account.md) e [Connessione all'account](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su DocumentDB: l'API per MongoDB è integrata nella documentazione complessiva su DocumentDB. Di seguito sono disponibili alcune informazioni utili per iniziare:
* Per istruzioni su come ottenere le informazioni della stringa di connessione dell'account, seguire l'esercitazione [Connettersi a un account MongoDB](documentdb-connect-mongodb-account.md).
* Per informazioni su come creare una connessione tra il database DocumentDB e l'app MongoDB in MongoChef, seguire l'esercitazione [Usare MongoChef con DocumentDB](documentdb-mongodb-mongochef.md).
* Seguire l'esercitazione [Eseguire la migrazione di dati in DocumentDB con il supporto del protocollo per MongoDB](documentdb-mongodb-migrate.md) per importare i dati in un database dell'API per MongoDB.
* Creare la prima app dell'API per MongoDB usando [Node.js](documentdb-mongodb-samples.md).
* Creare la prima app Web dell'API per MongoDB usando [.NET](documentdb-mongodb-application.md).
* Connettersi a un account dell'API per MongoDB usando [Robomongo](documentdb-mongodb-robomongo.md).
* Informazioni sul numero di unità richiesta usate dalle operazioni con il [comando GetLastRequestStatistics e le metriche del portale di Azure](documentdb-request-units.md#GetLastRequestStatistics).
* Informazioni su come [configurare le preferenze di lettura per le app distribuite globalmente](documentdb-distribute-data-globally.md#ReadPreferencesAPIforMongoDB).


