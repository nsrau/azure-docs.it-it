---
title: Risoluzione dei problemi di gestione degli aggiornamenti di Automazione di AzureTroubleshooting Azure Automation Update
description: Informazioni su come risolvere e risolvere i problemi relativi alla soluzione di gestione degli aggiornamenti in Automazione di Azure.Learn how to troubleshoot and resolve issues with the Update Management solution in Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91ecff311b8820d3b97e1de0e4b4e87c150e749b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678930"
---
# <a name="troubleshoot-issues-with-the-update-management-solution"></a>Risolvere i problemi relativi alla soluzione di gestione degli aggiornamentiTroubleshoot issues with the Update Management solution

In questo articolo vengono illustrati i problemi che potrebbero verificarsi quando si utilizza la soluzione di gestione degli aggiornamenti. Esiste uno strumento di risoluzione dei problemi dell'agente per l'agente di lavoro ibrido per runbook per determinare il problema sottostante. Per ulteriori informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi relativi](update-agent-issues.md) agli agenti di aggiornamento di Windows e Risolvere i problemi relativi agli [aggiornare Linux.](update-agent-issues-linux.md) Per altri problemi di onboarding, vedere Risolvere i problemi di [onboarding della soluzione](onboarding.md).

>[!NOTE]
>Se si riscontrano problemi durante l'onboarding della soluzione in una macchina virtuale (VM), controllare il log di **Operations Manager** in Registri applicazioni **e servizi** nel computer locale. Cercare gli eventi con ID evento 4502 e i dettagli dell'evento che contengono `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Scenario: viene visualizzato l'errore "Impossibile attivare la soluzione di aggiornamento"

### <a name="issue"></a>Problema

Quando si tenta di abilitare la soluzione di gestione degli aggiornamenti nell'account di automazione, viene visualizzato il seguente errore:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Le cause di questo errore sono le seguenti:

* I requisiti del firewall di rete per l'agente di Log Analytics potrebbero non essere configurati correttamente. Questa situazione può causare l'errore dell'agente durante la risoluzione degli URL DNS.

* Il targeting della soluzione non è configurato correttamente e il computer non riceve gli aggiornamenti come previsto.

* Si potrebbe anche notare che `Non-compliant` la macchina mostra lo stato di sotto **Conformità**. Allo stesso tempo, **Agent Desktop** Analytics `Disconnected`segnala l'agente come .

### <a name="resolution"></a>Risoluzione

* Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

* Passare a [Pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento di Gestione aggiornamenti.  

* Passare a [Pianificazione della rete](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento dell'agente di Log Analytics.Go to Network planning to learn about which addresses and ports must be allowed for the Log Analytics agent to work.

* Verificare la presenza di problemi di configurazione dell'ambito. [La configurazione dell'ambito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quali computer vengono configurati per la soluzione. Se il computer viene visualizzato nell'area di lavoro, ma non nel portale di gestione di aggiornamento, è necessario impostare la configurazione dell'ambito in modo che sia destinata ai computer. Per informazioni sulla configurazione dell'ambito, vedere [Eseguire l'onboarding dei computer nell'area di lavoro.](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)

* Rimuovere la configurazione del lavoratore seguendo la procedura descritta in Eliminazione del ruolo di lavoro ibrido per [runbook.](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker) 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: aggiornamento sostituito indicato come mancante nella gestione degli aggiornamenti

### <a name="issue"></a>Problema

I vecchi aggiornamenti vengono visualizzati per un account di automazione come mancante anche se sono stati sostituiti. Un aggiornamento sostituito è un aggiornamento che non è necessario installare perché è disponibile un aggiornamento successivo che corregge la stessa vulnerabilità. Gestione aggiornamenti ignora l'aggiornamento sostituito e lo rende non applicabile a favore dell'aggiornamento di sostituzione. Per informazioni su un problema correlato, vedere [Aggiornamento sostituito.](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="cause"></a>Causa

Gli aggiornamenti sostituiti non sono indicati correttamente come rifiutati in modo che possano essere considerati non applicabili.

### <a name="resolution"></a>Risoluzione

Quando un aggiornamento sostituito diventa non applicabile al 100%, è `Declined`necessario modificare lo stato di approvazione di tale aggiornamento in . Per modificare lo stato di approvazione per tutti gli aggiornamenti:

1. Nell'account di automazione selezionare **Gestione aggiornamenti** per visualizzare lo stato del computer. Consultate [Visualizzare le valutazioni degli aggiornamenti.](../manage-update-multi.md#view-an-update-assessment)

2. Controllare l'aggiornamento sostituito per assicurarsi che sia 100% non applicabile. 

3. Contrassegnare l'aggiornamento come rifiutato a meno che non si abbia una domanda sull'aggiornamento. 

4. Selezionare **Computer** e, nella colonna **Conformità,** forzare una rianalisi per la conformità. Consultate [Gestire gli aggiornamenti per più computer.](../manage-update-multi.md)

5. Ripetere i passaggi precedenti per gli altri aggiornamenti sostituiti.

6. Eseguire la pulizia guidata per eliminare i file dagli aggiornamenti rifiutati. 

7. Per Windows Server Update Services (WSUS), pulire manualmente tutti gli aggiornamenti sostituiti per aggiornare l'infrastruttura.

8. Ripetere questa procedura regolarmente per correggere il problema di visualizzazione e ridurre al minimo la quantità di spazio su disco utilizzata per la gestione degli aggiornamenti.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: i computer non vengono visualizzati nel portale in Gestione aggiornamenti

### <a name="issue"></a>Problema

Le macchine presentano i seguenti sintomi:

* Il computer `Not configured` viene visualizzato dalla visualizzazione Gestione aggiornamenti di una macchina virtuale.

* I computer non sono presenti nella visualizzazione Gestione aggiornamenti dell'account di Automazione di Azure.Your machines are missing on the Update Management view of your Azure Automation account.

* Si dispone di `Not assessed` macchine che vengono visualizzate come in **Conformità**. Tuttavia, vengono visualizzati i dati heartbeat nei log di Monitoraggio di Azure per il ruolo di lavoro ibrido per runbook, ma non per la gestione degli aggiornamenti.

### <a name="cause"></a>Causa

Questo problema può essere causato da problemi di configurazione locale o da una configurazione dell'ambito configurata in modo non corretto. Le possibili cause specifiche sono:

* Potrebbe essere necessario registrare nuovamente e reinstallare il ruolo di lavoro ibrido per runbook.

* È possibile che sia stata definita una quota nell'area di lavoro raggiunta e che impedisca un ulteriore spazio di archiviazione dei dati.

### <a name="resolution"></a>Risoluzione

1. Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

2. Assicurarsi che il computer stia segnalando all'area di lavoro corretta. Per istruzioni su come verificare questo aspetto, vedere Verificare la [connettività dell'agente a Log Analytics.](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics) Assicurarsi inoltre che questa area di lavoro sia collegata all'account di Automazione di Azure.Also make sure that this workspace is linked to your Azure Automation account. Per confermare, passare all'account di automazione e selezionare **Area di lavoro collegata** in **Risorse correlate**.

3. Assicurarsi che i computer vengano visualizzati nell'area di lavoro di Log Analytics collegata all'account di automazione. Eseguire la query seguente nell'area di lavoro di Log Analytics.Run the following query in the Log Analytics workspace.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Se il computer non viene visualizzato nei risultati della query, l'utente non è stato archiviato di recente. Probabilmente c'è un problema di configurazione locale ed è necessario [reinstallare l'agente](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). 

5. Se il computer viene visualizzato nei risultati della query, verificare la presenza di problemi di configurazione dell'ambito. [La configurazione dell'ambito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quali computer sono configurati per la soluzione. 

6. Se il computer viene visualizzato nell'area di lavoro ma non in Gestione aggiornamenti, è necessario configurare la configurazione dell'ambito in modo che sia destinata al computer. Per informazioni su come eseguire questa operazione, vedere [I computer di bordo nell'area di lavoro](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

7. Nell'area di lavoro eseguire questa query.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Se si `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` ottiene un risultato, viene raggiunta la quota definita nell'area di lavoro, che ha impedito il salvataggio dei dati. Nell'area di lavoro passare alla **gestione del volume** di dati in Utilizzo e costi **stimati**e modificare o rimuovere la quota.

9. Se il problema persiste, seguire la procedura descritta in Distribuire un ruolo di lavoro ibrido di Windows per reinstallare il ruolo di lavoro ibrido per Windows.If your issue is still unresolved, follow the steps in Deploy a [Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) to reinstall the Hybrid Worker for Windows. Per Linux, seguire i passaggi descritti in Distribuire un ruolo di lavoro [runbook ibrido Linux.](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: impossibile registrare il provider di risorse di automazione per le sottoscrizioniScenario: Unable to register Automation resource provider for subscriptions

### <a name="issue"></a>Problema

Quando si utilizzano soluzioni nell'account di automazione, si verifica il seguente errore:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Causa

Il provider di risorse di automazione non è registrato nella sottoscrizione.

### <a name="resolution"></a>Risoluzione

Per registrare il provider di risorse di automazione, seguire questi passaggi nel portale di Azure.To register the Automation resource provider, follow these steps in the Azure portal.

1. Nell'elenco Servizio di Azure nella parte inferiore del portale selezionare **Tutti i servizi**e quindi **Sottoscrizioni** nel gruppo di servizi Generale.

2. Selezionare la propria sottoscrizione.

3. In **Impostazioni**selezionare **Provider di risorse**.

4. Nell'elenco dei provider di risorse verificare che il provider di risorse Microsoft.Automation sia registrato.

5. Se non è presente nell'elenco, registrare il provider Microsoft.Automation seguendo la procedura descritta in Risolvere gli [errori per la registrazione](/azure/azure-resource-manager/resource-manager-register-provider-errors)del provider di risorse .

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenario: aggiornamento pianificato con una pianificazione dinamica perso alcuni computer

### <a name="issue"></a>Problema

Le macchine incluse in un'anteprima di aggiornamento non vengono tutte visualizzate nell'elenco dei computer sottoposti a patch durante un'esecuzione pianificata.

### <a name="cause"></a>Causa

Questo problema può avere una delle seguenti cause:

* Le sottoscrizioni definite nell'ambito in una query dinamica non sono configurate per il provider di risorse di automazione registrato.

* Le macchine non erano disponibili o non disponevano di tag appropriati al momento dell'esecuzione della pianificazione.

### <a name="resolution"></a>Risoluzione

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Sottoscrizioni non configurate per il provider di risorse di automazione registratoSubscriptions not configured for registered Automation resource provider

Se la sottoscrizione non è configurata per il provider di risorse di automazione, non è possibile eseguire query o recuperare informazioni sui computer in tale sottoscrizione. Utilizzare la procedura seguente per verificare la registrazione per la sottoscrizione.

1. Nel [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)accedere all'elenco dei servizi di Azure.In the Azure portal , access the Azure service list.

2. Selezionare **Tutti i servizi**, quindi **Sottoscrizioni** nel gruppo di servizi Generale. 

3. Trovare la sottoscrizione definita nell'ambito della distribuzione.

4. In **Impostazioni**scegliere **Provider di risorse**.

5. Verificare che il provider di risorse Microsoft.Automation sia registrato.

6. Se non è presente nell'elenco, registrare il provider Microsoft.Automation seguendo la procedura descritta in Risolvere gli [errori per la registrazione](/azure/azure-resource-manager/resource-manager-register-provider-errors)del provider di risorse .

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Macchine non disponibili o non contrassegnate correttamente durante l'esecuzione della pianificazione

Utilizzare la procedura seguente se la sottoscrizione è configurata per il provider di risorse di automazione, ma l'esecuzione della pianificazione dell'aggiornamento con i [gruppi dinamici](../automation-update-management-groups.md) specificati ha perso alcuni computer.

1. Nel portale di Azure aprire l'account di automazione e selezionare **Gestione aggiornamenti**.

2. Controllare la [cronologia di gestione](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) degli aggiornamenti per determinare l'ora esatta in cui è stata eseguita la distribuzione dell'aggiornamento. 

3. Per i computer che si sospetta siano stati persi da Gestione aggiornamenti, usare Azure Resource Graph (ARG) per [individuare le modifiche.](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details) 

4. Cercare le modifiche in un periodo considerevole, ad esempio un giorno, prima dell'esecuzione della distribuzione degli aggiornamenti.

5. Controllare i risultati della ricerca per eventuali modifiche di sistema, ad esempio le modifiche di eliminazione o aggiornamento, alle macchine in questo periodo. Queste modifiche possono modificare lo stato o i tag del computer in modo che i computer non siano selezionati nell'elenco dei computer quando vengono distribuiti gli aggiornamenti.

6. Modificare le impostazioni delle macchine e delle risorse in base alle esigenze per correggere i problemi relativi allo stato della macchina o ai tag.

7. Eseguire nuovamente la pianificazione degli aggiornamenti per assicurarsi che la distribuzione con i gruppi dinamici specificati includa tutti i computer.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scenario: i computer previsti non vengono visualizzati nell'anteprima per il gruppo dinamicoScenario: Expected machines don't appear in preview for dynamic group

### <a name="issue"></a>Problema

Le macchine virtuali per gli ambiti selezionati di un gruppo dinamico non vengono visualizzate nell'elenco di anteprima del portale di Azure.Virtual machines for selected scopes of a dynamic group are not showing up in the Azure portal preview list. Questo elenco è costituito da tutti i computer recuperati da una query ARG per gli ambiti selezionati. Gli ambiti vengono filtrati per i computer in cui sono installati i ruoli di lavoro ibridi Runbook e per i quali si dispone delle autorizzazioni di accesso. 

### <a name="cause"></a>Causa
 
Di seguito sono riportate le possibili cause di questo problema:

* Non si dispone dell'accesso corretto sugli ambiti selezionati.
* La query ARG non recupera i computer previsti.
* Hybrid Runbook Worker non è installato nei computer.

### <a name="resolution"></a>Risoluzione 

#### <a name="incorrect-access-on-selected-scopes"></a>Accesso non corretto agli ambiti selezionati

Il portale di Azure visualizza solo i computer per i quali si dispone dell'accesso in scrittura in un determinato ambito. Se non si dispone dell'accesso corretto per un ambito, vedere [Esercitazione: Concedere a un utente l'accesso alle risorse](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)di Azure tramite RBAC e il portale di Azure.If you don't have the correct access for a scope, see Tutorial: Grant a user access to Azure resources using RBAC and the Azure portal .

#### <a name="arg-query-doesnt-return-expected-machines"></a>La query ARG non restituisce i computer previsti

Attenersi alla procedura seguente per scoprire se le query funzionano correttamente.

1. Eseguire una query ARG formattata come illustrato di seguito nel pannello Esplora diagramma risorse nel portale di Azure.Run an ARG query formatted as shown below in the Resource Graph explorer blade in Azure portal. Questa query simula i filtri selezionati al momento della creazione del gruppo dinamico in Gestione aggiornamenti. Consultate [Utilizzare gruppi dinamici con Gestione aggiornamenti.](https://docs.microsoft.com/azure/automation/automation-update-management-groups) 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Esempio:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. Verificare se i computer che si sta cercando sono elencati nei risultati della query. 

3. Se le macchine non sono elencate, probabilmente si è verificato un problema con il filtro selezionato nel gruppo dinamico. Regolare la configurazione del gruppo in base alle esigenze.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker non installato nei computer

I computer vengono visualizzati nei risultati delle query ARG, ma non vengono ancora visualizzati nell'anteprima del gruppo dinamico. In questo caso, i computer potrebbero non essere designati come lavoratori ibridi e pertanto non possono eseguire i processi di automazione e gestione degli aggiornamenti di Azure.In this case, the machines might not be designated as hybrid workers and thus can't run Azure Automation and Update Management jobs. Per assicurarsi che i computer previsti siano impostati come ruoli di lavoro per runbook ibridi:

1. Nel portale di Azure passare all'account di automazione per un computer che non viene visualizzato correttamente.

2. Selezionare **Gruppi di ruoli di lavoro ibridi** in **Automazione processo**.

3. Selezionare la scheda **Gruppi di lavoratori ibridi** di sistema.

4. Verificare che il lavoratore ibrido sia presente per il computer.

5. Se la macchina non è configurata come lavoratore ibrido, apportare modifiche utilizzando le istruzioni in Automatizzare le [risorse nel data center o nel cloud utilizzando Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker).

6. Aggiungere il computer al gruppo Hybrid Runbook Worker.

7. Ripetere i passaggi precedenti per tutte le macchine che non sono state visualizzate nell'anteprima.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: componenti per la soluzione di gestione degli aggiornamenti abilitati, mentre la macchina virtuale continua a essere visualizzata come configurataScenario: Components for Update Management solution enabled, while VM continues to show as being configured

### <a name="issue"></a>Problema

Nella macchina virtuale continua a essere visualizzato il messaggio seguente 15 minuti dopo l'onboarding:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Le cause di questo errore sono le seguenti:

* La comunicazione con l'account di automazione è bloccata.

* Esiste un nome computer duplicato con ID computer di origine diversi. Questo scenario si verifica quando una macchina virtuale con un nome di computer specifico viene creata in gruppi di risorse diversi e segnala la stessa area di lavoro dell'agente logistico nella sottoscrizione.

* L'immagine della macchina virtuale in fase di onboarding potrebbe provenire da un computer clonato che non è stato preparato con preparazione del sistema (sysprep) con l'agente di Log Analytics per Windows installato.

### <a name="resolution"></a>Risoluzione

Per determinare il problema esatto della macchina virtuale, eseguire la query seguente nell'area di lavoro di Log Analytics collegata all'account di Automazione.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Comunicazione con l'account Automation bloccata

Passare a [Pianificazione della rete](../automation-update-management.md#ports) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento di Gestione aggiornamenti.

#### <a name="duplicate-computer-name"></a>Nome computer duplicato

Rinominare le macchine virtuali per garantire nomi univoci nel proprio ambiente.

#### <a name="onboarded-image-from-cloned-machine"></a>Immagine onboarding dalla macchina clonata

Se si utilizza un'immagine clonata, nomi di computer diversi hanno lo stesso ID computer di origine. In questo caso:

1. Nell'area di lavoro di Log Analytics rimuovere `MicrosoftDefaultScopeConfig-Updates` la macchina virtuale dalla ricerca salvata per la configurazione dell'ambito, se visualizzata. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.

2. Eseguire il cmdlet seguente.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Eseguire `Restart-Service HealthService` per riavviare il servizio integrità. Questa operazione ricrea la chiave e genera un nuovo UUID.

4. Se questo approccio non funziona, eseguire Sysprep sull'immagine prima e quindi installare l'MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: viene visualizzato un errore di sottoscrizione collegata quando si crea una distribuzione di aggiornamento per i computer in un altro tenant di AzureScenario: You receive a linked subscription error when you create an update deployment for machines in another Azure tenant

### <a name="issue"></a>Problema

Si verifica il seguente errore quando si tenta di creare una distribuzione di aggiornamento per i computer in un altro tenant di Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando si crea una distribuzione di aggiornamento con macchine virtuali di Azure in un altro tenant incluso in una distribuzione di aggiornamento.

### <a name="resolution"></a>Risoluzione

Utilizzare la soluzione seguente per visualizzare questi elementi pianificati. È possibile utilizzare il cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) con il `ForUpdateConfiguration` parametro per creare una pianificazione. Quindi, utilizzare il cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passare i `NonAzureComputer` computer nell'altro tenant al parametro. L'esempio seguente illustra come farlo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: riavvii inspiegabili

### <a name="issue"></a>Problema

Anche se è stata impostata l'opzione **Controllo riavvio su** **Non riavviare mai**, i computer vengono comunque riavviati dopo l'installazione degli aggiornamenti.

### <a name="cause"></a>Causa

Windows Update può essere modificato da diverse chiavi del Registro di sistema, ognuna delle quali può modificare il comportamento di riavvio.

### <a name="resolution"></a>Risoluzione

Esaminare le chiavi del Registro di sistema elencate in Configurazione di [Aggiornamenti automatici modificando il Registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) di sistema e [le chiavi del Registro](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) di sistema utilizzate per gestire il riavvio per assicurarsi che i computer siano configurati correttamente.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: computer viene visualizzato "Impossibile avviare" in una distribuzione di aggiornamento

### <a name="issue"></a>Problema

Una macchina `Failed to start` mostra uno stato. Quando si visualizzano i dettagli specifici per la macchina, viene visualizzato il seguente errore:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per uno dei motivi seguenti:

* La macchina non esiste più.
* La macchina è spenta e irraggiungibile.
* La macchina presenta un problema di connettività di rete e pertanto il ruolo di lavoro ibrido nel computer non è raggiungibile.
* Si è verificato un aggiornamento per mMA che ha modificato l'ID del computer di origine.
* L'esecuzione dell'aggiornamento è stata limitata se si raggiunge il limite di 2000 processi simultanei in un account di automazione. Ogni distribuzione è considerata un processo e ogni computer in una distribuzione di aggiornamento viene considerato come processo. Qualsiasi altro processo di automazione o distribuzione di aggiornamento attualmente in esecuzione nell'account di automazione viene conteggiato per il limite di processi simultanei.

### <a name="resolution"></a>Risoluzione

Se applicabile, utilizzare [i gruppi dinamici](../automation-update-management-groups.md) per le distribuzioni degli aggiornamenti. Inoltre, è possibile effettuare le seguenti operazioni.

1. Verificare che il computer esista e sia ancora raggiungibile. 
2. Se il computer non esiste, modificare la distribuzione e rimuoverla.
3. Vedere la sezione [Pianificazione della rete](../automation-update-management.md#ports) per un elenco delle porte e degli indirizzi necessari per la gestione degli aggiornamenti e quindi verificare che il computer soddisfi questi requisiti.
4. Verificare la connettività al ruolo di lavoro ibrido per runbook usando lo strumento di risoluzione dei problemi dell'agente di lavoro ibrido per runbook. Per altre informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi dell'agente di aggiornamento](update-agent-issues.md).
5. Eseguire la query seguente in Log Analytics per trovare i computer nell'ambiente per cui è stato modificato l'ID del computer di origine. Cercare i computer che `Computer` hanno lo `SourceComputerId` stesso valore ma un valore diverso.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Dopo aver trovato i computer interessati, modificare le distribuzioni di aggiornamento `SourceComputerId` destinate a tali computer e quindi rimuoverli e aggiungerli nuovamente in modo che rifletta il valore corretto.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: gli aggiornamenti vengono installati senza una distribuzioneScenario: Updates are installed without a deployment

### <a name="issue"></a>Problema

Quando si registra un computer Windows in Gestione aggiornamenti, vengono visualizzati gli aggiornamenti installati senza una distribuzione.

### <a name="cause"></a>Causa

In Windows, gli aggiornamenti vengono installati automaticamente non appena sono disponibili. Questo comportamento può causare confusione se non è stato pianificato un aggiornamento da distribuire al computer.

### <a name="resolution"></a>Risoluzione

Il `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` valore predefinito della chiave del `auto download and install`Registro di sistema è l'impostazione 4: .

Per i client di gestione degli aggiornamenti, è consigliabile impostare questa chiave su 3: `auto download but do not auto install`.

Per ulteriori informazioni, vedere [Configurazione di Aggiornamenti automatici](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: Il computer è già registrato per un altro account

### <a name="issue"></a>Problema

Viene visualizzato il messaggio di errore seguente:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

La macchina è già stata caricata in un'altra area di lavoro per la gestione degli aggiornamenti.

### <a name="resolution"></a>Risoluzione

1. Seguire i passaggi descritti in [I computer non vengono visualizzati nel portale in Gestione aggiornamenti](#nologs) per assicurarsi che il computer segnali all'area di lavoro corretta.
2. Pulire gli elementi nel computer eliminando il gruppo ibrido di [runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), quindi riprovare.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: la macchina non può comunicare con il servizio

### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Causa

Un proxy, un gateway o un firewall potrebbe bloccare la comunicazione di rete. 

### <a name="resolution"></a>Risoluzione

Esaminare la rete e assicurarsi che le porte e gli indirizzi appropriati siano consentiti. Vedere [i requisiti](../automation-hybrid-runbook-worker.md#network-planning) di rete per un elenco delle porte e degli indirizzi richiesti da Update Management e Hybrid Runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: Impossibile creare un certificato autofirmato

### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per runbook non è in stato di generazione di un certificato autofirmato.

### <a name="resolution"></a>Risoluzione

Verificare che l'account di sistema disponga dell'accesso in lettura alla cartella **C:**

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: l'aggiornamento pianificato non è riuscito con un errore MaintenanceWindowExceeded

### <a name="issue"></a>Problema

La finestra di manutenzione predefinita per gli aggiornamenti è 120 minuti. È possibile aumentare la finestra di manutenzione a un massimo di 6 ore o 360 minuti.

### <a name="resolution"></a>Risoluzione

Modificare eventuali distribuzioni di aggiornamenti pianificate non riuscite e aumentare la finestra di manutenzione.

Per ulteriori informazioni sulle finestre di manutenzione, vedere [Installare gli aggiornamenti](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: il computer viene visualizzato come "Non valutato" e mostra un'eccezione HRESULT

### <a name="issue"></a>Problema

* Sono presenti computer `Not assessed` che vengono visualizzati come in **Conformità**e viene visualizzato un messaggio di eccezione sotto di essi.
* Viene visualizzato un codice di errore HRESULT nel portale.

### <a name="cause"></a>Causa

L'agente di aggiornamento (agente di Windows Update in Windows; il gestore di pacchetti per una distribuzione Linux) non è configurato correttamente. Gestione aggiornamenti si basa sull'agente di aggiornamento del computer per fornire gli aggiornamenti necessari, lo stato della patch e i risultati delle patch distribuite. Senza queste informazioni, Gestione aggiornamenti non è in grado di creare correttamente report sulle patch necessarie o installate.

### <a name="resolution"></a>Risoluzione

Provare a eseguire gli aggiornamenti in locale nel computer. Se questa operazione non riesce, significa in genere che si è verificato un errore di configurazione dell'agente di aggiornamento.

Questo problema è spesso causato da problemi di configurazione di rete e firewall. Utilizzare i seguenti controlli per correggere il problema.

* Per Linux, consultare la documentazione appropriata per assicurarsi di poter raggiungere l'endpoint di rete del repository dei pacchetti.

* Per Windows, controllare la configurazione dell'agente come elencato in [Gli aggiornamenti non vengono scaricati dall'endpoint Intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Se i computer sono configurati per Windows Update, assicurarsi che sia possibile raggiungere gli endpoint descritti in [Problemi relativi a HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se i computer sono configurati per Windows Server Update Services (WSUS), assicurarsi che sia possibile raggiungere il server WSUS configurato dalla [chiave del Registro](/windows/deployment/update/waas-wu-settings)di sistema WUServer .

Se viene visualizzato un HRESULT, fare doppio clic sull'eccezione visualizzata in rosso per visualizzare l'intero messaggio di eccezione. Esaminare la tabella seguente per le soluzioni potenziali o le azioni consigliate.

|Eccezione  |Risoluzione o azione  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Cercare il codice di errore pertinente nell'elenco dei codici di errore di [Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) per trovare ulteriori dettagli sulla causa dell'eccezione.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Questi indicano problemi di connettività di rete. Assicurarsi che il computer disponga di connettività di rete a Gestione aggiornamenti. Vedere la sezione [Pianificazione della rete](../automation-update-management.md#ports) per un elenco delle porte e degli indirizzi necessari.        |
|`0x8024001E`| L'operazione di aggiornamento non è stata completata perché il servizio o il sistema è stato arrestato.|
|`0x8024002E`| Il servizio Windows Update è disabilitato.|
|`0x8024402C`     | Se si utilizza un server WSUS, assicurarsi `WUServer` `WUStatusServer` che `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` i valori del Registro di sistema per e sotto la chiave del Registro di sistema specificate il server WSUS corretto.        |
|`0x80072EE2`|Si è verificato un problema di connettività di rete o un problema nel comunicare con un server WSUS configurato. Controllare le impostazioni WSUS e assicurarsi che il servizio sia accessibile dal client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Assicurarsi che il servizio Windows Update (wuauserv) sia in esecuzione e non disabilitato.        |
|`0x80070005`| Un errore di accesso negato può essere causato da uno dei seguenti:<br> Computer infetto<br> Impostazioni di Windows Update non configurate correttamente<br> Errore di autorizzazione del file con la cartella %WinDir%/SoftwareDistribution<br> Spazio su disco insufficiente nell'unità di sistema (C:).
|Qualsiasi altra eccezione generica     | Eseguire una ricerca su Internet per le possibili soluzioni e lavorare con il supporto IT locale.         |

Anche la revisione del file **%Windir%/Windowsupdate.log** consente di determinare le possibili cause. Per ulteriori informazioni su come leggere il registro, vedere [Come leggere il file Windowsupdate.log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

È inoltre possibile scaricare ed eseguire lo strumento di risoluzione dei problemi di [Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) per verificare la presenza di eventuali problemi con Windows Update nel computer.

> [!NOTE]
> La documentazione per lo strumento di risoluzione dei problemi di [Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica che è utilizzabile nei client Windows, ma funziona anche su Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: l'esecuzione dell'aggiornamento restituisce lo stato Non riuscito (Linux)Scenario: Update run returns Failed status (Linux)

### <a name="issue"></a>Problema

Un'esecuzione dell'aggiornamento viene avviata ma rileva errori durante l'esecuzione.

### <a name="cause"></a>Causa

Possibili cause:

* Il gestore di pacchetti non è integro.
* Update Agent (WUA per Windows, gestore di pacchetti specifico per Linux) non è configurato correttamente.
* Pacchetti specifici interferiscono con l'applicazione di patch basata su cloud.
* La macchina è irraggiungibile.
* Gli aggiornamenti avevano dipendenze che non sono state risolte.

### <a name="resolution"></a>Risoluzione

Se si verificano errori durante l'esecuzione di un aggiornamento dopo l'avvio, [controllare l'output del processo](../manage-update-multi.md#view-results-of-an-update-deployment) dal computer interessato durante l'esecuzione. È possibile trovare messaggi di errore specifici dai computer che è possibile ricercare e intervenire. Gestione aggiornamenti richiede che la gestione pacchetti sia integra affinché le distribuzioni degli aggiornamenti abbiano esito positivo.

Se patch, pacchetti o aggiornamenti specifici vengono visualizzati immediatamente prima che il processo non riesca, è possibile provare [a escludere](../automation-tutorial-update-management.md#schedule-an-update-deployment) questi elementi dalla distribuzione dell'aggiornamento successivo. Per raccogliere informazioni di registro da Windows Update, vedere File di [registro](/windows/deployment/update/windows-update-logs)di Windows Update .

Se non è possibile risolvere un problema di applicazione di patch, creare una copia del file **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** e conservarlo a scopo di risoluzione dei problemi prima dell'avvio della distribuzione dell'aggiornamento successivo.

## <a name="patches-arent-installed"></a>Le patch non sono installate

### <a name="machines-dont-install-updates"></a>I computer non installano gli aggiornamenti

Provare a eseguire gli aggiornamenti direttamente nel computer. Se il computer non è in grado di applicare gli aggiornamenti, consultare [l'elenco dei potenziali errori nella guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Se gli aggiornamenti vengono eseguiti localmente, provare a rimuovere e reinstallare l'agente nel computer seguendo le indicazioni fornite in [Remove a VM from Update Management](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>So che gli aggiornamenti sono disponibili, ma non vengono visualizzati come disponibili sui miei computer

Ciò si verifica spesso se i computer sono configurati per ottenere gli aggiornamenti da WSUS o Microsoft Endpoint Configuration Manager ma WSUS e Configuration Manager non hanno approvato gli aggiornamenti.

È possibile verificare se i computer sono configurati per WSUS e `UseWUServer` SCCM facendo riferimento incrociato alla chiave del Registro di sistema alle chiavi del Registro di sistema nella sezione Configurazione di [Aggiornamenti automatici modificando il Registro di sistema di questo articolo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se gli aggiornamenti non vengono approvati in WSUS, non vengono installati. È possibile verificare la disponibilità di aggiornamenti non approvati in Log Analytics eseguendo la query seguente.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Gli aggiornamenti vengono visualizzati come installati, ma non è possibile trovarli sul computer

Spesso gli aggiornamenti vengono sostituiti da altri aggiornamenti. Per ulteriori informazioni, vedere [aggiornamento viene sostituito](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) nella guida alla risoluzione dei problemi di Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Installazione degli aggiornamenti in base alla classificazione in Linux

La distribuzione degli aggiornamenti in Linux in base alla classificazione ("Aggiornamenti critici e della sicurezza") presenta avvertimenti importanti, in particolare per CentOS. Queste limitazioni sono documentate nella [pagina Panoramica](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)di Gestione aggiornamenti .

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 è costantemente mancante

KB2267602 è l'[aggiornamento delle definizioni di Windows Defender](https://www.microsoft.com/wdsi/definitions). Viene aggiornato ogni giorno.

## <a name="next-steps"></a>Passaggi successivi

Se non vedi il problema o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza.

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
