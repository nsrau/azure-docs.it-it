---
title: Limite di spesa di Azure | Microsoft Docs
description: Questo articolo descrive come funziona un limite di spesa di Azure e come rimuoverlo.
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
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114442"
---
# <a name="azure-spending-limit"></a>Limite di spesa di Azure

Il limite di spesa in Azure impedisce la spesa per l'importo del credito. Per tutti i nuovi clienti che si iscrivono a una versione di valutazione di Azure o a offerte che includono crediti per più mesi, il limite di spesa è attivato per impostazione predefinita. Il limite di spesa è $0 e non può essere modificato. Ad esempio, non è possibile modificare il limite di spesa in $100. Tuttavia, è possibile rimuovere il limite di spesa. Quindi, non si dispone di alcun limite oppure si ha un limite di zero che impedisce la maggior parte dei tipi di spesa. Il limite di spesa non è disponibile per le sottoscrizioni in alcuni piani, ad esempio piani di impegno e piani con prezzi con pagamento in base al consumo. Vedi l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Raggiungimento di un limite di spesa

Quando l'utilizzo comporta costi che esauriscono gli importi mensili inclusi nella sottoscrizione di Azure, i servizi distribuiti vengono disabilitati per il resto del periodo di fatturazione.

Ad esempio, quando si spende tutto il credito incluso nella sottoscrizione, le risorse di Azure distribuite vengono rimosse dalla produzione e le macchine virtuali di Azure vengono arrestate e deallocate. I dati negli account di archiviazione sono disponibili in sola lettura.

All'inizio del periodo di fatturazione successivo, se l'offerta di sottoscrizione include crediti per più mesi, la sottoscrizione verrà riabilitata automaticamente. Sarà quindi possibile ridistribuire le risorse di Azure e disporre dell'accesso completo agli account di archiviazione e ai database.

Azure invia notifiche tramite posta elettronica quando si raggiunge il limite di spesa per la sottoscrizione. Accedere al [centro account](https://account.windowsazure.com/Subscriptions) per visualizzare le notifiche relative alle sottoscrizioni che hanno raggiunto il limite di spesa.

Se si dispone di una sottoscrizione di valutazione gratuita e si raggiunge il limite di spesa, è possibile eseguire l'aggiornamento a un piano con prezzi con [pagamento in base](billing-upgrade-azure-subscription.md) al consumo per rimuovere il limite di spesa e abilitare automaticamente la sottoscrizione.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Rimuovere il limite di spesa nel Centro account

È possibile rimuovere il limite di spesa in qualsiasi momento, purché sia presente un metodo di pagamento valido associato alla sottoscrizione di Azure. Per le offerte con credito per più mesi, è anche possibile abilitare il limite di spesa all'inizio del periodo di fatturazione successivo.

Per rimuovere il limite di spesa, seguire questi passaggi:

1. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions).
1. Selezionare una sottoscrizione. Se la sottoscrizione è disabilitata perché è stato raggiunto il limite di spesa, fare clic sulla notifica: **La sottoscrizione ha raggiunto il limite di spesa ed è stata disabilitata per impedire addebiti.** In caso contrario, fai clic su **Rimuovere il limite di spesa** nell'area **STATO SOTTOSCRIZIONE**.
1. Seleziona l'opzione appropriata.

![Selezione di un'opzione per la rimozione del limite di spesa](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opzione | Effetto |
| --- | --- |
| Rimuovi il limite di spesa per un periodo illimitato | Rimuove il limite di spesa senza riattivarlo automaticamente all'inizio del periodo di fatturazione successivo. |
| Rimuovi il limite di spesa per il periodo di fatturazione corrente | Rimuove il limite di spesa in modo che si riattivi automaticamente all'inizio del periodo di fatturazione successivo. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Perché potrebbe essere necessario rimuovere il limite di spesa

Il limite di spesa può impedire all'utente di distribuire o usare determinati servizi Microsoft e di terze parti. Di seguito sono riportate le situazioni in cui è necessario rimuovere il limite di spesa per la sottoscrizione.

-  Se si prevede di distribuire immagini proprietarie come Oracle e servizi come Azure DevOps Services. Questa situazione comporta il superamento quasi immediato del limite di spesa e causa la disabilitazione della sottoscrizione.
- Si dispone di servizi che non si desidera vengano interrotti.
- Se si usano servizi e risorse con impostazioni come indirizzi IP virtuali che non si vuole perdere. Queste impostazioni vanno perse quando le risorse e i servizi vengono deallocati.

## <a name="turn-on-the-spending-limit-after-removing"></a>Attivare il limite di spesa dopo la rimozione

Questa funzionalità è disponibile solo quando il limite di spesa è stato rimosso per un periodo illimitato. Modificarlo in modo da attivarlo automaticamente all'inizio del periodo di fatturazione successivo.

1. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions).
1. Fare clic sul banner giallo per modificare l'opzione relativa al limite di spesa.
1. Scegliere **Attiva limite di spesa per il prossimo periodo di fatturazione \<data di inizio del periodo di fatturazione\>** .

## <a name="custom-spending-limit"></a>Limite di spesa personalizzato

Non sono disponili limiti di spesa personalizzati.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Un limite di spesa non impedisce tutti gli addebiti

[Alcuni servizi esterni pubblicati in Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) non possono essere usati con i crediti di sottoscrizione e possono sostenere costi distinti anche quando è impostato il limite di spesa. Questo succede, ad esempio, per le licenze di Visual Studio, Azure Active Directory Premium, i piani di supporto e la maggior parte dei servizi personalizzati di terze parti. Quando si esegue il provisioning di un nuovo servizio esterno, viene visualizzato un avviso per informare che i servizi vengono fatturati separatamente:

![Avviso di acquisto di Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Eseguire l'aggiornamento a un piano con prezzi con [pagamento in base](billing-upgrade-azure-subscription.md) al consumo.
