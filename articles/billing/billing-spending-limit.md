---
title: Comprendere il limite di spesa di Azure | Microsoft Docs
description: Descrive il funzionamento del limite di spesa di Azure e la relativa rimozione
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/22/2017
ms.author: genli
ms.openlocfilehash: d222300a591bcba556be5813ada2de555d25a1f0
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2017
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Comprendere il limite di spesa di Azure e la relativa rimozione

Il limite di spesa in Azure serve per evitare spese superiori all'importo del credito. Per tutti i nuovi clienti che eseguono l'iscrizione per la versione di valutazione o per offerte che includono crediti per più mesi, il limite di spesa è attivato per impostazione predefinita. Il limite di spesa è $0, ma non può essere modificato. Il limite di spesa non è disponibile per i tipi di sottoscrizione come quelle con pagamento in base al consumo e i piani di impegno. Vedi l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

**Si vogliono ricevere avvisi di fatturazione?** Vedere [Impostare avvisi di fatturazione o di credito per le sottoscrizioni di Microsoft Azure](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Cosa succede quando raggiungo il limite di spesa?

Quando l'uso comporta costi che superano gli importi mensili inclusi nella sottoscrizione, i servizi distribuiti vengono disabilitati per il resto del periodo di fatturazione. 

Se, ad esempio, si spende tutto il credito incluso nella sottoscrizione, le istanze di Servizi cloud distribuite vengono rimosse dalla produzione e le macchine virtuali di Azure vengono arrestate e deallocate. I dati negli account di archiviazione e nei database sono disponibili in sola lettura.

All'inizio del periodo di fatturazione successivo, se l'offerta per la sottoscrizione include crediti per più mesi, la sottoscrizione verrà riabilitata automaticamente. È possibile quindi ridistribuire i servizi cloud e avere accesso completo agli account e ai database di archiviazione.

Al raggiungimento del limite di spesa previsto per la sottoscrizione, vengono inviate notifiche di posta elettronica. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions) per visualizzare le notifiche relative alle sottoscrizioni che hanno raggiunto il limite di spesa.

Se si usa una versione di valutazione gratuita e si raggiunge il limite di spesa, è possibile [eseguire l'aggiornamento alla versione con pagamento in base al consumo](billing-upgrade-azure-subscription.md) per rimuovere il limite di spesa e abilitare di nuovo la sottoscrizione.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Rimuovere il limite di spesa nel Centro account

È possibile rimuovere il limite di spesa in qualsiasi momento, a condizione che alla sottoscrizione sia associato un metodo di pagamento valido. Per le offerte che prevedono un credito per più mesi, è anche possibile riabilitare il limite di spesa all'inizio del periodo di fatturazione successivo.

Per rimuovere il limite di spesa, seguire questi passaggi:

1. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions).
1. Selezionare una sottoscrizione.
. Se la sottoscrizione è stata disabilitata a causa del raggiungimento del limite di spesa, fare clic sulla notifica che indica che la sottoscrizione ha raggiunto il limite di spesa ed è stata disabilitata per impedire costi aggiuntivi. In caso contrario, fai clic su **Rimuovere il limite di spesa** nell'area **STATO SOTTOSCRIZIONE**.
1. Seleziona l'opzione appropriata.

![Selezione di un'opzione per la rimozione del limite di spesa](./media/billing-spending-limit/remove-spending-limit.PNG)

|Opzione|Effetto|
|-------|-----|
|Rimuovi il limite di spesa per un periodo illimitato|Rimuove il limite di spesa senza riattivarlo automaticamente all'inizio del periodo di fatturazione successivo.|
|Rimuovi il limite di spesa per il periodo di fatturazione corrente|Rimuove il limite di spesa in modo che si riattivi automaticamente all'inizio del periodo di fatturazione successivo.|

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Perché può essere necessario rimuovere il limite di spesa?

Il limite di spesa può impedire all'utente di distribuire o usare determinati servizi Microsoft e di terze parti. Ecco gli scenari in cui è consigliabile rimuovere il limite di spesa per la sottoscrizione.

* Se si prevede di distribuire immagini proprietarie come Oracle e servizi come Visual Studio Team Services. Questo scenario comporta il superamento quasi immediato del limite di spesa e la disabilitazione della sottoscrizione.
* Se si usano servizi che non possono essere interrotti.
* Se si usano servizi e risorse con impostazioni come indirizzi IP virtuali che non si vuole perdere. Queste impostazioni vanno perse quando le risorse e i servizi vengono deallocati.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Come si attiva il limite di spesa dopo averlo rimosso?

Questa funzionalità è disponibile solo quando il limite di spesa è stato rimosso per un periodo illimitato. Modificarlo in modo da attivarlo automaticamente all'inizio del periodo di fatturazione successivo.

1. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions).
1. Fare clic sul banner giallo per modificare l'opzione relativa al limite di spesa.
1. Scegliere **Attiva limite di spesa per il prossimo periodo di fatturazione \<data di inizio del periodo di fatturazione\>**.

### <a name="how-do-i-set-a-custom-spending-limit"></a>Come si imposta un limite di spesa personalizzato?

Al momento non è possibile impostare limiti di spesa personalizzati. È tuttavia possibile acconsentire esplicitamente a [usare gli avvisi di fatturazione per controllare la spesa](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>Il limite di spesa impedisce qualsiasi addebito da parte di Azure?

[Alcuni servizi esterni pubblicati in Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) non possono essere usati con i crediti per la sottoscrizione e possono incorrere in addebiti separati anche quando è impostato il limite di spesa. Questo succede, ad esempio, per le licenze di Visual Studio, Azure Active Directory Premium, i piani di supporto e la maggior parte dei servizi personalizzati di terze parti. Quando si esegue il provisioning di un nuovo servizio esterno, viene visualizzato un avviso per informare che i servizi vengono fatturati separatamente:

![Avviso di acquisto di Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
