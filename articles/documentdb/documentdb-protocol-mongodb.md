---
title: Informazioni sul supporto del protocollo DocumentDB per MongoDB | Microsoft Docs
description: Informazioni sul supporto del protocollo DocumentDB per MongoDB Consente di usare Azure DocumentDB, un servizio basato sul cloud gestito, come archivio dati per le app scritte per MongoDB.
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
ms.date: 11/23/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 0703fa4f4d3eb9f23aa9b3c0b90267fac9557f13
ms.openlocfilehash: 876fff6a63ccf1a3c10a6852927c60b454436954


---
# <a name="what-is-documentdb-protocol-support-for-mongodb"></a>Informazioni sul supporto del protocollo DocumentDB per MongoDB

I database DocumentDB ora possono essere usati come archivio dati per le app scritte per MongoDB. Di conseguenza, usando i [driver](https://docs.mongodb.org/ecosystem/drivers/) esistenti per i database MongoDB, l'applicazione scritta per MongoDB ora può comunicare con DocumentDB e usare i database DocumentDB invece dei database MongoDB. In molti casi, per passare da MongoDB a DocumentDB e viceversa, è sufficiente modificare una stringa di connessione. Usando questa funzionalità, i clienti possono sviluppare ed eseguire con facilità applicazioni di database MongoDB nel cloud di Azure, sfruttando i vantaggi dei database NoSQL scalabili e completamente gestiti di DocumentDB, continuando al tempo stesso a usare competenze e strumenti familiari per MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-protocol-support-for-mongodb"></a>Qual è il vantaggio di usare il supporto del protocollo DocumentDB per MongoDB?
**Nessuna gestione server**: DocumentDB è un servizio completamente gestito e quindi non è necessario gestire manualmente alcuna infrastruttura o macchina virtuale. DocumentDB è disponibile in più di 20 [aree di Azure](https://azure.microsoft.com/regions/services/).

**Scalabilità illimitata**: è possibile ridimensionare la velocità effettiva e lo spazio di archiviazione in modo indipendente ed elastico. È possibile aggiungere la capacità necessaria per gestire milioni di richieste al secondo con facilità.

**Classe enterprise**: DocumentDB supporta più repliche locali per garantire il 99,99% della disponibilità e la protezione dati nonostante gli errori locali e a livello di area. DocumentDB ha [certificazioni di conformità](https://www.microsoft.com/trustcenter) e funzionalità di sicurezza di classe enterprise. 

**Compatibilità con MongoDB**: il supporto del protocollo DocumentDB per MongoDB è progettato per garantire la compatibilità con MongoDB. È possibile usare il codice, le applicazioni, i driver e gli strumenti esistenti per lavorare con DocumentDB. 

## <a name="how-to-get-started"></a>Come iniziare?
Creare un account DocumentDB con il supporto del protocollo per MongoDB nel [portale di Azure](https://portal.azure.com) ed effettuare lo swapping della connessione al nuovo account. 

*Non sono necessarie altre operazioni.*

Per istruzioni più dettagliate, vedere [Creare un account](documentdb-create-mongodb-account.md) e [Connessione all'account](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Passaggi successivi
* Per creare un account DocumentDB, seguire l'esercitazione [Creare un account DocumentDB con supporto del protocollo per MongoDB](documentdb-create-mongodb-account.md).
* Per le istruzioni per ottenere le informazioni della stringa di connessione dell'account, seguire l'esercitazione [Connettersi a un account DocumentDB con il supporto del protocollo per MongoDB](documentdb-connect-mongodb-account.md).
* Per informazioni su come creare una connessione tra il database DocumentDB e l'app MongoDB in MongoChef, seguire l'esercitazione [Usare MongoChef con un account DocumentDB con supporto del protocollo per MongoDB](documentdb-mongodb-mongochef.md).
* Esplorare DocumentDB con supporto del protocollo per trovare [esempi](documentdb-mongodb-samples.md)di MongoDB.




<!--HONumber=Nov16_HO4-->


