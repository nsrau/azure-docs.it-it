<properties
	pageTitle="Raccolte di connessioni per database SQL e SQL Server"
	description="In questo argomento viene elencato il numero di versione minima per ogni driver che è possibile utilizzare nei programmi client per la connessione al database SQL di Azure o a Microsoft SQL Server. È disponibile un collegamento per le informazioni sulla versione relative ai driver rilasciati dalla community anziché da Microsoft."
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
	ms.date="06/24/2015"
	ms.author="pehteh"/>


# Raccolte di connessioni per database SQL e SQL Server


In questo argomento viene elencato il numero di versione minima per ogni libreria/driver che è possibile utilizzare nei programmi client quando ci si connette al database SQL di Azure o a Microsoft SQL Server.


L’argomento è suddiviso in due sezioni:


- *Tabella delle librerie di driver rilasciate da Microsoft*: vengono illustrate le librerie rilasciate da Microsoft. Microsoft gestisce le informazioni contenute in questa sezione.
- *Librerie di terze parti*: vengono elencate le librerie rilasciate e gestite da terze parti e piuttosto che da Microsoft. **Questa sezione è gestita unicamente dalla community di sviluppatori pubblica. Microsoft non gestisce questa sezione.**


## Tabella delle librerie di driver rilasciate da Microsoft


Nella tabella seguente vengono visualizzate le librerie rilasciate da Microsoft. Nella colonna **Librerie** vengono forniti i collegamenti che è possibile utilizzare per scaricare ciascuna libreria. Nella colonna **Versione** è elencata la versione minima consigliata per l'interazione con database SQL di Azure e Microsoft SQL Server.


| Piattaforma | Oper Sys | Librerie<br/>per il download | Versione<br/>del driver | Descrizione<br/>del driver | Ulteriori<br/>informazioni |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | Multipiattaforma (.NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4\.5 + | Provider SQL server per .NET Framework | . |
| PHP | Windows | [PHP for SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2\.0 + | Driver PHP per SQL Server | [Collegamento](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2\.0 + | Driver JDBC di tipo 4 che offre connettività di database tramite l’API JDBC standard | [Collegamento](http://msdn.microsoft.com/library/dn425070.aspx) |
| ODBC | Windows | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11\.0 + | Driver Microsoft ODBC per SQL Server | [Collegamento](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0 + | Driver Microsoft ODBC per SQL Server | . |
| ODBC | Redhat Linux | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0 + | Driver Microsoft ODBC per SQL Server | . |


### OLEDB per DB2 e SQL Server, per la progettazione DRDA


Il Provider OLEDB Microsoft per DB2 versione 5.0 (provider di dati) consente di creare applicazioni distribuite destinate ai database IBM DB2. Il provider di dati si avvale dell'architettura di accesso ai dati Microsoft SQL Server unitamente a un client di rete Microsoft per DB2 che svolge la funzione di richiedente dell’applicazione Distributed Relational Database Architecture (DRDA). Il provider di dati converte i comandi OLE DB COM (Component Object Model) di Microsoft e tipi di dati in formati di dati e i punti di codice del protocollo DRDA.


Per altre informazioni, vedere:


- [Provider Microsoft OLEDB per DB2 versione 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Provider Microsoft OLEDB per DB2 v4.0 per Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)


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

<!---HONumber=Oct15_HO3-->