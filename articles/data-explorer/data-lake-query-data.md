---
title: Query data in Azure Data Lake using Azure Data Explorer
description: Informazioni su come eseguire query sui dati in Azure Data Lake usando Azure Data Explorer.Learn how to query data in Azure Data Lake using Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161750"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Query data in Azure Data Lake using Azure Data Explorer

Azure Data Lake Storage è una soluzione data lake altamente scalabile ed economica per l'analisi dei Big Data. Combina la potenza di un file system ad alte prestazioni con la scalabilità massiccia e l'economia per aiutarti a velocizzare il tuo tempo per capire. Data Lake Storage Gen2 estende le funzionalità di Archiviazione BLOB di Azure ed è ottimizzato per i carichi di lavoro di analisi.
 
Azure Data Explorer si integra con Archiviazione BLOB di Azure e Archiviazione lake di Azure (Gen1 e Gen2), fornendo accesso rapido, memorizzato nella cache e indicizzato ai dati nel lake. È possibile analizzare ed eseguire query sui dati nel lake senza eseguire l'inserimento precedente in Azure Data Explorer.You can analyze and query data in the lake without prior ingestion into Azure Data Explorer. È inoltre possibile eseguire query sui dati lake nativi ingeriti e non instradati contemporaneamente.  

> [!TIP]
> Le migliori prestazioni delle query richiedono l'inserimento di dati in Azure Data Explorer.The best query performance impedes data iningon into Azure Data Explorer. La possibilità di eseguire query su dati esterni senza inserimento precedente deve essere utilizzata solo per i dati cronologici o i dati su cui viene eseguita raramente la query. [Ottimizzare le prestazioni delle query nel lake](#optimize-your-query-performance) per ottenere risultati ottimali.
 

## <a name="create-an-external-table"></a>Creare una tabella esterna

 > [!NOTE]
 > Gli account di archiviazione attualmente supportati sono Archiviazione BLOB di Azure o Archiviazione lake di Azure (Gen1 e Gen2).

1. Usare `.create external table` il comando per creare una tabella esterna in Esplora dati di Azure.Use the command to create an external table in Azure Data Explorer. Ulteriori comandi di `.show`tabella `.drop`esterna, ad esempio , e `.alter` sono documentati in [Comandi tabella esterna](/azure/kusto/management/externaltables).

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
    > * Si prevede un aumento delle prestazioni con un partizionamento più granulare. Ad esempio, le query su tabelle esterne con partizioni giornaliere avranno prestazioni migliori rispetto a quelle con tabelle partizionate mensili.
    > * Quando si definisce una tabella esterna con partizioni, si prevede che la struttura di archiviazione sia identica.
Ad esempio, se la tabella è definita con una partizione DateTime nel formato aaaa/MM/gg (impostazione predefinita), il percorso del file di archiviazione URI deve essere *container1/yyyy/MM/dd/all_exported_blobs*. 
    > * Se la tabella esterna è partizionata da una colonna datetime, includere sempre un filtro `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` temporale per un intervallo chiuso nella `ArchivedProducts | where Timestamp > ago(1h)` query (ad esempio, la query - - deve avere prestazioni migliori di questa (intervallo aperto) uno - ). 
    > * Tutti i formati di [inserimento supportati](ingest-data-overview.md#supported-data-formats) possono essere sottoposti a query utilizzando tabelle esterne.

1. La tabella esterna è visibile nel riquadro sinistro dell'interfaccia utente Web

    ![tabella esterna nell'interfaccia utente Web](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Creare una tabella esterna con formato jsonCreate an external table with json format

È possibile creare una tabella esterna con formato json. Per altre informazioni, vedere [Comandi di tabella esternaFor more information](/azure/kusto/management/externaltables) see External table commands

1. Utilizzare `.create external table` il comando per creare una tabella denominata *ExternalTableJson*:

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
 
1. Il formato Json richiede un secondo passaggio per la creazione del mapping alle colonne, come illustrato di seguito. Nella query seguente creare un mapping json specifico denominato *mappingName*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Autorizzazioni tabella esterna
 
* L'utente del database può creare una tabella esterna. L'autore della tabella diventa automaticamente l'amministratore della tabella.
* Il cluster, il database o l'amministratore della tabella può modificare una tabella esistente.
* Qualsiasi utente o lettore di database può eseguire query su una tabella esterna.
 
## <a name="query-an-external-table"></a>Eseguire una query su una tabella esterna
 
Per eseguire una query `external_table()` su una tabella esterna, utilizzare la funzione e specificare il nome della tabella come argomento della funzione. Il resto della query è il linguaggio di query Kusto standard.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense non è attualmente supportato nelle query di tabella esterna.

### <a name="query-an-external-table-with-json-format"></a>Eseguire una query su una tabella esterna con formato jsonQuery an external table with json format

Per eseguire una query su una `external_table()` tabella esterna in formato json, usare la funzione e fornire sia il nome della tabella che il nome del mapping come argomenti della funzione. Nella query seguente, se *mappingName* non è specificato, verrà utilizzato un mapping creato in precedenza.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Eseguire query insieme a dati esterni e ingeriti

È possibile eseguire query sia su tabelle esterne che su tabelle di dati ingerite all'interno della stessa query. L'utente [`join`](/azure/kusto/query/joinoperator) o [`union`](/azure/kusto/query/unionoperator) la tabella esterna con dati aggiuntivi da Azure Data Explorer, sql server o altre origini. Utilizzare [`let( ) statement`](/azure/kusto/query/letstatement) a per assegnare un nome a sintassi abbreviata a un riferimento a una tabella esterna.

Nell'esempio seguente Products è una tabella di dati ingerita e ArchivedProducts è una tabella esterna che contiene dati in Azure Data Lake Storage Gen2:In the example below, *Products* is an ingested data table and *ArchivedProducts* is an external table that contains data in the Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Query *TaxiRides* tabella esterna nel cluster di aiuto

Il set di dati di esempio *TaxiRides* contiene i dati dei taxi di New York City provenienti da [NYC Taxi and Limousine Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Creare una tabella esterna *TaxiRidesCreate* external table TaxiRides 

> [!NOTE]
> In questa sezione viene illustrata la query utilizzata per creare la tabella esterna *TaxiRides* nel cluster *della Guida.* Poiché questa tabella è già stata creata, è possibile ignorare questa sezione ed eseguire [query *TaxiRides* dati della tabella esterna](#query-taxirides-external-table-data). 

1. La query seguente è stata utilizzata per creare la tabella esterna *TaxiRides* nel cluster della Guida. 

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
1. La tabella risultante è stata creata nel cluster *della Guida:The* resulting table was created in the help cluster:

    ![TaxiRides tavolo esterno](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Eseguire query sui dati della tabella esterna *TaxiRidesQuery TaxiRides* external table data 

Accedere [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) per eseguire una query sulla tabella esterna *TaxiRides.* 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Query *TaxiRides* tabella esterna senza partizionamento

[Eseguire questa query](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) sulla tabella esterna *TaxiRides* per rappresentare le corse per ogni giorno della settimana, nell'intero set di dati. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Questa query mostra il giorno più trafficato della settimana. Poiché i dati non sono partizionati, questa query potrebbe richiedere molto tempo per restituire i risultati (fino a diversi minuti).

![eseguire il rendering di query non partizionate](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Query TaxiRides tabella esterna con partizionamento 

[Eseguire questa query](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) sulla tabella esterna *TaxiRides* che mostra i tipi di taxi (giallo o verde) utilizzato nel gennaio del 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Questa query usa il partizionamento, che ottimizza il tempo di query e le prestazioni. La query filtra in base a una colonna partizionata (pickup_datetime) e restituisce i risultati in pochi secondi.

![eseguire il rendering di query partizionate](media/data-lake-query-data/taxirides-with-partition.png)
  
È possibile scrivere query aggiuntive da eseguire sulla tabella esterna *TaxiRides* e ottenere ulteriori informazioni sui dati. 

## <a name="optimize-your-query-performance"></a>Ottimizzare le prestazioni delle queryOptimize your query performance

Ottimizzare le prestazioni delle query nel lake utilizzando le procedure consigliate seguenti per l'esecuzione di query su dati esterni. 
 
### <a name="data-format"></a>Formato dati
 
Utilizzare un formato a colonne per le query analitiche in quanto:
* È possibile leggere solo le colonne rilevanti per una query. 
* Le tecniche di codifica delle colonne possono ridurre in modo significativo le dimensioni dei dati.  
Azure Data Explorer supporta i formati parquet e ORC columnar. Il formato parquet è suggerito grazie all'implementazione ottimizzata. 
 
### <a name="azure-region"></a>Area di Azure 
 
Verificare che i dati esterni si trovino nella stessa area di Azure del cluster azure Data Explorer.Acertain that external data resides in the same Azure region as your Azure Data Explorer cluster. In questo modo si riducono i costi e i tempi di recupero dei dati.
 
### <a name="file-size"></a>Dimensione del file
 
La dimensione ottimale del file è di centinaia di Mb (fino a 1 Gb) per file. Evitare molti file di piccole dimensioni che richiedono un sovraccarico non necessario, ad esempio un processo di enumerazione dei file più lento e un utilizzo limitato del formato a colonne. Si noti che il numero di file deve essere maggiore del numero di core della CPU nel cluster di Azure Data Explorer.Note that the number of files should be greater than the number of CPU cores in your Azure Data Explorer cluster. 
 
### <a name="compression"></a>Compressione
 
Utilizzare la compressione per ridurre la quantità di dati recuperati dall'archiviazione remota. Per il formato Parquet, utilizzare il meccanismo di compressione interno Parquet che comprime i gruppi di colonne separatamente, consentendo così di leggerli separatamente. Per convalidare l'uso del meccanismo di compressione, verificare che i file siano denominati come segue: "<filename>.gz.parquet" o ".snappy.parquet"<filename>al contrario di "<filename>.parquet.gz"). 
 
### <a name="partitioning"></a>Partizionamento
 
Organizzare i dati utilizzando partizioni "cartella" che consente alla query di ignorare i percorsi irrilevanti. Quando si pianifica il partizionamento, considerare le dimensioni del file e i filtri comuni nelle query, ad esempio timestamp o ID tenant.
 
### <a name="vm-size"></a>Dimensioni macchina virtuale
 
Selezionare SKU di VM con più core e una maggiore velocità effettiva della rete (la memoria è meno importante). Per altre [informazioni, vedere Selezionare lo SKU della macchina virtuale corretto per il cluster Azure Data Explorer.For](manage-cluster-choose-sku.md)more information see Select the correct VM SKU for your Azure Data Explorer cluster.

## <a name="next-steps"></a>Passaggi successivi

Query your data in the Azure Data Lake using Azure Data Explorer. Informazioni su [come scrivere query](write-queries.md) e ricavarne informazioni aggiuntive dai dati.
