<properties 
   pageTitle="Conservazione dei dati di Automazione di Azure"
   description="Illustra i criteri di conservazione dei dati per Automazione di Azure."
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

# Conservazione dei dati di Automazione di Azure

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

## Articoli correlati
- [Backup di Automazione di Azure](https://msdn.microsoft.com/library/dn643635.aspx) 

<!---HONumber=July15_HO4-->