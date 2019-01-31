---
title: Monitorare Batch con Azure Application Insights | Microsoft Docs
description: Informazioni su come instrumentare un'applicazione .NET di Azure Batch con la libreria di Azure Application Insights.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: 42ea8398fa1a8b1fbc42108d1165dc17da2c34d7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473463"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorare ed eseguire il debug di un'applicazione .NET di Azure Batch con Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) offre agli sviluppatori un modo elegante e potente per monitorare ed eseguire il debug delle applicazioni distribuite nei servizi di Azure. Usare Application Insights per monitorare i contatori delle prestazioni e le eccezioni, nonché per instrumentare il codice con metriche e funzionalità di traccia personalizzate. L'integrazione di Application Insights con l'applicazione Azure Batch consente di ottenere informazioni dettagliate sui comportamenti e di analizzare i problemi in tempo quasi reale.

Questo articolo illustra come aggiungere e configurare la libreria di Application Insights nella soluzione .NET di Azure Batch e instrumentare il codice dell'applicazione. Vengono inoltre presentati i modi per monitorare l'applicazione tramite il portale di Azure e creare dashboard personalizzati. Per informazioni sul supporto di Application Insights in altri linguaggi, vedere la [documentazione relativa a linguaggi, piattaforme e integrazioni](../azure-monitor/app/platforms.md).

In [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) è disponibile una soluzione C# di esempio con codice per seguire questo articolo. Questo esempio aggiunge codice di strumentazione di Application Insights all'esempio [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords). Se non si ha familiarità con tale esempio, provare prima di tutto a compilare ed eseguire TopNWords. In questo modo sarà possibile comprendere un flusso di lavoro semplice di Batch per l'elaborazione di un set di BLOB di input in parallelo su più nodi di calcolo. 

> [!TIP]
> In alternativa, configurare la soluzione Batch per visualizzare i dati di Application Insights, ad esempio i contatori delle prestazioni delle macchine virtuali in Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) è uno strumento client autonomo, gratuito e ricco di funzionalità che consente di creare, eseguire il debug e monitorare le applicazioni di Azure Batch. È possibile scaricare un [pacchetto di installazione](https://azure.github.io/BatchExplorer/) per Mac, Linux o Windows. Vedere il [repository di informazioni dettagliate su Batch](https://github.com/Azure/batch-insights) per azioni rapide per abilitare i dati di Application Insights in Batch Explorer. 
>

## <a name="prerequisites"></a>Prerequisiti
* [Visual Studio 2017](https://www.visualstudio.com/vs)

* [Un account Batch e un account di archiviazione collegato](batch-account-create-portal.md)

* [Una risorsa di Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Usare il portale di Azure per creare una *risorsa* di Application Insights. Selezionare *Generale* **Tipo di applicazione**.

   * Copiare la [chiave di strumentazione](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) dal portale. Sarà necessaria più avanti in questo articolo.
  
  > [!NOTE]
  > Potrebbe essere registrato un [addebito](https://azure.microsoft.com/pricing/details/application-insights/) per i dati archiviati in Application Insights. Tali dati includono i dati di diagnostica e monitoraggio descritti in questo articolo.
  > 

## <a name="add-application-insights-to-your-project"></a>Aggiunta di Application Insights al progetto

Per il progetto sono necessari il pacchetto NuGet **Microsoft.ApplicationInsights.WindowsServer** e le relative dipendenze. Aggiungerli o ripristinarli nel progetto dell'applicazione. Per installare il pacchetto, usare il comando `Install-Package` o Gestione pacchetti NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Aggiungere un riferimento ad Application Insights nell'applicazione .NET tramite lo spazio dei nomi **Microsoft.ApplicationInsights**.

## <a name="instrument-your-code"></a>Instrumentare il codice

Per instrumentare il codice, la soluzione deve creare un [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) di Application Insights. Nell'esempio, il TelemetryClient carica la relativa configurazione dal file [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md). Assicurarsi di aggiornare il file ApplicationInsights.config nei progetti seguenti con la chiave di strumentazione di Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask e TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Aggiungere la chiave di strumentazione anche al file TopNWords.cs.

L'esempio in TopNWords.cs usa le [chiamate di strumentazione](../azure-monitor/app/api-custom-events-metrics.md) seguenti dall'API Application Insights:
* `TrackMetric()` - Tiene traccia del tempo medio richiesto per il download del file di testo richiesto dal nodo di calcolo.
* `TrackTrace()` - Aggiunge le chiamate di debug al codice.
* `TrackEvent()` - Tiene traccia degli eventi interessanti da acquisire.

Questo esempio esclude intenzionalmente la gestione delle eccezioni. Application Insights segnala invece automaticamente le eccezioni non gestite, con un miglioramento notevole dell'esperienza di debug. 

Il frammento di codice seguente illustra come usare questi metodi.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Helper dell'inizializzatore di telemetria di Azure Batch
Quando si segnalano i dati di telemetria per un determinato server e un'istanza, Application Insights usa il ruolo di macchina virtuale di Azure e il nome della macchina virtuale per i valori predefiniti. Nel contesto di Azure Batch, l'esempio mostra come usare invece il nome del pool e il nome del nodo di calcolo. Usare un [inizializzatore di telemetria](../azure-monitor/app/api-filtering-sampling.md#add-properties) per sostituire i valori predefiniti. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Per abilitare l'inizializzatore di telemetria, il file Applicationinsights.config nel progetto TopNWordsSample include quanto segue:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aggiornare il processo e le attività per includere i file binari di Application Insights

Per la corretta esecuzione di Application Insights sui nodi di calcolo, assicurarsi che i file binari siano posizionati correttamente. Aggiungere i file binari necessari alla raccolta di file di risorse dell'attività, in modo che vengano scaricati in fase di esecuzione dell'attività. I frammenti di codice seguenti sono simili al codice in Job.cs.

Prima di tutto, creare un elenco statico di file di Application Insights da caricare.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Successivamente, creare i file di gestione temporanea usati dall'attività.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

Il metodo `FileToStage` è una funzione helper nell'esempio di codice che consente di caricare facilmente un file dal disco locale in un BLOB di Archiviazione di Azure. Ogni file viene in seguito scaricato in un nodo di calcolo e un'attività vi fa riferimento.

Infine, aggiungere le attività al processo e includere i file binari di Application Insights necessari.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Visualizzare i dati nel portale di Azure

Dopo aver configurato il processo e le attività per usare Application Insights, eseguire il processo di esempio nel pool. Passare al portale di Azure e aprire la risorsa di Application Insights di cui è stato eseguito il provisioning. Dopo il provisioning del pool dovrebbero iniziare il flusso dei dati e la relativa registrazione. Il resto di questo articolo descrive brevemente solo alcune funzionalità di Application Insights, ma può essere utile esplorare il set completo di funzionalità.

### <a name="view-live-stream-data"></a>Visualizzare i dati di Live Stream

Per visualizzare i log di traccia nella risorsa di Application Insights, fare clic su **Live Stream**. Lo screenshot seguente mostra come visualizzare i dati in tempo reale provenienti dai nodi di calcolo nel pool, ad esempio l'utilizzo della CPU per ogni nodo di calcolo.

![Dati dei nodi di calcolo per Live Stream](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Visualizzare i log di traccia

Per visualizzare i log di traccia nella risorsa di Application Insights, fare clic su **Cerca**. Questa visualizzazione mostra un elenco dei dati di diagnostica acquisiti da Application Insights, inclusi eventi, tracce ed eccezioni. 

Lo screenshot seguente mostra come registrare una singola traccia per un'attività e poi sottoporla a query per scopi di debug.

![Immagine dei log di traccia](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Visualizzare le eccezioni non gestite

Lo screenshot seguente mostra come vengono registrate le eccezioni generate dall'applicazione in Application Insights. In questo caso, entro pochi secondi da quando l'applicazione genera l'eccezione, è possibile analizzare un'eccezione specifica e diagnosticare il problema.

![Eccezioni non gestite](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Misurare i tempi di download dei BLOB

Anche le metriche personalizzate sono uno strumento prezioso nel portale. Ad esempio, è possibile visualizzare il tempo medio impiegato per scaricare il file di testo richiesto per l'elaborazione da ogni nodo di calcolo.

Per creare un grafico di esempio:
1. Nella risorsa di Application Insights fare clic su **Esplora metriche** > **Aggiungi grafico**.
2. Fare clic su **Modifica** nel grafico aggiunto.
2. Aggiornare i dettagli del grafico come segue:
   * Impostare **Tipo di grafico** su **Griglia**.
   * Impostare **Aggregazione** su **Media**.
   * Impostare **Raggruppa per** su **NodeId**.
   * In **Metriche** selezionare **Personalizzato** > **Blob download in seconds** (Download BLOB in secondi).
   * Modificare la visualizzazione della **Tavolozza dei colori** in base alle preferenze. 

![Tempi di download dei BLOB per ogni nodo](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Monitoraggio continuo dei nodi di calcolo

Probabilmente si è notato che tutte le metriche, inclusi i contatori delle prestazioni, vengono registrate solo durante l'esecuzione delle attività. Questo comportamento è utile perché limita la quantità di dati registrata da Application Insights. In alcuni casi, tuttavia, può essere necessario monitorare sempre i nodi di calcolo, ad esempio perché eseguono attività in background che non sono pianificate tramite il servizio Batch. In questo caso, configurare un processo di monitoraggio per l'esecuzione per la durata del nodo di calcolo. 

Un modo per ottenere questo comportamento consiste nel generare un processo che carica la libreria di Application Insights e viene eseguito in background. Nell'esempio, l'attività di avvio carica i file binari nel computer e mantiene un processo in esecuzione per un periodo illimitato. Configurare il file di configurazione di Application Insights in modo che questo processo produca i dati aggiuntivi di interesse, ad esempio contatori delle prestazioni.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Per aumentare la gestibilità della soluzione, è possibile aggregare l'assembly in un [pacchetto di applicazione](./batch-application-packages.md). Quindi, per distribuire automaticamente il pacchetto dell'applicazione nei pool, aggiungere un riferimento al pacchetto dell'applicazione nella configurazione del pool.
>

## <a name="throttle-and-sample-data"></a>Limitazione e dati di esempio 

Tenendo conto del fatto che le applicazioni di Azure Batch in esecuzione in ambiente di produzione sono per natura su larga scala, potrebbe essere utile limitare la quantità di dati raccolti da Application Insights per gestire i costi. Vedere [Campionamento in Application Insights](../azure-monitor/app/sampling.md) per informazioni su alcuni meccanismi per ottenere questo risultato.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [Application Insights](../azure-monitor/app/app-insights-overview.md).

* Per informazioni sul supporto di Application Insights in altri linguaggi, vedere la [documentazione relativa a linguaggi, piattaforme e integrazioni](../azure-monitor/app/platforms.md).


