---
title: Guida per l'uso di PolyBase in SQL Data Warehouse | Microsoft Docs
description: Linee guida e consigli per l'uso di PolyBase in scenari di SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 9/13/2017
ms.custom: loading
ms.author: cakarst;barbkess
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 7594a0730477fe3f3bd34b0b6207478de70c7595
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guida per l'uso di PolyBase in SQL Data Warehouse
Questa guida offre informazioni pratiche per l'uso di PolyBase in SQL Data Warehouse.

Per iniziare, seguire l'esercitazione [Caricamento dei dati con PolyBase][Load data with PolyBase].

## <a name="rotating-storage-keys"></a>Rotazione delle chiavi di archiviazione
A volte si desidererà modificare la chiave di accesso per l'archiviazione blob per motivi di sicurezza.

Il modo più elegante per eseguire questa operazione consiste nel seguire un processo noto come "ruotare le chiavi". Si sarà notato che siano due chiavi di archiviazione per l'account di archiviazione blob. Si tratta in modo che è possibile eseguire la transizione

Ruotare le chiavi dell'account di archiviazione di Azure è un processo semplice tre passaggio

1. Creare seconda credenziale con ambito database in base alla chiave di accesso di archiviazione secondario
2. Creare una seconda origine dati esterna in base a questa nuova credenziale
3. Eliminare e creare le tabelle esterne che puntano alla nuova origine dati esterna

Dopo aver migrato esterno tutte le tabelle per la nuova origine dati esterna, quindi è possibile eseguire attività di pulizia:

1. Eliminare prima origine dati esterna
2. Credenziali in base alla chiave di accesso di archiviazione primaria con ambito database primo di rilascio
3. Accedere a Azure e rigenerare la chiave di accesso primaria pronta per la volta successiva



## <a name="load-data-with-external-tables"></a>Caricare dati con tabelle esterne
Questo esempio carica i dati dall'archiviazione BLOB di Azure nel database di SQL Data Warehouse.

Archiviando i dati direttamente viene eliminato il tempo di trasferimento dei dati per le query. L'archiviazione dei dati con un indice columnstore migliora le prestazioni delle query di analisi fino a 10 volte.

Questo esempio usa l'istruzione CREATE TABLE AS SELECT per caricare i dati. La nuova tabella eredita le colonne indicate nella query. Eredita i tipi di dati di tali colonne dalla definizione della tabella esterna.

CREATE TABLE AS SELECT è un’istruzione con elevate prestazioni di Transact-SQL che carica i dati in parallelo per tutti i nodi di calcolo di SQL Data Warehouse.  È stata sviluppata in origine per il motore di elaborazione a elevato parallelismo (MPP) nel sistema di piattaforma di analisi ed è ora inclusa in SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Vedere [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

> [!NOTE]
> Un caricamento con una tabella esterna può avere esito negativo con l'errore *"Query interrotta. È stata raggiunta la soglia massima di rifiuti durante la lettura da un'origine esterna"*. Indica che i dati esterni contengono record *sporchi* . Un record di dati viene considerato "sporco" se i tipi/numero dei dati effettivi delle colonne non corrispondono a definizioni di colonna della tabella esterna o se i dati non sono conformi al formato di file esterno specificato. Per risolvere questo problema, assicurarsi che la tabella esterna e le definizioni del formato del file esterno siano corrette e i dati esterni siano conformi a queste definizioni. Nel caso in cui un subset di record di dati esterni sia sporco, è possibile scegliere di rifiutare tali record per le query utilizzando le opzioni di rifiuto in CREATE EXTERNAL TABLE DDL.
> 
> 


## <a name="create-statistics-on-newly-loaded-data"></a>Creare statistiche sui dati appena caricati
SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico.  Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati.  Per una spiegazione dettagliata delle statistiche, vedere l'argomento [Statistiche][Statistics] nel gruppo di argomenti sullo sviluppo.  Di seguito è possibile vedere un rapido esempio di come creare statistiche nella tabella caricata in questo esempio.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-with-external-tables"></a>Esportare dati con tabelle esterne
Questa sezione illustra come esportare i dati da SQL Data Warehouse in Archiviazione BLOB di Azure usando tabelle esterne. In questo esempio si utilizza CREATE EXTERNAL TABLE AS SELECT che è un’istruzione con elevate prestazioni di Transact-SQL per esportare i dati in parallelo da tutti i nodi di calcolo.

Nell'esempio seguente si crea una tabella esterna Weblogs2014 utilizzando le definizioni delle colonne e dati dalla tabella dbo.Weblogs. La definizione della tabella esterna viene archiviata in SQL Data Warehouse e i risultati dell’istruzione SELECT sono esportati nella directory "/ archiviazione/log2014 /" nel contenitore BLOB specificato dall'origine dati. I dati vengono esportati nel formato di file di testo specificato.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>Isolare il caricamento degli utenti
È spesso necessario fare in modo che più utenti possano caricare dati in un data warehouse SQL. Dato che [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] richiede autorizzazioni CONTROL per il database, il risultato sarà la presenza di più utenti con accesso di controllo su tutti gli schemi. Per limitare questa situazione, è possibile usare l'istruzione DENY CONTROL.

Esempio: si supponga che esistano gli schemi di database schema_A per reparto A e schema_B per reparto B e di consentire agli utenti di database utente_A e utente _B di effettuare caricamenti PolyBase rispettivamente in reparto A e B. A entrambi gli utenti sono state concesse le autorizzazioni di database CONTROL.
Gli autori di schema A e B usano a questo punto DENY per bloccare i rispettivi schemi:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 In questo modo, utente_A e utente_B dovrebbero ora essere esclusi dall'accesso allo schema del reparto dell'altro utente.
 
## <a name="polybase-performance-optimizations"></a>Ottimizzazioni delle prestazioni di PolyBase
Per ottenere prestazioni di caricamento ottimali con PolyBase si consiglia quanto segue:
- Suddividere i file compressi di grandi dimensioni in file compressi di dimensioni inferiori. I tipi di compressione attualmente supportati non sono divisibili. Si noterà quindi un peggioramento delle prestazioni caricando un unico file di grandi dimensioni.
- Per ottenere la velocità massima, eseguire il caricamento in una tabella heap di staging round_robin. Si tratta del modo più efficiente per spostare i dati dal livello di archiviazione al data warehouse.
- Tutti i formati di file hanno caratteristiche di prestazioni diverse. Per ottenere la velocità di caricamento massima, usare file di testo delimitati compressi. La differenza di prestazioni tra UTF-8 e UTF-16 è minima.
- Condividere il percorso del livello di archiviazione e del data warehouse per ridurre al minimo la latenza
- Aumentare le prestazioni del data warehouse se si prevede un processo di caricamento di grandi dimensioni.

## <a name="polybase-limitations"></a>Limitazioni di PolyBase
PolyBase in SQL Data Warehouse presenta le limitazioni seguenti che devono essere prese in considerazione quando si progetta un processo di caricamento:
- Una singola riga non può essere maggiore di 1.000.000 byte, indipendentemente dallo schema di tabella definito, incluse le colonne (n)varchar(max). Per le tabelle esterne, ciò significa che le colonne (n)varchar(max) possono avere dimensioni massime di 1.000.000 byte, non il limite di 2 GB definito dal tipo di dati.
- Quando si esportano dati in un formato file ORC da SQL Server o Azure SQL Data Warehouse, le colonne con molto testo possono essere limitate a 50, a causa di errori di memoria insufficiente di Java. Per risolvere questo problema, esportare solo un subset di colonne.





## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sullo spostamento di dati in SQL Data Warehouse, vedere [Panoramica sulla migrazione di dati][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->

