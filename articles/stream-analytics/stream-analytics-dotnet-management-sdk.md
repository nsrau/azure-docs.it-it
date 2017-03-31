---
title: Management .NET SDK per Analisi di flusso | Documentazione Microsoft
description: 'Introduzione a .NET SDK per la gestione di Analisi di flusso. Informazioni su come configurare ed eseguire i processi di analisi: creare un progetto, gli input, gli output e le trasformazioni.'
keywords: .NET SDK, API di analisi
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: b2729dfdb6bf85e6d254b08669eeb259f4ab59d5
ms.lasthandoff: 03/06/2017


---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK: impostare ed eseguire processi di analisi tramite l'API di Analisi di flusso di Azure per .NET
Informazioni su come impostare ed eseguire processi di analisi tramite l'API di Analisi di flusso per .NET usando Management .NET SDK. Impostare un progetto, creare origini di input e output, trasformazioni e avviare e arrestare i processi. Per i processi di analisi, è possibile trasmettere i dati di flusso dall'archiviazione BLOB o da un hub eventi.

Vedere la [documentazione di riferimento sulla gestione per l'API di Analisi di flusso per .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Analisi dei flussi di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Analisi di flusso consente ai clienti di configurare processi di flusso per analizzare i flussi di dati e di condurre operazioni di analisi pressoché in tempo reale.  

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* Installare Visual Studio 2017 o 2015.
* Scaricare e installare [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Creare un gruppo di risorse di Azure nella sottoscrizione. Di seguito è riportato un esempio di script di Azure PowerShell: Per informazioni su Azure PowerShell , vedere [Installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs);  

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
2. Nella Console di Gestione pacchetti, eseguire i comandi seguenti per installare i pacchetti NuGet. Il primo è .NET SDK di gestione di Analisi di flusso di Azure. Il secondo è il client Azure Active Directory che verrà usato per l'autenticazione.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
3. Aggiungere la sezione **appSettings** seguente al file App.config:
   
        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>

    Sostituire i valori per **SubscriptionId** e **ActiveDirectoryTenantId** con gli ID della sottoscrizione di Azure e del tenant. È possibile ottenere questi valori eseguendo il cmdlet Azure PowerShell seguente:

        Get-AzureAccount

1. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. Aggiungere un metodo helper di autenticazione:

   ```   
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Creare un client di gestione di Analisi di flusso.
Un oggetto **StreamAnalyticsManagementClient** consente di gestire il processo e i componenti di processo, ad esempio l'input, l'output e la trasformazione.

Aggiungere il codice seguente all'inizio del metodo **Main** .

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
        ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Il valore della variabile **resourceGroupName** deve essere lo stesso del nome del gruppo di risorse creato o selezionato nei passaggi preliminari.

Per automatizzare l'aspetto di presentazione delle credenziali della creazione del processo, fare riferimento a [Autenticazione di un'entità servizio con Gestione risorse di Azure](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Nelle sezioni rimanenti di questo articolo si presuppone che il codice sia all'inizio del metodo **Main** .

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.
Il codice seguente crea un processo di Analisi di flusso nel gruppo di risorse che è stato definito. Verranno aggiunti un input, un output e la trasformazione al processo in un secondo momento.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Creare un'origine di input di Analisi di flusso.
Il codice seguente crea un'origine di input di Analisi di flusso con il tipo di origine di input BLOB e la serializzazione CSV. Per creare un'origine di input di hub eventi, usare **EventHubStreamInputDataSource** anziché **BlobStreamInputDataSource**. Analogamente, è possibile personalizzare il tipo di serializzazione dell'origine di input.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Le origini di input, dall'archiviazione BLOB o un hub eventi, sono legate a un processo specifico. Per usare la stessa origine di input per processi diversi, è necessario chiamare nuovamente il metodo e specificare un nome diverso.

## <a name="test-a-stream-analytics-input-source"></a>Testare un'origine di input di Analisi di flusso
Il metodo **TestConnection** verifica se il processo di Analisi di flusso è in grado di connettersi all'origine di input, nonché altri aspetti specifici del tipo di origine di input. Ad esempio, nell'origine di input BLOB creata in precedenza, il metodo verifica che il nome dell'account di archiviazione e la coppia di chiavi possano essere usati per connettersi all'account di archiviazione, nonché verificare che il contenitore specificato esista.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Creare una destinazione di output di Analisi di flusso
La creazione di una destinazione di output è molto simile alla creazione di un'origine di input di Analisi di flusso. Analogamente alle origini di input, le destinazioni di output sono legate a un processo specifico. Per usare la stessa destinazione di output per processi diversi, è necessario chiamare nuovamente il metodo e specificare un nome diverso.

Il codice seguente crea una destinazione di output (database SQL di Azure). È possibile personalizzare il tipo di dati della destinazione di output e/o il tipo di serializzazione.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Testare una destinazione di output di Analisi di flusso
Una destinazione di output di Analisi di flusso dispone inoltre del metodo **TestConnection** per il test delle connessioni.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Creare una trasformazione di Analisi di flusso
Il codice seguente crea una trasformazione di Analisi di flusso con la query "select * from Input" e specifica l'assegnazione di un'unità di streaming per il processo di Analisi di flusso. Per altre informazioni sulla regolazione di unità di streaming, vedere [Scalabilità dei processi di Analisi di flusso di Azure](stream-analytics-scale-jobs.md).

    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Analogamente all'input e all'output, una trasformazione è inoltre collegata a un processo di Analisi di flusso specifico in cui è stato creata.

## <a name="start-a-stream-analytics-job"></a>Avvio di un processo di Analisi di flusso
Dopo aver creato un processo di Analisi di flusso nonché gli input, gli output e la trasformazione, è possibile avviare il processo chiamando il metodo **Start** .

Il codice di esempio seguente avvia un processo di Analisi di flusso con un'ora di inizio dell'output personalizzato impostata sulle 12:12:12 UTC del 12 dicembre 2012:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>Arresto di un processo di Analisi di flusso
È possibile arrestare un processo di Analisi di flusso in esecuzione chiamando il metodo **Stop** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Eliminazione di un processo di Analisi di flusso
Il metodo **Delete** consente di eliminare il processo, nonché le risorse secondarie sottostanti, inclusi gli input, gli output e la trasformazione del processo.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
Sono state fornite le nozioni di base dell'utilizzo di .NET SDK per creare ed eseguire i processi di analisi. Per altre informazioni, vedere gli argomenti seguenti:

* [Introduzione ad Analisi di flusso di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
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
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

