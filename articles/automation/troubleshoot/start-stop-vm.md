---
title: Risoluzione dei problemi relativi alla soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi relativi alla soluzione di avvio/arresto di macchine virtuali durante gli orari di indisponibilità.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 611e8441fab56114ca010d0b555c9ed156ae9d40
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855068"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Risolvere i problemi della soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Questo articolo fornisce informazioni sulla risoluzione dei problemi che si verificano quando si usano le VM di avvio/arresto di automazione di Azure durante le ore di inattività.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione di Azure, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: la soluzione di avvio/arresto di macchine virtuali durante gli orari di indisponibilità non riesce a distribuire correttamente

### <a name="issue"></a>Problema

Quando si distribuisce la [soluzione avvia/arresta macchine virtuali durante le ore](../automation-solution-vm-management.md)di indisponibilità, viene visualizzato uno degli errori seguenti:

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

- È già presente un account di automazione con lo stesso nome nell'area selezionata.
- Un criterio impedisce la distribuzione delle VM di avvio/arresto durante le ore di indisponibilità.
- Il `Microsoft.OperationsManagement`tipo `Microsoft.Insights`di risorsa `Microsoft.Automation` , o non è registrato.
- L'area di lavoro Log Analytics è bloccata.
- Si dispone di una versione obsoleta dei moduli AzureRM o della soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità.

### <a name="resolution"></a>Soluzione

Esaminare le correzioni seguenti per le potenziali soluzioni al problema:

* Gli account di automazione devono essere univoci all'interno di un'area di Azure, anche se si trovano in gruppi di risorse diversi. Controllare gli account di automazione esistenti nell'area di destinazione.
* Un criterio esistente impedisce la distribuzione di una risorsa necessaria per la distribuzione della soluzione di avvio/arresto di macchine virtuali durante gli orari di indisponibilità. Passare alle assegnazioni dei criteri nell'portale di Azure e verificare se si dispone di un'assegnazione di criteri che non consente la distribuzione di questa risorsa. Per altre informazioni, vedere [errore RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Per distribuire la soluzione di avvio/arresto di macchine virtuali, la sottoscrizione deve essere registrata negli spazi dei nomi delle risorse di Azure seguenti:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Per altre informazioni sugli errori durante la registrazione dei provider, vedere [risolvere gli errori per la registrazione del provider di risorse](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Se è presente un blocco sull'area di lavoro Log Analytics, passare all'area di lavoro nel portale di Azure e rimuovere tutti i blocchi sulla risorsa.
* Se queste soluzioni non consentono di risolvere il problema, seguire le istruzioni in [aggiornare la soluzione](../automation-solution-vm-management.md#update-the-solution) per ridistribuire la soluzione avvio/arresto di macchine virtuali durante gli orari di indisponibilità.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: l'avvio o l'arresto di tutte le VM non riesce

### <a name="issue"></a>Problema

È stata configurata la soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità, ma non avvia né arresta tutte le macchine virtuali.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

- Una pianificazione non è configurata correttamente.
- L'account RunAs potrebbe non essere configurato correttamente.
- È possibile che si siano verificati errori in un Runbook.
- È possibile che le macchine virtuali siano state escluse.

### <a name="resolution"></a>Soluzione

Esaminare l'elenco seguente per le possibili soluzioni per il problema:

* Verificare di aver configurato correttamente una pianificazione per la soluzione avvio/arresto di macchine virtuali durante gli orari di indisponibilità. Per informazioni su come configurare una pianificazione, vedere [pianificazioni](../automation-schedules.md).

* Controllare i [flussi del processo](../automation-runbook-execution.md#job-statuses) per individuare eventuali errori. Cercare i processi da uno dei seguenti manuali operativi:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verificare che l' [account RunAs](../manage-runas-account.md) disponga delle autorizzazioni appropriate per le macchine virtuali che si sta tentando di avviare o arrestare. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: visualizzare i ruoli assegnati a un utente usando il portale di Azure](../../role-based-access-control/check-access.md). È necessario fornire l'ID applicazione per l'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di automazione nella portale di Azure. Selezionare account **RunAs** in **Impostazioni account**e selezionare l'account RunAs appropriato.

* Le macchine virtuali potrebbero non essere avviate o arrestate se sono esplicitamente escluse. Le macchine virtuali escluse vengono `External_ExcludeVMNames` impostate nella variabile dell'account di automazione in cui viene distribuita la soluzione. Nell'esempio seguente viene illustrato come è possibile eseguire una query su tale valore con PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: non è possibile avviare o arrestare alcune macchine virtuali

### <a name="issue"></a>Problema

È stata configurata la soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità, ma non avvia o arresta alcune delle macchine virtuali configurate.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

- Nello scenario di sequenza un tag potrebbe essere mancante o errato.
- La macchina virtuale potrebbe essere esclusa.
- È possibile che l'account RunAs non disponga di autorizzazioni sufficienti per la macchina virtuale.
- La macchina virtuale può avere un problema che ne ha impedito l'avvio o l'arresto.

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

* Per avviare e arrestare le macchine virtuali, l'account RunAs per l'account di automazione deve avere le autorizzazioni appropriate per la macchina virtuale. Per informazioni su come controllare le autorizzazioni per una risorsa, vedere [Guida introduttiva: visualizzare i ruoli assegnati a un utente usando il portale di Azure](../../role-based-access-control/check-access.md). È necessario fornire l'ID applicazione per l'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di automazione nella portale di Azure. Selezionare account **RunAs** in **Impostazioni account** e selezionare l'account RunAs appropriato.
* Se si verifica un problema durante l'avvio o la deallocazione della macchina virtuale, potrebbe essersi verificato un problema nella macchina virtuale. Gli esempi sono un aggiornamento che viene applicato quando la macchina virtuale tenta di arrestarsi, un servizio che si blocca e altro ancora. Passare alla risorsa VM e controllare i **log attività** per verificare se sono presenti errori nei log. È anche possibile tentare di accedere alla macchina virtuale per verificare se sono presenti errori nei log eventi. Per altre informazioni sulla risoluzione dei problemi della VM, vedere [risoluzione dei problemi relativi alle macchine virtuali di Azure](../../virtual-machines/troubleshooting/index.yml).
* Controllare i [flussi del processo](../automation-runbook-execution.md#job-statuses) per individuare eventuali errori. Nel portale passare all'account di automazione e selezionare **processi** in **automazione processi**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: il Runbook personalizzato non riesce ad avviare o arrestare le macchine virtuali

### <a name="issue"></a>Problema

È stato creato un Runbook personalizzato o ne è stato scaricato uno dal PowerShell Gallery e non funziona correttamente.

### <a name="cause"></a>Causa

Le cause dell'errore possono essere molteplici. Passare all'account di automazione nella portale di Azure e selezionare **processi** in **automazione processi**. Nella pagina **Processi** cercare eventuali errori nei processi del runbook.

### <a name="resolution"></a>Soluzione

È consigliabile:

* Usare la [soluzione avvia/arresta macchine virtuali durante le ore](../automation-solution-vm-management.md) di indisponibilità per avviare e arrestare le macchine virtuali in automazione di Azure. Questa soluzione è stata creata da Microsoft. 
* Tenere presente che Microsoft non supporta manuali operativi personalizzati. È possibile trovare una soluzione per il Runbook personalizzato dalla [risoluzione dei problemi di Runbook](runbooks.md). Controllare i [flussi del processo](../automation-runbook-execution.md#job-statuses) per individuare eventuali errori. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: le macchine virtuali non vengono avviate o arrestate nella sequenza corretta

### <a name="issue"></a>Problema

Le macchine virtuali configurate nella soluzione non vengono avviate o arrestate nella sequenza corretta.

### <a name="cause"></a>Causa

Questo problema è causato da un contrassegno errato nelle VM.

### <a name="resolution"></a>Soluzione

Per assicurarsi che la soluzione sia configurata correttamente, seguire questa procedura.

1. Verificare che tutte le macchine virtuali da avviare o arrestare abbiano un tag `sequencestart` o `sequencestop`, a seconda della situazione. Questi tag devono avere come valore un numero intero positivo. Le macchine virtuali vengono elaborate in ordine crescente in base a questo valore.
1. Verificare che i gruppi di risorse delle macchine virtuali da avviare o arrestare siano nelle variabili `External_Start_ResourceGroupNames` o `External_Stop_ResourceGroupNames`, a seconda della situazione.
1. Testare le modifiche eseguendo `SequencedStartStop_Parent` runbook con il `WHATIF` parametro impostato su true per visualizzare in anteprima le modifiche.

Per altre informazioni su come usare la soluzione per avviare e arrestare le macchine virtuali in sequenza, vedere [avviare/arrestare macchine virtuali in sequenza](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: il processo di avvio/arresto di macchine virtuali durante le ore di inattività non riesce con 403 errore consentito

### <a name="issue"></a>Problema

Si trovano processi non riusciti con un `403 forbidden` errore per l'avvio/arresto di macchine virtuali durante gli orari di inattività manuali operativi soluzione.

### <a name="cause"></a>Causa

Questo problema può essere causato da un account RunAs configurato in modo errato o scaduto. Potrebbe anche essere dovuto a autorizzazioni non adeguate per le risorse delle macchine virtuali da parte dell'account RunAs.

### <a name="resolution"></a>Soluzione

Per verificare che l'account RunAs sia configurato correttamente, passare all'account di automazione nella portale di Azure e selezionare **account RunAs** in **Impostazioni account**. Se un account RunAs non è configurato correttamente o è scaduto, lo stato indica la condizione.

Se l'account RunAs non è configurato correttamente, eliminare e ricreare l'account RunAs. Per altre informazioni, vedere [gestire gli account RunAs di automazione di Azure](../manage-runas-account.md).

Se il certificato è scaduto per l'account RunAs, attenersi alla procedura descritta in [rinnovo del certificato autofirmato](../manage-runas-account.md#cert-renewal) per rinnovare il certificato.

Se sono presenti autorizzazioni mancanti, vedere [Guida introduttiva: visualizzare i ruoli assegnati a un utente usando il portale di Azure](../../role-based-access-control/check-access.md). È necessario fornire l'ID applicazione per l'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di automazione nella portale di Azure. Selezionare account **RunAs** in **Impostazioni account**e selezionare l'account RunAs appropriato.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenario: il problema non è elencato qui

### <a name="issue"></a>Problema

Si verifica un problema o un risultato imprevisto quando si usa la soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità non elencata in questa pagina.

### <a name="cause"></a>Causa

Spesso gli errori possono essere causati dall'uso di una versione vecchia e non aggiornata della soluzione.

> [!NOTE]
> La soluzione avvio/arresto di macchine virtuali durante gli orari di indisponibilità è stata testata con i moduli di Azure che sono stati importati nell'account di automazione quando si distribuisce la soluzione. La soluzione attualmente non funziona con le versioni più recenti del modulo Azure. Questa restrizione influiscono solo sull'account di automazione usato per eseguire la soluzione avvia/arresta macchine virtuali durante le ore di indisponibilità. È comunque possibile usare le versioni più recenti del modulo Azure negli altri account di automazione, come descritto in [come aggiornare Azure PowerShell moduli in automazione di Azure](../automation-update-azure-modules.md).

### <a name="resolution"></a>Soluzione

Per risolvere molti errori, rimuovere e [aggiornare la soluzione avvio/arresto di macchine virtuali durante gli orari](../automation-solution-vm-management.md#update-the-solution)di indisponibilità. È anche possibile controllare i [flussi del processo](../automation-runbook-execution.md#job-statuses) per individuare eventuali errori. 

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato qui o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto tecnico di Azure connette la community di Azure a risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/)e selezionare **ottenere supporto**.