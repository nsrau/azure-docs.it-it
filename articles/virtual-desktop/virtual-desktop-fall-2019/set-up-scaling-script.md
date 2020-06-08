---
title: Dimensionare gli host di sessioni con Automazione di Azure - Azure
description: Come dimensionare automaticamente gli host di sessioni di Desktop virtuale Windows con Automazione di Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f659a40cbb9e3ef2d0e7fe4e527518a76507d5ee
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745703"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Dimensionare gli host di sessioni con Automazione di Azure

>[!IMPORTANT]
>Questo contenuto si applica alla versione Autunno 2019 che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows.

È possibile ridurre il costo totale di distribuzione di Desktop virtuale Windows dimensionando le macchine virtuali (VM). Ciò significa arrestare e deallocare le VM host di sessioni nelle ore non di punta, quindi riattivarle e riallocarle nelle ore di punta.

Questo articolo illustra lo strumento integrato in Automazione di Azure e App per la logica di Azure che dimensiona automaticamente le macchine virtuali host di sessioni nell'ambiente di Desktop virtuale Windows. Per informazioni su come usare lo strumento di dimensionamento, vedere [Prerequisiti](#prerequisites).

## <a name="report-issues"></a>Segnalare i problemi

Le segnalazioni di problemi relativi allo strumento di dimensionamento vengono attualmente gestite in GitHub invece che dal supporto tecnico Microsoft. Se si verificano problemi con lo strumento di dimensionamento, è possibile segnalarli con l'apertura di un problema di GitHub con tag "4a-WVD-scaling-logicapps" nella [pagina di GitHub per Servizi Desktop remoto](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).

## <a name="how-the-scaling-tool-works"></a>Funzionamento dello strumento di dimensionamento

Lo strumento di dimensionamento offre un'opzione di automazione a basso costo per i clienti che vogliono ottimizzare i costi delle VM host di sessioni.

È possibile usare lo strumento per:
 
- Pianificare l'avvio e l'arresto delle VM in base agli orari di ufficio di punta e non di punta.
- Aumentare le VM in base al numero di sessioni per core di CPU.
- Ridurre le VM nelle ore non di punta, lasciando in esecuzione il numero minimo di VM host di sessioni.

Per il funzionamento dello strumento di dimensionamento, viene usata una combinazione di runbook di PowerShell di Automazione di Azure, webhook e App per la logica di Azure. Quando viene eseguito lo strumento, App per la logica di Azure chiama un webhook per avviare il runbook di Automazione di Azure. Il runbook crea quindi un processo.

Durante le ore di punta, il processo verifica il numero corrente di sessioni e la capacità di VM dell'host di sessioni attualmente in esecuzione per ogni pool di host. Queste informazioni vengono usate per calcolare se le VM host di sessioni in esecuzione possono supportare le sessioni esistenti in base al parametro *SessionThresholdPerCPU* definito per il file **createazurelogicapp.ps1**. Se le VM host di sessioni non possono supportare le sessioni esistenti, il processo ne avvia altre nel pool di host.

>[!NOTE]
>Il parametro *SessionThresholdPerCPU* non limita il numero di sessioni nella VM. Determina solo quando è necessario avviare nuove VM per bilanciare il carico delle connessioni. Per limitare il numero di sessioni, è necessario seguire le istruzioni relative a [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) per configurare di conseguenza il parametro *MaxSessionLimit*.

Durante le ore non di punta, il processo determina quali VM host di sessioni arrestare in base al parametro *MinimumNumberOfRDSH*. Il processo imposterà le VM host di sessioni sulla modalità di svuotamento per impedire la connessione di nuove sessioni all'host. Se si imposta il parametro *LimitSecondsToForceLogOffUser* su un valore positivo diverso da zero, il processo invierà una notifica a tutti gli utenti attualmente connessi indicando di salvare le modifiche, aspetterà per la durata dell'intervallo configurato e quindi forzerà la disconnessione degli utenti. Dopo la disconnessione di tutte le sessioni utente nella VM host di sessioni, il processo arresterà la VM.

Se si imposta il parametro *LimitSecondsToForceLogOffUser* su zero, il processo consentirà la gestione della disconnessione delle sessioni utente in base all'impostazione di configurazione delle sessioni nei Criteri di gruppo specificati. Per visualizzare questi Criteri di gruppo, passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Servizi terminal** > **Terminal Server** > **Limiti di tempo sessione**. Se sono presenti sessioni attive in una VM host di sessioni, il processo lascerà tale VM in esecuzione. Se non sono presenti sessioni attive, il processo arresterà la VM host di sessioni.

Il processo viene eseguito periodicamente in base a un intervallo di ricorrenza impostato. È possibile cambiare questo intervallo in base alle dimensioni dell'ambiente di Desktop virtuale Windows, ma si noti che l'avvio e l'arresto delle macchine virtuali possono richiedere del tempo, quindi assicurarsi di tenere conto del ritardo. È consigliabile impostare l'intervallo di ricorrenza su ogni 15 minuti.

Tuttavia, lo strumento presenta anche le limitazioni seguenti:

- Questa soluzione si applica solo alle VM host di sessioni in pool.
- Questa soluzione gestisce le VM in qualsiasi area, ma può essere usata solo nella stessa sottoscrizione dell'account di Automazione di Azure e di App per la logica di Azure.

>[!NOTE]
>Lo strumento di dimensionamento controlla la modalità di bilanciamento del carico del pool di host da dimensionare. Imposta il bilanciamento in ampiezza per le ore di punta e non di punta.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la configurazione dello strumento di dimensionamento, assicurarsi che siano disponibili gli elementi seguenti:

- Un [pool di host e tenant di Desktop virtuale Windows](create-host-pools-arm-template.md)
- VM del pool di host di sessioni configurate e registrate con il servizio Desktop virtuale Windows
- Un utente con [accesso di Collaboratore](../../role-based-access-control/role-assignments-portal.md) nella sottoscrizione di Azure

Il computer da usare per distribuire lo strumento deve avere: 

- Windows PowerShell 5.1 o versione successiva
- Il modulo Microsoft Az di PowerShell

Se è tutto pronto, è possibile iniziare.

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure

Per prima cosa, è necessario un account di Automazione di Azure per eseguire il runbook di PowerShell. Ecco come configurare l'account:

1. Aprire Windows PowerShell come amministratore.
2. Eseguire il cmdlet seguente per accedere all'account Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >L'account deve avere diritti di collaboratore nella sottoscrizione di Azure in cui distribuire lo strumento di dimensionamento.

3. Eseguire il cmdlet seguente per scaricare lo script per la creazione dell'account di Automazione di Azure:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Eseguire il cmdlet seguente per eseguire lo script e creare l'account di Automazione di Azure:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. L'output del cmdlet includerà l'URI di un webhook. Assicurarsi di prendere nota dell'URI, perché verrà usato come parametro per configurare la pianificazione dell'esecuzione per App per la logica di Azure.

6. Dopo aver configurato l'account di Automazione di Azure, accedere alla sottoscrizione di Azure e verificare che l'account di Automazione di Azure e il runbook pertinente vengano visualizzati nel gruppo di risorse specificato, come illustrato nell'immagine seguente:

![Immagine della pagina di panoramica di Azure che mostra l'account di automazione e il runbook appena creati.](../media/automation-account.png)

  Per verificare se il webhook si trova dove previsto, selezionare il nome del runbook. Passare quindi alla sezione Risorse del runbook e selezionare **Webhook**.

## <a name="create-an-azure-automation-run-as-account"></a>Creare un account RunAs di Automazione di Azure

Dopo aver creato un account di Automazione di Azure, è necessario creare anche un account RunAs di Automazione di Azure per accedere alle risorse di Azure.

Gli [account RunAs di Automazione di Azure](../../automation/manage-runas-account.md) offrono l'autenticazione per la gestione delle risorse in Azure con i cmdlet di Azure. Quando si crea un account RunAs, viene creato un nuovo utente dell'entità servizio in Azure Active Directory a cui viene assegnato il ruolo Collaboratore a livello di sottoscrizione. L'account RunAs di Azure è la soluzione ideale per eseguire l'autenticazione sicura con i certificati e con un nome dell'entità servizio senza la necessità di archiviare un nome utente e una password in un oggetto credenziali. Per altre informazioni sull'autenticazione RunAs, vedere [Limitare le autorizzazioni dell'account RunAs](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Qualsiasi utente membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione può creare un account RunAs seguendo le istruzioni della sezione successiva.

Per creare un account RunAs nell'account Azure:

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco di risorse immettere e selezionare **Account di Automazione**.

2. Nella pagina **Account di Automazione** selezionare il nome dell'account di Automazione.

3. Nel riquadro sul lato sinistro della finestra selezionare **Account RunAs** nella sezione Impostazioni account.

4. Selezionare **Account RunAs di Azure**. Quando viene visualizzato il riquadro **Aggiungi account RunAs di Azure**, rivedere le informazioni generali e quindi selezionare **Crea** per avviare il processo di creazione dell'account.

5. Attendere alcuni minuti mentre viene creato l'account RunAs. È possibile tenere traccia dello stato di avanzamento della creazione nel menu Notifiche.

6. Al termine del processo, nell'account di Automazione specificato verrà creato un asset denominato AzureRunAsConnection. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato. Prendere nota dell'ID applicazione, perché verrà usato più avanti.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Creare un'assegnazione di ruolo in Desktop virtuale Windows

Successivamente, è necessario creare un'assegnazione di ruolo in modo che AzureRunAsConnection possa interagire con Desktop virtuale Windows. Assicurarsi di usare PowerShell per accedere con un account che abbia le autorizzazioni per creare assegnazioni di ruolo.

Prima di tutto, scaricare e importare il [modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Eseguire i cmdlet di PowerShell seguenti per connettersi a Desktop virtuale Windows e visualizzare i tenant.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Una volta individuato il tenant con i pool di host da dimensionare, seguire le istruzioni riportate in [Creare un account di Automazione di Azure](#create-an-azure-automation-account) e usare nel cmdlet seguente il nome del tenant ottenuto dal cmdlet precedente per creare l'assegnazione di ruolo:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Creare l'app per la logica di Azure e la pianificazione dell'esecuzione

Infine, è necessario creare l'app per la logica di Azure e configurare una pianificazione dell'esecuzione per il nuovo strumento di dimensionamento.

1.  Aprire Windows PowerShell come amministratore

2.  Eseguire il cmdlet seguente per accedere all'account Azure.

     ```powershell
     Login-AzAccount
     ```

3. Eseguire il cmdlet seguente per scaricare il file di script createazurelogicapp.ps1 nel computer locale.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. Eseguire il cmdlet seguente per accedere a Desktop virtuale Windows con un account che abbia le autorizzazioni di Proprietario o Collaboratore di Servizi Desktop remoto.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Eseguire lo script di PowerShell seguente per creare l'app per la logica di Azure e la pianificazione dell'esecuzione.

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id
     
     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id
     
     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location
     
     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName
     
     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName
     
     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"
     
     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name
     
     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     Dopo aver eseguito lo script, l'app per la logica verrà visualizzata in un gruppo di risorse, come illustrato nell'immagine seguente.

     ![Immagine della pagina di panoramica con un esempio di app per la logica di Azure.](../media/logic-app.png)

Per apportare modifiche alla pianificazione dell'esecuzione, ad esempio per cambiare l'intervallo di ricorrenza o il fuso orario, passare all'utilità di pianificazione per la scalabilità automatica e selezionare **Modifica** per aprire la finestra di progettazione di App per la logica.

![Immagine della finestra di progettazione di App per la logica. I menu Ricorrenza e Webhook che consentono di modificare i tempi di ricorrenza e il file webhook sono aperti.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gestire lo strumento di dimensionamento

Ora che è stato creato lo strumento di dimensionamento, è possibile accedere al relativo output. Questa sezione descrive alcune funzionalità che possono risultare utili.

### <a name="view-job-status"></a>Visualizzare lo stato di un processo

Nel portale di Azure è possibile visualizzare uno stato riepilogativo di tutti i processi del runbook o uno stato più dettagliato di uno specifico processo.

A destra dell'account di Automazione selezionato, in "Statistiche processi", è possibile visualizzare un elenco di riepiloghi di tutti i processi del runbook. Aprendo la pagina **Processi** sul lato sinistro della finestra vengono visualizzati gli stati dei processi correnti, l'ora di inizio e l'ora di completamento.

![Screenshot della pagina di stati dei processi.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Visualizzare i log e l'output dello strumento di dimensionamento

È possibile visualizzare i log delle operazioni di aumento e riduzione aprendo il runbook e selezionando il nome del processo.

Passare al runbook (il nome predefinito è WVDAutoScaleRunbook) nel gruppo di risorse che ospita l'account di Automazione di Azure e selezionare **Panoramica**. Nella pagina di panoramica selezionare un processo in Processi recenti e visualizzare il relativo output dello strumento di dimensionamento, come illustrato nell'immagine seguente.

![Immagine della finestra di output dello strumento di dimensionamento.](../media/tool-output.png)

