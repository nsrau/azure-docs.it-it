<properties 
   pageTitle="Gestione dei dati di Automazione di Azure"
   description="Questo articolo contiene più argomenti per la gestione di un ambiente di Automazione di Azure. Include attualmente conservazione dei dati e backup del ripristino di emergenza di Automazione di Azure in Automazione di Azure."
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
   ms.date="10/08/2015"
   ms.author="bwren;sngun" />

# Gestione dei dati di Automazione di Azure

Questo articolo contiene più argomenti per la gestione di un ambiente di Automazione di Azure.

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

##Replica geografica in Automazione di Azure

Automazione di Azure supporta la replica geografica. Con la replica geografica, Automazione di Azure mantiene i dati persistenti in due aree geografiche. Durante la creazione di un account di automazione nel portale di Azure si sceglie l'area in cui crearlo, che sarà l'area primaria. L'area in cui viene effettuata la replica geografica dei dati viene definita area secondaria. Le aree primarie e secondaria comunicano tra loro per eseguire la replica geografica gli aggiornamenti apportati all'account di automazione. Poiché nell'area secondaria è archiviata una copia delle informazioni, in caso di failover di un account di automazione dall'area primaria a quella secondaria, tutte le informazioni relative all'account di automazione ancora saranno disponibili nell'area secondaria.

La replica geografica è integrata negli account di automazione e non comporta costi aggiuntivi. Non è possibile scegliere l'area secondaria, che viene determinata automaticamente in base all'area primaria scelta.

 
###Ubicazione delle repliche geografiche

Attualmente è possibile creare account di automazione in cinque aree geografiche e in futuro verrà aggiunto il supporto per ulteriori aree. Nella tabella seguente vengono illustrate le associazioni di aree primarie e secondarie:

|Primaria |Secondaria
| ---------------   |----------------
|Stati Uniti centro-meridionali |Stati Uniti centro-settentrionali
|Stati Uniti orientali 2 |Stati Uniti centrali
|Europa occidentale |Europa settentrionale
|Asia sudorientale |Asia orientale
|Giappone orientale |Giappone occidentale


###Ripristino di emergenza in Automazione di Azure

Quando si verifica una grave emergenza nell'area primaria, il team di Automazione di Azure tenta prima di tutto di ripristinare l'area primaria. In alcuni casi, quando non è possibile ripristinare l'area primaria, quindi viene eseguito il failover geografico e i clienti interessati saranno informati attraverso la propria sottoscrizione.

<!---HONumber=Oct15_HO3-->