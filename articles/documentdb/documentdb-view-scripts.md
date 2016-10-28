<properties
	pageTitle="Esplora script di DocumentDB, un editor JavaScript | Microsoft Azure"
	description="Informazioni su Esplora script, uno strumento del portale di Azure che consente di gestire elementi di programmazione sul lato server di DocumentDB quali stored procedure, trigger e funzioni definite dall'utente."
	keywords="Editor JavaScript"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="anhoh"/>

# Creare ed eseguire stored procedure, trigger e funzioni definite dall'utente usando Esplora script di DocumentDB

Questo articolo fornisce una panoramica di Esplora script di [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), un editor JavaScript del portale di Azure che consente di visualizzare ed eseguire elementi di programmazione sul lato server di DocumentDB quali stored procedure, trigger e funzioni definite dall'utente. Per altre informazioni sulla programmazione sul lato server di DocumentDB, vedere l'articolo relativo alla [programmazione sul lato server DocumentDB: stored procedure, trigger del database e funzioni definite dall'utente](documentdb-programming.md).

## Avviare Esplora script

1. Nell'indice del portale di Azure fare clic su **DocumentDB (NoSQL)**. Se **Account DocumentDB** non è visibile, fare clic su **More Services** (Altri servizi) e quindi su **DocumentDB (NoSQL)**.

2. Nel menu delle risorse fare clic su **Esplora script**.

	![Schermata del comando Esplora script](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
    Gli elenchi a discesa **Database** e **Raccolta** vengono precompilati a seconda del contesto in cui viene avviato Esplora script. Ad esempio, se viene avviato da un pannello di database, viene prepopolato il database corrente. Se viene avviato da un pannello della raccolta, viene prepopolata la raccolta corrente.

4.  Usare gli elenchi a discesa **Database** e **Raccolta** per modificare facilmente la raccolta da cui sono attualmente visualizzati gli script senza dover chiudere e riavviare Esplora script.

5. Esplora script supporta anche l'applicazione di filtri al set di script attualmente caricato in base alla relativa proprietà ID. Digitare i criteri nella casella di filtro. I risultati presenti nell'elenco di Esplora script verranno filtrati in base ai criteri forniti.

	![Schermata di Esplora script con risultati del filtro](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT] La funzionalità di filtro di Esplora script consente di filtrare solo dal set di script caricati ***correntemente*** e non aggiorna automaticamente la raccolta selezionata.

5. Per aggiornare l'elenco di script caricati da Esplora script, fare semplicemente clic sul comando **Aggiorna** nella parte superiore del pannello.

	![Schermata del comando di aggiornamento di Esplora script](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## Creare, visualizzare e modificare stored procedure, trigger e funzioni definite dall'utente

Esplora script consente di eseguire facilmente operazioni CRUD sugli elementi di programmazione lato server di DocumentDB.

- Per creare uno script, è sufficiente fare clic sul comando di creazione applicabile all'interno di Esplora script, fornire un ID, immettere il contenuto dello script e fare clic su **Salva**.

	![Schermata dell'opzione di creazione di Esplora script con l'editor JavaScript](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- Quando si crea un trigger, è necessario specificare anche il tipo di trigger e l'operazione del trigger

	![Schermata dell’opzione di creazione trigger di Esplora script](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- Fare clic sullo script a cui si è interessati per visualizzarlo.

	![Schermata dell’esperienza di visualizzazione script di Esplora script](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Per modificare uno script, è sufficiente apportare le modifiche desiderate nell'editor JavaScript e fare clic su **Salva**.

	![Schermata dell’esperienza di visualizzazione script di Esplora script](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- Per annullare le modifiche in sospeso a uno script, fare semplicemente clic sul comando **Annulla**.

	![Schermata dell’esperienza di annullamento modifiche di Esplora script](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- Esplora script consente, inoltre, di visualizzare facilmente le proprietà di sistema dello script attualmente caricato facendo clic sul comando **Proprietà**.

	![Schermata della vista delle proprietà dello script di Esplora script](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE] La proprietà timestamp (\_ts) viene rappresentata internamente come epoch time, ma Esplora script consente di visualizzare il valore in formato GMT leggibile.

- Per eliminare uno script, selezionarlo in Esplora script e scegliere il comando **Elimina**.

	![Schermata del comando di eliminazione di Esplora script](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- Confermare l'azione di eliminazione facendo clic su **Sì** o annullare l'azione di eliminazione facendo clic su **No**.

	![Schermata del comando di eliminazione di Esplora script](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## Eseguire una stored procedure

> [AZURE.WARNING] L’esecuzione di stored procedure in Esplora script non è ancora supportata per le raccolte partizionate lato server. Per altre informazioni, visitare [Partizionamento e ridimensionamento in DocumentDB](documentdb-partition-data.md).

Esplora script consente di eseguire stored procedure lato server dal portale di Azure.

- Quando si apre il pannello di una stored procedure appena creata, viene fornito uno script predefinito (*prefix*). Per eseguire lo script *prefix* o uno script personale, aggiungere un *ID* e gli *input*. Per le stored procedure che accettano più parametri, tutti gli input devono trovarsi all'interno di una matrice, ad esempio *["foo", "bar"]*.

	![Schermata del pannello Stored procedure di Esplora script per l'aggiunta di input e l'esecuzione di una stored procedure](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)

- Per eseguire una stored procedure, è sufficiente fare clic sul comando **Salva ed esegui** nel riquadro dell'editor di script.

	> [AZURE.NOTE] Il comando **Salva ed esegui** salva la stored procedure prima dell'esecuzione ovvero sovrascrive la versione precedentemente salvata della stored procedure.

- Le esecuzioni di stored procedure eseguite con esito positivo presentano lo stato *La stored procedure è stata salvata e l'esecuzione è riuscita* e i risultati restituiti sono visualizzati nel riquadro *Risultati*.

	![Schermata del pannello Stored procedure di Esplora script per l'esecuzione di una stored procedure](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)

- Se l'esecuzione genera un errore, viene visualizzato nel riquadro *Risultati*.

	![Schermata della vista delle proprietà dello script di Esplora script Esecuzione di una stored procedure con errori](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## Usare gli script all'esterno del portale

Esplora script del portale di Azure è solo uno dei modi per utilizzare stored procedure, trigger e funzioni definite dall'utente in DocumentDB. Gli script possono essere usati anche con l'API REST e gli [SDK client](documentdb-sdk-dotnet.md). La documentazione dell'API REST include esempi dell'uso di [stored procedure mediante REST](https://msdn.microsoft.com/library/azure/mt489092.aspx), [funzioni definite dall'utente mediante REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) e [trigger mediante REST](https://msdn.microsoft.com/library/azure/mt489116.aspx). Sono inoltre disponibili esempi che mostrano come [usare gli script mediante C#](documentdb-dotnet-samples.md#server-side-programming-examples) e [usare gli script mediante Node.js](documentdb-nodejs-samples.md#server-side-programming-examples).

## Passaggi successivi

Per altre informazioni sulla programmazione lato server di DocumentDB, vedere l'articolo relativo a [Stored procedure, trigger del database e funzioni definite dall'utente](documentdb-programming.md).

Anche il [percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/documentdb/) è una risorsa utile per l'utente durante l'apprendimento di DocumentDB.

<!---HONumber=AcomDC_0831_2016-->