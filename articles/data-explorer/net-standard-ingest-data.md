---
title: 'Guida introduttiva: Inserire dati usando .NET Standard SDK di Esplora dati di Azure (anteprima)'
description: Questa guida introduttiva descrive come inserire (caricare) i dati in Esplora dati di Azure usando .NET Standard SDK.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/18/2018
ms.openlocfilehash: b0e8c4dabea6aeae8d93d64d97b598ec97b2d18a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52277082"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Guida introduttiva: Inserire dati usando .NET Standard SDK di Esplora dati di Azure (anteprima)

Esplora dati di Azure (ADX) è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Esplora dati di Azure offre due librerie client per .NET Standard: una [libreria di inserimento](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) e una [libreria di dati](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Queste librerie consentono di inserire (caricare) i dati in un cluster ed eseguire una query di dati dal codice. In questa guida introduttiva, è innanzitutto necessario creare una tabella e il mapping dei dati in un cluster di prova. Quindi viene accodato l'inserimento nel cluster e vengono convalidati i risultati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* [Un cluster e un database di test](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Installare la libreria di inserimento

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

Per autenticare un'applicazione, Esplora dati di Azure usa l'ID tenant AAD. Per trovare l'ID del tenant, usare l'URL seguente, sostituendo il dominio a *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ad esempio, se il dominio è *contoso.com*, l'URL è: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Fare clic su questo URL per visualizzare i risultati; la prima riga è come indicato di seguito. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Il tenant ID in questo caso è `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

Questo esempio usa un utente e una password di AAD per l'autenticazione al cluster. È anche possibile usare il certificato dell'applicazione AAD e la chiave dell'applicazione AAD. Impostare i valori per `tenantId`, `user` e `password` prima di eseguire questo codice.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Costruire la stringa di connessione
Costruire ora la stringa di connessione. Creare la tabella di destinazione e il mapping in un passaggio successivo.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Impostare le informazioni sul file di origine

Impostare le costanti per il file di origine dati. Questo esempio usa un file di esempio ospitato nell'archiviazione BLOB di Azure. Il set di dati di esempio **StormEvents** contiene dati relativi al meteo del [National Center for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Creare una tabella nel cluster di prova
Creare una tabella che corrisponda allo schema dei dati nel file `StormEvents.csv`. Quando viene eseguito questo codice, restituisce un messaggio simile al seguente: *Per accedere, usare un web browser per aprire la pagina https://microsoft.com/devicelogin e immettere il codice F3W4VWZDM per l'autenticazione*. Seguire i passaggi per l'accesso, quindi tornare a eseguire il blocco di codice successivo. I blocchi di codice successivi che stabiliscono una connessione richiedono di eseguire nuovamente l'accesso.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Definire il mapping di inserimento

Eseguire il mapping dei dati CSV in ingresso sui nomi di colonna e tipi di dati usati durante la creazione della tabella.
Effettuare il provisioning di un [oggetto di mapping di colonne CSV](/azure/kusto/management/tables#create-ingestion-mapping) su tale tabella

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 21 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 22 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Accodare un messaggio per l'inserimento

Accodare un messaggio per eseguire il pull dei dati dall'archiviazione BLOB e inserire i dati in Esplora dati di Azure.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Verificare che i dati siano stati inseriti nella tabella

Attendere da cinque a dieci minuti per l'operazione di inserimento in coda per pianificare l'inserimento e caricare i dati in Esplora dati di Azure. Eseguire quindi il codice seguente per ottenere il numero di record nella tabella StormEvents.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = "StormEvents | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Eseguire query sulla risoluzione dei problemi

Accedere al [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e connettersi al cluster. Eseguire il comando seguente nel database per verificare la presenza di eventuali errori di inserimento nelle ultime quattro ore. Sostituire il nome del database prima dell'esecuzione.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Eseguire il comando seguente per visualizzare lo stato di tutte le operazioni di inserimento nelle ultime quattro ore. Sostituire il nome del database prima dell'esecuzione.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di seguire altre guide introduttive ed esercitazioni, non eliminare le risorse create. In caso contrario, eseguire il comando seguente nel database per pulire la tabella StormEvents.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scrivere query](write-queries.md)