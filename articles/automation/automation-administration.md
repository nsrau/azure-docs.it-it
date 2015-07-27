<properties 
   pageTitle="Amministrazione di automazione di Azure"
   description="Questo articolo contiene più argomenti per l'amministrazione di un ambiente di automazione di Azure. Include attualmente conservazione dei dati e backup di automazione di Azure."
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
   ms.date="07/06/2015"
   ms.author="bwren" />

# Amministrazione di automazione di Azure

Questo articolo contiene più argomenti per l'amministrazione di un ambiente di automazione di Azure.

## Conservazione dei dati

Quando si elimina una risorsa in Automazione di Azure, i dati corrispondenti vengono conservati per 90 giorni per finalità di controllo, prima della rimozione permanente. In tale periodo non sarà possibile visualizzare o usare la risorsa. Questi criteri sono applicabili anche alle risorse appartenenti a un account di Automazione eliminato.

Automazione di Azure elimina automaticamente e rimuove definitivamente i processi dopo 90 giorni.

La tabella seguente riepiloga i criteri di conservazione per diverse risorse.

|Dati|Criterio|
|:---|:---|
|Account|Rimosso definitivamente 90 giorni dopo l'eliminazione dell'account da parte di un utente.|
|Asset|Rimosso definitivamente 90 giorni dopo l'eliminazione dell'asset da parte di un utente o 90 giorni dopo l'eliminazione dell'account che include l'asset da parte di un utente.|
|Moduli|Rimossi definitivamente 90 giorni dopo l'eliminazione del modulo da parte di un utente o 90 giorni dopo l'eliminazione dell'account che include il modulo da parte di un utente.|
|Runbook|Rimossi definitivamente 90 giorni dopo l'eliminazione della risorsa da parte di un utente o 90 giorni dopo l'eliminazione dell'account che include la risorsa da parte di un utente.|
|Processi|Eliminati e rimossi definitivamente 90 giorni dopo l'ultima modifica, ad esempio dopo il completamento, l'arresto o la sospensione del processo.|

I criteri di conservazione sono applicabili a tutti gli utenti e non è attualmente possibile personalizzarli.

## Backup di Automazione di Azure

Quando si elimina un account di automazione in Microsoft Azure, vengono eliminati tutti gli oggetti presenti nell'account, ad esempio Runbook, moduli, impostazioni, processi e asset. Non sarà possibile ripristinare gli oggetti dopo l'eliminazione dell'account. È possibile usare le informazioni seguenti per eseguire il backup dei contenuti dell'account di Automazione prima di eliminarlo.

### Runbook

È possibile esportare i Runbook in file di script usando il portale di gestione di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) in Windows PowerShell. Questi file di script possono essere importati in un account di Automazione, come illustrato in [Creazione o importazione di un Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


### Moduli di integrazione

Non è possibile esportare i moduli di integrazione da Automazione di Azure. È necessario assicurare che siano disponibili all'esterno dell'account di Automazione.

### Asset

Non è possibile esportare [asset](https://msdn.microsoft.com/library/dn939988.aspx) da Automazione di Azure. Usando il portale di gestione di Azure, è necessario annotare i dettagli di variabili, credenziali, certificati, connessioni e pianificazioni. È quindi necessario creare manualmente eventuali asset usati dai Runbook importati in un altro account di Automazione.

È possibile usare i [cmdlet di Azure](https://msdn.microsoft.com/library/dn690262.aspx) per recuperare i dettagli di asset non crittografati e salvarli come riferimento futuro o creare asset equivalenti in un altro account di Automazione.

Non è possibile recuperare il valore delle variabili crittografate o il campo password delle credenziali mediante i cmdlet. Se non si conoscono questi valori, sarà possibile recuperarli da un Runbook mediante le attività [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

Non è possibile esportare certificati da Automazione di Azure. È necessario assicurarsi che eventuali certificati siano disponibili all'esterno di Azure.

<!---HONumber=July15_HO3-->