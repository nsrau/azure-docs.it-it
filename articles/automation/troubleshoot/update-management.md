---
title: Risolvere i problemi relativi a Gestione aggiornamenti di Automazione di Azure
description: Questo articolo mostra come risolvere i problemi relativi alla Gestione aggiornamenti di Automazione di Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 35049e148af09376667a55e2f0bb4a28cf728245
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83735889"
---
# <a name="troubleshoot-update-management-issues"></a>Risolvere i problemi relativi a Gestione aggiornamenti

Questo articolo descrive i problemi che potrebbero verificarsi durante la distribuzione della funzionalità Gestione aggiornamenti nei computer. È disponibile uno strumento di risoluzione dei problemi dell'agente che consente all'agente del ruolo di lavoro ibrido per runbook di determinare il problema sottostante. Per altre informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi relativi all'agente di aggiornamento Windows](update-agent-issues.md) e [Risolvere i problemi relativi all'agente di aggiornamento Linux](update-agent-issues-linux.md). Per altri problemi relativi alla distribuzione di funzionalità, vedere [Risolvere i problemi relativi alla distribuzione di funzionalità](onboarding.md).

>[!NOTE]
>Se si verificano problemi durante la distribuzione di Gestione aggiornamenti in una macchina virtuale, controllare il registro di **Operations Manager** in **Registri applicazioni e servizi** nel computer locale. Cercare gli eventi con ID 4502 e dettagli evento che contengono `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Scenario: viene visualizzato l'errore "Non è stato possibile abilitare la soluzione Aggiornamento"

### <a name="issue"></a>Problema

Quando si prova ad abilitare Gestione aggiornamenti nell'account Automazione compare l'errore seguente:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Le cause di questo errore sono le seguenti:

* I requisiti del firewall di rete per l'agente di Log Analytics potrebbero non essere configurati correttamente. Questa situazione può causare un errore dell'agente durante la risoluzione degli URL DNS.

* La destinazione di Gestione aggiornamenti non è configurata correttamente e il computer non riceve gli aggiornamenti come previsto.

* È anche possibile notare che il computer mostra lo stato `Non-compliant` in **Conformità**. Allo stesso tempo, **Agente Desktop Analytics** segnala l'agente come `Disconnected`.

### <a name="resolution"></a>Risoluzione

* Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

* Passare a [Configurazione di rete](../automation-hybrid-runbook-worker.md#network-planning) per informazioni sugli indirizzi e sulle porte che devono essere consentiti per il funzionamento di Gestione aggiornamenti.  

* Passare a [Configurazione di rete](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) per informazioni sugli indirizzi e sulle porte che devono essere consentiti per il funzionamento dell'agente di Log Analytics.

* Verificare la presenza di problemi di configurazione dell'ambito. [Configurazione ambito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quali computer sono configurati per Gestione aggiornamenti. Se il computer viene visualizzato nell'area di lavoro ma non nel portale Gestione aggiornamenti, è necessario impostare la configurazione dell'ambito per usare i computer come destinazione. Per informazioni sulla configurazione dell'ambito, vedere [Abilitare i computer nell'area di lavoro](../automation-onboard-solutions-from-automation-account.md#enable-machines-in-the-workspace).

* Rimuovere la configurazione del ruolo di lavoro attenendosi ai passaggi descritti in [Rimuovere il ruolo di lavoro ibrido per runbook da un computer Windows locale](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) oppure [Rimuovere il ruolo di lavoro ibrido per runbook da un computer Linux locale](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: aggiornamento sostituito indicato come mancante in Gestione aggiornamenti

### <a name="issue"></a>Problema

Gli aggiornamenti precedenti vengono visualizzati come mancanti per un account di Automazione anche se sono stati sostituiti. Non è necessario installare un aggiornamento sostituito perché è disponibile un aggiornamento successivo che corregge la stessa vulnerabilità. Gestione aggiornamenti ignora l'aggiornamento sostituito e lo rende non disponibile a favore di quello sostitutivo. Per informazioni su un problema correlato, vedere [L'aggiornamento viene sostituito](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Causa

Gli aggiornamenti sostituiti non vengono indicati correttamente come rifiutati, in modo da essere considerati non disponibili.

### <a name="resolution"></a>Risoluzione

Quando un aggiornamento sostituito diventa non disponibile al 100%, è necessario modificare lo stato di approvazione di tale aggiornamento in `Declined`. Per modificare lo stato di approvazione di tutti gli aggiornamenti:

1. Selezionare **Gestione aggiornamenti** nell'account di Automazione per visualizzare lo stato del computer. Vedere [Visualizzare la valutazione degli aggiornamenti](../manage-update-multi.md#view-an-update-assessment).

2. Controllare l'aggiornamento sostituito per assicurarsi che sia non disponibile al 100%. 

3. Contrassegnare l'aggiornamento come rifiutato, a meno che non si abbiano domande sull'aggiornamento. 

4. Selezionare **Computer** e nella colonna **Conformità** forzare la ripetizione dell'analisi per la conformità. Vedere [Gestire gli aggiornamenti per più macchine virtuali](../manage-update-multi.md).

5. Ripetere i passaggi precedenti per gli altri aggiornamenti sostituiti.

6. Eseguire la pulizia guidata per eliminare i file dagli aggiornamenti rifiutati. 

7. Per Windows Server Update Services (WSUS), eliminare manualmente tutti gli aggiornamenti sostituiti per aggiornare l'infrastruttura.

8. Ripetere questa procedura regolarmente per correggere il problema di visualizzazione e ridurre al minimo la quantità di spazio su disco usata per la gestione aggiornamenti.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: i computer non vengono visualizzati nel portale in Gestione aggiornamenti

### <a name="issue"></a>Problema

I computer presentano i sintomi seguenti:

* Il computer mostra `Not configured` dalla visualizzazione Gestione aggiornamenti di una macchina virtuale.

* I computer non sono presenti nella visualizzazione Gestione aggiornamenti dell'account di Automazione di Azure.

* Sono presenti computer visualizzati come `Not assessed` in **Conformità**. Tuttavia, vengono visualizzati i dati heartbeat nei log di Monitoraggio di Azure per il ruolo di lavoro ibrido per runbook ma non per Gestione aggiornamenti.

### <a name="cause"></a>Causa

Questo problema può essere causato da anomalie relative alla configurazione locale o da una configurazione non corretta dell'ambito. Possibili cause specifiche:

* Potrebbe essere necessario registrare nuovamente e reinstallare il ruolo di lavoro ibrido per runbook.

* È possibile che sia stata definita una quota nell'area di lavoro che è stata raggiunta e che impedisce l'archiviazione di altri dati.

### <a name="resolution"></a>Risoluzione

1. Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

2. Controllare che il computer invii report all'area di lavoro corretta. Per indicazioni su come verificare questo aspetto, vedere [Verificare la connettività dell'agente per Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Assicurarsi anche che l'area di lavoro sia collegata all'account di Automazione di Azure. Per confermare, passare all'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate**.

3. Assicurarsi che i computer siano visualizzati nell'area di lavoro Log Analytics collegata all'account di Automazione. Eseguire la query seguente nell'area di lavoro Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Se il computer non viene visualizzato nei risultati della query, non è stato sincronizzato di recente. Probabilmente si è verificato un problema di configurazione locale ed è necessario [reinstallare l'agente](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). 

5. Se il computer viene visualizzato nei risultati della query, verificare la presenza di problemi di configurazione dell'ambito. La [configurazione ambito](../automation-scope-configurations-update-management.md) determina quali computer sono configurati per Gestione aggiornamenti. 

6. Se il computer viene visualizzato nell'area di lavoro ma non in Gestione aggiornamenti, è necessario impostare la configurazione dell'ambito per usare i computer come destinazione. Per informazioni su come eseguire questa operazione, vedere [Abilitare i computer nell'area di lavoro](../automation-onboard-solutions-from-automation-account.md#enable-machines-in-the-workspace).

7. Eseguire la query nell'area di lavoro.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Se si ottiene il risultato `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, vuol dire che è stata raggiunta la quota definita nell'area di lavoro, interrompendo il salvataggio dei dati. Nell'area di lavoro passare a **gestione del volume dati** in **Utilizzo e costi stimati** e modificare o rimuovere la quota.

9. Se il problema persiste, seguire i passaggi descritti in [Distribuire un ruolo di lavoro ibrido per runbook di Windows](../automation-windows-hrw-install.md) per reinstallare il ruolo di lavoro ibrido per Windows. Per Linux, seguire i passaggi descritti in [Distribuire un ruolo di lavoro ibrido per runbook di Linux](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: impossibile registrare il provider di risorse di Automazione per le sottoscrizioni

### <a name="issue"></a>Problema

Quando si lavora con le distribuzioni di funzionalità nell'account di Automazione, si verifica l'errore seguente:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Causa

Il provider di risorse di Automazione non è registrato nella sottoscrizione.

### <a name="resolution"></a>Risoluzione

Per registrare il provider di risorse di Automazione, seguire questi passaggi nel portale di Azure.

1. Nell'elenco dei servizi di Azure in fondo al portale selezionare **Tutti i servizi** e quindi **Sottoscrizioni** nel gruppo di servizi Generale.

2. Selezionare la propria sottoscrizione.

3. In **Impostazioni** selezionare **Provider di risorse**.

4. Dall'elenco dei provider di risorse, verificare che il provider di risorse Microsoft.Automation sia registrato.

5. Se non è elencato, registrare il provider Microsoft.Automation seguendo i passaggi descritti in [Risolvere gli errori per la registrazione del provider di risorse](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenario: l'aggiornamento con pianificazione dinamica ha escluso alcuni computer

### <a name="issue"></a>Problema

I computer inclusi nell'anteprima di aggiornamento non vengono visualizzati nell'elenco dei computer con patch installate durante un'esecuzione pianificata.

### <a name="cause"></a>Causa

Le cause di questo problema possono essere le seguenti:

* Le sottoscrizioni definite nell'ambito di una query dinamica non sono configurate per il provider di risorse di Automazione registrato.

* I computer non erano disponibili o non avevano i tag appropriati quando è stata eseguita la pianificazione.

### <a name="resolution"></a>Risoluzione

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Sottoscrizioni non configurate per il provider di risorse di Automazione registrato

Se la sottoscrizione non è configurata per il provider di risorse di Automazione, non è possibile eseguire query o recuperare informazioni sui computer nella sottoscrizione. Usare i passaggi seguenti per verificare la registrazione per la sottoscrizione.

1. Nel [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal), accedere all'elenco dei servizi di Azure.

2. Selezionare **Tutti i servizi** e quindi **Sottoscrizioni** nel gruppo di servizi Generale. 

3. Trovare la sottoscrizione definita nell'ambito della distribuzione.

4. In **Impostazioni**, scegliere **Provider di risorse**.

5. Verificare che il provider di risorse Microsoft.Automation sia registrato.

6. Se non è elencato, registrare il provider Microsoft.Automation seguendo i passaggi descritti in [Risolvere gli errori per la registrazione del provider di risorse](/azure/azure-resource-manager/resource-manager-register-provider-errors).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Computer non disponibili o non contrassegnati correttamente quando viene eseguita la pianificazione

Usare la procedura seguente se la sottoscrizione è configurata per il provider di risorse di Automazione, ma l'esecuzione della pianificazione degli aggiornamenti con i [gruppi dinamici](../automation-update-management-groups.md) specificati ha escluso alcuni computer.

1. Nel portale di Azure aprire l'account di Automazione e selezionare **Gestione aggiornamenti**.

2. Controllare [Cronologia Gestione aggiornamenti](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) per determinare l'orario esatto in cui è stata eseguita la distribuzione degli aggiornamenti. 

3. Per i computer che si sospetta siano stati esclusi da Gestione aggiornamenti, usare Azure Resource Graph (ARG) per [individuare le modifiche del computer](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details). 

4. Cercare le modifiche apportate in un periodo di tempo considerevole, ad esempio un giorno, prima dell'esecuzione della distribuzione degli aggiornamenti.

5. Verificare nei risultati della ricerca la presenza di eventuali modifiche sistematiche, ad esempio quelle di eliminazione o aggiornamento, apportate ai computer in questo periodo. Queste modifiche possono modificare lo stato o i tag dei computer in modo che non siano selezionati nell'elenco dei computer quando vengono distribuiti gli aggiornamenti.

6. Modificare le impostazioni dei computer e delle risorse in modo da correggerne lo stato o i problemi relativi ai tag.

7. Eseguire di nuovo la pianificazione degli aggiornamenti per assicurarsi che la distribuzione con i gruppi dinamici specificati includa tutti i computer.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scenario: i computer previsti non sono visualizzati in anteprima per il gruppo dinamico

### <a name="issue"></a>Problema

Le macchine virtuali per gli ambiti selezionati di un gruppo dinamico non vengono visualizzate nell'elenco di anteprima del portale di Azure. In questo elenco sono presenti tutti i computer recuperati da una query ARG per gli ambiti selezionati. Gli ambiti vengono filtrati per i computer in cui sono installati i ruoli di lavoro ibridi per runbook e per i quali si dispone di autorizzazioni di accesso. 

### <a name="cause"></a>Causa
 
Possibili cause di questo problema:

* Non si dispone dell'accesso corretto agli ambiti selezionati.
* La query ARG non recupera i computer previsti.
* Il ruolo di lavoro ibrido per runbook non è installato nei computer.

### <a name="resolution"></a>Risoluzione 

#### <a name="incorrect-access-on-selected-scopes"></a>Accesso errato agli ambiti selezionati

Nel portale di Azure vengono visualizzati solo i computer per i quali si dispone dell'accesso in scrittura in un ambito specifico. Se non si dispone dell'accesso corretto per un ambito, vedere [Esercitazione: Concedere l'accesso alle risorse di Azure a un utente usando il controllo degli accessi in base al ruolo e il portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>La query ARG non restituisce i computer previsti

Attenersi ai passaggi seguenti per determinare se le query funzionano correttamente.

1. Eseguire una query ARG formattata come illustrato qui sotto nel pannello Resource Graph explorer nel portale di Azure. Questa query simula i filtri selezionati al momento della creazione del gruppo dinamico in Gestione aggiornamenti. Vedere [Usare i gruppi dinamici con Gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

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
 
2. Controllare che i computer desiderati siano elencati nei risultati della query. 

3. Se i computer non sono elencati, probabilmente si è verificato un problema con il filtro selezionato nel gruppo dinamico. Apportare le modifiche necessarie alla configurazione del gruppo.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Ruolo di lavoro ibrido per runbook non installato nei computer

I computer appaiono nei risultati delle query ARG ma non vengono ancora visualizzati nell'anteprima del gruppo dinamico. In questo caso, è possibile che i computer non siano designati come ruoli di lavoro ibridi e che quindi non possano eseguire i processi di Automazione di Azure e Gestione aggiornamenti. Per assicurarsi che i computer che si prevede di visualizzare siano configurati come ruoli di lavoro ibridi per runbook:

1. Nel portale di Azure, passare all'account di Automazione per un computer che non viene visualizzato correttamente.

2. Selezionare **Gruppi di ruolo di lavoro ibridi** in **Automazione processi**.

3. Selezionare la scheda **Gruppi di ruolo lavoro ibrido di sistema**.

4. Verificare che il ruolo di lavoro ibrido sia presente per tale computer.

5. Se il computer non è configurato come ruolo di lavoro ibrido, apportare rettifiche usando le istruzioni in [Automatizzare le risorse nel data center o nel cloud usando il ruolo di lavoro ibrido per runbook](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker).

6. Aggiungere il computer al gruppo ruolo di lavoro ibrido per runbook.

7. Ripetere i passaggi precedenti per tutti i computer che non sono stati visualizzati nell'anteprima.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: i componenti di Gestione aggiornamenti sono abilitati, mentre la macchina virtuale continua a risultare configurata

### <a name="issue"></a>Problema

Nella macchina virtuale continua a essere visualizzato il messaggio seguente 15 minuti dopo l'inizio della distribuzione:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Le cause di questo errore sono le seguenti:

* Le comunicazioni verso l'account di Automazione sono bloccate.

* È presente un nome computer duplicato con ID del computer di origine diversi. Questo scenario si verifica quando viene creata una macchina virtuale con un nome computer specifico in gruppi di risorse diversi e viene segnalata alla stessa area di lavoro dell'Agente logistico nella sottoscrizione.

* L'immagine della macchina virtuale da distribuire potrebbe provenire da un computer clonato senza preparazione sistema (sysprep) con l'agente di Log Analytics per Windows installato.

### <a name="resolution"></a>Risoluzione

Per individuare il problema esatto della macchina virtuale, eseguire la query seguente nell'area di lavoro Log Analytics collegata all'account di Automazione.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Le comunicazioni con l'account di Automazione sono bloccate

Passare a [Pianificazione della rete](../automation-update-management.md#ports) per informazioni sugli indirizzi e sulle porte da abilitare per il funzionamento di Gestione aggiornamenti.

#### <a name="duplicate-computer-name"></a>Nome computer duplicato

Rinominare le macchine virtuali in modo che i nomi nell'ambiente siano univoci.

#### <a name="deployed-image-from-cloned-machine"></a>Immagine distribuita dal computer clonato

Se si usa un'immagine clonata, nomi computer diversi hanno lo stesso ID computer di origine. In questo caso:

1. Nell'area di lavoro Log Analytics rimuovere la macchina virtuale dalla ricerca salvata per la configurazione dell'ambito `MicrosoftDefaultScopeConfig-Updates` se visualizzata. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.

2. Eseguire il cmdlet seguente.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Eseguire `Restart-Service HealthService` per riavviare il servizio integrità. Questa operazione ricrea la chiave e genera un nuovo UUID.

4. Se questo approccio non funziona, eseguire prima sysprep sull'immagine, quindi installare MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: viene visualizzato un errore di sottoscrizione collegata durante la creazione di una distribuzione degli aggiornamenti per i computer in un altro tenant di Azure

### <a name="issue"></a>Problema

Viene visualizzato l'errore seguente durante la creazione di una distribuzione degli aggiornamenti per i computer in un altro tenant di Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando si crea una distribuzione degli aggiornamenti con macchine virtuali di Azure in un altro tenant incluso in una distribuzione degli aggiornamenti.

### <a name="resolution"></a>Risoluzione

Usare la soluzione alternativa seguente per la pianificazione degli elementi. È possibile usare il cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) con il parametro `ForUpdateConfiguration` per creare una pianificazione. Usare quindi il cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passare i computer nell'altro tenant al parametro `NonAzureComputer`. L'esempio seguente illustra come farlo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: Riavvii inspiegabili

### <a name="issue"></a>Problema

Anche se è impostata l'opzione **Controllo riavvio** per **Non riavviare mai**, i computer continuano a riavviarsi dopo l'installazione degli aggiornamenti.

### <a name="cause"></a>Causa

È possibile modificare Windows Update con diverse chiavi del Registro di sistema, che possono cambiare il comportamento del riavvio.

### <a name="resolution"></a>Risoluzione

Esaminare le chiavi del registro di sistema elencate in [Configurazione degli Aggiornamenti automatici modificando il Registro di sistema](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e [Chiavi del registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) per assicurarsi che i computer siano configurati correttamente.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: il computer mostra "Avvio non riuscito" in una distribuzione degli aggiornamenti

### <a name="issue"></a>Problema

Un computer mostra lo stato `Failed to start`. Quando si visualizzano i dettagli specifici per il computer, compare l'errore seguente:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per uno dei motivi seguenti:

* Il computer non esiste più.
* Il computer è disattivato e non è raggiungibile.
* Il computer ha un problema di connettività di rete e quindi il ruolo di lavoro ibrido nel computer non è raggiungibile.
* È stato eseguito un aggiornamento di MMA che ha modificato l'ID del computer di origine.
* L'esecuzione dell'aggiornamento viene limitata se si raggiunge il limite di 2000 processi simultanei in un account di Automazione. Ogni distribuzione viene considerata un processo e ogni computer in una distribuzione di aggiornamenti conta come processo. Qualsiasi altro processo di automazione o distribuzione di aggiornamenti attualmente in esecuzione nell'account di Automazione viene conteggiato verso il limite di processi simultanei.

### <a name="resolution"></a>Risoluzione

Se applicabili, usare i [gruppi dinamici](../automation-update-management-groups.md) per le distribuzioni degli aggiornamenti. Inoltre, è possibile eseguire i passaggi seguenti.

1. Verificare che il computer esista ancora e che sia raggiungibile. 
2. Se il computer non esiste, modificare la distribuzione e rimuoverlo.
3. Per un elenco delle porte e degli indirizzi necessari per Gestione aggiornamenti, vedere la sezione [Pianificazione della rete](../automation-update-management.md#ports), quindi verificare che il computer soddisfi tali requisiti.
4. Verificare la connettività al ruolo di lavoro ibrido per runbook usando lo strumento di risoluzione dei problemi dell'agente del ruolo lavoro ibrido per runbook. Per altre informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi dell'agente di aggiornamento](update-agent-issues.md).
5. Eseguire la query seguente in Log Analytics per trovare i computer nell'ambiente per cui è stato modificato l'ID del computer di origine. Cercare i computer con lo stesso valore `Computer` ma con un valore `SourceComputerId` diverso.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Dopo aver individuato i computer interessati, modificare le distribuzioni degli aggiornamenti destinate a tali computer, quindi rimuoverle e aggiungerle nuovamente in modo che `SourceComputerId` rifletta il valore corretto.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: gli aggiornamenti vengono installati senza una distribuzione

### <a name="issue"></a>Problema

Quando si registra un computer Windows in Gestione aggiornamenti, vengono visualizzati gli aggiornamenti installati senza una distribuzione.

### <a name="cause"></a>Causa

In Windows gli aggiornamenti vengono installati automaticamente non appena sono disponibili. Questo comportamento può causare confusione se non è stato pianificato un aggiornamento da distribuire nel computer.

### <a name="resolution"></a>Risoluzione

Per impostazione predefinita, la chiave del Registro di sistema `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` è impostata su 4: `auto download and install`.

Per i client Gestione aggiornamenti, è consigliabile impostare questa chiave su 3: `auto download but do not auto install`.

Per altre informazioni, vedere [Configurazione degli Aggiornamenti automatici](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: il computer è già registrato per un altro account

### <a name="issue"></a>Problema

Viene visualizzato il messaggio di errore seguente:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

Il computer è già stato distribuito in un'altra area di lavoro per Gestione aggiornamenti.

### <a name="resolution"></a>Risoluzione

1. Seguire i passaggi descritti in [I computer non vengono visualizzati nel portale in Gestione aggiornamenti](#nologs) per assicurarsi che il computer stia inviando report all'area di lavoro corretta.
2. Eseguire la pulizia degli artefatti nel computer [eliminando il gruppo di runbook ibrido](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group) e riprovare.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: il computer non riesce a comunicare con il servizio

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

La comunicazione di rete potrebbe essere bloccata da un proxy, un gateway o un firewall. 

### <a name="resolution"></a>Risoluzione

Esaminare le funzionalità di rete e assicurarsi che le porte e gli indirizzi appropriati siano consentiti. Vedere i [requisiti di rete](../automation-hybrid-runbook-worker.md#network-planning) per un elenco di porte e indirizzi richiesti da Gestione aggiornamenti e i ruoli di lavoro ibridi per runbook.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: impossibile creare un certificato autofirmato

### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per runbook non è riuscito a generare un certificato autofirmato.

### <a name="resolution"></a>Risoluzione

Verificare che l'account di sistema abbia accesso in lettura alla cartella **C:\ProgramData\Microsoft\Crypto\RSA** e riprovare.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: l'aggiornamento pianificato non è riuscito e viene visualizzato l'errore MaintenanceWindowExceeded

### <a name="issue"></a>Problema

La finestra di manutenzione predefinita per gli aggiornamenti è di 120 minuti. È possibile aumentare la finestra di manutenzione fino a un massimo di 6 ore o 360 minuti.

### <a name="resolution"></a>Risoluzione

Modificare le distribuzioni degli aggiornamenti pianificati con errori e aumentare la finestra di manutenzione.

Per altre informazioni sulle finestre di manutenzione, vedere [Installa aggiornamenti](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: il computer viene indicato come "Non valutato" e genera a un'eccezione HRESULT

### <a name="issue"></a>Problema

* Sono presenti computer che risultano `Not assessed` in **Conformità** con un messaggio di eccezione sottostante.
* Nel portale viene visualizzato un codice di errore HRESULT.

### <a name="cause"></a>Causa

L'Agente di aggiornamento (Agente di Windows Update in Windows; la gestione pacchetti per una distribuzione Linux) non è configurato correttamente. Gestione aggiornamenti si basa sul'Agente di aggiornamento del computer per offrire gli aggiornamenti necessari, lo stato della patch e i risultati delle patch distribuite. Senza queste informazioni Gestione aggiornamenti non può segnalare correttamente le patch necessarie o installate.

### <a name="resolution"></a>Risoluzione

Provare a eseguire gli aggiornamenti localmente nel computer. Se l'operazione ha esito negativo, in genere significa che si è verificato un errore nella configurazione dell'agente di aggiornamento.

Questo problema è spesso causato da anomalie relative a configurazione di rete e firewall. Usare i controlli seguenti per correggere il problema.

* Per Linux, consultare la documentazione appropriata e assicurarsi che sia possibile raggiungere l'endpoint di rete del repository del pacchetto.

* Per Windows, controllare la configurazione dell'agente come elencato in [Gli aggiornamenti non vengono scaricati dall'endpoint Intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Se i computer sono configurati per Windows Update, assicurarsi che sia possibile raggiungere gli endpoint descritti in [Problemi relativi a HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se i computer sono configurati per Windows Server Update Services (WSUS), verificare che sia possibile raggiungere il server WSUS configurato dalla [chiave del Registro di sistema WUServer](/windows/deployment/update/waas-wu-settings).

Se si visualizza HRESULT, fare doppio clic sull'eccezione in rosso per visualizzare il messaggio completo. Individuare possibili risoluzioni o azioni consigliate nella tabella seguente.

|Eccezione  |Risoluzione o azione  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Per maggiori dettagli sulla causa dell'eccezione, cercare il codice di errore pertinente nell'[elenco codici di errore relativi a Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list).        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Indicano problemi di connettività di rete. Assicurarsi che il computer disponga della connettività di rete per Gestione aggiornamenti. Per un elenco di porte e indirizzi necessari, vedere la sezione [pianificazione della rete](../automation-update-management.md#ports).        |
|`0x8024001E`| L'operazione di aggiornamento non è stata completata perché il servizio o il sistema era in fase di arresto.|
|`0x8024002E`| Il servizio Windows Update è disabilitato.|
|`0x8024402C`     | Se si usa un server WSUS, assicurarsi che i valori del Registro di sistema per `WUServer` e `WUStatusServer` nella chiave del Registro di sistema `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` indichino il server WSUS corretto.        |
|`0x80072EE2`|Si sono verificati problemi di connettività di rete o problemi di comunicazione con un server WSUS configurato. Controllare le impostazioni di WSUS e verificare che il servizio sia accessibile dal client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Assicurarsi che il servizio Windows Update (wuauserv) sia in esecuzione e non disattivato.        |
|`0x80070005`| Un errore di accesso negato può dipendere da una delle cause seguenti:<br> Computer infetto<br> Impostazioni di Windows Update non configurate correttamente<br> Errore di autorizzazione file con la cartella%WinDir%\SoftwareDistribution<br> Spazio su disco insufficiente nell'unità di sistema (C:).
|Qualsiasi altra eccezione generica     | Ricercare possibili risoluzioni in Internet e collaborare con il supporto tecnico IT locale.         |

È possibile determinare le possibili cause anche esaminando il file **%Windir%\Windowsupdate.log**. Per altre informazioni su come leggere il log, vedere [Come leggere il file Windowsupdate.log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

È inoltre possibile scaricare ed eseguire lo [strumento di risoluzione dei problemi di Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) per verificare la presenza di problemi con Windows Update nel computer.

> [!NOTE]
> La documentazione relativa allo [strumento di risoluzione dei problemi di Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica che è destinata all'uso nei client Windows, ma funziona anche su Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: l'esecuzione dell'aggiornamento restituisce lo stato Operazione non riuscita (Linux)

### <a name="issue"></a>Problema

Un'operazione di aggiornamento viene avviata ma rileva errori durante l'esecuzione.

### <a name="cause"></a>Causa

Possibili cause:

* La gestione pacchetti non è integra.
* L'agente di aggiornamento (WUA per Windows, gestione pacchetti specifica per le distribuzioni per Linux) non è configurato correttamente.
* Pacchetti specifici stanno interferendo con l'applicazione di patch basata sul cloud.
* Il computer non è raggiungibile.
* Gli aggiornamenti hanno dipendenze non risolte.

### <a name="resolution"></a>Risoluzione

Se si verificano errori durante l'esecuzione di un aggiornamento dopo un normale avvio, [controllare l'output del processo](../manage-update-multi.md#view-results-of-an-update-deployment) del computer interessato. È possibile trovare messaggi di errore specifici generati dai computer che possono essere esaminati per intervenire di conseguenza. Gestione aggiornamenti richiede che la gestione pacchetti sia integra affinché le distribuzioni degli aggiornamenti abbiano esito positivo.

Se vengono visualizzate patch, pacchetti o aggiornamenti specifici immediatamente prima che il processo abbia esito negativo, è possibile provare a[escludere](../automation-tutorial-update-management.md#schedule-an-update-deployment) questi elementi dalla successiva distribuzione degli aggiornamenti. Per raccogliere informazioni sui log da Windows Update, vedere [File di log di Windows Update](/windows/deployment/update/windows-update-logs).

Se non è possibile risolvere un problema di patch, effettuare una copia del file **/var/opt/Microsoft/omsagent/Run/automationworker/omsupdatemgmt.log** e conservarlo per la risoluzione dei problemi prima dell'inizio della successiva distribuzione degli aggiornamenti.

## <a name="patches-arent-installed"></a>Le patch non sono installate

### <a name="machines-dont-install-updates"></a>I computer non installano gli aggiornamenti

Provare a eseguire gli aggiornamenti direttamente nel computer. Se non è possibile eseguire gli aggiornamenti nel computer, vedere l'[elenco degli errori potenziali nella guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Se gli aggiornamenti sono eseguiti in locale, provare a rimuovere e reinstallare l'agente nel computer seguendo il materiale sussidiario in [Rimuovere una macchina virtuale da Gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sono disponibili aggiornamenti che tuttavia non vengono visualizzati come disponibili nei computer

Questo problema si verifica spesso se i computer sono configurati per ottenere gli aggiornamenti da WSUS o Microsoft Endpoint Configuration Manager ma WSUS e Configuration Manager non hanno approvato gli aggiornamenti.

È possibile controllare se i computer sono configurati per WSUS e SCCM eseguendo un riferimento incrociato tra la `UseWUServer`chiave del Registro di sistema e le chiavi del Registro di sistema nella [sezione Configurazione degli aggiornamenti automatici modificando il Registro di sistema del presente articolo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se gli aggiornamenti non sono approvati in WSUS, non sono installati. È possibile verificare la presenza di aggiornamenti non approvati in Log Analytics eseguendo la query seguente.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Gli aggiornamenti vengono visualizzati come installati, ma non è possibile trovarli sul computer

Spesso gli aggiornamenti vengono sostituiti da altri aggiornamenti. Per altre informazioni, vedere [Aggiornamento sostituito](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) nella Guida alla risoluzione dei problemi di Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Installazione degli aggiornamenti in base alla classificazione in Linux

La distribuzione degli aggiornamenti in Linux in base alla classificazione ("Aggiornamenti critici e della sicurezza") presenta avvertimenti importanti, in particolare per CentOS. Queste limitazioni sono documentate nella [pagina di panoramica di Gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 manca in modo coerente

KB2267602 è l'[aggiornamento delle definizioni di Windows Defender](https://www.microsoft.com/wdsi/definitions). Viene aggiornato quotidianamente.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza.

* Ricevere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
