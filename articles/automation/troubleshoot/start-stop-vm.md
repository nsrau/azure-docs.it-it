---
title: Risoluzione dei problemi relativi alla soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi relativi alla soluzione di avvio/arresto della macchina virtuale.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679154"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Risolvere i problemi della soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Questo articolo fornisce informazioni sulla risoluzione dei problemi che si verificano durante l'uso di macchine virtuali di avvio/arresto durante le ore di inattività.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: la soluzione di avvio/arresto di macchine virtuali durante gli orari di indisponibilità non riesce a distribuire correttamente

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

Le distribuzioni possono avere esito negativo a causa di uno dei motivi seguenti:

1. È già presente un account di automazione con lo stesso nome nell'area selezionata.
2. Un criterio non consente la distribuzione delle macchine virtuali di avvio/arresto durante le ore di indisponibilità.
3. Il `Microsoft.OperationsManagement`tipo `Microsoft.Insights`di risorsa `Microsoft.Automation` , o non è registrato.
4. L'area di lavoro Log Analytics è bloccata.
5. Si dispone di una versione obsoleta dei moduli AzureRM o della soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità.

### <a name="resolution"></a>Soluzione

Esaminare le correzioni seguenti per le potenziali soluzioni al problema:

* Gli account di Automazione devono essere univoci all'interno di un'area di Azure, anche se si trovano in gruppi di risorse diversi. Controllare gli account di automazione esistenti nell'area di destinazione.
* Un criterio esistente impedisce la distribuzione di una risorsa necessaria per la distribuzione della soluzione di avvio/arresto di macchine virtuali durante gli orari di indisponibilità. Passare alle assegnazioni dei criteri nel portale di Azure e controllare se è presente un'assegnazione dei criteri che non consente la distribuzione di questa risorsa. Per altre informazioni, vedere [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Per distribuire la soluzione di avvio/arresto di macchine virtuali, la sottoscrizione deve essere registrata negli spazi dei nomi delle risorse di Azure seguenti:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Per ulteriori informazioni sugli errori durante la registrazione dei provider, vedere [risolvere gli errori per la registrazione del provider di risorse](../../azure-resource-manager/templates/error-register-resource-provider.md) .
* Se è presente un blocco sull'area di lavoro Log Analytics, passare all'area di lavoro nel portale di Azure e rimuovere tutti i blocchi sulla risorsa.
* Se le risoluzioni precedenti non consentono di risolvere il problema, seguire le istruzioni in [aggiornare la soluzione](../automation-solution-vm-management.md#update-the-solution) per ridistribuire la soluzione di avvio/arresto.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: l'avvio o l'arresto di tutte le VM non riesce

### <a name="issue"></a>Problema

È stata configurata la soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità ma non avvia o arresta tutte le macchine virtuali.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

1. Una pianificazione non è configurata correttamente.
2. L'account RunAs potrebbe non essere configurato correttamente.
3. È possibile che si siano verificati errori in un Runbook.
4. È possibile che le macchine virtuali siano state escluse.

### <a name="resolution"></a>Soluzione

Esaminare l'elenco seguente per le possibili soluzioni per il problema:

* Verificare di aver configurato correttamente una pianificazione per la soluzione avvio/arresto di macchine virtuali durante gli orari di indisponibilità. Per informazioni su come configurare una pianificazione, vedere [Pianificazioni](../automation-schedules.md).

* Controllare i [flussi del processo](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per individuare eventuali errori. Cercare i processi da uno dei seguenti manuali operativi:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verificare che l' [account RunAs](../manage-runas-account.md) disponga delle autorizzazioni appropriate per le macchine virtuali che si sta tentando di avviare o arrestare. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: visualizzare i ruoli assegnati a un utente usando il portale di Azure](../../role-based-access-control/check-access.md). È necessario fornire l'ID applicazione per l'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di automazione nella portale di Azure, selezionando **account RunAs** in **Impostazioni account**e facendo clic sull'account RunAs appropriato.

* Se le macchine virtuali sono escluse in modo esplicito, non possono essere avviate o arrestate. Le macchine virtuali escluse vengono `External_ExcludeVMNames` impostate nella variabile dell'account di automazione in cui viene distribuita la soluzione. Nell'esempio seguente viene illustrato come è possibile eseguire una query su tale valore con PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: non è possibile avviare o arrestare alcune macchine virtuali

### <a name="issue"></a>Problema

È stata configurata la soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità, ma non avvia o arresta alcune delle macchine virtuali configurate.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

1. Nello scenario di sequenza un tag potrebbe essere mancante o errato.
2. La macchina virtuale potrebbe essere esclusa.
3. È possibile che l'account RunAs non disponga di autorizzazioni sufficienti per la macchina virtuale.
4. La macchina virtuale può avere un problema che ne ha impedito l'avvio o l'arresto.

### <a name="resolution"></a>Soluzione

Esaminare l'elenco seguente per individuare possibili soluzioni al problema o posizioni in cui cercare:

* Quando si usa lo [scenario di sequenza](../automation-solution-vm-management.md) della soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità, è necessario assicurarsi che ogni macchina virtuale che si vuole avviare o arrestare includa il tag appropriato. Verificare che le macchine virtuali da avviare abbiano il tag `sequencestart` e che quelle da arrestare abbiano il tag `sequencestop`. Entrambi i tag richiedono un valore intero positivo. È possibile usare una query simile all'esempio seguente per cercare tutte le macchine virtuali con i tag e i relativi valori.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Le macchine virtuali potrebbero non essere avviate o arrestate se sono esplicitamente escluse. Le macchine virtuali escluse vengono `External_ExcludeVMNames` impostate nella variabile dell'account di automazione in cui viene distribuita la soluzione. Nell'esempio seguente viene illustrato come è possibile eseguire una query su tale valore con PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Per avviare e arrestare le macchine virtuali, l'account RunAs per l'account di automazione deve avere le autorizzazioni appropriate per la macchina virtuale. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: visualizzare i ruoli assegnati a un utente usando il portale di Azure](../../role-based-access-control/check-access.md). È necessario fornire l'ID applicazione per l'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di automazione nella portale di Azure, selezionando account **RunAs** in **Impostazioni account** e facendo clic sull'account RunAs appropriato.

* Se si verifica un problema durante l'avvio o la deallocazione della macchina virtuale, potrebbe essersi verificato un problema nella macchina virtuale. Ad esempio, viene applicato un aggiornamento quando la macchina virtuale tenta di arrestarsi, un servizio si blocca e altro ancora. Passare alla risorsa macchina virtuale e verificare se nei **log attività** sono presenti errori. È anche possibile tentare di accedere alla macchina virtuale per verificare se sono presenti errori nei log eventi. Per altre informazioni sulla risoluzione dei problemi della VM, vedere [risoluzione dei problemi relativi alle macchine virtuali di Azure](../../virtual-machines/troubleshooting/index.yml)

* Controllare i [flussi del processo](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per individuare eventuali errori. Nel portale passare all'account di automazione e selezionare **processi** in **automazione processi**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: il Runbook personalizzato non riesce ad avviare o arrestare le macchine virtuali

### <a name="issue"></a>Problema

Il runbook personalizzato creato o quello scaricato da PowerShell Gallery non funziona correttamente.

### <a name="cause"></a>Causa

Le cause dell'errore possono essere molteplici. Passare all'account di automazione nella portale di Azure e selezionare **processi** in **automazione processi**. Nella pagina Processi cercare eventuali errori nei processi del runbook.

### <a name="resolution"></a>Soluzione

È consigliabile:

* Usare la [soluzione avvia/arresta macchine virtuali durante le ore](../automation-solution-vm-management.md) di indisponibilità per avviare e arrestare le macchine virtuali in automazione di Azure. Questa soluzione è stata creata da Microsoft. 

* Tenere presente che Microsoft non supporta manuali operativi personalizzati. È possibile trovare una soluzione per il Runbook personalizzato dalla [risoluzione dei problemi di Runbook](runbooks.md). Controllare i [flussi del processo](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per individuare eventuali errori. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: le macchine virtuali non vengono avviate o arrestate nella sequenza corretta

### <a name="issue"></a>Problema

Le macchine virtuali configurate nella soluzione non vengono avviate o arrestate nella sequenza corretta.

### <a name="cause"></a>Causa

Questo problema è causato da un contrassegno errato nelle VM.

### <a name="resolution"></a>Soluzione

Eseguire la procedura seguente per verificare che la soluzione sia configurata correttamente.

1. Verificare che tutte le macchine virtuali da avviare o arrestare abbiano un tag `sequencestart` o `sequencestop`, a seconda della situazione. Questi tag devono avere come valore un numero intero positivo. Le macchine virtuali vengono elaborate in ordine crescente in base a questo valore.
2. Verificare che i gruppi di risorse delle macchine virtuali da avviare o arrestare siano nelle variabili `External_Start_ResourceGroupNames` o `External_Stop_ResourceGroupNames`, a seconda della situazione.
3. Testare le modifiche eseguendo `SequencedStartStop_Parent` runbook con il `WHATIF` parametro impostato su true per visualizzare in anteprima le modifiche.
4. Per altre informazioni sull'uso della soluzione per avviare e arrestare le macchine virtuali in sequenza, vedere [avviare/arrestare macchine virtuali in sequenza](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: il processo di avvio/arresto di macchine virtuali durante le ore di inattività non riesce con 403 errore consentito

### <a name="issue"></a>Problema

Si trovano processi non riusciti con un `403 forbidden` errore per l'avvio/arresto di macchine virtuali durante gli orari di inattività manuali operativi soluzione.

### <a name="cause"></a>Causa

Questo problema può essere causato da un account RunAs configurato in modo errato o scaduto. Potrebbe anche essere dovuto a autorizzazioni non adeguate per le risorse delle macchine virtuali da parte dell'account RunAs.

### <a name="resolution"></a>Soluzione

Per verificare che l'account RunAs sia configurato correttamente, passare all'account di automazione nella portale di Azure e selezionare **account RunAs** in **Impostazioni account**. Se un account RunAs non è configurato correttamente o è scaduto, lo stato indica la condizione.

Se l'account RunAs non è configurato correttamente, è necessario eliminare e ricreare l'account RunAs. Vedere [gestire gli account RunAs di automazione di Azure](../manage-runas-account.md).

Se il certificato è scaduto per l'account RunAs, vedere la procedura di rinnovo del certificato [autofirmato](../manage-runas-account.md#cert-renewal) per rinnovare il certificato.

Se sono presenti autorizzazioni mancanti, vedere [Guida introduttiva: visualizzare i ruoli assegnati a un utente usando il portale di Azure](../../role-based-access-control/check-access.md). È necessario fornire l'ID applicazione per l'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di automazione nella portale di Azure, selezionando **account RunAs** in **Impostazioni account**e facendo clic sull'account RunAs appropriato.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenario: il problema non è elencato sopra

### <a name="issue"></a>Problema

Si verifica un problema o un risultato imprevisto quando si usa la soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità non elencata in questa pagina.

### <a name="cause"></a>Causa

Spesso gli errori possono essere causati dall'uso di una versione vecchia e non aggiornata della soluzione.

> [!NOTE]
> La soluzione avvio/arresto di macchine virtuali durante gli orari di indisponibilità è stata testata con i moduli di Azure che sono stati importati nell'account di automazione quando si distribuisce la soluzione. La soluzione attualmente non funziona con le versioni più recenti del modulo Azure. Questa operazione ha effetto solo sull'account di automazione usato per eseguire la soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità. È comunque possibile usare le versioni più recenti del modulo Azure negli altri account di automazione, come descritto in [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md)

### <a name="resolution"></a>Soluzione

Per risolvere molti errori, è consigliabile rimuovere e [aggiornare la soluzione avvio/arresto di macchine virtuali durante gli orari](../automation-solution-vm-management.md#update-the-solution)di indisponibilità. Inoltre, è possibile controllare i [flussi del processo](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) per individuare eventuali errori. 

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato in precedenza o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi a [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.