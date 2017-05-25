---
title: Passaggio da DocumentDB ad Azure Cosmos DB | Microsoft Docs
description: Altre informazioni sull&quot;annuncio presentato in occasione di //build 2017, relativo al passaggio dei clienti di DocumentDB ad Azure Cosmos DB.
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: n/a
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f60c3502ed51424647088fa8109f13cabc4e73da
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017

---

# <a name="welcome-documentdb-customers-to-azure-cosmos-db"></a>Passaggio da DocumentDB ad Azure Cosmos DB

I clienti di DocumentDB ora fanno parte della famiglia di Azure Cosmos DB. 

Azure Cosmos DB, [annunciato in occasione della conferenza Microsoft Build 2017](https://azure.microsoft.com/blog/azure-cosmos-db-microsofts-globally-distributed-multi-model-database-service/), è il primo servizio di database distribuito a livello globale che consente di ridimensionare in modo elastico la velocità effettiva e lo spazio di archiviazione in un numero illimitato di aree geografiche garantendo al contempo bassa latenza, disponibilità elevata e coerenza, oltre a offrire i contratti di servizio più completi del settore e supportare diversi modelli di dati e API. 

![Azure Cosmos DB è il servizio di database Microsoft distribuito a livello globale con scalabilità orizzontale elastica, bassa latenza garantita, cinque modelli di coerenza e contratti di servizio completi garantiti.](./media/welcome-documentdb-customers/azure-cosmos-db.png)

Una delle API supportata da Azure Cosmos DB è l'API di DocumentDB, oltre al modello di dati di documento. Si ha già familiarità con le API di DocumentDB. Si tratta delle API già usate per eseguire le applicazioni di DocumentDB correnti. Queste API _non_ subiranno modifiche: il pacchetto NuGet, gli spazi dei nomi e tutte le dipendenze rimangono invariate. **Non è necessaria alcuna modifica** per continuare a eseguire le app compilate con l'API di DocumentDB. Azure Cosmos DB è solo il nome del servizio di cui ora fanno parte. 

Di seguito vengono date le risposte ad alcune possibili domande. 

## <a name="why-move-to-azure-cosmos-db"></a>Perché passare ad Azure Cosmos DB? 

Azure Cosmos DB è il prossimo passo decisivo nell'ambito dei database cloud distribuiti a livello globale su larga scala. I clienti di DocumentDB ora hanno accesso al nuovo sistema e alle nuove funzionalità all'avanguardia offerti da Azure Cosmos DB.

Azure Cosmos DB è stato introdotto come "progetto Florence" nel 2010 per far fronte ai problemi critici di sviluppo riscontrati dalle applicazioni su larga scala all'interno di Microsoft. Avendo osservato che i problemi relativi alla compilazione di app distribuite a livello globale non erano stati riscontrati solo da Microsoft, nel 2015 è stata resa disponibile per gli sviluppatori di Azure la prima generazione di questa tecnologia, ovvero Azure DocumentDB. 

Da quel momento sono state aggiunte nuove importanti funzionalità.  Il risultato è Azure Cosmos DB.  Nell'ambito di questo rilascio di Azure Cosmos DB, i clienti di DocumentDB, con i relativi dati, diventano automaticamente clienti di Azure Cosmos DB. La transizione è semplice e si ha accesso al nuovo sistema e alle nuove funzionalità all'avanguardia offerti da Azure Cosmos DB. Queste funzionalità sono relative al motore di database principale, ma anche a distribuzione globale, scalabilità elastica e contratti di servizio completi, leader del settore. In particolare, il motore di database di Azure Cosmos DB è stato sviluppato per poter eseguire il mapping efficiente di tutti i più comuni modelli di dati, sistemi di tipi e API al modello di dati sottostante di Azure Cosmos DB. 

Il risultato di questo miglioramento più evidente per gli sviluppatori è attualmente il nuovo supporto per le [API di Gremlin](graph-introduction.md) e di [archiviazione tabelle](table-introduction.md). E questo è solo l'inizio. Nel corso del tempo verranno aggiunte altre API popolari e modelli di dati più recenti con ulteriori miglioramenti in termini di prestazioni e di archiviazione su scala globale. 

È importante sottolineare che il [dialetto SQL](../documentdb/documentdb-sql-query.md) di DocumentDB è sempre stato solo una delle tante API che possono essere supportate da Cosmos DB sottostante. Per gli sviluppatori che usano un servizio completamente gestito come Azure Cosmos DB, la sola interfaccia per il servizio sono le API esposte dal servizio. In tal senso non cambia nulla per i clienti esistenti di DocumentDB. Azure Cosmos DB offre esattamente la stessa API SQL di DocumentDB. Ora (e in futuro) è però possibile accedere ad altre funzionalità che prima non erano accessibili. 

Un altro risultato evidente dei miglioramenti apportati è l'ampia base per la scalabilità globale ed elastica della velocità effettiva e dello spazio di archiviazione, come dimostrano soprattutto le [UR/m](request-units-per-minute.md), ma anche altre funzionalità che verranno annunciate in questi ambiti. Queste nuove funzionalità consentono di ridurre i costi per i clienti per svariati carichi di lavoro. Sono stati apportati diversi miglioramenti al sottosistema di distribuzione globale. Uno dei molti risultati di questo lavoro evidenti per gli sviluppatori è il modello di coerenza del prefisso, che porta a cinque il numero di modelli di coerenza ben definiti. Esistono poi diverse funzionalità ancora più interessanti che verranno rilasciate non appena saranno definitive. 

## <a name="what-do-i-need-to-do-to-ensure-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>Che cosa è necessario fare per assicurarsi che le risorse di DocumentDB continuino a essere eseguite in Azure Cosmos DB?

Niente. Non è necessario apportare alcuna modifica. Le risorse di DocumentDB ora sono risorse di Azure Cosmos DB e non si sono verificate interruzioni del servizio al momento del passaggio.

## <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Quali modifiche è necessario apportare all'app perché funzioni con Azure Cosmos DB?

Non è necessario apportare alcuna modifica. Classi, spazi dei nomi e nomi di pacchetti NuGet non sono stati modificati. Come sempre, è consigliabile mantenere aggiornati gli SDK per poter sfruttare i vantaggi delle funzionalità e dei miglioramenti più recenti. 

## <a name="whats-changed-in-the-azure-portal"></a>Che cosa è cambiato nel portale di Azure?

Azure DocumentDB non è più visualizzato nel portale come servizio di Azure. Viene invece visualizzato Azure Cosmos DB con una nuova icona, come illustrato nell'immagine seguente. Tutte le raccolte sono disponibili esattamente come prima ed è sempre possibile ridimensionare la velocità effettiva, modificare la coerenza e monitorare i contratti di servizio. Sono state migliorate le funzionalità di **Esplora dati (anteprima)**. Ora è possibile visualizzare e modificare i documenti, creare ed eseguire query e usare stored procedure, trigger e funzioni definite dall'utente da una sola pagina, come illustrato nell'immagine seguente. 

![Visualizzazione e copia di una chiave di accesso nel portale di Azure, pannello Chiavi](./media/welcome-documentdb-customers/cosmos-db-data-explorer.png)

## <a name="are-there-changes-to-pricing"></a>I prezzi sono stati modificati?

No, il costo per eseguire l'app in Azure Cosmos DB è lo stesso di prima. È tuttavia possibile trarre vantaggio dalla nuova **funzionalità di unità richiesta al minuto** annunciata. Per altre informazioni, vedere l'articolo [Unità richiesta al minuto in Azure Cosmos DB](request-units-per-minute.md).

## <a name="are-there-changes-to-the-service-level-agreements-slas"></a>Sono state apportate modifiche ai contratti di servizio?

No, i contratti di servizio per disponibilità, coerenza, latenza e velocità effettiva non sono stati modificati e vengono ancora visualizzati nel portale. Per informazioni dettagliate sui contratti di servizio, vedere [Contratto di Servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![App elenco attività con dati di esempio](./media/welcome-documentdb-customers/azure-cosmosdb-portal-metrics-slas.png)

## <a name="whats-next-with-azure-cosmos-db"></a>Quali sono le prospettive di Azure Cosmos DB?

Azure Cosmos DB è un servizio di database in costante evoluzione. Tutte le nuove funzionalità vengono convalidate in applicazioni su larga scala in Microsoft, quindi esposte ai principali clienti esterni e infine rilasciate pubblicamente. 

Le API di Gremlin e di tabella sono solo le prime tra le API e i modelli di dati popolari che saranno supportati da Azure Cosmos DB e presto sarà annunciata la disponibilità di nuovi modelli di dati. Si continuano anche a compiere grandi progressi nell'ambito delle prestazioni e dello spazio di archiviazione su scala globale. 

Ancora una volta, grazie per essere stati clienti di Azure DocumentDB, ora parte della famiglia di sviluppatori e clienti di tutto il mondo di Azure Cosmos DB. L'obiettivo è quello di realizzare il servizio di database più affidabile del mondo per consentire di compilare più facilmente app globali incredibilmente avanzate. Provare le nuove funzionalità di Azure Cosmos DB e inviare commenti e suggerimenti,  illustrando le app compilate con Cosmos DB.

- Il team di Azure Cosmos DB [@AzureCosmosDB](https://twitter.com/AzureCosmosDB)

