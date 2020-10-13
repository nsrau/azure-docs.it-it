---
title: Come aggiungere feed di dati da origini diverse a metrica Advisor
titleSuffix: Azure Cognitive Services
description: aggiungere feed di dati diversi a metrica Advisor
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: aahi
ms.openlocfilehash: 11b75bcadc6292c17ef7e1e0f482d0c53bd9f8f5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971949"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Aggiungere feed di dati da origini dati diverse a metrica Advisor

Usare questo articolo per trovare le impostazioni e i requisiti per la connessione di diversi tipi di origini dati a metrica Advisor. Per informazioni sui concetti chiave per l'uso dei dati con la metrica Advisor, vedere come caricare i [dati](how-tos/onboard-your-data.md) . 

## <a name="supported-authentication-types"></a>Tipi di autenticazione supportati

| Tipi di autenticazione | Descrizione |
| ---------------------|-------------|
|**Base** | Sarà necessario essere in grado di fornire parametri di base per l'accesso alle origini dati. Ad esempio una stringa di connessione o una chiave. Gli amministratori del feed di dati sono in grado di visualizzare queste credenziali. |
| **AzureManagedIdentity** | Le [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per le risorse di Azure sono una funzionalità di Azure Active Directory. Fornisce servizi di Azure con un'identità gestita automaticamente in Azure AD. È possibile usare l'identità per eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD.|
| **AzureSQLConnectionString**| Archiviare la stringa di connessione AzureSQL come **entità Credential** in Metrics Advisor e usarla direttamente ogni volta durante l'onboarding dei dati di metrica. Solo gli amministratori dell'entità Credential sono in grado di visualizzare queste credenziali, ma consentono ai visualizzatori autorizzati di creare feed di dati senza che sia necessario conoscerne i dettagli. |
| **DataLakeGen2SharedKey**| Archiviare la chiave dell'account data Lake come **entità Credential** in Metrics Advisor e usarla direttamente ogni volta quando si caricano i dati di metrica. Solo gli amministratori dell'entità Credential sono in grado di visualizzare queste credenziali, ma consentono ai visualizzatori autorizzati di creare feed di dati senza che sia necessario conoscerne i dettagli.|
| **Entità servizio**| Archiviare l'entità servizio come **entità Credential** in Metrics Advisor e usarla direttamente ogni volta quando si caricano i dati di metrica. Solo gli amministratori dell'entità Credential sono in grado di visualizzare le credenziali, ma consentono ai visualizzatori autorizzati di creare feed di dati senza che sia necessario conoscerne i dettagli.|
| **Entità servizio da Key Vault**|Archiviare l'entità servizio in un insieme di credenziali delle chiavi come **entità di credenziali** in metriche Advisor e usarla direttamente ogni volta quando si caricano i dati delle metriche. Solo gli amministratori di un' **entità Credential** sono in grado di visualizzare le credenziali, ma lasciano anche che i visualizzatori siano in grado di creare feed di dati senza che siano necessarie informazioni dettagliate sulle credenziali. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Origini dati supportate e tipi di autenticazione corrispondenti


| Origini dati | Tipi di autenticazione |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Basic |
|[**Archiviazione BLOB di Azure (JSON)**](#blob) | Basic<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Basic |
|[**Esplora dati di Azure (Kusto)**](#kusto) | Basic<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>DataLakeGen2SharedKey<br>Entità servizio<br>Entità servizio da Key Vault<br> |
|[**Database SQL di Azure/SQL Server**](#sql) | Basic<br>ManagedIdentity<br>Entità servizio<br>Entità servizio da Key Vault<br>AzureSQLConnectionString
|[**Archiviazione tabelle di Azure**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**Richiesta http**](#http) | Basic | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|

Creare un' **entità Credential** e usarla per l'autenticazione alle origini dati. Le sezioni seguenti specificano i parametri richiesti da per l'autenticazione di *base* . 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **ID applicazione**: viene usato per identificare l'applicazione quando si usa l'API Application Insights. Per ottenere l'ID applicazione, eseguire le operazioni seguenti:

    1. Dalla risorsa di Application Insights fare clic su Accesso all'API.

    2. Copiare l'ID applicazione generato nel campo **ID applicazione** in metrica Advisor. 
    
    Per ulteriori informazioni, vedere la [documentazione del servizio Azure bot](https://docs.microsoft.com/azure/bot-service/bot-service-resources-app-insights-keys#application-id) .

* **Chiave API**: le chiavi API vengono usate dalle applicazioni esterne al browser per accedere a questa risorsa. Per ottenere la chiave API, eseguire le operazioni seguenti:

    1. Dalla risorsa di Application Insights fare clic su Accesso all'API.

    2. Fare clic su Crea chiave API.

    3. Immettere una breve descrizione, selezionare l'opzione Leggi telemetria e fare clic sul pulsante Genera chiave.

    4. Copiare la chiave API nel campo **chiave API** in metrica Advisor.

* **Query**: i log di applicazione Azure Insights sono basati su Esplora dati di Azure e le query del log di monitoraggio di Azure usano una versione dello stesso linguaggio di query kusto. La [documentazione del linguaggio di query di Kusto](https://docs.microsoft.com/azure/data-explorer/kusto/query/) include tutti i dettagli relativi al linguaggio e deve essere la risorsa primaria per la scrittura di una query su Application Insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Archiviazione BLOB di Azure (JSON)</span>

* **Stringa di connessione**: per informazioni sul recupero di questa stringa, vedere l'articolo [stringa di connessione](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string#view-and-copy-a-connection-string) di archiviazione BLOB di Azure.

* **Container**: la metrica Advisor prevede che i dati delle serie temporali siano archiviati come file BLOB (un BLOB per ogni timestamp) in un singolo contenitore. Si tratta del campo del nome del contenitore.

* **Modello BLOB**: modello dei nomi di file BLOB. Ad esempio: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. Sono supportati i seguenti parametri:
  * `%Y` è l'anno formattato come `yyyy`
  * `%m` è il mese formattato come `MM`
  * `%d` è il giorno formattato come `dd`
  * `%h` è l'ora formattata come `HH`
  * `%M` è il minuto formattato come `mm`

* **Versione formato JSON**: definisce lo schema di dati nei file JSON. Attualmente metrica Advisor supporta due versioni:
  
  * V1 (valore predefinito)

      Vengono accettati solo il *nome* e il *valore* della metrica. Ad esempio:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      Vengono accettate anche le *dimensioni* di metrica e il *timestamp* . Ad esempio:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

È consentito un solo timestamp per ogni file JSON. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Stringa di connessione**: la stringa di connessione per accedere al Azure Cosmos DB. Si trova nella risorsa Cosmos DB, in **chiavi**. 
* **Database**: database su cui eseguire la query. Si trova nella sezione **contenitori** della pagina **Sfoglia** .
* **ID raccolta**: ID raccolta su cui eseguire una query. Si trova nella sezione **contenitori** della pagina **Sfoglia** .
* **Query SQL**: query SQL per ottenere e formulare i dati in dati di serie temporali multidimensionali. È possibile utilizzare le `@StartTime` `@EndTime` variabili e nella query. Devono essere formattati: `yyyy-MM-dd HH:mm:ss` .

    Query di esempio:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Query di esempio per una sezione di dati da 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Esplora dati di Azure (Kusto)</span>

* **Stringa di connessione**: metrica Advisor supporta l'accesso ad Azure Esplora dati (kusto) utilizzando Azure ad l'autenticazione dell'applicazione. Sarà necessario creare e registrare un'applicazione Azure AD e quindi autorizzarla ad accedere a un database di Esplora dati di Azure. Per ottenere la stringa di connessione, vedere la documentazione di [Azure Esplora dati](https://docs.microsoft.com/azure/data-explorer/provision-azure-ad-app) .

* **Query**: vedere il [linguaggio di query kusto](https://docs.microsoft.com/azure/data-explorer/kusto/query) per ottenere e formulare i dati in dati di serie temporali multidimensionali. È possibile utilizzare le `@StartTime` `@EndTime` variabili e nella query. Devono essere formattati: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Nome account**: nome dell'account del Azure Data Lake storage Gen2. Si trova nella risorsa dell'account di archiviazione di Azure (Azure Data Lake Storage Gen2) in **chiavi di accesso**.

* **Chiave dell'account**: specificare il nome dell'account per accedere al Azure Data Lake storage Gen2. Questo problema si trova nella risorsa dell'account di archiviazione di Azure (Azure Data Lake Storage Gen2) nell'impostazione delle **chiavi di accesso** .

* **Nome file System (contenitore)**: la metrica Advisor prevede che i dati delle serie temporali siano archiviati come file BLOB (un BLOB per timestamp) in un singolo contenitore. Si tratta del campo del nome del contenitore. Si trova nell'istanza dell'account di archiviazione di Azure (Azure Data Lake Storage Gen2) e si fa clic su' contenitori ' nella sezione ' servizio BLOB '.

* **Modello di directory**: si tratta del modello di directory del file BLOB. Ad esempio: */%Y/%m/%d*. Sono supportati i seguenti parametri:
  * `%Y` è l'anno formattato come `yyyy`
  * `%m` è il mese formattato come `MM`
  * `%d` è il giorno formattato come `dd`
  * `%h` è l'ora formattata come `HH`
  * `%M` è il minuto formattato come `mm`

* **Modello di file**: si tratta del modello di file del file BLOB. Ad esempio: *X_% Y-% m-% d-% h-% M.jssu*. Sono supportati i seguenti parametri:
  * `%Y` è l'anno formattato come `yyyy`
  * `%m` è il mese formattato come `MM`
  * `%d` è il giorno formattato come `dd`
  * `%h` è l'ora formattata come `HH`
  * `%M` è il minuto formattato come `mm`

Attualmente metrica Advisor supporta lo schema dei dati nei file JSON come segue. Ad esempio:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Database SQL di Azure | SQL Server</span>

* **Stringa di connessione**: la metrica Advisor accetta una [stringa di connessione in stile ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax) per l'origine dati SQL Server.

    Stringa di connessione di esempio:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Query**: query SQL per ottenere e formulare i dati in dati di serie temporali multidimensionali. È possibile usare una `@StartTime` variabile nella query per facilitare l'ottenimento del valore della metrica prevista.

  * `@StartTime`: valore DateTime nel formato `yyyy-MM-dd HH:mm:ss`

    Query di esempio:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Query effettiva eseguita per la sezione di dati di 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Archiviazione tabelle di Azure</span>

* **Stringa di connessione**: per informazioni su come recuperare la stringa di connessione dall'archiviazione tabelle di Azure, vedere [visualizzare e copiare una stringa](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage?toc=%2Fazure%2Fstorage%2Ftables%2Ftoc.json&tabs=azure-portal#view-account-access-keys) di connessione.

* **Nome tabella**: specificare una tabella in base a cui eseguire una query. Si trova nell'istanza dell'account di archiviazione di Azure. Fare clic su **tabelle** nella sezione **servizio tabelle** .

* **Query** di È possibile usare `@StartTime` nella query. `@StartTime` viene sostituito con una stringa di formato AAAA-MM-GGThh: mm: SS nello script.

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Host**: specificare l'host master del cluster ElasticSearch.
* **Porta**: specificare la porta master del cluster ElasticSearch.
* **Intestazione autorizzazione**: specificare il valore dell'intestazione di autorizzazione del cluster ElasticSearch.
* **Query**: specificare la query per ottenere i dati. Il segnaposto @StartTime è supportato. ad esempio, quando i dati del 2020-06-21T00:00:00Z viene inserito, @StartTime = 2020-06-21T00:00:00)

## <a name="span-idhttphttp-requestspan"></a><span id="http">Richiesta HTTP</span>

* **URL della richiesta**: un URL http che può restituire JSON. Sono supportati i segnaposto% Y,% m,% d,% h,% M:% Y = anno nel formato aaaa,% m = mese nel formato MM,% d = giorno nel formato gg,% h = ora in formato HH,% M = minuto nel formato mm. Ad esempio: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **Metodo http request**: usare Get o post.
* **Intestazione della richiesta**: è possibile aggiungere l'autenticazione di base. 
* **Payload della richiesta**: è supportato solo il payload JSON. Il segnaposto @StartTime è supportato nel payload. La risposta deve essere nel formato JSON seguente: [{"timestamp": "2018-01-01T00:00:00Z", "Market": "en-US", "count": 11, "Revenue": 1.23}, {"timestamp": "2018-01-01T00:00:00Z", "Market": "zh-CN", "count": 22, "Revenue": 4,56}]. (ad esempio, quando i dati del 2020-06-21T00:00:00Z viene inserito, @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Stringa di connessione**: la stringa di connessione per accedere al InfluxDB.
* **Database**: database su cui eseguire la query.
* **Query**: query per ottenere e formulare i dati in dati di serie temporali multidimensionali per l'inserimento.
* **Nome utente**: questa opzione è facoltativa per l'autenticazione. 
* **Password**: questa opzione è facoltativa per l'autenticazione. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Stringa di connessione**: la stringa di connessione per accedere a MongoDB.
* **Database**: database su cui eseguire la query.
* **Command**: comando che consente di ottenere e formulare i dati in dati di serie temporali multidimensionali per l'inserimento.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Stringa di connessione**: la stringa di connessione per accedere al database MySQL.
* **Query**: query per ottenere e formulare i dati in dati di serie temporali multidimensionali per l'inserimento.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Stringa di connessione**: la stringa di connessione per accedere al database PostgreSQL.
* **Query**: query per ottenere e formulare i dati in dati di serie temporali multidimensionali per l'inserimento.

## <a name="next-steps"></a>Passaggi successivi

* Durante l'attesa dell'inserimento dei dati delle metriche nel sistema, vedere informazioni su [come gestire le configurazioni del feed di dati](how-tos/manage-data-feeds.md).
* Quando si inseriscono i dati delle metriche, è possibile [configurare le metriche e ottimizzare la configurazione del rilevamento](how-tos/configure-metrics.md).
