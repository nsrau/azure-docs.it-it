<properties
	pageTitle="Raccolte di connessioni per database SQL e SQL Server"
	description="In questo argomento viene elencato il numero di versione minima per ogni driver che è possibile utilizzare nei programmi client per la connessione al database SQL di Azure o a Microsoft SQL Server. Viene fornito un collegamento per le informazioni sulla versione relativamente ai driver rilasciati dalla community invece che da Microsoft."
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/01/2015"
	ms.author="pehteh"/>


# Raccolte di connessioni per database SQL e SQL Server


In questo argomento viene elencato il numero di versione minima per ogni libreria/driver che è possibile utilizzare nei programmi client quando ci si connette al database SQL di Azure o a Microsoft SQL Server.


L’argomento è suddiviso in due sezioni:


- *Tabella delle librerie di driver rilasciate da Microsoft*: vengono illustrate le librerie rilasciate da Microsoft. Microsoft gestisce le informazioni contenute in questa sezione.
- *Librerie di terze parti*: vengono elencate le librerie rilasciate e gestite da terze parti e piuttosto che da Microsoft. **Questa sezione è gestita unicamente dalla community di sviluppatori pubblica. Microsoft non gestisce questa sezione.**


## Tabella delle librerie di driver rilasciate da Microsoft


Nella tabella seguente vengono visualizzate le librerie rilasciate da Microsoft. Nella colonna **Librerie** vengono forniti i collegamenti che è possibile utilizzare per scaricare ciascuna libreria. Nella colonna **Versione** è elencata la versione minima consigliata per l'interazione con database SQL di Azure e Microsoft SQL Server.


| Piattaforma | Sist oper | Librerie | Versione | Descrizione |
| :--- | :--- | :--- | :--- | :--- |
| .NET | Multipiattaforma (.NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4.5 + | Provider SQL server per .NET Framework |
| PHP | Windows | [PHP for SQL Server](http://www.microsoft.com/it-it/download/details.aspx?id=20098) | 2.0 + | Driver PHP per SQL Server |
| Java | Windows | [JDBC for SQL Server](https://www.microsoft.com/it-it/download/details.aspx?id=11774) | 2.0 + | Driver JDBC di tipo 4 che offre connettività di database tramite l’API JDBC standard |
| ODBC | Windows | [ODBC for SQL Server](http://www.microsoft.com/it-it/download/details.aspx?id=36434) | 11.0 + | Driver Microsoft ODBC per SQL Server |
| ODBC | Suse Linux | [ODBC for SQL Server](http://www.microsoft.com/it-it/download/details.aspx?id=34687) | 11.0 + | Driver Microsoft ODBC per SQL Server |
| ODBC | Redhat Linux | [ODBC for SQL Server](http://www.microsoft.com/it-it/download/details.aspx?id=34687) | 11.0 + | Driver Microsoft ODBC per SQL Server |


## Librerie di terze parti


> [AZURE.IMPORTANT]Nella tabella seguente vengono visualizzate le librerie che rilasciate da terze parti in base ai termini di licenza di terze parti. L’utente è responsabile della verifica e della conformità con le licenze di terze parti rilevanti al fine di utilizzare queste librerie. L’utente si assume il rischio dell'utilizzo di queste librerie. Microsoft non fornisce alcuna garanzia, esplicita o implicita, relativamente alle informazioni fornite di seguito e ha semplicemente fornito le informazioni per ragioni di comodità per gli utenti. Niente di quanto indicato di seguito implica qualsiasi tipo di approvazione da parte di Microsoft. <br/><br/>L’aggiornamento e la gestione delle informazioni contenute nella sezione "Librerie di terze parti" spettano alla community pubblica degli sviluppatori, tramite il repository [azure-content](http://github.com/Azure/azure-content/) di proprietà di **Azure** su GitHub.com. Microsoft invita gli sviluppatori ad aggiornare questa sezione. Il personale Microsoft *non* intende gestire le informazioni contenute in questa sezione, in parte perché altri utenti hanno una maggiore esperienza con ciascuna libreria di terze parti. Grazie


Nella tabella seguente vengono visualizzate le librerie rilasciate da terze parti, quali altre società o dalla community. Le librerie rilasciate da Microsoft sono limitate alla sezione precedente in questo argomento.


| Piattaforma | Librerie |
| :-- | :-- |
| Python | [pymssql *(org, stable)*](http://pymssql.org/en/stable/)<br/><br/>[pymssql *(org)*](http://pymssql.org/) |
| Node.js | [Tedious *(npmjs)*](http://www.npmjs.com/package/tedious) |
| Node.js | [Node-MSSQL *(github, patriksimek)*](https://github.com/patriksimek/node-mssql)<br/><br/>[Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| Node.js | [Edge.js *(github com, tjanczuk)*](https://github.com/tjanczuk/edge)<br/><br/>[Edge.js *(tjanczuk, github io)*](http://tjanczuk.github.io/edge/) |
| . | [FreeTDS *(org)*](http://www.freetds.org/) |


<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=58--> 