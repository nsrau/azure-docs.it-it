---
title: Soluzione di gestione di Office 365 in Azure
description: In questo articolo vengono fornite informazioni dettagliate sulla configurazione e l'uso della soluzione Office 365 in Azure.  Include una descrizione dettagliata dei record di Office 365 creati in Monitoraggio di Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: 14f7b5546d30d98adf4a14408882c972687a2d71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86498798"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Soluzione Gestione di Office 365 in Azure (Anteprima)

![Logo di Office 365](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Aggiornamento della soluzione
> Questa soluzione è stata sostituita dalla soluzione di disponibilità generale di [Office 365](../../sentinel/connect-office-365.md) in [Sentinel di Azure](../../sentinel/overview.md) e dalla [soluzione Azure ad Reporting and Monitoring](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Insieme forniscono una versione aggiornata della soluzione Office 365 di monitoraggio di Azure precedente con un'esperienza di configurazione migliorata. È possibile continuare a usare la soluzione esistente fino al 31 ottobre 2020.
> 
> Azure Sentinel è una soluzione di gestione degli eventi e delle informazioni di sicurezza nativa del cloud che inserisce log e fornisce funzionalità SIEM aggiuntive, tra cui rilevamenti, indagini, ricerche e informazioni dettagliate basate su Machine Learning. Con Azure Sentinel verranno ora importati i registri attività di Office 365 SharePoint e gestione di Exchange.
> 
> Azure AD Reporting fornisce una visualizzazione più completa dei log dall'attività Azure AD nell'ambiente, inclusi gli eventi di accesso, gli eventi di controllo e le modifiche apportate alla directory. Per connettere i log di Azure AD, è possibile usare il [connettore Azure Sentinel Azure ad](../../sentinel/connect-azure-active-directory.md) o configurare l' [integrazione di Azure ad logs con monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> La raccolta di Azure AD log è soggetta ai prezzi di monitoraggio di Azure.  Per ulteriori informazioni, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) .
>
> Per usare la soluzione Office 365 di Azure Sentinel:
> 1. L'uso del connettore Office 365 in Sentinel di Azure influiscono sui prezzi per l'area di lavoro. Per altre informazioni, vedere [prezzi di Sentinel di Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Se si usa già la soluzione Office 365 di monitoraggio di Azure, è necessario prima disinstallarla usando lo script nella [sezione Uninstall riportata di seguito](#uninstall).
> 3. [Abilitare la soluzione sentinel di Azure](../../sentinel/quickstart-onboard.md) nell'area di lavoro.
> 4. Passare alla pagina **connettori dati** in Sentinel di Azure e abilitare il connettore **Office 365** .
>
> ## <a name="frequently-asked-questions"></a>Domande frequenti
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>D: è possibile eseguire l'onboarding della soluzione di monitoraggio di Azure per Office 365 tra ora e 31 ottobre?
> No, gli script di onboarding della soluzione Office 365 di monitoraggio di Azure non sono più disponibili. La soluzione verrà rimossa il 31 ottobre.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>D: le tabelle e gli schemi verranno modificati?
> Il nome e lo schema della tabella **OfficeActivity** rimarranno identici a quelli della soluzione corrente. È possibile continuare a usare le stesse query nella nuova soluzione, escluse le query che fanno riferimento a Azure AD dati.
> 
> I nuovi log della [soluzione per la creazione di report e il monitoraggio di Azure ad](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) verranno inseriti nelle tabelle [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) e [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) anziché **OfficeActivity**. Per altre informazioni, vedere [How to analyze Azure ad logs](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), che è anche pertinente for Azure Sentinel and Azure Monitor Users.
> 
> Di seguito sono riportati alcuni esempi per la conversione di query da **OfficeActivity** a **SigninLogs**:
> 
> **Accessi non riusciti della query, per utente:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Visualizza Azure AD operazioni:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>D: in che modo è possibile caricare Azure Sentinel?
> Azure Sentinel è una soluzione che è possibile abilitare in un'area di lavoro Log Analytics nuova o esistente. Per altre informazioni, vedere la [documentazione sul](../../sentinel/quickstart-onboard.md)caricamento di Sentinel di Azure.
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>D: è necessario Azure Sentinel per connettere i log di Azure AD?
> È possibile configurare l' [integrazione dei log di Azure ad con monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), che non è correlato alla soluzione sentinel di Azure. Azure Sentinel fornisce un connettore nativo e contenuto predefinito per i log Azure AD. Per ulteriori informazioni, vedere la domanda seguente sul contenuto predefinito orientato alla sicurezza.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>D: quali sono le differenze durante la connessione di log Azure AD da Azure Sentinel e monitoraggio di Azure?
> Azure Sentinel e monitoraggio di Azure si connettono ai log Azure AD in base alla stessa [soluzione Azure ad Reporting e monitoraggio](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Azure Sentinel fornisce un connettore nativo con un solo clic che connette gli stessi dati e fornisce informazioni di monitoraggio.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>D: cosa è necessario modificare quando si passa alla nuova Azure AD le tabelle di Reporting e monitoraggio?
> Tutte le query che usano dati Azure AD, incluse le query in avvisi, dashboard e qualsiasi contenuto creato con Office 365 Azure AD dati, devono essere ricreate usando le nuove tabelle.
>
> Azure Sentinel e Azure AD forniscono contenuti predefiniti che è possibile usare per il passaggio alla soluzione di monitoraggio e monitoraggio Azure AD. Per altre informazioni, vedere la prossima domanda sul contenuto orientato alla sicurezza predefinito e [come usare le cartelle di lavoro di monitoraggio di Azure per i report Azure Active Directory](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>D: come è possibile usare i contenuti basati sulla sicurezza predefiniti di Azure Sentinel?
> Azure Sentinel fornisce dashboard predefiniti per la sicurezza, query di avviso personalizzate, query di ricerca, analisi e funzionalità di risposta automatica basate sui registri di Office 365 e Azure AD. Per altre informazioni, vedere le esercitazioni su GitHub e Azure Sentinel.
>
> - [Rileva le minacce predefinite](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Creare regole di analisi personalizzate per rilevare minacce sospette](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorare i dati](../../sentinel/tutorial-monitor-your-data.md)
> - [Esaminare gli eventi imprevisti con Sentinel di Azure](../../sentinel/tutorial-investigate-cases.md)
> - [Configurare le risposte automatiche alle minacce in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Community di GitHub di Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>D: Azure Sentinel fornisce connettori aggiuntivi come parte della soluzione?
> Sì, vedere [origini dati di Azure Sentinel Connect](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>D: cosa avverrà il 31 ottobre? È necessario offboard in anticipo?
> 
> - Non sarà possibile ricevere i dati dalla soluzione **Office365** . La soluzione non sarà più disponibile nel Marketplace
> - Per i clienti di Azure Sentinel, la soluzione per l'area di lavoro Log Analytics **Office365** verrà inclusa nella soluzione **SecurityInsights** di Sentinel di Azure.
> - Se non si offboard la soluzione manualmente, i dati verranno disconnessi automaticamente il 31 ottobre.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>D: i dati vengono trasferiti alla nuova soluzione?
> Sì. Quando si rimuove la soluzione **Office 365** dall'area di lavoro, i relativi dati diventeranno temporaneamente non disponibili perché lo schema è stato rimosso. Quando si Abilita il nuovo connettore **Office 365** in Sentinel, lo schema viene ripristinato nell'area di lavoro e tutti i dati già raccolti diventeranno disponibili. 
 

La soluzione di gestione di Office 365 consente di monitorare l'ambiente Office 365 in Monitoraggio di Azure.

- Monitoraggio delle attività degli utenti negli account di Office 365 per analizzare i modelli di utilizzo nonché identificare le tendenze di comportamento. Ad esempio, è possibile estrarre scenari di uso specifici, ad esempio i file condivisi all'esterno dell'organizzazione o i siti di SharePoint più diffusi.
- Monitoraggio delle attività dell'amministratore per tenere traccia delle modifiche alla configurazione o le operazioni con privilegi elevati.
- Rilevamento e analisi del comportamento utente indesiderato, che può essere personalizzato per esigenze organizzative.
- Dimostrazione di conformità e controllo. Ad esempio, è possibile monitorare le operazioni di accesso nei file riservati, favorendo così il processo di conformità e controllo.
- Risoluzione dei problemi operativi usando le [query di log](../log-query/log-query-overview.md) oltre ai dati di attività di Office 365 dell'organizzazione.


## <a name="uninstall"></a>Disinstallare

È possibile rimuovere la soluzione di Gestione di Office 365 seguendo la procedura descritta in [Rimuovere una soluzione di gestione](solutions.md#remove-a-monitoring-solution). In questo modo tuttavia la raccolta dei dati da Office 365 a Monitoraggio di Azure non verrà interrotta. Seguire la procedura seguente per annullare la sottoscrizione a Office 365 e interrompere la raccolta dei dati.

1. Salvare lo script seguente come *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
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

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Verranno richieste le credenziali. Fornire le credenziali per l'area di lavoro Log Analytics.

## <a name="data-collection"></a>Raccolta dati

L'operazione iniziale di raccolta dei dati può richiedere alcune ore. Dopo l'avvio della raccolta, Office 365 invia a Monitoraggio di Azure una [notifica webhook](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) con dati dettagliati ogni volta che viene creato un record. Il record è disponibile in Monitoraggio di Azure entro pochi minuti dalla ricezione.

## <a name="using-the-solution"></a>Uso della soluzione

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando si aggiunge la soluzione Office 365 all'area di lavoro Log Analytics, il riquadro **Office 365** verrà aggiunto al dashboard. Il riquadro visualizza un conteggio e la rappresentazione grafica del numero di computer nell'ambiente con la relativa conformità degli aggiornamenti.<br><br>
![Riquadro di riepilogo di Office 365](media/solution-office-365/tile.png)  

Fare clic sul riquadro **Office 365** per aprire la dashboard di **Office 365**.

![Dashboard di Office 365](media/solution-office-365/dashboard.png)  

Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca i primi dieci avvisi per numero corrispondente ai criteri della colonna per l'ambito e l'intervallo di tempo specificati. È possibile eseguire una ricerca di log che fornisce l'intero elenco facendo clic su Visualizza tutto nella parte inferiore della colonna o facendo clic sull'intestazione di colonna.

| Colonna | Descrizione |
|:--|:--|
| Operazioni | Fornisce informazioni sugli utenti attivi da tutte le sottoscrizioni Office 365 monitorate. Sarà inoltre possibile visualizzare il numero di attività che si verificano nel corso del tempo.
| Exchange | Mostra i dettagli delle attività di Exchange Server, ad esempio Add-Mailbox Permission o Set-Mailbox. |
| SharePoint | Mostra le prime attività che gli utenti eseguono nei documenti di SharePoint. Quando si visualizzano i dettagli da questo riquadro, nella pagina di ricerca vengono visualizzati i dettagli di queste attività, ad esempio il documento di destinazione e il percorso di questa attività. Ad esempio, per un evento di accesso ai file, sarà possibile visualizzare il documento a cui si accede, il nome dell'account associato e l'indirizzo IP. |
| Azure Active Directory | Include le attività degli utenti superiori, ad esempio i tentativi di accesso e di reimpostazione password utente. È possibile visualizzare i dettagli di queste attività, ad esempio lo stato dei risultati. Questa funzionalità è particolarmente utile se si desidera monitorare le attività sospette in Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Record di log di Monitoraggio di Azure

Tutti i record creati nell'area di lavoro Log Analytics in Monitoraggio di Azure dalla soluzione Office 365 dispongono di un tipo (**Type**) di **OfficeActivity**.  La proprietà **OfficeWorkload** determina a quale servizio di Office 365 fa riferimento il record: Exchange, AzureActiveDirectory, SharePoint o OneDrive.  La proprietà **RecordType** specifica il tipo di operazione.  Le proprietà variano per ogni tipo di operazione e vengono visualizzate nelle tabelle seguenti.

### <a name="common-properties"></a>Proprietà comuni

Le proprietà seguenti sono comuni a tutti i record di Office 365.

| Proprietà | Descrizione |
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
| UserType | Il tipo di utente che ha eseguito l'operazione.<br><br>Amministratore<br>Applicazione<br>DcAdmin<br>Normale<br>Riservato<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base di Azure Active Directory

Le proprietà seguenti sono comuni a tutti i record di Azure Active Directory.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Il tipo di evento di Azure AD. |
| ExtendedProperties | Le proprietà estese dell'evento di Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Accesso all'account di Azure Active Directory

Questi record vengono creati quando un utente di Active Directory tenta di accedere.

| Proprietà | Descrizione |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | L'applicazione che attiva l'evento di accesso all'account, ad esempio Office 15. |
| `Client` | Dettagli relativi al dispositivo del client, al sistema operativo del dispositivo e al browser del dispositivo usato per l'evento di accesso all'account. |
| `LoginStatus` | Questa proprietà deriva direttamente da OrgIdLogon.LoginStatus. Il mapping di diversi errori di accesso interessanti potrebbe essere eseguito da algoritmi di avviso. |
| `UserDomain` | Le informazioni sull'identità del tenant (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Questi record vengono creati quando vengono apportate modifiche o aggiunte agli oggetti di Azure Active Directory.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | L'utente su cui è stata eseguita l'azione (identificato dalla proprietà Operation). |
| Attore | Utente o entità servizio che ha eseguito l'azione. |
| ActorContextId | Il GUID dell'organizzazione a cui appartiene l'attore. |
| ActorIpAddress | Indirizzo IP dell'attore in formato di indirizzo IPv4 o IPv6. |
| InterSystemsId | GUID che rileva le azioni nei vari componenti all'interno del servizio di Office 365. |
| IntraSystemId |     Il GUID generato da Azure Active Directory per tenere traccia dell'azione. |
| SupportTicketId | L'ID del ticket di supporto cliente per l'azione in situazioni in cui si agisce per conto di qualcuno. |
| TargetContextId | Il GUID dell'organizzazione a cui appartiene l'utente di destinazione. |


### <a name="data-center-security"></a>Sicurezza del centro dati

Questi record vengono creati dai dati di controllo della sicurezza del centro dati.  

| Proprietà | Descrizione |
|:--- |:--- |
| EffectiveOrganization | Il nome del tenant a cui l'elevazione/cmdlet sono destinati. |
| ElevationApprovedTime | Il timestamp di quando è stata approvata l'elevazione. |
| ElevationApprover | Il nome di una gestione di Microsoft. |
| ElevationDuration | La durata per cui è stata attiva l'elevazione. |
| ElevationRequestId |     Identificatore univoco per la richiesta di elevazione. |
| ElevationRole | Il ruolo per cui è stata richiesta l'elevazione. |
| ElevationTime | L'ora di inizio dell'elevazione. |
| Start_Time | L'ora di inizio dell'esecuzione del cmdlet. |


### <a name="exchange-admin"></a>Amministratore di Exchange

Questi record vengono creati quando vengono apportate modifiche alla configurazione di Exchange.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Specifica se il cmdlet è stato eseguito da un utente nell'organizzazione, dal personale del centro dati di Microsoft, da un account di servizio del centro dati o da un amministratore delegato. Il valore False indica che il cmdlet è stato eseguito da un utente nell'organizzazione. Il valore True indica che il cmdlet è stato eseguito dal personale del centro dati, un account del servizio del centro dati o un amministratore delegato. |
| ModifiedObjectResolvedName |     Questo è il nome descrittivo dell'oggetto modificato dal cmdlet. Viene registrato solo se il cmdlet modifica l'oggetto. |
| OrganizationName | Nome del tenant. |
| OriginatingServer | Il nome del server da cui è stato eseguito il cmdlet. |
| Parametri | Nome e valore per tutti i parametri usati con il cmdlet identificato nella proprietà Operations. |


### <a name="exchange-mailbox"></a>Cassetta postale di Exchange

Questi record vengono creati quando vengono apportate modifiche o aggiunte alle cassette postali di Exchange.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informazioni sul client di posta elettronica usato per eseguire l'operazione, ad esempio una versione del browser, la versione di Outlook e le informazioni sul dispositivo mobile. |
| Client_IPAddress | L'indirizzo IP del dispositivo usato quando l'operazione è stata registrata. L'indirizzo IP viene visualizzato in formato di indirizzo IPv4 o IPv6. |
| ClientMachineName | Nome del computer che ospita il client di Outlook. |
| ClientProcessName | Il client di posta elettronica usato per accedere alla cassetta postale. |
| ClientVersion | Versione del client di posta elettronica. |
| InternalLogonType | Riservato per utilizzo interno. |
| Logon_Type | Indica il tipo di utente che ha eseguito l'accesso alla cassetta postale e ha compiuto l'operazione che è stata registrata. |
| LogonUserDisplayName |     Il nome descrittivo dell'utente che ha eseguito l'operazione. |
| LogonUserSid | L'ID di sicurezza dell'utente che ha eseguito l'operazione. |
| MailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso. |
| MailboxOwnerMasterAccountSid | ID di sicurezza dell'account proprietario della cassetta postale. |
| MailboxOwnerSid | L'ID di sicurezza del proprietario della cassetta postale. |
| MailboxOwnerUPN | L'indirizzo e-mail della persona a cui appartiene la cassetta postale che ha effettuato l'accesso. |


### <a name="exchange-mailbox-audit"></a>Controllo della cassetta postale di Exchange

Questi record vengono creati quando viene creata una voce di controllo delle cassette postali.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | Rappresenta l'elemento su cui è stata eseguita l'operazione | 
| SendAsUserMailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso per inviare e-mail. |
| SendAsUserSmtp | Indirizzo SMTP dell'utente che viene rappresentato. |
| SendonBehalfOfUserMailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso per inviare e-mail per conto di. |
| SendOnBehalfOfUserSmtp | Indirizzo SMTP dell'utente per conto del quale viene inviata l'e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Gruppo di controllo della cassetta postale di Exchange

Questi record vengono creati quando vengono apportate modifiche o aggiunte ai gruppi di Exchange.

| Proprietà | Descrizione |
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

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica un evento che si è verificato in SharePoint. I valori possibili sono ObjectModel o SharePoint. |
| ItemType | Il tipo dell'oggetto a cui è stato effettuato l'accesso o che è stato modificato. Vedere la tabella ItemType per informazioni dettagliate sui tipi di oggetti. |
| MachineDomainInfo | Informazioni sulle operazioni di sincronizzazione dei dispositivi. Questa informazione viene segnalata solo se è presente nella richiesta. |
| MachineId |     Informazioni sulle operazioni di sincronizzazione dei dispositivi. Questa informazione viene segnalata solo se è presente nella richiesta. |
| Site_ | Il GUID del sito in cui si trova il file o la cartella a cui l'utente ha effettuato l'accesso. |
| Source_Name | L'entità che ha attivato le operazioni di controllo. I valori possibili sono ObjectModel o SharePoint. |
| UserAgent | Informazioni sul client o browser dell'utente. Queste informazioni vengono fornite dal client o dal browser. |


### <a name="sharepoint-schema"></a>Schema di SharePoint

Questi record vengono creati quando vengono apportate modifiche alla configurazione di SharePoint.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Stringa facoltativa per gli eventi personalizzati. |
| Event_Data |     Payload facoltativo per gli eventi personalizzati. |
| ModifiedProperties | La proprietà è inclusa per gli eventi amministrativi, ad esempio l'aggiunta di un utente come membro di un sito o un gruppo di amministrazione raccolta siti. La proprietà include il nome della proprietà che è stata modificata (ad esempio, il gruppo amministratore del sito), il nuovo valore della proprietà modificata (come l'utente che è stato aggiunto come amministratore del sito) e il valore precedente dell'oggetto modificato. |


### <a name="sharepoint-file-operations"></a>Operazioni sui file di SharePoint

Questi record vengono creati in risposta alle operazioni sui file in SharePoint.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Estensione di un file che viene copiato o spostato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| DestinationFileName | Il nome del file che viene copiato o spostato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| DestinationRelativeUrl | L'URL della cartella di destinazione in cui un file viene copiato o spostato. La combinazione dei valori dei parametri SiteURL, DestinationRelativeURL e DestinationFileName è lo stesso del valore della proprietà ObjectID, ovvero il nome percorso completo del file che è stato copiato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| SharingType | Il tipo di autorizzazioni di condivisione che sono state assegnate all'utente con cui è stata condivisa la risorsa. Questo utente viene identificato dal parametro UserSharedWith. |
| Site_Url | L'URL del sito in cui si trova il file o la cartella a cui l'utente ha effettuato l'accesso. |
| SourceFileExtension | L'estensione del file a cui l'utente ha effettuato l'accesso. Questa proprietà è vuota se l'oggetto a cui è stato effettuato l'accesso è una cartella. |
| SourceFileName |     Il nome del file o della cartella a cui l'utente ha effettuato l'accesso. |
| SourceRelativeUrl | L'URL della cartella che contiene il file a cui l'utente ha effettuato l'accesso. La combinazione dei valori dei parametri SiteURL, SourceRelativeURL e SourceFileName è lo stesso del valore della proprietà ObjectID, ovvero il nome percorso completo del file a cui l'utente ha effettuato l'accesso. |
| UserSharedWith |     L'utente con cui è stata condivisa una risorsa. |




## <a name="sample-log-queries"></a>Query di log di esempio

La tabella seguente include le query di log di esempio per i record di aggiornamento raccolti da questa soluzione.

| Query | Descrizione |
| --- | --- |
|Conteggio di tutte le operazioni per la sottoscrizione di Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Uso di siti di SharePoint|OfficeActivity &#124; where OfficeWorkload = ~ "SharePoint" &#124; riepilogare Count () by SiteUrl \| Sort by count ASC|
|Operazioni di accesso ai file per tipo di utente | OfficeActivity &#124; riepilogare Count () by UserType |
|Azioni esterne di monitoraggio di Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Passaggi successivi

* Usare le [query di log in Monitoraggio di Azure](../log-query/log-query-overview.md) per visualizzare dati di aggiornamento dettagliati.
* [Creare dashboard personalizzati](../learn/tutorial-logs-dashboards.md) per visualizzare le query di ricerca di Office 365 preferite.
* [Creare avvisi](../platform/alerts-overview.md) per essere notificati in modo proattivo delle attività importanti di Office 365.  
