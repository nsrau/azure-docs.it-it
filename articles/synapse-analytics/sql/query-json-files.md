---
title: Eseguire query su file JSON con SQL su richiesta (anteprima)
description: Questa sezione illustra come leggere i file JSON con SQL su richiesta in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 04b2d7842222426010b76a1a7ed4c72ee74e3d87
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489725"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Eseguire query su file JSON con SQL su richiesta (anteprima) in Azure Synapse Analytics

Questo articolo illustra come scrivere una query usando SQL su richiesta (anteprima) in Azure Synapse Analytics. L'obiettivo della query è leggere i file JSON tramite [OPENROWSET](develop-openrowset.md). 
- File JSON standard in cui più documenti JSON vengono archiviati come matrice JSON.
- File JSON delimitati da righe, in cui i documenti JSON sono separati da caratteri di nuova riga. Le estensioni comuni per questi tipi di file sono `jsonl` , `ldjson` e `ndjson` .

## <a name="read-json-documents"></a>Leggere documenti JSON

Il modo più semplice per vedere il contenuto del file JSON consiste nel fornire l'URL del file per la `OPENROWSET` funzione, specificare CSV `FORMAT` e impostare i valori `0x0b` per `fieldterminator` e `fieldquote` . Se è necessario leggere i file JSON delimitati da righe, questo è sufficiente. Se si dispone di un file JSON classico, è necessario impostare i valori `0x0b` per `rowterminator` . `OPENROWSET`la funzione analizzerà JSON e restituirà tutti i documenti nel formato seguente:

| doc |
| --- |
|{"date_rep": "2020-07-24", "giorno": 24, "mese": 7, "anno": 2020, "case": 3, "decessioni": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25", "giorno": 25, "mese": 7, "anno": 2020, "case": 7, "decessioni": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "giorno": 26, "mese": 7, "anno": 2020, "case": 4, "decessioni": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "giorno": 27, "mese": 7, "anno": 2020, "case": 8, "decessioni": 0, "geo_id": "AF"}|

Se il file è disponibile pubblicamente o se l'identità del Azure AD può accedere a questo file, dovrebbe essere possibile visualizzare il contenuto del file usando la query come quella mostrata negli esempi seguenti.

### <a name="read-json-files"></a>Leggere file JSON

La query di esempio seguente legge i file JSON e i file JSON delimitati da riga e restituisce ogni documento come riga separata.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Questa query restituirà ogni documento JSON come riga separata del set di risultati. Verificare che sia possibile accedere a questo file. Se il file è protetto con la chiave SAS o l'identità personalizzata, è necessario configurare le [credenziali a livello di server per l'accesso SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

### <a name="data-source-usage"></a>Utilizzo dell'origine dati

Nell'esempio precedente viene usato il percorso completo del file. In alternativa, è possibile creare un'origine dati esterna con il percorso che punta alla cartella radice dell'archiviazione e usare tale origine dati e il percorso relativo del file nella `OPENROWSET` funzione:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Se un'origine dati è protetta con la chiave SAS o l'identità personalizzata, è possibile configurare l' [origine dati con le credenziali con ambito database](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

Nelle sezioni seguenti è possibile vedere come eseguire query su vari tipi di file JSON.

## <a name="parse-json-documents"></a>Analizzare i documenti JSON

Nelle query degli esempi precedenti vengono restituiti tutti i documenti JSON come una singola stringa in una riga separata del set di risultati. È possibile usare `JSON_VALUE` le funzioni e `OPENJSON` per analizzare i valori nei documenti JSON e restituirli come valori relazionali, come illustrato nell'esempio seguente:

| Data \_ Rep | cases | ID geografico \_ |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Documento JSON di esempio

Gli esempi di query leggono i file *JSON* contenenti documenti con la struttura seguente:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Se questi documenti vengono archiviati come JSON delimitati da righe, è necessario impostare `FIELDTERMINATOR` e `FIELDQUOTE` su 0x0B. Se si dispone di un formato JSON standard, è necessario impostare `ROWTERMINATOR` su 0x0B.

### <a name="query-json-files-using-json_value"></a>Eseguire query sui file JSON usando JSON_VALUE

La query seguente illustra come usare [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare i valori scalari (titolo, autore) da documenti JSON:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

### <a name="query-json-files-using-openjson"></a>Eseguire query sui file JSON usando OPENJSON

Nella query seguente viene usato il comando [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Recupererà le statistiche di COVID segnalate in Serbia:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```

## <a name="next-steps"></a>Passaggi successivi

Negli articoli successivi di questa serie verrà illustrato come:

- [Eseguire query su cartelle e file multipli](query-folders-multiple-csv-files.md)
- [Creare e usare viste](create-use-views.md)
