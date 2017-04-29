---
title: Ruoli di lavoro ibridi per runbook di Automazione di Azure | Microsoft Docs
description: "Questo articolo fornisce informazioni sull&quot;installazione e l&quot;uso di Hybrid Runbook Workers, una funzionalità di Automazione di Azure che consente di eseguire i runbook nei computer del data center locale."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 3a958b790b4501153aad86310c3783d49733dd90
ms.lasthandoff: 03/31/2017

---

# <a name="automate-resources-in-your-data-center-with-hybrid-runbook-worker"></a>Automatizzare le risorse nel data center con i ruoli di lavoro ibrido per runbook
I runbook in Automazione di Azure non possono accedere alle risorse nel data center locale perché vengono eseguiti nel cloud di Azure.  La funzionalità di ruolo di lavoro ibrido per runbook di Automazione di Azure consente di eseguire runbook su macchine che si trovano nel data center per gestire le risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer locali.  

Questa funzionalità è illustrata nell'immagine seguente:<br>  

![Panoramica di Hybrid Runbook Workers](media/automation-hybrid-runbook-worker/automation.png)

Grazie a tale funzionalità, è possibile designare uno o più computer del data center come computer di lavoro runbook ibridi ed eseguire i runbook da Automazione di Azure.  Ogni ruolo di lavoro richiede l'agente di gestione Microsoft con una connessione a Microsoft Operations Management Suite e all'ambiente runbook di Automazione di Azure.  Operations Management Suite viene usato solo per installare e gestire l'agente di gestione e per monitorare la funzionalità del ruolo di lavoro.  La distribuzione dei runbook e l'istruzione per eseguirli vengono gestite mediante Automazione di Azure.

Non sono previsti requisiti di firewall in ingresso per supportare Hybrid Runbook Workers. L'agente nel computer locale avvia tutte le comunicazioni con Automazione di Azure nel cloud. Quando un runbook viene avviato, Automazione di Azure crea un'istruzione che viene recuperata dall'agente, che esegue quindi il pull del runbook e di tutti i parametri prima dell'esecuzione,  oltre a recuperare [asset](http://msdn.microsoft.com/library/dn939988.aspx) usati dal runbook di Automazione di Azure.

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per runbook con DSC (Desired State Configuration), è necessario aggiungerli come nodi DSC.  Per informazioni sul caricamento dei server per la gestione con DSC, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).           
><br>
>Se si abilita la [soluzione di gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md), qualsiasi computer Windows connesso all'area di lavoro OMS viene automaticamente configurato come ruolo di lavoro ibrido per runbook per supportare i runbook che fanno parte di questa soluzione.  Non viene però eseguita la registrazione con i gruppi di ruoli di lavoro ibridi già definiti nell'account di Automazione.  È possibile aggiungere il computer a un gruppo ruolo di lavoro ibrido per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo ruolo di lavoro ibrido per runbook.  Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.  

## <a name="hybrid-runbook-worker-groups"></a>Gruppi di computer di lavoro runbook ibridi
Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente.  Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata.

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook, è necessario specificare il gruppo in cui verrà eseguito.  I membri del gruppo determinano il ruolo di lavoro che gestirà la richiesta.  Non è possibile scegliere un computer di lavoro specifico.

## <a name="hybrid-runbook-worker-requirements"></a>Requisiti del ruolo di lavoro ibrido per runbook
È necessario designare almeno un computer locale per eseguire i processi dei runbook ibridi.  Il computer deve avere le caratteristiche seguenti:

* Windows Server 2012 o versioni successive
* Windows PowerShell 4.0 o versioni successive.  Si consiglia di installare Windows PowerShell 5.0 nel computer per una maggiore affidabilità. È possibile scaricare la versione più recente dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=50395)
* Almeno 2 core e 4 GB di RAM

Tenere presente quanto segue per i ruoli di lavoro ibridi:

* Designare più ruoli di lavoro ibridi in ogni gruppo per garantire disponibilità elevata.  
* I ruoli di lavoro ibridi possono coesistere con server runbook Service Management Automation o System Center Orchestrator.
* Può essere opportuno usare un computer situato fisicamente all'interno o in prossimità dell'area dell'account di automazione, perché al termine di un processo i relativi dati vengono restituiti ad Automazione di Azure.

### <a name="configure-proxy-and-firewall-settings"></a>Configurare le impostazioni di proxy e firewall
Per connettersi e registrarsi al servizio Microsoft Operations Management Suite (OMS), il ruolo di lavoro ibrido per runbook locale deve avere accesso al numero di porta e agli URL descritti di seguito,  in aggiunta alle [porte e agli URL necessari per la connessione di Microsoft Monitoring Agent](../log-analytics/log-analytics-proxy-firewall.md#configure-settings-with-the-microsoft-monitoring-agent) a OMS. Se si usa un server proxy per la comunicazione tra l'agente e il servizio OMS, è necessario assicurarsi che le risorse appropriate siano accessibili. Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso.

Di seguito sono elencati la porta e gli URL necessari affinché il ruolo di lavoro ibrido per runbook comunichi con Automazione.

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita
* URL globale: *.azure-automation.net

Se si ha un account di automazione definito per un'area specifica e si vuole limitare la comunicazione con il data center di tale area, nella tabella seguente è riportato il record DNS per ogni area.

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net |
| Asia sudorientale |sea-jobruntimedata-prod-su1.azure-automation.net |
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net |

## <a name="installing-hybrid-runbook-worker"></a>Installazione di Hybrid Runbook Workers

Per installare e configurare un ruolo di lavoro ibrido per runbook, sono disponibili due metodi.  Il metodo consigliato consiste nell'usare un runbook di Automazione per automatizzare completamente il processo necessario per configurare un computer Windows.  Il secondo metodo prevede l'esecuzione di una procedura dettagliata per installare e configurare il ruolo manualmente.  

### <a name="automated-deployment"></a>Distribuzione automatizzata

Seguire questa procedura per automatizzare l'installazione e configurazione del ruolo di lavoro ibrido.  

1. Scaricare lo script *New-OnPremiseHybridWorker.ps1* da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) direttamente dal computer che esegue il ruolo di lavoro ibrido per runbook o da un altro computer nell'ambiente e copiarlo nel ruolo di lavoro.  

    Per l'esecuzione dello script *New-OnPremiseHybridWorker.ps1* sono necessari i parametri seguenti:

  * *AutomationAccountName* (obbligatorio) - nome dell'account di Automazione.  
  * *ResourceGroupName* (obbligatorio) - nome del gruppo di risorse associato all'account di Automazione.  
  * *HybridGroupName* (obbligatorio) - nome di un gruppo di ruoli di lavoro ibridi per runbook che si specifica come destinazione per i runbook che supportano questo scenario. 
  *  *SubscriptionID* (obbligatorio) - ID della sottoscrizione di Azure in cui si trova l'account di Automazione.
  *  *WorkspaceName* (facoltativo) - nome dell'area di lavoro OMS.  Se non si ha un'area di lavoro OMS, lo script ne crea e configura una.  

     > [!NOTE]
     > Le uniche aree di Automazione supportate per l'integrazione con OMS sono attualmente: **Australia sud-orientale**, **Stati Uniti orientali 2**, **Asia sud-orientale** ed **Europa occidentale**.  Se l'account di Automazione non si trova in una di queste aree, lo script crea un'area di lavoro OMS ma avvisa l'utente che non sarà possibile eseguire il collegamento.
     > 
2. Nel computer in uso avviare **Windows PowerShell** dalla schermata **Start** in modalità amministratore.  
3. Dalla shell della riga di comando di PowerShell passare alla cartella che contiene lo script scaricato ed eseguirlo modificando i valori per i parametri *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* e *-WorkspaceName*.

     > [!NOTE] 
     > Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script.  È **necessario** accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Verrà chiesto di accettare di installare **NuGet** e di eseguire l'autenticazione con le credenziali di Azure.<br><br> ![Esecuzione dello script New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Dopo il completamento dello script, il pannello Gruppi di ruoli di lavoro ibridi mostrerà il nuovo gruppo e il numero di membri oppure, nel caso di un gruppo esistente, il numero di membri verrà incrementato.  È possibile selezionare il gruppo nell'elenco nel pannello **Gruppi di ruoli di lavoro ibridi** e selezionare il riquadro **Ruoli di lavoro per runbook**.  Nel pannello **Ruoli di lavoro per runbook** sono elencati i membri del gruppo.  

### <a name="manual-deployment"></a>Distribuzione manuale 
Eseguire i primi due passaggi una volta per l'ambiente di automazione e quindi ripetere i passaggi rimanenti per ogni computer di lavoro.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Creare l'area di lavoro di Operations Management Suite
Se non si ha ancora un'area di lavoro di Operations Management Suite, crearne una seguendo le istruzioni in [Gestire le aree di lavoro](../log-analytics/log-analytics-manage-access.md). Se già si dispone di un'area di lavoro, è possibile usarla.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Aggiungere la soluzione di automazione all'area di lavoro di Operations Management Suite
Le soluzioni aggiungono funzionalità a Operations Management Suite.  La soluzione di automazione aggiunge funzionalità per Automazione di Azure, incluso il supporto per il ruolo di lavoro ibrido per runbook.  Quando si aggiunge la soluzione all'area di lavoro, i componenti del ruolo di lavoro vengono automaticamente propagati al computer dell'agente che verrà installato nel passaggio successivo.

Per aggiungere la soluzione [Automazione](../log-analytics/log-analytics-add-solutions.md) all'area di lavoro di Operations Management Suite, seguire le istruzioni contenute nell'articolo relativo a **come aggiungere una soluzione tramite la raccolta soluzioni** .

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installare Microsoft Monitoring Agent
Microsoft Monitoring Agent connette i computer a Operations Management Suite.  Quando si installa l'agente nel computer locale e lo si connette all'area di lavoro, viene eseguito automaticamente il download dei componenti necessari per il ruolo di lavoro ibrido per runbook.

Per installare l'agente nel computer locale, seguire le istruzioni contenute in [Connettere computer Windows a Log Analytics](../log-analytics/log-analytics-windows-agents.md).  È possibile ripetere questo processo per più computer per aggiungere più ruoli di lavoro nell'ambiente.

Dopo che l'agente si è connesso a Operations Management Suite, viene elencato nella scheda **Origini connesse** del riquadro **Impostazioni** di Operations Management Suite.  È possibile verificare che l'agente abbia scaricato correttamente la soluzione di automazione se include una cartella **AzureAutomationFiles** in C:\Programmi\Microsoft Monitoring Agent\Agent.  Per verificare la versione del ruolo di lavoro ibrido per runbook, passare a C:\Programmi\Microsoft Monitoring Agent\Agent\AzureAutomation\ e prendere nota della sottocartella \\*version*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installare l'ambiente runbook e connettersi ad Automazione di Azure
Quando si aggiunge un agente a Operations Management Suite, la soluzione di automazione esegue il push del modulo **HybridRegistration** di PowerShell che contiene il cmdlet **Add-HybridRunbookWorker**.  È possibile usare questo cmdlet per installare l'ambiente runbook nel computer e registrarlo in Automazione di Azure.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Eseguire quindi il cmdlet **Add-HybridRunbookWorker** con la sintassi seguente:

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

È possibile ottenere le informazioni necessarie per questo cmdlet dal pannello **Gestisci chiavi** del portale di Azure.  Aprire il pannello selezionando l'opzione **Chiavi** nel pannello **Impostazioni** nell'account di Automazione.

![Panoramica di Hybrid Runbook Workers](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Name** è il nome del gruppo di computer di lavoro runbook ibridi. Se il gruppo già esiste nell'account di automazione, il computer corrente vi verrà aggiunto direttamente.  Se il gruppo ancora non esiste, verrà aggiunto.
* **EndPoint** è il valore del campo **URL** del pannello **Gestisci chiavi**.
* **Token** è il valore **Chiave di accesso primaria** nel pannello **Gestisci chiavi**.  

Usare l'opzione **-Verbose** con **Add-HybridRunbookWorker** per ricevere informazioni dettagliate sull'installazione.

#### <a name="5-install-powershell-modules"></a>5. Installare i moduli di PowerShell
I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure.  Questi moduli tuttavia non vengono distribuiti automaticamente nei computer locali, pertanto è necessario installarli manualmente.  L'unica eccezione è rappresentata dal modulo Azure, che viene installato per impostazione predefinita, garantendo l'accesso ai cmdlet per tutti i servizi e le attività di Azure per Automazione di Azure.

Poiché lo scopo principale della funzionalità ruolo di lavoro ibrido per runbook è gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano queste risorse.  Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere il [relativo articolo](http://msdn.microsoft.com/library/dd878350.aspx) .  I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente PSModulePath, in modo da venire importati automaticamente dal ruolo di lavoro ibrido.  Per altre informazioni, vedere [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx) (Modifica del percorso di installazione di PSModulePath). 

## <a name="removing-hybrid-runbook-worker"></a>Rimozione del ruolo di lavoro ibrido per runbook 
È possibile rimuovere uno o più ruoli di lavoro ibrido per runbook da un gruppo o è possibile rimuovere il gruppo, a seconda dei requisiti.  Per rimuovere un ruolo di lavoro ibrido per runbook da un computer locale, seguire questa procedura.

1. Nel portale di Azure passare all'account di Automazione.  
2. Nel pannello **Impostazioni** selezionare **Chiavi** e prendere nota dei valori per il campo **URL** e **Chiave di accesso primaria**.  Queste informazioni saranno necessarie per il passaggio successivo.
3. Aprire una sessione di PowerShell in modalità amministratore ed eseguire il comando seguente - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Per un log dettagliato del processo di rimozione, usare l'opzione **-Verbose** .

> [!NOTE]
> Questa operazione non rimuove Microsoft Monitoring Agent dal computer, ma solo la funzionalità e la configurazione del ruolo di lavoro ibrido per runbook.  

## <a name="remove-hybrid-worker-groups"></a>Rimuovere gruppi di ruoli di lavoro ibridi
Per rimuovere un gruppo, è innanzitutto necessario rimuovere il ruolo di lavoro ibrido per runbook da ogni computer membro del gruppo usando la procedura descritta in precedenza e quindi attenersi alla procedura seguente per rimuovere il gruppo.  

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare il riquadro **Gruppi di ruoli di lavoro ibridi** e nel pannello **Gruppi di lavoro ibridi** selezionare il gruppo che si desidera eliminare.  Dopo aver selezionato il gruppo specifico, viene visualizzato il pannello delle proprietà del **Gruppo di lavoro ibrido**.<br> ![Pannello Gruppi di ruoli di lavoro ibridi ](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. Nel pannello delle proprietà del gruppo selezionato fare clic su **Elimina**.  Viene visualizzato un messaggio che chiede di confermare l'azione. Selezionare **Sì** se si è certi che si vuole procedere.<br> ![Finestra di dialogo di conferma dell'eliminazione del gruppo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>Avvio dei runbook in Hybrid Runbook Workers
[avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) illustra diversi modi in cui è possibile eseguire l'avvio dei runbook.  Hybrid Runbook Workers aggiunge un'opzione **RunOn** in cui è possibile specificare il nome di un gruppo di computer di lavoro runbook ibridi.  Se si specifica un gruppo, il runbook verrà recuperato ed eseguito dai computer di lavoro inclusi in tale gruppo.  Se non si specifica l'opzione, verrà eseguito come di consueto in Automazione di Azure.

Quando si avvia un runbook nel portale di Azure, viene visualizzata l'opzione **Esegui in**, che consente di scegliere tra **Azure** o **Ruolo di lavoro ibrido**.  Se si seleziona **Computer di lavoro ibrido**, sarà quindi possibile selezionare il gruppo da un elenco a discesa.

Usare il parametro **RunOn**.  È possibile usare il comando seguente per avviare un runbook denominato Test-Runbook in un gruppo di ruoli di lavoro ibridi per runbook denominato MyHybridGroup usando Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> Il parametro **RunOn** è stato aggiunto al cmdlet **Start-AzureAutomationRunbook** nella versione 0.9.1 di Microsoft Azure PowerShell.  È consigliabile [scaricare la versione più recente](https://azure.microsoft.com/downloads/) se la versione installata è precedente.  È sufficiente installare questa versione nella workstation in cui si avvierà il runbook da Windows PowerShell.  Non è necessario installarla nel computer di lavoro, a meno che non si intenda avviare i runbook da tale computer.  Non è attualmente possibile avviare un runbook in un computer di lavoro runbook ibrido da un altro runbook, in quanto nell'account di automazione dovrebbe essere installata la versione più recente di Azure PowerShell.  Tale versione viene aggiornata automaticamente in Automazione di Azure e viene inviata automaticamente tramite push ai ruoli di lavoro.
>
>

## <a name="runbook-permissions"></a>Autorizzazioni per i runbook
I runbook eseguiti in un ruolo di lavoro ibrido per runbook non possono usare lo stesso metodo in genere usato per l'autenticazione dei runbook per le risorse di Azure, perché accedono a risorse esterne ad Azure.  Il runbook può fornire la propria autenticazione alle risorse locali oppure è possibile specificare un account RunAs per fornire un contesto utente per tutti i runbook.

### <a name="runbook-authentication"></a>Autenticazione dei runbook
Per impostazione predefinita, i runbook vengono eseguiti nel contesto dell'account di sistema locale nel computer locale, quindi devono autenticarsi per le risorse a cui accederanno.  

Nel proprio runbook è possibile usare asset di tipo [Credenziali](http://msdn.microsoft.com/library/dn940015.aspx) e [Certificato](http://msdn.microsoft.com/library/dn940013.aspx) con cmdlet che consentono di specificare le credenziali per poter eseguire l'autenticazione per risorse diverse.  L'esempio seguente illustra una parte di un runbook che riavvia un computer.  Recupera le credenziali da un asset di tipo credenziale e il nome del computer da un asset di tipo variabile e quindi usa questi valori con il cmdlet Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

È anche possibile usare [InlineScript](automation-powershell-workflow.md#inlinescript), che consente di eseguire blocchi di codice in un altro computer con le credenziali specificate dal [parametro comune PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Account RunAs
Per evitare che i runbook debbano autenticarsi per le risorse locali, è possibile specificare un account **RunAs** per un gruppo di ruoli di lavoro ibridi.  Specificare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali. Tutti i runbook useranno queste credenziali durante l'esecuzione in un ruolo di lavoro ibrido per runbook nel gruppo.  

Il nome utente per le credenziali deve essere in uno dei formati seguenti:

* dominio\nome utente
* username@domain
* nome utente (per gli account locali nel computer locale)

Usare la procedura seguente per specificare un account RunAs per un gruppo di lavoro ibrido:

1. Creare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali.
2. Nel portale di Azure aprire l'account di automazione.
3. Selezionare il riquadro **Gruppi di ruoli di lavoro ibridi** e quindi il gruppo.
4. Selezionare **Tutte le impostazioni** e quindi **Impostazioni del gruppo di lavoro ibrido**.
5. Modificare **Esegui come** da **Predefinito** a **Personalizzato**.
6. Selezionare le credenziali e fare clic su **Salva**.

### <a name="automation-run-as-account"></a>Account RunAs di Automazione
Come parte del processo di compilazione automatizzato per la distribuzione di risorse in Azure, potrebbe essere necessario interrogare sistemi locali per supportare un'attività o un set di passaggi nella sequenza di distribuzione.  Per supportare l'autenticazione in Azure con l'account RunAs, è necessario installare il certificato dell'account RunAs.  

Il runbook di PowerShell seguente, *Export-RunAsCertificateToHybridWorker*, esporta il certificato RunAs dall'account di automazione di Azure e lo scarica e lo importa nell'archivio certificati del computer locale in un ruolo di lavoro ibrido connesso allo stesso account.  Una volta completato questo passaggio, viene verificato che il ruolo di lavoro possa eseguire l'autenticazione in Azure usando l'account RunAs.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT 
    .TAGS Azure Automation 
    .LICENSEURI 
    .PROJECTURI 
    .ICONURI 
    .EXTERNALMODULEDEPENDENCIES 
    .REQUIREDSCRIPTS 
    .EXTERNALSCRIPTDEPENDENCIES 
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. 
  
    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team 
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])] 

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"
       
    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"
   
    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose 

    Write-Output ("Importing certificate into local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection" 
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Select-AzureRmSubscription -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

Salvare il runbook *Export-RunAsCertificateToHybridWorker* nel computer con un'estensione `.ps1`.  Importarlo nell'account di Automazione e modificare il runbook, cambiando il valore della variabile `$Password` con quello della propria password.  Pubblicare e quindi eseguire il runbook scegliendo come destinazione il gruppo di ruoli di lavoro ibridi che esegue e autentica i runbook usando l'account RunAs.  Il flusso di processo segnala il tentativo di importare il certificato nell'archivio del computer locale e visualizza più righe a seconda del numero di account di Automazione definiti nella sottoscrizione e del fatto che l'autenticazione abbia o meno esito positivo.  

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>Creazione dei runbook per Hybrid Runbook Workers
Non esiste alcuna differenza nella struttura dei runbook che vengono eseguiti in Automazione di Azure e di quelli eseguiti in Hybrid Runbook Workers. I runbook usati nell'uno o nell'altro caso saranno tuttavia molto diversi perché i runbook per i ruoli di lavoro ibridi per runbook in genere gestiscono le risorse locali nel data center, mentre i runbook in Automazione di Azure in genere gestiscono le risorse nel cloud Azure.

È possibile modificare un runbook per Hybrid Runbook Workers in Automazione di Azure, ma si potrebbero incontrare difficoltà se si tenta di testare il runbook nell'editor.  I moduli di PowerShell che accedono alle risorse locali potrebbero non essere installati nell'ambiente di Automazione di Azure e in questo caso il test avrebbe esito negativo.  Se si sceglie di installare i moduli necessari, il runbook verrà eseguito, ma non sarà in grado di accedere alle risorse locali per un test completo.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Risoluzione dei problemi relativi ai runbook nel ruolo di lavoro ibrido per runbook
[output e i messaggi di runbook](automation-runbook-output-and-messages.md) vengono inviati ad Automazione di Azure da ruoli di lavoro ibridi nello stesso modo in cui vengono eseguiti i processi per runbook nel cloud.  È anche possibile abilitare i flussi Verbose e Progress come per qualsiasi altro runbook.  

I log vengono archiviati localmente in ogni ruolo di lavoro ibrido in C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.

Se i runbook non vengono completati correttamente e il riepilogo del processo visualizza lo stato **Sospeso**, vedere l'articolo sulla risoluzione dei problemi [Ruolo di lavoro ibrido per runbook: un processo runbook termina con lo stato Sospeso](automation-troubleshooting-hrw-runbook-terminates-suspended.md).   

## <a name="relationship-to-service-management-automation"></a>Relazione con Service Management Automation
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) consente di eseguire gli stessi runbook supportati da Automazione di Azure nel data center locale. SMA viene in genere distribuito insieme a Windows Azure Pack, dal momento che Windows Azure Pack contiene un'interfaccia grafica per la gestione di SMA. A differenza di Automazione di Azure, SMA richiede un'installazione locale che include server Web per ospitare l'API, un database per contenere i runbook e la configurazione SMA e ruoli di lavoro per runbook per eseguire i processi del runbook. Automazione di Azure offre questi servizi nel cloud e richiede solo di gestire i computer di lavoro runbook ibridi nell'ambiente locale.

Gli utenti SMA già esistenti possono spostare i runbook in Automazione di Azure affinché vengano usati con Hybrid Runbook Workers senza alcuna modifica, presupponendo che eseguano la propria autenticazione per le risorse come illustrato in [Creazione dei runbook per Hybrid Runbook Workers](#creating-runbooks-for-hybrid-runbook-worker).  I runbook in SMA vengono eseguiti nel contesto dell'account del servizio nel server di lavoro, che può garantire tale autenticazione per i runbook.

Per determinare se per le proprie esigenze sia più opportuno ricorrere ad Automazione di Azure con Hybrid Runbook Workers o a Service Management Automation, è possibile usare i criteri seguenti.

* Se è necessaria un'interfaccia di gestione grafica, SMA richiede un'installazione locale di componenti sottostanti connessi a Windows Azure Pack. Sono necessarie più risorse locali con costi di manutenzione superiori rispetto ad Automazione di Azure, che richiede solo l'installazione di un agente nei ruoli lavoro per runbook locali. Gli agenti vengono gestiti da Operations Management Suite, riducendo ulteriormente i costi di manutenzione.
* Automazione di Azure archivia i runbook nel cloud e li distribuisce ai computer di ruolo di lavoro ibrido per runbook in locale. Se i criteri di sicurezza non consentono questo comportamento, è consigliabile usare SMA.
* SMA è incluso in System Center e richiede pertanto una licenza di System Center 2012 R2. Automazione di Azure si basa su un modello di sottoscrizione a livelli.
* Automazione di Azure offre funzionalità avanzate, tra cui runbook grafici, non disponibili in SMA.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sui vari modi per avviare un runbook, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md).  
* Per comprendere le diverse procedure per l'uso di PowerShell e dei runbook del flusso di lavoro di PowerShell in Automazione di Azure con l'editor di testo, vedere [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md)

