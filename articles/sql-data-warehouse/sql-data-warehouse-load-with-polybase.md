<properties
   pageTitle="Esercitazione su PolyBase in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni su PolyBase e sul relativo uso per scenari di data warehousing."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# Caricare dati con PolyBase
La tecnologia PolyBase consente di eseguire query e join di dati da più origini usando comandi Transact-SQL.

Con PolyBase, è possibile eseguire query sui dati memorizzati nell'archiviazione BLOB di Azure e caricare i dati nel database di SQL Data Warehouse con i passaggi seguenti:

- Creare la chiave master e le credenziali del database.
- Creare oggetti PolyBase: origine dati esterna, formato di file esterno e tabella esterna. 
- Eseguire query sui dati memorizzati nell'archiviazione BLOB di Azure.
- Caricare dati dall'archiviazione BLOB di Azure in SQL Data Warehouse.
- Esportare i dati da SQL Data Warehouse nella risorsa di archiviazione BLOB di Azure.


## Prerequisiti
Per eseguire questa esercitazione, è necessario:

- Account di archiviazione di Azure
- Dati memorizzati nell'archiviazione BLOB di Azure come file con testo delimitato

Creare prima gli oggetti richiesti da PolyBase per la connessione ai dati nell'archiviazione BLOB di Azure e l'esecuzione di query su di essi.

> [AZURE.IMPORTANT]I tipi di account di archiviazione di Azure supportati da PolyBase sono:
> 
> + Archiviazione con ridondanza locale standard (Standard LRS)
> + Archiviazione con ridondanza geografica standard (Standard LRS)
> + Archiviazione con ridondanza geografica e accesso in lettura standard (Standard RAGRS)
>
> I tipi di conto di archiviazione con ridondanza area standard (Standard ZRS) e archiviazione con ridondanza locale premium (LRS Premium) non sono supportati da PolyBase. Se si sta creando un nuovo account di archiviazione di Azure, assicurarsi di selezionare un tipo di account di archiviazione supportato da PolyBase dal livello dei prezzi.

## Passaggio 1: Archiviare le credenziali nel database
Per accedere all'archiviazione BLOB di Azure, è necessario creare una credenziale con ambito di database che archivia informazioni di autenticazione per l'account di archiviazione Azure. Seguire questi passaggi per archiviare le credenziali con il database.

1. Connettersi al database SQL Data Warehouse.
2. Usare [CREATE MASTER KEY (Transact-SQL)][] per creare una chiave master del database. Se il database ha già una chiave master che non è necessario crearne un’altra. Questa chiave viene usata per crittografare il segreto delle credenziali nel passaggio successivo.

    ```
    -- Create a E master key
    CREATE MASTER KEY;
    ```

1. Verificare se si dispone già di credenziali del database. A tale scopo, utilizzare la vista del sistema sys.database\_credentials, non sys.credentials che mostra solo le credenziali del server.

    ``` -- Verificare le credenziali con ambito di database esistenti. SELECT * FROM sys.database\_credentials;

3. Usare [CREATE CREDENTIAL (Transact-SQL)][] per creare credenziali con ambito di database per ogni account di archiviazione di Azure a cui si desidera accedere. In questo esempio, IDENTITY è un nome descrittivo per le credenziali. Il nome dell'identità non influenza l'autenticazione in Archiviazione di Azure. SECRET è la chiave dell'account di archiviazione Azure.

    -- Crea credenziali con ambito di database CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe' , Secret = '<azure_storage_account_key>' ; ```

1. Se è necessario eliminare le credenziali con ambito di database, usare [DROP CREDENTIAL (Transact-SQL)][]\:

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

## Passaggio 2: Creare un'origine dati esterna
L'origine dati esterna è un oggetto di database che archivia il percorso dei dati di archiviazione BLOB di Azure e le informazioni di accesso. Utilizzare [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][] per definire un'origine dati esterna per ogni BLOB di archiviazione di Azure a cui si desidera accedere.

    ```
    -- Create an external data source for an Azure storage blob
    CREATE EXTERNAL DATA SOURCE azure_storage 
    WITH
    (
        TYPE = HADOOP,
        LOCATION ='wasbs://mycontainer@test.blob.core.windows.net',
        CREDENTIAL = ASBSecret
    )
    ;
    ```

Se è necessario eliminare la tabella esterna, utilizzare [DROP EXTERNAL DATA SOURCE][]:

    ```
    -- Drop an external data source
    DROP EXTERNAL DATA SOURCE azure_storage
    ;
    ```

## Passaggio 3: Creare un formato di file esterno
Il formato di file esterno è un oggetto di database che specifica il formato dei dati esterni. PolyBase supporta dati compressi e non compressi nei formati di file con testo delimitato, Hive RCFILE e HIVE ORC.

Utilizzare [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][] per creare il formato di file esterno. In questo esempio si specifica che i dati nel file sono rappresentati da un file di testo decompresso e i campi sono separati dal carattere barra verticale ('|').

```
-- Create an external file format for a text-delimited file.
-- Data is uncompressed and fields are separated with the
-- pipe character.
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT, 
    FORMAT_OPTIONS  
    (
        FIELD_TERMINATOR ='|',
        USE_TYPE_DEFAULT = TRUE
    )
)
;
```

Se si desidera eliminare un formato di file esterno, utilizzare [DROP EXTERNAL FILE FORMAT].

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```

## Creare una tabella esterna

La definizione della tabella esterna è simile alla definizione di una tabella relazionale. La differenza principale è costituita dal percorso e dal formato dei dati.

- La definizione di tabella esterna viene archiviata come metadati nel database SQL Data Warehouse. 
- I dati vengono archiviati nel percorso esterno specificato dall'origine dati.

Utilizzare [CREATE EXTERNAL TABLE (Transact-SQL)][] per definire la tabella esterna.

L'opzione LOCATION specifica il percorso dei dati dalla radice dell'origine dati. In questo esempio i dati si trovano in 'wasbs://mycontainer@test.blob.core.windows.net/path/Demo/'. Tutti i file per la stessa tabella devono trovarsi all'interno della stessa cartella logica nell'archiviazione BLOB di Azure.

Facoltativamente, è possibile specificare le opzioni di rifiuto (REJECT\_TYPE, REJECT\_VALUE, REJECT\_SAMPLE\_VALUE) che determinano la modalità di gestione da parte di PolyBase dei record sporchi riceve dall'origine dati esterna.

```
-- Creating an external table for data in Azure blob storage.
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL,
     [CustomerKey]   int    NOT NULL,
     [GeographyKey]  int        NULL,
     [Speed]         float  NOT NULL,
     [YearMeasured]  int    NOT NULL,
)
WITH 
(
    LOCATION    = '/Demo/',
    DATA_SOURCE = azure_storage,
    FILE_FORMAT = text_file_format      
)
;
```

Gli oggetti appena creati vengono archiviati nel database di SQL Data Warehouse. È possibile visualizzarli in Esplora oggetti di SQL Server Data Tools (SSDT).

Per eliminare una tabella esterna, è necessario utilizzare la sintassi seguente:

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]Quando si elimina una tabella esterna è necessario usare `DROP EXTERNAL TABLE`. **Non è possibile** usare `DROP TABLE`.

Argomento di riferimento: [DROP EXTERNAL TABLE (Transact-SQL)][].

È inoltre importante notare che entrambe le tabelle esterne sono visibili nelle viste del catalogo `sys.tables` e in particolare in `sys.external_tables`.

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


```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE]Una query su una tabella esterna può avere esito negativo con l'errore *"Query interrotta: è stata raggiunta la soglia massima durante la lettura da un'origine esterna"*. Indica che i dati esterni contengono record *sporchi*. Un record di dati viene considerato "sporco" se i tipi/numero dei dati effettivi delle colonne non corrispondono a definizioni di colonna della tabella esterna o se i dati non sono conformi al formato di file esterno specificato. Per risolvere questo problema, assicurarsi che la tabella esterna e le definizioni del formato del file esterno siano corrette e i dati esterni siano conformi a queste definizioni. Nel caso in cui un subset di record di dati esterni sia sporco, è possibile scegliere di rifiutare tali record per le query utilizzando le opzioni di rifiuto in CREATE EXTERNAL TABLE DDL.


## Caricare dati dall'archiviazione BLOB di Azure
Questo esempio carica i dati dall'archiviazione BLOB di Azure nel database di SQL Data Warehouse.

Archiviando i dati direttamente viene eliminato il tempo di trasferimento dei dati per le query. L'archiviazione dei dati con un indice columnstore migliora le prestazioni delle query di analisi fino a 10 volte.

Questo esempio usa l'istruzione CREATE TABLE AS SELECT per caricare i dati. La nuova tabella eredita le colonne indicate nella query. Eredita i tipi di dati di tali colonne dalla definizione della tabella esterna.

CREATE TABLE AS SELECT è un’istruzione con elevate prestazioni di Transact-SQL che carica i dati in parallelo per tutti i nodi di calcolo di SQL Data Warehouse. È stata sviluppata in origine per il motore di elaborazione a elevato parallelismo (MPP) nel sistema di piattaforma di analisi ed è ora inclusa in SQL Data Warehouse.

```
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

```
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## Esportare i dati in archiviazione BLOB di Azure
Questa sezione illustra come esportare i dati da SQL Data Warehouse nella risorsa di archiviazione BLOB di Azure. In questo esempio si utilizza CREATE EXTERNAL TABLE AS SELECT che è un’istruzione con elevate prestazioni di Transact-SQL per esportare i dati in parallelo da tutti i nodi di calcolo.

Nell'esempio seguente si crea una tabella esterna Weblogs2014 utilizzando le definizioni delle colonne e dati dalla tabella dbo.Weblogs. La definizione della tabella esterna viene archiviata in SQL Data Warehouse e i risultati dell’istruzione SELECT sono esportati nella directory "/ archiviazione/log2014 /" nel contenitore BLOB specificato dall'origine dati. I dati vengono esportati nel formato di file di testo specificato.

```
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
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Tuttavia, mentre questo è un modo semplice per codificare di nuovo i dati è assolutamente più efficiente. Nell'esempio di flusso dei / o seguente è molto, molto più velocemente e raggiunge lo stesso risultato.

### Nell'esempio di flusso i/o per file di dimensioni maggiori

Nell'esempio di codice riportato di seguito è più complessa, ma è molto più efficiente durante il flusso di righe di dati dall'origine alla destinazione. Utilizzare questo approccio per file di dimensioni maggiori.

```
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
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[Statistiche]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!---HONumber=AcomDC_1210_2015-->