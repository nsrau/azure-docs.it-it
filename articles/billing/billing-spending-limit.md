---
title: Limite di spesa di Azure | Microsoft Docs
description: Questo articolo descrive il funzionamento del limite di spesa di Azure e spiega come rimuoverlo.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114442"
---
# <a name="azure-spending-limit"></a>Limite di spesa di Azure

Il limite di spesa in Azure impedisce spese superiori all'importo del credito. Per tutti i nuovi clienti che eseguono l'iscrizione per una versione di valutazione di Azure o per offerte che includono crediti per più mesi, il limite di spesa è attivato per impostazione predefinita. Il limite di spesa è pari a 0 dollari e non può essere modificato. Ad esempio, non è possibile modificare il limite di spesa in 100 dollari. È tuttavia possibile rimuovere il limite di spesa. È quindi possibile non definire alcun limite oppure definirne uno pari a zero per impedire la maggior parte dei tipi di spesa. Il limite di spesa non è disponibile per le sottoscrizioni in alcuni piani, ad esempio piani di impegno e piani che prevedono prezzi con pagamento in base al consumo. Vedi l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Raggiungimento di un limite di spesa

Quando l'utilizzo comporta costi che superano gli importi mensili inclusi nella sottoscrizione di Azure, i servizi distribuiti vengono disabilitati per il resto del periodo di fatturazione.

Se, ad esempio, si spende tutto il credito incluso nella sottoscrizione, le risorse di Azure distribuite vengono rimosse dalla produzione e le macchine virtuali di Azure vengono arrestate e deallocate. I dati negli account di archiviazione sono disponibili in sola lettura.

Se all'inizio del periodo di fatturazione successivo l'offerta per la sottoscrizione include crediti per più mesi, la sottoscrizione verrà riabilitata automaticamente. È possibile quindi ridistribuire le risorse di Azure e avere accesso completo agli account e ai database di archiviazione.

Al raggiungimento del limite di spesa previsto per la sottoscrizione, Azure invia notifiche di posta elettronica. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions) per visualizzare le notifiche relative alle sottoscrizioni che hanno raggiunto il limite di spesa.

Se si usa una versione di valutazione gratuita e si raggiunge il limite di spesa, è possibile eseguire l'aggiornamento a un piano con [pagamento in base al consumo](billing-upgrade-azure-subscription.md) per rimuovere il limite di spesa e abilitare di nuovo la sottoscrizione.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Rimuovere il limite di spesa nel Centro account

È possibile rimuovere il limite di spesa in qualsiasi momento, purché alla sottoscrizione di Azure sia associato un metodo di pagamento valido. Per le offerte che prevedono un credito per più mesi, è anche possibile abilitare il limite di spesa all'inizio del periodo di fatturazione successivo.

Per rimuovere il limite di spesa, seguire questi passaggi:

1. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions).
1. Selezionare una sottoscrizione. Se la sottoscrizione viene disabilitata a causa del raggiungimento del limite di spesa, fare clic sulla notifica: **La sottoscrizione ha raggiunto un limite di spesa ed è stata disabilitata per impedire addebiti.** In caso contrario, fai clic su **Rimuovere il limite di spesa** nell'area **STATO SOTTOSCRIZIONE**.
1. Seleziona l'opzione appropriata.

![Selezione di un'opzione per la rimozione del limite di spesa](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opzione | Effetto |
| --- | --- |
| Rimuovi il limite di spesa per un periodo illimitato | Rimuove il limite di spesa senza riattivarlo automaticamente all'inizio del periodo di fatturazione successivo. |
| Rimuovi il limite di spesa per il periodo di fatturazione corrente | Rimuove il limite di spesa in modo che si riattivi automaticamente all'inizio del periodo di fatturazione successivo. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Perché può essere necessario rimuovere il limite di spesa

Il limite di spesa può impedire all'utente di distribuire o usare determinati servizi Microsoft e di terze parti. Ecco le situazioni in cui è consigliabile rimuovere il limite di spesa per la sottoscrizione.

-  Se si prevede di distribuire immagini proprietarie come Oracle e servizi come Azure DevOps Services. Questa situazione comporta il superamento quasi immediato del limite di spesa e la disabilitazione della sottoscrizione.
- Se si usano servizi che non devono essere interrotti.
- Se si usano servizi e risorse con impostazioni come indirizzi IP virtuali che non si vuole perdere. Queste impostazioni vanno perse quando le risorse e i servizi vengono deallocati.

## <a name="turn-on-the-spending-limit-after-removing"></a>Attivare il limite di spesa dopo la rimozione

Questa funzionalità è disponibile solo quando il limite di spesa è stato rimosso per un periodo illimitato. Modificarlo in modo da attivarlo automaticamente all'inizio del periodo di fatturazione successivo.

1. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions).
1. Fare clic sul banner giallo per modificare l'opzione relativa al limite di spesa.
1. Scegliere **Attiva limite di spesa per il prossimo periodo di fatturazione \<data di inizio del periodo di fatturazione\>** .

## <a name="custom-spending-limit"></a>Limite di spesa personalizzato

Non sono disponili limiti di spesa personalizzati.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Un limite di spesa non impedisce qualsiasi addebito

[Alcuni servizi esterni pubblicati in Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) non possono essere usati con i crediti per la sottoscrizione e possono essere addebitati separatamente anche quando è impostato il limite di spesa. Questo succede, ad esempio, per le licenze di Visual Studio, Azure Active Directory Premium, i piani di supporto e la maggior parte dei servizi personalizzati di terze parti. Quando si esegue il provisioning di un nuovo servizio esterno, viene visualizzato un avviso per informare che i servizi vengono fatturati separatamente:

![Avviso di acquisto di Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Eseguire l'aggiornamento a un piano che prevede prezzi [con pagamento in base al consumo](billing-upgrade-azure-subscription.md).
