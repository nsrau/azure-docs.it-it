<properties 
	pageTitle="Creare, modificare ed eseguire query SQL in una raccolta di DocumentDB tramite Esplora query | Azure" 
	description="Informazioni su Esplora query di DocumentDB, uno strumento del portale di anteprima di Azure per creare, modificare ed eseguire query SQL in una raccolta di DocumentDB." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/13/2015" 
	ms.author="stbaro"/>

# Creare, modificare ed eseguire query SQL in una raccolta di DocumentDB tramite Esplora query #

Questo articolo fornisce una panoramica Di Esplora query per [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), uno strumento del portale di anteprima di Azure che consente di creare, modificare ed eseguire query in una raccolta di DocumentDB. 

Dopo aver completato questa esercitazione, si potrà rispondere alle domande seguenti:  

-	Come è possibile creare, modificare ed eseguire query con facilità in una raccolta di DocumentDB tramite un Web browser?
-	Come è possibile spostarsi facilmente tra le pagine dei risultati delle query in DocumentDB tramite un Web browser?
-	Come è possibile risolvere errori di sintassi delle query per DocumentDB? 

##<a id="Launch"></a>Avviare e spostarsi in Esplora query##

Esplora query può essere avviato da qualsiasi account, database e pannelli di raccolte di DocumentDB.
  
1. Nella parte inferiore di ogni pannello è presente una sezione **Strumenti di sviluppo** che contiene la sezione **Esplora query**.
	
	![Screenshot of Query Explorer part](./media/documentdb-query-collections-query-explorer/queryexplorerpart.png) 

2. Fare clic su questa sezione per avviare Esplora query.

	Gli elenchi a discesa **Database** e **Raccolta** vengono precompilati a seconda del contesto in cui viene avviato Esplora query.  Ad esempio, se viene avviato da un pannello di database, viene prepopolato il database corrente. Se viene avviato da un pannello della raccolta, viene prepopolata la raccolta corrente.

	![Screenshot of Query Explorer](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

##<a id="Create"></a>Creare, modificare ed eseguire query tramite Esplora query##

Esplora query consente di creare, modificare ed eseguire query in modo semplice su una raccolta di DocumentDB e offre l'evidenziazione delle parole chiave e dei valori principali per migliorare l'esperienza di creazione delle query.  

- Quando si apre inizialmente Esplora query, viene fornita una query predefinita SELECT * FROM c.  È possibile accettare la query predefinita oppure crearne una propria e quindi usare il pulsante **Esegui query** per visualizzare i risultati. Esplora query supporta il linguaggio di query SQL DocumentDB come descritto in [Eseguire query con DocumentDB](documentdb-sql-query.md).

	![Screenshot of Query Explorer query results](./media/documentdb-query-collections-query-explorer/queryresults1.png) 

- Per impostazione predefinita, Esplora query restituisce risultati in set di 100.  Se la query produce più di 100 risultati, usare semplicemente i comandi **Pagina successiva** e **Pagina precedente** per spostarsi all'interno del set di risultati.

	![Screenshot of Query Explorer pagination support](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

- Le query completate forniscono informazioni quali l'addebito di richiesta, il set di risultati attualmente visualizzato e se sono presenti altri risultati, nel qual caso è possibile accedervi tramite il comando **Pagina successiva**, come descritto in precedenza.

	![Screenshot of Query Explorer query information](./media/documentdb-query-collections-query-explorer/queryinformation.png)

- Analogamente, se una query viene completata con errori, Esplora query visualizza un elenco di errori che possono facilitare la risoluzione dei problemi.

	![Screenshot of Query Explorer query errors](./media/documentdb-query-collections-query-explorer/queryerror.png)

##<a name="NextSteps"></a>Passaggi successivi

- Per altre informazioni su DocumentDB, vedere [qui](http://azure.com/docdb).
- Per altre informazioni sulla grammatica SQL DocumentDB supportata in Esplora query, vedere [qui](documentdb-sql-query.md).

<!--HONumber=49-->