<properties 
	pageTitle="Esempi di codice di avvio rapido del client per il database SQL | Microsoft Azure" 
	description="Fornisce esempi di codice e i driver per Node.js su Linux, Python su Mac OS, Java e Windows e altro ancora per i client di Database SQL di Azure."
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
	ms.date="07/21/2015" 
	ms.author="genemi"/>


# Esempi di codice di avvio rapido del client per il database SQL


Questo argomento include collegamenti a esempi di codice di avvio rapido che è possibile usare per connettersi al database SQL di Azure.


- Esempi brevi connessione e query.
- Gli esempi di riesecuzione connettono e eseguono la query, ma riprovano automaticamente se si riscontrano errori classificati come [*errori temporanei*](http://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors)(ad esempio, un timeout di connessione).


Sono riportati vari tipi di esempi:


- Esempi in diversi linguaggi di programmazione.
- Esempi per Windows, Linux e Mac OS, a seconda del sistema operativo in cui è possibile eseguire il programma client.
- Collegamenti per i download dei driver di connessione necessari.
- Esempi brevi di codice di avvio rapido.
- Esempi più dettagliati che contengono la gestione di errori temporanei sotto forma di logica di riesecuzione automatica.
- Esempi di codice che convertono i set di risultati relazionali in un formato orientato a oggetti.


> [AZURE.NOTE]Stiamo preparando esempi di codice in più lingue e i relativi collegamenti verranno aggiunti a questo argomento.


## Client su Linux


Questa sezione fornisce collegamenti ad argomenti con esempi di codice per i programmi client eseguiti su Linux.


| Linguaggio | Breve esempio | Esempio di nuovo tentativo | Relazionale a oggetti |
| :-- | :-- | :-- | :-- |
| Node.js | [Tedious](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-unbutu-linux.md) | . | . |
| Ruby | [FreeTDS, TinyTDS](sql-database-develop-ruby-simple-linux.md) | . | . |


## Client su Mac OS


Questa sezione fornisce collegamenti ad argomenti con esempi di codice per i programmi client eseguiti su Mac OS.


| Linguaggio | Breve esempio | Esempio di nuovo tentativo | Relazionale a oggetti |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |
| Ruby | [Homebrew<br/>FreeTDS, TinyTDS](sql-database-develop-ruby-simple-mac-osx.md) | . | . |


## Client su Windows


Questa sezione fornisce collegamenti ad argomenti con esempi di codice per programmi client eseguiti su Windows.


| Linguaggio | Breve esempio | Esempio di nuovo tentativo | Relazionale a oggetti |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)<br/><br/>[ADO.NET con Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx) | [ADO.NET Entity Framework](http://msdn.microsoft.com/library/azure/ff951633.aspx) |
| C++ | [Driver ODBC](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. Insert, Transaction, Select.](sql-database-develop-java-simple-windows.md)<br/><br/>[Java. Eclipse](sql-data-java-how-to-use-sql-database.md)<br/><br/>[Java. JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [ODBC](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## Vedere anche


- [Download di SDK e strumenti per numerosi linguaggi e piattaforme](http://azure.microsoft.com/downloads/#cmd-line-tools)
- [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md)
- [Elenco di codici numerici per gli errori temporanei](http://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors)<br/>&nbsp;
- [Sviluppo per il database SQL di Azure: procedure](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Connessione al database SQL: collegamenti, procedure consigliate e linee guida per la progettazione](sql-database-connect-central-recommendations.md)
- [Creare il primo database SQL di Azure](sql-database-get-started.md)

<!---HONumber=July15_HO4-->