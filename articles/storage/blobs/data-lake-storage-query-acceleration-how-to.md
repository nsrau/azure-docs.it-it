---
title: Filtrare i dati tramite Azure Data Lake Storage accelerazione query | Microsoft Docs
description: Usare l'accelerazione delle query per recuperare un subset di dati dall'account di archiviazione.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/09/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b7f566f85ebdb6b481797823cba78aa968747e9f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746416"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrare i dati tramite Azure Data Lake Storage accelerazione query

Questo articolo illustra come usare l'accelerazione delle query per recuperare un subset di dati dall'account di archiviazione. 

L'accelerazione delle query consente alle applicazioni e ai Framework di analisi di ottimizzare in modo significativo l'elaborazione dei dati recuperando solo i dati necessari per eseguire una determinata operazione. Per altre informazioni, vedere [Azure Data Lake storage accelerazione delle query](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Prerequisiti

- Per accedere ad Archiviazione di Azure è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un account di archiviazione per **utilizzo generico V2** . vedere [creare un account di archiviazione](../common/storage-quickstart-create-account.md).

- Scegliere una scheda per visualizzare i prerequisiti specifici dell'SDK.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Non applicabile

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versione 8 o successiva

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > Questo articolo presuppone che sia stato creato un progetto Java usando Apache Maven. Per un esempio di come creare un progetto usando Apache Maven, vedere [configurazione](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3,8 o versione successiva.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Non sono previsti prerequisiti aggiuntivi per l'uso di Node.js SDK.

---

## <a name="enable-query-acceleration"></a>Abilita accelerazione query

Per utilizzare l'accelerazione delle query, è necessario registrare la funzionalità di accelerazione delle query con la sottoscrizione. Dopo aver verificato che la funzionalità è registrata, è necessario registrare il provider di risorse di archiviazione di Azure. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Passaggio 1: registrare la funzionalità di accelerazione delle query

Per utilizzare l'accelerazione delle query, è innanzitutto necessario registrare la funzionalità di accelerazione delle query con la sottoscrizione. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Aprire una finestra dei comandi di Windows PowerShell.

1. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

3. Registrare la funzionalità di accelerazione delle query usando il comando [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Aprire [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)o aprire un'applicazione console comando come Windows PowerShell, se è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure in locale.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

3. Registrare la funzionalità di accelerazione delle query usando il comando [AZ feature Register](/cli/azure/feature#az-feature-register) .

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Passaggio 2: verificare che la funzionalità sia registrata

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare che la registrazione sia stata completata, usare il comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare che la registrazione sia stata completata, usare il comando [AZ feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Passaggio 3: registrare il provider di risorse di archiviazione di Azure

Dopo che la registrazione è stata approvata, è necessario registrare di nuovo il provider di risorse di archiviazione di Azure. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Per registrare il provider di risorse, usare il comando [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per registrare il provider di risorse, usare il comando [AZ provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Configurare l'ambiente

### <a name="step-1-install-packages"></a>Passaggio 1: installare i pacchetti 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installare AZ Module Version 4.6.0 o versione successiva.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Per eseguire l'aggiornamento da una versione precedente di AZ, eseguire il comando seguente:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Aprire un prompt dei comandi e cambiare la directory ( `cd` ) nella cartella del progetto, ad esempio:

   ```console
   cd myProject
   ```

2. Installare la `12.5.0-preview.6` versione della libreria client di archiviazione BLOB di Azure per il pacchetto .NET usando il `dotnet add package` comando. 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.6.0
   ```

3. Gli esempi riportati in questo articolo analizzano un file CSV usando la libreria [CsvHelper](https://www.nuget.org/packages/CsvHelper/) . Per utilizzare tale libreria, utilizzare il comando seguente.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Aprire il file di *pom.xml* del progetto in un editor di testo. Aggiungere gli elementi di dipendenza seguenti al gruppo di dipendenze. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Installare la libreria client di Azure Data Lake Storage per Python usando [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installare Data Lake libreria client per JavaScript aprendo una finestra del terminale e digitando il comando seguente.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Passaggio 2: aggiungere istruzioni

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Non applicabile

#### <a name="net"></a>[.NET](#tab/dotnet)

Aggiungere queste `using` istruzioni all'inizio del file di codice.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

L'accelerazione query recupera i dati in formato CSV e JSON. Assicurarsi quindi di aggiungere istruzioni using per qualsiasi libreria CSV o di analisi JSON che si sceglie di usare. Gli esempi riportati in questo articolo analizzano un file CSV usando la libreria [CsvHelper](https://www.nuget.org/packages/CsvHelper/) disponibile in NuGet. Quindi, aggiungeremo queste `using` istruzioni all'inizio del file di codice.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Per compilare gli esempi presentati in questo articolo, è necessario anche aggiungere queste `using` istruzioni.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Aggiungere queste `import` istruzioni all'inizio del file di codice.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Aggiungere queste istruzioni Import all'inizio del file di codice.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Includere il `storage-blob` modulo inserendo questa istruzione all'inizio del file di codice. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

L'accelerazione query recupera i dati in formato CSV e JSON. Assicurarsi quindi di aggiungere istruzioni per qualsiasi modulo di analisi CSV o JSON che si sceglie di usare. Gli esempi riportati in questo articolo analizzano un file CSV usando il modulo [CSV veloce](https://www.npmjs.com/package/fast-csv) . Quindi, aggiungiamo questa istruzione all'inizio del file di codice.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Recuperare i dati tramite un filtro

È possibile utilizzare SQL per specificare i predicati del filtro di riga e le proiezioni di colonna in una richiesta di accelerazione della query. Nel codice seguente viene eseguita una query su un file CSV nell'archivio e vengono restituite tutte le righe di dati in cui la terza colonna corrisponde al valore `Hemingway, Ernest` . 

- Nella query SQL, la parola chiave `BlobStorage` viene usata per indicare il file su cui viene eseguita la query.

- I riferimenti di colonna vengono specificati come `_N` dove la prima colonna è `_1` . Se il file di origine contiene una riga di intestazione, è possibile fare riferimento alle colonne in base al nome specificato nella riga di intestazione. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Il metodo asincrono `BlobQuickQueryClient.QueryAsync` Invia la query all'API di accelerazione della query e quindi trasmette nuovamente i risultati all'applicazione come oggetto [flusso](https://docs.microsoft.com/dotnet/api/system.io.stream) .

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Context.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Il metodo `BlobQuickQueryClient.openInputStream()` Invia la query all'API di accelerazione delle query e quindi trasmette nuovamente i risultati all'applicazione come `InputStream` oggetto che può essere letto come qualsiasi altro oggetto InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Questo esempio Invia la query all'API di accelerazione della query e quindi trasmette i risultati.

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Recuperare colonne specifiche

È possibile definire l'ambito dei risultati in un subset di colonne. In questo modo è possibile recuperare solo le colonne necessarie per eseguire un calcolo specificato. Ciò migliora le prestazioni dell'applicazione e riduce i costi perché i dati vengono trasferiti in rete. 

Questo codice recupera solo la `BibNum` colonna per tutti i libri del set di dati. Vengono inoltre utilizzate le informazioni della riga di intestazione del file di origine per fare riferimento alle colonne nella query.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

Il codice seguente combina le proiezioni di colonne e filtri di riga nella stessa query. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Accelerazione query Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Informazioni di riferimento sul linguaggio SQL di accelerazione query](query-acceleration-sql-reference.md)
