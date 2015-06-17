<properties 
	pageTitle="Creazione e gestione di processi elastici di database" 
	description="Informazioni sulla gestione e la creazione di un processo elastico di database." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh"/>

# Creazione e gestione di processi elastici di database

I **pool elastici di database** offrono un modello prevedibile per la distribuzione di un elevato numero di database. È possibile impostare unità di velocità effettiva (DTU, Database Throughput Unit) per ciascun database a un costo prefissato. In questi database la gestione di oggetti comuni può essere eseguita in modo più semplice usando **processi elastici di database**. Il servizio consente di eseguire script T-SQL su tutti i database del pool in una singola operazione. Ad esempio, è possibile impostare criteri su ogni database in modo da consentire l'accesso ai dati sensibili a una sola persona in possesso delle credenziali corrette.

## Prerequisiti

* Una sottoscrizione di Azure. Per una versione di valutazione gratuita, vedere [Versione di valutazione gratuita di un mese](http://azure.microsoft.com/pricing/free-trial/).
* Un pool elastico di database. Vedere l'articolo relativo ai [pool elastici di database](sql-database-elastic-pool.md)
* Installazione dei componenti del servizio relativo ai processi elastici di database. Vedere l'articolo sull'[installazione del servizio relativo ai processi elastici di database](sql-database-elastic-jobs-service-installation.md).

## Creazione di processi

1. Nel pannello del pool di processi elastici di database fare clic su **Crea processo**.
2. Digitare il nome e la password dell'amministratore del database (creati in fase di installazione).
2. Nel pannello **Crea processo** digitare il nome del processo.
3. Incollare o digitare nello script T-SQL.
4. Fare clic su **Salva** e quindi su **Esegui**.

	![Denominare il processo, digitare o incollare il nome nel codice e fare clic su Esegui][1]

## Eseguire processi idempotenti

Quando si esegue uno script su un insieme di database, è necessario assicurarsi che tale script sia idempotente. In altri termini, è necessario che lo script possa essere eseguito più volte anche se in precedenza aveva avuto esito negativo e non era stato completato. Se ad esempio uno script non riesce, il processo verrà ripetuto automaticamente fino a quando non avrà esito positivo (all'interno di limiti stabiliti, poiché la logica di ripetizione potrà eventualmente interrompere l'esecuzione di nuovi tentativi). Il metodo per eseguire questa operazione consiste nell'uso della clausola "IF EXISTS" e nell'eliminazione di qualsiasi istanza trovata prima della creazione di un nuovo oggetto. Di seguito è riportato un esempio:

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID 
    ON Purchasing.ProductVendor (VendorID);

In alternativa, usare una clausola "IF NOT EXISTS" prima di creare una nuova istanza:

	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable') 
	BEGIN 
	 CREATE TABLE TestTable( 
	  TestTableId INT PRIMARY KEY IDENTITY, 
	  InsertionTime DATETIME2 
	 ); 
	END 
	GO 
	
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime()); 
	GO 

Questo script aggiorna quindi la tabella creata in precedenza.

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation') 
	BEGIN 
	
	ALTER TABLE TestTable 
	
	ADD AdditionalInformation NVARCHAR(400); 
	END 
	GO 
	
	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test'); 
	GO 


## Verifica dello stato del processo

Dopo aver iniziato un processo, è possibile controllarne lo stato di avanzamento.

1. Nella pagina del pool elastico di database fare clic su **Gestione processi**.

	![Fare clic su "Gestione processi"][2]

2. Fare clic sul nome (a) di un processo. Lo **STATO** può essere "Completato" o "Non riuscito". I dettagli del processo vengono visualizzati (b) insieme alla data e all'ora di creazione ed esecuzione. L'elenco (c) sottostante mostra lo stato di avanzamento dello script su ogni database del pool, fornendo dettagli relativi alla data e all'ora.

	![Controllo di un processo completato][3]


## Controllo dei processi non riusciti

Se un processo ha esito negativo, è disponibile un log dell'esecuzione. Fare clic sul nome del processo non riuscito per visualizzarne i dettagli.

![Controllo di un processo non riuscito][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/provide-creds.png

<!---HONumber=58--> 