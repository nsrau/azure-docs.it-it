---
title: Risolvere i problemi di Avvio/Arresto di macchine virtuali durante gli orari di minore attività in Automazione di Azure
description: Questo articolo spiega come risolvere i problemi che si verificano durante l'uso della funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bb8fa53fa07d666693ae545c193faaf3d6d0a30c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187150"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Risolvere i problemi relativi ad Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Questo articolo fornisce informazioni sulla risoluzione dei problemi che si verificano quando si distribuisce la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività in Automazione di Azure nelle VM. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: Impossibile distribuire Avvio/Arresto di macchine virtuali durante gli orari di minore attività

### <a name="issue"></a>Problema

Quando si distribuisce [Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md), viene visualizzato uno degli errori seguenti:

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

- Esiste già un account di Automazione con lo stesso nome nell'area selezionata.
- Un criterio non consente la distribuzione di Avvio/Arresto di macchine virtuali durante gli orari di minore attività.
- Il tipo di risorsa `Microsoft.OperationsManagement`, `Microsoft.Insights` o `Microsoft.Automation` non è registrato.
- L'area di lavoro Log Analytics è bloccata.
- Si dispone di una versione obsoleta dei moduli AzureRM o della funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività.

### <a name="resolution"></a>Risoluzione

Esaminare le seguenti correzioni per le possibili risoluzioni:

* Gli account di Automazione devono essere univoci all'interno di un'area di Azure, anche se si trovano in gruppi di risorse diversi. Controllare gli account di Automazione esistenti nell'area di destinazione.
* Un criterio esistente impedisce la distribuzione di una risorsa necessaria per la distribuzione di Avvio/Arresto di macchine virtuali durante gli orari di minore attività. Passare alle assegnazioni dei criteri nel portale di Azure e controllare se è presente un'assegnazione dei criteri che non consente la distribuzione di questa risorsa. Per altre informazioni, vedere [Errore RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Per distribuire Avvio/Arresto di macchine virtuali durante gli orari di minore attività, la sottoscrizione deve essere registrata negli spazi dei nomi delle risorse di Azure seguenti:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Per altre informazioni sugli errori di registrazione dei provider, vedere [Risolvere gli errori di registrazione del provider di risorse](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Se è presente un blocco sull'area di lavoro Log Analytics, passare all'area di lavoro nel portale di Azure e rimuovere tutti i blocchi sulla risorsa.
* Se queste soluzioni non consentono di risolvere il problema, seguire le istruzioni riportate in [ggiornare la soluzione](../automation-solution-vm-management.md#update-the-feature) per ridistribuire Avvio/Arresto di macchine virtuali durante gli orari di minore attività.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: impossibile avviare o arrestare tutte le macchine virtuali

### <a name="issue"></a>Problema

La soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività è stata configurata, ma non avvia o arresta tutte le macchine virtuali.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

- Una pianificazione non è configurata correttamente.
- L'account RunAs potrebbe non essere configurato correttamente.
- Potrebbero essersi verificati errori in un runbook.
- Le macchine virtuali potrebbero essere state escluse.

### <a name="resolution"></a>Risoluzione

Esaminare l'elenco seguente per le possibili risoluzioni:

* Verificare di aver configurato correttamente una pianificazione per Avvio/Arresto di macchine virtuali durante gli orari di minore attività. Per informazioni su come configurare una pianificazione, vedere [Pianificazioni](../shared-resources/schedules.md).

* Cercare eventuali errori nei [flussi di processo](../automation-runbook-execution.md#job-statuses). Cercare i processi di uno dei runbook seguenti:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verificare che il proprio [account RunAs](../manage-runas-account.md) abbia le autorizzazioni appropriate sulle macchine virtuali che si tenta di avviare o arrestare. Per informazioni su come controllare le autorizzazioni su una risorsa, vedere [Avvio rapido - Visualizzare i ruoli assegnati a un utente tramite il portale di Azure](../../role-based-access-control/check-access.md). È necessario specificare l'ID applicazione dell'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di Automazione nel portale di Azure. Selezionare **Account RunAs** in **Impostazioni account** e selezionare l'account RunAs appropriato.

* Se le macchine virtuali sono escluse in modo esplicito, non possono essere avviate o arrestate. Le macchine virtuali escluse vengono impostate nella variabile `External_ExcludeVMNames` nell'account di Automazione in cui viene distribuita la funzionalità. L'esempio seguente mostra come eseguire una query su tale valore con PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: non è possibile avviare o arrestare alcune macchine virtuali

### <a name="issue"></a>Problema

La soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività è stata configurata, ma non avvia o arresta alcune macchine virtuali configurate.

### <a name="cause"></a>Causa

Questo errore può dipendere da una delle cause seguenti:

- Nello scenario in sequenza un tag potrebbe mancare o non essere corretto.
- La macchina virtuale potrebbe essere stata esclusa.
- L'account RunAs potrebbe non avere autorizzazioni sufficienti sulla macchina virtuale.
- La macchina virtuale potrebbe avere un problema che ne ha impedito l'avvio o l'arresto.

### <a name="resolution"></a>Risoluzione

Esaminare l'elenco seguente per le possibili risoluzioni:

* Quando si usa lo [scenario in sequenza](../automation-solution-vm-management.md) di Avvio/Arresto di macchine virtuali durante gli orari di minore attività, occorre verificare che ogni macchina virtuale che si vuole avviare o arrestare abbia il tag appropriato. Verificare che le macchine virtuali da avviare abbiano il tag `sequencestart` e che quelle da arrestare abbiano il tag `sequencestop`. Entrambi i tag richiedono un valore intero positivo. È possibile usare una query simile all'esempio seguente per cercare tutte le macchine virtuali con i tag e i relativi valori.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Se le macchine virtuali sono escluse in modo esplicito, non possono essere avviate o arrestate. Le macchine virtuali escluse vengono impostate nella variabile `External_ExcludeVMNames` nell'account di Automazione in cui viene distribuita la funzionalità. L'esempio seguente mostra come eseguire una query su tale valore con PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Per avviare e arrestare macchine virtuali, l'account RunAs per l'account di Automazione deve avere le autorizzazioni appropriate sulla macchina virtuale. Per informazioni su come controllare le autorizzazioni su una risorsa, vedere [Avvio rapido - Visualizzare i ruoli assegnati a un utente tramite il portale di Azure](../../role-based-access-control/check-access.md). È necessario specificare l'ID applicazione dell'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di Automazione nel portale di Azure. Selezionare **Account RunAs** in **Impostazioni account** e selezionare l'account RunAs appropriato.
* Se la macchina virtuale mostra un problema di avvio o deallocazione, potrebbe essere dovuto a un problema nella macchina virtuale stessa. Tra gli esempi rientrano un aggiornamento che viene applicato quando la macchina virtuale tenta di arrestarsi, un servizio che si blocca e altro ancora. Passare alla risorsa macchina virtuale e verificare se nei **log attività** sono presenti errori. Si può anche provare ad accedere alla macchina virtuale per verificare se sono presenti errori nei log eventi. Per altre informazioni sulla risoluzione dei problemi della VM, vedere la [risoluzione dei problemi relativi alle macchine virtuali di Azure](../../virtual-machines/troubleshooting/index.yml).
* Cercare eventuali errori nei [flussi di processo](../automation-runbook-execution.md#job-statuses). Nel portale passare all'account di Automazione e selezionare **Processi** in **Automazione processi**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: Il runbook personalizzato non riesce ad avviare o arrestare le macchine virtuali

### <a name="issue"></a>Problema

Il runbook personalizzato creato o quello scaricato da PowerShell Gallery non funziona correttamente.

### <a name="cause"></a>Causa

Le cause dell'errore possono essere molteplici. Nel portale di Azure passare all'account di Automazione e selezionare **Processi** in **Automazione processi**. Nella pagina **Processi** cercare eventuali errori nei processi del runbook.

### <a name="resolution"></a>Risoluzione

È consigliabile:

* Usare [Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md) per avviare e arrestare macchine virtuali  in Automazione di Azure. 
* Tenere presente che Microsoft non supporta runbook personalizzati. È possibile trovare una soluzione per il runbook personalizzato in [Risolvere gli errori dei runbook](runbooks.md). Cercare eventuali errori nei [flussi di processo](../automation-runbook-execution.md#job-statuses). 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: le macchine virtuali non vengono avviate o arrestate nella sequenza corretta

### <a name="issue"></a>Problema

Le macchine virtuali abilitate per la funzionalità non vengono avviate o arrestate nella sequenza corretta.

### <a name="cause"></a>Causa

Il problema è causato dall'assegnazione di tag non corretti alle macchine virtuali.

### <a name="resolution"></a>Risoluzione

Per verificare che la funzionalità sia abilitata correttamente, attenersi alla procedura seguente:

1. Verificare che tutte le macchine virtuali da avviare o arrestare abbiano un tag `sequencestart` o `sequencestop`, a seconda della situazione. Questi tag devono avere come valore un numero intero positivo. Le macchine virtuali vengono elaborate in ordine crescente in base a questo valore.
1. Verificare che i gruppi di risorse delle macchine virtuali da avviare o arrestare siano nelle variabili `External_Start_ResourceGroupNames` o `External_Stop_ResourceGroupNames`, a seconda della situazione.
1. Testare le modifiche eseguendo il runbook **SequencedStartStop_Parent** con il parametro `WHATIF` impostato su True per visualizzare in anteprima le modifiche.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: il processo Avvio/Arresto di macchine virtuali durante gli orari di minore attività ha esito negativo con errore 403 forbidden

### <a name="issue"></a>Problema

Vengono rilevati processi non riusciti con errore `403 forbidden` per i runbook di Avvio/Arresto di macchine virtuali durante gli orari di minore attività.

### <a name="cause"></a>Causa

Questo problema può essere causato da un account RunAs configurato in modo non corretto o scaduto. Può anche essere dovuto ad autorizzazioni inadeguate sulle risorse macchine virtuali dell'account RunAs.

### <a name="resolution"></a>Risoluzione

Per verificare che l'account RunAs sia configurato correttamente, passare al proprio account di Automazione nel portale di Azure e selezionare **Account RunAs** in **Impostazioni account**. Se un account RunAs non è configurato correttamente o è scaduto, lo stato indica la condizione.

Se l'account RunAs è configurato in modo errato, eliminarlo e ricrearlo. Per altre informazioni, vedere [Gestire account RunAs di Automazione di Azure](../manage-runas-account.md).

Se il certificato dell'account RunAs è scaduto, seguire la procedura descritta in [Rinnovo di un certificato autofirmato](../manage-runas-account.md#cert-renewal) per rinnovare il certificato.

Se sono presenti autorizzazioni mancanti, vedere [Guida introduttiva: Visualizzare i ruoli assegnati a un utente tramite il portale di Azure](../../role-based-access-control/check-access.md). È necessario specificare l'ID applicazione dell'entità servizio usata dall'account RunAs. È possibile recuperare questo valore passando all'account di Automazione nel portale di Azure. Selezionare **Account RunAs** in **Impostazioni account** e selezionare l'account RunAs appropriato.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenario: il problema non è incluso qui

### <a name="issue"></a>Problema

Durante l'uso di Avvio/Arresto di macchine virtuali durante gli orari di minore attività si riscontra un problema o un risultato imprevisto non descritto in questa pagina.

### <a name="cause"></a>Causa

Spesso gli errori possono essere causati dall'uso di una versione precedente e non aggiornata della funzionalità.

> [!NOTE]
> La funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività è stata testata con i moduli di Azure importati nell'account di Automazione quando si distribuisce la funzionalità nelle macchine virtuali. La funzionalità attualmente non funziona con le versioni più recenti del modulo di Azure. Questa restrizione riguarda solo l'account di Automazione usato per eseguire Avvio/Arresto di macchine virtuali durante gli orari di minore attività. È comunque possibile usare le versioni più recenti del modulo di Azure negli altri account di Automazione, come descritto in [Aggiornare i moduli Azure PowerShell](../automation-update-azure-modules.md).

### <a name="resolution"></a>Risoluzione

Per risolvere molti errori, rimuovere e [aggiornare Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md#update-the-feature). È anche possibile cercare eventuali errori nei [flussi di processo](../automation-runbook-execution.md#job-statuses). 

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottenere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto di Azure mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.
