<properties 
	pageTitle="Sviluppo client ed esempi di codice di avvio rapido per il database SQL" 
	description="Questo argomento elenca il numero di versione minima per ogni driver che i programmi client possono usare per connettersi al database SQL di Azure o a Microsoft SQL Server. È disponibile un collegamento per le informazioni sulla versione relative ai driver rilasciati dalla community anziché da Microsoft."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="genemi"/>


# Sviluppo client ed esempi di codice di avvio rapido per il database SQL


Questo argomento include collegamenti a esempi di codice di avvio rapido che è possibile usare per connettersi al database SQL di Azure. Sono riportati vari tipi di esempi:


- Esempi in diversi linguaggi di programmazione.
- Esempi per Windows e Linux, a seconda del sistema operativo in cui verrà eseguito il programma client.
- Esempi brevi di codice di avvio rapido ed esempi più lunghi che gestiscono gli errori temporanei con una logica di retry automatizzata.
- Esempi di codice che convertono i set di risultati relazionali in un formato orientato a oggetti.


> [AZURE.NOTE]Alla data del 19 maggio 2015 è in corso la preparazione di esempi di codice per più linguaggi. I collegamenti a tali esempi verranno aggiunti in questo argomento.


## Client su Linux


Questa sezione fornisce collegamenti ad argomenti con esempi di codice per i programmi client eseguiti su Linux.


| Linguaggio | Breve | Retry | Relazionale a oggetti |
| :-- | :-- | :-- | :-- |
| Node.js | [Node.JS](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [Python](sql-database-develop-python-simple-unbutu-linux.md) | . | . |


## Client su Windows


Questa sezione fornisce collegamenti ad argomenti con esempi di codice per programmi client eseguiti su Windows.


| Linguaggio | Breve | Retry | Relazionale a oggetti |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)<br/><br/>[ADO.NET con Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx) | [ADO.NET Entity Framework](http://msdn.microsoft.com/library/azure/ff951633.aspx) |
| C++ | [Driver ODBC](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. Insert, Transaction, Select.](sql-database-develop-java-simple-windows.md)<br/><br/>[Java. Eclipse](sql-data-java-how-to-use-sql-database.md)<br/><br/>[Java. JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx) | . | . |
| Node.js | [Node.JS](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [PHP](sql-database-develop-php-simple-windows.md) | . | . |
| Python | [Python](sql-database-develop-python-simple-windows.md) | . | . |


## Vedere anche


- [Sviluppo per il database SQL di Azure: procedure](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Connessione al database SQL: collegamenti, procedure consigliate e linee guida per la progettazione](sql-database-connect-central-recommendations.md)
- [Creare il primo database SQL di Azure](sql-database-get-started.md)
- [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md)

<!---HONumber=58-->