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
ms.topic: article
ms.date: 04/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: a2743ef34bde0faabb3afd2ace27acddd59d3d70
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Comprendere il limite di spesa di Azure e la relativa rimozione

Il limite di spesa di Azure è un limite sulla spesa possibile della sottoscrizione di Azure. Per tutti i nuovi clienti che si iscrivono all'offerta della versione di valutazione o a offerte che includono crediti per più mesi, il limite di spesa sarà ora attivato per impostazione predefinita. Il limite di spesa è $0, ma non può essere modificato. Il limite di spesa non è disponibile per i tipi di sottoscrizione come quelle con pagamento in base al consumo e i piani di impegno. Vedi l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Cosa succede quando raggiungo il limite di spesa?

Quando l'uso comporta costi che esauriscono gli importi mensili inclusi nell'offerta, i servizi distribuiti vengono disabilitati per il resto del mese di fatturazione. Ad esempio, i Servizi cloud distribuiti verranno rimossi dalla produzione e le macchine virtuali di Azure verranno arrestate e deallocate. Per impedire che i servizi vengano disabilitati, è possibile scegliere di rimuovere il limite di spesa. Quando i servizi vengono disabilitati, i dati nei database e negli account di archiviazione saranno disponibili solo in modalità di lettura per gli amministratori. All'inizio del mese di fatturazione successivo, se l'offerta include crediti più mesi, la sottoscrizione sarà abilitata di nuovo. È possibile quindi ridistribuire i servizi cloud e avere accesso completo agli account e ai database di archiviazione.

Quando la sottoscrizione della versione di valutazione gratuita raggiunge il limite di spesa, è possibile riattivare la sottoscrizione e [aggiornarla automaticamente in base all'offerta standard di pagamento in base al consumo](billing-upgrade-azure-subscription.md) entro 90 giorni.

Al raggiungimento del limite di spesa previsto per l'offerta, saranno inviate notifiche. Accedere al [Centro account di Azure](https://account.windowsazure.com), fare clic su **ACCOUNT** e quindi selezionare **Sottoscrizioni**. Verranno visualizzate le notifiche relative alle sottoscrizioni che hanno raggiunto il limite di spesa.

## <a name="things-you-are-charged-for-even-if-you-have-a-spending-limit-enabled"></a>Elementi che prevedono un addebito anche in presenza di un limite di spesa

Alcuni servizi di Azure e [acquisti del Marketplace](https://azure.microsoft.com/marketplace/) possono comportare l'addebito di costi in base al metodo di pagamento in uso (carta di credito) anche se è impostato un limite di spesa. Questo succede, ad esempio, per le licenze di Visual Studio, Azure Active Directory Premium, i piani di supporto e la maggior parte dei servizi di terze parti venduti nel Marketplace.


## <a name="when-not-to-use-the-spending-limit"></a>Quando non usare il limite di spesa

Il limite di spesa può impedire all'utente di distribuire o usare determinati servizi Microsoft e del Marketplace. Ecco gli scenari in cui è consigliabile rimuovere il limite di spesa per la sottoscrizione.

- Se si prevede di distribuire immagini proprietarie come Oracle e servizi come Visual Studio Team Services. Questo scenario comporta il superamento quasi immediato del limite di spesa e la disabilitazione della sottoscrizione.

- Se si usano servizi che non possono essere interrotti.

- Se si usano servizi e risorse con impostazioni come indirizzi IP virtuali che non si vuole perdere. Queste impostazioni andranno perse quando le risorse e i servizi vengono deallocati.


## <a name="remove-the-spending-limit"></a>Rimuovere il limite di spesa

È possibile rimuovere il limite di spesa in qualsiasi momento, a condizione che alla sottoscrizione sia associato un metodo di pagamento valido. Per le offerte che prevedono un credito per più mesi, è anche possibile riabilitare il limite di spesa all'inizio del ciclo di fatturazione successivo.

Per rimuovere il limite di spesa, seguire questi passaggi:

1. Accedere al [Centro account di Azure](https://account.windowsazure.com).

2. Selezionare una sottoscrizione.

3. Se la sottoscrizione è stata disabilitata a causa del raggiungimento del limite di spesa, fare clic su questa notifica: "Subscription reached the Spending Limit and has been disabled to prevent charges" (La sottoscrizione ha raggiunto un limite di spesa ed è stata disabilitata per prevenire eventuali addebiti). In caso contrario, fai clic su **Rimuovere il limite di spesa** nell'area **STATO SOTTOSCRIZIONE**.

4. Seleziona l'opzione appropriata.

|Opzione|Effetto|
|-------|-----|
|Rimuovi il limite di spesa per un periodo illimitato|Rimuove il limite di spesa senza riattivarlo automaticamente all'inizio del periodo di fatturazione successivo.|
|Rimuovi il limite di spesa per il periodo di fatturazione corrente|Rimuove il limite di spesa in modo che si riattivi automaticamente all'inizio del periodo di fatturazione successivo.|

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

