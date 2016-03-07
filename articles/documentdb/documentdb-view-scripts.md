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
	ms.date="02/23/2016"
	ms.author="anhoh"/>

# Creare ed eseguire stored procedure, trigger e funzioni definite dall'utente usando Esplora script di DocumentDB

Questo articolo fornisce una panoramica di Esplora script di [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), un editor JavaScript del portale di Azure che consente di visualizzare elementi di programmazione sul lato server di DocumentDB quali stored procedure, trigger e funzioni definite dall'utente. Per altre informazioni sulla programmazione sul lato server di DocumentDB, vedere l'articolo relativo alla [programmazione sul lato server DocumentDB: stored procedure, trigger del database e funzioni definite dall'utente](documentdb-programming.md).

## Avviare Esplora script

1. Nel portale di Azure, nell'indice, fare clic su **Account DocumentDB**. Se **Account DocumentDB** non è visibile, fare clic su **Sfoglia** e quindi su **Account DocumentDB**.

2. Nella parte superiore del pannello **Account DocumentDB** fare clic su **Esplora script**.

	![Schermata del comando Esplora script](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
    >[AZURE.NOTE] Esplora script viene visualizzato anche nei pannelli di database e raccolte.

    Gli elenchi a discesa **Database** e **Raccolta** vengono precompilati a seconda del contesto in cui viene avviato Esplora script. Ad esempio, se viene avviato da un pannello di database, viene prepopolato il database corrente. Se viene avviato da un pannello della raccolta, viene prepopolata la raccolta corrente.

	![Schermata di Esplora script](./media/documentdb-view-scripts/scriptexplorerinitial.png)

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

Esplora script consente di eseguire stored procedure lato server dal portale di Azure.

- Quando si apre il pannello di una stored procedure appena creata, verrà fornito uno script predefinito (*prefix*). Per eseguire lo script *prefix* o un proprio script, aggiungere un *ID* e alcuni *input*. Per le stored procedure che accettano più parametri, tutti gli input devono trovarsi all'interno di una matrice, ad esempio *["foo", "bar"]*.

	![Schermata del pannello Stored procedure di Esplora script per l'aggiunta di input e l'esecuzione di una stored procedure](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)

- Per eseguire una stored procedure, è sufficiente fare clic sul comando **Salva ed esegui** nel riquadro dell'editor di script.

	> [AZURE.NOTE] Il comando **Salva ed esegui** salverà la stored procedure prima dell'esecuzione. Questo significa che le precedenti versioni della stored procedure verranno sovrascritte.

- Le esecuzioni di stored procedure eseguite con esito positivo avranno lo stato *Stored procedure salvata ed eseguita correttamente* e i risultati restituiti verranno visualizzati nel riquadro *Risultati*.

	![Schermata del pannello Stored procedure di Esplora script per l'esecuzione di una stored procedure](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)

- Se l'esecuzione genera un errore, tale errore verrà visualizzato nel riquadro *Risultati*.

	![Schermata della vista delle proprietà dello script di Esplora script Esecuzione di una stored procedure con errori](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## Usare gli script all'esterno del portale

Esplora script del portale di Azure è solo uno dei modi per utilizzare stored procedure, trigger e funzioni definite dall'utente in DocumentDB. È anche possibile usare gli script mediante l'API REST e gli [SDK client](documentdb-sdk-dotnet.md). La documentazione dell'API REST include esempi dell'utilizzo di [stored procedure mediante REST](https://msdn.microsoft.com/library/azure/mt489092.aspx), [funzioni definite dall'utente mediante REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) e [trigger mediante REST](https://msdn.microsoft.com/library/azure/mt489116.aspx). Sono inoltre disponibili esempi che mostrano come [utilizzare gli script mediante C#](documentdb-dotnet-samples.md#server-side-programming-examples) e [utilizzare gli script mediante Node.js](documentdb-nodejs-samples.md#server-side-programming-examples).

## Passaggi successivi

Altre informazioni sulla programmazione sul lato server di DocumentDB sono disponibili nell'articolo relativo alla [programmazione sul lato server DocumentDB: stored procedure, trigger del database e funzioni definite dall'utente](documentdb-programming.md).

Anche il [percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/documentdb/) è una risorsa utile per l'utente durante l'apprendimento di DocumentDB.

<!---HONumber=AcomDC_0224_2016-->