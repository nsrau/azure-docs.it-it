<properties 
	pageTitle="Creare un insieme di database DocumentDB | Microsoft Azure" 
	description="Informazioni su come creare raccolte tramite il portale del servizio in linea per Azure DocumentDB, un database di documenti NoSQL gestito per JSON. Ottenere una versione di valutazione." 
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
	ms.date="07/08/2015" 
	ms.author="mimig"/>

# Creare una raccolta DocumentDB usando il portale di anteprima di Azure

PPer utilizzare Microsoft Azure DocumentDB, è necessario disporre di un[DocumentDB account](documentdb-create-account.md)[database](documentdb-create-database.md)una raccolta e documenti. In questo argomento viene descritto come creare una raccolta DocumentDB nel portale di anteprima di Microsoft Azure.

Le raccolte non possono essere create utilizzando il portale di anteprima, ma è anche possibile crearle utilizzando [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Per un esempio di codice c# che illustra come creare un insieme utilizzando DocumentDB .NET SDK, vedere il[Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs)file nel progetto CollectionManagement, disponibile in[azure-documentdb-net](https://github.com/Azure/azure-documentdb-net)repository in[GitHub.com](https://github.com).

1.  Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Sfoglia**.

2.  Nel pannello **Sfoglia** fare clic su **Account DocumentDB**.

3.  Nel pannello **Account DocumentDB** selezionare l'account contenente il database in cui aggiungere una raccolta. Se non è elencato alcun account, sarà necessario [creare un account DocumentDB](documentdb-create-account.md).
    
    ![Cattura di schermata in cui sono evidenziati il pulsante Sfoglia, Account DocumentDB nel pannello Sfoglia e un account DocumentDB nel pannello Account DocumentDB](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

4. Nel pannello **Database** fare clic su **Aggiungi raccolta**.

5. Nel blade**Aggiungi raccolta**, immettere l'ID per il nuovo insieme. Quando il nome viene convalidato, appare un segno di spunta verde nella casella ID.

6. Selezionare un piano tariffario per la nuova raccolta. Ogni raccolta creata è un'entità fatturabile. Per altre informazioni sui livelli di prestazioni disponibili, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).

7. Selezionare uno dei seguenti**criteri di indicizzazione**.

	- **Default** Questo criterio è migliore quando si eseguono query di uguaglianza su stringhe e utilizzando ORDER BY, l'intervallo e le query di uguaglianza per i numeri. Questo criterio non ha un sovraccarico dell’indice di 'archiviazione inferiore a **intervallo**.
	- **Hash**. Questo criterio è migliore quando si eseguono le query di uguaglianza per i numeri e stringhe. Questo criterio ha l'overhead di archiviazione di indice più basso.
	- **Range**. Questo criterio è consigliabile quando si sta utilizzando query ORDER BY, intervallo e uguaglianza su stringhe e numeri. Questo criterio non ha un sovraccarico dell’indice di 'archiviazione superiore a quello**predefinito**o**Hash**.

	Per ulteriori informazioni sulle regole di indicizzazione, vedere[DocumentDB regole di indicizzazione](documentdb-indexing-policies.md).

8. Fare su **OK** nella parte inferiore della schermata per creare la nuova raccolta.

	![Cattura di schermata in cui sono evidenziati il pulsante Aggiungi raccolta nel pannello Database, la casella ID nel pannello Aggiungi raccolta e il pulsante OK](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

9. La nuova raccolta ora appare nella sezione **Raccolte** del pannello **Database**.
 
	![Cattura di schermata del nuovo database nel pannello Account DocumentDB](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## Passaggi successivi

A questo punto, dopo aver creato una raccolta, il passaggio successivo consiste nell'aggiungere i documenti o nell'importarli nella raccolta. Per l'aggiunta di documenti a una raccolta, sono disponibili alcune opzioni:

- È possibile [aggiungere i documenti](../documentdb-view-json-document-explorer.md) usando il riquadro Esplora documenti nel portale di anteprima.
- È possibile [importare i documenti e i dati](documentdb-import-data.md) usando lo strumento di migrazione dati di DocumentDB, che consente di importare i file JSON e CSV, oltre ai dati da SQL Server, MongoDB, Archiviazione tabelle di Azure e altre raccolte DocumentDB. 
- Oppure è possibile aggiungere i documenti usando gli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. Il file [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) nel progetto DocumentManagement, disponibile nel[azure-documentdb-net](https://github.com/Azure/azure-documentdb-net)repository in[GitHub.com](https://github.com)illustra le operazioni CRUD su documenti tramite il SDK di .NET DocumentDB.

Quando si hanno documenti in una raccolta, è possibile utilizzare [DocumentDB SQL](documentdb-sql-query.md) e [eseguire query](documentdb-sql-query.md#executing-queries) rispetto ai documenti utilizzando il [Explorer Query](documentdb-query-collections-query-explorer.md) nel portale di anteprima, [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno del [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=July15_HO4-->