---
title: La sessione di scalabilità ospita Automazione di Azure - AzureScale session hosts Azure Automation - Azure
description: Come ridimensionare automaticamente gli host di sessione di Windows Virtual Desktop con Automazione di Azure.How to automatically scale Windows Virtual Desktop session hosts with Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349872"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Ridimensionare gli host di sessione usando Automazione di AzureScale session hosts using Azure Automation

È possibile ridurre il costo totale di distribuzione di Windows Virtual Desktop con la scalabilità delle macchine virtuali (VM). Ciò significa arrestare e deallocare le macchine virtuali host della sessione durante le ore di minore attività di utilizzo, quindi riattivarle e riassegnarle durante le ore di punta.

In questo articolo verranno fornite informazioni sullo strumento di scalabilità creato con Automazione di Azure e app per la logica di Azure che conterà automaticamente le macchine virtuali host di sessione nell'ambiente Windows Virtual Desktop.In this article, you'll learn about the scaling tool built with Azure Automation and Azure Logic Apps that will automatically scale session host virtual machines in your Windows Virtual Desktop environment. Per informazioni su come utilizzare lo strumento di ridimensionamento, passare a [Prerequisiti](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Funzionamento dello strumento di ridimensionamento

Lo strumento di scalabilità offre un'opzione di automazione a basso costo per i clienti che desiderano ottimizzare i costi della macchina virtuale dell'host di sessione.

È possibile utilizzare lo strumento di ridimensionamento per:You can use the scaling tool to:
 
- Pianificare l'avvio e l'arresto delle macchine virtuali in base all'orario di ufficio di Peak e Off-Peak.Schedule VMs to start and stop based on Peak and Off-Peak business hours.
- Scalabilità orizzontale delle macchine virtuali in base al numero di sessioni per core della CPU.
- Scalabilità nelle macchine virtuali durante le ore non di punta, lasciando in esecuzione il numero minimo di macchine virtuali host della sessione.

Lo strumento di ridimensionamento usa una combinazione di runbook di Azure Automation PowerShell, webhook e app per la logica di Azure per funzionare. Quando lo strumento viene eseguito, App per la logica di Azure chiama un webhook per avviare il runbook di Automazione di Azure.When the tool runs, Azure Logic Apps calls a webhook to start the Azure Automation runbook. Il runbook crea quindi un processo.

Durante il periodo di utilizzo massimo, il processo controlla il numero corrente di sessioni e la capacità della macchina virtuale dell'host della sessione in esecuzione corrente per ogni pool host. Usa queste informazioni per calcolare se le macchine virtuali host della sessione in esecuzione possono supportare sessioni esistenti in base al parametro *SessionThresholdPerCPU* definito per il file **createazurelogicapp.ps1.** Se le macchine virtuali dell'host della sessione non supportano le sessioni esistenti, il processo avvia macchine virtuali host di sessione aggiuntive nel pool host.

>[!NOTE]
>*SessionThresholdPerCPU* non limita il numero di sessioni nella macchina virtuale. Questo parametro determina solo quando è necessario iniziare nuove macchine virtuali per bilanciare il carico delle connessioni. Per limitare il numero di sessioni, è necessario seguire le istruzioni [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) per configurare il parametro *MaxSessionLimit* di conseguenza.

Durante il tempo di utilizzo non di punta, il processo determina quali macchine virtuali host della sessione devono essere arrestate in base al parametro *MinimumNumberOfRDSH.* Il processo imposterà le macchine virtuali dell'host della sessione sulla modalità di svuotamento per impedire la connessione delle nuove sessioni agli host. Se si imposta il parametro *LimitSecondsToForceLogOffUser* su un valore positivo diverso da zero, il processo notificherà a tutti gli utenti attualmente connessi di salvare il proprio lavoro, attendere il periodo di tempo configurato e quindi forzare la disconnessione degli utenti. Dopo che tutte le sessioni utente nella macchina virtuale dell'host di sessione sono state disconnesse, il processo arresterà la macchina virtuale.

Se si imposta il parametro *LimitSecondsToForceLogOffUser* su zero, il processo consentirà all'impostazione di configurazione della sessione nei criteri di gruppo specificati di gestire le sessioni utente di disconnessione. Per visualizzare questi criteri di gruppo, passare a Criteri **di configurazione** > **Policies**del computer > **Modelli amministrativi Modelli** > amministrativi**Componenti di Windows Componenti** > **Terminal Services** > Terminal Server Limiti di tempo per la sessione di**Server.** > **Session Time Limits** Se sono presenti sessioni attive in una macchina virtuale host sessione, il processo lascerà in esecuzione la macchina virtuale dell'host della sessione. Se non sono presenti sessioni attive, il processo arresterà la macchina virtuale dell'host della sessione.

Il processo viene eseguito periodicamente in base a un intervallo di ricorrenza impostato. È possibile modificare questo intervallo in base alle dimensioni dell'ambiente Windows Virtual Desktop, ma tenere presente che l'avvio e l'arresto di macchine virtuali può richiedere del tempo, pertanto tenere conto del ritardo. È consigliabile impostare l'intervallo di ricorrenza ogni 15 minuti.

Tuttavia, lo strumento ha anche le seguenti limitazioni:

- Questa soluzione si applica solo alle macchine virtuali host di sessione in pool.
- Questa soluzione gestisce le macchine virtuali in qualsiasi area, ma può essere usata solo nella stessa sottoscrizione dell'account di automazione di Azure e delle app per la logica di Azure.This solution manages VMs in any region, but can only be used in the same subscription as your Azure Automation account and Azure Logic Apps.

>[!NOTE]
>Lo strumento di ridimensionamento controlla la modalità di bilanciamento del carico del pool host di cui è ridimensionato. Imposta il bilanciamento del carico di livello per le ore di punta e non di punta.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a configurare lo strumento di ridimensionamento, assicurarsi di avere a disposizione le operazioni seguenti:

- Un [tenant e un pool host](create-host-pools-arm-template.md) di Windows Virtual Desktop
- Macchine virtuali del pool host di sessione configurate e registrate con il servizio Desktop virtuale di WindowsSession host pool VMs configured and registered with the Windows Virtual Desktop service
- Un utente con [accesso di collaboratore](../role-based-access-control/role-assignments-portal.md) nella sottoscrizione di AzureA user with Contributor access on Azure subscription

Il computer utilizzato per distribuire lo strumento deve disporre di: 

- Windows PowerShell 5.1 o versione successiva
- Modulo Microsoft Az PowerShell

Se hai tutto pronto, allora cominciamo.

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure

In primo luogo, è necessario un account di automazione di Azure per eseguire il runbook di PowerShell.First, you'll need an Azure Automation account to run the PowerShell runbook. Ecco come configurare il tuo account:

1. Aprire Windows PowerShell come amministratore.
2. Eseguire il cmdlet seguente per accedere all'account Azure.Run the following cmdlet to sign in to your Azure Account.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >L'account deve disporre dei diritti di collaboratore per la sottoscrizione di Azure in cui si vuole distribuire lo strumento di scalabilità.

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

5. L'output del cmdlet includerà un URI webhook. Assicurarsi di tenere un record dell'URI perché verrà usato come parametro quando si imposta la pianificazione dell'esecuzione per le app per la logica di Azure.Make sure to keep a record of the URI because you'll use it as a parameter when you set up the execution schedule for the Azure Logic apps.

6. Dopo aver configurato l'account di Automazione di Azure, accedere alla sottoscrizione di Azure e verificare che l'account di Automazione di Azure e il runbook pertinente siano stati visualizzati nel gruppo di risorse specificato, come illustrato nell'immagine seguente:After you've set up your Azure Automation account, sign in to your Azure subscription and check to make sure your Azure Automation account and the relevant runbook have appare in your specified resource group, as shown in the following image:

   ![Immagine della pagina di panoramica di Azure che mostra l'account di automazione e il runbook appena creati.](media/automation-account.png)

  Per verificare se il webhook è dove dovrebbe essere, selezionare il nome del runbook. Passare quindi alla sezione Risorse del runbook e selezionare **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Creare un account RunAs di Automazione di AzureCreate an Azure Automation RunAs account

Ora che si dispone di un account di Automazione di Azure, è necessario creare anche un account RunAs di Automazione di Azure per accedere alle risorse di Azure.Now that you have an Azure Automation account, you'll also need to create an Azure Automation RunAs account to access your Azure resources.

Un account RunAs di Automazione di Azure fornisce l'autenticazione per la gestione delle risorse in Azure con i cmdlet di Azure.An [Azure Automation RunAs account](../automation/manage-runas-account.md) provides authentication for managing resources in Azure with the Azure cmdlets. Quando si crea un account RunAs, viene creato un nuovo utente dell'entità servizio in Azure Active Directory e viene assegnato il ruolo Collaboratore all'utente dell'entità servizio a livello di sottoscrizione, l'account RunAs di Azure è un ottimo modo per eseguire l'autenticazione in modo sicuro con certificati e un nome dell'entità servizio senza dover archiviare un nome utente e una password in un oggetto credenziali. Per ulteriori informazioni sull'autenticazione RunAs, vedere [Limitazione delle autorizzazioni per gli account RunAs](../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Qualsiasi utente membro del ruolo Amministratori sottoscrizione e coamministratore della sottoscrizione può creare un account RunAs seguendo le istruzioni della sezione successiva.

Per creare un account RunAs nell'account Azure:To create a RunAs account in your Azure account:

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse immettere e selezionare **Account di automazione**.

2. Nella pagina Account di **automazione** selezionare il nome dell'account di automazione.

3. Nel riquadro sul lato sinistro della finestra selezionare **Account RunAs** nella sezione Impostazioni account.

4. Selezionare **Account RunAs**di Azure . Quando viene visualizzato il riquadro **Aggiungi account RunAs** di Azure, esaminare le informazioni generali e quindi selezionare **Crea** per avviare il processo di creazione dell'account.

5. Attendere alcuni minuti che Azure crei l'account RunAs. È possibile tenere traccia dello stato di avanzamento della creazione nel menu in Notifiche.

6. Al termine del processo, verrà creato un asset denominato AzureRunAsConnection nell'account di automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato. Ricordare l'ID dell'applicazione, perché lo userai in un secondo momento.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Creare un'assegnazione di ruolo in Desktop virtuale Windows

Successivamente, è necessario creare un'assegnazione di ruolo in modo che AzureRunAsConnection possa interagire con Windows Virtual Desktop.Next, you need to create a role assignment so that AzureRunAsConnection can interact with Windows Virtual Desktop. Assicurarsi di usare PowerShell per accedere con un account che dispone delle autorizzazioni per creare assegnazioni di ruolo.

Scaricare e importare il [modulo PowerShell](/powershell/windows-virtual-desktop/overview/) di Windows Virtual Desktop da usare nella sessione di PowerShell, se non è già stato fatto. Eseguire i cmdlet di PowerShell seguenti per connettersi a Desktop virtuale Windows e visualizzare i tenant.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Quando si trova il tenant con i pool host che si desidera ridimensionare, seguire le istruzioni in [Creare un account di Automazione](#create-an-azure-automation-account) di Azure e usare il nome del tenant ottenuto dal cmdlet precedente nel cmdlet seguente per creare l'assegnazione di ruolo:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Creare l'app per la logica di Azure e la pianificazione dell'esecuzioneCreate the Azure Logic App and execution schedule

Infine, è necessario creare l'app per la logica di Azure e impostare una pianificazione di esecuzione per il nuovo strumento di ridimensionamento.

1.  Aprire Windows PowerShell come amministratore

2.  Eseguire il cmdlet seguente per accedere all'account Azure.Run the following cmdlet to sign in to your Azure Account.

     ```powershell
     Login-AzAccount
     ```

3. Eseguire il cmdlet seguente per scaricare il file di script createazurelogicapp.ps1 nel computer locale.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Eseguire il cmdlet seguente per accedere a Windows Virtual Desktop con un account con autorizzazioni di proprietario o collaboratore RDS.

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

     Dopo aver eseguito lo script, l'app per la logica deve essere visualizzata in un gruppo di risorse, come illustrato nell'immagine seguente.

     ![Immagine della pagina di panoramica per un'app per la logica di Azure di esempio.](media/logic-app.png)

Per apportare modifiche alla pianificazione dell'esecuzione, ad esempio la modifica dell'intervallo di ricorrenza o del fuso orario, passare all'utilità di pianificazione di scalabilità automatica e selezionare **Modifica** per passare a Progettazione app per la logica.

![Immagine di Progettazione app per la logica. I menu Ricorrenza e Webhook che consentono all'utente di modificare i tempi di ricorrenza e il file webhook sono aperti.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gestire lo strumento di ridimensionamento

Dopo aver creato lo strumento di ridimensionamento, è possibile accedere al relativo output. In questa sezione vengono descritte alcune funzionalità che potrebbero essere utili.

### <a name="view-job-status"></a>Visualizzare lo stato di un processo

È possibile visualizzare uno stato riepilogatio di tutti i processi del runbook o uno stato più approfondito di un processo di runbook specifico nel portale di Azure.You can view a summarized status of all runbook jobs or view a more in-depth status of a specific runbook job in the Azure portal.

A destra dell'account di automazione selezionato, in "Statistiche processo", è possibile visualizzare un elenco di riepiloghi di tutti i processi del runbook. L'apertura della pagina **Processi** sul lato sinistro della finestra mostra gli stati dei lavori correnti, gli orari di inizio e i tempi di completamento.

![Schermata della pagina di stato del processo.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Visualizzare i log e l'output dello strumento di ridimensionamento

È possibile visualizzare i log delle operazioni di scalabilità orizzontale e verticale aprendo il runbook e selezionando il nome del processo.

Passare al runbook (il nome predefinito è WVDAutoScaleRunbook) nel gruppo di risorse che ospita l'account di Automazione di Azure e selezionare **Panoramica**. Nella pagina di panoramica, selezionare un processo in Processi recenti per visualizzarne l'output dello strumento di ridimensionamento, come illustrato nell'immagine seguente.

![Immagine della finestra di output per lo strumento di ridimensionamento.](media/tool-output.png)

## <a name="report-issues"></a>Segnalare i problemi

Se si verificano problemi con lo strumento di ridimensionamento, è possibile segnalarli nella [pagina GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)di RDS .
