---
title: Eseguire query sui dati in Azure Data Lake usando Azure Esplora dati
description: Informazioni su come eseguire query sui dati in Azure Data Lake usando Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: ef4dfc4370c71eac1978a6f3535b571a5e6009b5
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950147"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Eseguire query sui dati in Azure Data Lake usando Esplora dati di Azure (anteprima)

Azure Data Lake Storage è una soluzione data Lake altamente scalabile ed economica per Big Data Analytics. Combina la potenza di un file system ad alte prestazioni con caratteristiche che permettono di ottenere rapidamente informazioni dettagliate in modo scalabile ed economico. Data Lake Storage Gen2 estende le funzionalità di Archiviazione BLOB di Azure ed è ottimizzato per i carichi di lavoro di analisi.
 
Azure Esplora dati si integra con l'archiviazione BLOB di Azure e Azure Data Lake Storage Gen2, offrendo accesso rapido, memorizzato nella cache e indicizzato ai dati nel Lake. È possibile analizzare ed eseguire query sui dati nel lago senza inserimenti prima in Azure Esplora dati. È anche possibile eseguire query su dati di Lake native inseriti e non inseriti simultaneamente.  

> [!TIP]
> Per ottimizzare le prestazioni delle query è necessario inserire i dati in Esplora dati di Azure. La possibilità di eseguire query sui dati in Azure Data Lake Storage Gen2 senza inserimento precedente deve essere utilizzata solo per i dati cronologici o per i dati raramente sottoposti a query.
 
## <a name="optimize-query-performance-in-the-lake"></a>Ottimizzare le prestazioni delle query nel Lake 

* Partizionare i dati per migliorare le prestazioni e ottimizzare i tempi di esecuzione delle query.
* Comprimi i dati per migliorare le prestazioni (gzip per la compressione ottimale, LZ4 per prestazioni ottimali).
* Usare l'archiviazione BLOB di Azure o Azure Data Lake Storage Gen2 con la stessa area del cluster di Azure Esplora dati. 

## <a name="create-an-external-table"></a>Creare una tabella esterna

 > [!NOTE]
 > Gli account di archiviazione attualmente supportati sono archiviazione BLOB di Azure o Azure Data Lake Storage Gen2. I formati di dati attualmente supportati sono JSON, CSV, TSV e txt.

1. Usare il `.create external table` comando per creare una tabella esterna in Azure Esplora dati. Altri comandi della tabella esterna, `.show`ad `.drop`esempio, `.alter` e, sono documentati nei [comandi della tabella esterna](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * È previsto un miglioramento delle prestazioni con il partizionamento più granulare. Ad esempio, le query su tabelle esterne con partizioni giornaliere avranno prestazioni migliori rispetto alle query con tabelle partizionate mensili.
    > * Quando si definisce una tabella esterna con partizioni, la struttura di archiviazione dovrebbe essere identica.
Se, ad esempio, la tabella è definita con una partizione DateTime nel formato AAAA/MM/GG (impostazione predefinita), il percorso del file di archiviazione URI deve essere *container1/aaaa/mm/gg/all_exported_blobs*. 

1. La tabella esterna è visibile nel riquadro sinistro dell'interfaccia utente Web

    ![tabella esterna nell'interfaccia utente Web](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Creare una tabella esterna con formato JSON

È possibile creare una tabella esterna con formato JSON. Per altre informazioni, vedere [comandi della tabella esterna](/azure/kusto/management/externaltables)

1. Usare il `.create external table` comando per creare una tabella denominata *ExternalTableJson*:

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Il formato JSON richiede un secondo passaggio della creazione del mapping alle colonne, come illustrato di seguito. Nella query seguente creare un mapping JSON specifico denominato MappingName:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Autorizzazioni per tabelle esterne
 
* L'utente del database può creare una tabella esterna. L'autore della tabella diventa automaticamente l'amministratore della tabella.
* L'amministratore del cluster, del database o della tabella può modificare una tabella esistente.
* Qualsiasi utente o lettore del database può eseguire una query su una tabella esterna.
 
## <a name="query-an-external-table"></a>Eseguire query su una tabella esterna
 
Per eseguire una query su una tabella esterna `external_table()` , utilizzare la funzione e specificare il nome della tabella come argomento della funzione. Il resto della query è il linguaggio di query kusto standard.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> IntelliSense non è attualmente supportato nelle query della tabella esterna.

### <a name="query-an-external-table-with-json-format"></a>Eseguire query su una tabella esterna con formato JSON

Per eseguire una query su una tabella esterna con formato JSON `external_table()` , usare la funzione e specificare il nome della tabella e il nome del mapping come argomenti della funzione. Nella query seguente, se *MappingName* non è specificato, verrà usato un mapping creato in precedenza.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Eseguire query su dati esterni e inseriti insieme

È possibile eseguire query su tabelle esterne e tabelle dati inserite all'interno della stessa query. L' [`join`](/azure/kusto/query/joinoperator) utente [`union`](/azure/kusto/query/unionoperator) o la tabella esterna con dati aggiuntivi da Esplora dati di Azure, da SQL Server o da altre origini. Usare un [`let( ) statement`](/azure/kusto/query/letstatement) oggetto per assegnare un nome abbreviato a un riferimento a una tabella esterna.

Nell'esempio seguente, *Products* è una tabella di dati inserita e *ArchivedProducts* è una tabella esterna che contiene i dati nel Azure Data Lake storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Eseguire query su una tabella esterna *TaxiRides* nel cluster della Guida

Il set di dati di esempio *TaxiRides* contiene i dati relativi ai taxi di New York City di [NYC Taxi e limousine Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Creare una tabella esterna *TaxiRides* 

> [!NOTE]
> In questa sezione viene illustrata la query utilizzata per creare la tabella esterna *TaxiRides* nel cluster della *Guida* . Poiché questa tabella è già stata creata, è possibile ignorare questa sezione ed eseguire [query sui dati della tabella esterna *TaxiRides* ](#query-taxirides-external-table-data). 

1. La query seguente è stata usata per creare la tabella esterna *TaxiRides* nel cluster della guida. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. La tabella risultante è stata creata nel cluster della *Guida* :

    ![Tabella esterna TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Eseguire query sui dati della tabella esterna *TaxiRides* 

Accedere a [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) per eseguire una query sulla tabella esterna *TaxiRides* . 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Eseguire query su una tabella esterna *TaxiRides* senza partizionamento

[Eseguire questa query](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) sulla tabella esterna *TaxiRides* per rappresentare le corse per ogni giorno della settimana, nell'intero set di dati. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Questa query Mostra il giorno della settimana più occupato. Poiché i dati non sono partizionati, questa query potrebbe richiedere molto tempo per restituire i risultati (fino a diversi minuti).

![eseguire il rendering di una query non partizionata](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Eseguire query su una tabella esterna TaxiRides con il partizionamento 

[Eseguire questa query](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) sulla tabella esterna *TaxiRides* mostrando i tipi di taxi cab (giallo o verde) usati nel gennaio 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Questa query usa il partizionamento, che consente di ottimizzare le prestazioni e il tempo di esecuzione delle query. La query Filtra in una colonna partizionata (pickup_datetime) e restituisce i risultati in pochi secondi.

![Esegui rendering query partizionata](media/data-lake-query-data/taxirides-with-partition.png)
  
È possibile scrivere query aggiuntive da eseguire nella tabella esterna *TaxiRides* e ottenere ulteriori informazioni sui dati. 

## <a name="next-steps"></a>Passaggi successivi

Eseguire query sui dati nel Azure Data Lake usando Esplora dati di Azure. Informazioni su come [scrivere query](write-queries.md) e derivare informazioni aggiuntive dai dati.
