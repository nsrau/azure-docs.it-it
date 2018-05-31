---
title: Pianificazioni in Automazione di Azure
description: Le pianificazioni di Automazione di Azure vengono usate per pianificare l'esecuzione automatica dei runbook in Automazione di Azure. Illustra come creare e gestire una pianificazione in modo da poter avviare automaticamente un runbook a una determinata ora o in base a una pianificazione ricorrente.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 97922acdd6b14e594e4274b0db0f930a02b3b7c3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936961"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Pianificazione di un runbook in Automazione di Azure

Per pianificare un runbook in Automazione di Azure per l'avvio a un'ora specifica, è necessario collegarlo a una o più pianificazioni. È possibile configurare una pianificazione in modo che i runbook vengano eseguiti una sola volta oppure con cadenza oraria o giornaliera nel portale di Azure. È inoltre possibile pianificare un'esecuzione settimanale, mensile o in giorni specifici della settimana o del mese oppure in un determinato giorno del mese. Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook.

> [!NOTE]
> Le pianificazioni attualmente non supportano le configurazioni DSC di automazione di Azure.

## <a name="windows-powershell-cmdlets"></a>Cmdlet di Windows PowerShell

I cmdlet della tabella seguente vengono usati per creare e gestire pianificazioni con Windows PowerShell in Automazione di Azure. Vengono forniti nel [modulo Azure PowerShell](/powershell/azure/overview).

| Cmdlets | DESCRIZIONE |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Recupera una pianificazione. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Crea una nuova pianificazione. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Rimuove una pianificazione. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Imposta le proprietà di una pianificazione esistente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Recupera i runbook pianificati. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa un runbook a una pianificazione. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Annulla l'associazione di un runbook a una pianificazione. |

## <a name="creating-a-schedule"></a>Creazione di una pianificazione

È possibile creare una nuova pianificazione per i runbook nel portale di Azure o con Windows PowerShell.

> [!NOTE]
> Quando viene eseguito un nuovo processo pianificato, Automazione di Azure usa i moduli più recenti nell'account di automazione.  Per evitare conseguenze per i runbook e i processi che vengono automatizzati, è necessario innanzitutto testare tutti i runbook che dispongono di pianificazioni collegate con un account di automazione dedicato ai test.  In questo modo vengono convalidati i runbook pianificati, che continuano a funzionare correttamente; in caso contrario è possibile risolvere i problemi e applicare eventuali modifiche necessarie prima della migrazione della versione aggiornata del runbook all'ambiente di produzione.
> L'account di automazione non riceve automaticamente nuove versioni dei moduli a meno che tali versioni non siano state aggiornate manualmente selezionando l'opzione [Aggiorna moduli di Azure](automation-update-azure-modules.md) nel riquadro **Moduli**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Per creare una nuova pianificazione nel portale di Azure

1. Dall'account di automazione nel portale di Azure, selezionare **Pianificazioni** nella sezione **Risorse condivise** a sinistra.
1. Fare clic su **Aggiungi pianificazione** nella parte superiore della pagina.
1. Nel riquadro **Nuova pianificazione** digitare un **Nome** e facoltativamente una **Descrizione** per la nuova pianificazione.
1. Specificare se la pianificazione verrà eseguita una volta o in modo ricorrente, selezionando **Una sola volta** o **Ricorrenza**. Se si seleziona **Una sola volta** specificare un'**Ora di inizio** e quindi fare clic su **Crea**. Se si seleziona **Ricorrenza**, specificare un'**Ora di inizio** e la frequenza desiderata per la ripetizione del runbook, ad esempio **ora**, **giorno**, **settimana** o **mese**. Se si seleziona **settimana** o **mese** dall'elenco a discesa, l'**opzione Ricorrenza** verrà visualizzata nel riquadro e al momento della selezione verrà visualizzato il riquadro **Opzioni di ricorrenza**, in cui sarà possibile selezionare il giorno della settimana, se è stata selezionata l'opzione **settimana**. Se è stata selezionata l'opzione **mese**, è possibile scegliere **Giorni della settimana** o indicare i giorni specifici del mese nel calendario e infine specificare se si vuole eseguire la pianificazione l'ultimo giorno del mese, quindi fare clic su **OK**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Per creare una nuova pianificazione con Windows PowerShell

Usare il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) per creare le pianificazioni. Specificare l'ora di inizio per la pianificazione e la frequenza di esecuzione.

I comandi di esempio seguenti illustrano come creare una pianificazione per i giorni 15 e 30 di ogni mese usando un cmdlet di Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Collegamento di una pianificazione a un runbook

Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook. Se un runbook dispone di parametri, è possibile fornire valori per tali parametri. È necessario specificare i valori per tutti i parametri obbligatori, mentre è possibile scegliere se specificare o meno i valori per i parametri facoltativi. Questi valori vengono usati ogni volta che il runbook viene avviato dalla pianificazione. È possibile collegare lo stesso runbook a un'altra pianificazione e specificare valori diversi per i parametri.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Per collegare una pianificazione a un runbook con il portale di Azure

1. Dall'account di automazione nel portale di Azure, selezionare **Runbook** nella sezione **Automazione processi** a sinistra.
1. Fare clic sul nome del runbook da pianificare.
1. Se il runbook non è attualmente collegato a una pianificazione, è possibile creare una nuova pianificazione o collegarsi a una pianificazione esistente.
1. Se il runbook include parametri, è possibile selezionare l'opzione **Modifica le impostazioni di esecuzione (impostazione predefinita: Azure)**. Verrà visualizzato il riquadro **Parametri**, in cui è possibile immettere le informazioni in base alla esigenze.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Per collegare una pianificazione a un runbook con Windows PowerShell

È possibile usare il cmdlet [Register AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) per collegare una pianificazione. È possibile specificare i valori per i parametri del runbook con il parametro Parameters. Per altre informazioni su come specificare i valori dei parametri, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md).
I comandi di esempio seguenti mostrano come collegare una pianificazione a un runbook usando un cmdlet di Azure Resource Manager con parametri.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Pianificazione più frequente dei runbook

L'intervallo più frequente per il quale è possibile configurare una pianificazione in Automazione di Azure è un'ora. Se sono necessarie pianificazioni da eseguire più frequentemente rispetto a questa, sono disponibili due opzioni:

* Creare un [webhook](automation-webhooks.md) per il runbook e usare l'[Utilità di pianificazione di Azure](../scheduler/scheduler-get-started-portal.md) per chiamare il webhook. Utilità di pianificazione di Azure fornisce una granularità più precisa quando si definisce una pianificazione.

* Creare quattro pianificazioni, tutte in avvio entro 15 minuti una dall'altra, in esecuzione una volta ogni ora. Questo scenario consente l'esecuzione del runbook ogni 15 minuti con pianificazioni diverse.

## <a name="disabling-a-schedule"></a>Disabilitazione di una pianificazione

Quando si disabilita una pianificazione, i runbook a essa collegati non vengono più eseguiti in base a tale pianificazione. È possibile disabilitare manualmente una pianificazione o impostare un'ora di scadenza per le pianificazioni con una frequenza durante la fase di creazione. Quando viene raggiunta l'ora di scadenza, la pianificazione viene disabilitata.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Per disabilitare una pianificazione dal portale di Azure

1. Dall'account di automazione nel portale di Azure, selezionare **Pianificazioni** nella sezione **Risorse condivise** a sinistra.
1. Fare clic sul nome di una pianificazione per aprire il rispettivo riquadro dei dettagli.
1. Impostare **Abilitata** su **No**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Per disabilitare una pianificazione con Windows PowerShell

È possibile usare il cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) per modificare le proprietà di una pianificazione esistente. Per disabilitare la pianificazione, specificare **false** per il parametro **IsEnabled**.

I comandi di esempio seguenti mostrano come disabilitare una pianificazione per un runbook usando un cmdlet di Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)