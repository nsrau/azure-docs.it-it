<properties
   pageTitle="Eseguire la migrazione di un SQL Data Warehouse di Azure esistente all’archiviazione Premium | Microsoft Azure"
   description="Istruzioni per la migrazione di un SQL Data Warehouse esistente all’archiviazione Premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# Dettagli sulla migrazione ad Archiviazione Premium
SQL Data Warehouse ha recentemente introdotto [Archiviazione Premium per una maggiore prevedibilità delle prestazioni][]. Ora è possibile eseguire la migrazione dei data warehouse esistenti attualmente inclusi in Archiviazione Premium e in Archiviazione Standard. Questo documento fornisce informazioni sui tempi e le modalità di esecuzione delle migrazioni automatiche e su come eseguire la migrazione self-service se si preferisce mantenere il controllo sui tempi di inattività.

Se si hanno più data warehouse, usare la [pianificazione della migrazione automatica][] riportata di seguito per determinare quando verrà eseguita la relativa migrazione.

## Determinare il tipo di archiviazione
Se il data warehouse è stato creato prima delle date riportate di seguito, si sta usando Archiviazione Standard. Per ogni Data Warehouse in Archiviazione Standard soggetto alla migrazione automatica è presente una notifica nella parte superiore del pannello Data Warehouse nel [portale di Azure][] in cui viene indicato che un *aggiornamento futuro di archiviazione Premium richiederà un'interruzione del servizio. Altre informazioni->*".

| **Area** | **Data warehouse creato prima di questa data** |
| :------------------ | :-------------------------------- |
| Australia orientale | Archiviazione Premium non ancora disponibile |
| Australia sudorientale | 5 agosto 2016 |
| Brasile meridionale | 5 agosto 2016 |
| Canada centrale | 25 maggio 2016 |
| Canada orientale | 26 maggio 2016 |
| Stati Uniti centrali | 26 maggio 2016 |
| Cina orientale | Archiviazione Premium non ancora disponibile |
| Cina settentrionale | Archiviazione Premium non ancora disponibile |
| Asia orientale | 25 maggio 2016 |
| Stati Uniti orientali | 26 maggio 2016 |
| Stati Uniti Orientali 2 | 27 maggio 2016 |
| India centrale | 27 maggio 2016 |
| India meridionale | 26 maggio 2016 |
| India occidentale | Archiviazione Premium non ancora disponibile |
| Giappone orientale | 5 agosto 2016 |
| Giappone occidentale | Archiviazione Premium non ancora disponibile |
| Stati Uniti centro-settentrionali | Archiviazione Premium non ancora disponibile |
| Europa settentrionale | 5 agosto 2016 |
| Stati Uniti centro-meridionali | 27 maggio 2016 |
| Asia sudorientale | 24 maggio 2016 |
| Europa occidentale | 25 maggio 2016 |
| Stati Uniti centro-occidentali | 26 agosto 2016 |
| Stati Uniti occidentali | 26 maggio 2016 |
| Stati Uniti occidentali 2 | 26 agosto 2016 |

## Dettagli sulla migrazione automatica
Per impostazione predefinita, la migrazione automatica del database verrà eseguita tra le 18.00 e le 06.00, ora locale dell'area di appartenenza, in base alla [pianificazione della migrazione automatica][] riportata di seguito. Durante la migrazione non sarà possibile usare il Data Warehouse esistente. In base alle stime la migrazione richiederà circa un'ora per TB di archiviazione per ogni data warehouse. Microsoft si assicurerà che non vengano addebitati costi durante il processo di migrazione automatica.

> [AZURE.NOTE] Durante la migrazione non sarà possibile usare il data warehouse esistente. Al termine della migrazione, il data warehouse sarà di nuovo online.

Di seguito sono riportati in dettaglio i passaggi eseguiti automaticamente da Microsoft per completare la migrazione senza richiedere alcun intervento da parte dell'utente. In questo esempio, immaginare che il Data Warehouse esistente in Archiviazione Standard sia attualmente denominato "MyDW".

1.	Microsoft rinomina "MyDW" in "MyDW\_DO\_NOT\_USE\_[Timestamp]".
2.	Microsoft sospende "MyDW" in "MyDW\_DO\_NOT\_USE\_[Timestamp]". Nel mentre, viene eseguito il backup. In caso problemi, il processo potrebbe essere sospeso e riprendere più volte.
3.	Microsoft crea un nuovo Data Warehouse denominato "MyDW" in Archiviazione Premium dal backup eseguito al passaggio 2. "MyDW" non viene visualizzato fino al termine del processo di ripristino.
4.	Dopo aver completato il ripristino, "MyDW" torna agli stessi DWU e allo stato sospeso o attivo di prima della migrazione.
5.	Al termine della migrazione, Microsoft elimina "MyDW\_DO\_NOT\_USE\_[Timestamp]".
	
> [AZURE.NOTE] Le impostazioni seguenti non vengono mantenute come parte della migrazione:
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

### Pianificazione della migrazione automatica
I processi di migrazione automatica vengono eseguiti tra le 18.00 e le 06.00 (ora locale per ogni area) durante la seguente pianificazione di interruzione del servizio.

| **Area** | **Data di inizio prevista** | **Data di fine prevista** |
| :------------------ | :--------------------------- | :--------------------------- |
| Australia orientale | Non ancora determinata | Non ancora determinata |
| Australia sudorientale | 10 agosto 2016 | 24 agosto 2016 |
| Brasile meridionale | 10 agosto 2016 | 24 agosto 2016 |
| Canada centrale | 23 giugno 2016 | 1 luglio 2016 |
| Canada orientale | 23 giugno 2016 | 1 luglio 2016 |
| Stati Uniti centrali | 23 giugno 2016 | 4 luglio 2016 |
| Cina orientale | Non ancora determinata | Non ancora determinata |
| Cina settentrionale | Non ancora determinata | Non ancora determinata |
| Asia orientale | 23 giugno 2016 | 1 luglio 2016 |
| Stati Uniti orientali | 23 giugno 2016 | 11 luglio 2016 |
| Stati Uniti Orientali 2 | 23 giugno 2016 | 8 luglio 2016 |
| India centrale | 23 giugno 2016 | 1 luglio 2016 |
| India meridionale | 23 giugno 2016 | 1 luglio 2016 |
| India occidentale | Non ancora determinata | Non ancora determinata |
| Giappone orientale | 10 agosto 2016 | 24 agosto 2016 |
| Giappone occidentale | Non ancora determinata | Non ancora determinata |
| Stati Uniti centro-settentrionali | Non ancora determinata | Non ancora determinata |
| Europa settentrionale | 10 agosto 2016 | 31 agosto 2016 |
| Stati Uniti centro-meridionali | 23 giugno 2016 | 2 luglio 2016 |
| Asia sudorientale | 23 giugno 2016 | 1 luglio 2016 |
| Europa occidentale | 23 giugno 2016 | 8 luglio 2016 |
| Stati Uniti centro-occidentali | 14 agosto 2016 | 31 agosto 2016 |
| Stati Uniti occidentali | 23 giugno 2016 | 7 luglio 2016 |
| Stati Uniti occidentali 2 | 14 agosto 2016 | 31 agosto 2016 |

## Migrazione self-service ad Archiviazione Premium
Se si preferisce mantenere il controllo sui tempi di inattività, è possibile attenersi alla procedura seguente per eseguire la migrazione di un Data Warehouse esistente da Archiviazione Standard in Archiviazione Premium. Se si sceglie di eseguire la migrazione self-service, è necessario completarla prima dell'inizio della migrazione automatica nella stessa area, per evitare il rischio di conflitti dovuti alla migrazione automatica. Vedere in proposito la [pianificazione della migrazione automatica][].

### Istruzioni per la migrazione self-service
Se si preferisce mantenere il controllo sui tempi di inattività, è possibile eseguire la migrazione self-service del data warehouse usando la funzionalità di backup/ripristino. La parte della migrazione relativa al ripristino dovrebbe richiedere circa un'ora per TB di archiviazione per ogni Data Warehouse. Per mantenere lo stesso nome dopo il completamento della migrazione, seguire la [Procedura di ridenominazione durante la migrazione][].

1.	[Sospendere][] il Data Warehouse. Verrà eseguito un backup automatico
2.	[Ripristinare][] il data warehouse dallo snapshot più recente
3.	Eliminare il data warehouse esistente in Archiviazione Standard. **Se non viene eseguito questo passaggio, verranno addebitati entrambi i data warehouse.**

> [AZURE.NOTE] Le impostazioni seguenti non vengono mantenute come parte della migrazione:
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

#### Facoltativo: Procedura di ridenominazione durante la migrazione 
Due database nello stesso server logico non possono avere lo stesso nome. SQL Data Warehouse ora supporta la possibilità di rinominare un data warehouse.

In questo esempio, immaginare che il Data Warehouse esistente in Archiviazione Standard sia attualmente denominato "MyDW".

1.	Rinominare "MyDW" facendo seguire il comando ALTER DATABASE a un elemento come "MyDW\_BeforeMigration". Questo comando termina tutte le transazioni esistenti e deve essere eseguita nel database master abbia esito positivo.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.	[Sospendere][] "MyDW\_BeforeMigration" per eseguire un backup automatico
3.	[Ripristinare][] dallo snapshot più recente un nuovo database con il nome di prima (es: "MyDW")
4.	Eliminare "MyDW\_BeforeMigration". **Se non viene eseguito questo passaggio, verranno addebitati entrambi i data warehouse.**

> [AZURE.NOTE] Le impostazioni seguenti non vengono mantenute come parte della migrazione:
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

## Passaggi successivi
Con il passaggio ad Archiviazione Premium, il numero di file BLOB del database nell'architettura sottostante del data warehouse è aumentato. Per ottenere il massimo dei vantaggi delle prestazioni per questa modifica, si consiglia di ricreare gli indici columnstore cluster usando il seguente script. Lo script di seguito forzerà alcuni dei dati esistenti per i BLOB aggiuntivi. Se non viene eseguita alcuna azione, i dati verranno ovviamente ridistribuiti nel tempo mentre si caricano più dati nelle tabelle di Data Warehouse.

**Prerequisiti:**

1.	È necessario eseguire Data Warehouse con almeno 1.000 DWU (vedere [Ridimensionare la potenza di calcolo][]).
2.	L'utente che esegue lo script deve essere nel [ruolo mediumrc][] o superiore.
	1.	Per aggiungere un utente a questo ruolo, eseguire questo codice:
		1.	````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Passaggio 1: creare una tabella per controllare la ricompilazione degli indici
-- Eseguire come utente in mediumrc o superiore
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Passaggio 2: eseguire le ricompilazioni degli indici Se si verifica un errore di script, il codice riportato di seguito può essere eseguito nuovamente per riavviare il processo dal punto in cui si è interrotto.
-- Eseguire come utente in mediumrc o superiore
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Passaggio 3: tabella di pulizia creata nel passaggio 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

In caso di problemi con il data warehouse, [creare un ticket di supporto][] e specificare la migrazione ad Archiviazione Premium come possibile causa.

<!--Image references-->

<!--Article references-->
[pianificazione della migrazione automatica]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[creare un ticket di supporto]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Sospendere]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Ripristinare]: sql-data-warehouse-restore-database-portal.md
[Procedura di ridenominazione durante la migrazione]: #optional-steps-to-rename-during-migration
[Ridimensionare la potenza di calcolo]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[ruolo mediumrc]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Archiviazione Premium per una maggiore prevedibilità delle prestazioni]: https://azure.microsoft.com/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[portale di Azure]: https://portal.azure.com

<!-----HONumber=AcomDC_0831_2016-->