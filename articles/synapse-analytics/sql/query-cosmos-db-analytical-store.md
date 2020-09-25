---
title: Eseguire query sui dati Azure Cosmos DB usando SQL su richiesta nel collegamento sinapsi di Azure (anteprima)
description: In questo articolo si apprenderà come eseguire query Azure Cosmos DB usando SQL su richiesta nel collegamento sinapsi di Azure (anteprima).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 8dd6ab5bcb42765c995e8cd767358be5e62aa0b6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288394"
---
# <a name="query-azure-cosmos-db-data-using-sql-on-demand-in-azure-synapse-link-preview"></a>Eseguire query sui dati Azure Cosmos DB usando SQL su richiesta nel collegamento sinapsi di Azure (anteprima)

SQL Server (in precedenza SQL su richiesta) consente di analizzare i dati nei contenitori Azure Cosmos DB abilitati con il [collegamento sinapsi di Azure](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) quasi in tempo reale senza influito sulle prestazioni dei carichi di lavoro transazionali. Offre una nota sintassi T-SQL per eseguire query sui dati dall' [Archivio analitico](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e la connettività integrata a un'ampia gamma di strumenti di query ad hoc e BI tramite l'interfaccia t-SQL.

> [!NOTE]
> Il supporto per l'esecuzione di query Azure Cosmos DB archivio analitico con SQL su richiesta è attualmente disponibile in anteprima controllata. 

Per eseguire query Azure Cosmos DB, la superficie di attacco di [selezione](/sql/t-sql/queries/select-transact-sql.md?view=sql-server-ver15&preserve-view=true) completa è supportata tramite la funzione [OPENROWSET](develop-openrowset.md) , inclusa la maggior parte degli [operatori e delle funzioni SQL](overview-features.md). È anche possibile archiviare i risultati della query che legge i dati da Azure Cosmos DB insieme ai dati nell'archivio BLOB di Azure o Azure Data Lake Storage usando [Crea tabella esterna come SELECT](develop-tables-cetas.md#cetas-in-sql-on-demand). Attualmente non è possibile archiviare i risultati delle query su richiesta SQL per Azure Cosmos DB usando [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

In questo articolo si apprenderà come scrivere una query usando SQL su richiesta che eseguirà query sui dati di contenitori di Azure Cosmos DB che sono abilitati per il collegamento sinapsi. È quindi possibile ottenere altre informazioni sulla creazione di visualizzazioni su richiesta SQL su Azure Cosmos DB contenitori e sulla connessione ai modelli di Power BI in [questa](./tutorial-data-analyst.md) esercitazione. 

## <a name="overview"></a>Panoramica

Per supportare l'esecuzione di query e l'analisi dei dati in Azure Cosmos DB archivio analitico, SQL su richiesta usa la `OPENROWSET` sintassi seguente:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ]
```

La stringa di connessione Azure Cosmos DB specifica il nome dell'account di Azure Cosmos DB, il nome del database, la chiave master dell'account del database e un nome di area facoltativo per il `OPENROWSET` funzionamento. Il formato della stringa di connessione è il seguente:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Il nome del contenitore Azure Cosmos DB viene specificato senza virgolette nella `OPENROWSET` sintassi. Se il nome del contenitore contiene caratteri speciali, ad esempio un trattino "-", il nome deve essere racchiuso tra parentesi quadre `[]` nella `OPENROWSET` sintassi.

> [!NOTE]
> SQL su richiesta non supporta l'esecuzione di query Azure Cosmos DB archivio transazionale.

## <a name="sample-data-set"></a>Set di dati campione

Gli esempi in questo articolo si basano sui dati del [Centro europeo per la prevenzione delle malattie e il controllo (ECDC) COVID-19 Cases](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) e [COVID-19 Open Research set di dati (Cord-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

È possibile visualizzare la licenza e la struttura dei dati in queste pagine e scaricare i dati di esempio per i set di dati [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) e [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Per seguire la procedura illustrata in questo articolo come eseguire query sui dati di Cosmos DB con SQL su richiesta, assicurarsi di creare le risorse seguenti:
* Un account di database di Azure Cosmos DB con il [collegamento sinapsi abilitato](../../cosmos-db/configure-synapse-link.md)
* Un database Azure Cosmos DB denominato `covid`
* Sono stati caricati due Azure Cosmos DB contenitori denominati `EcdcCases` e `Cord19` con i set di dati di esempio precedenti.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Esplorazione dei dati di Azure Cosmos DB con inferenza automatica dello schema

Il modo più semplice per esplorare i dati in Azure Cosmos DB consiste nel sfruttare la funzionalità di inferenza automatica dello schema. Omettendo la `WITH` clausola dall' `OPENROWSET` istruzione, è possibile indicare a SQL su richiesta di rilevare automaticamente (dedurre) lo schema dell'archivio analitico del contenitore Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
Nell'esempio precedente si sta indicando a SQL su richiesta di connettersi al `covid` database in Azure Cosmos DB account `MyCosmosDbAccount` autenticato usando la chiave di Azure Cosmos DB (fittizio nell'esempio precedente). Viene quindi eseguito l'accesso all' `EcdcCases` Archivio analitico del contenitore nell' `West US 2` area. Poiché non è presente alcuna proiezione di proprietà specifiche, `OPENROWSET` la funzione restituirà tutte le proprietà dagli elementi Azure Cosmos DB.

Se è necessario esplorare i dati dall'altro contenitore nello stesso database di Azure Cosmos DB, è possibile usare la stessa stringa di connessione e il contenitore di riferimento necessario come terzo parametro:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Specifica in modo esplicito lo schema

Sebbene la funzionalità di inferenza automatica dello schema in `OPENROWSET` fornisca un querience semplice e facile da utilizzare, gli scenari aziendali potrebbero richiedere di specificare in modo esplicito lo schema per le proprietà rilevanti di sola lettura dei dati Azure Cosmos DB.

`OPENROWSET` consente di specificare in modo esplicito le proprietà che si desidera leggere dai dati nel contenitore e di specificare i relativi tipi di dati. Si supponga di aver importato alcuni dati dal [set di dati COVID di ECDC](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) con la seguente struttura in Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Questi documenti JSON flat in Azure Cosmos DB possono essere rappresentati come un set di righe e colonne in sinapsi SQL. `OPENROWSET` la funzione consente di specificare un subset di proprietà che si desidera leggere e i tipi di colonna esatti nella `WITH` clausola:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Il risultato di questa query potrebbe essere simile al seguente:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Per ulteriori informazioni sui tipi SQL da utilizzare per Azure Cosmos DB valore, esaminare le [regole per i mapping dei tipi SQL](#azure-cosmos-db-to-sql-type-mappings) alla fine dell'articolo.

## <a name="querying-nested-objects-and-arrays"></a>Esecuzione di query su oggetti e matrici annidati

Azure Cosmos DB consente di rappresentare modelli di dati più complessi componendoli come oggetti annidati o matrici. La funzionalità di sincronizzazione automatica del collegamento sinapsi per Azure Cosmos DB gestisce la rappresentazione dello schema nell'archivio analitico, che include la gestione dei tipi di dati annidati che consentono di eseguire query avanzate da SQL su richiesta.

Il set di dati [Cord-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) , ad esempio, contiene documenti JSON che seguono la struttura seguente:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Gli oggetti annidati e le matrici in Azure Cosmos DB sono rappresentati come stringhe JSON nel risultato della query quando vengono `OPENROWSET` letti dalla funzione. Un'opzione per leggere i valori da questi tipi complessi come colonne SQL consiste nell'usare funzioni JSON SQL:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

Il risultato di questa query potrebbe essere simile al seguente:

| title | authors | first_autor_name |
| --- | --- | --- |
| Informazioni supplementari su Eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

In alternativa, è possibile specificare i percorsi dei valori annidati negli oggetti quando si utilizza la `WITH` clausola:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Altre informazioni sull'analisi di [tipi di dati complessi nel collegamento sinapsi](../how-to-analyze-complex-schema.md) e [nelle strutture annidate in SQL su richiesta](query-parquet-nested-types.md).

> [!IMPORTANT]
> Se vengono visualizzati caratteri imprevisti nel testo, `MÃƒÂ©lade` ad esempio anziché `Mélade` , le regole di confronto del database non sono impostate sulle regole di confronto [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Modificare le regole di confronto del database](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) in alcune regole di confronto UTF8 usando alcune istruzioni SQL come `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .


## <a name="flattening-nested-arrays"></a>Flating di matrici annidate

Azure Cosmos DB dati potrebbero avere sottomatrici annidate come la matrice dell'autore dal set di dati [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

In alcuni casi, potrebbe essere necessario "unire" le proprietà dall'elemento principale (metadati) con tutti gli elementi della matrice (autori). SQL su richiesta consente di rendere flat le strutture annidate applicando `OPENJSON` la funzione sulla matrice annidata:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Il risultato di questa query potrebbe essere simile al seguente:

| title | authors | first | last | associazione |
| --- | --- | --- | --- | --- |
| Informazioni supplementari su Eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Informazioni supplementari su Eco-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Informazioni supplementari su Eco-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Informazioni supplementari su Eco-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Se vengono visualizzati caratteri imprevisti nel testo, `MÃƒÂ©lade` ad esempio anziché `Mélade` , le regole di confronto del database non sono impostate sulle regole di confronto [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Modificare le regole di confronto del database](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) in alcune regole di confronto UTF8 usando alcune istruzioni SQL come `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB ai mapping dei tipi SQL

È importante notare che, anche se Azure Cosmos DB archivio transazionale è indipendente dallo schema, l'archivio analitico è schematizzato per ottimizzare le prestazioni delle query analitiche. Con la funzionalità di sincronizzazione automatica del collegamento sinapsi, Azure Cosmos DB gestisce la rappresentazione dello schema nell'archivio analitico, inclusa la gestione dei tipi di dati annidati. Poiché SQL su richiesta esegue query nell'archivio analitico, è importante comprendere come eseguire il mapping di Azure Cosmos DB tipi di dati di input ai tipi di dati SQL.

Gli account Azure Cosmos DB dell'API SQL (Core) supportano i tipi di proprietà JSON di Number, String, Boolean, null, oggetto annidato o matrice. Se si usa la clausola in, è necessario scegliere i tipi SQL che corrispondono a questi tipi JSON `WITH` `OPENROWSET` . Vedere sotto i tipi di colonna SQL da usare per diversi tipi di proprietà in Azure Cosmos DB.

| Tipo di proprietà Azure Cosmos DB | Tipo di colonna SQL |
| --- | --- |
| Boolean | bit |
| Integer | bigint |
| Decimal | float |
| string | varchar (regole di confronto del database UTF8) |
| Data/ora (stringa formattata ISO) | varchar (30) |
| Data/ora (timestamp Unix) | bigint |
| Null | `any SQL type` 
| Oggetto annidato o matrice | varchar (max) (regole di confronto del database UTF8), serializzate come testo JSON |

Per eseguire query Azure Cosmos DB account del tipo di API Mongo DB, è possibile ottenere altre informazioni sulla rappresentazione dello schema con fedeltà completa nell'archivio analitico e i nomi di proprietà estese da usare [qui](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- [Procedura: creare e usare le visualizzazioni in SQL su richiesta](create-use-views.md) 
- [Esercitazione sulla creazione di visualizzazioni su richiesta SQL su Azure Cosmos DB e sulla connessione a modelli di Power BI tramite DirectQuery](./tutorial-data-analyst.md)
