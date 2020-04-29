---
title: Risoluzione dei problemi di automazione di Azure Gestione aggiornamenti
description: Informazioni su come risolvere i problemi relativi alla soluzione Gestione aggiornamenti in automazione di Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91ecff311b8820d3b97e1de0e4b4e87c150e749b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678930"
---
# <a name="troubleshoot-issues-with-the-update-management-solution"></a>Risolvere i problemi relativi alla soluzione Gestione aggiornamenti

Questo articolo illustra i problemi che possono verificarsi quando si usa la soluzione Gestione aggiornamenti. È disponibile uno strumento di risoluzione dei problemi dell'agente per l'agente di lavoro ibrido per Runbook per determinare il problema sottostante. Per ulteriori informazioni sulla risoluzione dei problemi, vedere [risoluzione dei problemi dell'agente di Windows Update](update-agent-issues.md) e [risoluzione dei](update-agent-issues-linux.md)problemi dell'agente di aggiornamento di Linux. Per altri problemi di onboarding, vedere risolvere i problemi di caricamento della [soluzione](onboarding.md).

>[!NOTE]
>Se si riscontrano problemi durante il caricamento della soluzione in una macchina virtuale (VM), controllare il **Operations Manager** registro in **registri applicazioni e servizi** nel computer locale. Cercare gli eventi con ID evento 4502 e i dettagli dell'evento `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`che contengono.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Scenario: viene visualizzato l'errore "non è stato possibile abilitare la soluzione di aggiornamento"

### <a name="issue"></a>Problema

Quando si prova ad abilitare la soluzione Gestione aggiornamenti nell'account di automazione, viene ricevuto l'errore seguente:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Le cause di questo errore sono le seguenti:

* I requisiti del firewall di rete per l'agente di Log Analytics potrebbero non essere configurati correttamente. Questa situazione può causare un errore dell'agente durante la risoluzione degli URL DNS.

* La destinazione della soluzione non è configurata correttamente e il computer non riceve gli aggiornamenti come previsto.

* È anche possibile notare che il computer Visualizza lo stato `Non-compliant` in **conformità**. Allo stesso tempo, **Agent Desktop Analytics** segnala l'agente come `Disconnected`.

### <a name="resolution"></a>Soluzione

* Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

* Passare a [pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento gestione aggiornamenti.  

* Passare a [pianificazione della rete](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento dell'agente log Analytics.

* Verificare la presenza di problemi di configurazione dell'ambito. La [configurazione dell'ambito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quali computer vengono configurati per la soluzione. Se il computer viene visualizzato nell'area di lavoro, ma non nel portale * * Gestione aggiornamenti, sarà necessario impostare la configurazione dell'ambito in modo che sia destinata ai computer. Per ulteriori informazioni sulla configurazione dell'ambito, vedere l'articolo relativo all' [onboarding dei computer nell'area di lavoro](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Rimuovere la configurazione del ruolo di lavoro attenendosi alla procedura descritta in [eliminazione del ruolo di lavoro ibrido per Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: aggiornamento sostituito indicato come mancante in Gestione aggiornamenti

### <a name="issue"></a>Problema

Gli aggiornamenti precedenti vengono visualizzati per un account di automazione come mancanti anche se sono stati sostituiti. Un aggiornamento sostituito è uno che non è necessario installare perché è disponibile un aggiornamento successivo che corregge la stessa vulnerabilità. Gestione aggiornamenti ignora l'aggiornamento sostituito e lo rende non applicabile a favore dell'aggiornamento sostitutivo. Per informazioni su un problema correlato, vedere l' [aggiornamento è stato sostituito](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Causa

Gli aggiornamenti sostituiti non vengono indicati correttamente come rifiutati, in modo che possano essere considerati non applicabili.

### <a name="resolution"></a>Soluzione

Quando un aggiornamento sostituito diventa il 100% non applicabile, è necessario modificare lo stato di approvazione di tale aggiornamento `Declined`in. Per modificare lo stato di approvazione per tutti gli aggiornamenti:

1. Nell'account di automazione selezionare **Gestione aggiornamenti** per visualizzare lo stato del computer. Vedere [visualizzare le valutazioni degli aggiornamenti](../manage-update-multi.md#view-an-update-assessment).

2. Controllare l'aggiornamento sostituito per assicurarsi che il 100% non sia applicabile. 

3. Contrassegnare l'aggiornamento come rifiutato, a meno che non si disponga di una domanda sull'aggiornamento. 

4. Selezionare **computer** e nella colonna **conformità** forzare una ripetizione dell'analisi per la conformità. Vedere [gestire gli aggiornamenti per più computer](../manage-update-multi.md).

5. Ripetere i passaggi precedenti per gli altri aggiornamenti sostituiti.

6. Eseguire la pulizia guidata per eliminare i file dagli aggiornamenti rifiutati. 

7. Per Windows Server Update Services (WSUS), pulire manualmente tutti gli aggiornamenti sostituiti per aggiornare l'infrastruttura.

8. Ripetere questa procedura regolarmente per correggere il problema di visualizzazione e ridurre al minimo la quantità di spazio su disco utilizzata per la gestione degli aggiornamenti.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: i computer non vengono visualizzati nel portale in Gestione aggiornamenti

### <a name="issue"></a>Problema

I computer presentano i sintomi seguenti:

* Il computer viene `Not configured` visualizzato dalla visualizzazione Gestione aggiornamenti di una macchina virtuale.

* I computer non sono presenti nella visualizzazione Gestione aggiornamenti dell'account di automazione di Azure.

* Sono presenti computer che risultano `Not assessed` **conformi**. Tuttavia, vengono visualizzati i dati heartbeat nei log di monitoraggio di Azure per il ruolo di lavoro ibrido per Runbook, ma non per Gestione aggiornamenti.

### <a name="cause"></a>Causa

Questo problema può essere causato da problemi di configurazione locali o da una configurazione non corretta dell'ambito. Possibili cause specifiche sono:

* Potrebbe essere necessario ripetere la registrazione e reinstallare il ruolo di lavoro ibrido per Runbook.

* È possibile che sia stata definita una quota nell'area di lavoro che è stata raggiunta e che impedisce l'archiviazione di altri dati.

### <a name="resolution"></a>Soluzione

1. Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

2. Verificare che il computer stia segnalando l'area di lavoro corretta. Per indicazioni su come verificare questo aspetto, vedere [verificare la connettività dell'agente a log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Assicurarsi anche che l'area di lavoro sia collegata all'account di automazione di Azure. Per confermare, passare all'account di automazione e selezionare **area di lavoro collegata** in **risorse correlate**.

3. Assicurarsi che i computer siano visualizzati nell'area di lavoro Log Analytics collegata all'account di automazione. Eseguire la query seguente nell'area di lavoro Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Se il computer non viene visualizzato nei risultati della query, non è stato archiviato di recente. Probabilmente si è verificato un problema di configurazione locale ed è necessario [reinstallare l'agente](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). 

5. Se il computer viene visualizzato nei risultati della query, verificare la presenza di problemi di configurazione dell'ambito. La [configurazione dell'ambito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quali computer sono configurati per la soluzione. 

6. Se il computer viene visualizzato nell'area di lavoro, ma non in Gestione aggiornamenti, è necessario configurare la configurazione dell'ambito per il computer di destinazione. Per informazioni su come eseguire questa operazione, vedere l'articolo relativo all' [onboarding dei computer nell'area di lavoro](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

7. Eseguire la query nell'area di lavoro.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Se si ottiene un `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` risultato, è stata raggiunta la quota definita nell'area di lavoro, che ha impedito il salvataggio dei dati. Nell'area di lavoro passare a **gestione volume dati** in **utilizzo e costi stimati**e modificare o rimuovere la quota.

9. Se il problema persiste, seguire la procedura descritta in [distribuire un ruolo di lavoro ibrido per Runbook Windows](../automation-windows-hrw-install.md) per reinstallare il ruolo di lavoro ibrido per Windows. Per Linux, seguire la procedura descritta in [distribuire un ruolo di lavoro ibrido per Runbook di Linux](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: non è possibile registrare il provider di risorse di automazione per le sottoscrizioni

### <a name="issue"></a>Problema

Quando si lavora con soluzioni nell'account di automazione, si verifica l'errore seguente:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Causa

Il provider di risorse di automazione non è registrato nella sottoscrizione.

### <a name="resolution"></a>Soluzione

Per registrare il provider di risorse di automazione, attenersi alla procedura seguente nella portale di Azure.

1. Nell'elenco dei servizi di Azure nella parte inferiore del portale selezionare **tutti i servizi**e quindi selezionare **sottoscrizioni** nel gruppo di servizi generale.

2. Selezionare la propria sottoscrizione.

3. In **Impostazioni**selezionare **provider di risorse**.

4. Dall'elenco dei provider di risorse, verificare che il provider di risorse Microsoft. Automation sia registrato.

5. Se non è elencato, registrare il provider Microsoft. Automation attenendosi alla procedura descritta in [risolvere gli errori per la registrazione del provider di risorse](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenario: l'aggiornamento pianificato con una pianificazione dinamica ha perso alcuni computer

### <a name="issue"></a>Problema

I computer inclusi in un'anteprima di aggiornamento non vengono visualizzati nell'elenco dei computer con patch durante un'esecuzione pianificata.

### <a name="cause"></a>Causa

Questo problema può avere una delle cause seguenti:

* Le sottoscrizioni definite nell'ambito di una query dinamica non sono configurate per il provider di risorse di automazione registrato.

* I computer non sono disponibili o non hanno tag appropriati quando la pianificazione è stata eseguita.

### <a name="resolution"></a>Soluzione

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Sottoscrizioni non configurate per il provider di risorse di automazione registrato

Se la sottoscrizione non è configurata per il provider di risorse di automazione, non è possibile eseguire query o recuperare informazioni sui computer della sottoscrizione. Usare la procedura seguente per verificare la registrazione per la sottoscrizione.

1. Nella [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)accedere all'elenco dei servizi di Azure.

2. Selezionare **tutti i servizi**e quindi selezionare **sottoscrizioni** nel gruppo di servizi generale. 

3. Trovare la sottoscrizione definita nell'ambito della distribuzione.

4. In **Impostazioni**scegliere **provider di risorse**.

5. Verificare che il provider di risorse Microsoft. Automation sia registrato.

6. Se non è elencato, registrare il provider Microsoft. Automation attenendosi alla procedura descritta in [risolvere gli errori per la registrazione del provider di risorse](/azure/azure-resource-manager/resource-manager-register-provider-errors).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Macchine virtuali non disponibili o non contrassegnate correttamente quando viene eseguita la pianificazione

Usare la procedura seguente se la sottoscrizione è configurata per il provider di risorse di automazione, ma l'esecuzione della pianificazione degli aggiornamenti con i [gruppi dinamici](../automation-update-management-groups.md) specificati ha perso alcuni computer.

1. Nella portale di Azure aprire l'account di automazione e selezionare **Gestione aggiornamenti**.

2. Controllare [Gestione aggiornamenti cronologia](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) per determinare l'ora esatta in cui è stata eseguita la distribuzione degli aggiornamenti. 

3. Per i computer sospetti che non sono stati Gestione aggiornamenti, usare Azure Resource Graph (ARG) per [individuare le modifiche del computer](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details). 

4. Cercare le modifiche in un periodo di tempo considerevole, ad esempio un giorno, prima dell'esecuzione della distribuzione degli aggiornamenti.

5. Controllare nei risultati della ricerca eventuali modifiche sistematiche, ad esempio le modifiche di eliminazione o aggiornamento, ai computer in questo periodo. Queste modifiche possono modificare lo stato o i tag del computer in modo che i computer non siano selezionati nell'elenco dei computer quando vengono distribuiti gli aggiornamenti.

6. Modificare le impostazioni del computer e delle risorse in modo da correggerne lo stato o i problemi relativi ai tag.

7. Eseguire di nuovo la pianificazione degli aggiornamenti per assicurarsi che la distribuzione con i gruppi dinamici specificati includa tutti i computer.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scenario: i computer previsti non sono visualizzati in anteprima per il gruppo dinamico

### <a name="issue"></a>Problema

Le macchine virtuali per ambiti selezionati di un gruppo dinamico non vengono visualizzate nell'elenco portale di Azure anteprima. Questo elenco è costituito da tutti i computer recuperati da una query ARG per gli ambiti selezionati. Gli ambiti vengono filtrati per i computer in cui sono installati i ruoli di lavoro ibridi per Runbook e per i quali si dispone di autorizzazioni di accesso. 

### <a name="cause"></a>Causa
 
Di seguito sono riportate le possibili cause del problema:

* Non si dispone dell'accesso corretto agli ambiti selezionati.
* La query ARG non recupera i computer previsti.
* Il ruolo di lavoro ibrido per Runbook non è installato nei computer.

### <a name="resolution"></a>Soluzione 

#### <a name="incorrect-access-on-selected-scopes"></a>Accesso errato agli ambiti selezionati

Nel portale di Azure vengono visualizzati solo i computer per i quali si dispone dell'accesso in scrittura in un ambito specifico. Se non si dispone dell'accesso corretto per un ambito, vedere [esercitazione: concedere a un utente l'accesso alle risorse di Azure usando RBAC e il portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>La query ARG non restituisce i computer previsti

Attenersi alla procedura seguente per determinare se le query funzionano correttamente.

1. Eseguire una query ARG formattata come illustrato di seguito nel pannello Resource Graph Explorer in portale di Azure. Questa query simula i filtri selezionati al momento della creazione del gruppo dinamico in Gestione aggiornamenti. Vedere [usare i gruppi dinamici con gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

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
 
2. Verificare se i computer che si stanno cercando sono elencati nei risultati della query. 

3. Se i computer non sono elencati, probabilmente si verifica un problema con il filtro selezionato nel gruppo dinamico. Modificare la configurazione di gruppo in base alle esigenze.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Ruolo di lavoro ibrido per Runbook non installato nei computer

I computer vengono visualizzati nei risultati delle query ARG ma non vengono ancora visualizzati nell'anteprima del gruppo dinamico. In questo caso, è possibile che i computer non siano designati come ruoli di lavoro ibridi e che quindi non possano eseguire automazione di Azure e Gestione aggiornamenti processi. Per assicurarsi che i computer che si prevede di visualizzare siano configurati come ruoli di lavoro ibridi per Runbook:

1. Nella portale di Azure passare all'account di automazione per un computer che non viene visualizzato correttamente.

2. Selezionare **gruppi di lavoro ibridi** in **automazione processi**.

3. Selezionare la scheda **gruppi di lavoro ibridi di sistema** .

4. Verificare che il ruolo di lavoro ibrido sia presente per tale computer.

5. Se il computer non è configurato come ruolo di lavoro ibrido, apportare modifiche usando le istruzioni in [automatizzare le risorse nel Data Center o nel cloud usando Hybrid Runbook Workers](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker).

6. Aggiungere il computer al gruppo ruolo di lavoro ibrido per Runbook.

7. Ripetere i passaggi precedenti per tutti i computer che non sono stati visualizzati nell'anteprima.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: Componenti per Gestione aggiornamenti soluzione abilitata, mentre la macchina virtuale continua a essere visualizzata come configurata

### <a name="issue"></a>Problema

Nella macchina virtuale continua a essere visualizzato il messaggio seguente 15 minuti dopo l'onboarding:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Le cause di questo errore sono le seguenti:

* È in corso il blocco della comunicazione con l'account di automazione.

* È presente un nome di computer duplicato con ID del computer di origine diversi. Questo scenario si verifica quando viene creata una macchina virtuale con un nome di computer specifico in gruppi di risorse diversi e viene segnalata la stessa area di lavoro dell'agente logistico nella sottoscrizione.

* L'immagine di macchina virtuale da caricare potrebbe provenire da una macchina clonata non preparata con la preparazione del sistema (Sysprep) con l'agente di Log Analytics per Windows installato.

### <a name="resolution"></a>Soluzione

Per individuare il problema esatto della macchina virtuale, eseguire la query seguente nell'area di lavoro Log Analytics collegata all'account di automazione.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Comunicazione con l'account di automazione bloccato

Passare a [pianificazione della rete](../automation-update-management.md#ports) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento gestione aggiornamenti.

#### <a name="duplicate-computer-name"></a>Nome computer duplicato

Rinominare le VM in modo da garantire nomi univoci nel proprio ambiente.

#### <a name="onboarded-image-from-cloned-machine"></a>Immagine caricata dal computer clonato

Se si usa un'immagine clonata, i nomi di computer diversi hanno lo stesso ID computer di origine. In questo caso:

1. Nell'area di lavoro Log Analytics rimuovere la macchina virtuale dalla ricerca salvata per `MicrosoftDefaultScopeConfig-Updates` la configurazione dell'ambito, se visualizzata. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.

2. Eseguire il cmdlet seguente.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Eseguire `Restart-Service HealthService` per riavviare il servizio integrità. Questa operazione ricrea la chiave e genera un nuovo UUID.

4. Se questo approccio non funziona, eseguire prima Sysprep sull'immagine, quindi installare MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: si riceve un errore di sottoscrizione collegata quando si crea una distribuzione degli aggiornamenti per i computer in un altro tenant di Azure

### <a name="issue"></a>Problema

Si verifica l'errore seguente quando si tenta di creare una distribuzione degli aggiornamenti per i computer in un altro tenant di Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando si crea una distribuzione degli aggiornamenti con macchine virtuali di Azure in un altro tenant incluso in una distribuzione degli aggiornamenti.

### <a name="resolution"></a>Soluzione

Usare la soluzione alternativa seguente per fare in modo che questi elementi siano pianificati. È possibile usare il cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) con il `ForUpdateConfiguration` parametro per creare una pianificazione. Usare quindi il cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passare le macchine nell'altro tenant al `NonAzureComputer` parametro. L'esempio seguente illustra come farlo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: riavvii non spiegati

### <a name="issue"></a>Problema

Anche se è stata impostata l'opzione di **controllo riavvio** in modo che **non venga mai**riavviata, i computer continuano a essere riavviati dopo l'installazione degli aggiornamenti.

### <a name="cause"></a>Causa

Windows Update possono essere modificate da diverse chiavi del registro di sistema, le quali possono modificare il comportamento del riavvio.

### <a name="resolution"></a>Soluzione

Esaminare le chiavi del registro di sistema elencate in [configurazione aggiornamenti automatici modificando il registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) di sistema e le [chiavi del registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) per assicurarsi che i computer siano configurati correttamente.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: il computer Mostra "Impossibile avviare" in una distribuzione di aggiornamenti

### <a name="issue"></a>Problema

Un computer Visualizza uno `Failed to start` stato. Quando si visualizzano i dettagli specifici per il computer, viene visualizzato l'errore seguente:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per uno dei motivi seguenti:

* Il computer non esiste più.
* Il computer è disattivato e non è raggiungibile.
* Il computer ha un problema di connettività di rete e quindi il ruolo di lavoro ibrido nel computer non è raggiungibile.
* Si è verificato un aggiornamento di MMA che ha modificato l'ID del computer di origine.
* L'esecuzione dell'aggiornamento è stata limitata se si raggiunge il limite di 2000 processi simultanei in un account di automazione. Ogni distribuzione viene considerata un processo e ogni computer in una distribuzione di aggiornamenti viene conteggiato come un processo. Qualsiasi altro processo di automazione o distribuzione di aggiornamenti attualmente in esecuzione nell'account di automazione viene conteggiato per il limite di processi simultanei.

### <a name="resolution"></a>Soluzione

Quando applicabile, usare i [gruppi dinamici](../automation-update-management-groups.md) per le distribuzioni degli aggiornamenti. Inoltre, è possibile eseguire i passaggi seguenti.

1. Verificare che il computer esista ancora e che sia raggiungibile. 
2. Se il computer non esiste, modificare la distribuzione e rimuovere il computer.
3. Vedere la sezione relativa alla [pianificazione della rete](../automation-update-management.md#ports) per un elenco di porte e indirizzi necessari per gestione aggiornamenti, quindi verificare che il computer soddisfi questi requisiti.
4. Verificare la connettività al ruolo di lavoro ibrido per Runbook usando la risoluzione dei problemi dell'agente di lavoro ibrido per Runbook Per altre informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi dell'agente di aggiornamento](update-agent-issues.md).
5. Eseguire la query seguente in Log Analytics per trovare i computer nell'ambiente per cui è stato modificato l'ID del computer di origine. Cercare i computer con lo stesso `Computer` valore ma con un valore `SourceComputerId` diverso.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Dopo aver individuato i computer interessati, modificare le distribuzioni degli aggiornamenti destinate a tali computer, quindi rimuoverle e leggerle in modo che `SourceComputerId` riflettano il valore corretto.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: gli aggiornamenti vengono installati senza una distribuzione

### <a name="issue"></a>Problema

Quando si registra un computer Windows in Gestione aggiornamenti, vengono visualizzati gli aggiornamenti installati senza una distribuzione.

### <a name="cause"></a>Causa

In Windows, gli aggiornamenti vengono installati automaticamente non appena sono disponibili. Questo comportamento può causare confusione se non è stato pianificato un aggiornamento da distribuire nel computer.

### <a name="resolution"></a>Soluzione

Per `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` impostazione predefinita, la chiave del registro di sistema è `auto download and install`impostata su 4:.

Per Gestione aggiornamenti client, è consigliabile impostare questa chiave su 3: `auto download but do not auto install`.

Per ulteriori informazioni, vedere [configurazione di aggiornamenti automatici](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: Il computer è già registrato per un altro account

### <a name="issue"></a>Problema

Viene visualizzato il messaggio di errore seguente:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

Il computer è già stato caricato in un'altra area di lavoro per Gestione aggiornamenti.

### <a name="resolution"></a>Soluzione

1. Seguire i passaggi in [computer non vengono visualizzati nel portale in Gestione aggiornamenti](#nologs) per assicurarsi che il computer stia segnalando l'area di lavoro corretta.
2. Pulire gli elementi nel computer [eliminando il gruppo Runbook ibrido](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), quindi riprovare.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: il computer non può comunicare con il servizio

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

### <a name="resolution"></a>Soluzione

Verificare la rete e assicurarsi che siano consentite le porte e gli indirizzi appropriati. Vedere [requisiti di rete](../automation-hybrid-runbook-worker.md#network-planning) per un elenco di porte e indirizzi necessari per gestione aggiornamenti e ruoli di lavoro ibridi per Runbook.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: Impossibile creare un certificato autofirmato

### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per Runbook non è riuscito a generare un certificato autofirmato.

### <a name="resolution"></a>Soluzione

Verificare che l'account di sistema disponga dell'accesso in lettura alla cartella **C:\ProgramData\Microsoft\Crypto\RSA** e riprovare.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: l'aggiornamento pianificato non è riuscito con un errore MaintenanceWindowExceeded

### <a name="issue"></a>Problema

La finestra di manutenzione predefinita per gli aggiornamenti è di 120 minuti. È possibile aumentare la finestra di manutenzione fino a un massimo di 6 ore o 360 minuti.

### <a name="resolution"></a>Soluzione

Modificare le distribuzioni degli aggiornamenti pianificati con errori e aumentare la finestra di manutenzione.

Per ulteriori informazioni sulle finestre di manutenzione, vedere [Install Updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: il computer viene visualizzato come "non valutato" e Mostra un'eccezione HRESULT

### <a name="issue"></a>Problema

* Sono presenti computer che risultano `Not assessed` **conformi**e viene visualizzato un messaggio di eccezione al di sotto di essi.
* Nel portale viene visualizzato un codice di errore HRESULT.

### <a name="cause"></a>Causa

L'agente di aggiornamento (agente Windows Update in Windows; Gestione pacchetti per una distribuzione Linux) non è configurato correttamente. Gestione aggiornamenti si basa sull'agente di aggiornamento del computer per fornire gli aggiornamenti necessari, lo stato della patch e i risultati delle patch distribuite. Senza queste informazioni, Gestione aggiornamenti non è in grado di segnalare correttamente le patch necessarie o installate.

### <a name="resolution"></a>Soluzione

Provare a eseguire gli aggiornamenti localmente nel computer. Se questa operazione ha esito negativo, in genere significa che si è verificato un errore di configurazione dell'agente di aggiornamento.

Questo problema è spesso causato da problemi di configurazione di rete e firewall. Per risolvere il problema, utilizzare i controlli seguenti.

* Per Linux, consultare la documentazione appropriata per assicurarsi che sia possibile raggiungere l'endpoint di rete del repository del pacchetto.

* Per Windows, verificare che la configurazione dell'agente come elencato negli [aggiornamenti non venga scaricata dall'endpoint Intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Se i computer sono configurati per Windows Update, assicurarsi che sia possibile raggiungere gli endpoint descritti in [problemi correlati a http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se i computer sono configurati per Windows Server Update Services (WSUS), verificare che sia possibile raggiungere il server WSUS configurato dalla [chiave del registro di sistema WUServer](/windows/deployment/update/waas-wu-settings).

Se viene visualizzato un HRESULT, fare doppio clic sull'eccezione visualizzata in rosso per visualizzare l'intero messaggio di eccezione. Esaminare la tabella seguente per individuare possibili soluzioni o azioni consigliate.

|Eccezione  |Risoluzione o azione  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Eseguire una ricerca nel codice di errore pertinente nell' [elenco dei codici di errore di Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) per trovare ulteriori dettagli sulla ragione dell'eccezione.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Che indicano problemi di connettività di rete. Verificare che il computer disponga della connettività di rete per Gestione aggiornamenti. Vedere la sezione relativa alla [pianificazione della rete](../automation-update-management.md#ports) per un elenco di porte e indirizzi obbligatori.        |
|`0x8024001E`| L'operazione di aggiornamento non è stata completata perché il servizio o il sistema è stato arrestato.|
|`0x8024002E`| Il servizio Windows Update è disabilitato.|
|`0x8024402C`     | Se si usa un server WSUS, assicurarsi che i valori del registro di `WUServer` sistema `WUStatusServer` per e `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` nella chiave del registro di sistema specifichino il server WSUS corretto.        |
|`0x80072EE2`|Si è verificato un problema di connettività di rete o si è verificato un problema durante la comunicazione con un server WSUS configurato. Controllare le impostazioni di WSUS e verificare che il servizio sia accessibile dal client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Verificare che il servizio Windows Update (wuauserv) sia in esecuzione e non sia disabilitato.        |
|`0x80070005`| Un errore di accesso negato può essere causato da uno dei seguenti elementi:<br> Computer infetto<br> Impostazioni di Windows Update non configurate correttamente<br> Errore di autorizzazione file con la cartella%WinDir%\SoftwareDistribution<br> Spazio su disco insufficiente nell'unità di sistema (C:).
|Qualsiasi altra eccezione generica     | Eseguire una ricerca su Internet per individuare le possibili soluzioni e collaborare con il supporto IT locale.         |

La revisione del file **file%windir%\WindowsUpdate.log** consente inoltre di determinare le possibili cause. Per ulteriori informazioni sulla lettura del log, vedere [come leggere il file windowsupdate. log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

È anche possibile scaricare ed eseguire lo strumento di [risoluzione dei problemi Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) per verificare la presenza di eventuali problemi con Windows Update nel computer.

> [!NOTE]
> La documentazione per la [risoluzione dei problemi Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica che è destinata all'uso nei client Windows, ma funziona anche in Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: l'esecuzione dell'aggiornamento restituisce lo stato non riuscito (Linux)

### <a name="issue"></a>Problema

Viene avviata un'operazione di aggiornamento, ma vengono rilevati errori durante l'esecuzione.

### <a name="cause"></a>Causa

Possibili cause:

* Gestione pacchetti non è integro.
* L'agente di aggiornamento (WUA per Windows, gestione pacchetti specifica della distribuzione per Linux) non è configurato correttamente.
* I pacchetti specifici interferiscono con l'applicazione di patch basata sul cloud.
* Il computer non è raggiungibile.
* Aggiornamenti con dipendenze non risolte.

### <a name="resolution"></a>Soluzione

Se si verificano errori durante l'esecuzione di un aggiornamento dopo l'avvio, [controllare l'output del processo](../manage-update-multi.md#view-results-of-an-update-deployment) dal computer interessato nell'esecuzione. Potrebbero essere presenti messaggi di errore specifici dei computer su cui è possibile eseguire ricerche e su cui intervenire. Gestione aggiornamenti richiede che la gestione pacchetti sia integra affinché le distribuzioni degli aggiornamenti abbiano esito positivo.

Se vengono visualizzate patch, pacchetti o aggiornamenti specifici immediatamente prima che il processo abbia esito negativo, è possibile provare a [escludere](../automation-tutorial-update-management.md#schedule-an-update-deployment) questi elementi dalla successiva distribuzione degli aggiornamenti. Per raccogliere informazioni sui log da Windows Update, vedere [Windows Update file di log](/windows/deployment/update/windows-update-logs).

Se non è possibile risolvere un problema di applicazione di patch, creare una copia del file **/var/opt/Microsoft/omsagent/Run/automationworker/omsupdatemgmt.log** e conservarla per la risoluzione dei problemi prima che venga avviata la prossima distribuzione degli aggiornamenti.

## <a name="patches-arent-installed"></a>Le patch non sono installate

### <a name="machines-dont-install-updates"></a>I computer non installano gli aggiornamenti

Provare a eseguire gli aggiornamenti direttamente nel computer. Se il computer non può applicare gli aggiornamenti, consultare l' [elenco di potenziali errori nella Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Se gli aggiornamenti vengono eseguiti localmente, provare a rimuovere e reinstallare l'agente nel computer seguendo le istruzioni riportate in [rimuovere una macchina virtuale da Gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sono consapevole che gli aggiornamenti sono disponibili, ma non vengono visualizzati come disponibile nei computer

Questo problema si verifica spesso se i computer sono configurati per ottenere gli aggiornamenti da WSUS o Microsoft endpoint Configuration Manager ma WSUS e Configuration Manager non hanno approvato gli aggiornamenti.

È possibile verificare se i computer sono configurati per WSUS e SCCM facendo riferimento incrociato `UseWUServer` alla chiave del registro di sistema nelle chiavi del registro di sistema nella [sezione configurazione di aggiornamenti automatici modificando il registro di sistema di questo articolo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se gli aggiornamenti non sono approvati in WSUS, non sono installati. È possibile verificare la presenza di aggiornamenti non approvati in Log Analytics eseguendo la query seguente.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Gli aggiornamenti vengono visualizzati come installati, ma non è possibile trovarli sul computer

Spesso gli aggiornamenti vengono sostituiti da altri aggiornamenti. Per ulteriori informazioni, vedere l'articolo relativo all' [aggiornamento sostituito](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) nella Windows Update Guida alla risoluzione dei problemi.

### <a name="installing-updates-by-classification-on-linux"></a>Installazione degli aggiornamenti in base alla classificazione in Linux

La distribuzione degli aggiornamenti in Linux in base alla classificazione ("Aggiornamenti critici e della sicurezza") presenta avvertimenti importanti, in particolare per CentOS. Queste limitazioni sono documentate nella [pagina panoramica gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 è sempre mancante

KB2267602 è l'[aggiornamento delle definizioni di Windows Defender](https://www.microsoft.com/wdsi/definitions). Viene aggiornato ogni giorno.

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo.

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.
