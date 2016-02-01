<properties
   pageTitle="Eseguire la migrazione dello schema in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la migrazione dello schema in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/19/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Eseguire la migrazione dello schema in SQL Data Warehouse#

I riepiloghi seguenti consentono di capire le differenze tra l'uso di SQL Server e di SQL Data Warehouse per la migrazione di un database.

### Funzionalità delle tabelle
SQL Data Warehouse non usa né supporta le funzionalità seguenti:

- Chiavi primarie
- Chiavi esterne
- Vincoli CHECK
- Vincoli UNIQUE
- Indici univoci
- Colonne calcolate
- Colonne di tipo sparse
- Tipi definiti dall'utente
- Viste indicizzate
- Identità
- Sequenze
- Trigger
- Sinonimi

### Differenze nei tipi di dati
SQL Data Warehouse supporta i tipi di dati business comuni elencati di seguito:

- bigint
- binary
- bit
- char
- date
- datetime
- datetime2
- datetimeoffset
- decimal
- float
- int
- money
- nchar
- nvarchar
- real
- smalldatetime
- smallint
- smallmoney
- time
- tinyint
- varbinary
- varchar

Per identificare le colonne del data warehouse che contengono tipi non compatibili, è possibile usare questa query:

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

La query include tutti i tipi di dati definiti dall'utente e non supportati.

L'eventuale presenza di tipi non supportati nel database in uso non costituisce un problema. Di seguito vengono proposte alcune alternative che è possibile usare.

Invece di:

- **geometry**, usare un tipo varbinary.
- **geography**, usare un tipo varbinary.
- **hierarchyid**, questo tipo CLR non è supportato.
- **image**, **text**, **ntext**, usare varchar/nvarchar (il valore inferiore offre prestazioni migliori).
- **nvarchar(max)**, usare nvarchar(4000) o un valore inferiore per prestazioni migliori
- **numeric**, usare decimal.
- **sql\_variant**, dividere la colonna in più colonne fortemente tipizzate.
- **sysname**, usare nvarchar(128).
- **table**, convertire in tabelle temporanee.
- **timestamp**, rielaborare il codice per l'uso di datetime2 e della funzione `CURRENT_TIMESTAMP`. Tenere presente che non è possibile usare current\_timestamp come vincolo predefinito e che il valore non verrà aggiornato automaticamente. Se è necessario eseguire la migrazione di valori rowversion da una colonna di tipo timestamp, usare binary(8) o varbinary(8) per i valori di versione di riga NOT NULL o NULL.
- **varchar(max)**, usare varchar(8000) o un valore inferiore per prestazioni migliori.
- **uniqueidentifier**, usare varbinary(16) o varchar(36) a seconda del formato di input (file binario o carattere) dei valori. Se il formato di input è basato sui caratteri, l'ottimizzazione è possibile. Convertendo il tipo di formato da carattere a binario, è possibile ridurre l'archiviazione delle colonne di oltre il 50%. Nelle tabelle di grandi dimensioni questa ottimizzazione può essere utile.
- **tipi definiti dall'utente**, riconvertirli nei tipi nativi corrispondenti, se possibile.
- **xml**, usare varchar(8000) o un valore inferiore per prestazioni migliori. Suddividere in colonne, se necessario.

Supporto parziale:

- I vincoli predefiniti supportano solo valori letterali e costanti. Le espressioni o le funzioni non deterministiche, ad esempio `GETDATE()` o `CURRENT_TIMESTAMP`, non sono supportate.

> [AZURE.NOTE]Definire le tabelle in modo che le dimensioni massime possibili della riga, inclusa la lunghezza totale delle colonne a lunghezza variabile, non superino 32.767 byte. Anche se è possibile definire una riga con dati a lunghezza variabile che possono superare questa cifra, non si potranno inserire dati nella tabella. Provare anche a limitare le dimensioni delle colonne a lunghezza variabile per migliorare la velocità effettiva delle query in esecuzione.

## Passaggi successivi
Dopo la migrazione dello schema del database in SQLDW, è possibile passare a uno degli articoli seguenti:

- [Eseguire la migrazione dei dati][]
- [Eseguire la migrazione del codice][]

Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->
[Eseguire la migrazione del codice]: sql-data-warehouse-migrate-code.md
[Eseguire la migrazione dei dati]: sql-data-warehouse-migrate-data.md
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0121_2016-->