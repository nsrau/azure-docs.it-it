---
title: Usare HBase .NET SDK - Azure HDInsight | Microsoft Docs
description: Usare HBase .NET SDK per creare ed eliminare tabelle e per leggere e scrivere dati.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: 083150fe5f8787ba791d3d692db73c5156f11e55
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="use-the-hbase-net-sdk"></a>Usare HBase .NET SDK

[HBase](apache-hbase-overview.md) fornisce due opzioni principali per lavorare con i dati: [query Hive e chiamate all'API RESTful di HBase](apache-hbase-tutorial-get-started-linux.md). È possibile lavorare direttamente con l'API REST usando il comando `curl` o un'utilità analoga.

Per le applicazioni C# e .NET, è disponibile la [libreria client Microsoft REST HBase per .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) basata sull'API REST HBase.

## <a name="install-the-sdk"></a>Installare l'SDK

HBase .NET SDK è disponibile come pacchetto NuGet, che è possibile installare dalla **Console di Gestione pacchetti NuGet** di Visual Studio con il comando seguente:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Creare un'istanza di un nuovo oggetto HBaseClient

Per usare l'SDK, creare un'istanza di un nuovo oggetto `HBaseClient` passando l'oggetto `ClusterCredentials` costituito da `Uri` del cluster e nome utente e password Hadoop.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Sostituire CLUSTERNAME con il nome del cluster HBase HDInsight e USERNAME e PASSWORD con le credenziali di Hadoop specificate alla creazione del cluster. Il nome utente predefinito di Hadoop è **admin**.

## <a name="create-a-new-table"></a>Crea una nuova tabella

HBase archivia i dati in tabelle. Una tabella è costituita da un elemento *Rowkey*, la chiave primaria, e uno o più gruppi di colonne detti *famiglie di colonne*. I dati in ogni tabella sono distribuiti in senso orizzontale in base a un intervallo di Rowkey in *aree*. Ogni area ha una chiave di inizio e di fine. Una tabella può avere una o più aree. Man mano che i dati nella tabella aumentano, HBase suddivide le aree di grandi dimensioni in aree di dimensioni più piccole. Le aree vengono archiviate nei *server di area* e un server di area può archiviare più aree.

I dati vengono archiviati fisicamente in oggetti *HFile*. Un singolo oggetto HFile contiene i dati per una tabella, un'area e una famiglia di colonne. Le righe in un oggetto HFile vengono archiviate ordinate in base a Rowkey. Ogni oggetto HFile ha un indice *albero B+* che consente il recupero rapido delle righe.

Per creare una nuova tabella, specificare un oggetto `TableSchema` e colonne. Il codice seguente controlla se la tabella "RestSDKTable" esiste già. Se la tabella non esiste, viene creata.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Questa nuova tabella ha due famiglie di colonne, t1 e t2. Poiché le famiglie di colonne vengono archiviate separatamente in oggetti HFile diversi, è opportuno disporre di una famiglia di colonne distinta per i dati su cui vengono eseguite frequentemente query. Nell'esempio nella sezione [Inserire dati](#insert-data) seguente le colonne vengono aggiunte alla famiglia di colonne t1.

## <a name="delete-a-table"></a>Eliminare una tabella

Per eliminare una tabella:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Inserire dati

Per inserire dati, specificare una chiave di riga univoca come identificatore di riga. Tutti i dati vengono archiviati in una matrice `byte[]`. Il codice seguente definisce e aggiunge le colonne `title`, `director` e `release_date` alla famiglia di colonne t1, in quanto sono le colonne di uso più frequente. Le colonne `description` e `tagline` vengono aggiunte alla famiglia di colonne t2. È possibile partizionare i dati in famiglie di colonne in base alle esigenze.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase implementa BigTable, quindi il formato dei dati è simile al seguente:

![Utente con ruolo di utente del cluster](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Selezionare i dati

Per leggere i dati da una tabella HBase, passare il nome della tabella e la chiave di riga al metodo `GetCellsAsync` per restituire l'oggetto `CellSet`.

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

In questo caso, il codice restituisce solo la prima riga corrispondente, in quanto deve essere presente solo una riga per una chiave univoca. Il valore restituito viene modificato in formato `string` dalla matrice `byte[]`. È anche possibile convertire il valore in altri tipi, ad esempio un tipo integer per la data di rilascio del film:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Analizzare le righe

HBase usa `scan` per recuperare una o più righe. Questo esempio richiede più righe in batch da 10 e recupera i dati i cui valori di chiave sono compresi tra 25 e 35. Dopo aver recuperato tutte le righe, eliminare l'oggetto scanner per pulire le risorse.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a usare un esempio di Apache HBase in HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Creare un'applicazione end-to-end con [Analizzare i sentiment di Twitter in tempo reale con HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
