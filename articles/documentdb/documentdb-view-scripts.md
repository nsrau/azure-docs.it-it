<properties
	pageTitle="Visualizzare stored procedure, trigger e funzioni definite dall'utente utilizzando Esplora script di DocumentDB | Microsoft Azure"
	description="Informazioni su Esplora script, uno strumento del portale di Azure che consente di visualizzare elementi di programmazione sul lato server di DocumentDB quali stored procedure, trigger e funzioni definite dall'utente."
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
	ms.date="12/01/2015"
	ms.author="anhoh"/>

# Visualizzare, modificare e creare stored procedure, trigger e funzioni definite dall'utente utilizzando Esplora script di DocumentDB

In questo articolo viene fornita una panoramica di Esplora script di [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), uno strumento del portale di Azure che consente di visualizzare elementi di programmazione sul lato server di DocumentDB quali stored procedure, trigger e funzioni definite dall'utente. Ulteriori informazioni sulla programmazione sul lato server di DocumentDB sono disponibili [qui](documentdb-programming.md).

Dopo aver completato questa esercitazione, si potrà rispondere alle domande seguenti:

-	Come è possibile visualizzare facilmente stored procedure di DocumentDB tramite un browser Web?
-	Come è possibile visualizzare facilmente trigger di DocumentDB tramite un browser Web?
-	Come è possibile visualizzare facilmente funzioni definite dall’utente di DocumentDB tramite un browser Web?

## Avviare e utilizzare Esplora script

Esplora script può essere avviato da qualsiasi pannello di account, database e raccolta di DocumentDB.

1. Nella parte superiore del pannello di database o account di DocumentDB, fare semplicemente clic sul comando **Esplora script**.

	![Schermata del comando Esplora script](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
2. In alternativa, nella parte inferiore di ogni pannello è presente una sezione **Strumenti di sviluppo** che contiene la parte **Esplora script**.

	![Schermata della parte Esplora script](./media/documentdb-view-scripts/scriptexplorerpart.png)

2. Selezionare semplicemente il comando o la parte per avviare Esplora script.

	<p>Gli elenchi a discesa **Database** e **Raccolta** vengono precompilati a seconda del contesto in cui viene avviato Esplora script. Ad esempio, se viene avviato da un pannello di database, viene prepopolato il database corrente. Se viene avviato da un pannello della raccolta, viene prepopolata la raccolta corrente.

	![Schermata di Esplora script](./media/documentdb-view-scripts/scriptexplorerinitial.png)


3. Gli elenchi a discesa **Database** e **Raccolta** possono essere usati per modificare facilmente la raccolta da cui sono attualmente visualizzati gli script senza dover chiudere e riavviare Esplora script.

4. Esplora script supporta anche l'applicazione di filtri al set di script attualmente caricato in base alla relativa proprietà ID. È sufficiente digitare nella casella del filtro.

	![Schermata di Esplora script con filtro evidenziato](./media/documentdb-view-scripts/scriptexplorerfilter.png)

	I risultati nell'elenco di Esplora script vengono filtrati in base ai criteri forniti.

	![Schermata di Esplora script con risultati del filtro](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT]La funzionalità di filtro di Esplora script consente di filtrare solo dal set di script caricati ***correntemente*** e non aggiorna automaticamente la raccolta selezionata.

5. Per aggiornare l'elenco di script caricati da Esplora script, fare semplicemente clic sul comando **Aggiorna** nella parte superiore del pannello.

	![Schermata del comando di aggiornamento di Esplora script](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## Visualizzare, modificare, creare ed eliminare stored procedure, trigger e funzioni definite dall'utente con Esplora script

Esplora script consente di eseguire facilmente operazioni CRUD sugli elementi di programmazione lato server di DocumentDB.

- Per creare uno script, è sufficiente fare clic sul comando di creazione applicabile all'interno di Esplora script, fornire un id, immettere il contenuto dello script e fare clic sul comando **Salva**.

	![Schermata dell’opzione di creazione di Esplora script](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- Quando si crea un trigger, è necessario specificare anche il tipo di trigger e l'operazione del trigger

	![Schermata dell’opzione di creazione trigger di Esplora script](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- Fare clic sullo script a cui si è interessati per visualizzarlo.

	![Schermata dell’esperienza di visualizzazione script di Esplora script](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Per modificare uno script, è sufficiente apportare le modifiche desiderate e fare clic sul comando **Salva**.

	![Schermata dell’esperienza di visualizzazione script di Esplora script](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- Per annullare le modifiche in sospeso a uno script, fare semplicemente clic sul comando **Annulla**.

	![Schermata dell’esperienza di annullamento modifiche di Esplora script](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- Esplora script consente, inoltre, di visualizzare facilmente le proprietà di sistema dello script attualmente caricato facendo clic sul comando **Proprietà**.

	![Schermata della vista delle proprietà dello script di Esplora script](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE]La proprietà timestamp (\_ts) viene rappresentata internamente come epoch time, ma Esplora script consente di visualizzare il valore in formato GMT leggibile.

- Per eliminare uno script, selezionarlo in Esplora script e scegliere il comando **Elimina**.

	![Schermata del comando di eliminazione di Esplora script](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- Confermare l'azione di eliminazione facendo clic su **Sì** o annullare l'azione di eliminazione facendo clic su **No**.

	![Schermata del comando di eliminazione di Esplora script](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## Passaggi successivi

Per altre informazioni su DocumentDB, vedere [qui](http://azure.com/docdb).
 

<!---HONumber=AcomDC_1203_2015-->