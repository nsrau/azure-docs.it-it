---
title: Pianificazioni in Automazione di Azure
description: Le pianificazioni di Automazione di Azure vengono usate per pianificare l'esecuzione automatica dei runbook in Automazione di Azure. Illustra come creare e gestire una pianificazione in modo da poter avviare automaticamente un runbook a una determinata ora o in base a una pianificazione ricorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d6677733339babf45e7351e10d6de1c002a6ea93
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436504"
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
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Recupera i runbook pianificati. |
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
1. Specificare se la pianificazione verrà eseguita una volta o in modo ricorrente, selezionando **Una sola volta** o **Ricorrente**. Se si seleziona **Una sola volta** specificare un'**Ora di inizio** e quindi fare clic su **Crea**. Se si seleziona **Ricorrente**, specificare un valore per **Ora di inizio** ed **Esegui ogni**, selezionare la frequenza di ripetizione del runbook, ad esempio **Ora**, **Giorno**, **Settimana** o **Mese**.
    1. Se si seleziona **Settimana** viene visualizzato un elenco dei giorni della settimana tra cui scegliere. Selezionare tutti i giorni necessari. La prima esecuzione della pianificazione avverrà il primo giorno selezionato all'ora di inizio.
    2. Se si seleziona **Mese**, saranno disponibili opzioni diverse. Per il **le occorrenze mensili** , quindi uno **giorni del mese** oppure **giorni della settimana**. Se si sceglie **Giorni del mese** viene visualizzato un calendario che consente di scegliere tutti i giorni necessari. Se si sceglie una data, ad esempio il 31, che non è contemplata nel mese corrente, la pianificazione non verrà eseguita. Se si vuole che la pianificazione venga eseguita l'ultimo giorno, scegliere **Sì** sotto **Esegui nell'ultimo giorno del mese**. Se si sceglie **Giorni della settimana**, viene visualizzata l'opzione **Esegui ogni**. Selezionare **Primo**, **Secondo**, **Terzo**, **Quarto** o **Ultimo**. Scegliere il giorno della ripetizione.
1. Al termine, fare clic su **Crea**.

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
2. Fare clic sul nome del runbook da pianificare.
3. Se il runbook non è attualmente collegato a una pianificazione, è possibile creare una nuova pianificazione o collegarsi a una pianificazione esistente.
4. Se il runbook include parametri, è possibile selezionare l'opzione **Modifica le impostazioni di esecuzione (impostazione predefinita: Azure)**. Verrà visualizzato il riquadro **Parametri**, in cui è possibile immettere le informazioni in base alla esigenze.

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

