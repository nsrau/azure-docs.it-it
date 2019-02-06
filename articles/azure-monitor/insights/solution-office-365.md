---
title: Soluzione Gestione di Office 365 in Azure | Microsoft Docs
description: In questo articolo vengono fornite informazioni dettagliate sulla configurazione e l'uso della soluzione Office 365 in Azure.  Include una descrizione dettagliata dei record di Office 365 creati in Log Analytics.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: 370483b92dcd2c468cd676a32db0ded80e8814d0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216613"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Soluzione Gestione di Office 365 in Azure (Anteprima)

![Logo di Office 365](media/solution-office-365/icon.png)

La soluzione Gestione di Office 365 consente di monitorare l'ambiente Office 365 in Log Analytics.

- Monitoraggio delle attività degli utenti negli account di Office 365 per analizzare i modelli di utilizzo nonché identificare le tendenze di comportamento. Ad esempio, è possibile estrarre scenari di uso specifici, ad esempio i file condivisi all'esterno dell'organizzazione o i siti di SharePoint più diffusi.
- Monitoraggio delle attività dell'amministratore per tenere traccia delle modifiche alla configurazione o le operazioni con privilegi elevati.
- Rilevamento e analisi del comportamento utente indesiderato, che può essere personalizzato per esigenze organizzative.
- Dimostrazione di conformità e controllo. Ad esempio, è possibile monitorare le operazioni di accesso nei file riservati, favorendo così il processo di conformità e controllo.
- Risoluzione dei problemi operativi usando le [ricerche log](../log-query/log-query-overview.md) per i dati di attività di Office 365 dell'organizzazione.

## <a name="prerequisites"></a>Prerequisiti
Prima di installare e configurare la soluzione, è richiesto quanto segue.

- Sottoscrizione a Office 365 dell'organizzazione.
- Credenziali per un account utente che rappresenta un Amministratore globale.
- Per ricevere i dati di controllo, è necessario [configurare il controllo](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) nella sottoscrizione di Office 365.  Si noti che [controllo delle cassette postali](https://technet.microsoft.com/library/dn879651.aspx) viene configurato separatamente.  È comunque possibile installare la soluzione e raccogliere altri dati se il controllo non è configurato.
 

## <a name="management-packs"></a>Management Pack
Questa soluzione non installa alcun Management Pack nei [gruppi di gestione connessi](../platform/om-agents.md).
  
## <a name="install-and-configure"></a>Installare e configurare
Per iniziare, aggiungere la [soluzione di Office 365 alla sottoscrizione](solutions.md#install-a-management-solution). Dopo aver aggiunto la soluzione, è necessario eseguire i passaggi di configurazione descritti in questa sezione per consentire l'accesso alla sottoscrizione di Office 365.

### <a name="required-information"></a>Informazioni necessarie
Prima di iniziare questa procedura, raccogliere le informazioni seguenti.

Dall'area di lavoro di Log Analytics:

- Nome dell'area di lavoro: area di lavoro in cui verranno raccolti i dati di Office 365.
- Nome gruppo di risorse: Gruppo di risorse che contiene il gruppo di risorse.
- ID sottoscrizione di Azure: la sottoscrizione che contiene il gruppo di risorse.

Dalla sottoscrizione di Office 365:

- Nome utente: indirizzo di posta elettronica di un account amministrativo.
- ID tenant: ID univoco per la sottoscrizione di Office 365.
- ID client: stringa di 16 caratteri che rappresenta il client di Office 365.
- Segreto client: stringa crittografata necessaria per l'autenticazione.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Creare un'applicazione di Office 365 in Azure Active Directory
Il primo passaggio consiste nel creare un'applicazione in Azure Active Directory che userà la soluzione di gestione per accedere alla soluzione di Office 365.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).
1. Selezionare **Azure Active Directory** e quindi **Registrazioni per l'app**.
1. Fare clic su **Registrazione nuova applicazione**.

    ![Aggiungere la registrazione per l'app](media/solution-office-365/add-app-registration.png)
1. Impostare **Nome** e **URL di accesso** per l'applicazione.  Il nome deve essere descrittivo.  Usare _http://localhost_ per l'URL e lasciare _App Web/API_ come **Tipo di applicazione**.
    
    ![Creare l'applicazione](media/solution-office-365/create-application.png)
1. Fare clic su **Crea** e convalidare le informazioni dell'applicazione.

    ![App registrata](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Configurare l'applicazione per Office 365

1. Fare clic su **Impostazioni** per aprire il menu **Impostazioni**.
1. Selezionare **Proprietà**. Impostare **Multi-tenant** su _Sì_.

    ![Impostazioni multi-tenant](media/solution-office-365/settings-multitenant.png)

1. Selezionare **Autorizzazioni necessarie** nel menu **Impostazioni** e quindi fare clic su **Aggiungi**.
1. Fare clic su **Selezionare un'API** e quindi **Office 365 Management APIs** (API di gestione di Office 365). Fare clic su **Office 365 Management APIs** (API di gestione di Office 365). Fare clic su **Seleziona**.

    ![Selezionare l'API](media/solution-office-365/select-api.png)

1. In **Selezionare le autorizzazioni** selezionare le opzioni seguenti per **Autorizzazioni applicazione** e **Autorizzazioni delegate**:
    - Legge le informazioni sull'integrità dei servizi per l'organizzazione
    - Legge i dati dell'attività per l'organizzazione
    - Legge i report attività per l'organizzazione

    ![Selezionare l'API](media/solution-office-365/select-permissions.png)

1. Fare clic su **Seleziona** e quindi su **Fine**.
1. Fare clic su **Concedi autorizzazioni** e quindi su **Sì** quando viene chiesto di confermare.

    ![Concedere le autorizzazioni](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Aggiungere una chiave per l'applicazione

1. Select **Chiavi** nel menu **Impostazioni**.
1. Impostare **Descrizione** e **Durata** per la nuova chiave.
1. Fare clic su **Salva** e quindi copiare il **valore** che viene generato.

    ![Chiavi](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Aggiungere il consenso dell'amministratore
Per abilitare l'account amministrativo per la prima volta, è necessario fornire il consenso dell'amministratore per l'applicazione. È possibile eseguire questa operazione con uno script di PowerShell. 

1. Salvare lo script seguente come *office365_consent.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Eseguire lo script con il comando seguente. Verranno richieste due volte le credenziali. Specificare prima di tutto le credenziali per l'area di lavoro di Log Analytics e quindi le credenziali di amministratore globale per il tenant di Office 365.
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    Esempio:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Verrà visualizzata una finestra simile alla seguente. Fare clic **Accept**.
    
    ![Consenso dell'amministratore](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Creare una sottoscrizione all'area di lavoro di Log Analytics
L'ultimo passaggio consiste nel creare la sottoscrizione dell'applicazione all'area di lavoro di Log Analytics. Anche questa operazione può essere eseguita tramite uno script di PowerShell.

1. Salvare lo script seguente come *office365_subscription.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Eseguire lo script con il comando seguente:
    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```
    Esempio:

    ```
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>risoluzione dei problemi

Se è già stata eseguita la sottoscrizione dell'applicazione per quest'area di lavoro o se è stata eseguita la sottoscrizione del tenant per un'altra area di lavoro, è possibile che venga visualizzato l'errore seguente.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Se si specificano valori di parametro non validi, è possibile che venga visualizzato l'errore seguente.

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>Disinstallare
È possibile rimuovere la soluzione di Gestione di Office 365 seguendo la procedura descritta in [Rimuovere una soluzione di gestione](solutions.md#remove-a-management-solution). In questo modo, tuttavia, la raccolta dei dati da Office 365 a Log Analytics non viene interrotta. Seguire la procedura seguente per annullare la sottoscrizione a Office 365 e interrompere la raccolta dei dati.

1. Salvare lo script seguente come *office365_unsubscribe.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Get-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Eseguire lo script con il comando seguente:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Esempio:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Raccolta dei dati
### <a name="supported-agents"></a>Agenti supportati
La soluzione Office 365 non recupera i dati dagli [agenti Log Analytics](../platform/agent-data-sources.md).  Recupera dati direttamente da Office 365.

### <a name="collection-frequency"></a>Frequenza della raccolta
L'operazione iniziale di raccolta dei dati può richiedere alcune ore. Dopo l'avvio della raccolta, ogni volta che viene creato un record, Office 365 invia a Log Analytics una [notifica webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) con dati dettagliati. Il record è disponibile in Log Analytics entro pochi minuti dalla ricezione.

## <a name="using-the-solution"></a>Uso della soluzione
Quando si aggiunge la soluzione Office 365 all'area di lavoro di Log Analytics, il riquadro **Office 365** verrà aggiunto al dashboard. Il riquadro visualizza un conteggio e la rappresentazione grafica del numero di computer nell'ambiente con la relativa conformità degli aggiornamenti.<br><br>
![Riquadro di riepilogo di Office 365](media/solution-office-365/tile.png)  

Fare clic sul riquadro **Office 365** per aprire la dashboard di **Office 365**.

![Dashboard di Office 365](media/solution-office-365/dashboard.png)  

Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca i primi dieci avvisi per numero corrispondente ai criteri della colonna per l'ambito e l'intervallo di tempo specificati. È possibile eseguire una ricerca di log che fornisce l'intero elenco facendo clic su Visualizza tutto nella parte inferiore della colonna o facendo clic sull'intestazione di colonna.

| Colonna | DESCRIZIONE |
|:--|:--|
| Operazioni | Fornisce informazioni sugli utenti attivi da tutte le sottoscrizioni Office 365 monitorate. Sarà inoltre possibile visualizzare il numero di attività che si verificano nel corso del tempo.
| Exchange | Mostra i dettagli delle attività di Exchange Server, ad esempio Add-Mailbox Permission o Set-Mailbox. |
| SharePoint | Mostra le prime attività che gli utenti eseguono nei documenti di SharePoint. Quando si visualizzano i dettagli da questo riquadro, nella pagina di ricerca vengono visualizzati i dettagli di queste attività, ad esempio il documento di destinazione e il percorso di questa attività. Ad esempio, per un evento di accesso al file sarà possibile visualizzare il documento a cui si accede, il nome account associato e l'indirizzo IP. |
| Azure Active Directory | Include le attività degli utenti superiori, ad esempio i tentativi di accesso e di reimpostazione password utente. È possibile visualizzare i dettagli di queste attività, ad esempio lo stato dei risultati. Questa funzionalità è particolarmente utile se si desidera monitorare le attività sospette in Azure Active Directory. |




## <a name="log-analytics-records"></a>Record di Log Analytics

Tutti i record creati nell'area di lavoro Log Analytics dalla soluzione Office 365 dispongono di un **tipo** di **OfficeActivity**.  La proprietà **OfficeWorkload** determina a quale servizio di Office 365 fa riferimento il record: Exchange, AzureActiveDirectory, SharePoint o OneDrive.  La proprietà **RecordType** specifica il tipo di operazione.  Le proprietà variano per ogni tipo di operazione e vengono visualizzate nelle tabelle seguenti.

### <a name="common-properties"></a>Proprietà comuni
Le proprietà seguenti sono comuni a tutti i record di Office 365.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | L'indirizzo IP del dispositivo usato quando l'attività è stata registrata. L'indirizzo IP viene visualizzato in formato di indirizzo IPv4 o IPv6. |
| OfficeWorkload | Servizio di Office 365 a cui il record fa riferimento.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operazione | Il nome dell'attività utente o l'attività dell'amministratore.  |
| OrganizationId | GUID per il tenant di Office 365 dell'organizzazione. Questo valore sarà sempre lo stesso per l'organizzazione, indipendentemente dal servizio Office 365 in cui si verifica. |
| RecordType | Tipo di operazione eseguita. |
| ResultStatus | Indica se l'azione (specificata nella proprietà Operation) è andata a buon fine o meno. I possibili valori sono Succeeded, PartiallySucceded o Failed. Per le attività dell'amministratore di Exchange, il valore è True o False. |
| UserId | Il nome UPN (User Principal Name) dell'utente che ha eseguito l'azione ha generato la registrazione del record, ad esempio my_name@my_domain_name. Si noti che sono inclusi anche i record per l'attività eseguita dall'account di sistema (ad esempio SHAREPOINT\system o NTAUTHORITY\SYSTEM). | 
| UserKey | Un ID alternativo per l'utente identificato con la proprietà UserId.  Ad esempio, questa proprietà viene popolata con l'ID univoco passport (PUID) per gli eventi eseguiti dagli utenti in SharePoint, OneDrive for Business ed Exchange. Questa proprietà può inoltre specificare lo stesso valore della proprietà UserID per gli eventi che si verificano in altri servizi ed eventi eseguiti dall'account di sistema|
| UserType | Il tipo di utente che ha eseguito l'operazione.<br><br>Admin<br>Applicazione<br>DcAdmin<br>Normale<br>Riservato<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base di Azure Active Directory
Le proprietà seguenti sono comuni a tutti i record di Azure Active Directory.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Il tipo di evento di Azure AD. |
| ExtendedProperties | Le proprietà estese dell'evento di Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Accesso all'account di Azure Active Directory
Questi record vengono creati quando un utente di Active Directory tenta di accedere.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Applicazione | L'applicazione che attiva l'evento di accesso all'account, ad esempio Office 15. |
| Client | Dettagli relativi al dispositivo del client, al sistema operativo del dispositivo e al browser del dispositivo usato per l'evento di accesso all'account. |
| LoginStatus | Questa proprietà deriva direttamente da OrgIdLogon.LoginStatus. Il mapping di diversi errori di accesso interessanti potrebbe essere eseguito da algoritmi di avviso. |
| UserDomain | Le informazioni sull'identità del tenant (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Questi record vengono creati quando vengono apportate modifiche o aggiunte agli oggetti di Azure Active Directory.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | L'utente su cui è stata eseguita l'azione (identificato dalla proprietà Operation). |
| Attore | Utente o entità servizio che ha eseguito l'azione. |
| ActorContextId | Il GUID dell'organizzazione a cui appartiene l'attore. |
| ActorIpAddress | Indirizzo IP dell'attore in formato di indirizzo IPv4 o IPv6. |
| InterSystemsId | GUID che rileva le azioni nei vari componenti all'interno del servizio di Office 365. |
| IntraSystemId |   Il GUID generato da Azure Active Directory per tenere traccia dell'azione. |
| SupportTicketId | L'ID del ticket di supporto cliente per l'azione in situazioni in cui si agisce per conto di qualcuno. |
| TargetContextId | Il GUID dell'organizzazione a cui appartiene l'utente di destinazione. |


### <a name="data-center-security"></a>Sicurezza del centro dati
Questi record vengono creati dai dati di controllo della sicurezza del centro dati.  

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| EffectiveOrganization | Il nome del tenant a cui l'elevazione/cmdlet sono destinati. |
| ElevationApprovedTime | Il timestamp di quando è stata approvata l'elevazione. |
| ElevationApprover | Il nome di una gestione di Microsoft. |
| ElevationDuration | La durata per cui è stata attiva l'elevazione. |
| ElevationRequestId |  Identificatore univoco per la richiesta di elevazione. |
| ElevationRole | Il ruolo per cui è stata richiesta l'elevazione. |
| ElevationTime | L'ora di inizio dell'elevazione. |
| Start_Time | L'ora di inizio dell'esecuzione del cmdlet. |


### <a name="exchange-admin"></a>Amministratore di Exchange
Questi record vengono creati quando vengono apportate modifiche alla configurazione di Exchange.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Specifica se il cmdlet è stato eseguito da un utente nell'organizzazione, dal personale del centro dati di Microsoft, da un account di servizio del centro dati o da un amministratore delegato. Il valore False indica che il cmdlet è stato eseguito da un utente nell'organizzazione. Il valore True indica che il cmdlet è stato eseguito dal personale del centro dati, un account del servizio del centro dati o un amministratore delegato. |
| ModifiedObjectResolvedName |  Questo è il nome descrittivo dell'oggetto modificato dal cmdlet. Viene registrato solo se il cmdlet modifica l'oggetto. |
| OrganizationName | Nome del tenant. |
| OriginatingServer | Il nome del server da cui è stato eseguito il cmdlet. |
| Parametri | Nome e valore per tutti i parametri usati con il cmdlet identificato nella proprietà Operations. |


### <a name="exchange-mailbox"></a>Cassetta postale di Exchange
Questi record vengono creati quando vengono apportate modifiche o aggiunte alle cassette postali di Exchange.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informazioni sul client di posta elettronica usato per eseguire l'operazione, ad esempio una versione del browser, la versione di Outlook e le informazioni sul dispositivo mobile. |
| Client_IPAddress | L'indirizzo IP del dispositivo usato quando l'operazione è stata registrata. L'indirizzo IP viene visualizzato in formato di indirizzo IPv4 o IPv6. |
| ClientMachineName | Nome del computer che ospita il client di Outlook. |
| ClientProcessName | Il client di posta elettronica usato per accedere alla cassetta postale. |
| ClientVersion | Versione del client di posta elettronica. |
| InternalLogonType | Riservato per uso interno. |
| Logon_Type | Indica il tipo di utente che ha eseguito l'accesso alla cassetta postale e ha compiuto l'operazione che è stata registrata. |
| LogonUserDisplayName |    Il nome descrittivo dell'utente che ha eseguito l'operazione. |
| LogonUserSid | L'ID di sicurezza dell'utente che ha eseguito l'operazione. |
| MailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso. |
| MailboxOwnerMasterAccountSid | ID di sicurezza dell'account proprietario della cassetta postale. |
| MailboxOwnerSid | L'ID di sicurezza del proprietario della cassetta postale. |
| MailboxOwnerUPN | L'indirizzo e-mail della persona a cui appartiene la cassetta postale che ha effettuato l'accesso. |


### <a name="exchange-mailbox-audit"></a>Controllo della cassetta postale di Exchange
Questi record vengono creati quando viene creata una voce di controllo delle cassette postali.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Elemento | Rappresenta l'elemento su cui è stata eseguita l'operazione | 
| SendAsUserMailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso per inviare e-mail. |
| SendAsUserSmtp | Indirizzo SMTP dell'utente che viene rappresentato. |
| SendonBehalfOfUserMailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso per inviare e-mail per conto di. |
| SendOnBehalfOfUserSmtp | Indirizzo SMTP dell'utente per conto del quale viene inviata l'e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Gruppo di controllo della cassetta postale di Exchange
Questi record vengono creati quando vengono apportate modifiche o aggiunte ai gruppi di Exchange.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informazioni su ogni elemento nel gruppo. |
| CrossMailboxOperations | Indica se nell'operazione è coinvolta più di una cassetta postale. |
| DestMailboxId | Impostare solo se il parametro CrossMailboxOperations è True. Specifica il GUID cassetta postale di destinazione. |
| DestMailboxOwnerMasterAccountSid | Impostare solo se il parametro CrossMailboxOperations è True. Specifica l'ID di sicurezza per l'account principale del proprietario della cassetta postale di destinazione. |
| DestMailboxOwnerSid | Impostare solo se il parametro CrossMailboxOperations è True. Specifica l'ID di sicurezza della cassetta postale di destinazione. |
| DestMailboxOwnerUPN | Impostare solo se il parametro CrossMailboxOperations è True. Specifica il nome UPN del proprietario della cassetta postale di destinazione. |
| DestFolder | La cartella di destinazione per operazioni quali Sposta. |
| Cartella | La cartella in cui si trova un gruppo di elementi. |
| Cartelle |     Informazioni sulle cartelle di origine coinvolte in un'operazione; ad esempio, se le cartelle vengono selezionate e quindi eliminate. |


### <a name="sharepoint-base"></a>Base SharePoint
Queste proprietà sono comuni a tutti i record di SharePoint.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica un evento che si è verificato in SharePoint. I valori possibili sono ObjectModel o SharePoint. |
| ItemType | Il tipo dell'oggetto a cui è stato effettuato l'accesso o che è stato modificato. Vedere la tabella ItemType per informazioni dettagliate sui tipi di oggetti. |
| MachineDomainInfo | Informazioni sulle operazioni di sincronizzazione dei dispositivi. Questa informazione viene segnalata solo se è presente nella richiesta. |
| MachineId |   Informazioni sulle operazioni di sincronizzazione dei dispositivi. Questa informazione viene segnalata solo se è presente nella richiesta. |
| Site_ | Il GUID del sito in cui si trova il file o la cartella a cui l'utente ha effettuato l'accesso. |
| Source_Name | L'entità che ha attivato le operazioni di controllo. I valori possibili sono ObjectModel o SharePoint. |
| UserAgent | Informazioni sul client o browser dell'utente. Queste informazioni vengono fornite dal client o dal browser. |


### <a name="sharepoint-schema"></a>Schema di SharePoint
Questi record vengono creati quando vengono apportate modifiche alla configurazione di SharePoint.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Stringa facoltativa per gli eventi personalizzati. |
| Event_Data |  Payload facoltativo per gli eventi personalizzati. |
| ModifiedProperties | La proprietà è inclusa per gli eventi amministrativi, ad esempio l'aggiunta di un utente come membro di un sito o un gruppo di amministrazione raccolta siti. La proprietà include il nome della proprietà che è stata modificata (ad esempio, il gruppo amministratore del sito), il nuovo valore della proprietà modificata (come l'utente che è stato aggiunto come amministratore del sito) e il valore precedente dell'oggetto modificato. |


### <a name="sharepoint-file-operations"></a>Operazioni sui file di SharePoint
Questi record vengono creati in risposta alle operazioni sui file in SharePoint.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Estensione di un file che viene copiato o spostato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| DestinationFileName | Il nome del file che viene copiato o spostato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| DestinationRelativeUrl | L'URL della cartella di destinazione in cui un file viene copiato o spostato. La combinazione dei valori dei parametri SiteURL, DestinationRelativeURL e DestinationFileName è lo stesso del valore della proprietà ObjectID, ovvero il nome percorso completo del file che è stato copiato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| SharingType | Il tipo di autorizzazioni di condivisione che sono state assegnate all'utente con cui è stata condivisa la risorsa. Questo utente viene identificato dal parametro UserSharedWith. |
| Site_Url | L'URL del sito in cui si trova il file o la cartella a cui l'utente ha effettuato l'accesso. |
| SourceFileExtension | L'estensione del file a cui l'utente ha effettuato l'accesso. Questa proprietà è vuota se l'oggetto a cui è stato effettuato l'accesso è una cartella. |
| SourceFileName |  Il nome del file o della cartella a cui l'utente ha effettuato l'accesso. |
| SourceRelativeUrl | L'URL della cartella che contiene il file a cui l'utente ha effettuato l'accesso. La combinazione dei valori dei parametri SiteURL, SourceRelativeURL e SourceFileName è lo stesso del valore della proprietà ObjectID, ovvero il nome percorso completo del file a cui l'utente ha effettuato l'accesso. |
| UserSharedWith |  L'utente con cui è stata condivisa una risorsa. |




## <a name="sample-log-searches"></a>Ricerche di log di esempio
La tabella seguente contiene esempi di ricerche log per i record di aggiornamento raccolti da questa soluzione.

| Query | DESCRIZIONE |
| --- | --- |
|Conteggio di tutte le operazioni per la sottoscrizione di Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Uso di siti di SharePoint|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl | sort by Count asc|
|Operazioni di accesso ai file per tipo di utente|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|Ricerca con una parola chiave specifica|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Azioni esterne di monitoraggio di Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Passaggi successivi
* Usare le ricerche log in [Log Analytics](../log-query/log-query-overview.md) per visualizzare dati dettagliati sugli aggiornamenti.
* [Creare dashboard personalizzati](../learn/tutorial-logs-dashboards.md) per visualizzare le query di ricerca di Office 365 preferite.
* [Creare avvisi](../platform/alerts-overview.md) per essere notificati in modo proattivo delle attività importanti di Office 365.  
