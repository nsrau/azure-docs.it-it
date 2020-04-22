---
title: Gestire le pianificazioni in Automazione di AzureManage schedules in Azure Automation
description: Le pianificazioni di Automazione di Azure vengono usate per pianificare l'esecuzione automatica dei runbook in Automazione di Azure. Illustra come creare e gestire una pianificazione in modo da poter avviare automaticamente un runbook a una determinata ora o in base a una pianificazione ricorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3603e76186ce30fb491d829d3a804837f4ac2e6d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732785"
---
# <a name="manage-schedules-in-azure-automation"></a>Gestire le pianificazioni in Automazione di AzureManage schedules in Azure Automation

Per pianificare un runbook in Automazione di Azure per l'avvio a un'ora specifica, è necessario collegarlo a una o più pianificazioni. È possibile configurare una pianificazione in modo che i runbook vengano eseguiti una sola volta oppure con cadenza oraria o giornaliera nel portale di Azure. È inoltre possibile pianificare un'esecuzione settimanale, mensile o in giorni specifici della settimana o del mese oppure in un determinato giorno del mese. Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook.

> [!NOTE]
> Le pianificazioni attualmente non supportano le configurazioni DSC di automazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="powershell-cmdlets"></a>Cmdlet di PowerShell

The cmdlets in the following table are used to create and manage schedules with PowerShell in Azure Automation. Vengono forniti nel [modulo Azure PowerShell](/powershell/azure/overview).

| Cmdlet | Descrizione |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Recupera una pianificazione. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Recupera i runbook pianificati. |
| [Pianificazione dell'automazione New-AzAutomation](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Crea una nuova pianificazione. |
| [Registrare-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associa un runbook a una pianificazione. |
| [Rimozione-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Rimuove una pianificazione. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Imposta le proprietà di una pianificazione esistente. |
| [Annullare la registrazione-AzAutomationScheduledRunbookUnregister-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Annulla l'associazione di un runbook a una pianificazione. |

## <a name="creating-a-schedule"></a>Creazione di una pianificazione

È possibile creare una nuova pianificazione per i runbook nel portale di Azure o con PowerShell.You can create a new schedule for runbooks in the Azure portal or with PowerShell.

> [!NOTE]
> Quando viene eseguito un nuovo processo pianificato, Automazione di Azure usa i moduli più recenti nell'account di automazione.  Per evitare conseguenze per i runbook e i processi che vengono automatizzati, è necessario innanzitutto testare tutti i runbook che dispongono di pianificazioni collegate con un account di automazione dedicato ai test.  In questo modo vengono convalidati i runbook pianificati, che continuano a funzionare correttamente; in caso contrario è possibile risolvere i problemi e applicare eventuali modifiche necessarie prima della migrazione della versione aggiornata del runbook all'ambiente di produzione.
> L'account di automazione non riceve automaticamente nuove versioni dei moduli a meno che tali versioni non siano state aggiornate manualmente selezionando l'opzione [Aggiorna moduli di Azure](../automation-update-azure-modules.md) nel riquadro **Moduli**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Creare una nuova pianificazione nel portale di AzureCreate a new schedule in the Azure portal

1. Nel portale di Azure selezionare **Pianificazioni** nella sezione **Risorse condivise** a sinistra.
2. Fare clic su **Aggiungi pianificazione** nella parte superiore della pagina.
3. Nel riquadro Nuova pianificazione digitare un nome e, facoltativamente, una descrizione per la nuova pianificazione.
4. Specificare se la pianificazione verrà eseguita una volta o in modo ricorrente, selezionando **Una sola volta** o **Ricorrente**. Se si seleziona **Una volta**, specificare un'ora di inizio e quindi fare clic su **Crea**. Se si seleziona **Ricorrente**, specificare un'ora di inizio. Per **Ricorre ogni**, selezionare la frequenza con cui si desidera che il runbook si ripeta, per ora, giorno, settimana o mese.
    1. Se si seleziona **settimana**, i giorni della settimana vengono presentati per voi tra cui scegliere. Selezionare tutti i giorni necessari. La prima esecuzione della pianificazione avverrà il primo giorno selezionato all'ora di inizio. Ad esempio, per scegliere una pianificazione del fine settimana, selezionare Sabato e Domenica. 
    
       ![Impostazione della pianificazione ricorrente del fine settimana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se si seleziona **mese**, vengono fornite diverse opzioni. Per l'opzione **Occorrenze mensili,** selezionare **Giorni mese** o **Giorni settimana**. Se si sceglie **Mese giorni**, viene visualizzato un calendario che consente di scegliere tutti i giorni desiderati. Se si sceglie una data, ad esempio la 31a che non si verifica nel mese corrente, la pianificazione non verrà eseguita. Se si vuole che la pianificazione venga eseguita l'ultimo giorno, scegliere **Sì** sotto **Esegui nell'ultimo giorno del mese**. Se si sceglie **Giorni della settimana**, viene visualizzata l'opzione **Esegui ogni**. Selezionare **Primo**, **Secondo**, **Terzo**, **Quarto** o **Ultimo**. Scegliere il giorno della ripetizione.

       ![Programma mensile il primo, il quindicesimo e l'ultimo giorno del mese](../media/schedules/monthly-first-fifteenth-last.png)

5. Al termine, fare clic su **Crea**.

### <a name="create-a-new-schedule-with-powershell"></a>Creare una nuova pianificazione con PowerShellCreate a new schedule with PowerShell

Utilizzare il cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) per creare pianificazioni. Specificare l'ora di inizio per la pianificazione e la frequenza di esecuzione. Negli esempi seguenti viene illustrato come creare molti scenari di pianificazione diversi.

#### <a name="create-a-one-time-schedule"></a>Creare una pianificazione una tantera

I comandi di esempio seguenti creano una pianificazione una tantera.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Creare una pianificazione ricorrente

Nell'esempio seguente viene illustrato come creare una pianificazione ricorrente che viene eseguita ogni giorno alle 13:00 per un anno.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Creare una pianificazione ricorrente settimanale

Nell'esempio seguente viene illustrato come creare una pianificazione settimanale che viene eseguita solo nei giorni feriali.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Creare una pianificazione ricorrente settimanale per i fine settimana

I comandi di esempio seguenti illustrano come creare una pianificazione settimanale che viene eseguita solo nei fine settimana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Creare una pianificazione ricorrente per il primo, il quindicesimo e l'ultimo giorno del mese

Nell'esempio seguente viene illustrato come creare una pianificazione ricorrente che viene eseguita il primo, il quindicesimo e l'ultimo giorno del mese.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Collegamento di una pianificazione a un runbook

Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook. Se un runbook dispone di parametri, è possibile fornire valori per tali parametri. È necessario specificare i valori per tutti i parametri obbligatori, mentre è possibile scegliere se specificare o meno i valori per i parametri facoltativi. Questi valori vengono usati ogni volta che il runbook viene avviato dalla pianificazione. È possibile collegare lo stesso runbook a un'altra pianificazione e specificare valori diversi per i parametri.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Collegare una pianificazione a un runbook con il portale di AzureLink a schedule to a runbook with the Azure portal

1. Nel portale di Azure selezionare **Runbooks** in **Automazione processo**dal tuo account di automazione.
2. Fare clic sul nome del runbook da pianificare.
3. Se il runbook non è attualmente collegato a una pianificazione, ti viene offerta la possibilità di creare una nuova pianificazione o un nuovo collegamento a una pianificazione esistente.
4. Se il runbook include parametri, è possibile selezionare l'opzione **Modifica impostazioni esecuzione (Default:Azure)** e viene visualizzato il riquadro Parametri.If the runbook has parameters, you can select the option Modify run settings (Default:Azure) and the Parameters pane is presented. È possibile immettere qui le informazioni sui parametri.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Collegare una pianificazione a un runbook con PowerShellLink a schedule to a runbook with PowerShell

Utilizzare il cmdlet [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) per collegare una pianificazione. È possibile specificare i valori per i parametri del runbook con il parametro Parameters. Per altre informazioni su come specificare i valori dei parametri, vedere [Avvio di un runbook in Automazione di Azure](../automation-starting-a-runbook.md).
Nell'esempio seguente viene illustrato come collegare una pianificazione a un runbook usando un cmdlet di Azure Resource Manager con parametri.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Pianificazione dell'esecuzione dei runbook con maggiore frequenza

L'intervallo più frequente per il quale è possibile configurare una pianificazione in Automazione di Azure è un'ora. Se sono necessarie pianificazioni da eseguire più frequentemente rispetto a questa, sono disponibili due opzioni:

* Creare un [webhook](../automation-webhooks.md) per il runbook e usare [app per](../../logic-apps/logic-apps-overview.md) la logica di Azure per chiamare il webhook. App per la logica di Azure offre una granularità più dettagliata quando si definisce una pianificazione.

* Creare quattro pianificazioni, tutte in avvio entro 15 minuti una dall'altra, in esecuzione una volta ogni ora. Questo scenario consente l'esecuzione del runbook ogni 15 minuti con pianificazioni diverse.

## <a name="disabling-a-schedule"></a>Disabilitazione di una pianificazione

Quando si disabilita una pianificazione, i runbook a essa collegati non vengono più eseguiti in base a tale pianificazione. È possibile disabilitare manualmente una pianificazione o impostare un'ora di scadenza per le pianificazioni con una frequenza durante la fase di creazione. Una volta raggiunta l'ora di scadenza, la pianificazione viene disabilitata.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Disabilitare una pianificazione dal portale di AzureDisable a schedule from the Azure portal

1. Nell'account Di automazione selezionare **Pianificazioni** in **Risorse condivise**.
2. Fare clic sul nome di una pianificazione per aprire il rispettivo riquadro dei dettagli.
3. Impostare **Abilitata** su **No**.

> [!NOTE]
> Se si desidera disabilitare una pianificazione con un'ora di inizio in passato, è necessario modificare la data di inizio in un'ora futura prima di salvarla.

### <a name="disable-a-schedule-with-powershell"></a>Disabilitare una pianificazione con PowerShellDisable a schedule with PowerShell

Utilizzare il cmdlet [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) per modificare le proprietà di una pianificazione esistente. Per disabilitare la pianificazione, `IsEnabled` specificare False per il parametro.

Nell'esempio seguente viene illustrato come disabilitare una pianificazione per un runbook usando un cmdlet di Azure Resource Manager.The following example shows how to disable a schedule for a runbook using an Azure Resource Manager cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione](../automation-starting-a-runbook.md)di Azure.To get started with runbooks in Azure Automation, see Starting a Runbook in Azure Automation.