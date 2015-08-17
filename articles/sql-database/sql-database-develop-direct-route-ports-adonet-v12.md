<properties 
	pageTitle="Porte superiori a 1433 per ADO.NET 4.5, ODBC 11 e database SQL V12 | Microsoft Azure"
	description="Le connessioni client al database SQL V12 di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2015" 
	ms.author="genemi"/>


# Porte superiori a 1433 per ADO.NET 4.5, ODBC 11 e database SQL V12


In questo argomento vengono descritte le modifiche apportate dal database SQL V12 di Azure al comportamento di connessione dei client che utilizzano ADO.NET 4.5 o versione successiva.


## Chiarimenti sulla versione


#### ADO.NET


- ADO.NET 4.0 supporta il protocollo TDS 7.3, ma non 7.4.
- ADO.NET 4.5 e versioni successive supportano il protocollo TDS 7.4.
- Internamente, ADO.NET 4.5 utilizza ODBC 11.
 - Le informazioni applicabili a ADO.NET 4.5 si applicano anche a ODBC 11.


#### Database SQL V11 e V12


In questo argomento vengono evidenziate le differenze di connessione del client tra il database SQL V11 e V12.


*Nota:* l’istruzione Transact-SQL`SELECT @@version;` restituisce un valore che inizia con un numero, ad esempio '11.' o '12'., corrispondenti ai nomi di versione V11 e V12 per il database SQL.


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


#### *All'interno:* il client è in esecuzione in una macchina virtuale di Azure


Quando il client viene eseguito all'interno del limite del cloud di Azure, viene utilizzato ciò che possiamo definire un *percorso diretto* per interagire con il server del database SQL. Una volta stabilita una connessione, ulteriori interazioni tra il client e il database non coinvolgono alcun proxy middleware.


La sequenza è la seguente:


1. ADO.NET 4.5 (o versione successiva) avvia una breve interazione con il cloud di Azure e riceve un numero di porta identificato in modo dinamico.
 - Il numero di porta identificato in modo dinamico è compreso nell'intervallo tra 11000 e 11999.

2. ADO.NET quindi si connette direttamente al server del database SQL, senza alcun middleware intermedio.

3. Le query vengono inviate direttamente al database e i risultati vengono restituiti direttamente al client.


Assicurarsi che l'intervallo di porte 11000 - 11999 sul computer client di Azure venga reso disponibile per le interazioni del client ADO.NET 4.5 con il database SQL V12.

- In particolare, le porte nell'intervallo devono essere libere da eventuali altri blocchi in uscita.
- Windows Firewall nella VM di Azure consente di controllare le impostazioni della porta.


## Logica di ripetizione tentativi implicita contenuta nel percorso proxy


In un ambiente di produzione, è consigliabile che i client che si connettono al database SQL V11 o V12 di Azure implementino nel loro codice una logica di ripetizione tentativi. Il codice può essere personalizzato oppure può sfruttare un’API come la libreria Enterprise.


Il percorso proxy descritto in precedenza in questo argomento riguarda la questione della logica di ripetizione tentativi:


- Nella versione V11, il modulo middleware che fungeva da proxy forniva anche un modesto livello di logica di ripetizione tentativi per gestire correttamente alcuni errori temporanei.

- In V12, il proxy non fornisce alcuna logica di ripetizione tentativi.


In entrambi gli scenari è consigliabile che i client implementino la logica di ripetizione tentativi nel proprio codice. Probabilmente la necessità di logica di ripetizione tentativi nel client è aumentata con il nuovo percorso proxy che non fornisce alcuna logica di questo tipo.


Per esempi di codice che illustrino la logica di ripetizione tentativi, vedere: [Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md).


## Collegamenti correlati


- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)

- Considerazioni sulla logica di ripetizione tentativi:[ sezione "Il gateway non fornisce più la logica di riesecuzione in V12" nell'argomento "Connessione al database SQL: collegamenti, procedure consigliate e linee guida per la progettazione"](sql-database-connect-central-recommendations.md#gatewaynoretry)

- ADO.NET 4.6 è stato rilasciato il 20 luglio 2015. [Qui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx) è disponibile un annuncio di blog del team .NET.

- ADO.NET 4.5 è stato rilasciato il 15 agosto 2012. [Qui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx) è disponibile un annuncio di blog del team .NET.
 - [Qui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx) è disponibile un post di blog su ADO.NET 4.5.1.

- [Elenco versioni del protocollo TDS](http://www.freetds.org/userguide/tdshistory.htm)

<!---HONumber=August15_HO6-->