<properties
   pageTitle="Guida per l'uso di PolyBase in SQL Data Warehouse | Microsoft Azure"
   description="Linee guida e consigli per l'uso di PolyBase in scenari di SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# Guida per l'uso di PolyBase in SQL Data Warehouse

Questa guida offre informazioni pratiche per l'uso di PolyBase in SQL Data Warehouse.

Per iniziare, seguire l'esercitazione [Caricamento dei dati con PolyBase][].


## Rotazione delle chiavi di archiviazione

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

## Eseguire query sui dati di archiviazione BLOB di Azure
Le query su tabelle esterne usano semplicemente il nome della tabella come se fosse una tabella relazionale.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] Una query su una tabella esterna può avere esito negativo con l'errore *"Query interrotta-- è stata raggiunta la soglia massima durante la lettura da un'origine esterna"*. Indica che i dati esterni contengono record *sporchi*. Un record di dati viene considerato "sporco" se i tipi/numero dei dati effettivi delle colonne non corrispondono a definizioni di colonna della tabella esterna o se i dati non sono conformi al formato di file esterno specificato. Per risolvere questo problema, assicurarsi che la tabella esterna e le definizioni del formato del file esterno siano corrette e i dati esterni siano conformi a queste definizioni. Nel caso in cui un subset di record di dati esterni sia sporco, è possibile scegliere di rifiutare tali record per le query utilizzando le opzioni di rifiuto in CREATE EXTERNAL TABLE DDL.


## Caricare dati dall'archiviazione BLOB di Azure
Questo esempio carica i dati dall'archiviazione BLOB di Azure nel database di SQL Data Warehouse.

Archiviando i dati direttamente viene eliminato il tempo di trasferimento dei dati per le query. L'archiviazione dei dati con un indice columnstore migliora le prestazioni delle query di analisi fino a 10 volte.

Questo esempio usa l'istruzione CREATE TABLE AS SELECT per caricare i dati. La nuova tabella eredita le colonne indicate nella query. Eredita i tipi di dati di tali colonne dalla definizione della tabella esterna.

CREATE TABLE AS SELECT è un’istruzione con elevate prestazioni di Transact-SQL che carica i dati in parallelo per tutti i nodi di calcolo di SQL Data Warehouse. È stata sviluppata in origine per il motore di elaborazione a elevato parallelismo (MPP) nel sistema di piattaforma di analisi ed è ora inclusa in SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Vedere [CREATE TABLE AS SELECT (Transact-SQL)][].

## Creare statistiche sui dati appena caricati

SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico. Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati. Per una spiegazione dettagliata delle statistiche, vedere l'argomento [Statistiche][] nel gruppo di argomenti sullo sviluppo. Di seguito è possibile vedere un rapido esempio di come creare statistiche nella tabella caricata in questo esempio.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## Esportare i dati in archiviazione BLOB di Azure
Questa sezione illustra come esportare i dati da SQL Data Warehouse nella risorsa di archiviazione BLOB di Azure. In questo esempio si utilizza CREATE EXTERNAL TABLE AS SELECT che è un’istruzione con elevate prestazioni di Transact-SQL per esportare i dati in parallelo da tutti i nodi di calcolo.

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


## Risolvere il requisito PolyBase UTF-8
Nel presente PolyBase supporta il caricamento di file di dati che sono stati con codificata UTF-8. Come UTF-8 viene utilizzata la stessa codifica dei caratteri come ASCII PolyBase verrà inoltre supporto per il caricamento di dati è con codifica ASCII. Tuttavia, PolyBase non supporta altri codifica dei caratteri, ad esempio UTF-16 / Unicode o caratteri ASCII estesi. ASCII esteso include i caratteri con accenti, ad esempio umlaut comune in tedesco.

Per ovviare a questo requisito la risposta migliore consiste nello scrivere nuovamente per la codifica UTF-8.

Esistono diversi modi per eseguire questa operazione. Di seguito sono due approcci con Powershell:

### Esempio semplice di file di piccole dimensioni

Di seguito è una semplice uno linea script di Powershell che crea il file.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Tuttavia, mentre questo è un modo semplice per codificare di nuovo i dati è assolutamente più efficiente. Nell'esempio di flusso dei / o seguente è molto, molto più velocemente e raggiunge lo stesso risultato.

### Nell'esempio di flusso i/o per file di dimensioni maggiori

Nell'esempio di codice riportato di seguito è più complessa, ma è molto più efficiente durante il flusso di righe di dati dall'origine alla destinazione. Utilizzare questo approccio per file di dimensioni maggiori.

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## Passaggi successivi
Per ulteriori informazioni sullo spostamento di dati in SQL Data Warehouse, vedere [Panoramica sulla migrazione di dati][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Caricamento dei dati con PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Panoramica sulla migrazione di dati]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026).aspx
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

<!---HONumber=AcomDC_0907_2016-->