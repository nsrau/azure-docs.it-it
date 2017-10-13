---
title: Creare un monitoraggio dei processi di Analisi di flusso a livello di codice| Microsoft Docs
description: Informazioni su come monitorare a livello di programmazione i processi di analisi di flusso creati tramite le API REST, Azure SDK o PowerShell.
keywords: monitoraggio .net, monitoraggio processi, monitoraggio app
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 2ec02cc9-4ca5-4a25-ae60-c44be9ad4835
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: 7e9d2f6f03fd539c59b105108fb46697bcd60f1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Creare un monitoraggio dei processi di Analisi di flusso a livello di codice

In questo articolo viene illustrato come abilitare il monitoraggio per un processo di analisi di flusso. Per i processi di analisi di flusso creati tramite le API REST, Azure SDK o PowerShell, il monitoraggio non è abilitato per impostazione predefinita. È possibile attivare questa funzione manualmente nel portale di Azure passando alla pagina Monitoraggio del processo e facendo clic sul pulsante Attiva oppure è possibile automatizzare il processo seguendo la procedura descritta in questo articolo. I dati di monitoraggio verranno visualizzati nell'area Metrica del portale di Azure relativa al processo di analisi di flusso.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare il processo è necessario disporre degli strumenti seguenti:

* Visual Studio 2017 o 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) scaricato e installato
* Un processo di analisi di flusso esistente che richiede l'abilitazione del monitoraggio.

## <a name="create-a-project"></a>Creare un progetto

1. Creare un'applicazione console .NET di Visual Studio C#.
2. Nella Console di Gestione pacchetti, eseguire i comandi seguenti per installare i pacchetti NuGet. Il primo è l'SDK per .NET di Analisi di flusso di Azure. Il secondo è l'SDK di Monitoraggio di Azure, che verrà usato per abilitare il monitoraggio. L'ultimo è il client Azure Active Directory che verrà usato per l'autenticazione.
   
   ```
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Aggiungere la sezione appSettings seguente al file App.config:
   
   ```
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Sostituire i valori per *SubscriptionId* e *ActiveDirectoryTenantId* con gli ID della sottoscrizione di Azure e del tenant. È possibile ottenere questi valori eseguendo il cmdlet PowerShell seguente:
   
   ```
   Get-AzureAccount
   ```
4. Aggiungere le istruzioni using seguenti al file di origine (Program.cs) nel progetto.
   
   ```
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Aggiungere un metodo helper di autenticazione.
   
     public static string GetAuthorizationHeader()
   
         {
             AuthenticationResult result = null;
             var thread = new Thread(() =>
             {
                 try
                 {
                     var context = new AuthenticationContext(
                         ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                         ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
   
                     result = context.AcquireToken(
                         resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                         clientId: ConfigurationManager.AppSettings["AsaClientId"],
                         redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                         promptBehavior: PromptBehavior.Always);
                 }
                 catch (Exception threadEx)
                 {
                     Console.WriteLine(threadEx.Message);
                 }
             });
   
             thread.SetApartmentState(ApartmentState.STA);
             thread.Name = "AcquireTokenThread";
             thread.Start();
             thread.Join();
   
             if (result != null)
             {
                 return result.AccessToken;
             }
   
             throw new InvalidOperationException("Failed to acquire token");
     }

## <a name="create-management-clients"></a>Creare i client di gestione

Il codice seguente configurerà le variabili e i client di gestione necessari.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Abilitare il monitoraggio per un processo di analisi di flusso esistente

Il codice seguente abilita il monitoraggio per un processo di analisi di flusso **esistente**. La prima parte del codice esegue una richiesta GET al servizio di Analisi dei flussi per recuperare informazioni sul processo di Analisi dei flussi specifico. Viene usa la proprietà *Id* (recuperata dalla richiesta GET) come parametro per il metodo Put nella seconda metà del codice, che invia una richiesta PUT al servizio Insights per abilitare il monitoraggio del processo di analisi di flusso.

>[!WARNING]
>Se la funzione di monitoraggio è stata precedentemente abilitata per un processo di analisi di flusso diverso, tramite il portale di Azure o a livello di programmazione con il codice riportato di seguito, **è consigliabile specificare lo stesso nome di account di archiviazione usato nel monitoraggio abilitato precedentemente.**
> 
> L'account di archiviazione è collegato all'area in cui è stato creato il processo di analisi di flusso, non specificamente al processo stesso.
> 
> Tutti i processi di analisi di flusso (e tutte le altre risorse di Azure) nella stessa area condividono questo account di archiviazione per archiviare i dati di monitoraggio. Se si specifica un account di archiviazione diverso, potrebbero verificarsi effetti collaterali imprevisti durante il monitoraggio di altri processi di analisi di flusso e/o altre risorse di Azure.
> 
> Il nome dell'account di archiviazione usato per sostituire `<YOUR STORAGE ACCOUNT NAME>` nel codice seguente deve essere un account di archiviazione presente nella stessa sottoscrizione del processo di analisi di flusso per cui viene abilitato il monitoraggio.
> 
> 

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Supporto

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

