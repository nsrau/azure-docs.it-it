<properties 
   pageTitle="Pianificazione di un runbook in Automazione di Azure"
   description="Illustra come creare una pianificazione in Automazione di Azure in modo da poter avviare automaticamente un runbook a una determinata ora o in base a una pianificazione ricorrente."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="bwren" />

# Pianificazione di un runbook in Automazione di Azure

Per pianificare un runbook in Automazione di Azure per l'avvio a un'ora specifica, è necessario collegarlo a una o più pianificazioni. Una pianificazione può essere configurata per venire eseguita una sola volta o in modo ricorrente dopo un determinato numero di ore o giorni. Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook.

## Creazione di una pianificazione

È possibile creare una nuova pianificazione con il portale di gestione di Azure o con Windows PowerShell. È anche possibile creare una nuova pianificazione quando si collega un runbook a una pianificazione tramite il portale di gestione di Azure.

### Per creare una nuova pianificazione con il portale di gestione di Azure

1. Nel portale di gestione di Azure selezionare Automazione e quindi fare clic sul nome di un account di automazione.
1. Fare clic sulla scheda **Asset**.
1. Nella parte inferiore della finestra fare clic su **Aggiungi impostazione**.
1. Fare clic su **Aggiungi pianificazione**.
1. Completare il campo **Nome** e facoltativamente il campo **Descrizione** per la nuova pianificazione.
1. Scegliere quando eseguire la pianificazione selezionando **Una volta**, **Ogni ora** o **Ogni giorno**.
1. Specificare un valore per il campo **Ora inizio** e altre opzioni a seconda del tipo di pianificazione selezionato.

### Per creare una nuova pianificazione con Windows PowerShell

È possibile usare il cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) per creare una nuova pianificazione in Automazione di Azure. È necessario specificare l'ora di inizio della pianificazione e se deve essere eseguita una volta, ogni ora o ogni giorno.

I comandi di esempio seguenti mostrano come creare una nuova pianificazione che viene eseguita ogni giorno alle 15.30 a partire dal 20 gennaio 2015.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## Collegamento di una pianificazione a un runbook

Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook. Se un runbook dispone di parametri, è possibile fornire valori per tali parametri. È necessario specificare i valori per tutti i parametri obbligatori, mentre è possibile scegliere se specificare o meno i valori per i parametri facoltativi. Questi valori verranno usati ogni volta che il runbook viene avviato dalla pianificazione. È possibile collegare lo stesso runbook a un'altra pianificazione e specificare valori diversi per i parametri.

### Per collegare una pianificazione a un runbook con il portale di gestione di Azure

1. Nel portale di gestione di Azure selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
1. Fare clic sulla scheda **Runbook**.
1. Fare clic sul nome del runbook da pianificare.
1. Fare clic sulla scheda **Pianificazione**.
2. Se il runbook attualmente non è collegato a una pianificazione, si avrà la possibilità di selezionare **Collega a nuova pianificazione** o **Collega a pianificazione esistente**. Se il runbook attualmente è collegato a una pianificazione, fare clic su **Collegamento** nella parte inferiore della finestra per accedere a queste opzioni.
1. Se il runbook include parametri, verrà chiesto di inserire i relativi valori.  

### Per collegare una pianificazione a un runbook con Windows PowerShell

È possibile usare [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) per collegare una pianificazione a un runbook. È possibile specificare i valori per i parametri del runbook con il parametro Parameters. Per altre informazioni su come specificare i valori dei parametri, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md).

I comandi di esempio seguenti mostrano come collegare una pianificazione a un runbook con parametri.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## Disabilitazione di una pianificazione

Quando si disabilita una pianificazione, i runbook a essa collegati non vengono più eseguiti in base a tale pianificazione. È possibile disabilitare una pianificazione manualmente o impostare un'ora di scadenza per le pianificazioni oraria e giornaliera al momento della creazione. Quando viene raggiunta l'ora di scadenza, la pianificazione viene disabilitata.

### Per disabilitare una pianificazione con il portale di gestione di Azure

È possibile disabilitare una pianificazione nel portale di gestione di Azure tramite la pagina Dettagli pianificazione per la pianificazione.

1. Nel portale di gestione di Azure selezionare Automazione e quindi fare clic sul nome di un account di automazione.
1. Fare clic sulla scheda Asset.
1. Fare clic sul nome di una pianificazione per aprire la relativa pagina dei dettagli.
2. Impostare **Abilitata** su **No**.

### Per disabilitare una pianificazione con Windows PowerShell

È possibile usare il cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) per modificare le proprietà di una pianificazione esistente. Per disabilitare la pianificazione, specificare **false** per il parametro **IsEnabled**.

I comandi di esempio seguenti mostrano come disabilitare una pianificazione.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## Articoli correlati

- [Asset di tipo pianificazione in Automazione di Azure](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) 

<!---HONumber=July15_HO3-->