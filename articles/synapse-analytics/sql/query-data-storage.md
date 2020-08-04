---
title: Eseguire query sui dati nell'archiviazione con SQL su richiesta (anteprima)
description: Questo articolo descrive come eseguire query sull'archiviazione di Azure usando la risorsa SQL su richiesta (anteprima) all'interno di Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 7ef7a771442dba5c7f82196a13b77cb28b0d1ed8
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386657"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Eseguire query sui file di archiviazione con le risorse di SQL su richiesta (anteprima) all'interno di Synapse SQL

SQL su richiesta (anteprima) consente di eseguire query sui dati del data lake. Offre una superficie di attacco per query T-SQL che supporta le query su dati semistrutturati e non strutturati. Per l'esecuzione di query, sono supportati gli aspetti di T-SQL seguenti:

- Superficie di attacco [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) completa, inclusa la maggioranza di [funzioni e operatori SQL](overview-features.md).
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)), che crea una [tabella esterna](develop-tables-external-tables.md) e quindi esporta, in parallelo, i risultati di un'istruzione Transact-SQL SELECT in Archiviazione di Azure.

Per altre informazioni sulle funzionalità attualmente supportate e non, vedere l'articolo [Panoramica di SQL su richiesta](on-demand-workspace-overview.md) o gli articoli seguenti:
- [Accedere alle risorse di archiviazione esterne in Synapse SQL (su richiesta)](develop-storage-files-overview.md), che illustra come usare le [tabelle esterne](develop-tables-external-tables.md) e la funzione [OPENROWSET](develop-openrowset.md) per leggere i dati dall'archiviazione.
- [Controllare l'accesso agli account di archiviazione per SQL su richiesta (anteprima)](develop-storage-files-storage-access-control.md), che illustra come abilitare Synapse SQL per accedere all'archiviazione tramite l'autenticazione di firma di accesso condiviso o l'identità gestita dell'area di lavoro.

## <a name="overview"></a>Panoramica

Per offrire un'esperienza uniforme per le query sul posto sui dati presenti nei file di Archiviazione di Azure, SQL su richiesta usa la funzione [OPENROWSET](develop-openrowset.md) con funzionalità aggiuntive:

- [Esecuzione di query su più file o cartelle](#query-multiple-files-or-folders)
- [Formato di file Parquet](#query-parquet-files)
- [Esecuzione di query su file CSV e testo delimitato (carattere di terminazione del campo, carattere di terminazione della riga, carattere di escape)](#query-csv-files)
- [Lettura di un sottoinsieme selezionato di colonne](#read-a-chosen-subset-of-columns)
- [Inferenza dello schema](#schema-inference)
- [Funzione filename](#filename-function)
- [Funzione filepath](#filepath-function)
- [Uso di tipi complessi e strutture di dati annidate o ripetute](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Eseguire query su file PARQUET

Per eseguire query sui dati di origine Parquet, usare FORMAT = 'PARQUET'

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

Per esempi di utilizzo, vedere l'articolo [Eseguire query su file Parquet](query-parquet-files.md).

## <a name="query-csv-files"></a>Eseguire query su file CSV

Per eseguire query sui dati di origine CSV, usare FORMAT = 'CSV'. Quando si eseguono query sui file CSV è possibile specificare lo schema del file CSV come parte della funzione `OPENROWSET`:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

Sono disponibili alcune opzioni aggiuntive che possono essere usate per modificare le regole di analisi nel formato CSV personalizzato:
- ESCAPE_CHAR = 'char' - Specifica il carattere nel file usato per l'escape di se stesso e per tutti i valori dei delimitatori del file. Se seguito da un valore diverso da se stesso o da uno qualsiasi dei valori dei delimitatori, il carattere di escape viene eliminato durante la lettura del valore.
Il parametro ESCAPE_CHAR verrà applicato se FIELDQUOTE è o non è abilitato. Non verrà usato per l'escape del carattere virgolette singole. Il carattere di virgolette deve essere impostato come escape con un altro carattere di virgolette. Il carattere di virgolette può essere visualizzato all'interno del valore della colonna solo se il valore è incapsulato con caratteri di virgolette.
- FIELDTERMINATOR ='field_terminator' - Specifica il carattere di terminazione del campo da usare. Il carattere di terminazione del campo predefinito è la virgola (" **,** ")
- ROWTERMINATOR ='row_terminator' - Specifica il carattere di terminazione della riga da usare. Il carattere di terminazione della riga predefinito è  **\r\n**.

## <a name="file-schema"></a>Schema del file

Il linguaggio SQL in Synapse SQL consente di definire lo schema del file come parte della funzione `OPENROWSET` e di leggere tutte le colonne o un sottoinsieme oppure tenta di determinare automaticamente i tipi di colonna dal file usando l'inferenza dello schema.

### <a name="read-a-chosen-subset-of-columns"></a>Lettura di un sottoinsieme selezionato di colonne

Per specificare le colonne da leggere, è possibile fornire una clausola WITH facoltativa all'interno dell'istruzione `OPENROWSET`.

- Nel caso di file di dati CSV, per leggere tutte le colonne, specificare i nomi di colonna e i relativi tipi di dati. Se si vuole specificare un sottoinsieme di colonne, selezionare le colonne dai file di dati di origine in base a numeri ordinali. Le colonne verranno associate in base alla designazione dell'ordinale.
- Nel caso di file di dati Parquet, specificare i nomi di colonna che corrispondono a quelli dei file di dati di origine. Le colonne verranno associate per nome.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 as varchar(max)
) as rows
```

Per ogni colonna è necessario specificare il nome e il tipo della colonna nella clausola `WITH`.
Per alcuni esempi, vedere [Leggere file CSV senza specificare tutte le colonne](query-single-csv-file.md#returning-subset-of-columns).

## <a name="schema-inference"></a>Inferenza dello schema

Omettendo la clausola WITH dall'istruzione `OPENROWSET`, è possibile indicare al servizio di rilevare automaticamente lo schema (tramite inferenza) dai file sottostanti.

> [!NOTE]
> Attualmente questa opzione funziona solo per il formato di file PARQUET.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Assicurarsi che vengano usati i [tipi di dati derivati appropriati](best-practices-sql-on-demand.md#check-inferred-data-types) per ottenere prestazioni ottimali. 

## <a name="query-multiple-files-or-folders"></a>Esecuzione di query su più file o cartelle

Per eseguire una query T-SQL su un set di file all'interno di una cartella o di un set di cartelle, considerandoli come singola entità o singolo set di righe, fornire il percorso di una cartella o di un modello (usando caratteri jolly) su un set di file o cartelle.

Sono applicabili le regole seguenti:

- I modelli possono essere presenti in parte di un percorso di directory o in un nome file.
- Nello stesso percorso di directory o nome file possono essere presenti diversi modelli.
- Nel caso di più caratteri jolly, i file all'interno di tutti i percorsi corrispondenti verranno inclusi nel set di file risultante.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Per esempi di utilizzo, vedere [Eseguire query su cartelle e più file](query-folders-multiple-csv-files.md).

## <a name="file-metadata-functions"></a>Funzioni per i metadati dei file

### <a name="filename-function"></a>Funzione filename

Questa funzione restituisce il nome del file da cui ha origine la riga. 

Per eseguire query su file specifici, vedere la sezione Filename nell'articolo [Eseguire query su file specifici](query-specific-files.md#filename).

Il tipo di dati restituito è nvarchar (1024). Per ottenere prestazioni ottimali, è sempre possibile eseguire il cast del risultato della funzione filename al tipo di dati appropriato. Se si usa il tipo di dati Char, assicurarsi che venga usata la lunghezza appropriata.

### <a name="filepath-function"></a>Funzione filepath

Questa funzione restituisce un percorso completo o parziale:

- Se viene chiamata senza il parametro, restituisce il percorso completo del file da cui ha origine una riga.
- Se viene chiamata con il parametro, viene restituita una parte del percorso che corrisponde al carattere jolly nella posizione specificata nel parametro. Ad esempio, il valore del parametro 1 restituisce la parte del percorso che corrisponde al primo carattere jolly.

Per altre informazioni, vedere la sezione Filepath dell'articolo [Eseguire query su file specifici](query-specific-files.md#filepath).

Il tipo di dati restituito è nvarchar (1024). Per ottenere prestazioni ottimali, è sempre possibile eseguire il cast del risultato della funzione filepath al tipo di dati appropriato. Se si usa il tipo di dati Char, assicurarsi che venga usata la lunghezza appropriata.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Uso di tipi complessi e strutture di dati annidate o ripetute

Per garantire un'esperienza uniforme con i dati archiviati in tipi di dati annidati o ripetuti, come nei file [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types), in SQL su richiesta sono state aggiunte le estensioni seguenti.

#### <a name="project-nested-or-repeated-data"></a>Proiettare dati annidati o ripetuti

Per proiettare i dati, eseguire un'istruzione SELECT sul file Parquet contenente colonne di tipi di dati annidati. Nell'output i valori annidati verranno serializzati in JSON e restituiti come tipo di dati SQL varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Per informazioni più dettagliate, vedere la sezione Proiettare dati annidati o ripetuti dell'articolo [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md#project-nested-or-repeated-data).

#### <a name="access-elements-from-nested-columns"></a>Accesso agli elementi di colonne annidate

Per accedere agli elementi annidati di una colonna annidata, ad esempio struct, usare la "notazione del punto" per concatenare i nomi di campo nel percorso. Specificare il percorso come nome_colonna nella clausola WITH della funzione `OPENROWSET`.

Ecco un esempio di frammento di sintassi:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Per impostazione predefinita, la funzione `OPENROWSET` abbina il nome e il percorso del campo di origine ai nomi di colonna specificati nella clausola WITH. Gli elementi contenuti a livelli di annidamento diversi all'interno dello stesso file Parquet di origine sono accessibili tramite la clausola WITH.

**Valori restituiti**

- La funzione restituisce un valore scalare, ad esempio int, decimal e varchar, dall'elemento specificato e nel percorso specificato per tutti i tipi Parquet che non sono inclusi nel gruppo di tipi annidati.
- Se il percorso punta a un elemento che è di un tipo annidato, la funzione restituisce un frammento JSON a partire dal primo elemento nel percorso specificato. Il frammento JSON è di tipo varchar(8000).
- Se la proprietà non si trova in corrispondenza dell'elemento column_name specificato, la funzione restituisce un errore.
- Se la proprietà non si trova in corrispondenza dell'elemento column_path specificato, in base alla [modalità Path](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), la funzione restituisce un errore se in modalità strict o Null se in modalità lax.

Per esempi di query, vedere la sezione Accesso agli elementi di colonne annidate nell'articolo [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md#read-properties-from-nested-object-columns).

#### <a name="access-elements-from-repeated-columns"></a>Accesso agli elementi di colonne ripetute

Per accedere agli elementi di una colonna ripetuta, ad esempio un elemento di una matrice o di una mappa, usare la funzione [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per ogni elemento scalare che è necessario proiettare e fornire:

- Come primo parametro, una colonna annidata o ripetuta
- Come secondo parametro, un [percorso JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) che specifica l'elemento o la proprietà a cui accedere

Per accedere a elementi non scalari di una colonna ripetuta, usare la funzione [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per ogni elemento non scalare che è necessario proiettare e fornire:

- Come primo parametro, una colonna annidata o ripetuta
- Come secondo parametro, un [percorso JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) che specifica l'elemento o la proprietà a cui accedere

Vedere il frammento di sintassi seguente:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Per gli esempi di query per l'accesso agli elementi di colonne ripetute, vedere l'articolo [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md#access-elements-from-repeated-columns).

## <a name="query-samples"></a>Esempi di query

Le query di esempio forniscono maggiori informazioni sull'esecuzione di query su vari tipi di dati.

### <a name="tools"></a>Strumenti

Gli strumenti necessari per eseguire query:
    - Azure Synapse Studio (anteprima)
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Configurazione demo

Il primo passaggio consiste nel **creare un database** in cui verranno eseguite le query. Si inizializzeranno quindi gli oggetti eseguendo uno [script di configurazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. 

Questo script di configurazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati per leggere i dati in questi esempi.

> [!NOTE]
> I database vengono usati solo per la visualizzazione di metadati, non per i dati effettivi.  Prendere nota del nome del database usato, perché sarà necessario in un secondo momento.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Dati demo forniti

I dati demo contengono i set di dati seguenti:

- Taxi di New York: dati sui viaggi e parte del set di dati pubblico di New York in formato CSV e Parquet
- Set di dati sulla popolazione in formato CSV
- File parquet di esempio con colonne annidate
- Libri in formato JSON

| Percorso della cartella                                                  | Descrizione                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Cartella padre per i dati in formato CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Cartelle con file di dati della popolazione in formati CSV diversi. |
| /csv/taxi/                                                   | Cartella con i file di dati pubblici di New York in formato CSV              |
| /parquet/                                                    | Cartella padre per i dati in formato Parquet                     |
| /parquet/taxi                                                | File di dati pubblici di New York in formato Parquet, partizionati per anno e mese tramite lo schema di partizionamento Hive/Hadoop. |
| /parquet/nested/                                             | File parquet di esempio con colonne annidate                     |
| /json/                                                       | Cartella padre per i dati in formato JSON                        |
| /json/books/                                                 | File JSON con i dati dei manuali                                   |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come eseguire una query su tipi di file diversi e su come creare e usare le viste, vedere gli articoli seguenti:

- [Eseguire query su file CSV](query-single-csv-file.md)
- [Eseguire query su file Parquet](query-parquet-files.md)
- [Eseguire query su file JSON](query-json-files.md)
- [Eseguire query su valori annidati](query-parquet-nested-types.md)
- [Eseguire query su cartelle e più file CSV](query-folders-multiple-csv-files.md)
- [Usare i metadati dei file nelle query](query-specific-files.md)
- [Creare e usare viste](create-use-views.md)
