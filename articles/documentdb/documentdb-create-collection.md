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
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="mimig"/>

# Come creare una raccolta DocumentDB usando il portale di Azure

PPer utilizzare Microsoft Azure DocumentDB, è necessario disporre di un[DocumentDB account](documentdb-create-account.md)[database](documentdb-create-database.md)una raccolta e documenti. Questo argomento descrive come creare una raccolta DocumentDB nel portale di Azure.

Per informazioni sulle raccolte, vedere [Creare una raccolta DocumentDB](#what-is-a-documentdb-collection)

1.  Nel [portale di Azure](https://portal.azure.com/), nell’indice, fare clic su **Account DocumentDB**. Se **Account DocumentDB** non è visibile, fare clic su **Sfoglia** e quindi su **Account DocumentDB**.

    ![Schermata che evidenzia gli account di DocumentDB nell'indice, l'account nel pannello Account DocumentDB e il database nel pannello dell’account DocumentDB, nella sezione Database](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

2.  Nel pannello **Account DocumentDB** selezionare l'account in cui aggiungere una raccolta. Se non è elencato alcun account, sarà necessario [creare un account DocumentDB](documentdb-create-account.md).

3. Nel pannello **Account DocumentDB** per l'account selezionato, scorrere verso il basso alla sezione **Database** e quindi selezionare il database in cui si desidera aggiungere una raccolta.

    ![Schermata che evidenzia gli account di DocumentDB nell'indice, l'account nel pannello Account DocumentDB e il database nel pannello dell’account DocumentDB, nella sezione Database](./media/documentdb-create-collection/docdb-database-creation-3.png)

4. Nel pannello **Database** fare clic su **Aggiungi raccolta**.

	![Cattura di schermata in cui sono evidenziati il pulsante Aggiungi raccolta nel pannello Database, le impostazioni nel pannello Aggiungi raccolta e il pulsante OK. Portale di Azure per DocumentDB. Creatore di database basati su cloud per database JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-4.png)

5. Nel pannello **Aggiungi raccolta** immettere l'ID della nuova raccolta nella casella **ID**. I nomi delle raccolte devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali. Quando il nome viene convalidato, appare un segno di spunta verde nella casella ID.

	![Cattura di schermata in cui sono evidenziati il pulsante Aggiungi raccolta nel pannello Database, le impostazioni nel pannello Aggiungi raccolta e il pulsante OK. Portale di Azure per DocumentDB. Creatore di database basati su cloud per database JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)

6. Se si intende creare una raccolta con una singola partizione, fare clic sulla freccia per selezionare un piano tariffario, fare clic su Visualizza tutto e quindi selezionare un livello di prestazioni per la raccolta. Per altre informazioni sui livelli di prestazioni disponibili, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md). Ogni raccolta creata è un'entità fatturabile.

	Se si intende creare una raccolta partizionata, andare direttamente al passaggio 7 dato che non è necessario selezionare un piano tariffario.

7. Selezionare una **modalità di partizionamento** per la raccolta, **a singola partizione** o **partizionata**. Una singola partizione ha una capacità di archiviazione riservata di 10 GB e può includere livelli di velocità effettiva da 400 a 10.000 unità richiesta al secondo. Una raccolta partizionata può essere ridimensionata per gestire 250 GB di spazio di archiviazione in più partizioni e può avere livelli di velocità effettiva da 10.100 a 250.000 unità richiesta al secondo.

8. Selezionare la **velocità effettiva** per la raccolta partizionata. Una unità richiesta (RU) corrisponde alla velocità effettiva dell'operazione di lettura di un documento da 1 KB. Per altre informazioni in proposito, vedere l'articolo relativo alle [unità richiesta](documentdb-request-units.md).

9. Se si sta creando una raccolta partizionata, selezionare la **chiave di partizione** per la raccolta. Quando si crea una raccolta ad alte prestazioni è importante selezionare la chiave di partizione corretta. Per altre informazioni sulla selezione di una chiave di partizione, vedere l'articolo relativo al [partizionamento dei dati in DocumentDB](documentdb-partition-data.md).

10. Fare su **OK** nella parte inferiore della schermata per creare la nuova raccolta.

11. La nuova raccolta ora appare nella sezione **Raccolte** del pannello **Database**.
 
	![Cattura di schermata della nuova raccolta nel pannello Database. Portale di Azure per DocumentDB. Creatore di database basati su cloud per database JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-9.png)

## Informazioni sulle raccolte di DocumentDB. 

Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. Una raccolta è un'entità fatturabile, in cui il [costo](documentdb-performance-levels.md) è determinato dalla velocità effettiva con provisioning della raccolta. Le raccolte possono estendersi su più partizioni o server e possono essere ridimensionate per gestire volumi praticamente illimitati di archiviazione o di velocità effettiva.

Le raccolte vengono partizionate automaticamente in uno o più server fisici da DocumentDB. Quando si crea una raccolta, è possibile specificare la velocità effettiva con provisioning in termini di unità richiesta al secondo e una proprietà chiave di partizione. Il valore di questa proprietà viene usato da DocumentDB per distribuire i documenti tra le partizioni e indirizzare le richieste come query. Il valore della chiave di partizione funge anche da limite della transazione per le stored procedure e i trigger. Ogni raccolta ha una quantità di velocità effettiva riservata che non viene condivisa con altre raccolte nello stesso account. È quindi possibile aumentare il numero di istanze dell'applicazione sia in termini di archiviazione che di velocità effettiva.

Le raccolte sono diverse dalle tabelle nei database relazionali. Tramite le raccolte non viene applicato alcuno schema, in quanto DocumentDB è un database privo di schemi. Pertanto è possibile archiviare diversi tipi di documenti con schemi diversi nella stessa raccolta. È possibile scegliere di usare le raccolte per archiviare oggetti di un singolo tipo come si farebbe con le tabelle. Il modello migliore dipende solo dal modo in cui i dati vengono visualizzati insieme in query e transazioni.

## Altri modi per creare una raccolta di DocumentDB

Per creare le raccolte non è necessario usare il portale. Possono essere create anche usando gli [SDK di DocumentDB](documentdb-sdk-dotnet.md) e l'API REST.

- Per un esempio di codice C#, vedere gli [esempi di raccolta per C#](documentdb-dotnet-samples.md#collection-examples). 
- Per un esempio di codice Node.js, vedere gli [esempi di raccolta per Node.js](documentdb-nodejs-samples.md#collection-examples).
- Per un esempio di codice Python, vedere gli [esempi di raccolta per Python](documentdb-python-samples.md#collection-examples).
- Per un esempio di API REST, vedere l'articolo relativo alla [creazione di una raccolta](https://msdn.microsoft.com/library/azure/mt489078.aspx).

## Risoluzione dei problemi

Se **Aggiungi raccolta** è disabilitato nel portale di Azure, significa che l'account è attualmente disabilitato. Questo in genere si verifica quando sono stati usati tutti i crediti dei vantaggi del mese.

## Passaggi successivi

A questo punto, dopo aver creato una raccolta, il passaggio successivo consiste nell'aggiungere i documenti o nell'importarli nella raccolta. Per l'aggiunta di documenti a una raccolta, sono disponibili alcune opzioni:

- È possibile [aggiungere i documenti](documentdb-view-json-document-explorer.md) usando il riquadro Esplora documenti nel portale.
- È possibile [importare i documenti e i dati](documentdb-import-data.md) usando lo strumento di migrazione dati di DocumentDB, che consente di importare i file JSON e CSV, oltre ai dati da SQL Server, MongoDB, Archiviazione tabelle di Azure e altre raccolte DocumentDB. 
- Oppure è possibile aggiungere i documenti usando gli [SDK di DocumentDB](documentdb-sdk-dotnet.md). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. Per esempi di codice C# che illustrano l'uso dei documenti con DocumentDB .NET SDK, vedere gli [esempi di documento per C#](documentdb-dotnet-samples.md#document-examples). Per esempi di codice Node.js che illustrano l'uso dei documenti con DocumentDB Node.js SDK, vedere gli [esempi di documento per Node.js](documentdb-nodejs-samples.md#document-examples).

Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#executing-queries) nei documenti con [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](documentdb-sdk-dotnet.md).

<!---HONumber=AcomDC_0406_2016-->