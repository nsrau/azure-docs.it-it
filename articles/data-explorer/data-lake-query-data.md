---
title: Eseguire query sui dati in Azure Data Lake usando Esplora dati di Azure
description: Informazioni su come eseguire query sui dati in Azure Data Lake usando Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453177"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Eseguire query sui dati in Azure Data Lake usando Esplora dati di Azure (anteprima)

Archiviazione di Azure Data Lake è una soluzione di lake dati estremamente scalabile e conveniente per analitica dei big Data. Combina la potenza di un file system ad alte prestazioni con caratteristiche che permettono di ottenere rapidamente informazioni dettagliate in modo scalabile ed economico. Data Lake Storage Gen2 estende le funzionalità di Archiviazione BLOB di Azure ed è ottimizzato per i carichi di lavoro di analisi.
 
Esplora i dati di Azure si integra con archiviazione Blob di Azure e Azure Data Lake Storage Gen2, offrono un rapido, memorizzato nella cache e indicizzati di accesso ai dati nel lake. Puoi analizzare ed eseguire query sui dati nel lake senza l'inserimento precedente in Esplora dati di Azure. È anche possibile eseguire query tra i dati inseriti e uningested lake nativo contemporaneamente.  

> [!TIP]
> Le migliori prestazioni di query richiede l'inserimento di dati in Esplora dati di Azure. La possibilità di eseguire query sui dati in Azure Data Lake Storage Gen2 senza precedente inserimento usare solo per i dati cronologici o dati che raramente viene eseguita una query.
 
## <a name="optimize-query-performance-in-the-lake"></a>Ottimizzare le prestazioni delle query nel lake 

* Partizionare i dati per migliorare le prestazioni e l'ora di query ottimizzato.
* Comprimere i dati per migliorare le prestazioni (gzip per la compressione migliore, lz4 per ottenere prestazioni ottimali).
* Usare l'archivio Blob di Azure o Azure Data Lake Storage Gen2 con la stessa area del cluster di Esplora dati di Azure. 

## <a name="create-an-external-table"></a>Creare una tabella esterna

1. Usare il `.create external table` comando per creare una tabella esterna in Esplora dati di Azure. Comandi, ad esempio tabella esterna aggiuntiva `.show`, `.drop`, e `.alter` sono documentati nel [i comandi di tabella esterna](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    Questa query crea partizioni giornaliere *container1/yyyy/MM/dd/all_exported_blobs.csv*. È previsto un miglioramento delle prestazioni con il partizionamento più granulare. Ad esempio, le query sulle tabelle esterne con partizioni giornaliere, ad esempio quello precedente, avranno prestazioni migliori rispetto a queste query con le tabelle partizionate mensile.

    > [!NOTE]
    > Gli account di archiviazione attualmente supportate sono archiviazione Blob di Azure o Azure Data Lake Storage Gen2. Formati di dati attualmente supportati sono csv, tsv e txt.

1. La tabella esterna è visibile nel riquadro sinistro dell'interfaccia utente Web

    ![tabella esterna nell'interfaccia utente web](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>Autorizzazioni di tabella esterna
 
* L'utente del database può creare una tabella esterna. L'autore della tabella diventa automaticamente l'amministratore di tabella.
* Il cluster, database o tabella amministratore può modificare una tabella esistente.
* Qualsiasi utente del database o un lettore può eseguire una query una tabella esterna.
 
## <a name="query-an-external-table"></a>Eseguire query su una tabella esterna
 
Per eseguire query su una tabella esterna, usare il `external_table()` funzionare e fornire il nome della tabella come argomento di funzione. Il resto della query è linguaggio di query Kusto standard.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> IntelliSense non è attualmente supportato per le query di tabella esterna.

## <a name="query-external-and-ingested-data-together"></a>Eseguire query sui dati esterni e inseriti insieme

È possibile eseguire una query sia le tabelle esterne e tabelle di dati inseriti all'interno della stessa query. Si [ `join` ](/azure/kusto/query/joinoperator) oppure [ `union` ](/azure/kusto/query/unionoperator) tabella esterna con dati aggiuntivi da Esplora dati di Azure, istanze di SQL Server o da altre origini. Usare un [ `let( ) statement` ](/azure/kusto/query/letstatement) per assegnare un nome a sintassi abbreviata per un riferimento alla tabella esterna.

Nell'esempio riportato di seguito *prodotti* è una tabella di dati inseriti e *ArchivedProducts* è una tabella esterna che contiene i dati in Gen2 di archiviazione di Azure Data Lake:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Query *TaxiRides* tabella esterna nel cluster della Guida

Il *TaxiRides* set di dati di esempio contiene i dati dei taxi di New York City da [NYC Taxi and Limousine Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Creare una tabella esterna *TaxiRides* 

> [!NOTE]
> In questa sezione viene illustrata la query usata per creare il *TaxiRides* tabella esterna nel *Guida* cluster. Poiché questa tabella è già stata creata è possibile ignorare questa sezione ed eseguire [query *TaxiRides* i dati della tabella esterna](#query-taxirides-external-table-data). 

1. La query seguente è stata usata per creare la tabella esterna *TaxiRides* nel cluster della Guida. 

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
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. La tabella risultante è stata creata nel *aiutare* cluster:

    ![Tabella esterna TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Query *TaxiRides* dati della tabella esterna 

Accedere al [ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples) alla query il *TaxiRides* tabella esterna. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Query *TaxiRides* tabella esterna senza il partizionamento

[Esecuzione della query](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) sulla tabella esterna *TaxiRides* per rappresentare corse per ogni giorno della settimana, sull'intero set di dati. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Questa query illustra il giorno della settimana più attivo. Poiché i dati non sono partizionati, questa query potrebbe richiedere molto tempo per restituire i risultati (fino a diversi minuti).

![eseguire il rendering di query non partizionata](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Eseguire query TaxiRides tabella esterna con il partizionamento 

[Esecuzione della query](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) sulla tabella esterna *TaxiRides* che mostra i tipi di file cab dei taxi (giallo o verde) usato in gennaio 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Questa query Usa il partizionamento, che consente di ottimizzare prestazioni e tempo della query. La query filtra su una colonna di partizionamento (pickup_datetime) e restituisce i risultati in pochi secondi.

![eseguire il rendering di query di partizionamento](media/data-lake-query-data/taxirides-with-partition.png)
  
È possibile scrivere query aggiuntive da eseguire sulla tabella esterna *TaxiRides* e altre informazioni sui dati. 

## <a name="next-steps"></a>Passaggi successivi

Eseguire una query dei dati in Azure Data Lake usando Esplora dati di Azure. Informazioni su come [scrivere query](write-queries.md) e derivare informazioni aggiuntive dai tuoi dati.