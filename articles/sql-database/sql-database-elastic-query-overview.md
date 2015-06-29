<properties 
	title="Elastic database query – previewing May 2015" 
	pageTitle="Query di database elastica – anteprima maggio 2015" 
	description="Annuncia la funzionalità di query elastica" 
	metaKeywords="azure sql database elastic global queries" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh" />

# Query di database elastica – anteprima giugno 2015 

Query di database elastica è una nuova funzionalità di database SQL di Azure in anteprima alla fine di giugno 2015. Introduce la capacità di eseguire una query T-SQL che si estende su più database con una singola connessione a un database SQL di Azure. In questo modo, un set di dati partizionati, definiti mediante una mappa di partizionamento orizzontale di database elastico, può essere visualizzato come singolo archivio dati integrato. Utilizzata per la creazione di report e l’integrazione dei dati, tutta l’elaborazione delle query distribuite viene gestita dietro le quinte. Le connessioni sono supportate tramite qualsiasi driver in grado di comunicare con il database SQL, tra cui ADO.Net, ODBC, JDBC, Node. js, PHP e così via.

## Scenari di query di database elastica

L'obiettivo della query di database elastica consiste nel facilitare la creazione di report attraverso un livello di dati partizionati in cui più database o partizioni contribuiscono righe in un singolo risultato complessivo di una query T-SQL. La query T-SQL può essere composta in modo diretto dall'utente o dall’applicazione oppure indirettamente tramite strumenti che sono connessi al database di query elastiche della query globale. Ciò è particolarmente utile per gli strumenti di BI commerciale, creazione di report o di integrazione dei dati, pacchetti software che non possono essere estesi direttamente utilizzando le librerie a scalabilità elastica. Inoltre, facilita l'accesso a un'intera raccolta di dati partizionati tramite query eseguite da SQL Server Management Studio o Visual Studio e supporta l'accesso trasparente ai dati con più partizioni da Entity Framework o da altri ambienti ORM. Nella Figura 1 viene illustrato questo scenario in cui la query di DB elastica della query globale offre una seconda via, diversa, per inviare query al livello dati partizionati oltre all'applicazione cloud esistente \(che può utilizzare le librerie a scalabilità elastica\).

![Query di database elastiche][1]

Gli scenari possono essere caratterizzati dalle seguenti topologie:

-	Livello dati partizionati \(topologia 1\): il livello dati è stato progettato per il partizionamento orizzontale. Il partizionamento orizzontale viene eseguito e gestito utilizzando \(1\) la libreria client di database elastici o \(2\) il partizionamento automatico. Il caso della creazione di report consiste nel calcolare i report tra molte partizioni con requisito funzionale o di connettività al di là di quanto sia possibile ottenere con la query multipartizione nella libreria client di database elastici. 

-	Design multidatabase \(topologia 2\): in questo caso, il livello dati è stato partizionato verticalmente, in modo che i diversi tipi di dati risiedano in database differenti. Il caso della creazione di report consiste nel calcolare i report sui dati distribuiti su più database, ma che devono essere integrati in un unico risultato complessivo per il report.

Nell’anteprima della query di database elastica verranno trattati entrambi i casi. I clienti con topologia 1 possono basarsi sulla mappa delle partizioni esistente, se utilizzano la libreria client di database elastici per la gestione delle partizioni. Gli strumenti di auto partizionamento, a loro volta, dovranno creare una mappa di partizioni utilizzando strumenti di database elastici per i relativi livelli di dati, al fine di utilizzare questa funzionalità di anteprima. I clienti che seguono la topologia 2 devono creare una mappa di partizionamento diversa per ognuno dei relativi database. Ciascuna mappa di partizionamento farà riferimento a una singola partizione che può quindi esporre le relative tabelle per le query tra partizioni.

## Passaggi successivi
La query di database elastica è pianificata per l'anteprima entro la fine di giugno 2015. Controllare nuovamente questa pagina per ulteriori informazioni e istruzioni dettagliate su come utilizzare la funzionalità in quel momento.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
<!--anchors-->

<!---HONumber=58_postMigration-->