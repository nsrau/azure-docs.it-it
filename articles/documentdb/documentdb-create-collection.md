<properties 
	pageTitle="Creare un insieme di database DocumentDB | Microsoft Azure" 
	description="Informazioni su come creare raccolte di documenti JSON tramite il portale del servizio online per Azure DocumentDB, un database di documenti NoSQL basato su cloud. Ottenere una versione di valutazione." 
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
	ms.date="12/18/2015" 
	ms.author="mimig"/>

# Come creare una raccolta DocumentDB usando il portale di Azure

PPer utilizzare Microsoft Azure DocumentDB, è necessario disporre di un[DocumentDB account](documentdb-create-account.md)[database](documentdb-create-database.md)una raccolta e documenti. Questo argomento descrive come creare una raccolta DocumentDB nel portale di Azure.

Per informazioni sulle raccolte, vedere [Creare una raccolta DocumentDB](#what-is-a-documentdb-collection)

![Schermata che evidenzia gli account di DocumentDB nell'indice, l'account nel pannello Account DocumentDB e il database nel pannello dell’account DocumentDB, nella sezione Database](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  Nel [portale di Azure](https://portal.azure.com/), nell’indice, fare clic su **Account DocumentDB**. 

2.  Nel pannello **Account DocumentDB** selezionare l'account in cui aggiungere una raccolta. Se non è elencato alcun account, sarà necessario [creare un account DocumentDB](documentdb-create-account.md).

3. Nel pannello **Account DocumentDB** per l'account selezionato, scorrere verso il basso alla sezione **Database** e quindi selezionare il database in cui si desidera aggiungere una raccolta.
    
4. Nel pannello **Database** fare clic su **Aggiungi raccolta**.

	![Cattura di schermata in cui sono evidenziati il pulsante Aggiungi raccolta nel pannello Database, le impostazioni nel pannello Aggiungi raccolta e il pulsante OK. Portale di Azure per DocumentDB. Creatore di database basati su cloud per database JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. Nel pannello **Aggiungi raccolta** immettere l'ID per la nuova raccolta. I nomi delle raccolte devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali. Quando il nome viene convalidato, appare un segno di spunta verde nella casella ID.

6. Selezionare un piano tariffario per la nuova raccolta. Ogni raccolta creata è un'entità fatturabile. Per altre informazioni sui livelli di prestazioni disponibili, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).

7. Selezionare uno dei seguenti **criteri di indicizzazione**.

	- **Default**. Questo criterio usa l'indicizzazione hash per le stringhe e l'indicizzazione intervalli per i numeri. Questo criterio è migliore per query di uguaglianza in presenza di stringhe, ORDER BY e query di uguaglianza e di intervallo in presenza di numeri. Questo criterio ha un overhead di archiviazione dell'indice inferiore e include l'indicizzazione geospaziale.
	- **Range**. Questo criterio è migliore quando si utilizzano query ORDER BY, di intervallo e di uguaglianza sia in presenza di numeri che di stringhe. Questo criterio ha un overhead di archiviazione dell'indice maggiore rispetto a **Default** e include l'indicizzazione geospaziale.

	Per altre informazioni sulle regole di indicizzazione, vedere [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md).

8. Fare su **OK** nella parte inferiore della schermata per creare la nuova raccolta.


9. La nuova raccolta ora appare nella sezione **Raccolte** del pannello **Database**.
 
	![Cattura di schermata della nuova raccolta nel pannello Database. Portale di Azure per DocumentDB. Creatore di database basati su cloud per database JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## Informazioni sulle raccolte di DocumentDB. 

Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. Una raccolta è un'entità fatturabile, in cui il [costo](documentdb-performance-levels.md) è determinato dal livello di prestazioni associato alla raccolta.

Le raccolte sono il limite della transazione per stored procedure e trigger e il punto di ingresso per query e operazioni CRUD. Ogni raccolta ha una quantità di velocità effettiva riservata che non viene condivisa con altre raccolte nello stesso account. È quindi possibile applicare la scalabilità orizzontale all'applicazione, in termini sia di archiviazione sia di velocità effettiva, aggiungendo altre raccolte e quindi distribuendo i documenti tra di esse.

Le raccolte sono diverse dalle tabelle nei database relazionali. Tramite le raccolte non viene applicato alcuno schema, in quanto DocumentDB è un database privo di schemi. Pertanto è possibile archiviare diversi tipi di documenti con schemi diversi nella stessa raccolta. È possibile scegliere di usare le raccolte per archiviare oggetti di un singolo tipo come si farebbe con le tabelle. Il modello migliore dipende solo dal modo in cui i dati vengono visualizzati insieme in query e transazioni.

## Altri modi per creare una raccolta di DocumentDB

Le raccolte non possono essere create tramite il portale, ma è possibile crearle usando gli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Per un esempio di codice C# che illustra come creare una raccolta usando l'SDK per .NET di DocumentDB, vedere il file [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) nel progetto CollectionManagement, disponibile nell'archivio [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) in [GitHub.com](https://github.com).

## Risoluzione dei problemi

Se **Aggiungi raccolta** è disabilitato nel portale di Azure, significa che l'account è attualmente disabilitato. Questo in genere si verifica quando sono stati usati tutti i crediti dei vantaggi del mese.

## Passaggi successivi

A questo punto, dopo aver creato una raccolta, il passaggio successivo consiste nell'aggiungere i documenti o nell'importarli nella raccolta. Per l'aggiunta di documenti a una raccolta, sono disponibili alcune opzioni:

- È possibile [aggiungere i documenti](documentdb-view-json-document-explorer.md) usando il riquadro Esplora documenti nel portale.
- È possibile [importare i documenti e i dati](documentdb-import-data.md) usando lo strumento di migrazione dati di DocumentDB, che consente di importare i file JSON e CSV, oltre ai dati da SQL Server, MongoDB, Archiviazione tabelle di Azure e altre raccolte DocumentDB. 
- Oppure è possibile aggiungere i documenti usando gli [SDK di DocumentDB](documentdb-sdk-dotnet.md). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. Il file [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) nel progetto DocumentManagement, disponibile nell'archivio [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) in [GitHub.com](https://github.com), illustra le operazioni CRUD sui documenti tramite l'SDK per .NET di DocumentDB.

Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#executing-queries) nei documenti con [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](documentdb-sdk-dotnet.md).

<!---HONumber=AcomDC_0218_2016-->