<properties 
	pageTitle="Come creare un database in DocumentDB | Microsoft Azure" 
	description="Informazioni su come creare un database NoSQL estremamente veloce su scala mondiale mediante il portale dei servizi online per Azure DocumentDB." 
	keywords="come creare un database" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="mimig"/>

# Come creare un database per DocumentDB usando il portale di Azure

Per utilizzare Microsoft Azure DocumentDB, è necessario disporre di un [account DocumentDB](documentdb-create-account.md), un database, una raccolta e documenti. Questo argomento descrive come creare un database per DocumentDB nel portale di Microsoft Azure. Per informazioni su come creare un database usando uno degli SDK, vedere [Altri modi per creare un database di DocumentDB](#other-ways-to-create-a-documentdb-database).

1.  Nel [portale di Azure](https://portal.azure.com/), nell’indice, fare clic su **Account DocumentDB**. Se **Account DocumentDB** non è visibile, fare clic su **More Services** (Altri servizi) e quindi su **Account DocumentDB**.


    ![Screenshot che illustra come creare un database. Sono evidenziati Account DocumentDB nel pannello Sfoglia e un account DocumentDB nel pannello Account DocumentDB.](./media/documentdb-create-database/docdb-database-creation-1-2.png)

2.  Nel pannello **Account DocumentDB** selezionare l'account in cui aggiungere un database NoSQL DocumentDB. Se non è elencato alcun account, sarà necessario [creare un account DocumentDB](documentdb-create-account.md).

3. Nel pannello dell'**account DocumentDB** fare clic sul comando **Aggiungi database**.

    ![Screenshot che illustra come creare un database. Sono evidenziati il pulsante Aggiungi database, la casella ID e il pulsante OK.](./media/documentdb-create-database/docdb-database-creation-3-5.png)

4. Nel pannello **Aggiungi database** immettere l'ID del nuovo database. Quando il nome viene convalidato, appare un segno di spunta verde nella casella **ID**.

5. Fare su **OK** nella parte inferiore della schermata per creare il nuovo database.

6. Il nuovo database è ora visualizzato nel filtro **Database** nel pannello **Account DocumentDB**.
 
	![Cattura di schermata del nuovo database nel pannello Account DocumentDB](./media/documentdb-create-database/docdb-database-creation-6.png)

## Altri modi per creare un database di DocumentDB

Non è necessario creare i database dal portale. È possibile crearli anche usando gli [SDK di DocumentDB](documentdb-sdk-dotnet.md) o l'[API REST](https://msdn.microsoft.com/library/mt489072.aspx). Per informazioni sull'uso dei database con .NET SDK, vedere gli [esempi di database .NET](documentdb-dotnet-samples.md#database-examples). Per informazioni sull'uso dei database con Node.js SDK, vedere gli [esempi di database Node.js](documentdb-nodejs-samples.md#database-examples).

## Passaggi successivi

Dopo aver appreso come creare un database per DocumentDB, il passaggio successivo consiste nella [creazione di una raccolta](documentdb-create-collection.md).

Una volta creata la raccolta, è possibile[aggiungere documenti JSON](documentdb-view-json-document-explorer.md)utilizzando Esplora Documenti nel portale, [importare documenti](documentdb-import-data.md)nella raccolta utilizzando lo strumento di migrazione dei dati DocumentDB o utilizzare uno degli [SDK di DocumentDB](documentdb-sdk-dotnet.md)per eseguire le operazioni CRUD. DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. Per esempi di codice .NET che illustrano come creare, rimuovere, aggiornare ed eliminare documenti, vedere gli [esempi di documento .NET](documentdb-dotnet-samples.md#document-examples). Per informazioni sull'uso dei documenti con Node.js SDK, vedere gli [esempi di documento Node.js](documentdb-nodejs-samples.md#document-examples).

Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#executing-sql-queries) nei documenti tramite [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](documentdb-sdk-dotnet.md).

<!---HONumber=AcomDC_0817_2016-->