<properties 
   pageTitle="Backup di Automazione di Azure"
   description="Illustra come eseguire il backup dei contenuti di un account di Automazione, in modo che sia possibile conservarli dopo l'eliminazione di un account di Automazione."
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
   ms.date="07/22/2015"
   ms.author="bwren" />

# Backup di Automazione di Azure

Quando si elimina un account di automazione in Microsoft Azure, vengono eliminati tutti gli oggetti presenti nell'account, ad esempio Runbook, moduli, impostazioni, processi e asset. Non sarà possibile ripristinare gli oggetti dopo l'eliminazione dell'account. È possibile usare le informazioni seguenti per eseguire il backup dei contenuti dell'account di Automazione prima di eliminarlo.

## Runbook

È possibile esportare i Runbook in file di script usando il portale di gestione di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) in Windows PowerShell. Questi file di script possono essere importati in un account di Automazione, come illustrato in [Creazione o importazione di un Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


## Moduli di integrazione

Non è possibile esportare i moduli di integrazione da Automazione di Azure. È necessario assicurare che siano disponibili all'esterno dell'account di Automazione.

## Asset

Non è possibile esportare [asset](https://msdn.microsoft.com/library/dn939988.aspx) da Automazione di Azure. Usando il portale di gestione di Azure, è necessario annotare i dettagli di variabili, credenziali, certificati, connessioni e pianificazioni. È quindi necessario creare manualmente eventuali asset usati dai Runbook importati in un altro account di Automazione.

È possibile usare i [cmdlet di Azure](https://msdn.microsoft.com/library/dn690262.aspx) per recuperare i dettagli di asset non crittografati e salvarli come riferimento futuro o creare asset equivalenti in un altro account di Automazione.

Non è possibile recuperare il valore delle variabili crittografate o il campo password delle credenziali mediante i cmdlet. Se non si conoscono questi valori, sarà possibile recuperarli da un Runbook mediante le attività [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

Non è possibile esportare certificati da Automazione di Azure. È necessario assicurarsi che eventuali certificati siano disponibili all'esterno di Azure.

## Articoli correlati

- [Creazione o importazione di un Runbook](https://msdn.microsoft.com/library/dn643637.aspx)
- [Asset di automazione](https://msdn.microsoft.com/library/dn939988.aspx)
- [Cmdlet di Azure](https://msdn.microsoft.com/library/dn690262.aspx) 

<!---HONumber=August15_HO6-->