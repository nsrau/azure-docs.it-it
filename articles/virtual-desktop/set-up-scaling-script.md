---
title: La sessione di ridimensionamento ospita automazione di Azure-Azure
description: Come ridimensionare automaticamente gli host sessione desktop virtuale di Windows con automazione di Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349872"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Ridimensionare gli host di sessione usando automazione di Azure

È possibile ridurre il costo totale di distribuzione di desktop virtuali Windows scalando le macchine virtuali (VM). Ciò significa arrestare e deallocare le macchine virtuali host della sessione durante le ore di minore utilizzo, quindi riaccenderle e riallocarle durante le ore di picco.

Questo articolo illustra lo strumento di ridimensionamento creato con automazione di Azure e app per la logica di Azure che scala automaticamente le macchine virtuali host della sessione nell'ambiente di desktop virtuale Windows. Per informazioni su come usare lo strumento di scalabilità, passare a [prerequisiti](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Funzionamento dello strumento di scalabilità

Lo strumento di scalabilità offre un'opzione di automazione a basso costo per i clienti che desiderano ottimizzare i costi della VM host della sessione.

È possibile utilizzare lo strumento di ridimensionamento per:
 
- Pianificare l'avvio e l'arresto delle macchine virtuali in base a orari di picco e di minore attività.
- Scalare in orizzontale le VM in base al numero di sessioni per core CPU.
- Ridimensionare le macchine virtuali durante le fasce orarie non di punta, lasciando il numero minimo di VM host sessione in esecuzione.

Lo strumento di scalabilità usa una combinazione di manuali operativi di PowerShell di automazione di Azure, webhook e app per la logica di Azure per funzionare. Quando viene eseguito lo strumento, app per la logica di Azure chiama un webhook per avviare il Runbook di automazione di Azure. Il Runbook crea quindi un processo.

Durante il periodo di picco dell'utilizzo, il processo controlla il numero corrente di sessioni e la capacità della VM dell'host della sessione attualmente in esecuzione per ogni pool host. Usa queste informazioni per calcolare se le VM host della sessione in esecuzione possono supportare le sessioni esistenti in base al parametro *SessionThresholdPerCPU* definito per il file **createazurelogicapp. ps1** . Se le macchine virtuali host sessione non supportano le sessioni esistenti, il processo avvia altre VM host sessione nel pool host.

>[!NOTE]
>*SessionThresholdPerCPU* non limita il numero di sessioni nella macchina virtuale. Questo parametro determina solo quando è necessario avviare nuove macchine virtuali per bilanciare il carico delle connessioni. Per limitare il numero di sessioni, è necessario seguire le istruzioni [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) per configurare il parametro *MaxSessionLimit* di conseguenza.

Durante il tempo di utilizzo inferiore, il processo determina quali macchine virtuali host sessione devono essere arrestate in base al parametro *MinimumNumberOfRDSH* . Il processo imposterà le VM host della sessione in modalità svuotamento per impedire che le nuove sessioni si connettano agli host. Se si imposta il parametro *LimitSecondsToForceLogOffUser* su un valore positivo diverso da zero, il processo invierà una notifica a tutti gli utenti attualmente connessi per salvare il lavoro, attendere la quantità di tempo configurata e quindi forzare la disconnessione degli utenti. Dopo la disconnessione di tutte le sessioni utente nella macchina virtuale host sessione, il processo arresterà la macchina virtuale.

Se si imposta il parametro *LimitSecondsToForceLogOffUser* su zero, il processo consentirà l'impostazione di configurazione della sessione nei criteri di gruppo specificati per gestire la firma delle sessioni utente. Per visualizzare questi criteri di gruppo, passare a **configurazione** > **computer criteri** > **modelli amministrativi** > **componenti** > di Windows**Servizi** > Terminal**Terminal Server** > **limiti di tempo della sessione**. Se sono presenti sessioni attive in una macchina virtuale host sessione, il processo lascerà la macchina virtuale host sessione in esecuzione. Se non sono presenti sessioni attive, il processo arresterà la macchina virtuale host sessione.

Il processo viene eseguito periodicamente in base a un intervallo di ricorrenza del set. È possibile modificare questo intervallo in base alle dimensioni dell'ambiente di desktop virtuale di Windows, ma tenere presente che l'avvio e l'arresto delle macchine virtuali possono richiedere del tempo, quindi ricordare di tenere conto del ritardo. È consigliabile impostare l'intervallo di ricorrenza su ogni 15 minuti.

Tuttavia, lo strumento presenta anche le limitazioni seguenti:

- Questa soluzione si applica solo alle VM host della sessione in pool.
- Questa soluzione gestisce le macchine virtuali in qualsiasi area, ma può essere usata solo nella stessa sottoscrizione dell'account di automazione di Azure e delle app per la logica di Azure.

>[!NOTE]
>Lo strumento di scalabilità controlla la modalità di bilanciamento del carico del pool host in cui viene ridimensionato. Lo imposta sul bilanciamento del carico per la prima volta per le ore di picco e di minore traffico.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la configurazione dello strumento di scalabilità, assicurarsi che siano disponibili gli elementi seguenti:

- Un [tenant e un pool host di desktop virtuali Windows](create-host-pools-arm-template.md)
- Macchine virtuali del pool host sessione configurate e registrate con il servizio desktop virtuale di Windows
- Un utente con [accesso collaboratore](../role-based-access-control/role-assignments-portal.md) per la sottoscrizione di Azure

Il computer usato per distribuire lo strumento deve avere: 

- Windows PowerShell 5,1 o versione successiva
- Il modulo Microsoft AZ PowerShell

Se il tutto è pronto, iniziamo.

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure

Per prima cosa, è necessario un account di automazione di Azure per eseguire il Runbook di PowerShell. Di seguito viene illustrato come configurare l'account:

1. Aprire Windows PowerShell come amministratore.
2. Eseguire il cmdlet seguente per accedere al proprio account Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >L'account deve disporre dei diritti di collaboratore per la sottoscrizione di Azure in cui si vuole distribuire lo strumento di scalabilità.

3. Eseguire il cmdlet seguente per scaricare lo script per la creazione dell'account di automazione di Azure:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Eseguire il cmdlet seguente per eseguire lo script e creare l'account di automazione di Azure:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. L'output del cmdlet includerà un URI del webhook. Assicurarsi di tenere un record dell'URI perché verrà usato come parametro quando si configura la pianificazione di esecuzione per le app per la logica di Azure.

6. Dopo aver configurato l'account di automazione di Azure, accedere alla sottoscrizione di Azure e verificare che l'account di automazione di Azure e i Runbook pertinenti siano visualizzati nel gruppo di risorse specificato, come illustrato nell'immagine seguente:

   ![Immagine della pagina Panoramica di Azure che mostra l'account di automazione appena creato e Runbook.](media/automation-account.png)

  Per verificare se il webhook è il punto in cui deve essere, selezionare il nome della Runbook. Passare quindi alla sezione risorse di Runbook e selezionare **webhook**.

## <a name="create-an-azure-automation-run-as-account"></a>Creare un account RunAs di automazione di Azure

Ora che si dispone di un account di automazione di Azure, è necessario creare anche un account RunAs di automazione di Azure per accedere alle risorse di Azure.

Un [account RunAs di automazione di Azure](../automation/manage-runas-account.md) fornisce l'autenticazione per la gestione delle risorse in Azure con i cmdlet di Azure. Quando si crea un account RunAs, viene creato un nuovo utente entità servizio in Azure Active Directory e il ruolo Collaboratore viene assegnato all'utente dell'entità servizio a livello di sottoscrizione, l'account RunAs di Azure è un ottimo modo per eseguire l'autenticazione in modo sicuro con certificati e un nome dell'entità servizio senza dover archiviare un nome utente e una password in un oggetto credenziale. Per ulteriori informazioni sull'autenticazione Esegui come, vedere [limitazione delle autorizzazioni dell'account RunAs](../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Qualsiasi utente che sia membro del ruolo amministratori della sottoscrizione e coamministratore della sottoscrizione può creare un account RunAs seguendo le istruzioni della sezione successiva.

Per creare un account RunAs nell'account Azure:

1. Nella portale di Azure selezionare **tutti i servizi**. Nell'elenco delle risorse immettere e selezionare account di **automazione**.

2. Nella pagina **account di automazione** selezionare il nome dell'account di automazione.

3. Nel riquadro sul lato sinistro della finestra selezionare **account RunAs** nella sezione Impostazioni account.

4. Selezionare **account RunAs di Azure**. Quando viene visualizzato il riquadro **Aggiungi account RunAs di Azure** , esaminare le informazioni generali e quindi selezionare **Crea** per avviare il processo di creazione dell'account.

5. Attendere alcuni minuti per la creazione dell'account RunAs da parte di Azure. È possibile tenere traccia dello stato di avanzamento della creazione nel menu in notifiche.

6. Al termine del processo, verrà creato un asset denominato AzureRunAsConnection nell'account di automazione specificato. L'asset di connessione include l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato. Ricordare l'ID applicazione, perché verrà usato in un secondo momento.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Creare un'assegnazione di ruolo in Desktop virtuale Windows

Successivamente, è necessario creare un'assegnazione di ruolo in modo che AzureRunAsConnection possa interagire con il desktop virtuale di Windows. Assicurarsi di usare PowerShell per accedere con un account che disponga delle autorizzazioni per creare assegnazioni di ruolo.

Prima di tutto, scaricare e importare il [modulo PowerShell per desktop virtuale Windows](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Eseguire i cmdlet di PowerShell seguenti per connettersi a Desktop virtuale Windows e visualizzare i tenant.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Quando si trova il tenant con i pool host di cui si vuole eseguire la scalabilità, seguire le istruzioni in [creare un account di automazione di Azure](#create-an-azure-automation-account) e usare il nome del tenant ottenuto dal cmdlet precedente nel cmdlet seguente per creare l'assegnazione di ruolo:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Creare l'app per la logica di Azure e la pianificazione di esecuzione

Infine, è necessario creare l'app per la logica di Azure e configurare una pianificazione dell'esecuzione per il nuovo strumento di scalabilità.

1.  Aprire Windows PowerShell come amministratore

2.  Eseguire il cmdlet seguente per accedere al proprio account Azure.

     ```powershell
     Login-AzAccount
     ```

3. Eseguire il cmdlet seguente per scaricare il file di script createazurelogicapp. ps1 nel computer locale.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Eseguire il cmdlet seguente per accedere a desktop virtuale di Windows con un account che disponga di autorizzazioni di proprietario Servizi Desktop remoto o di collaboratore Servizi Desktop remoto.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Eseguire lo script di PowerShell seguente per creare l'app per la logica di Azure e la pianificazione dell'esecuzione.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

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

     Dopo aver eseguito lo script, l'app per la logica deve essere visualizzata in un gruppo di risorse, come illustrato nella figura seguente.

     ![Immagine della pagina Panoramica per un'app per la logica di Azure di esempio.](media/logic-app.png)

Per apportare modifiche alla pianificazione dell'esecuzione, ad esempio modificare l'intervallo di ricorrenza o il fuso orario, passare all'utilità di pianificazione per la scalabilità automatica e selezionare **modifica** per passare alla finestra di progettazione delle app per la logica.

![Immagine della finestra di progettazione di app per la logica. I menu di ricorrenza e webhook che consentono all'utente di modificare i tempi di ricorrenza e il file webhook sono aperti.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gestire lo strumento di scalabilità

Ora che è stato creato lo strumento di ridimensionamento, è possibile accedere al relativo output. In questa sezione vengono descritte alcune funzionalità che possono risultare utili.

### <a name="view-job-status"></a>Visualizzare lo stato di un processo

È possibile visualizzare un riepilogo dello stato di tutti i processi di Runbook o visualizzare uno stato più dettagliato di un processo Runbook specifico nel portale di Azure.

A destra dell'account di automazione selezionato, in "statistiche processo", è possibile visualizzare un elenco di riepiloghi di tutti i processi di Runbook. L'apertura della pagina **processi** sul lato sinistro della finestra Mostra gli Stati dei processi correnti, l'ora di inizio e l'ora di completamento.

![Screenshot della pagina relativa allo stato del processo.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Visualizzare i log e l'output dello strumento di ridimensionamento

È possibile visualizzare i log delle operazioni di scalabilità orizzontale e di scalabilità orizzontale aprendo il Runbook e selezionando il nome del processo.

Passare a Runbook (il nome predefinito è WVDAutoScaleRunbook) nel gruppo di risorse che ospita l'account di automazione di Azure e selezionare **Overview (panoramica**). Nella pagina Panoramica selezionare un processo in processi recenti per visualizzare l'output dello strumento di ridimensionamento, come illustrato nella figura seguente.

![Immagine della finestra di output per lo strumento di ridimensionamento.](media/tool-output.png)

## <a name="report-issues"></a>Segnalare i problemi

Se si verificano problemi con lo strumento di scalabilità, è possibile segnalarli nella [pagina di GitHub di RDS](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).
