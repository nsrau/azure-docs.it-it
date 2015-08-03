<properties 
	pageTitle="Creare un database NoSQL DocumentDB | Microsoft Azure" 
	description="Informazioni su come creare i database gestiti mediante il portale dei servizi online per Azure DocumentDB, un database di documenti NoSQL per JSON. Ottenere una versione di valutazione." 
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
	ms.topic="get-started-article" 
	ms.date="06/26/2015" 
	ms.author="mimig"/>

# Creare un database DocumentDB usando il portale di anteprima di Azure

Per utilizzare Microsoft Azure DocumentDB, è necessario disporre di un[DocumentDB account](documentdb-create-account.md)un database e una raccolta documenti. In questo argomento viene descritto come creare un database DocumentDB nel portale di anteprima di Microsoft Azure.

I database non devono essere necessariamente creati dal portale di anteprima. È possibile crearli anche usando gli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Per un esempio di codice C# che illustra come creare un database utilizzando il SDK di .NET DocumentDB, vedere il[Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs)file nel progetto DatabaseManagement, disponibile nel[azure-documentdb-net](https://github.com/Azure/azure-documentdb-net)repository in[GitHub.com](https://github.com).

1.  Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Sfoglia**.

2.  Nel pannello **Sfoglia** fare clic su **Account DocumentDB**.

3.  Nel pannello **Account DocumentDB** selezionare l'account in cui aggiungere un database DocumentDB. Se non è elencato alcun account, sarà necessario [creare un account DocumentDB](documentdb-create-account.md).
    
    ![Cattura di schermata in cui sono evidenziati il pulsante Sfoglia, Account DocumentDB nel pannello Sfoglia e un account DocumentDB nel pannello Account DocumentDB](./media/documentdb-create-database/docdb-database-creation-1-3.png)

4. Nel pannello dell'**account DocumentDB** fare clic sul comando **Aggiungi database**.

5. Nel pannello **Aggiungi database** immettere l'ID del nuovo database. Quando il nome viene convalidato, appare un segno di spunta verde nella casella ID.

6. Fare su **OK** nella parte inferiore della schermata per creare il nuovo database.

	![Cattura di schermata in cui sono evidenziati il pulsante Aggiungi database nel pannello Account DocumentDB, la casella ID nel pannello Aggiungi database e il pulsante OK](./media/documentdb-create-database/docdb-database-creation-4-6.png)

8. Il nuovo database è ora visualizzato nel filtro **Database** nel pannello **Account DocumentDB**.
 
	![Cattura di schermata del nuovo database nel pannello Account DocumentDB](./media/documentdb-create-database/docdb-database-creation-7.png)

## Passaggi successivi

Ora che è disponibile un database di DocumentDB, il passaggio successivo consiste nel [creare una raccolta](documentdb-create-collection.md).

Una volta creata la raccolta, è possibile[aggiungere documenti](../documentdb-view-json-document-explorer.md)utilizzando Microsoft Document Explorer nel portale di anteprima[importare documenti](documentdb-import-data.md)nella raccolta utilizzando lo strumento di migrazione dei dati DocumentDB o utilizzare uno del[DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)per eseguire le operazioni CRUD. DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. Per esempi di codice .NET che illustra come creare, rimuovere, aggiornare ed eliminare i documenti, vedere[Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs)nel progetto DocumentManagement nel repository di azure-documentdb-net in GitHub.com.

Quando si hanno documenti in una raccolta, è possibile utilizzare [DocumentDB SQL](documentdb-sql-query.md) e [eseguire query](documentdb-sql-query.md#executing-queries) rispetto ai documenti utilizzando il [Explorer Query](documentdb-query-collections-query-explorer.md) nel portale di anteprima, [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno del [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=July15_HO4-->