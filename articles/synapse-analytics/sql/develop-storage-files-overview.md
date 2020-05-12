---
title: Eseguire query sui file di archiviazione con SQL su richiesta (anteprima) all'interno di Synapse SQL
description: Questo articolo descrive come eseguire query sui file di archiviazione con le risorse di SQL su richiesta (anteprima) all'interno di Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 941fa8d2570d22b6c2a54de02a61b4a7ece2e632
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691885"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Eseguire query sui file di archiviazione con le risorse di SQL su richiesta (anteprima) all'interno di Synapse SQL

SQL su richiesta (anteprima) consente di eseguire query sui dati del data lake. Offre una superficie di attacco per query T-SQL che supporta le query su dati semistrutturati e non strutturati.

Per l'esecuzione di query, sono supportati gli aspetti di T-SQL seguenti:

- Superficie di attacco [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) completa, inclusa la maggioranza di funzioni, operatori SQL e così via.
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)), che crea una [tabella esterna](develop-tables-external-tables.md) e quindi esporta, in parallelo, i risultati di un'istruzione Transact-SQL SELECT in Archiviazione di Azure.

Per altre informazioni sulle funzionalità attualmente supportate e non, vedere l'articolo [Panoramica di SQL su richiesta](on-demand-workspace-overview.md).

Quando gli utenti di Azure AD eseguono query, per impostazione predefinita l'accesso agli account di archiviazione viene effettuato tramite il protocollo di autenticazione pass-through di Azure AD. Di conseguenza, gli utenti vengono rappresentati e le autorizzazioni vengono controllate a livello di archiviazione. È possibile [controllare l'accesso agli account di archiviazione](develop-storage-files-storage-access-control.md) in base a specifiche esigenze.

## <a name="extensions"></a>Estensioni

Per offrire un'esperienza uniforme per le query sul posto sui dati presenti nei file di Archiviazione di Azure, SQL su richiesta usa la funzione [OPENROWSET](develop-openrowset.md) con funzionalità aggiuntive:

- [Esecuzione di query su più file o cartelle](#query-multiple-files-or-folders)
- [Formato di file Parquet](#parquet-file-format)
- [Opzioni aggiuntive per l'uso di testo delimitato (carattere di terminazione del campo, carattere di terminatore della riga, carattere di escape)](#additional-options-for-working-with-delimited-text)
- [Lettura di un sottoinsieme selezionato di colonne](#read-a-chosen-subset-of-columns)
- [Inferenza dello schema](#schema-inference)
- [Funzione filename](#filename-function)
- [Funzione filepath](#filepath-function)
- [Uso di tipi complessi e strutture di dati annidate o ripetute](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Esecuzione di query su più file o cartelle

Per eseguire una query T-SQL su un set di file all'interno di una cartella o di un set di cartelle, considerandoli come singola entità o singolo set di righe, fornire il percorso di una cartella o di un modello (usando caratteri jolly) su un set di file o cartelle.

Sono applicabili le regole seguenti:

- I modelli possono essere presenti in parte di un percorso di directory o in un nome file.
- Nello stesso percorso di directory o nome file possono essere presenti diversi modelli.
- Nel caso di più caratteri jolly, i file all'interno di tutti i percorsi corrispondenti verranno inclusi nel set di file risultante.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Per esempi di utilizzo, vedere [Eseguire query su cartelle e più file](query-folders-multiple-csv-files.md).

### <a name="parquet-file-format"></a>Formato di file Parquet

Per eseguire query sui dati di origine Parquet, usare FORMAT = 'PARQUET'

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Per esempi di utilizzo, vedere l'articolo [Eseguire query su file Parquet](query-parquet-files.md).

### <a name="additional-options-for-working-with-delimited-text"></a>Opzioni aggiuntive per l'uso di testo delimitato

Questi parametri aggiuntivi sono stati introdotti per l'uso di file CSV (testo delimitato):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' - Specifica il carattere nel file usato per l'escape di se stesso e per tutti i valori dei delimitatori del file. Se seguito da un valore diverso da se stesso o da uno qualsiasi dei valori dei delimitatori, il carattere di escape viene eliminato durante la lettura del valore.
Il parametro ESCAPE_CHAR verrà applicato indipendentemente dal fatto che FIELDQUOTE sia o meno abilitato. Non verrà usato per l'escape del carattere virgolette singole. L'escape del carattere virgolette singole viene eseguito con le virgolette doppie, in linea con il comportamento dei file CSV di Excel.
- FIELDTERMINATOR ='field_terminator' - Specifica il carattere di terminazione del campo da usare. Il carattere di terminazione del campo predefinito è la virgola (" **,** ")
- ROWTERMINATOR ='row_terminator' - Specifica il carattere di terminazione della riga da usare. Il carattere di terminazione della riga predefinito è  **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Lettura di un sottoinsieme selezionato di colonne

Per specificare le colonne da leggere, è possibile fornire una clausola WITH facoltativa all'interno dell'istruzione OPENROWSET.

- Nel caso di file di dati CSV, per leggere tutte le colonne, specificare i nomi di colonna e i relativi tipi di dati. Se si vuole specificare un sottoinsieme di colonne, selezionare le colonne dai file di dati di origine in base a numeri ordinali. Le colonne verranno associate in base alla designazione dell'ordinale.
- Nel caso di file di dati Parquet, specificare i nomi di colonna che corrispondono a quelli dei file di dati di origine. Le colonne verranno associate per nome.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Per alcuni esempi, vedere [Leggere file CSV senza specificare tutte le colonne](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Inferenza dello schema

Omettendo la clausola WITH dall'istruzione OPENROWSET, è possibile indicare al servizio di rilevare automaticamente (dedurre) lo schema dai file sottostanti.

> [!NOTE]
> Attualmente questa opzione funziona solo per il formato di file PARQUET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

Assicurarsi che vengano usati i [tipi di dati derivati appropriati](best-practices-sql-on-demand.md#check-inferred-data-types) per ottenere prestazioni ottimali. 

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

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Uso di tipi complessi e strutture di dati annidate o ripetute

Per garantire un'esperienza uniforme quando si usano dati archiviati in tipi di dati annidati o ripetuti, ad esempio nei file [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types), in SQL su richiesta sono state aggiunte le estensioni seguenti.

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

Per accedere agli elementi annidati di una colonna annidata, ad esempio struct, usare la "notazione del punto" per concatenare i nomi di campo nel percorso. Specificare il percorso come column_name nella clausola WITH della funzione OPENROWSET.

Ecco un esempio di frammento di sintassi:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Per impostazione predefinita, la funzione OPENROWSET abbina il nome e il percorso del campo di origine ai nomi di colonna specificati nella clausola WITH. Gli elementi contenuti a livelli di annidamento diversi all'interno dello stesso file Parquet di origine sono accessibili tramite la clausola WITH.

**Valori restituiti**

- La funzione restituisce un valore scalare, ad esempio int, decimal e varchar, dall'elemento specificato e nel percorso specificato per tutti i tipi Parquet che non sono inclusi nel gruppo di tipi annidati.
- Se il percorso punta a un elemento che è di un tipo annidato, la funzione restituisce un frammento JSON a partire dal primo elemento nel percorso specificato. Il frammento JSON è di tipo varchar(8000).
- Se la proprietà non si trova in corrispondenza dell'elemento column_name specificato, la funzione restituisce un errore.
- Se la proprietà non si trova in corrispondenza dell'elemento column_path specificato, in base alla [modalità Path](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), la funzione restituisce un errore se in modalità strict o Null se in modalità lax.

Per esempi di query, vedere la sezione Accesso agli elementi di colonne annidate nell'articolo [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md#access-elements-from-nested-columns).

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come eseguire una query su tipi di file diversi e su come creare e usare le viste, vedere gli articoli seguenti:

- [Eseguire query su un singolo file CSV](query-single-csv-file.md)
- [Eseguire query su file Parquet](query-parquet-files.md)
- [Eseguire query su file JSON](query-json-files.md)
- [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md)
- [Eseguire query su cartelle e più file CSV](query-folders-multiple-csv-files.md)
- [Usare i metadati dei file nelle query](query-specific-files.md)
- [Creare e usare viste](create-use-views.md)
