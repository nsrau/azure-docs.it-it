---
title: Filtrare i dati usando l'accelerazione delle query di Azure Data Lake Storage (anteprima)Filter data by using Azure Data Lake Storage query acceleration (preview) Documenti Microsoft
description: Usare l'accelerazione delle query (anteprima) per recuperare un subset di dati dall'account di archiviazione.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771846"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrare i dati usando l'accelerazione delle query di Azure Data Lake Storage (anteprima)Filter data by using Azure Data Lake Storage query acceleration (preview)

Questo articolo illustra come usare l'accelerazione delle query (anteprima) per recuperare un subset di dati dall'account di archiviazione. 

L'accelerazione delle query (anteprima) è una nuova funzionalità per Azure Data Lake Storage che consente alle applicazioni e ai framework di analisi di ottimizzare notevolmente l'elaborazione dei dati recuperando solo i dati necessari per eseguire una determinata operazione. Per altre informazioni, vedere Accelerazione delle query di [Archiviazione di Azure Data Lake (anteprima).](data-lake-storage-query-acceleration.md)

> [!NOTE]
> La funzionalità di accelerazione delle query è disponibile nell'anteprima pubblica ed è disponibile nelle aree centrali canadesi e francesi. Per esaminare le limitazioni, vedere l'articolo [Problemi noti.](data-lake-storage-known-issues.md) Per iscriversi all'anteprima, vedere [questo modulo.](https://aka.ms/adls/qa-preview-signup)  

## <a name="prerequisites"></a>Prerequisiti

### <a name="net"></a>[.NET](#tab/dotnet)

- Per accedere ad Archiviazione di Azure è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un account di archiviazione **v2 generico.** vedere [Creare un account di archiviazione](../common/storage-quickstart-create-account.md).

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Per accedere ad Archiviazione di Azure è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un account di archiviazione **v2 generico.** vedere [Creare un account di archiviazione](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) versione 8 o successiva.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > In questo articolo si presuppone che sia stato creato un progetto Java utilizzando Apache Maven. Per un esempio di creazione di un progetto utilizzando Apache Maven, vedere [Impostazione](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Installare i pacchetti 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Scaricare i pacchetti di accelerazione delle query. È possibile ottenere un file .zip compresso contenente [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)questi pacchetti utilizzando questo collegamento: . 

2. Estrarre il contenuto di questo file nella directory del progetto.

3. Aprire il file di progetto (*con estensione csproj*) in \<\> un editor di testo e aggiungere questi riferimenti al pacchetto all'interno dell'elemento Project .

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Ripristinare i pacchetti SDK di anteprima. Questo comando di esempio ripristina i `dotnet restore` pacchetti SDK di anteprima utilizzando il comando . 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Ripristinare tutte le altre dipendenze dal repository NuGet pubblico.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Creare una directory nella radice del progetto. La directory principale è la directory che contiene il file **pom.xml.**

   > [!NOTE]
   > Negli esempi di questo articolo si presuppone che il nome della directory sia **lib**.

2. Scaricare i pacchetti di accelerazione delle query. È possibile ottenere un file .zip compresso contenente [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)questi pacchetti utilizzando questo collegamento: . 

3. Estrarre i file in questo file .zip nella directory creata. In questo esempio, tale directory è denominata **lib**. 

4. Aprire il file *pom.xml* nell'editor di testo. Aggiungere gli elementi di dipendenza seguenti al gruppo di dipendenze. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Aggiungere istruzioni


### <a name="net"></a>[.NET](#tab/dotnet)

Aggiungere `using` queste istruzioni all'inizio del file di codice.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

L'accelerazione delle query recupera i dati in formato CSV e Json. Pertanto, assicurarsi di aggiungere istruzioni using per tutte le librerie di analisi CSV o Json che si sceglie di utilizzare. Gli esempi visualizzati in questo articolo analizzano un file CSV usando la libreria CsvHelper disponibile in NuGet.The examples that appear in this article parse a CSV file by using the [CsvHelper](https://www.nuget.org/packages/CsvHelper/) library that is available on NuGet. Pertanto, aggiungeremmo `using` queste istruzioni all'inizio del file di codice.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Per compilare esempi presentati in questo articolo, `using` è inoltre necessario aggiungere anche queste istruzioni.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Aggiungere `import` queste istruzioni all'inizio del file di codice.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Recuperare i dati utilizzando un filtroRetrieve data by using a filter

È possibile utilizzare SQL per specificare i predicati del filtro di riga e le proiezioni di colonna in una richiesta di accelerazione delle query. Il codice seguente esegue una query su un file CSV nell'archivio e restituisce tutte le righe di dati in cui la terza colonna corrisponde al valore `Hemingway, Ernest`. 

- Nella query SQL, `BlobStorage` la parola chiave viene utilizzata per indicare il file su cui viene eseguita la query.

- I riferimenti di `_N` colonna vengono `_1`specificati come dove si trova la prima colonna. Se il file di origine contiene una riga di intestazione, è possibile fare riferimento alle colonne in base al nome specificato nella riga di intestazione. 

### <a name="net"></a>[.NET](#tab/dotnet)

Il metodo `BlobQuickQueryClient.QueryAsync` asincrono invia la query all'API di accelerazione delle query e quindi trasmette i risultati all'applicazione come oggetto [Stream.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Il `BlobQuickQueryClient.openInputStream()` metodo invia la query all'API di accelerazione delle query `InputStream` e quindi trasmette i risultati all'applicazione come oggetto che può essere letto come qualsiasi altro oggetto InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Recuperare colonne specificheRetrieve specific columns

È possibile definire l'ambito dei risultati in un sottoinsieme di colonne. In questo modo si recuperano solo le colonne necessarie per eseguire un determinato calcolo. Ciò migliora le prestazioni dell'applicazione e riduce i costi perché meno dati vengono trasferiti in rete. 

Questo codice recupera `PublicationYear` solo la colonna per tutti i libri nel set di dati. Vengono inoltre utilizzate le informazioni della riga di intestazione nel file di origine per fare riferimento alle colonne nella query.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

Il codice seguente combina il filtro di riga e le proiezioni di colonna nella stessa query. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Modulo di registrazione per l'accelerazione delle query](https://aka.ms/adls/queryaccelerationpreview)    
- [Accelerazione delle query di Azure Data Lake Storage (anteprima)Azure Data Lake Storage query acceleration (preview)](data-lake-storage-query-acceleration.md)
- [Riferimento al linguaggio SQL per l'accelerazione delle query (anteprima)Query acceleration SQL language reference (preview)](query-acceleration-sql-reference.md)
- Informazioni di riferimento sull'API REST per l'accelerazione delle queryQuery acceleration REST
