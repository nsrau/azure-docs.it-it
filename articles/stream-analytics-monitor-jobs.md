<properties 
	pageTitle="Processi di monitoraggio di Analisi dei flussi | Azure" 
	description="Monitorare i processi di Analisi dei flussi a livello di codice." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/07/2015" 
	ms.author="jeffstok"/>


# Monitorare i processi di Analisi dei flussi a livello di codice
In questo articolo viene illustrato come abilitare il monitoraggio per un processo di Analisi dei flussi. Nei processi di Analisi dei flussi creati tramite le API REST, Azure SDK o Powershell il monitoraggio non è abilitato per impostazione predefinita. È possibile attivare manualmente questa funzione nel portale di Azure passando alla pagina di monitoraggio del processo e facendo clic sul pulsante Attiva oppure è possibile automatizzare questo processo attenendosi alla procedura descritta in questo articolo. I dati di monitoraggio verranno visualizzati nella scheda "Monitoraggio" nel portale di Azure relativa al processo di Analisi dei flussi.

![Scheda Processi di monitoraggio](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

- Visual Studio 2012 o 2013
- Scaricare e installare [Azure .NET SDK](http://azure.microsoft.com/downloads/).
- Un processo di Analisi dei flussi esistente che richiede l'attivazione del monitoraggio.

## Configurare un progetto

1.	Creare un'applicazione console .Net di Visual Studio C#.
2.	Nella Console di Gestione pacchetti, eseguire i comandi seguenti per installare i pacchetti NuGet. Il primo è l'SDK per .NET di Analisi di flusso di Azure. Il secondo è l'SDK di Azure Insights, che verrà utilizzato per abilitare il monitoraggio. L’ultimo è il client Azure Active Directory che verrà usato per l'autenticazione.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics –Pre
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.	Aggiungere la sezione appSettings seguente al file App.config:

    ```
    <appSettings>
    	<!--CSM Prod related values-->
    	<add key="ActiveDirectoryEndpoint" value="https://login.windows-ppe.net/" />
    	<add key="ResourceManagerEndpoint" value="https://api-current.resources.windows-int.net/" />
    	<add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    	<add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
    	<add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
    	<add key="SubscriptionId" value="<YOUR AZURE SUBSCRIPTION ID>" />
    	<add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
	```
Sostituire i valori per *SubscriptionId* e *ActiveDirectoryTenantId* con gli ID della sottoscrizione Azure e del tenant. È possibile ottenere questi valori eseguendo il cmdlet PowerShell seguente:

    ```
    Get-AzureAccount
    ```
4.	Aggiungere le istruzioni using seguenti al file di origine (Program.cs) nel progetto. 

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
5.	Aggiungere un metodo helper di autenticazione.

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

## Creare i client di gestione
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

## Abilitare il monitoraggio per un processo di Analisi dei flussi esistente.

Il codice seguente consentirà di abilitare il monitoraggio per un processo di Analisi dei flussi **esistente**. La prima parte del codice esegue una richiesta GET al servizio di Analisi dei flussi per recuperare informazioni sul processo di Analisi dei flussi specifico. Viene utilizzata la proprietà "Id" (recuperata dalla richiesta GET) come parametro per il metodo Put nella seconda metà del codice che invia una richiesta PUT al servizio Insights per abilitare il monitoraggio per il processo di Analisi dei flussi.

> [AZURE.WARNING]-Se in precedenza è stato abilitato il monitoraggio per un processo di Analisi dei flussi diverso, tramite il portale di Azure o a livello di codice, **è consigliabile fornire lo stesso nome account di archiviazione fornito al momento dell’abilitazione del monitoraggio.** -L'account di archiviazione è collegato all'area in cui è stato creato il processo di Analisi dei flussi, non specificamente per il processo stesso. -Tutti i processi di Analisi dei flussi (e tutte le altre risorse di Azure) nella stessa area condividono questo account di archiviazione per archiviare i dati di monitoraggio. Se si specifica un account di archiviazione diverso, potrebbe causare effetti collaterali imprevisti al monitoraggio degli altri processi di Analisi dei flussi e/o altre risorse di Azure. -Il nome dell’account di archiviazione utilizzato per sostituire ```“<YOUR STORAGE ACCOUNT NAME>”``` di seguito deve essere un account di archiviazione che si trova nella stessa sottoscrizione del processo di Analisi dei flussi per il quale si sta abilitando il monitoraggio.

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



## Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=54-->