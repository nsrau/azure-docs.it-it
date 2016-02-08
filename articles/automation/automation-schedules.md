<properties 
   pageTitle="Pianificazioni in Automazione di Azure | Microsoft Azure"
   description="Le pianificazioni di Automazione di Azure vengono usate per pianificare l'esecuzione automatica dei runbook in Automazione di Azure. Questo articolo illustra come creare pianificazioni."
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
   ms.date="01/27/2016"
   ms.author="bwren" />

# Pianificazioni in Automazione di Azure

Le pianificazioni di Automazione di Azure vengono usate per pianificare l'esecuzione automatica dei runbook. Può trattarsi di una singola data e ora per un'unica esecuzione del runbook oppure di una pianificazione ricorrente per avviare il runbook più volte. Alle pianificazioni non è in genere possibile accedere dai runbook.

>[AZURE.NOTE]  Le pianificazioni attualmente non supportano le configurazioni DSC di automazione di Azure.

## Cmdlet di Windows PowerShell

I cmdlet della tabella seguente vengono usati per creare e gestire variabili con Windows PowerShell in Automazione di Azure. Vengono forniti nel [modulo Azure PowerShell](../powershell-install-configure.md).

|Cmdlet|Descrizione|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Recupera una pianificazione.|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Crea una nuova pianificazione.|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Rimuove una pianificazione.|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Imposta le proprietà di una pianificazione esistente.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Recupera i runbook pianificati.|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Associa un runbook a una pianificazione.|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|Annulla l'associazione di un runbook a una pianificazione.|

## Creazione di una nuova pianificazione

### Per creare una nuova pianificazione con il portale di Azure


1. Dall'account di automazione fare clic su **Asset** nella parte superiore della finestra.
1. Nella parte inferiore della finestra fare clic su **Aggiungi impostazione**.
1. Fare clic su **Aggiungi pianificazione**.
1. Completare la procedura guidata e selezionare la casella di controllo per salvare la nuova variabile.

### Per creare una nuova pianificazione con il portale di anteprima di Azure

1. Dall'account di automazione fare clic sulla parte **Asset** per aprire il pannello **Asset**.
1. Fare clic sulla parte **Pianificazioni** per aprire il pannello **Pianificazioni**.
1. Fare clic su **Aggiungi pianificazione** nella parte superiore del pannello.
1. Completare il modulo e fare clic su **Crea** per salvare la nuova pianificazione.

### Per creare una nuova pianificazione con Windows PowerShell

Il cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) crea una nuova pianificazione e imposta il valore per una pianificazione esistente. I comandi di esempio di Windows PowerShell seguenti creano una nuova pianificazione denominata My Daily Schedule che viene avviata il giorno dopo a mezzogiorno e viene attivata ogni giorno per un anno:

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "My Daily Schedule"
	$startTime = (Get-Date).Date.AddDays(1).AddHours(12)
	$expiryTime = $startTime.AddYears(1)
	
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## Vedere anche
- [Pianificazione di un runbook in Automazione di Azure](automation-scheduling-a-runbook.md)
 

<!---HONumber=AcomDC_0128_2016-->