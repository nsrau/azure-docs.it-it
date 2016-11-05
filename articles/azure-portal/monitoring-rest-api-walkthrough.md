---
title: Procedura dettagliata sull'API REST di monitoraggio di Azure | Microsoft Docs
description: Come autenticare le richieste e usare l'API REST di monitoraggio di Azure.
author: mcollier
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: mcollier

---
# Procedura dettagliata sull'API REST di monitoraggio di Azure
In questo articolo viene illustrato come eseguire l'autenticazione in modo che il codice possa usare le [informazioni di riferimento sulle API REST di monitoraggio di Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

L'API di monitoraggio di Azure consente di recuperare in modo programmatico le definizioni della metrica predefinita disponibili (cioè il tipo di metrica come tempo di CPU, richieste e così via), la granularità e i valori della metrica. Una volta recuperati, i dati possono essere salvati in un archivio dati separato come il database SQL di Azure, DocumentDB o Azure Data Lake. Da qui, se necessario, è possibile svolgere ulteriori analisi.

Oltre a essere compatibile con vari punti dati della metrica, come illustrato in questo articolo, l'API di monitoraggio consente di elencare le regole di avviso, di visualizzare i registri delle attività e molto altro ancora. Per un elenco completo delle operazioni disponibili, vedere le [informazioni di riferimento sulle API REST di monitoraggio di Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## Autenticazione delle richieste di monitoraggio di Azure
Innanzitutto è necessario autenticare la richiesta.

Tutte le attività eseguite sull'API di monitoraggio di Azure usano il modello di autenticazione Azure Resource Manager. Di conseguenza, tutte le richieste devono essere autenticate con Azure Active Directory (Azure AD). Un approccio per autenticare l'applicazione client consiste nel creare un'entità servizio di Azure AD e recuperare il token di autenticazione (JTW). Il seguente script di esempio mostra la creazione di un'entità servizio di Azure AD tramite PowerShell. Per una procedura più dettagliata, fare riferimento alla documentazione sull'[uso di Azure PowerShell per creare un'entità servizio per accedere alle risorse](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). È anche possibile [creare un'entità servizio tramite il portale di Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Insights" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Per eseguire query nell'API di monitoraggio di Azure, l'applicazione client deve effettuare l'autenticazione usando l'entità servizio creata in precedenza. Nel seguente esempio di script di PowerShell di esempio viene illustrato un approccio in cui viene usato [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) per ottenere il token di autenticazione JTW. Il token JTW viene trasmesso come parte di un parametro di autorizzazione HTTP nelle richieste all'API di Azure Insights.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Una volta completato il passaggio di configurazione dell'autenticazione, è possibile eseguire le query con l'API REST di monitoraggio di Azure. Esistono due query utili:

1. Elencare le definizioni delle metriche per una risorsa
2. Recuperare i valori delle metriche

## Recuperare le definizioni delle metriche
> [!NOTE]
> Per recuperare le definizioni delle metriche mediante l'API REST di monitoraggio di Azure, usare "2016-03-01" come versione dell'API.
> 
> 

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Per un'app per la logica di Azure, le definizioni delle metriche avranno un aspetto simile alla schermata seguente:

![Alt "Visualizzazione JSON della risposta alle definizioni delle metriche"](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Per ulteriori informazioni, vedere la documentazione [Elencare le definizioni delle metriche per una risorsa nell'API REST di monitoraggio di Azure](https://msdn.microsoft.com/library/azure/mt743621.aspx).

## Recuperare i valori delle metriche
Una volta che le definizioni delle metriche disponibili sono note, è possibile recuperare i relativi valori. Usare il 'valore' del nome della metrica (non ' localizedValue') per tutte le richieste di filtro (ad esempio, per recuperare i punti dati delle metriche 'CpuTime' e 'Requests'). Se non è specificato alcun filtro, viene restituita la metrica predefinita.

> [!NOTE]
> Per recuperare i valori delle metriche mediante l'API REST di monitoraggio di Azure, usare "2016-06-01" come versione dell'API.
> 
> 

**Metodo**: GET

**URI della richiesta**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*

Ad esempio, per recuperare i dati della metrica RunsSucceeded per la finestra temporale specificata e per un intervallo di tempo di 1 ora, la richiesta sarà come mostrato di seguito:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

Il risultato sarà simile alla seguente schermata di esempio:

![Alt "Risposta JSON che mostra il valore della metrica per il tempo medio di risposta"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Per recuperare più punti dati o di aggregazione, aggiungere i nomi delle definizioni delle metriche e i tipi di aggregazione al filtro, come illustrato nell'esempio seguente:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### Usare ARMClient
Un'alternativa all'uso di PowerShell (come illustrato in precedenza) consiste nell'usare [ARMClient](https://github.com/projectkudu/ARMClient) su computer Windows. ARMClient gestisce automaticamente l'autenticazione di Azure AD (e il token JTW risultante). Di seguito viene illustrato l'uso di ARMClient per recuperare i dati delle metriche:

1. Installare [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/ARMClient).
2. In una finestra del terminale digitare *armclient.exe login*. Viene richiesto di accedere ad Azure.
3. Digitare *armclient GET [your\_resource\_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Digitare *armclient GET [your\_resource\_id]/providers/microsoft.insights/metrics?api-version=2016-06-01*

![Alt "Uso di ARMClient con l'API REST di monitoraggio di Azure"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)

## Recuperare l'ID risorsa
L'uso dell'API REST può aiutare davvero a comprendere le definizioni delle metriche disponibili, la granularità e i valori correlati. Tali informazioni sono utili quando si usa [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Per il codice precedente, l'ID risorsa da usare è il percorso completo verso la risorsa Azure desiderata. Ad esempio, per eseguire una query su un'App Web di Azure, l'ID risorsa potrebbe essere:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Il seguente elenco contiene alcuni esempi di formati di ID risorsa per le varie risorse di Azure:

* **Hub IoT** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Pool elastico di SQL** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **Database SQL (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Bus di servizio** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Set di scalabilità di VM** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VM** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Hub eventi** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Esistono approcci alternativi per recuperare l'ID risorsa, tra cui l'uso di Esplora risorse di Azure, la visualizzazione della risorsa desiderata nel portale di Azure e tramite PowerShell o l'interfaccia della riga di comando di Azure.

### Esplora risorse di Azure
Per trovare l'ID risorsa per la risorsa desiderata, un approccio utile consiste nell'usare lo strumento [Esplora risorse di Azure](https://resources.azure.com). Individuare la risorsa desiderata e quindi esaminare l'ID indicato, come illustrato nella schermata seguente:

![Alt "Esplora risorse di Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### Portale di Azure
L'ID risorsa può anche essere ottenuto dal portale di Azure. A tale scopo, accedere alla risorsa desiderata e scegliere Proprietà. L'ID risorsa viene visualizzato nel pannello Proprietà, come illustrato nella schermata seguente:

![Alt "ID risorsa visualizzato nel pannello Proprietà del portale di Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### Azure PowerShell
L'ID risorsa può essere recuperato anche tramite i cmdlet di Azure PowerShell. Ad esempio, per ottenere l'ID risorsa per un'App Web di Azure, eseguire il cmdlet Get-AzureRmWebApp, come illustrato nella seguente schermata:

![Alt "ID risorsa ottenuto tramite PowerShell"](./media\\monitoring-rest-api-walkthrough\\resourceid_powershell.png)

### Interfaccia della riga di comando di Azure
Per recuperare l'ID risorsa tramite l'interfaccia della riga di comando di Azure, eseguire il comando 'azure webapp show', specificando l'opzione '-json' come mostrato nella schermata seguente:

![Alt "ID risorsa ottenuto tramite PowerShell"](./media\\monitoring-rest-api-walkthrough\\resourceid_azurecli.png)

## Recuperare i dati del registro attività
Oltre a lavorare con le definizioni delle metriche e i valori correlati, è anche possibile recuperare interessanti informazioni aggiuntive relative alle risorse di Azure. Ad esempio, è possibile eseguire query sui dati del [registro attività](https://msdn.microsoft.com/library/azure/dn931934.aspx). Nell'esempio seguente viene illustrato l'uso dell'API REST di monitoraggio di Azure per eseguire query sui dati del registro attività all'interno di un intervallo di date specifico per una sottoscrizione di Azure:

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## Passaggi successivi
* Esaminare la [panoramica sul monitoraggio](../monitoring-and-diagnostics/monitoring-overview.md).
* Visualizzare le [metriche supportate con il monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Esaminare le [informazioni di riferimento sulle API REST di monitoraggio di Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Esaminare [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).

<!---HONumber=AcomDC_0928_2016-->