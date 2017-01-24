---
title: Creare una raccolta e un database DocumentDB | Documentazione Microsoft
description: Informazioni su come creare database NoSQL e raccolte di documenti JSON tramite il portale del servizio online per Azure DocumentDB, un database di documenti basato su cloud. Ottenere una versione di valutazione.
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b81ad2f6-df7f-4c6d-8ca9-f8a9982d647e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 8f3e6b271f3ca5973a00f2a5a9e11dafeb9637aa
ms.openlocfilehash: 45371ca337f291f37e1ac5c388170b7079dfad52


---
# <a name="how-to-create-a-documentdb-collection-and-database-using-the-azure-portal"></a>Come creare una raccolta e un database DocumentDB usando il portale di Azure
Per utilizzare Microsoft Azure DocumentDB, è necessario disporre di un [account DocumentDB](documentdb-create-account.md), un database, una raccolta e documenti. Questo articolo descrive come creare una raccolta DocumentDB nel portale di Azure.

Per informazioni sulle raccolte, vedere [Creare una raccolta DocumentDB](#what-is-a-documentdb-collection)

1. Nell'indice del [portale di Azure](https://portal.azure.com/) fare clic su **DocumentDB (NoSQL)** e quindi selezionare l'account in cui aggiungere una raccolta nel pannello **DocumentDB (NoSQL)**. Se non è elencato alcun account, sarà necessario [creare un account DocumentDB](documentdb-create-account.md).

   ![Schermata che evidenzia gli account di DocumentDB nell'indice, l'account nel pannello Account DocumentDB e il database nel pannello dell’account DocumentDB, nella sezione Database](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

   Se **DocumentDB (NoSQL)** non è visibile nell'indice, fare clic su **More Services** (Altri servizi) e quindi su **DocumentDB (NoSQL)**. Se non è elencato alcun account, sarà necessario [creare un account DocumentDB](documentdb-create-account.md).
2. Nel pannello **Account DocumentDB** dell'account selezionato fare clic su **Aggiungi raccolta**.

    ![Schermata che evidenzia gli account di DocumentDB nell'indice, l'account nel pannello Account DocumentDB e il database nel pannello dell’account DocumentDB, nella sezione Database](./media/documentdb-create-collection/docdb-database-creation-3.png)
3. Nel pannello **Aggiungi raccolta** immettere l'ID della nuova raccolta nella casella **ID**. I nomi delle raccolte devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali. Quando il nome viene convalidato, appare un segno di spunta verde nella casella ID.

    ![Cattura di schermata in cui sono evidenziati il pulsante Aggiungi raccolta nel pannello Database, le impostazioni nel pannello Aggiungi raccolta e il pulsante OK. Portale di Azure per DocumentDB. Creatore di database basati su cloud per database JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)
4. Per impostazione predefinita, l'opzione **Piano tariffario** è impostata su **Standard**, per consentire di personalizzare la velocità effettiva e l'archiviazione per la raccolta. Per altre informazioni sul piano tariffario, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).  
5. Selezionare una **modalità di partizionamento** per la raccolta, tra **Partizione singola** e **Partizionata**.

    Una **partizione singola** ha una capacità di archiviazione riservata di 10 GB e può avere livelli di velocità effettiva tra 400 e 10.000 unità richiesta al secondo (UR/sec). Una unità richiesta corrisponde alla velocità effettiva dell'operazione di lettura di un documento da 1 KB. Per altre informazioni sulle unità richiesta, vedere l'articolo [Unità richiesta in DocumentDB](documentdb-request-units.md).

    Una **raccolta partizionata** può essere ridimensionata per gestire uno spazio di archiviazione illimitato in più partizioni e può avere livelli di velocità effettiva a partire da 10.100 UR/sec. Per altre informazioni sulle raccolte partizionate, vedere [Raccolte a partizione singola e raccolte partizionate](documentdb-partition-data.md#single-partition-and-partitioned-collections).

    Per impostazione predefinita, la velocità effettiva di una nuova raccolta partizionata singola è impostata su 1000 UR/s con una capacità di archiviazione di 10 GB. Per una raccolta partizionata, la velocità effettiva è impostata su 10100 UR/s senza limite massimo di capacità o velocità effettiva.
6. Se si sta creando una raccolta partizionata, selezionare la **chiave di partizione** per la raccolta. Quando si crea una raccolta ad alte prestazioni è importante selezionare la chiave di partizione corretta. Per altre informazioni sulla selezione di una chiave di partizione, vedere [Progettazione per il partizionamento](documentdb-partition-data.md#designing-for-partitioning).
7. Nel pannello **Database** creare un nuovo database o usarne uno esistente. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali. Per convalidare il nome, fare clic all'esterno della casella di testo. Quando il nome è convalidato, viene visualizzato un segno di spunta verde nella casella.
8. Fare su **OK** nella parte inferiore della schermata per creare la nuova raccolta.
9. La nuova raccolta ora viene visualizzata nella sezione **Raccolte** del pannello **Panoramica**.

    ![Cattura di schermata della nuova raccolta nel pannello Database. Portale di Azure per DocumentDB. Creatore di database basati su cloud per database JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-9.png)
10. **Facoltativo:** per modificare la velocità effettiva della raccolta nel portale fare clic su **Proporzioni** nel menu Risorsa.

    ![Screenshot del menu Risorsa con Proporzioni selezionato](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

## <a name="what-is-a-documentdb-collection"></a>Informazioni sulle raccolte di DocumentDB.
Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. Una raccolta è un'entità fatturabile in cui il [costo](documentdb-performance-levels.md) è determinato dalla velocità effettiva con provisioning della raccolta. Le raccolte possono estendersi su più partizioni o server e possono essere ridimensionate per gestire volumi praticamente illimitati di archiviazione o di velocità effettiva.

Le raccolte vengono partizionate automaticamente in uno o più server fisici da DocumentDB. Quando si crea una raccolta, è possibile specificare la velocità effettiva con provisioning in termini di unità richiesta al secondo e una proprietà chiave di partizione. Il valore di questa proprietà viene usato da DocumentDB per distribuire i documenti tra le partizioni e indirizzare le richieste come query. Il valore della chiave di partizione funge anche da limite della transazione per le stored procedure e i trigger. Ogni raccolta ha una quantità di velocità effettiva riservata che non viene condivisa con altre raccolte nello stesso account. È quindi possibile aumentare il numero di istanze dell'applicazione sia in termini di archiviazione che di velocità effettiva.

Le raccolte sono diverse dalle tabelle nei database relazionali. Tramite le raccolte non viene applicato alcuno schema, in quanto DocumentDB è un database privo di schemi. Pertanto è possibile archiviare diversi tipi di documenti con schemi diversi nella stessa raccolta. È possibile scegliere di usare le raccolte per archiviare oggetti di un singolo tipo come si farebbe con le tabelle. Il modello migliore dipende solo dal modo in cui i dati vengono visualizzati insieme in query e transazioni.

## <a name="other-ways-to-create-a-documentdb-collection"></a>Altri modi per creare una raccolta di DocumentDB
Per creare le raccolte non è necessario usare il portale. Possono essere create anche usando gli [SDK di DocumentDB](documentdb-sdk-dotnet.md) e l'API REST.

* Per un esempio di codice C#, vedere gli [esempi di raccolta per C#](documentdb-dotnet-samples.md#collection-examples).
* Per un esempio di codice Node.js, vedere gli [esempi di raccolta per Node.js](documentdb-nodejs-samples.md#collection-examples).
* Per un esempio di codice Python, vedere gli [esempi di raccolta per Python](documentdb-python-samples.md#collection-examples).
* Per un esempio di API REST, vedere l'articolo [Creare una raccolta](https://msdn.microsoft.com/library/azure/mt489078.aspx).

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se **Aggiungi raccolta** è disabilitato nel portale di Azure, significa che l'account è attualmente disabilitato. Questo in genere si verifica quando sono stati usati tutti i crediti dei vantaggi disponibili per il mese in corso.    

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver creato una raccolta, il passaggio successivo consiste nell'aggiungere i documenti o nell'importarli nella raccolta. Per l'aggiunta di documenti a una raccolta, sono disponibili alcune opzioni:

* È possibile [aggiungere i documenti](documentdb-view-json-document-explorer.md) usando il riquadro Esplora documenti nel portale.
* È possibile [importare i documenti e i dati](documentdb-import-data.md) usando lo strumento di migrazione dati di DocumentDB, che consente di importare i file JSON e CSV, oltre ai dati da SQL Server, MongoDB, Archiviazione tabelle di Azure e altre raccolte DocumentDB.
* Oppure è possibile aggiungere i documenti usando gli [SDK di DocumentDB](documentdb-sdk-dotnet.md). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. Per esempi di codice C# che illustrano l'uso dei documenti con DocumentDB .NET SDK, vedere [Esempi di documento per C#](documentdb-dotnet-samples.md#document-examples). Per esempi di codice Node.js che illustrano l'uso dei documenti con DocumentDB Node.js SDK, vedere [Esempi di documento per Node.js](documentdb-nodejs-samples.md#document-examples).

Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#executing-sql-queries) nei documenti con [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](documentdb-sdk-dotnet.md). 



<!--HONumber=Dec16_HO3-->


