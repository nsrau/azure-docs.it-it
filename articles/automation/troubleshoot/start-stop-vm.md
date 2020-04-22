---
title: Risoluzione dei problemi relativi alla soluzione Start/Stop VMs durante le ore non lavorativeTroubleshooting the Start/Stop VMs during off hours solution
description: In questo articolo vengono fornite informazioni sulla risoluzione dei problemi relativi alla soluzione Start/Stop VM.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679154"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Risolvere i problemi della soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Questo articolo fornisce informazioni sulla risoluzione dei problemi che si verificano durante l'utilizzo di Start/Stop VM durante la soluzione fuori orario.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: la soluzione Start/Stop VMs durante le ore non lavorative non viene distribuita correttamente

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

Le distribuzioni possono avere esito negativo a causa di uno dei motivi seguenti:Deployments can fail due due of the following reasons:

1. Esiste già un account di automazione con lo stesso nome nell'area selezionata.
2. Un criterio non consente la distribuzione delle macchine virtuali di avvio/arresto durante la soluzione fuori orario.
3. Il `Microsoft.OperationsManagement` `Microsoft.Insights`tipo `Microsoft.Automation` di risorsa , , o non è registrato.
4. L'area di lavoro di Log Analytics è bloccata.
5. Si dispone di una versione obsoleta dei moduli di AzureRM o start/stop macchine virtuali durante la soluzione fuori orario.

### <a name="resolution"></a>Risoluzione

Esaminare le seguenti correzioni per le potenziali soluzioni al problema:

* Gli account di Automazione devono essere univoci all'interno di un'area di Azure, anche se si trovano in gruppi di risorse diversi. Controllare gli account di automazione esistenti nell'area di destinazione.
* Un criterio esistente impedisce la distribuzione di una risorsa necessaria per la distribuzione della soluzione Start/Stop VMs durante la distribuzione della soluzione fuori orario. Passare alle assegnazioni dei criteri nel portale di Azure e controllare se è presente un'assegnazione dei criteri che non consente la distribuzione di questa risorsa. Per altre informazioni, vedere [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Per distribuire la soluzione Start/Stop VMs, la sottoscrizione deve essere registrata negli spazi dei nomi delle risorse di Azure seguenti:To deploy the Start/Stop VMs solution, your subscription needs to be registered to the following Azure resource namespaces:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Per ulteriori informazioni sugli errori durante la registrazione dei provider, vedere [Risolvere gli errori per la registrazione del provider di risorse.](../../azure-resource-manager/templates/error-register-resource-provider.md)
* Se è presente un blocco sull'area di lavoro Log Analytics, passare all'area di lavoro nel portale di Azure e rimuovere tutti i blocchi sulla risorsa.
* Se le risoluzioni precedenti non risolvono il problema, seguire le istruzioni in [Aggiornare la soluzione](../automation-solution-vm-management.md#update-the-solution) per ridistribuire la soluzione Start/Stop.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: avvio o arresto di tutte le macchine virtualiScenario: All VMs fail to start or stop

### <a name="issue"></a>Problema

La soluzione Start/Stop VMs durante le ore non lavorative è stata configurata, ma non avvia o arresta tutte le macchine virtuali.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

1. Una pianificazione non è configurata correttamente.
2. L'account RunAs potrebbe non essere configurato correttamente.
3. Un runbook potrebbe essersi verificato errori.
4. Le macchine virtuali potrebbero essere state escluse.

### <a name="resolution"></a>Risoluzione

Esaminare l'elenco seguente per le potenziali soluzioni al problema:Review the following list for potential solutions to your problem:

* Verificare di aver configurato correttamente una pianificazione per la soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative. Per informazioni su come configurare una pianificazione, vedere [Pianificazioni](../automation-schedules.md).

* Controllare i [flussi di lavoro](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per cercare eventuali errori. Cercare i processi da uno dei seguenti runbook:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verificare che [l'account RunAs](../manage-runas-account.md) disponga delle autorizzazioni appropriate per le macchine virtuali che si sta tentando di avviare o arrestare. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: Visualizzare i ruoli assegnati a un utente tramite il portale](../../role-based-access-control/check-access.md)di Azure. È necessario fornire l'ID applicazione per l'entità servizio utilizzata dall'account RunAs. È possibile recuperare questo valore accedendo all'account di automazione nel portale di Azure, selezionando **Esegui come account** in Impostazioni **account**e facendo clic sull'account RunAs appropriato.

* Se le macchine virtuali sono escluse in modo esplicito, non possono essere avviate o arrestate. Le macchine virtuali escluse vengono impostate nella `External_ExcludeVMNames` variabile nell'account di automazione in cui viene distribuita la soluzione. Nell'esempio seguente viene illustrato come eseguire una query su tale valore con PowerShell.The following example shows how you can query that value with PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: alcune macchine virtuali non vengono avviate o interrotteScenario: Some of my VMs fail to start or stop

### <a name="issue"></a>Problema

La soluzione Start/Stop VMs durante le ore non lavorative è stata configurata, ma non avvia o arresta alcune delle macchine virtuali configurate.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

1. Nello scenario di sequenza, un tag potrebbe essere mancante o non corretto.
2. La macchina virtuale potrebbe essere esclusa.
3. L'account RunAs potrebbe non disporre di autorizzazioni sufficienti per la macchina virtuale.
4. La macchina virtuale può avere un problema che ne ha impedito l'avvio o l'arresto.

### <a name="resolution"></a>Risoluzione

Esaminare l'elenco seguente per individuare possibili soluzioni al problema o posizioni in cui cercare:

* Quando si usa lo scenario di [sequenza](../automation-solution-vm-management.md) della soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative, è necessario assicurarsi che ogni macchina virtuale che si vuole avviare o arrestare disponga del tag appropriato. Verificare che le macchine virtuali da avviare abbiano il tag `sequencestart` e che quelle da arrestare abbiano il tag `sequencestop`. Entrambi i tag richiedono un valore intero positivo. È possibile usare una query simile all'esempio seguente per cercare tutte le macchine virtuali con i tag e i relativi valori.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Le macchine virtuali potrebbero non essere avviate o arrestate se vengono escluse in modo esplicito. Le macchine virtuali escluse vengono impostate nella `External_ExcludeVMNames` variabile nell'account di automazione in cui viene distribuita la soluzione. Nell'esempio seguente viene illustrato come eseguire una query su tale valore con PowerShell.The following example shows how you can query that value with PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Per avviare e arrestare le macchine virtuali, l'account RunAs per l'account di automazione deve disporre delle autorizzazioni appropriate per la macchina virtuale. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: Visualizzare i ruoli assegnati a un utente tramite il portale](../../role-based-access-control/check-access.md)di Azure. È necessario fornire l'ID applicazione per l'entità servizio utilizzata dall'account RunAs. È possibile recuperare questo valore accedendo all'account di automazione nel portale di Azure, selezionando **Esegui come account** in Impostazioni **account** e facendo clic sull'account RunAs appropriato.

* Se la macchina virtuale ha un problema durante l'avvio o la deallocazione, potrebbe essersi verificato un problema nella macchina virtuale stessa. Ad esempio, un aggiornamento viene applicato quando la macchina virtuale sta tentando di arrestare, un servizio si blocca e altro ancora. Passare alla risorsa macchina virtuale e verificare se nei **log attività** sono presenti errori. È anche possibile tentare di accedere alla macchina virtuale per verificare se sono presenti errori nei registri eventi. Per altre informazioni sulla risoluzione dei problemi della macchina virtuale, vedere [Risoluzione dei problemi relativi alle macchine virtuali](../../virtual-machines/troubleshooting/index.yml) di AzureTo learn more about troubleshooting your VM, see Troubleshooting Azure virtual machines

* Controllare i [flussi di lavoro](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per cercare eventuali errori. Nel portale passare all'account di automazione e selezionare **Processi** in **Automazione processo**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: My custom runbook fails to start or stop my VMs

### <a name="issue"></a>Problema

Il runbook personalizzato creato o quello scaricato da PowerShell Gallery non funziona correttamente.

### <a name="cause"></a>Causa

Ci possono essere molte cause per l'errore. Passare all'account di automazione nel portale di Azure e selezionare **Processi** in **Automazione processo**. Nella pagina Processi cercare eventuali errori nei processi del runbook.

### <a name="resolution"></a>Risoluzione

Si consiglia di:

* Usare la [soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative](../automation-solution-vm-management.md) per avviare e arrestare le macchine virtuali in Automazione di Azure.Use the Start/Stop VMs during off hours solution to start and stop VMs in Azure Automation. Questa soluzione è stata creata da Microsoft. 

* Tieni presente che Microsoft non supporta runbook personalizzati. È possibile trovare una soluzione per il runbook personalizzato dalla [risoluzione dei problemi](runbooks.md)del runbook . Controllare i [flussi di lavoro](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per cercare eventuali errori. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: le macchine virtuali non vengono avviate o interrotte nella sequenza correttaScenario: VMs don't start or stop in the correct sequence

### <a name="issue"></a>Problema

Le macchine virtuali configurate nella soluzione non vengono avviate o arrestate nella sequenza corretta.

### <a name="cause"></a>Causa

Questo problema è causato da tag non corretti nelle macchine virtuali.

### <a name="resolution"></a>Risoluzione

Eseguire la procedura seguente per verificare che la soluzione sia configurata correttamente.

1. Verificare che tutte le macchine virtuali da avviare o arrestare abbiano un tag `sequencestart` o `sequencestop`, a seconda della situazione. Questi tag devono avere come valore un numero intero positivo. Le macchine virtuali vengono elaborate in ordine crescente in base a questo valore.
2. Verificare che i gruppi di risorse delle macchine virtuali da avviare o arrestare siano nelle variabili `External_Start_ResourceGroupNames` o `External_Stop_ResourceGroupNames`, a seconda della situazione.
3. Testare le modifiche `SequencedStartStop_Parent` eseguendo il `WHATIF` runbook con il parametro impostato su True per visualizzare in anteprima le modifiche.
4. Per altre informazioni sull'uso della soluzione per avviare e arrestare le macchine virtuali in [sequenza, vedere Avviare/arrestare le macchine virtuali in sequenza.](../automation-solution-vm-management.md)

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: Avvio/arresto di macchine virtuali durante il processo fuori orario non riesce con errore 403 non consentito

### <a name="issue"></a>Problema

Si trova processi non `403 forbidden` riusciti con un errore per start/stop macchine virtuali durante i runbook della soluzione fuori orario.

### <a name="cause"></a>Causa

Questo problema può essere causato da un account RunAs configurato in modo non corretto o scaduto. Potrebbe anche essere a causa di autorizzazioni inadeguate per le risorse della macchina virtuale da parte dell'account RunAs.It might also be of unadequate permissions to the VM resources by the RunAs account.

### <a name="resolution"></a>Risoluzione

Per verificare che l'account RunAs sia configurato correttamente, passare all'account di automazione nel portale di Azure e selezionare **Esegui come account** in Impostazioni **account**. Se un account RunAs non è configurato correttamente o è scaduto, lo stato mostra la condizione.

Se l'account RunAs non è configurato correttamente, è necessario eliminare e ricreare l'account RunAs. Vedere [Gestire gli account RunAs di Automazione di Azure.](../manage-runas-account.md)

Se il certificato è scaduto per l'account RunAs, vedere i passaggi descritti in [Rinnovo del certificato autofirmato](../manage-runas-account.md#cert-renewal) per rinnovare il certificato.

Se mancano le autorizzazioni, vedere [Guida introduttiva: Visualizzare i ruoli assegnati a un utente tramite il portale](../../role-based-access-control/check-access.md)di Azure.If there are missing permissions, see Quickstart: View roles assigned to a user using the Azure portal . È necessario fornire l'ID applicazione per l'entità servizio utilizzata dall'account RunAs. È possibile recuperare questo valore accedendo all'account di automazione nel portale di Azure, selezionando **Esegui come account** in Impostazioni **account**e facendo clic sull'account RunAs appropriato.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenario: Il problema non è elencato sopra

### <a name="issue"></a>Problema

Si verifica un problema o un risultato imprevisto quando si utilizza la soluzione Start/Stop macchine virtuali durante le ore non orari che non è elencato in questa pagina.

### <a name="cause"></a>Causa

Spesso gli errori possono essere causati dall'uso di una versione vecchia e non aggiornata della soluzione.

> [!NOTE]
> La soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative è stata testata con i moduli di Azure importati nell'account di automazione quando si distribuisce la soluzione. La soluzione attualmente non funziona con le versioni più recenti del modulo di Azure.The solution currently doesn't work with newer versions of the Azure module. Ciò influisce solo sull'account di automazione usato per eseguire la soluzione Start/Stop VMs durante le ore non lavorative. È comunque possibile usare le versioni più recenti del modulo di Azure negli altri account di automazione, come descritto in Come aggiornare i moduli di [Azure PowerShell in Automazione di AzureYou](../automation-update-azure-modules.md) can still use newer versions of the Azure module in your other Automation accounts, as described in How to update Azure PowerShell modules in Azure Automation

### <a name="resolution"></a>Risoluzione

Per risolvere molti errori, è consigliabile rimuovere e [aggiornare la soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative.](../automation-solution-vm-management.md#update-the-solution) Inoltre, è possibile controllare [i flussi di lavoro](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per cercare eventuali errori. 

## <a name="next-steps"></a>Passaggi successivi

Se non vedi il problema sopra o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.