---
title: Management .NET SDK per Analisi di flusso di Azure | Microsoft Docs
description: Introduzione a .NET SDK per la gestione di Analisi di flusso. Informazioni su come configurare ed eseguire i processi di analisi. Creare un progetto, input, output e trasformazioni.
keywords: .NET SDK, API di analisi
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: samacha
ms.openlocfilehash: 2ac5d305aae110eff46459ecb7d89ca50ae1823d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK: impostare ed eseguire processi di analisi tramite l'API di Analisi di flusso di Azure per .NET
Informazioni su come impostare ed eseguire processi di analisi tramite l'API di Analisi di flusso per .NET usando Management .NET SDK. Impostare un progetto, creare origini di input e output, trasformazioni e avviare e arrestare i processi. Per i processi di analisi, è possibile trasmettere i dati di flusso dall'archiviazione BLOB o da un hub eventi.

Vedere la [documentazione di riferimento sulla gestione per l'API di Analisi di flusso per .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Analisi dei flussi di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Analisi di flusso consente ai clienti di configurare processi di flusso per analizzare i flussi di dati e di condurre operazioni di analisi pressoché in tempo reale.  

> [!NOTE]
> Il codice di esempio in questo articolo è stato aggiornato con la versione v2.x .NET SDK per la gestione di Analisi di flusso di Azure. Per il codice di esempio che usa la versione legacy (1.x) SDK, vedere [Usare Management .NET SDK v1.x per Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* Installare Visual Studio 2017 o 2015.
* Scaricare e installare [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Creare un gruppo di risorse di Azure nella sottoscrizione. Di seguito è riportato un esempio di script di Azure PowerShell: Per informazioni su Azure PowerShell , vedere [Installare e configurare Azure PowerShell](/powershell/azure/overview);  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Configurare un'origine di input e una destinazione di output da usare. Per altre istruzioni, vedere [Aggiungere input](stream-analytics-add-inputs.md) per impostare un esempio di input e [Aggiungere output](stream-analytics-add-outputs.md) per impostare un esempio di output.

## <a name="set-up-a-project"></a>Configurare un progetto
Per creare un processo di analisi usando l'API di Analisi di flusso per .NET, configurare prima il progetto.

1. Creare un'applicazione console .NET di Visual Studio C#.
2. Nella Console di Gestione pacchetti, eseguire i comandi seguenti per installare i pacchetti NuGet. Il primo è .NET SDK di gestione di Analisi di flusso di Azure. Il secondo è per l'autenticazione del client di Azure.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
3. Aggiungere la sezione **appSettings** seguente al file App.config:
   
        <appSettings>
          <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
          <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
        </appSettings>

    Sostituire i valori per **SubscriptionId** e **ActiveDirectoryTenantId** con gli ID della sottoscrizione di Azure e del tenant. È possibile ottenere questi valori eseguendo il cmdlet Azure PowerShell seguente:

        Get-AzureAccount

4. Aggiungere il riferimento seguente nel file con estensione csproj:

        <Reference Include="System.Configuration" />

5. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.
   
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Threading;
        using System.Threading.Tasks;
        
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Rest;
6. Aggiungere un metodo helper di autenticazione:

   ```
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Creare un client di gestione di Analisi di flusso.
Un oggetto **StreamAnalyticsManagementClient** consente di gestire il processo e i componenti di processo, ad esempio l'input, l'output e la trasformazione.

Aggiungere il codice seguente all'inizio del metodo **Main** .

   ```
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

Il valore della variabile **resourceGroupName** deve essere lo stesso del nome del gruppo di risorse creato o selezionato nei passaggi preliminari.

Per automatizzare l'aspetto di presentazione delle credenziali della creazione del processo, fare riferimento a [Autenticazione di un'entità servizio con Gestione risorse di Azure](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Nelle sezioni rimanenti di questo articolo si presuppone che il codice sia all'inizio del metodo **Main** .

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.
Il codice seguente crea un processo di Analisi di flusso nel gruppo di risorse che è stato definito. Verranno aggiunti un input, un output e la trasformazione al processo in un secondo momento.

   ```
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Creare un'origine di input di Analisi di flusso.
Il codice seguente crea un'origine di input di Analisi di flusso con il tipo di origine di input BLOB e la serializzazione CSV. Per creare un'origine di input di hub eventi, usare **EventHubStreamInputDataSource** anziché **BlobStreamInputDataSource**. Analogamente, è possibile personalizzare il tipo di serializzazione dell'origine di input.

   ```
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Le origini di input, dall'archiviazione BLOB o un hub eventi, sono legate a un processo specifico. Per usare la stessa origine di input per processi diversi, è necessario chiamare nuovamente il metodo e specificare un nome diverso.

## <a name="test-a-stream-analytics-input-source"></a>Testare un'origine di input di Analisi di flusso
Il metodo **TestConnection** verifica se il processo di Analisi di flusso è in grado di connettersi all'origine di input, nonché altri aspetti specifici del tipo di origine di input. Ad esempio, nell'origine di input BLOB creata in precedenza, il metodo verifica che il nome dell'account di archiviazione e la coppia di chiavi possano essere usati per connettersi all'account di archiviazione, nonché verificare che il contenitore specificato esista.

   ```
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Creare una destinazione di output di Analisi di flusso
La creazione di una destinazione di output è molto simile alla creazione di un'origine di input di Analisi di flusso. Analogamente alle origini di input, le destinazioni di output sono legate a un processo specifico. Per usare la stessa destinazione di output per processi diversi, è necessario chiamare nuovamente il metodo e specificare un nome diverso.

Il codice seguente crea una destinazione di output (database SQL di Azure). È possibile personalizzare il tipo di dati della destinazione di output e/o il tipo di serializzazione.

   ```
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testare una destinazione di output di Analisi di flusso
Una destinazione di output di Analisi di flusso dispone inoltre del metodo **TestConnection** per il test delle connessioni.

   ```
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Creare una trasformazione di Analisi di flusso
Il codice seguente crea una trasformazione di Analisi di flusso con la query "select * from Input" e specifica l'assegnazione di un'unità di streaming per il processo di Analisi di flusso. Per altre informazioni sulla regolazione di unità di streaming, vedere [Scalabilità dei processi di Analisi di flusso di Azure](stream-analytics-scale-jobs.md).

   ```
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Analogamente all'input e all'output, una trasformazione è inoltre collegata a un processo di Analisi di flusso specifico in cui è stato creata.

## <a name="start-a-stream-analytics-job"></a>Avvio di un processo di Analisi di flusso
Dopo aver creato un processo di Analisi di flusso nonché gli input, gli output e la trasformazione, è possibile avviare il processo chiamando il metodo **Start** .

Il codice di esempio seguente avvia un processo di Analisi di flusso con un'ora di inizio dell'output personalizzato impostata sulle 12:12:12 UTC del 12 dicembre 2012:

   ```
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Arresto di un processo di Analisi di flusso
È possibile arrestare un processo di Analisi di flusso in esecuzione chiamando il metodo **Stop** .

   ```
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Eliminazione di un processo di Analisi di flusso
Il metodo **Delete** consente di eliminare il processo, nonché le risorse secondarie sottostanti, inclusi gli input, gli output e la trasformazione del processo.

   ```
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
Sono state fornite le nozioni di base dell'utilizzo di .NET SDK per creare ed eseguire i processi di analisi. Per altre informazioni, vedere gli argomenti seguenti:

* [Introduzione ad Analisi di flusso di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi di flusso di Azure](stream-analytics-scale-jobs.md)
* [.NET SDK per la gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
