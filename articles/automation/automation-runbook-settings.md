<properties 
   pageTitle="Impostazioni dei runbook"
   description="Illustra le impostazioni di configurazione per un runbook in Automazione di Azure e come modificarle usando il portale di gestione di Azure e Windows PowerShell."
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Impostazioni dei runbook

Ogni runbook in Automazione di Azure dispone di più impostazioni che consentono di identificarlo e di modificarne il comportamento di registrazione. Per ognuna di queste impostazioni, di seguito viene riportata una descrizione e vengono illustrate le procedure di modifica.

## Impostazioni

### Nome e descrizione

Non è possibile modificare il nome di un runbook dopo che è stato creato. La descrizione è facoltativa e può contenere fino a 512 caratteri.

### Tag

I tag consentono di assegnare parole e frasi distinte per facilitare l'identificazione di un runbook. Ad esempio, quando si invia un runbook alla [raccolta di runbook](https://msdn.microsoft.com/library/dn781422.aspx), è possibile specificare particolari tag per identificare le categorie in cui il runbook deve essere elencato. È possibile specificare più tag per un runbook separandole con le virgole.

### Registrazione

Per impostazione predefinita, i record dettagliati e di avanzamento non vengono scritti nella cronologia processo. È possibile modificare le impostazioni per un determinato runbook per registrare questi record. Per altre informazioni su tali record, vedere [Messaggi e output del runbook](https://msdn.microsoft.com/library/dn879148.aspx).

## Modifica delle impostazioni dei runbook

### Modifica delle impostazioni dei runbook con il portale di gestione di Azure

È possibile modificare le impostazioni di un runbook nel portale di gestione di Azure dalla pagina **Configura** relativa al runbook.

1. Nel portale di gestione di Azure selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
1. Fare clic sulla scheda **Runbook**.
1. Fare clic sul nome di un runbook.
1. Fare clic sulla scheda **Configura**.

### Modifica delle impostazioni dei runbook con Windows PowerShell

Per modificare le impostazioni di un runbook, è possibile usare il cmdlet [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx). Se si intende specificare più tag, è possibile fornire una matrice o una singola stringa con valori delimitati da virgole per il parametro Tags. È possibile ottenere i tag correnti con [Get AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

I comandi di esempio seguenti illustrano come impostare le proprietà di un runbook. Questo esempio aggiunge tre tag ai tag esistenti e specifica che vengano registrati i record dettagliati.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	$tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
	$tags += "Tag1,Tag2,Tag3"
	Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## Articoli correlati
- [Messaggi e output del runbook](../automation-runbook-output-and-messages) 
- [Creazione o importazione di un runbook](https://msdn.microsoft.com/library/dn643637.aspx) 

<!---HONumber=July15_HO4-->