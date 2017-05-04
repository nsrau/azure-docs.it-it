---
title: Pianificazioni in Automazione di Azure | Documentazione Microsoft
description: Le pianificazioni di Automazione di Azure vengono usate per pianificare l&quot;esecuzione automatica dei runbook in Automazione di Azure. Illustra come creare e gestire una pianificazione in modo da poter avviare automaticamente un runbook a una determinata ora o in base a una pianificazione ricorrente.
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 823d1155bc011466d0f3bd0ccd8650be161fd28b
ms.lasthandoff: 04/27/2017


---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Pianificazione di un runbook in Automazione di Azure
Per pianificare un runbook in Automazione di Azure per l'avvio a un'ora specifica, è necessario collegarlo a una o più pianificazioni. È possibile configurare una pianificazione perché venga eseguita una sola volta o perché venga eseguita in base a una pianificazione oraria o giornaliera ricorrente per i runbook nel portale di Azure classico. Per i runbook nel portale di Azure è possibile anche creare una pianificazione settimanale, mensile, relativa a giorni specifici della settimana o del mese o a una data specifica del mese.  Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook.

> [!NOTE]
> Le pianificazioni attualmente non supportano le configurazioni DSC di automazione di Azure.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlet di Windows PowerShell
I cmdlet della tabella seguente vengono usati per creare e gestire pianificazioni con Windows PowerShell in Automazione di Azure. Vengono forniti nel [modulo Azure PowerShell](/powershell/azure/overview).

| Cmdlet | Descrizione |
|:--- |:--- |
| **Cmdlet di Azure Resource Manager** | |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Recupera una pianificazione. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Crea una nuova pianificazione. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Rimuove una pianificazione. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Imposta le proprietà di una pianificazione esistente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Recupera i runbook pianificati. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa un runbook a una pianificazione. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Annulla l'associazione di un runbook a una pianificazione. |
| **Cmdlet di gestione dei servizi di Azure** | |
| [Get-AzureAutomationSchedule](/powershell/module/azure/get-azureautomationschedule?view=azuresmps-3.7.0) |Recupera una pianificazione. |
| [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) |Crea una nuova pianificazione. |
| [Remove-AzureAutomationSchedule](/powershell/module/azure/remove-azureautomationschedule?view=azuresmps-3.7.0) |Rimuove una pianificazione. |
| [Set-AzureAutomationSchedule](/powershell/module/azure/set-azureautomationschedule?view=azuresmps-3.7.0) |Imposta le proprietà di una pianificazione esistente. |
| [Get-AzureAutomationScheduledRunbook](/powershell/module/azure/get-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Recupera i runbook pianificati. |
| [Register-AzureAutomationScheduledRunbook](/powershell/module/azure/register-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Associa un runbook a una pianificazione. |
| [Unregister-AzureAutomationScheduledRunbook](/powershell/module/azure/unregister-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Annulla l'associazione di un runbook a una pianificazione. |

## <a name="creating-a-schedule"></a>Creazione di una pianificazione
È possibile creare una nuova pianificazione per i runbook nel portale di Azure, nel portale classico o con Windows PowerShell. È anche possibile creare una nuova pianificazione quando si collega un runbook a una pianificazione usando il portale di Azure classico o il portale di Azure.

> [!NOTE]
> Quando si associa una pianificazione a un runbook, Automazione archivia le versioni correnti dei moduli nell’account dell’utente e le collega alla pianificazione.  Ciò significa che se nell'account si disponeva di un modulo con la versione 1.0 quando è stata creata la pianificazione e successivamente il modulo è stato aggiornato alla versione 2.0, la pianificazione continuerà a utilizzare la versione 1.0.  Per utilizzare la versione aggiornata del modulo è necessario creare una nuova pianificazione. 
> 
> 

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Per creare una nuova pianificazione nel portale di Azure
1. Con l'account di automazione nel portale di Azure fare clic sul riquadro **Asset** per aprire il pannello **Asset**.
2. Fare clic sul riquadro **Pianificazioni** per aprire il pannello **Pianificazioni**.
3. Fare clic su **Aggiungi pianificazione** nella parte superiore del pannello.
4. Nel pannello **Nuova pianificazione** digitare un **Nome** e facoltativamente una **Descrizione** per la nuova pianificazione.
5. Specificare se la pianificazione verrà eseguita una volta o in modo ricorrente, selezionando **Una sola volta** o **Ricorrenza**.  Se si seleziona **Una sola volta** specificare un'**Ora di inizio** e quindi fare clic su **Crea**.  Se si seleziona **Ricorrenza**, specificare un'**Ora di inizio** e la frequenza desiderata per la ripetizione del runbook, ad esempio **ora**, **giorno**, **settimana** o **mese**.  Se si seleziona **settimana** o **mese** dall'elenco a discesa, l'**opzione Ricorrenza** verrà visualizzata nel pannello alla selezione e verrà visualizzato il pannello **Opzioni di ricorrenza**, in cui sarà possibile selezionare il giorno della settimana, se è stata selezionata l'opzione **settimana**.  Se è stata selezionata l'opzione **mese**, è possibile scegliere **Giorni della settimana** o indicare i giorni specifici del mese nel calendario e infine specificare se si vuole eseguire la pianificazione l'ultimo giorno del mese, quindi fare clic su **OK**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Per creare una nuova pianificazione nel portale di Azure classico
1. Nel portale di Azure classico selezionare Automazione e quindi selezionare il nome di un account di automazione.
2. Fare clic sulla scheda **Asset** .
3. Nella parte inferiore della finestra fare clic su **Aggiungi impostazione**.
4. Fare clic su **Aggiungi pianificazione**.
5. Digitare un **Nome** e facoltativamente una **Descrizione** per la nuova pianificazione, che verrà eseguita **Una sola volta**, **Ogni ora**, **Ogni giorno**, **Ogni settimana** oppure **Ogni mese**.
6. Specificare un valore per il campo **Ora inizio** e altre opzioni a seconda del tipo di pianificazione selezionato.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Per creare una nuova pianificazione con Windows PowerShell
È possibile usare il cmdlet [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) per creare una nuova pianificazione in Automazione di Azure per i runbook classici oppure il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) per i runbook nel portale di Azure. È necessario specificare l'ora di inizio per la pianificazione e la frequenza di esecuzione.

I comandi di esempio seguenti illustrano come creare una pianificazione per i giorni 15 e 30 di ogni mese usando un cmdlet di Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

I comandi di esempio seguenti mostrano come creare una nuova pianificazione che viene eseguita ogni giorno alle 15.30 a partire dal 20 gennaio 2015 con un cmdlet di gestione del servizio Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Collegamento di una pianificazione a un runbook
Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook. Se un runbook dispone di parametri, è possibile fornire valori per tali parametri. È necessario specificare i valori per tutti i parametri obbligatori, mentre è possibile scegliere se specificare o meno i valori per i parametri facoltativi.  Questi valori verranno usati ogni volta che il runbook viene avviato dalla pianificazione.  È possibile collegare lo stesso runbook a un'altra pianificazione e specificare valori diversi per i parametri.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Per collegare una pianificazione a un runbook con il portale di Azure
1. Con l'account di automazione nel portale di Azure fare clic sul riquadro **Runbook** per aprire il pannello **Runbook**.
2. Fare clic sul nome del runbook da pianificare.
3. Se il runbook non è attualmente collegato a una pianificazione, sarà possibile creare una nuova pianificazione o collegarsi a una pianificazione esistente.  
4. Se il runbook include parametri, è possibile selezionare l'opzione **Modifica le impostazioni di esecuzione (impostazione predefinita: Azure)**. Verrà visualizzato il pannello **Parametri**, in cui è possibile immettere le informazioni in base alla esigenze.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Per collegare una pianificazione a un runbook con il portale di Azure classico
1. Nel portale di Azure classico selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
2. Fare clic sulla scheda **Runbook** .
3. Fare clic sul nome del runbook da pianificare.
4. Fare clic sulla scheda **Pianificazione** .
5. Se il runbook attualmente non è collegato a una pianificazione, si avrà la possibilità di selezionare **Collega a una nuova pianificazione** o **Collega a una pianificazione esistente**.  Se il runbook attualmente è collegato a una pianificazione, fare clic su **Collegamento** nella parte inferiore della finestra per accedere a queste opzioni.
6. Se il runbook include parametri, verrà chiesto di inserire i relativi valori.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Per collegare una pianificazione a un runbook con Windows PowerShell
È possibile usare il cmdlet [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) per collegare una pianificazione a un runbook classico o il cmdlet [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) per i runbook nel portale di Azure.  È possibile specificare i valori per i parametri del runbook con il parametro Parameters. Per altre informazioni su come specificare i valori dei parametri, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) .

I comandi di esempio seguenti mostrano come collegare una pianificazione a un runbook usando un cmdlet di Azure Resource Manager con parametri.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
I comandi di esempio seguenti mostrano come collegare una pianificazione usando il cmdlet di gestione del servizio Azure con parametri.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Disabilitazione di una pianificazione
Quando si disabilita una pianificazione, i runbook a essa collegati non vengono più eseguiti in base a tale pianificazione. È possibile disabilitare manualmente una pianificazione o impostare un'ora di scadenza per le pianificazioni con una frequenza durante la fase di creazione. Quando viene raggiunta l'ora di scadenza, la pianificazione viene disabilitata.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Per disabilitare una pianificazione dal portale di Azure
1. Con l'account di automazione nel portale di Azure fare clic sul riquadro **Asset** per aprire il pannello **Asset**.
2. Fare clic sul riquadro **Pianificazioni** per aprire il pannello **Pianificazioni**.
3. Fare clic sul nome di una pianificazione per aprire il rispettivo pannello dei dettagli.
4. Impostare **Abilitata** su **No**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Per disabilitare una pianificazione dal portale di Azure classico
È possibile disabilitare una pianificazione nel portale di Azure classico dalla pagina Dettagli pianificazione per la pianificazione.

1. Nel portale di Azure classico selezionare Automazione e quindi fare clic sul nome di un account di automazione.
2. Fare clic sulla scheda Asset.
3. Fare clic sul nome di una pianificazione per aprire la relativa pagina dei dettagli.
4. Impostare **Abilitata** su **No**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Per disabilitare una pianificazione con Windows PowerShell
È possibile usare il cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) per modificare le proprietà di una pianificazione esistente per un runbook classico o il cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) per i runbook nel portale di Azure. Per disabilitare la pianificazione, specificare **false** per il parametro **IsEnabled**.

I comandi di esempio seguenti mostrano come disabilitare una pianificazione per un runbook usando un cmdlet di Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

I comandi di esempio seguenti mostrano come disabilitare una pianificazione usando il cmdlet di gestione del servizio Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare i runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) 


