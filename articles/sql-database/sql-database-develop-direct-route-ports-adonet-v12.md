<properties 
	pageTitle="Porte per 1433 per il database SQL | Microsoft Azure"
	description="Le connessioni client da ADO.NET al database SQL V12 di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="drivers"
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/17/2016"
	ms.author="annemill"/>


# Porte superiori a 1433 per ADO.NET 4.5, e database SQL V12


In questo argomento vengono descritte le modifiche apportate dal database SQL V12 di Azure al comportamento di connessione dei client che utilizzano ADO.NET 4.5 o versione successiva.


## V11 del database SQL: porta 1433


Quando il programma client utilizza ADO.NET 4.5 per connettersi ed eseguire una query con la versione V11 del database SQL, la sequenza interna è la seguente:


1. ADO.NET tenta di connettersi al database SQL.

2. ADO.NET utilizza la porta 1433 per chiamare un modulo middleware e il middleware si connette al database SQL.

3. Il database SQL invia la risposta al middleware che la inoltra ad ADO.NET sulla porta 1433.


**Terminologia:** viene descritta la precedente sequenza affermando che ADO.NET interagisce con il database SQL tramite il *percorso proxy*. Se nessun middleware è stato interessato è possibile affermare che è stato utilizzato il *percorso diretto*.


## V12 del database SQL: all'esterno rispetto all'interno


Per le connessioni a V12, è necessario chiedere se il programma client viene eseguito *all'esterno* o *all'interno* del limite del cloud di Azure. Nelle sottosezioni vengono illustrati due scenari comuni.


#### *Esterno:* il client è in esecuzione nel computer desktop


La porta 1433 è l'unica porta da aprire nel computer desktop che ospita l'applicazione client del database SQL.


#### *All'interno:* il client è in esecuzione in Azure


Quando il client viene eseguito all'interno del limite del cloud di Azure, viene utilizzato ciò che possiamo definire un *percorso diretto* per interagire con il server del database SQL. Una volta stabilita una connessione, ulteriori interazioni tra il client e il database non coinvolgono alcun proxy middleware.


La sequenza è la seguente:


1. ADO.NET 4.5 (o versione successiva) avvia una breve interazione con il cloud di Azure e riceve un numero di porta identificato in modo dinamico.
 - Il numero di porta identificato in modo dinamico è compreso nell'intervallo tra 11000-11999 o 14000-14999.

2. ADO.NET quindi si connette direttamente al server del database SQL, senza alcun middleware intermedio.

3. Le query vengono inviate direttamente al database e i risultati vengono restituiti direttamente al client.


Assicurarsi che l'intervallo di porte 11000-11999 e 14000-14999 sul computer client di Azure venga reso disponibile per le interazioni del client ADO.NET 4.5 con il database SQL V12.

- In particolare, le porte nell'intervallo devono essere libere da eventuali altri blocchi in uscita.

- Nella macchina virtuale di Azure, il **Windows Firewall con sicurezza avanzata** controlla le impostazioni della porta.
 - È possibile utilizzare l’[interfaccia utente del firewall](http://msdn.microsoft.com/library/cc646023.aspx) per aggiungere una regola per cui si specifica il protocollo**TCP** con un intervallo di porte con la sintassi **11000-11999**.


## Chiarimenti sulla versione


In questa sezione vengono spiegati i moniker che fanno riferimento a versioni precedenti del prodotto. Sono inoltre indicate alcune associazioni di versioni tra prodotti.


#### ADO.NET


- ADO.NET 4.0 supporta il protocollo TDS 7.3, ma non 7.4.
- ADO.NET 4.5 e versioni successive supportano il protocollo TDS 7.4.


#### Database SQL V11 e V12


In questo argomento vengono evidenziate le differenze di connessione del client tra il database SQL V11 e V12.


*Nota:* l’istruzione Transact-SQL`SELECT @@version;` restituisce un valore che inizia con un numero, ad esempio "11." o "12.", corrispondenti ai nomi di versione V11 e V12 per il database SQL.


## Collegamenti correlati


- ADO.NET 4.6 è stato rilasciato il 20 luglio 2015. È disponibile un annuncio di blog del team .NET [qui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4.5 è stato rilasciato il 15 agosto 2012. [Qui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx) è disponibile un annuncio di blog del team .NET.
 - È disponibile un post di blog su ADO.NET 4.5.1 [qui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Elenco versioni del protocollo TDS](http://www.freetds.org/userguide/tdshistory.htm)


- [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)


- [Firewall del database SQL di Azure](sql-database-firewall-configure.md)


- [Procedura: configurare le impostazioni del firewall su Database SQL](sql-database-configure-firewall-settings.md)

<!---HONumber=AcomDC_0817_2016-->