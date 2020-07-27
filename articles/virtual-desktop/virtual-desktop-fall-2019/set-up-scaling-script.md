---
title: Dimensionare gli host di sessioni con Automazione di Azure - Azure
description: Come dimensionare automaticamente gli host di sessioni di Desktop virtuale Windows con Automazione di Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 690f59c643f1fe8c8cfc74758a0f8f13b129f78a
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172066"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Dimensionare gli host di sessioni con Automazione di Azure

>[!IMPORTANT]
>Questo contenuto si applica alla versione Autunno 2019 che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows.

È possibile ridurre il costo totale di distribuzione di Desktop virtuale Windows dimensionando le macchine virtuali (VM). Ciò significa arrestare e deallocare le VM host di sessioni nelle ore non di punta, quindi riattivarle e riallocarle nelle ore di punta.

Questo articolo illustra lo strumento di ridimensionamento creato con l'account di automazione di Azure e l'app per la logica di Azure che ridimensiona automaticamente le VM host della sessione nell'ambiente di desktop virtuale Windows. Per informazioni su come usare lo strumento di dimensionamento, vedere [Prerequisiti](#prerequisites).

## <a name="report-issues"></a>Segnalare i problemi

Le segnalazioni di problemi relativi allo strumento di dimensionamento vengono attualmente gestite in GitHub invece che dal supporto tecnico Microsoft. Se si verificano problemi con lo strumento di scalabilità, ottenere le informazioni necessarie come descritto nella sezione [problemi relativi alla creazione di report](#reporting-issues) e aprire un problema di GitHub denominato "4a-Wvd-scaling-logica" nella [pagina di GitHub su RDS](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).

## <a name="how-the-scaling-tool-works"></a>Funzionamento dello strumento di dimensionamento

Lo strumento di dimensionamento offre un'opzione di automazione a basso costo per i clienti che vogliono ottimizzare i costi delle VM host di sessioni.

È possibile usare lo strumento per:

- Pianificare l'avvio e l'arresto delle VM in base agli orari di ufficio di punta e non di punta.
- Aumentare le VM in base al numero di sessioni per core di CPU.
- Ridurre le VM nelle ore non di punta, lasciando in esecuzione il numero minimo di VM host di sessioni.

Lo strumento di scalabilità usa una combinazione di un account di automazione di Azure, un Runbook di PowerShell, un webhook e l'app per la logica di Azure per funzionare. Quando viene eseguito lo strumento, app per la logica di Azure chiama un webhook per avviare il Runbook di automazione di Azure. Il runbook crea quindi un processo.

Durante le ore di punta, il processo verifica il numero corrente di sessioni e la capacità di VM dell'host di sessioni attualmente in esecuzione per ogni pool di host. Usa queste informazioni per calcolare se le VM host della sessione in esecuzione possono supportare le sessioni esistenti in base al parametro *SessionThresholdPerCPU* definito per il file di **CreateOrUpdateAzLogicApp.ps1** . Se le VM host di sessioni non possono supportare le sessioni esistenti, il processo ne avvia altre nel pool di host.

>[!NOTE]
>Il parametro *SessionThresholdPerCPU* non limita il numero di sessioni nella VM. Determina solo quando è necessario avviare nuove VM per bilanciare il carico delle connessioni. Per limitare il numero di sessioni, è necessario seguire le istruzioni relative a [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) per configurare di conseguenza il parametro *MaxSessionLimit*.

Durante il tempo di utilizzo inferiore, il processo determina il numero di VM host di sessione che devono essere arrestate in base al parametro *MinimumNumberOfRDSH* . Se si imposta il parametro *LimitSecondsToForceLogOffUser* su un valore positivo diverso da zero, il processo imposterà le VM host della sessione sulla modalità di svuotamento per impedire che le nuove sessioni si connettano agli host. Il processo invierà quindi notifiche a tutti gli utenti attualmente connessi per salvare il lavoro, attendere la quantità di tempo configurata e quindi forzare la disconnessione degli utenti. Dopo la disconnessione di tutte le sessioni utente nella macchina virtuale host sessione, il processo arresterà la macchina virtuale. Quando la macchina virtuale viene arrestata, il processo Reimposta la modalità di svuotamento host della sessione.

>[!NOTE]
>Se si imposta manualmente la macchina virtuale host sessione sulla modalità di svuotamento, il processo non gestirà la macchina virtuale host sessione. Se la macchina virtuale host sessione è in esecuzione e impostata sulla modalità svuotamento, verrà considerata come non disponibile, in modo che il processo avvii altre macchine virtuali per gestire il carico. È consigliabile contrassegnare le macchine virtuali di Azure prima di impostarle manualmente sulla modalità di svuotamento. È possibile assegnare un nome al tag con il parametro *MaintenanceTagName* quando si crea l'utilità di pianificazione dell'app per la logica di Azure in seguito. I tag consentiranno di distinguere queste macchine virtuali da quelle gestite dallo strumento di scalabilità. L'impostazione del tag di manutenzione impedisce anche allo strumento di ridimensionamento di apportare modifiche alla macchina virtuale fino a quando non si rimuove il tag.

Se si imposta il parametro *LimitSecondsToForceLogOffUser* su zero, il processo consente l'impostazione di configurazione della sessione nei criteri di gruppo specificati per gestire la firma delle sessioni utente. Per visualizzare questi criteri di gruppo, passare a **Configurazione computer**  >  **criteri**  >  **modelli amministrativi**  >  **componenti di Windows**  >  **Servizi Desktop remoto**  >  **host sessione Desktop remoto**  >  **limiti di tempo della sessione**. Se sono presenti sessioni attive in una VM host di sessioni, il processo lascerà tale VM in esecuzione. Se non sono presenti sessioni attive, il processo arresterà la macchina virtuale host sessione.

In qualsiasi momento, il processo accetta anche *MaxSessionLimit* del pool host per determinare se il numero corrente di sessioni è superiore al 90% della capacità massima. In caso contrario, il processo avvierà altre VM host sessione.

Il processo viene eseguito periodicamente in base a un intervallo di ricorrenza impostato. È possibile modificare questo intervallo in base alle dimensioni dell'ambiente di desktop virtuale di Windows, ma tenere presente che l'avvio e l'arresto delle macchine virtuali possono richiedere del tempo, quindi ricordare di tenere conto del ritardo. È consigliabile impostare l'intervallo di ricorrenza su ogni 15 minuti.

Tuttavia, lo strumento presenta anche le limitazioni seguenti:

- Questa soluzione si applica solo alle VM host sessione multisessione in pool.
- Questa soluzione gestisce le macchine virtuali in qualsiasi area, ma può essere usata solo nella stessa sottoscrizione dell'account di automazione di Azure e dell'app per la logica di Azure.
- Il runtime massimo di un processo in Runbook è di 3 ore. Se l'avvio o l'arresto delle macchine virtuali nel pool host richiede più tempo, il processo avrà esito negativo. Per informazioni dettagliate, vedere [risorse condivise](../../automation/automation-runbook-execution.md#fair-share).

>[!NOTE]
>Lo strumento di scalabilità controlla la modalità di bilanciamento del carico del pool host attualmente in fase di ridimensionamento. Lo strumento usa la modalità di bilanciamento del carico con prima larghezza per le ore di picco e di minore utilizzo.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la configurazione dello strumento di dimensionamento, assicurarsi che siano disponibili gli elementi seguenti:

- Un [pool di host e tenant di Desktop virtuale Windows](create-host-pools-arm-template.md)
- VM del pool di host di sessioni configurate e registrate con il servizio Desktop virtuale Windows
- Un utente con [accesso di Collaboratore](../../role-based-access-control/role-assignments-portal.md) nella sottoscrizione di Azure

Il computer da usare per distribuire lo strumento deve avere:

- Windows PowerShell 5.1 o versione successiva
- Il modulo Microsoft Az di PowerShell

Se è tutto pronto, è possibile iniziare.

## <a name="create-or-update-an-azure-automation-account"></a>Crea o aggiorna un account di automazione di Azure

>[!NOTE]
>Se si ha già un account di automazione di Azure con un Runbook che esegue una versione precedente dello script di ridimensionamento, è sufficiente seguire le istruzioni riportate di seguito per assicurarsi che sia aggiornato.

Per prima cosa, è necessario un account di Automazione di Azure per eseguire il runbook di PowerShell. Il processo descritto in questa sezione è valido anche se si dispone di un account di automazione di Azure esistente che si vuole usare per configurare il Runbook di PowerShell. Di seguito viene illustrato come configurarlo:

1. Aprire Windows PowerShell.

2. Eseguire il cmdlet seguente per accedere al proprio account Azure.

    ```powershell
    Login-AzAccount
    ```
    
    >[!NOTE]
    >L'account deve disporre dei diritti di collaboratore per la sottoscrizione di Azure in cui si vuole distribuire lo strumento di scalabilità.

3. Eseguire il cmdlet seguente per scaricare lo script per la creazione dell'account di Automazione di Azure:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Eseguire il cmdlet seguente per eseguire lo script e creare l'account di automazione di Azure. È possibile specificare i valori per i parametri o impostarli come commenti per utilizzarne le impostazioni predefinite.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }
    
    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. L'output del cmdlet includerà l'URI di un webhook. Assicurarsi di tenere un record dell'URI perché verrà usato come parametro quando si configura la pianificazione di esecuzione per l'app per la logica di Azure.

6. Se è stato specificato il parametro **WorkspaceName** per log Analytics, l'output del cmdlet includerà anche l'ID dell'area di lavoro log Analytics e la relativa chiave primaria. Assicurarsi di ricordare l'URI perché sarà necessario usarlo di nuovo in seguito come parametro quando si configura la pianificazione di esecuzione per l'app per la logica di Azure.

7. Dopo aver configurato l'account di Automazione di Azure, accedere alla sottoscrizione di Azure e verificare che l'account di Automazione di Azure e il runbook pertinente vengano visualizzati nel gruppo di risorse specificato, come illustrato nell'immagine seguente:

    >[!div class="mx-imgBorder"]
    >![Immagine della pagina Panoramica di Azure che mostra l'account di automazione di Azure appena creato e Runbook.](media/automation-account.png)

    Per verificare se il webhook si trova dove previsto, selezionare il nome del runbook. Passare quindi alla sezione Risorse del runbook e selezionare **Webhook**.

## <a name="create-an-azure-automation-run-as-account"></a>Creare un account RunAs di Automazione di Azure

Ora che si dispone di un account di automazione di Azure, è necessario creare anche un account RunAs di automazione di Azure, se non è già disponibile. Questo account consente allo strumento di accedere alle risorse di Azure.

Un [account RunAs di automazione di Azure](../../automation/manage-runas-account.md) fornisce l'autenticazione per la gestione delle risorse in Azure con i cmdlet di Azure. Quando si crea un account RunAs, viene creato un nuovo utente entità servizio in Azure Active Directory e il ruolo Collaboratore viene assegnato all'utente dell'entità servizio a livello di sottoscrizione. Un account RunAs di Azure è un ottimo modo per eseguire l'autenticazione in modo sicuro con certificati e un nome dell'entità servizio senza dover archiviare un nome utente e una password in un oggetto credenziale. Per ulteriori informazioni sull'autenticazione dell'account RunAs, vedere [limitare le autorizzazioni dell'account RunAs](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Qualsiasi utente che sia membro del ruolo amministratori della sottoscrizione e coamministratore della sottoscrizione può creare un account RunAs.

Per creare un account RunAs nell'account di automazione di Azure:

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse immettere e selezionare account di **automazione**.

2. Nella pagina **account di automazione** selezionare il nome dell'account di automazione di Azure.

3. Nel riquadro sul lato sinistro della finestra selezionare **account RunAs** nella sezione **Impostazioni account** .

4. Selezionare **account RunAs di Azure**. Quando viene visualizzato il riquadro **Aggiungi account RunAs di Azure** , esaminare le informazioni generali e quindi selezionare **Crea** per avviare il processo di creazione dell'account.

5. Attendere alcuni minuti mentre viene creato l'account RunAs. È possibile tenere traccia dello stato di avanzamento della creazione nel menu Notifiche.

6. Al termine del processo, verrà creato un asset denominato **AzureRunAsConnection** nell'account di automazione di Azure specificato. Selezionare **account RunAs di Azure**. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato. Prendere nota dell'ID applicazione, perché verrà usato più avanti. È anche possibile trovare le stesse informazioni nella pagina **connessioni** . Per passare a questa pagina, nel riquadro sul lato sinistro della finestra selezionare **connessioni** nella sezione **risorse condivise** e fare clic sull'asset di connessione denominato **AzureRunAsConnection**.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Creare un'assegnazione di ruolo in Desktop virtuale Windows

Successivamente, è necessario creare un'assegnazione di ruolo in modo che **AzureRunAsConnection** possa interagire con il desktop virtuale di Windows. Assicurarsi di usare PowerShell per accedere con un account che abbia le autorizzazioni per creare assegnazioni di ruolo.

Prima di tutto, scaricare e importare il [modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Eseguire i cmdlet di PowerShell seguenti per connettersi a Desktop virtuale Windows e visualizzare i tenant.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

# If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
# Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"

Get-RdsTenant
```

Quando si trova il tenant con i pool host di cui si vuole eseguire la scalabilità, seguire le istruzioni in [creare un account RunAs di automazione di Azure](#create-an-azure-automation-run-as-account) per raccogliere l'ID applicazione **AzureRunAsConnection** e usare il nome del tenant di desktop virtuale di Windows ottenuto dal cmdlet precedente nel cmdlet seguente per creare l'assegnazione di ruolo:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId "<applicationid>" -TenantName "<tenantname>"
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Creare l'app per la logica di Azure e la pianificazione dell'esecuzione

Infine, è necessario creare l'app per la logica di Azure e configurare una pianificazione dell'esecuzione per il nuovo strumento di dimensionamento.

1. Aprire Windows PowerShell.

2. Eseguire il cmdlet seguente per accedere al proprio account Azure.

    ```powershell
    Login-AzAccount
    ```

3. Eseguire il cmdlet seguente per scaricare lo script per la creazione dell'app per la logica di Azure.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. Eseguire il cmdlet seguente per accedere a Desktop virtuale Windows con un account che abbia le autorizzazioni di Proprietario o Collaboratore di Servizi Desktop remoto.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

    # If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
    # Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"
    ```

5. Eseguire lo script di PowerShell seguente per creare l'app per la logica di Azure e la pianificazione di esecuzione per il pool host 

    >[!NOTE]
    >È necessario eseguire questo script per ogni pool host di cui si vuole applicare la scalabilità automatica, ma è necessario un solo account di automazione di Azure.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id
    
    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id
    
    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"
    
    $RDBrokerURL = (Get-RdsContext).DeploymentUrl
    $WVDTenant = Get-RdsTenant | Out-GridView -OutputMode:Single -Title "Select your WVD tenant"
    $WVDHostPool = Get-RdsHostPool -TenantName $WVDTenant.TenantName | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"
    
    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"
    
    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"
    
    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURI' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName
    
    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "RDBrokerURL"                   = $RDBrokerURL                             # Optional. Default: "https://rdbroker.wvd.microsoft.com"
         "TenantGroupName"               = $WVDTenant.TenantGroupName               # Optional. Default: "Default Tenant Group"
         "TenantName"                    = $WVDTenant.TenantName
         "HostPoolName"                  = $WVDHostPool.HostPoolName
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }
    
    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    Dopo aver eseguito lo script, l'app per la logica di Azure dovrebbe essere visualizzata in un gruppo di risorse, come illustrato nella figura seguente.

    >[!div class="mx-imgBorder"]
    >![Immagine della pagina di panoramica con un esempio di app per la logica di Azure.](../media/logic-app.png)

    Per apportare modifiche alla pianificazione dell'esecuzione, ad esempio modificare l'intervallo di ricorrenza o il fuso orario, passare all'utilità di pianificazione della scalabilità automatica dell'app per la logica di Azure e selezionare **modifica** per passare alla finestra di progettazione di app per la logica di Azure.

    >[!div class="mx-imgBorder"]
    >![Immagine della finestra di progettazione di app per la logica di Azure. I menu di ricorrenza e webhook che consentono all'utente di modificare i tempi di ricorrenza e il file webhook sono aperti.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gestire lo strumento di dimensionamento

Ora che è stato creato lo strumento di dimensionamento, è possibile accedere al relativo output. Questa sezione descrive alcune funzionalità che possono risultare utili.

### <a name="view-job-status"></a>Visualizzare lo stato di un processo

Nel portale di Azure è possibile visualizzare uno stato riepilogativo di tutti i processi del runbook o uno stato più dettagliato di uno specifico processo.

A destra dell'account di automazione di Azure selezionato, in "statistiche processo", è possibile visualizzare un elenco di riepiloghi di tutti i processi di Runbook. Aprendo la pagina **Processi** sul lato sinistro della finestra vengono visualizzati gli stati dei processi correnti, l'ora di inizio e l'ora di completamento.

>[!div class="mx-imgBorder"]
>![Screenshot della pagina di stati dei processi.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Visualizzare i log e l'output dello strumento di dimensionamento

È possibile visualizzare i log delle operazioni di scalabilità orizzontale e di scalabilità orizzontale aprendo il Runbook e selezionando il processo.

Passare a Runbook nel gruppo di risorse che ospita l'account di automazione di Azure e selezionare **Overview (panoramica**). Nella pagina Panoramica selezionare un processo in **processi recenti** per visualizzare l'output dello strumento di ridimensionamento, come illustrato nella figura seguente.

>[!div class="mx-imgBorder"]
>![Immagine della finestra di output dello strumento di dimensionamento.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Verificare il numero di versione dello script Runbook

È possibile controllare la versione dello script Runbook in uso aprendo il file Runbook nell'account di automazione di Azure e selezionando **View (Visualizza**). Sul lato destro dello schermo verrà visualizzato uno script per Runbook. Nello script verrà visualizzato il numero di versione nel formato `v#.#.#` sotto la `SYNOPSIS` sezione. [Qui](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/basicScale.ps1#L1)è possibile trovare il numero di versione più recente. Se non viene visualizzato un numero di versione nello script Runbook, significa che si sta eseguendo una versione precedente dello script ed è necessario aggiornarlo immediatamente. Se è necessario aggiornare lo script Runbook, seguire le istruzioni riportate in [creare o aggiornare un account di automazione di Azure](#create-or-update-an-azure-automation-account).

### <a name="reporting-issues"></a>Segnalazione di problemi

Quando si segnala un problema, è necessario fornire le informazioni seguenti per facilitare la risoluzione dei problemi:

- Log completo dalla scheda **tutti i log** del processo che ha causato il problema. Per informazioni su come ottenere il log, seguire le istruzioni in [visualizzare i log e l'output dello strumento di ridimensionamento](#view-logs-and-scaling-tool-output). Se nel log sono presenti informazioni riservate o riservate, è possibile rimuoverle prima di inviare il problema a Microsoft.

- Versione dello script Runbook che si sta usando. Per informazioni su come ottenere il numero di versione, vedere [controllare il numero di versione dello script Runbook](#check-the-runbook-script-version-number)

- Il numero di versione di ognuno dei moduli di PowerShell seguenti installati nell'account di automazione di Azure. Per trovare questi moduli, aprire l'account di automazione di Azure, selezionare **moduli** nella sezione **risorse condivise** nel riquadro sul lato sinistro della finestra, quindi cercare il nome del modulo.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Microsoft. RDInfra. RDPowershell

- Data di scadenza dell' [account RunAs](#create-an-azure-automation-run-as-account). Per trovarlo, aprire l'account di automazione di Azure e quindi selezionare **account RunAs** in **Impostazioni account** nel riquadro sul lato sinistro della finestra. La data di scadenza deve essere nell' **account RunAs di Azure**.

### <a name="log-analytics"></a>Log Analytics

Se si è deciso di usare Log Analytics, è possibile visualizzare tutti i dati di log in un log personalizzato denominato **WVDTenantScale_CL** in **log personalizzati** nella visualizzazione **log** dell'area di lavoro log Analytics. Sono state elencate alcune query di esempio che potrebbero risultare utili.

- Per visualizzare tutti i log per un pool host, immettere la query seguente

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Per visualizzare il numero totale di macchine virtuali host sessione attualmente in esecuzione e sessioni utente attive nel pool host, immettere la query seguente

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Per visualizzare lo stato di tutte le macchine virtuali host sessione in un pool host, immettere la query seguente.

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Per visualizzare eventuali errori e avvisi, immettere la query seguente.

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```
