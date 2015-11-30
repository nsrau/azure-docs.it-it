<properties 
	pageTitle="Come creare un database in DocumentDB | Microsoft Azure" 
	description="Informazioni su come creare un database mediante il portale dei servizi online per Azure DocumentDB, un database di documenti NoSQL per JSON. Ottenere una versione di valutazione." 
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
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# Come creare un database per DocumentDB

Per utilizzare Microsoft Azure DocumentDB, è necessario disporre di un [account DocumentDB](documentdb-create-account.md), un database, una raccolta e documenti. In questo argomento viene descritto come creare un database per DocumentDB nel portale di anteprima di Microsoft Azure.

![Cattura di schermata in cui viene mostrato come creare un database e sono evidenziati il pulsante Sfoglia, Account DocumentDB nel pannello Sfoglia e un account DocumentDB nel pannello Account DocumentDB](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  Nel [portale di anteprima di Azure](https://portal.azure.com/), nell’indice fare clic su **Account DocumentDB**. 

2.  Nel pannello **Account DocumentDB** selezionare l'account in cui aggiungere un database NoSQL DocumentDB. Se non è elencato alcun account, sarà necessario [creare un account DocumentDB](documentdb-create-account.md).

3. Nel pannello dell'**account DocumentDB** fare clic sul comando **Aggiungi database**.

4. Nel pannello **Aggiungi database** immettere l'ID del nuovo database. Quando il nome viene convalidato, appare un segno di spunta verde nella casella **ID**.

5. Fare su **OK** nella parte inferiore della schermata per creare il nuovo database.

7. Il nuovo database è ora visualizzato nel filtro **Database** nel pannello **Account DocumentDB**.
 
	![Cattura di schermata del nuovo database nel pannello Account DocumentDB](./media/documentdb-create-database/docdb-database-creation-7.png)

## Altri modi per creare un database di DocumentDB

I database non devono essere necessariamente creati dal portale di anteprima. È possibile crearli anche usando gli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Per un esempio di codice C# che illustra come creare un database utilizzando il SDK di .NET DocumentDB, vedere il[Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs)file nel progetto DatabaseManagement, disponibile nel[azure-documentdb-net](https://github.com/Azure/azure-documentdb-net)repository in[GitHub.com](https://github.com).

## Passaggi successivi

Dopo aver appreso come creare un database per DocumentDB, il passaggio successivo consiste nella [creazione di una raccolta](documentdb-create-collection.md).

Una volta creata la raccolta, è possibile[aggiungere documenti JSON](../documentdb-view-json-document-explorer.md)utilizzando Microsoft Document Explorer nel portale di anteprima[importare documenti](documentdb-import-data.md)nella raccolta utilizzando lo strumento di migrazione dei dati DocumentDB o utilizzare uno del[DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)per eseguire le operazioni CRUD. DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. Per esempi di codice .NET che illustra come creare, rimuovere, aggiornare ed eliminare i documenti, vedere[Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs)nel progetto DocumentManagement nel repository di azure-documentdb-net in GitHub.com.

Quando si hanno documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#executing-queries) sui documenti usando [Esplora query](documentdb-query-collections-query-explorer.md) nel portale di anteprima, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=Nov15_HO4-->