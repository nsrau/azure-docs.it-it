---
title: Risolvere i problemi relativi all'avvio e all'arresto delle macchine virtuali - Automazione di AzureTroubleshoot Starting and Stopping VMs - Azure Automation
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi relativi all'avvio e all'arresto di macchine virtuali in Automazione di Azure.This article provides information on troubleshooting Starting and Stopping VMs in Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97ea98fc38fc8d06dc1bc65ee057241da6f15488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851390"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Risolvere i problemi della soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: la soluzione Start/Stop VM non riesce a distribuire correttamente

### <a name="issue"></a>Problema

Quando si distribuisce la [soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md), viene visualizzato uno degli errori seguenti:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Causa

Gli errori delle distribuzioni possono essere causati da uno dei motivi seguenti:

1. Esiste già un account di Automazione con lo stesso nome nell'area selezionata.
2. È applicato un criterio che non consente la distribuzione della soluzione Avvio/Arresto di macchine virtuali.
3. I tipi di risorse `Microsoft.OperationsManagement`, `Microsoft.Insights` o `Microsoft.Automation` non sono registrati.
4. Esiste un blocco per l'area di lavoro Log Analytics.
5. Si dispone di una versione obsoleta dei moduli di AzureRM o della soluzione Start/Stop.

### <a name="resolution"></a>Risoluzione

Esaminare l'elenco seguente per individuare possibili soluzioni al problema o posizioni in cui cercare:

1. Gli account di Automazione devono essere univoci all'interno di un'area di Azure, anche se si trovano in gruppi di risorse diversi. Controllare gli account di Automazione esistenti nell'area di destinazione.
2. Un criterio esistente impedisce la distribuzione di una risorsa necessaria per la soluzione Avvio/Arresto di macchine virtuali. Passare alle assegnazioni dei criteri nel portale di Azure e controllare se è presente un'assegnazione dei criteri che non consente la distribuzione di questa risorsa. Per altre informazioni, vedere [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
3. Per distribuire la soluzione Avvio/Arresto di macchine virtuali, la sottoscrizione deve essere registrata negli spazi dei nomi delle risorse di Azure seguenti:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Per altre informazioni sugli errori di registrazione dei provider, vedere[Risolvere gli errori di registrazione del provider di risorse](../../azure-resource-manager/templates/error-register-resource-provider.md).
4. Se è presente un blocco sull'area di lavoro Log Analytics, passare all'area di lavoro nel portale di Azure e rimuovere tutti i blocchi sulla risorsa.
5. Se le risoluzioni precedenti non risolvono il problema, seguire le istruzioni in [Aggiornare la soluzione](../automation-solution-vm-management.md#update-the-solution) per ridistribuire la soluzione Start/Stop.

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: impossibile avviare/arrestare tutte le macchine virtualiScenario: All VMs fail to start/stop

### <a name="issue"></a>Problema

La soluzione Avvio/Arresto di macchine virtuali è stata configurata, ma non avvia o arresta tutte le macchine virtuali configurate.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

1. Una pianificazione non è configurata correttamente
2. L'account RunAs potrebbe non essere configurato correttamente
3. Potrebbero essersi verificati errori in un runbook
4. Le macchine virtuali potrebbero essere state escluse

### <a name="resolution"></a>Risoluzione

Esaminare l'elenco seguente per individuare possibili soluzioni al problema o posizioni in cui cercare:

* Verificare di aver configurato correttamente una pianificazione per la soluzione Avvio/Arresto di macchine virtuali. Per informazioni su come configurare una pianificazione, vedere [Pianificazioni](../automation-schedules.md).

* Controllare i [flussi di lavoro](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per cercare eventuali errori. Nel portale passare all'account di automazione e selezionare **Processi** in **Automazione processi**. Nella pagina **Processi** cercare i processi di uno dei runbook seguenti:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Verificare che il proprio [account RunAs](../manage-runas-account.md) abbia le autorizzazioni appropriate sulle macchine virtuali che si sta cercando di avviare o arrestare. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: Visualizzare i ruoli assegnati a un utente tramite il portale](../../role-based-access-control/check-access.md)di Azure. È necessario fornire l'ID applicazione per l'entità servizio utilizzata dall'account RunAs. È possibile recuperare questo valore passando al proprio account di automazione nel portale di Azure, selezionando **Account RunAs** in **Impostazioni account** e facendo clic sull'account RunAs appropriato.

* Se le macchine virtuali sono escluse in modo esplicito, non possono essere avviate o arrestate. Le macchine virtuali escluse sono impostate nella variabile **External_ExcludeVMNames** nell'account di automazione in cui è distribuita la soluzione. L'esempio seguente mostra come eseguire una query su tale valore con PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: alcune macchine virtuali non vengono avviate o interrotteScenario: Some of my VMs fail to start or stop

### <a name="issue"></a>Problema

La soluzione Avvio/Arresto di macchine virtuali è stata configurata, ma non avvia o arresta alcune delle macchine virtuali configurate.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

1. Se si usa lo scenario di avvio/arresto in sequenza, un tag potrebbe mancare o non essere corretto
2. La macchina virtuale potrebbe essere stata esclusa
3. L'account RunAs potrebbe non avere autorizzazioni sufficienti sulla macchina virtuale
4. La macchina virtuale potrebbe avere qualcosa che ne ha impedito l'avvio o l'arresto

### <a name="resolution"></a>Risoluzione

Esaminare l'elenco seguente per individuare possibili soluzioni al problema o posizioni in cui cercare:

* Quando si usa lo [scenario di avvio/arresto in sequenza](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) della soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività, occorre verificare che ogni macchina virtuale che si vuole avviare o arrestare abbia il tag appropriato. Verificare che le macchine virtuali da avviare abbiano il tag `sequencestart` e che quelle da arrestare abbiano il tag `sequencestop`. Entrambi i tag richiedono un valore intero positivo. È possibile usare una query simile all'esempio seguente per cercare tutte le macchine virtuali con i tag e i relativi valori.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Se le macchine virtuali sono escluse in modo esplicito, non possono essere avviate o arrestate. Le macchine virtuali escluse sono impostate nella variabile **External_ExcludeVMNames** nell'account di automazione in cui è distribuita la soluzione. L'esempio seguente mostra come eseguire una query su tale valore con PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Per avviare e arrestare macchine virtuali, l'account RunAs per l'account di automazione deve avere le autorizzazioni appropriate sulla macchina virtuale. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: Visualizzare i ruoli assegnati a un utente tramite il portale](../../role-based-access-control/check-access.md)di Azure. È necessario fornire l'ID applicazione per l'entità servizio utilizzata dall'account RunAs. È possibile recuperare questo valore passando al proprio account di automazione nel portale di Azure, selezionando **Account RunAs** in **Impostazioni account** e facendo clic sull'account RunAs appropriato.

* Se la macchina virtuale mostra un problema di avvio o deallocazione, questo comportamento potrebbe essere dovuto a un problema nella macchina virtuale stessa. È possibile ad esempio che venga applicato un aggiornamento durante un tentativo di arresto, che un servizio si blocchi e così via. Passare alla risorsa macchina virtuale e verificare se nei **log attività** sono presenti errori. Si può anche provare ad accedere alla macchina virtuale per verificare se sono presenti errori nei log eventi. Per altre informazioni sulla risoluzione dei problemi della macchina virtuale, vedere [Risoluzione dei problemi relativi alle macchine virtuali](../../virtual-machines/troubleshooting/index.yml) di AzureTo learn more about troubleshooting your VM, see Troubleshooting Azure virtual machines

* Controllare i [flussi di lavoro](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per cercare eventuali errori. Nel portale passare all'account di automazione e selezionare **Processi** in **Automazione processi**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: My custom runbook fails to start or stop my VMs

### <a name="issue"></a>Problema

Il runbook personalizzato creato o quello scaricato da PowerShell Gallery non funziona correttamente.

### <a name="cause"></a>Causa

Le cause dell'errore possono essere molte. Nel portale di Azure passare all'account di automazione e selezionare **Processi** in **Automazione processi**. Nella pagina **Processi** cercare eventuali errori nei processi del runbook.

### <a name="resolution"></a>Risoluzione

È consigliabile usare la [soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md) per avviare e arrestare le macchine virtuali in Automazione di Azure. Questa soluzione è stata creata da Microsoft. I runbook personalizzati non sono supportati da Microsoft. Si può provare a cercare una soluzione per il runbook personalizzato nell'articolo [Risoluzione dei problemi relativi ai runbook](runbooks.md), che contiene indicazioni generali e procedure per la risoluzione dei problemi per i runbook di tutti i tipi. Controllare i [flussi di lavoro](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per cercare eventuali errori. Nel portale passare all'account di automazione e selezionare **Processi** in **Automazione processi**.

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: le macchine virtuali non vengono avviate o interrotte nella sequenza correttaScenario: VMs don't start or stop in the correct sequence

### <a name="issue"></a>Problema

Le macchine virtuali configurate nella soluzione non vengono avviate o arrestate nella sequenza corretta.

### <a name="cause"></a>Causa

Il problema è causato dall'assegnazione di tag non corretti alle macchine virtuali.

### <a name="resolution"></a>Risoluzione

Eseguire la procedura seguente per verificare che la soluzione sia configurata correttamente.

1. Verificare che tutte le macchine virtuali da avviare o arrestare abbiano un tag `sequencestart` o `sequencestop`, a seconda della situazione. Questi tag devono avere come valore un numero intero positivo. Le macchine virtuali vengono elaborate in ordine crescente in base a questo valore.
2. Verificare che i gruppi di risorse delle macchine virtuali da avviare o arrestare siano nelle variabili `External_Start_ResourceGroupNames` o `External_Stop_ResourceGroupNames`, a seconda della situazione.
3. Testare le modifiche eseguendo il runbook `SequencedStartStop_Parent` con il parametro WHATIF impostato su True per visualizzare in anteprima le modifiche.

Per informazioni più dettagliate e istruzioni aggiuntive su come usare la soluzione per avviare e arrestare le macchine virtuali in sequenza, vedere [Avviare/arrestare le macchine virtuali in sequenza usando i tag](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>Scenario: processo di avvio/arresto della macchina virtuale non riuscito con stato 403 non consentitoScenario: Start/Stop VM job fails with 403 forbidden status

### <a name="issue"></a>Problema

Vengono rilevati processi non riusciti con errore `403 forbidden` per i runbook della soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività.

### <a name="cause"></a>Causa

Questo problema può essere causato da un account RunAs configurato in modo non corretto o scaduto. Può anche essere dovuto ad autorizzazioni inadeguate sulle risorse macchine virtuali dell'account RunAs nell'account di automazione.

### <a name="resolution"></a>Risoluzione

Per verificare che l'account RunAs sia configurato correttamente, passare al proprio account di automazione nel portale di Azure e selezionare **Account RunAs** in **Impostazioni account**. Qui è possibile visualizzare lo stato degli account RunAs, pertanto se un account non è configurato correttamente o è scaduto sarà indicato qui.

Se l'account RunAs non è configurato correttamente, è necessario eliminare e ricreare l'account RunAs. Vedere [Gestire gli account RunAs di Automazione di Azure.](../manage-runas-account.md)

Se il certificato dell'account RunAs è scaduto, seguire la procedura descritta in [Rinnovo del certificato autofirmato](../manage-runas-account.md#cert-renewal) per rinnovare il certificato.

Il problema può essere causato dall'assenza di autorizzazioni. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: Visualizzare i ruoli assegnati a un utente tramite il portale](../../role-based-access-control/check-access.md)di Azure. È necessario fornire l'ID applicazione per l'entità servizio utilizzata dall'account RunAs. È possibile recuperare questo valore passando al proprio account di automazione nel portale di Azure, selezionando **Account RunAs** in **Impostazioni account** e facendo clic sull'account RunAs appropriato.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenario: Il problema non è elencato sopra

### <a name="issue"></a>Problema

Durante l'uso della soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività si riscontra un problema o un risultato imprevisto non descritto in questa pagina.

### <a name="cause"></a>Causa

Spesso gli errori possono essere causati dall'uso di una versione vecchia e non aggiornata della soluzione.

> [!NOTE]
> La soluzione Avvia/Arresta macchine virtuali durante le ore non orariè è stata testata con i moduli di Azure importati nell'account di automazione quando si distribuisce la soluzione. La soluzione attualmente non funziona con le versioni più recenti del modulo di Azure.The solution currently does not work with newer versions of the Azure module. Ciò influisce solo sull'account di automazione usato per eseguire le macchine virtuali di avvio/arresto durante la soluzione fuori orario. È comunque possibile usare le versioni più recenti del modulo di Azure negli altri account di automazione, come descritto in Come aggiornare i moduli di [Azure PowerShell in Automazione di AzureYou](../automation-update-azure-modules.md) can still use newer versions of the Azure module in your other Automation Accounts, as described in How to update Azure PowerShell modules in Azure Automation

### <a name="resolution"></a>Risoluzione

Per risolvere diversi errori, è consigliabile rimuovere e aggiornare la soluzione. Per informazioni su come aggiornare la soluzione, vedere [Aggiornare la soluzione](../automation-solution-vm-management.md#update-the-solution). Inoltre, è possibile controllare [i flussi di lavoro](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per cercare eventuali errori. Nel portale passare all'account di automazione e selezionare **Processi** in **Automazione processi**.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
