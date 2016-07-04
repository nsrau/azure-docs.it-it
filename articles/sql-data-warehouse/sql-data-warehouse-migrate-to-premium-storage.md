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
   ms.date="06/21/2016"
   ms.author="nicw;barbkess;sonyama"/>

# Dettagli sulla migrazione ad Archiviazione Premium
SQL Data Warehouse ha recentemente introdotto [Archiviazione Premium per una maggiore prevedibilità delle prestazioni][]. Ora è possibile eseguire la migrazione dei data warehouse esistenti attualmente inclusi in Archiviazione Premium e in Archiviazione Standard. Questo documento fornisce informazioni sui tempi e le modalità di esecuzione delle migrazioni automatiche e su come eseguire la migrazione self-service se si preferisce mantenere il controllo sui tempi di inattività.

Se si hanno più data warehouse, usare la [pianificazione della migrazione automatica][] riportata di seguito per determinare quando verrà eseguita la relativa migrazione.

## Dettagli sulla migrazione automatica
Per impostazione predefinita, la migrazione automatica del database verrà eseguita tra le 18.00 e le 6.00, ora locale dell'area di appartenenza, in base alla [pianificazione della migrazione automatica][] riportata di seguito. Durante la migrazione, non sarà possibile usare il data warehouse esistente. In base alle stime la migrazione richiederà circa un'ora per TB di archiviazione per ogni data warehouse. Microsoft si assicurerà che non vengano addebitati costi durante il processo di migrazione.

> [AZURE.NOTE] Durante la migrazione non sarà possibile usare il data warehouse esistente. Al termine della migrazione, il data warehouse sarà di nuovo online.

Di seguito sono riportati in dettaglio i passaggi eseguiti automaticamente da Microsoft per completare la migrazione senza richiedere alcun intervento da parte dell'utente. Ai fini di questo esempio, si supponga che il data warehouse esistente in Archiviazione Standard sia attualmente denominato "MyDW".

1.	Microsoft rinomina "MyDW" in "MyDW\_DO\_NOT\_USE\_[Timestamp]".
2.	Microsoft sospende "MyDW\_DO\_NOT\_USE\_[Timestamp]" e ne esegue il backup. In caso problemi, il processo potrebbe essere sospeso e riprendere più volte.
3.	Microsoft crea un nuovo data warehouse denominato "MyDW" in Archiviazione Premium dal backup eseguito al passaggio 2. "MyDW" non viene visualizzato fino al termine del processo di ripristino.
4.	Dopo aver completato il ripristino, "MyDW" torna allo stesso livello di DWU e nello stesso stato sospeso o attivo di prima della migrazione.
5.	Al termine della migrazione, Microsoft elimina "MyDW\_DO\_NOT\_USE\_[Timestamp]".
	
> [AZURE.NOTE] Le impostazioni seguenti non verranno mantenute come parte della migrazione:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

### Pianificazione della migrazione automatica
La migrazione automatica del database verrà eseguita tra le 18.00 e le 6.00, ora locale dell'area di appartenenza, in base alla pianificazione riportata di seguito.

| **Area** | **Data di inizio prevista** | **Data di fine prevista** |
| :------------------ | :--------------------------- | :--------------------------- |
| Australia orientale | Non ancora determinata | Non ancora determinata |
| Australia sudorientale | Non ancora determinata | Non ancora determinata |
| Brasile meridionale | Non ancora determinata | Non ancora determinata |
| Canada centrale | 23 giugno 2016 | 1 luglio 2016 |
| Canada orientale | 23 giugno 2016 | 1 luglio 2016 |
| Stati Uniti centrali | 23 giugno 2016 | 1 luglio 2016 |
| Cina orientale | Non ancora determinata | Non ancora determinata |
| Cina settentrionale | Non ancora determinata | Non ancora determinata |
| Asia orientale | 23 giugno 2016 | 1 luglio 2016 |
| Stati Uniti orientali | 23 giugno 2016 | 1 luglio 2016 |
| Stati Uniti Orientali 2 | 23 giugno 2016 | 1 luglio 2016 |
| India centrale | 23 giugno 2016 | 1 luglio 2016 |
| India meridionale | 23 giugno 2016 | 1 luglio 2016 |
| India occidentale | Non ancora determinata | Non ancora determinata |
| Giappone orientale | Non ancora determinata | Non ancora determinata |
| Giappone occidentale | Non ancora determinata | Non ancora determinata |
| Stati Uniti centro-settentrionali | Non ancora determinata | Non ancora determinata |
| Europa settentrionale | Non ancora determinata | Non ancora determinata |
| Stati Uniti centro-meridionali | 23 giugno 2016 | 1 luglio 2016 |
| Asia sudorientale | 23 giugno 2016 | 1 luglio 2016 |
| Europa occidentale | 23 giugno 2016 | 1 luglio 2016 |
| Stati Uniti occidentali | 23 giugno 2016 | 1 luglio 2016 |

## Migrazione self-service ad Archiviazione Premium
Se si preferisce mantenere il controllo sui tempi di inattività, è possibile usare la procedura seguente per eseguire la migrazione di un data warehouse esistente da Archiviazione Standard in Archiviazione Premium. Se si sceglie di eseguire la migrazione self-service, è necessario completarla prima dell'inizio della migrazione automatica nella stessa area, per evitare il rischio di conflitti dovuti alla migrazione automatica. Vedere in proposito la [pianificazione della migrazione automatica][].

> [AZURE.NOTE] SQL Data Warehouse con Archiviazione Premium attualmente non ha ridondanza geografica. Ciò significa che, dopo la migrazione del data warehouse in Archiviazione Premium, i dati risiedono soltanto nell'area corrente. Quando saranno disponibili, i backup geografici eseguiranno una copia del data warehouse ogni 24 ore nell'[area abbinata di Azure][], che permette di eseguire il ripristino dal backup geografico in qualsiasi area di Azure. Quando la funzionalità di backup geografico sarà disponibile per le migrazioni self-service, verrà annunciato nel [sito principale della documentazione][]. Le migrazioni automatiche, invece, non avranno questa limitazione.

### Determinare il tipo di archiviazione
Se il data warehouse è stato creato prima delle date riportate di seguito, si sta usando Archiviazione Standard.

| **Area** | **Data warehouse creato prima di questa data** |
| :------------------ | :-------------------------------- |
| Australia orientale | Archiviazione Premium non ancora disponibile |
| Australia sudorientale | Archiviazione Premium non ancora disponibile |
| Brasile meridionale | Archiviazione Premium non ancora disponibile |
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
| Giappone orientale | Archiviazione Premium non ancora disponibile |
| Giappone occidentale | Archiviazione Premium non ancora disponibile |
| Stati Uniti centro-settentrionali | Archiviazione Premium non ancora disponibile |
| Europa settentrionale | Archiviazione Premium non ancora disponibile |
| Stati Uniti centro-meridionali | 27 maggio 2016 |
| Asia sudorientale | 24 maggio 2016 |
| Europa occidentale | 25 maggio 2016 |
| Stati Uniti occidentali | 26 maggio 2016 |


### Istruzioni per la migrazione self-service
Se si preferisce mantenere il controllo sui tempi di inattività, è possibile eseguire la migrazione self-service del data warehouse usando la funzionalità di backup/ripristino. La parte della migrazione relativa al ripristino dovrebbe richiedere circa un'ora per TB di archiviazione per ogni data warehouse. Per mantenere lo stesso nome dopo aver completato la migrazione, usare la procedura riportata di seguito come [soluzione alternativa per la ridenominazione][].

1.	[Sospendere][] il data warehouse. Verrà eseguito un backup automatico.
2.	[Ripristinare][] il data warehouse dallo snapshot più recente.
3.	Eliminare il data warehouse esistente in Archiviazione Standard. **Se non viene eseguito questo passaggio, verranno addebitati entrambi i data warehouse.**

> [AZURE.NOTE] Le impostazioni seguenti non verranno mantenute come parte della migrazione:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

#### Facoltativo: soluzione alternativa per la ridenominazione 
Due database nello stesso server logico non possono avere lo stesso nome. SQL Data Warehouse attualmente non supporta la possibilità di rinominare un data warehouse. Le istruzioni riportate di seguito offrono una soluzione alternativa da usare nel corso di una migrazione self-service. Nota: le migrazioni automatiche non avranno questa limitazione.

Ai fini di questo esempio, si supponga che il data warehouse esistente in Archiviazione Standard sia attualmente denominato "MyDW".

1.	[Sospendere][] "MyDW". Verrà eseguito un backup automatico.
2.	[Ripristinare][] dallo snapshot più recente un nuovo database con un nome diverso, ad esempio "MyDWTemp".
3.	Eliminare "MyDW". **Se non viene eseguito questo passaggio, verranno addebitati entrambi i data warehouse.**
4.	Dato che "MyDWTemp" è un data warehouse di nuova creazione, per un certo periodo di tempo non sarà disponibile un backup per il ripristino. È consigliabile continuare a eseguire operazioni in "MyDWTemp" per alcune ore prima di procedere con i passaggi 5 e 6.
5.	[Sospendere][] "MyDWTemp". Verrà eseguito un backup automatico.
6.	[Ripristinare][] dallo snapshot più recente di "MyDWTemp" un nuovo database denominato "MyDW".
7.	Eliminare "MyDWTemp". **Se non viene eseguito questo passaggio, verranno addebitati entrambi i data warehouse.**

> [AZURE.NOTE] Le impostazioni seguenti non verranno mantenute come parte della migrazione:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

## Passaggi successivi
In caso di problemi con il data warehouse, [creare un ticket di supporto][] e specificare la migrazione ad Archiviazione Premium come possibile causa.

<!--Image references-->

<!--Article references-->
[pianificazione della migrazione automatica]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[area abbinata di Azure]: ./best-practices-availability-paired-regions.md
[sito principale della documentazione]: ./services/sql-data-warehouse.md
[Sospendere]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Ripristinare]: ./sql-data-warehouse-manage-database-restore-portal.md
[soluzione alternativa per la ridenominazione]: #optional-rename-workaround

<!--MSDN references-->


<!--Other Web references-->
[Archiviazione Premium per una maggiore prevedibilità delle prestazioni]: https://azure.microsoft.com/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/

<!---HONumber=AcomDC_0622_2016-->