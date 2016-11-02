<properties
	pageTitle="Esplora query di DocumentDB: un editor di query SQL | Microsoft Azure"
	description="Informazioni su Esplora query di DocumentDB, un editor di query SQL nel portale di Azure per la scrittura e l'esecuzione di query SQL in una raccolta DocumentDB NoSQL."
	keywords="scrittura di query sql, editor di query sql"
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

# Scrivere, modificare ed eseguire query SQL per DocumentDB con Esplora query 

Questo articolo offre una panoramica di Esplora query per [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), uno strumento del portale di Azure che consente di scrivere, modificare ed eseguire query SQL in una [raccolta DocumentDB](documentdb-create-collection.md).

1. Nell'indice del portale di Azure fare clic su **DocumentDB (NoSQL)**. Se **DocumentDB (NoSQL)** non è visibile, fare clic su **More Services** (Altri servizi) e quindi su **DocumentDB (NoSQL)**.

2. Nel menu delle risorse fare clic su **Esplora query**.

	![Screenshot del portale di Azure con Esplora query evidenziato](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)

3. Nel pannello **Esplora query** selezionare **Database** e **Raccolte** in cui eseguire la query dai rispettivi elenchi a discesa e digitare la query da eseguire.

    Gli elenchi a discesa **Database** e **Raccolte** vengono precompilati a seconda del contesto in cui viene avviato Esplora query.

    È disponibile una query predefinita, `SELECT TOP 100 * FROM c`. È possibile accettare la query predefinita o costruirne una usando il linguaggio di query SQL descritto nel [foglio informativo sulle query SQL](documentdb-sql-query-cheat-sheet.md) o nell'articolo [Query e sintassi SQL in DocumentDB](documentdb-sql-query.md).

    Fare clic su **Esegui query** per visualizzare i risultati.

	![Screenshot della scrittura di query SQL in Esplora query, un editor di query SQL](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

4. Il pannello **Risultati** mostra l'output della query.

	![Screenshot dei risultati della scrittura di query SQL in Esplora query](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## Usare i risultati

Per impostazione predefinita, Esplora query restituisce risultati in set di 100. Se la query produce più di 100 risultati, usare semplicemente i comandi **Pagina successiva** e **Pagina precedente** per spostarsi all'interno del set di risultati.

![Schermata del supporto di paginazione di Esplora query](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

Perché le query abbiano esito positivo, il pannello **Informazioni** deve contenere metriche quali l'addebito richiesto, il numero di round trip eseguiti dalla query, il set di risultati attualmente visualizzato e specificare l'eventuale presenza di altri risultati a cui è possibile accedere con il comando **Pagina successiva**, come descritto in precedenza.

![Schermata delle informazioni sulle query di Esplora query](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## Usare più query

Se si usano più query e si vuole passare rapidamente da una all'altra, è possibile immettere tutte le query nella casella di testo di query del pannello **Esplora query**, evidenziare quella da eseguire e quindi fare clic su **Esegui query** per visualizzare i risultati.

![Screenshot della scrittura di più query SQL in Esplora query, un editor di query SQL, in cui sono evidenziate ed eseguite singole query](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## Aggiungere query da un file nell'editor di query SQL

È possibile caricare il contenuto di un file esistente tramite il comando **Carica file**.

![Screenshot che illustra come caricare query SQL da un file in Esplora query usando Carica file](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## Risoluzione dei problemi

Se una query viene completata con errori, Esplora query visualizza un elenco di errori che può facilitare la risoluzione dei problemi.

![Schermata degli errori di query di Esplora query](./media/documentdb-query-collections-query-explorer/queryerror.png)

## Eseguire query SQL di DocumentDB all'esterno del portale

Esplora query nel portale di Azure è solo uno dei modi in cui è possibile eseguire query SQL in DocumentDB. Per eseguire query SQL è anche possibile usare l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o gli [SDK client](documentdb-sdk-dotnet.md). Per altre informazioni sull'uso degli altri metodi, vedere [Esecuzione di query SQL](documentdb-sql-query.md#executing-sql-queries)

## Passaggi successivi

Per altre informazioni sulla grammatica SQL di DocumentDB supportata in Esplora query, vedere l'articolo [Query e sintassi SQL in DocumentDB](documentdb-sql-query.md) o stampare una copia del [foglio informativo sulle query SQL](documentdb-sql-query-cheat-sheet.md). La pagina [Query Playground](https://www.documentdb.com/sql/demo) permette di testare le query online usando un set di dati di esempio.

<!---HONumber=AcomDC_0831_2016-->