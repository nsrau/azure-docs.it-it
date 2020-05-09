---
title: Gestire le pianificazioni in automazione di Azure
description: Informazioni su come creare e gestire una pianificazione in automazione di Azure in modo da poter avviare automaticamente un Runbook a una determinata ora o in base a una pianificazione ricorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4cd6d4236b95a17f404df13e8b50daf989cf6072
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652099"
---
# <a name="manage-schedules-in-azure-automation"></a>Gestire le pianificazioni in automazione di Azure

Per pianificare un runbook in Automazione di Azure per l'avvio a un'ora specifica, è necessario collegarlo a una o più pianificazioni. Una pianificazione può essere configurata in modo da essere eseguita una sola volta o in base a una pianificazione ricorrente oraria o giornaliera per manuali operativi nel portale di Azure. È inoltre possibile pianificare un'esecuzione settimanale, mensile o in giorni specifici della settimana o del mese oppure in un determinato giorno del mese. Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook.

> [!NOTE]
> Le pianificazioni non supportano attualmente le configurazioni di Azure Automation DSC.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Cmdlet di PowerShell usati per accedere alle pianificazioni

I cmdlet nella tabella seguente creano e gestiscono pianificazioni di automazione con PowerShell. Vengono forniti come parte dei [moduli AZ](modules.md#az-modules). 

| Cmdlet | Descrizione |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Recupera una pianificazione. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Recupera i runbook pianificati. |
| [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Crea una nuova pianificazione. |
| [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associa un runbook a una pianificazione. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Rimuove una pianificazione. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Imposta le proprietà di una pianificazione esistente. |
| [Unregister-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Annulla l'associazione di un runbook a una pianificazione. |

## <a name="create-a-schedule"></a>Creare una pianificazione

È possibile creare una nuova pianificazione per il manuali operativi nel portale di Azure o con PowerShell. Per evitare di influire sui manuali operativi e sui processi che consentono di automatizzare, è necessario innanzitutto testare eventuali manuali operativi con pianificazioni collegate con un account di automazione dedicato per il test. Un test convalida che il manuali operativi pianificato continui a funzionare correttamente. Se si verifica un problema, è possibile risolvere i problemi e applicare eventuali modifiche necessarie prima di eseguire la migrazione della versione aggiornata di Runbook nell'ambiente di produzione.

> [!NOTE]
> L'account di automazione non ottiene automaticamente le nuove versioni dei moduli a meno che non siano state aggiornate manualmente selezionando l'opzione [Aggiorna moduli di Azure](../automation-update-azure-modules.md) da **moduli**. Quando viene eseguito un nuovo processo pianificato, Automazione di Azure usa i moduli più recenti nell'account di automazione. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Crea una nuova pianificazione nel portale di Azure

1. Dall'account di automazione nel portale di Azure, selezionare **Pianificazioni** nella sezione **Risorse condivise** a sinistra.
1. Selezionare **Aggiungi una pianificazione** nella parte superiore della pagina.
1. Nel riquadro **nuova pianificazione** immettere un nome e, facoltativamente, immettere una descrizione per la nuova pianificazione.
1. Consente di indicare se la pianificazione viene eseguita una volta o in base a una pianificazione ricorrente selezionando **una sola volta** o **ricorrendo**. Se si seleziona **una sola volta**, specificare un'ora di inizio e quindi selezionare **Crea**. Se si seleziona **periodica**, specificare un'ora di inizio. Per ricorrere **ogni**, selezionare la frequenza con cui si desidera che il Runbook si ripeta. Selezionare per ora, giorno, settimana o mese.
    1. Se si seleziona **settimana**, verranno visualizzati i giorni della settimana in cui è possibile scegliere. Selezionare tutti i giorni necessari. La prima esecuzione della pianificazione avverrà il primo giorno selezionato all'ora di inizio. Ad esempio, per scegliere una pianificazione del weekend, selezionare sabato e domenica.
    
       ![Impostazione della pianificazione ricorrente del weekend](../media/schedules/week-end-weekly-recurrence.png)

    2. Se si seleziona **Month**, verranno fornite opzioni diverse. Per l'opzione **occorrenze mensili** selezionare giorni del **mese** o **giorni della settimana**. Se si seleziona **giorni del mese**, viene visualizzato un calendario in cui è possibile scegliere il numero di giorni desiderato. Se si sceglie una data, ad esempio il 31 che non si verifica nel mese corrente, la pianificazione non verrà eseguita. Se si desidera che la pianificazione venga eseguita nell'ultimo giorno, selezionare **Sì** in **Esegui nell'ultimo giorno del mese**. Se si seleziona **giorni della settimana**, viene visualizzata l'opzione ricorre **ogni** . Selezionare **Primo**, **Secondo**, **Terzo**, **Quarto** o **Ultimo**. Infine, scegliere un giorno per la ripetizione.

       ![Pianificazione mensile il primo, il quindicesimo e l'ultimo giorno del mese](../media/schedules/monthly-first-fifteenth-last.png)

1. Al termine, selezionare **Crea**.

### <a name="create-a-new-schedule-with-powershell"></a>Creare una nuova pianificazione con PowerShell

Usare il cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) per creare pianificazioni. Specificare l'ora di inizio per la pianificazione e la frequenza di esecuzione. Negli esempi seguenti viene illustrato come creare diversi scenari di pianificazione.

#### <a name="create-a-one-time-schedule"></a>Creare una pianificazione unica

Nell'esempio seguente viene creata una pianificazione una sola volta.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Crea una pianificazione ricorrente

Nell'esempio seguente viene illustrato come creare una pianificazione ricorrente che viene eseguita ogni giorno alle 1:00 PM per un anno.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Creare una pianificazione ricorrente settimanale

Nell'esempio seguente viene illustrato come creare una pianificazione settimanale eseguita solo nei giorni feriali.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Crea una pianificazione ricorrente settimanale per i fine settimana

Nell'esempio seguente viene illustrato come creare una pianificazione settimanale eseguita solo nei fine settimana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Creare una pianificazione ricorrente per il primo, il quindicesimo e l'ultimo giorno del mese

Nell'esempio seguente viene illustrato come creare una pianificazione ricorrente in esecuzione nel primo, quindicesimo e ultimo giorno del mese.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>Collegare una pianificazione a un Runbook

Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook. Se un runbook include parametri, è possibile specificarne i valori. È necessario specificare i valori per tutti i parametri obbligatori ed è anche possibile specificare i valori per tutti i parametri facoltativi. Questi valori vengono usati ogni volta che il runbook viene avviato dalla pianificazione. È possibile collegare lo stesso runbook a un'altra pianificazione e specificare valori diversi per i parametri.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Collegare una pianificazione a un runbook con il portale di Azure

1. Nell'portale di Azure, dall'account di automazione, selezionare **manuali operativi** in **automazione processi**.
1. Consente di selezionare il nome del Runbook da pianificare.
1. Se il Runbook non è attualmente collegato a una pianificazione, viene offerta la possibilità di creare una nuova pianificazione o un collegamento a una pianificazione esistente.
1. Se il Runbook dispone di parametri, è possibile selezionare l'opzione **Modifica impostazioni esecuzione (impostazione predefinita: Azure)** e viene visualizzato il riquadro **parametri** . Qui è possibile immettere le informazioni sui parametri.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Collegare una pianificazione a un runbook con PowerShell

Usare il cmdlet [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) per collegare una pianificazione. È possibile specificare i valori per i parametri del runbook con il parametro Parameters. Per altre informazioni su come specificare i valori dei parametri, vedere [avvio di un Runbook in automazione di Azure](../automation-starting-a-runbook.md).
Nell'esempio seguente viene illustrato come collegare una pianificazione a un Runbook utilizzando un cmdlet Azure Resource Manager con parametri.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Pianificare l'esecuzione di manuali operativi con maggiore frequenza

L'intervallo più frequente per cui è possibile configurare una pianificazione in automazione di Azure è di un'ora. Se sono necessarie pianificazioni per l'esecuzione più frequenti, sono disponibili due opzioni:

* Creare un [webhook](../automation-webhooks.md) per il Runbook e usare app per la [logica di Azure](../../logic-apps/logic-apps-overview.md) per chiamare il webhook. App per la logica di Azure offre una granularità più dettagliata per definire una pianificazione.

* Creare quattro pianificazioni che iniziano tutte entro 15 minuti l'uno dall'altro ed eseguite una volta ogni ora. Questo scenario consente l'esecuzione del runbook ogni 15 minuti con pianificazioni diverse.

## <a name="disable-a-schedule"></a>Disabilitare una pianificazione

Quando si disabilita una pianificazione, i runbook a essa collegati non vengono più eseguiti in base a tale pianificazione. È possibile disabilitare manualmente una pianificazione o impostare un'ora di scadenza per le pianificazioni con una frequenza durante la fase di creazione. Quando viene raggiunta l'ora di scadenza, la pianificazione viene disabilitata.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Disabilitare una pianificazione dal portale di Azure

1. Nell'account di automazione selezionare **pianificazioni** in **risorse condivise**.
1. Consente di selezionare il nome di una pianificazione per aprire il riquadro dettagli.
1. Impostare **Abilitata** su **No**.

> [!NOTE]
> Se si desidera disabilitare una pianificazione con un'ora di inizio nel passato, è necessario modificare la data di inizio a un'ora in futuro prima di salvarla.

### <a name="disable-a-schedule-with-powershell"></a>Disabilitare una pianificazione con PowerShell

Usare il cmdlet [set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) per modificare le proprietà di una pianificazione esistente. Per disabilitare la pianificazione, specificare false per il `IsEnabled` parametro.

Nell'esempio seguente viene illustrato come disabilitare una pianificazione per un Runbook utilizzando un cmdlet Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Rimuovere una pianificazione

Quando si è pronti per rimuovere le pianificazioni, è possibile usare il portale di Azure o PowerShell. Tenere presente che è possibile rimuovere solo una pianificazione che è stata disabilitata, come descritto nella sezione precedente.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Rimuovere una pianificazione utilizzando il portale di Azure

1. Nell'account di automazione selezionare **pianificazioni** in **risorse condivise**.
2. Fare clic sul nome di una pianificazione per aprire il rispettivo riquadro dei dettagli.
3. Fare clic su **Elimina**.

### <a name="remove-a-schedule-with-powershell"></a>Rimuovere una pianificazione con PowerShell

È possibile utilizzare il `Remove-AzAutomationSchedule` cmdlet come illustrato di seguito per eliminare una pianificazione esistente. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui cmdlet usati per accedere alle pianificazioni, vedere [gestire i moduli in automazione di Azure](modules.md).
* Per informazioni generali su manuali operativi, vedere [esecuzione di Runbook in automazione di Azure](../automation-runbook-execution.md).
