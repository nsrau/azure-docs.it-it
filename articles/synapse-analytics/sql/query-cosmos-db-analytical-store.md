---
title: Eseguire query sui dati Azure Cosmos DB usando un pool SQL senza server nel collegamento sinapsi di Azure (anteprima)
description: Questo articolo illustra come eseguire query Azure Cosmos DB usando un pool SQL senza server nel collegamento sinapsi di Azure (anteprima).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 2ffc524c14b9ba281d7e386f7f8c726093f11dbf
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661019"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Eseguire query sui dati Azure Cosmos DB con pool SQL senza server nel collegamento sinapsi di Azure (anteprima)

Il pool SQL senza server di sinapsi consente di analizzare i dati nei contenitori Azure Cosmos DB abilitati con il [collegamento sinapsi di Azure](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) in tempo quasi reale senza compromettere le prestazioni dei carichi di lavoro transazionali. Offre una nota sintassi T-SQL per eseguire query sui dati dall' [Archivio analitico](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e la connettività integrata a un'ampia gamma di strumenti di query ad hoc e BI tramite l'interfaccia t-SQL.

Per eseguire query Azure Cosmos DB, la superficie di attacco di [selezione](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) completa è supportata tramite la funzione [OPENROWSET](develop-openrowset.md) , inclusa la maggior parte degli [operatori e delle funzioni SQL](overview-features.md). È anche possibile archiviare i risultati della query che legge i dati da Azure Cosmos DB insieme ai dati nell'archivio BLOB di Azure o Azure Data Lake Storage usando [Crea tabella esterna come SELECT](develop-tables-cetas.md#cetas-in-serverless-sql-pool). Attualmente non è possibile archiviare i risultati delle query del pool SQL senza server per Azure Cosmos DB usando CETAS. 

In questo articolo si apprenderà come scrivere una query con un pool SQL senza server che eseguirà query sui dati da contenitori di Azure Cosmos DB che sono abilitati per il collegamento sinapsi. È quindi possibile ottenere altre informazioni sulla creazione di viste del pool SQL senza server su Azure Cosmos DB contenitori e sulla connessione ai modelli di Power BI in [questa](./tutorial-data-analyst.md) esercitazione. 

> [!IMPORTANT]
> Questa esercitazione usa un contenitore con uno [schema Azure Cosmos DB ben definito](../../cosmos-db/analytical-store-introduction.md#schema-representation). L'esperienza di query fornita dal pool SQL senza server per un [Azure Cosmos DB schema di fedeltà completa](#full-fidelity-schema) è un comportamento temporaneo che cambierà in base ai commenti in anteprima. Non fare affidamento sullo schema del set di risultati della `OPENROWSET` funzione senza la `WITH` clausola che legge i dati da un contenitore con uno schema di fedeltà completa perché l'esperienza di query potrebbe essere allineata con e modificare in base allo schema ben definito. Inviare commenti e suggerimenti nel [Forum di commenti e suggerimenti su Azure sinapsi Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics) o contattare il [team del prodotto del collegamento sinapsi](mailto:cosmosdbsynapselink@microsoft.com) per fornire commenti e suggerimenti.

## <a name="overview"></a>Panoramica

Per supportare l'esecuzione di query e l'analisi dei dati in Azure Cosmos DB archivio analitico, il pool SQL senza server utilizza la `OPENROWSET` sintassi seguente:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

La stringa di connessione Azure Cosmos DB specifica il nome dell'account di Azure Cosmos DB, il nome del database, la chiave master dell'account del database e un nome di area facoltativo per il `OPENROWSET` funzionamento. 

> [!IMPORTANT]
> Assicurarsi di utilizzare le regole di confronto del database UTF-8 (ad esempio `Latin1_General_100_CI_AS_SC_UTF8` ) perché i valori stringa in Cosmos DB archivio analitico sono codificati come testo UTF-8.
> Mancata corrispondenza tra la codifica del testo nel file e le regole di confronto potrebbero causare errori di conversione del testo imprevisti.
> È possibile modificare facilmente le regole di confronto predefinite del database corrente usando l'istruzione T-SQL seguente: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

Il formato della stringa di connessione è il seguente:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Il nome del contenitore Azure Cosmos DB viene specificato senza virgolette nella `OPENROWSET` sintassi. Se il nome del contenitore contiene caratteri speciali, ad esempio un trattino "-", il nome deve essere racchiuso tra parentesi quadre `[]` nella `OPENROWSET` sintassi.

> [!NOTE]
> Il pool SQL senza server non supporta l'esecuzione di query Azure Cosmos DB archivio transazionale.

## <a name="sample-data-set"></a>Set di dati campione

Gli esempi in questo articolo si basano sui dati del [Centro europeo per la prevenzione delle malattie e il controllo (ECDC) COVID-19 Cases](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) e [COVID-19 Open Research set di dati (Cord-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

È possibile visualizzare la licenza e la struttura dei dati in queste pagine e scaricare i dati di esempio per i set di dati [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) e [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Per seguire la procedura illustrata in questo articolo come eseguire query Cosmos DB dati con un pool SQL senza server, assicurarsi di creare le risorse seguenti:
* Un account di database di Azure Cosmos DB con il [collegamento sinapsi abilitato](../../cosmos-db/configure-synapse-link.md)
* Un database Azure Cosmos DB denominato `covid`
* Sono stati caricati due Azure Cosmos DB contenitori denominati `EcdcCases` e `Cord19` con i set di dati di esempio precedenti.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Esplorazione dei dati di Azure Cosmos DB con inferenza automatica dello schema

Il modo più semplice per esplorare i dati in Azure Cosmos DB consiste nel sfruttare la funzionalità di inferenza automatica dello schema. Omettendo la `WITH` clausola dall' `OPENROWSET` istruzione, è possibile indicare al pool SQL senza server di rilevare automaticamente (dedurre) lo schema dell'archivio analitico del contenitore Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
Nell'esempio precedente, viene indicato al pool SQL senza server di connettersi al `covid` database in Azure Cosmos DB account `MyCosmosDbAccount` autenticato usando la chiave di Azure Cosmos DB (fittizio nell'esempio precedente). Viene quindi eseguito l'accesso all' `EcdcCases` Archivio analitico del contenitore nell' `West US 2` area. Poiché non è presente alcuna proiezione di proprietà specifiche, `OPENROWSET` la funzione restituirà tutte le proprietà dagli elementi Azure Cosmos DB. 

In presupposto che gli elementi in Cosmos DB contenitore dispongano delle `date_rep` `cases` proprietà, e `geo_id` , i risultati della query sono illustrati nella tabella seguente:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

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

Azure Cosmos DB consente di rappresentare modelli di dati più complessi componendoli come oggetti annidati o matrici. La funzionalità di sincronizzazione automatica del collegamento sinapsi per Azure Cosmos DB gestisce la rappresentazione dello schema nell'archivio analitico, che include la gestione dei tipi di dati annidati che consentono di eseguire query avanzate dal pool SQL senza server.

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

Altre informazioni sull'analisi di [tipi di dati complessi nel collegamento sinapsi](../how-to-analyze-complex-schema.md) e [nelle strutture annidate nel pool SQL senza server](query-parquet-nested-types.md).

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

In alcuni casi, potrebbe essere necessario "unire" le proprietà dall'elemento principale (metadati) con tutti gli elementi della matrice (autori). Il pool SQL senza server consente di rendere flat le strutture annidate applicando la `OPENJSON` funzione nella matrice annidata:

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

È importante notare che, anche se Azure Cosmos DB archivio transazionale è indipendente dallo schema, l'archivio analitico è schematizzato per ottimizzare le prestazioni delle query analitiche. Con la funzionalità di sincronizzazione automatica del collegamento sinapsi, Azure Cosmos DB gestisce la rappresentazione dello schema nell'archivio analitico, inclusa la gestione dei tipi di dati annidati. Poiché il pool SQL senza server esegue query nell'archivio analitico, è importante comprendere come eseguire il mapping di Azure Cosmos DB tipi di dati di input ai tipi di dati SQL.

Gli account Azure Cosmos DB dell'API SQL (Core) supportano i tipi di proprietà JSON di Number, String, Boolean, null, oggetto annidato o matrice. Se si usa la clausola in, è necessario scegliere i tipi SQL che corrispondono a questi tipi JSON `WITH` `OPENROWSET` . Vedere sotto i tipi di colonna SQL da usare per diversi tipi di proprietà in Azure Cosmos DB.

| Tipo di proprietà Azure Cosmos DB | Tipo di colonna SQL |
| --- | --- |
| Boolean | bit |
| Integer | bigint |
| Decimale | float |
| string | varchar (regole di confronto del database UTF8) |
| Data/ora (stringa formattata ISO) | varchar (30) |
| Data/ora (timestamp Unix) | bigint |
| Null | `any SQL type` 
| Oggetto annidato o matrice | varchar (max) (regole di confronto del database UTF8), serializzate come testo JSON |

## <a name="full-fidelity-schema"></a>Schema di fedeltà completa

Azure Cosmos DB schema di fedeltà completa registra sia i valori che i tipi di corrispondenza migliori per ogni proprietà in un contenitore.
`OPENROWSET` la funzione in un contenitore con schema Full-Fidelity fornisce sia il tipo che il valore effettivo in ogni cella. Si supponga che la query seguente legga gli elementi da un contenitore con lo schema di fedeltà completa:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

Il risultato di questa query restituirà i tipi e i valori formattati come testo JSON: 

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date": "2020-08-13"} | {"Int32": "254"} | {"String": "RS"} |
| {"date": "2020-08-12"} | {"Int32": "235"}| {"String": "RS"} |
| {"date": "2020-08-11"} | {"Int32": "316"} | {"String": "RS"} |
| {"date": "2020-08-10"} | {"Int32": "281"} | {"String": "RS"} |
| {"date": "2020-08-09"} | {"Int32": "295"} | {"String": "RS"} |
| {"String": "2020/08/08"} | {"Int32": "312"} | {"String": "RS"} |
| {"date": "2020-08-07"} | {"float64": "339.0"} | {"String": "RS"} |

Per ogni valore, è possibile visualizzare il tipo identificato in Cosmos DB elemento contenitore. La maggior parte dei valori per la `date_rep` proprietà contiene `date` valori, ma alcuni di essi vengono archiviati erroneamente come stringhe in Cosmos DB. Lo schema di fedeltà completa restituirà i valori tipizzati correttamente `date` e i valori formattati in modo errato `string` .
Il numero di case è un'informazione archiviata come `int32` valore, ma è presente un valore immesso come numero decimale. Questo valore è di `float64` tipo. Se sono presenti alcuni valori che superano il `int32` numero più alto, vengono archiviati come `int64` tipo. Tutti `geo_id` i valori in questo esempio vengono archiviati come `string` tipi.

> [!IMPORTANT]
> `OPENROWSET` la funzione senza `WITH` clausola espone entrambi i valori con i tipi previsti e i valori con tipi immessi in modo errato. Questa funzione è progettata per l'esplorazione dei dati e non per la creazione di report. Non analizzare i valori JSON restituiti da questa funzione per compilare i report e utilizzare la [clausola with](#querying-items-with-full-fidelity-schema) esplicita per creare i report.
> È consigliabile eseguire la pulizia dei valori con tipi non corretti nel contenitore Azure Cosmos DB per applicare la corecta nell'archivio analitico Full Fidelity. 

Per eseguire query Azure Cosmos DB account del tipo di API Mongo DB, è possibile ottenere altre informazioni sulla rappresentazione dello schema con fedeltà completa nell'archivio analitico e i nomi di proprietà estese da usare [qui](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="querying-items-with-full-fidelity-schema"></a>Esecuzione di query su elementi con schema con fedeltà completa

Quando si esegue una query sullo schema di fedeltà completa, è necessario specificare in modo esplicito il tipo SQL e il tipo di proprietà previsto Cosmos DB nella `WITH` clausola. Non utilizzare `OPENROWSET` `WITH` la clausola without nei report poiché il formato del set di risultati può essere modificato in anteprima in base ai commenti e suggerimenti.

Nell'esempio seguente si presuppone che `string` sia il tipo corretto per la `geo_id` proprietà e il `int32` tipo corretto per la `cases` proprietà:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

I valori per `geo_id` e con `cases` altri tipi verranno restituiti come `NULL` valori. Questa query fa riferimento solo all'oggetto `cases` con il tipo specificato nell'espressione ( `cases.int32` ).

Se sono presenti valori con altri tipi ( `cases.int64` , `cases.float64` ) che non possono essere puliti nel contenitore Cosmos DB, è necessario farvi riferimento in modo esplicito nella `WITH` clausola e combinare i risultati. Nella query seguente vengono aggregati sia `int32` , `int64` che `float64` archiviati nella `cases` colonna:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

In questo esempio il numero di case viene archiviato come `int32` valori, `int64` o `float64` e tutti i valori devono essere estratti per calcolare il numero di case per paese. 

## <a name="known-issues"></a>Problemi noti

- L'esperienza di query fornita da un pool SQL senza server per [Azure Cosmos DB lo schema di fedeltà completa](#full-fidelity-schema) è un comportamento temporaneo che verrà modificato in base al feedback dell'anteprima. Non fare affidamento sullo schema che `OPENROWSET` la funzione senza `WITH` clausola fornisce durante l'anteprima pubblica perché l'esperienza di query potrebbe essere allineata con uno schema ben definito in base ai suggerimenti dei clienti. Per fornire commenti e suggerimenti, contattare il [team del prodotto del collegamento sinapsi](mailto:cosmosdbsynapselink@microsoft.com) .
- Il pool SQL senza server non restituirà un errore in fase di compilazione se le `OPENROSET` regole di confronto della colonna non includono la codifica UTF-8. È possibile modificare facilmente le regole di confronto predefinite per tutte le `OPENROWSET` funzioni in esecuzione nel database corrente usando l'istruzione T-SQL seguente: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

Nella tabella seguente sono elencati i possibili errori e le azioni per la risoluzione dei problemi:

| Errore | Causa radice |
| --- | --- |
| Errori di sintassi:<br/> -Sintassi non corretta in prossimità di ' OpenRowset '<br/> - `...` non è un'opzione del provider BULK OPENROWSET riconosciuta.<br/> -Sintassi non corretta in prossimità `...` | Possibili cause principali<br/> -Non si usa ' CosmosDB ' come primo parametro,<br/> -Utilizzo di un valore letterale stringa anziché di un identificatore nel terzo parametro<br/> -Non specifica del terzo parametro (nome contenitore) |
| Si è verificato un errore nella stringa di connessione CosmosDB | -Account, database, chiave non specificata <br/> -È presente un'opzione nella stringa di connessione non riconosciuta.<br/> -Il punto e virgola `;` viene inserito alla fine della stringa di connessione |
| La risoluzione del percorso di CosmosDB non è riuscita con errore ' nome account errato ' o ' nome database errato ' | Il nome dell'account, il nome del database o il contenitore specificato non è stato trovato oppure non è stata abilitata l'archiviazione analitica o la raccolta specificata|
| La risoluzione del percorso di CosmosDB non è riuscita con errore ' valore segreto errato ' o ' Secret is null o Empty ' | La chiave dell'account non è valida o è mancante. |
| La colonna `column name` di tipo `type name` non è compatibile con il tipo di dati esterno `type name` | Il tipo di colonna specificato nella `WITH` clausola non corrisponde al tipo in Cosmos DB contenitore. Provare a modificare il tipo di colonna, come descritto nella sezione [Azure Cosmos DB ai mapping dei tipi SQL o al](#azure-cosmos-db-to-sql-type-mappings) tipo di utilizzo `VARCHAR` . |
| La colonna contiene `NULL` i valori in tutte le celle. | È probabile che il nome di colonna o l'espressione di percorso nella clausola sia errato `WITH` . Il nome della colonna (o l'espressione di percorso dopo il tipo di colonna) nella `WITH` clausola deve corrispondere a un nome di proprietà nella raccolta Cosmos DB. Il confronto fa **distinzione tra maiuscole**  e minuscole (ad esempio, `productCode` e `ProductCode` sono proprietà diverse). |

È possibile segnalare suggerimenti e problemi nella [pagina dei commenti](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)e suggerimenti su sinapsi di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- [Usare Power BI e il pool SQL senza server con il collegamento a sinapsi di Azure](../../cosmos-db/synapse-link-power-bi.md)
- [Procedura: creare e usare le visualizzazioni nel pool SQL senza server](create-use-views.md) 
- [Esercitazione sulla creazione di viste del pool SQL senza server su Azure Cosmos DB e sulla connessione a modelli di Power BI tramite DirectQuery](./tutorial-data-analyst.md)
