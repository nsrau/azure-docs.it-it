<properties 
	pageTitle="Livelli di compatibilità nel database SQL | Microsoft Azure" 
	description="Illustra come configurare il livello di compatibilità per il database SQL di Azure e le funzionalità interessate."
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
	ms.date="09/21/2015" 
	ms.author="genemi"/>


# Livelli di compatibilità nel database SQL


Per il database SQL di Azure, questo argomento illustra le opzioni relative alle funzionalità disponibili tramite l'istruzione **ALTER DATABASE** di Transact-SQL. Il concetto relativo al livello di compatibilità è stato progettato per ridurre qualsiasi rischio di aggiornamento.


La maggior parte delle funzionalità viene attivata o disattivata dal livello di compatibilità ed è inclusa in Query Optimizer. Microsoft rende l'attivazione di una nuova funzionalità di Query Optimizer dipendente dal livello di compatibilità nelle situazioni seguenti:


- Il contesto aggiornato modifica la semantica di una funzionalità precedente.
- È molto probabile che l'ottimizzazione della query danneggi le prestazioni di determinate query SQL non comuni ma legittime.


Se le prestazioni della query risultano ridotte dopo un aggiornamento della versione eseguito nel server o nel database di database SQL di Azure, sarà possibile ridurre il livello di compatibilità. L'impostazione inferiore può disattivare un set ristretto di miglioramenti di Query Optimizer, inclusi probabilmente i miglioramenti sfavorevoli per la query.


## Funzionamento del livello di compatibilità


Microsoft SQL Server include da molti anni livelli di compatibilità configurabili. A partire dalla versione V12, il database SQL di Azure offre i livelli di compatibilità. L'attivazione di alcune nuove funzionalità è controllata dall'impostazione relativa al livello di compatibilità in ogni database SQL. Le possibili impostazioni includono le seguenti:


- 110: l'impostazione più bassa possibile e quindi l'impostazione che esclude la maggior parte delle nuove funzionalità.
- 120: corrisponde vagamente al database SQL V11, ovvero `SELECT @@version;` restituisce **11.**0.0.0.
 - La versione V11 del database SQL è definita anche 'SAWA V2'.
 - L'impostazione 120 è il valore massimo in Microsoft SQL Server 2014.
- 130: corrisponde vagamente a V12, ovvero `SELECT @@version;` restituisce **12.**0.0.0.
 - L'impostazione 130 è il valore massimo in Microsoft SQL Server 2016.


Ad esempio, un'impostazione pari a 120 indica che il database può accedere al sottoinsieme di funzionalità che vengono attivate al livello 120, *oltre* alle funzionalità che vengono attivate a eventuali impostazioni inferiori, ad esempio 110. Se impostato su 120, il database non potrà accedere alle funzionalità di livello 130.


## Leggere o impostare il livello di compatibilità


### Leggere il livello di compatibilità


```
SELECT d.name, d.compatibility_level
	FROM sys.databases AS d
	WHERE d.name = db_name();
```


### Configurare il livello di compatibilità


```
ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL = 130;
```


Per altre informazioni, vedere:


- [Livello di compatibilità ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/bb510680.aspx)
- [sys.databases (Transact-SQL)](http://msdn.microsoft.com/library/ms178534.aspx)


## Tabelle e indici columnstore in memoria


La maggior parte delle funzionalità relative al livello di compatibilità attivate ai livelli 120 e 130 è correlata alle tabelle e agli indici disponibili *in memoria*. Gli elementi importanti includono quindi:


- Tabelle con ottimizzazione per la memoria
- Indici Columnstore


Se sono coinvolti elementi in memoria, il livello di compatibilità 130 offre i vantaggi seguenti:


- Viene attivata la capacità di Query Optimizer di elaborare più query in modalità *batch*.
 - Se l'operazione interessa un numero elevato di righe, la modalità batch è più veloce della modalità *riga*.
- Aggiunta dell'operatore **SORT**.
- Aggiunta delle aggregazioni finestra.


Per altre informazioni sulle tabelle in memoria e sugli indici columnstore, vedere:


- [Tabelle con ottimizzazione per la memoria](http://msdn.microsoft.com/library/dn133165.aspx)
- [CREATE CLUSTERED COLUMNSTORE INDEX (Transact-SQL)](http://msdn.microsoft.com/library/dn511016.aspx)
- [Descrizione degli indici columnstore](http://msdn.microsoft.com/library/gg492088.aspx)


## Funzionalità generali che richiedono almeno il livello 130


Il modello di stima della cardinalità genera e archivia statistiche relative al numero approssimativo di righe in una tabella. Molte parti di Query Optimizer usano le informazioni sulla cardinalità.


Gli algoritmi usato dal modello di stima della cardinalità sono stati migliorati. I miglioramenti comportano modifiche e le modifiche possono provocare la regressione di casi specializzati non comuni.


| 130 è il livello<br/>minimo<br/>necessario | Area di query,<br/>Generale | Dettagli di<br/>miglioramento<br/>del piano di query |
| :-- | :-- | :-- |
| 130 | Modello di stima della cardinalità | Miglioramenti al modello di stima della cardinalità rispetto al modello di stima della cardinalità precedente a livello 120.<br/><br/>È possibile che il piano di query includa quanto segue: **CardinalityEstimationModelVersion="130"**<br/><br/>**Il flag di traccia** [**9481**](http://www.sqlservergeeks.com/sql-server-2014-trace-flags-9481/) può essere attivato per usare il modello di stima della cardinalità di livello 120 quando il database usa il livello 130.<br/><br/>**Il flag di traccia** [**4199**](http://support.microsoft.com/kb/974006), può essere configurato su Off per rifiutare esplicitamente gli hotfix per Query Optimizer quando il database usa il livello di compatibilità 130. Il flag è applicabile solo agli hotfix implementati dopo il passaggio completo del livello 130 dall'anteprima alla disponibilità generale. Per informazioni dettagliate, vedere:<br/><br/>● [DBCC TRACEON](http://msdn.microsoft.com/library/ms187329.aspx) |
| 130 | Piani di query paralleli per tabelle in memoria | Le query possono usare più thread e possono essere eseguite in parallelo quando sono relative a una tabella in memoria, ovvero una tabella creata con la clausola **MEMORY\_OPTIMIZED = YES**. Il parallelismo può velocizzare l'esecuzione delle query.<br/><br/>Questo miglioramento è supportato per istruzioni Transact-SQL normali e stored procedure utente. Non è tuttavia supportato per stored procedure native compilate in un file DLL. |


## Funzionalità di indice Columnstore che richiedono almeno il livello 130


Al livello di compatibilità 130, i miglioramenti di Query Optimizer possono avere come risultato un nuovo piano di query. Per i piani modificati che interessano un indice columnstore, la modifica comporta in genere una delle conseguenze seguenti:


- Una riduzione delle circostanze in cui i dati devono essere copiati per un'ulteriore operazione secondaria.
- Un passaggio dall'elaborazione in modalità *riga* all'elaborazione in modalità ***batch*** per operazioni quali l'ordinamento.


Nella maggior parte dei casi le modifiche del piano migliorano le prestazioni della query, interessando gli elementi seguenti:


- Inserimenti paralleli in un indice columnstore.
 - Il livello 130 non fornisce l'analisi parallela di indici non cluster.
- Incremento dell'uso del database **tempdb**.


| 130 è il livello<br/>minimo<br/>necessario | Area di query,<br/>indice Columnstore | Dettagli di<br/>miglioramento<br/>del piano di query |
| :-- | :-- | :-- |
| 130 | Query funzione | Le prestazioni migliorano grazie al passaggio alla modalità batch nei casi seguenti:<br/><br/>• L'operazione prevede l'ordinamento.<br/><br/>• Aggregazioni con *più* funzioni distinte<br/>(una funzione da ogni voce dell'elenco seguente):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT** *o* **COUNT\_BIG**<br/>&nbsp;&nbsp;&nbsp;▫ **AVG** *o* **SUM**<br/>&nbsp;&nbsp;&nbsp;▫ **CHECKSUM\_AGG**<br/>&nbsp;&nbsp;&nbsp;▫ **STDEV** *o* **STDEVP**<br/><br/>• Funzioni finestra aggregate<br/>(descritte [qui in MSDN](http://msdn.microsoft.com/library/ms189461.aspx) e [qui da Kathi Kellenberger](http://www.bidn.com/blogs/KathiKellenberger/sql-server/4397/what-is-a-window-aggregate-function)):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT**, **COUNT\_BIG**, **SUM**, **AVG**, **MIN**, **MAX**, **CLR**<br/><br/>• Aggregazioni finestra [definite dall'utente](http://msdn.microsoft.com/library/ms131057.aspx):<br/>&nbsp;&nbsp;&nbsp;▫ [**CHECKSUM\_AGG**](http://msdn.microsoft.com/library/ms188920.aspx), [**STDEV**](http://msdn.microsoft.com/library/ms190474.aspx), [**STDEVP**](http://msdn.microsoft.com/library/ms176080.aspx), [**VAR**](http://msdn.microsoft.com/library/ms186290.aspx), [**VARP**](http://msdn.microsoft.com/library/ms188735.aspx), [**GROUPING**](http://msdn.microsoft.com/library/ms178544.aspx)<br/><br/>• Funzioni finestra analitiche aggregate:<br/>&nbsp;&nbsp;&nbsp;▫ [**LAG**](http://msdn.microsoft.com/library/hh231256.aspx), [**LEAD**](http://msdn.microsoft.com/library/hh213125.aspx), [**FIRST\_VALUE**](http://msdn.microsoft.com/library/hh213018.aspx), [**LAST\_VALUE**](http://msdn.microsoft.com/library/hh231517.aspx), [**PERCENTILE\_CONT**](http://msdn.microsoft.com/library/hh231473.aspx), [**PERCENTILE\_DISC**](http://msdn.microsoft.com/library/hh231327.aspx), [**CUME\_DIST**](http://msdn.microsoft.com/library/hh231078.aspx), [**PERCENT\_RANK**](http://msdn.microsoft.com/library/hh213573.aspx) |
| 130 | Piano di query seriale a thread singolo | Una query eseguita su un singolo thread può essere eseguita in modalità batch, rendendo più veloci le prestazioni della query.<br/><br/>È possibile progettare un piano di query come a thread singolo oppure eseguire una query in **MAXDOP 1**. |
| 130 | Inserimento parallelo | Il piano di query può eseguire alcuni inserimenti in parallelo.<br/<br/>L'[esempio](#ExampleQueryParallelCciByCompatLevel) più avanti in questo argomento illustra il parallelismo. |
| 130 | Operatore Anti-semi join | Questo operatore può essere ora eseguito in modalità batch. |


## Funzionalità generali che richiedono almeno il livello 120


| 120 è il livello<br/>minimo<br/>necessario | Area di query,<br/>Generale | Dettagli di<br/>miglioramento<br/>del piano di query |
| :-- | :-- | :-- |
| 120 | [Modifica di tabelle MEMORY\_OPTIMIZED](http://msdn.microsoft.com/library/dn269114.aspx) | Consente di eseguire operazioni **ALTER TABLE** di Transact-SQL su tabelle con **MEMORY\_OPTIMIZED = YES**.<br/><br/>L'esecuzione dell'applicazione database può continuare, ma le operazioni che accedono alla tabella sono bloccate fino al completamento di **ALTER TABLE**. |
| 120 | [Creazione e gestione dell'archiviazione per gli oggetti con ottimizzazione per la memoria](http://msdn.microsoft.com/library/dn133174.aspx) | Il motore OLTP in memoria è integrato in SQL Server. Ciò consente di includere le tabelle **MEMORY\_OPTIMIZED** e le tabelle tradizionali basate su disco nello stesso database. |
| 120 | [Supporto di Transact-SQL per OLTP in memoria](http://msdn.microsoft.com/library/dn133180.aspx) | Alcuni comandi di Transact-SQL sono stati migliorati per supportare l'elaborazione delle transazioni online (OLTP, Online Transaction Processing) in memoria.<br/><br/>Un esempio è costituito dalla nuova parola chiave **NATIVE\_COMPILATION** nel comando [CREATE PROCEDURE](http://msdn.microsoft.com/library/ms187926.aspx). |
| 120 | Modello di stima della cardinalità | Miglioramenti al modello di stima della cardinalità rispetto al modello di stima della cardinalità precedente a livello 110.<br/><br/>È possibile che il piano di query includa quanto segue: **CardinalityEstimationModelVersion="120"**<br/><br/>Per informazioni dettagliate sul modo in cui il **flag di traccia 4199** interagisce con il valore del livello di compatibilità, vedere l'articolo [KB 974006](http://support.microsoft.com/kb/974006).|


## Funzionalità di indice Columnstore che richiedono almeno il livello 120


Questa sezione illustra le [funzionalità dell'indicizzazione columnstore](http://msdn.microsoft.com/library/dn934994.aspx) attivate a livello di compatibilità 120 o superiore.


| 120 è il livello<br/>minimo<br/>di compatibilità | Nome della funzionalità<br/>dell'indice Columnstore | Descrizione della funzionalità<br/>dell'indice Columnstore |
| :-- | :-- | :-- |
| 120 | Livello di isolamento dello snapshot e<br/><br/>livello RCSI (Read Committed Snapshot isolation). | Quando il piano di query interessa un indice columnstore, il livello di isolamento dello snapshot e il livello RCSI impediscono l'inclusione nei risultati dei dati di transazioni parzialmente complete, senza dovere ricorrere a blocchi eccessivi. |
| 120 | Deframmentazione dell'indice | Le righe eliminate vengono rimosse senza una ricompilazione esplicita dell'indice.<br/><br/>**ALTER INDEX... REORGANIZE** rimuove le righe eliminate dall'indice columnstore mentre l'indice e la tabella rimangono operativi online. |
| 120 | Accessibile su una [replica secondaria leggibile](http://msdn.microsoft.com/library/ff878253.aspx) AlwaysOn | È possibile migliorare le prestazioni per le analisi operative tramite l'offload delle query analitiche a una replica secondaria AlwaysOn. |
| 120 | Impostazione aggregata<br/>durante la fase di scansione di tabella delle funzioni aggregate | Migliora le prestazioni tramite il completamento dei calcoli provvisorio nelle fasi iniziali del piano di query, in modo che sia necessario copiare una quantità minore di dati nelle fasi successive.<br/><br/>Applicabile a **MIN**, **MAX**, **SUM**, **COUNT**, **AVG**.<br/><br/>Applicabile solo quando il tipo di dati è pari al massimo a otto byte, ma non per le stringhe. |
| 120 | Impostazione di clausole **WHERE** basate su stringhe | L'ottimizzazione dell'impostazione dei predicati può velocizzare le query che confrontano dati stringa di tipo &#x5b;var&#x5d;char o n&#x5b;var&#x5d;char. Questa ottimizzazione:<br/><br/>• È applicabile agli operatori di confronto comuni, incluso **LIKE**, che usano filtri bitmap.<br/><br/>• Funziona solo quando è disponibile un predicato di stringa.<br/><br/>• Funziona con tutte le regole di confronto supportate dal prodotto.<br/><br/>Per altre informazioni sui filtri bitmap, vedere questo post di blog sull'[introduzione ai filtri bitmap per l'esecuzione di query](http://blogs.msdn.com/b/sqlqueryprocessing/archive/2006/10/27/query-execution-bitmaps.aspx). |



<a id="ExampleQueryParallelCciByCompatLevel" name="ExampleQueryParallelCciByCompatLevel"></a>


&nbsp;


## Esempio di modifica di un piano di query in base al livello di compatibilità


Per un'istruzione **INSERT...SELECT** di Transact-SQL, questa sezione illustra le differenze tra i piani di query nei livelli di compatibilità 120 e 130.


#### Schema della tabella di origine


La tabella seguente contiene almeno 300.000 righe. Se la quantità di dati non è sufficiente per rendere vantaggioso il parallelismo, è possibile che il piano di query avanzato non includa il parallelismo.


```
CREATE TABLE [dbo].[ccitestoriginal](
	[FinanceKey] [int] NOT NULL,
	[DateKey] [int] NOT NULL,
	[OrganizationKey] [int] NOT NULL,
	[DepartmentGroupKey] [int] NOT NULL,
	[ScenarioKey] [int] NOT NULL,
	[AccountKey] [int] NOT NULL,
	[Amount] [float] NOT NULL,
	[Date] [datetime] NULL
) ON [PRIMARY];
GO

CREATE CLUSTERED COLUMNSTORE INDEX [cci_ccitestoriginal]
	ON [dbo].[ccitestoriginal]
	WITH (DROP_EXISTING = OFF) ON [PRIMARY];
GO
```


#### Script per la generazione del piano di query


Applicare questi passaggi allo script Transact-SQL seguente:


1. In **Ssms.exe** connettersi al database.
2. Incollare lo script Transact-SQL in una finestra della query **Ssms.exe**.
3. Modificare lo script per assicurare che **120** sia il valore specificato nell'istruzione **ALTER DATABASE**.
4. Eseguire solo la parte dello script che *precede* l'istruzione **INSERT INTO**.
5. Attivare l'opzione del menu: **Query** > **Includi piano di esecuzione effettivo (Ctrl+M)**.
6. Eseguire solo l'istruzione **INSERT INTO**.<br/><br/>
7. Disattivare l'opzione del menu: **Query** > **Includi piano di esecuzione effettivo (Ctrl+M)**.
8. Eseguire infine solo la parte dello script che *segue* l'istruzione **INSERT INTO**.
9. Notare il piano di query per **120** nella scheda **Piano di esecuzione**, che si trova accanto alla scheda **Risultati**.<br/>-- -- -- -- -- --
10. Modificare lo script in modo che **130** sia il valore specificato in **ALTER DATABASE**.
11. Eseguire di nuovo lo script, come illustrato nei passaggi precedenti.
12. Notare che il piano di query per **130** è diverso e include il parallelismo.


&nbsp;


```
go
SET NOCOUNT ON;
go

ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL =
	120
	--130
;
go

DROP TABLE ccitest_into_work;
go

SELECT *
	INTO ccitest_into_work  -- Create an empty copy of the table.
	FROM ccitestoriginal
	WHERE 1=2;
go

CREATE CLUSTERED COLUMNSTORE INDEX ccitest_into_work_cci
	ON ccitest_into_work;
go

SET NOCOUNT OFF;
go
	
	-- Run this INSERT statement alone!
	--
	-- First run all the preceding Transact-SQL statements.
	-- Then run this one INSERT statement alone.
	-- Then run all remaining Transact-SQL statements.

INSERT INTO ccitest_into_work WITH (TABLOCK)
	SELECT TOP 300000 *
		FROM ccitestoriginal;
go

SET NOCOUNT ON;
go

DROP TABLE ccitest_into_work;
go
```


#### Visualizzare i due piani



<br/>**120:** Questo è il piano di query quando il livello di compatibilità è **120**.


![Piano al livello 120][1-Plan-at-level-120]


<br/>**130:** Questo è il piano di query quando il livello di compatibilità è **130**.


Il piano **130** include il *parallelismo*, non presente nel piano **120**.


La visualizzazione del piano è abbastanza ampia in **Ssms.exe**. Per una visualizzazione migliore in questo articolo, la schermata è suddivisa in due parti. La seconda parte è la continuazione della prima.


![Piano al livello 130][2-Plan-at-level-130]


## Livello di compatibilità predefinito nel database


Quando si aggiorna il server del database SQL di Azure, ad esempio dalla versione V11 alla versione V12, il livello di compatibilità in ogni database non viene modificato. Dopo l'aggiornamento, sarà possibile aumentare il livello di compatibilità in qualsiasi database specifico usando l'istruzione **ALTER DATABASE**.


Qualsiasi database appena creato avrà il livello massimo di compatibilità consentito dalla versione del database SQL.



<!-- Image references. -->

[1-Plan-at-level-120]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b-120.png

[2-Plan-at-level-130]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b12-130.png

<!---HONumber=Sept15_HO4-->