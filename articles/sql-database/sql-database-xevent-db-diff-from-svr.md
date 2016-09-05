<properties
	pageTitle="Eventi estesi nel database SQL | Microsoft Azure"
	description="Vengono descritti gli eventi estesi (XEvent) in Azure SQL Database e come le sessioni di eventi sono leggermente diverse da sessioni di eventi in Microsoft SQL Server."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""
	tags=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="genemi"/>  


# Eventi estesi nel database SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

In questo argomento viene illustrato come l'implementazione di eventi estesi nel database SQL di Azure è leggermente diversa rispetto agli eventi estesi in Microsoft SQL Server.


- SQL Database V12 ha acquisito la funzionalità degli eventi estesi nella seconda metà del calendario 2015.
- SQL Server ha gli eventi estesi dal 2008.
- Il set di funzionalità degli eventi estesi nel database SQL è un subset affidabile delle funzionalità in SQL Server.


*XEvent* è un nome alternativo informale utilizzato talvolta per "eventi estesi" in blog e altri percorsi informali.


> [AZURE.NOTE] A partire da ottobre 2015, viene attivata la funzionalità della sessione eventi estesi in Azure SQL Database a livello di anteprima. La data di disponibilità generale (GA) non è ancora stabilita.
>
> La pagina degli [aggiornamenti dei servizi](https://azure.microsoft.com/updates/?service=sql-database) di Azure contiene post quando vengono fatti annunci GA.


Per altre informazioni sugli eventi estesi, per il database SQL di Azure e Microsoft SQL Server, vedere l'articolo:

- [Quick Start: Extended events in SQL Server](http://msdn.microsoft.com/library/mt733217.aspx) (Avvio rapido: eventi estesi in SQL Server)
- [Eventi estesi](http://msdn.microsoft.com/library/bb630282.aspx)


## Prerequisiti


In questo argomento si presuppone che si dispone già di una conoscenza di:


- [Servizio Azure SQL Database](https://azure.microsoft.com/services/sql-database/)


- [Eventi estesi](http://msdn.microsoft.com/library/bb630282.aspx) in Microsoft SQL Server.
 - La maggior parte della nostra documentazione sugli eventi estesi si applica sia a SQL Server che al database SQL.


Un’esposizione precedente a quanto riportato di seguito è utile quando si sceglie il file evento come [destinazione](#AzureXEventsTargets):


- [Servizio di Archiviazione di Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
 - [Utilizzo di Azure PowerShell con Archiviazione di Azure](../storage/storage-powershell-guide-full.md): fornisce informazioni complete su PowerShell e il servizio Archiviazione di Azure.


## Esempi di codice


Gli argomenti correlati forniscono due esempi di codice:


- [Codice di destinazione del buffer circolare per eventi estesi nel database SQL](sql-database-xevent-code-ring-buffer.md)
 - Breve script Transact-SQL semplice.
 - Nell'argomento dell'esempio di codice si evidenzia che, una volta completata la destinazione del buffer circolare, è necessario rilasciarne le risorse tramite l'esecuzione di un'istruzione `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` alter-drop. Successivamente è possibile aggiungere un'altra istanza del buffer circolare da `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Codice di destinazione del file evento per eventi estesi nel database SQL](sql-database-xevent-code-event-file.md)
 - Fase 1 è PowerShell per creare un contenitore di Archiviazione di Azure.
 - Fase 2 è Transact-SQL che utilizza il contenitore di Archiviazione di Azure.


## Differenze di Transact-SQL


- Quando si esegue il comando [CREATE EVENT SESSION](http://msdn.microsoft.com/library/bb677289.aspx) su SQL Server, si utilizza la clausola **ON SERVER**. Ma nel database SQL si utilizza invece la clausola **ON DATABASE**.


- La clausola **ON DATABASE** riguarda anche i comandi Transact-SQL [ALTER EVENT SESSION](http://msdn.microsoft.com/library/bb630368.aspx) e [DROP EVENT SESSION](http://msdn.microsoft.com/library/bb630257.aspx).


- Una procedura consigliata consiste nell'includere l'opzione della sessione eventi di **STARTUP\_STATE = ON** nelle istruzioni **CREATE EVENT SESSION** o **ALTER EVENT SESSION**.
 - Il valore **= ON** supporta un riavvio automatico dopo una riconfigurazione del database logico a causa di un failover.


## Nuove viste del catalogo


La funzionalità degli eventi estesi è supportata da diverse [viste del catalogo](http://msdn.microsoft.com/library/ms174365.aspx). Le viste del catalogo indicano i *metadati o le definizioni* di sessioni di eventi create dall'utente nel database corrente. Le viste non restituiscono informazioni sulle istanze delle sessioni di eventi attivi.


| Nome della<br/>vista del catalogo | Descrizione |
| :-- | :-- |
| **sys.database\_event\_session\_actions** | Restituisce una riga per ogni azione su ogni evento di una sessione di eventi. |
| **sys.database\_event\_session\_events** | Restituisce una riga per ogni evento in una sessione di eventi. |
| **sys.database\_event\_session\_fields** | Restituisce una riga per ogni colonna personalizzabile impostata in modo esplicito su eventi e destinazioni. |
| **sys.database\_event\_session\_targets** | Restituisce una riga per ogni destinazione di evento per una sessione di eventi. |
| **sys.database\_event\_sessions** | Restituisce una riga per ogni sessione di eventi nel database SQL del database. |


In Microsoft SQL Server, le viste del catalogo simili hanno nomi che includono *.server\_* anziché *.database\_*. Il modello di nome è simile a **sys.server\_event\_%**.


## [DMV](http://msdn.microsoft.com/library/ms188754.aspx) (nuove viste a gestione dinamica)


Il database SQL di Azure include [viste a gestione dinamica (DMV)](http://msdn.microsoft.com/library/bb677293.aspx) che supportano gli eventi estesi. Le DMV indicano le sessioni di eventi *attive*.


| Nome della DMV | Description |
| :-- | :-- |
| **sys.dm\_xe\_database\_session\_event\_actions** | Restituisce informazioni sulle azioni della sessione di eventi. |
| **sys.dm\_xe\_database\_session\_events** | Restituisce informazioni sugli eventi della sessione. |
| **sys.dm\_xe\_database\_session\_object\_columns** | Mostra i valori di configurazione per gli oggetti associati a una sessione. |
| **sys.dm\_xe\_database\_session\_targets** | Restituisce informazioni sulle destinazioni della sessione. |
| **sys.dm\_xe\_database\_sessions** | Restituisce una riga per ogni sessione di eventi con ambito nel database corrente. |


In Microsoft SQL Server, le viste del catalogo simili sono denominate senza la parte del nome *\_database*, ad esempio:


- **sys.dm\_xe\_sessions**, instead of name<br/>**sys.dm\_xe\_database\_sessions**.


### DMV comuni a entrambi


Per gli eventi estesi sono disponibili DMV aggiuntive comuni a Microsoft SQL Server e Azure SQL Database:


- **sys.dm\_xe\_map\_values**
- **sys.dm\_xe\_object\_columns**
- **sys.dm\_xe\_objects**
- **sys.dm\_xe\_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## Trovare gli eventi estesi, le azioni e le destinazioni disponibili


È possibile eseguire un semplice **SELECT** di SQL per ottenere un elenco di eventi, azioni e destinazioni disponibili.


```
SELECT
		o.object_type,
		p.name         AS [package_name],
		o.name         AS [db_object_name],
		o.description  AS [db_obj_description]
	FROM
		           sys.dm_xe_objects  AS o
		INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
	WHERE
		o.object_type in
			(
			'action',  'event',  'target'
			)
	ORDER BY
		o.object_type,
		p.name,
		o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## Destinazioni per le sessioni di eventi del database SQL


Di seguito si trovano le destinazioni che possono acquisire i risultati dalle sessioni di eventi nel database SQL:


- [Destinazione buffer circolare](http://msdn.microsoft.com/library/ff878182.aspx): contiene per un tempo breve i dati degli eventi nella memoria.
- [Destinazione contatore eventi](http://msdn.microsoft.com/library/ff878025.aspx): conta tutti gli eventi che si verificano durante una sessione di eventi estesi.
- [Destinazione file evento](http://msdn.microsoft.com/library/ff878115.aspx): scrive buffer completi in un contenitore di Archiviazione di Azure.


L’API [Tracciamento eventi per Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) non è disponibile per gli eventi estesi nel database SQL.


## Restrizioni


Esistono un paio di differenze relative alla sicurezza adatte all'ambiente cloud del database SQL:


- Gli eventi estesi si basano sul modello di isolamento single-tenant. Una sessione di eventi in un database non può accedere a dati o eventi da un altro database.

- Non è possibile eseguire un’istruzione **CREATE EVENT SESSION** dell'istruzione del database **master**.


## Modello di autorizzazione


È necessario disporre dell’autorizzazione **Controllo** nel database per emettere un’istruzione **CREATE EVENT SESSION**. Il proprietario del database (dbo) dispone dell’autorizzazione **controllo**.


### Autorizzazioni del contenitore di archiviazione


Il token della firma di accesso condiviso generato per il contenitore di Archiviazione di Azure deve specificare **rwl** per le autorizzazioni. Questo valore **rwl** fornisce le autorizzazioni seguenti:


- Lettura
- Scrittura
- Elenco


## Considerazioni sulle prestazioni


Esistono scenari in cui un uso intensivo di eventi estesi può accumulare più memoria attiva di quanto è adatto per l'intero sistema. Pertanto il sistema di Azure SQL Database imposta e regola in modo dinamico i limiti sulla quantità di memoria attiva che può essere accumulata da una sessione di eventi. Molti fattori vengono utilizzati nel calcolo dinamico.


Se si riceve un messaggio di errore che indica che è stato applicato un massimo di memoria, alcune azioni correttive da eseguire sono:


- Eseguire meno sessioni di eventi simultanee.


- Tramite le istruzioni **CREATE** e **ALTER** per le sessioni di eventi, ridurre la quantità di memoria specificata nella clausola **MAX\_MEMORY**.


### Latenza di rete


La destinazione del **file evento** potrebbe subire una latenza di rete o errori durante il mantenimento dei dati nei BLOB di archiviazione di Azure . Altri eventi nel database SQL potrebbero subire un ritardo mentre rimangono in attesa del completamento della comunicazione di rete. Questo ritardo può rallentare il carico di lavoro.

- Per ridurre questo rischio delle prestazioni, evitare di impostare l’opzione **EVENT\_RETENTION\_MODE** in **NO\_EVENT\_LOSS** nelle definizioni della sessione di eventi.


## Collegamenti correlati


- [Uso di Azure PowerShell con Archiviazione di Azure](../storage/storage-powershell-guide-full.md)
- [Cmdlet di Archiviazione di Azure](http://msdn.microsoft.com/library/dn806401.aspx)


- [Utilizzo di Azure PowerShell con Archiviazione di Azure](../storage/storage-powershell-guide-full.md): fornisce informazioni complete su PowerShell e il servizio Archiviazione di Azure.
- [Come usare l'archiviazione BLOB da .NET](../storage/storage-dotnet-how-to-use-blobs.md)


- [CREARE CREDENZIALI (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREARE LA SESSIONE DI EVENTI (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Post del blog di Jonathan Kehayias sugli eventi estesi in Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


Altri argomenti con esempi di codice per gli eventi estesi sono disponibili ai collegamenti seguenti. È comunque necessario controllare regolarmente qualsiasi esempio per verificare se è destinato a Microsoft SQL Server o al database SQL di Azure. È quindi possibile decidere se sono necessarie alcune modifiche per eseguire l'esempio.


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->  

<!---HONumber=AcomDC_0824_2016-->