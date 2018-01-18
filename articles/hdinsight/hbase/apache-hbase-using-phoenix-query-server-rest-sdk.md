---
title: Phoenix Query Server REST SDK - Azure HDInsight | Microsoft Docs
description: 
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: f3b29db2dd74e6b3c0c066045d05cb853d1541f8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="phoenix-query-server-rest-sdk"></a>Phoenix Query Server REST SDK

[Apache Phoenix](http://phoenix.apache.org/) è un livello di database relazionale open source altamente parallelo basato su [HBase](apache-hbase-overview.md). Phoenix consente di usare query simili a SQL con HBase tramite strumenti SSH, ad esempio [SQLLine](apache-hbase-phoenix-squirrel-linux.md). Phoenix fornisce anche un server HTTP chiamato Phoenix Query Server (PQS), un thin client che supporta due meccanismi di trasporto per le comunicazioni client: JSON e Protocol Buffers. Protocol Buffers è il meccanismo predefinito e offre comunicazioni più efficienti rispetto a JSON.

Questo articolo descrive come usare PQS REST SDK per creare tabelle, eseguire operazioni di upsert di righe singolarmente e in blocco e selezionare i dati usando istruzioni SQL. Gli esempi usano il [driver Microsoft .NET per Apache Phoenix Query Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Questo SDK si basa sulle API [Avatica di Apache Calcite](https://calcite.apache.org/avatica/), che usano esclusivamente Protocol Buffers per il formato di serializzazione.

Per altre informazioni, vedere le [informazioni di riferimento su Protocol Buffers Avatica di Apache Calcite](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Installare l'SDK

Il driver Microsoft .NET per Apache Phoenix Query Server è disponibile come pacchetto NuGet, che è possibile installare dalla **Console di Gestione pacchetti NuGet** di Visual Studio con il comando seguente:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Creare un'istanza di un nuovo oggetto PhoenixClient

Per iniziare a usare la libreria, creare un'istanza di un nuovo oggetto `PhoenixClient` passando l'oggetto `ClusterCredentials` contenente l'elemento `Uri` del cluster e nome utente e password Hadoop del cluster.

```c#
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Sostituire CLUSTERNAME con il nome del cluster HBase HDInsight e USERNAME e PASSWORD con le credenziali di Hadoop specificate alla creazione del cluster. Il nome utente predefinito di Hadoop è **admin**.

## <a name="generate-unique-connection-identifier"></a>Generare un identificatore di connessione univoco

Per inviare una o più richieste a PQS, è necessario includere un identificatore di connessione univoco per associare le richieste alla connessione.

```c#
string connId = Guid.NewGuid().ToString();
```

Ogni esempio esegue prima di tutto una chiamata al metodo `OpenConnectionRequestAsync`, passando l'identificatore di connessione univoco. Definire quindi `ConnectionProperties` e `RequestOptions`, passando gli oggetti e l'identificatore di connessione generato al metodo `ConnectionSyncRequestAsync`. L'oggetto `ConnectionSyncRequest` di PQS garantisce che il client e il server abbiano una vista coerente delle proprietà del database.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest e ConnectionProperties

Per chiamare `ConnectionSyncRequestAsync`, passare un oggetto `ConnectionProperties`.

```c#
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Ecco alcune proprietà di interesse:

| Proprietà | Descrizione |
| -- | -- |
| AutoCommit | Valore booleano che indica se l'opzione `autoCommit` è abilitata per le transazioni Phoenix. |
| ReadOnly | Valore booleano che indica se la connessione è di sola lettura. |
| TransactionIsolation | Valore intero che indica il livello di isolamento delle transazioni in base alla specifica JDBC. Vedere la tabella seguente.|
| Catalog | Nome del catalogo da usare per recuperare le proprietà di connessione. |
| Schema | Nome dello schema da usare per recuperare le proprietà di connessione. |
| IsDirty | Valore booleano che indica se le proprietà sono state modificate. |

Ecco i valori di `TransactionIsolation`:

| Valore di isolamento | Descrizione |
| -- | -- |
| 0 | Le transazioni non sono supportate. |
| 1 | Possono verificarsi letture dirty, letture non ripetibili e letture fantasma. |
| 2 | Possono verificarsi letture non ripetibili e letture fantasma, mentre le letture dirty non sono consentite. |
| 4 | Possono verificarsi letture fantasma, mentre le letture dirty e le letture non ripetibili non sono consentite. |
| 8 | Letture dirty, letture non ripetibili e letture fantasma non sono consentite. |

## <a name="create-a-new-table"></a>Crea una nuova tabella

HBase, come qualsiasi altro sistema RDBMS, archivia i dati nelle tabelle. Phoenix usa query SQL standard per creare nuove tabelle, definendo la chiave primaria e i tipi di colonna.

In questo esempio e tutti gli esempi successivi, usare l'oggetto `PhoenixClient` di cui è stata creata un'istanza come definito in [Creare un'istanza di un nuovo oggetto PhoenixClient](#instantiate-new-phoenixclient-object).

```c#
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

L'esempio precedente crea una nuova tabella denominata `Customers` usando l'opzione `IF NOT EXISTS`. La chiamata a `CreateStatementRequestAsync` crea una nuova istruzione nel server Avitica (PQS). Il blocco `finally` chiude gli oggetti `CreateStatementResponse` e `OpenConnectionResponse` restituiti.

## <a name="insert-data-individually"></a>Inserire dati singolarmente

Questo esempio mostra una singola operazione di inserimento di dati, che fa riferimento a una raccolta `List<string>` di abbreviazioni di stati e territori americani:

```c#
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Il valore della colonna `StateProvince` della tabella verrà usato in un'operazione select successiva.

```c#
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

La struttura per l'esecuzione di un'istruzione insert è simile a quella per la creazione di una nuova tabella. Si noti che alla fine del blocco `try` viene eseguito in modo esplicito il commit della transazione. Questo esempio ripete una transazione di inserimento 300 volte. L'esempio seguente mostra un processo di inserimento in batch più efficiente.

## <a name="batch-insert-data"></a>Inserire dati in batch

Il codice seguente è quasi identico al codice per l'inserimento di dati singolarmente. Questo esempio usa l'oggetto `UpdateBatch` in una chiamata a `ExecuteBatchRequestAsync` invece di chiamare ripetutamente `ExecuteRequestAsync` con un'istruzione preparata.

```c#
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

In ambiente di test per inserire singolarmente 300 nuovi record sono stati necessari quasi 2 minuti. L'inserimento di 300 record in batch ha invece richiesto solo 6 secondi.

## <a name="select-data"></a>Selezionare i dati

Questo esempio mostra come riutilizzare una connessione per eseguire più query:

1. Selezionare tutti i record e quindi recuperare i record rimanenti dopo che è stato restituito il numero massimo di 100 record.
2. Usare un'istruzione select per il conteggio totale delle righe per recuperare il risultato scalare singolo.
3. Eseguire un'istruzione select che restituisce il numero totale di clienti per ogni stato o territorio.

```c#
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

L'output delle istruzioni `select` dovrebbe fornire il risultato seguente:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

<!-- ## Next steps -->
<!-- * [Phoenix in HDInsight](hdinsight-phoenix-in-hdinsight.md)  -->
<!-- * [Using the HBase REST SDK](hdinsight-using-hbase-rest-sdk.md)  -->
