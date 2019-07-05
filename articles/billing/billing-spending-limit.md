---
title: Limite di spesa di Azure | Microsoft Docs
description: Questo articolo descrive come funziona un limite di spesa di Azure e su come rimuoverlo.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490603"
---
# <a name="azure-spending-limit"></a>Limite di spesa di Azure

Il limite di spesa di Azure impedisce la spesa all'importo del credito. Tutti i nuovi clienti che effettuano l'iscrizione per una versione di valutazione di Azure o a offerte che includono crediti per più mesi sono attivato per impostazione predefinita il limite di spesa. Il limite di spesa è $0, ma non può essere modificato. Il limite di spesa non è disponibile per le sottoscrizioni in alcuni piani di tali piani con pagamento a consumo e piani di impegno. Vedi l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Raggiungere un limite di spesa

Quando l'uso comporta costi che esauriscono gli importi mensili inclusi nella sottoscrizione di Azure, i servizi distribuiti vengono disabilitati per il resto del periodo di fatturazione.

Ad esempio, quando si spende tutto il credito incluso nella sottoscrizione, le risorse di Azure distribuiti verranno rimossi dalla produzione e macchine virtuali di Azure vengono arrestate e DEALLOCATE. I dati negli account di archiviazione sono disponibili in sola lettura.

All'inizio del periodo di fatturazione successivo, se l'offerta di sottoscrizione include crediti più mesi, la sottoscrizione verrà riabilitata automaticamente. È quindi possibile ridistribuire le risorse di Azure e hanno accesso completo agli account di archiviazione e database.

Azure Invia messaggio di posta elettronica limitano le notifiche quando si raggiunge la spesa per la sottoscrizione. Accedi per il [centro Account](https://account.windowsazure.com/Subscriptions) per visualizzare le notifiche relative alle sottoscrizioni che hanno raggiunto il limite di spesa.

Se si dispone di una sottoscrizione di valutazione gratuita e si raggiunge il limite di spesa, è possibile eseguire l'aggiornamento a un piano con [pagamento a consumo](billing-upgrade-azure-subscription.md) prezzi per rimuovere la spesa limitare e abilita automaticamente la sottoscrizione.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Rimuovere il limite di spesa nel Centro account

È possibile rimuovere il limite di spesa in qualsiasi momento, purché vi sia un metodo di pagamento valido associato alla sottoscrizione di Azure. Per le offerte con credito per più mesi, è anche possibile abilitare il limite di spesa all'inizio del periodo di fatturazione successivo.

Per rimuovere il limite di spesa, seguire questi passaggi:

1. Accedi per il [centro Account](https://account.windowsazure.com/Subscriptions).
1. Selezionare una sottoscrizione. Se la sottoscrizione è disabilitata a causa il raggiungimento del limite di spesa, fare clic sulla notifica: **Sottoscrizione ha raggiunto il limite di spesa ed è stata disabilitata per impedire addebiti.** In caso contrario, fai clic su **Rimuovere il limite di spesa** nell'area **STATO SOTTOSCRIZIONE**.
1. Seleziona l'opzione appropriata.

![Selezione di un'opzione per la rimozione del limite di spesa](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opzione | Effetto |
| --- | --- |
| Rimuovi il limite di spesa per un periodo illimitato | Rimuove il limite di spesa senza riattivarlo automaticamente all'inizio del periodo di fatturazione successivo. |
| Rimuovi il limite di spesa per il periodo di fatturazione corrente | Rimuove il limite di spesa in modo che si riattivi automaticamente all'inizio del periodo di fatturazione successivo. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Il motivo per cui si potrebbe voler rimuovere il limite di spesa

Il limite di spesa può impedire all'utente di distribuire o usare determinati servizi Microsoft e di terze parti. Ecco le situazioni in cui è necessario rimuovere il limite di spesa della sottoscrizione.

-  Se si prevede di distribuire immagini proprietarie come Oracle e servizi come Azure DevOps Services. Questa situazione determina il superamento quasi immediato del limite di spesa e fa sì che la sottoscrizione deve essere disabilitata.
- Sono disponibili servizi che si preferisce non interrotti.
- Se si usano servizi e risorse con impostazioni come indirizzi IP virtuali che non si vuole perdere. Queste impostazioni vanno perse quando le risorse e i servizi vengono deallocati.

## <a name="turn-on-the-spending-limit-after-removing"></a>Attivare il limite di spesa dopo la rimozione

Questa funzionalità è disponibile solo quando il limite di spesa è stato rimosso per un periodo illimitato. Modificarlo in modo da attivarlo automaticamente all'inizio del periodo di fatturazione successivo.

1. Accedi per il [centro Account](https://account.windowsazure.com/Subscriptions).
1. Fare clic sul banner giallo per modificare l'opzione relativa al limite di spesa.
1. Scegliere **Attiva limite di spesa per il prossimo periodo di fatturazione \<data di inizio del periodo di fatturazione\>** .

## <a name="custom-spending-limit"></a>Limite di spesa personalizzato

Non sono disponili limiti di spesa personalizzati.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Un limite di spesa non impedisce a tutti i costi

[Alcuni servizi esterni pubblicati in Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) non può essere usato con i crediti della sottoscrizione e possono incorrere in addebiti separati anche quando il limite di spesa è impostato. Questo succede, ad esempio, per le licenze di Visual Studio, Azure Active Directory Premium, i piani di supporto e la maggior parte dei servizi personalizzati di terze parti. Quando si esegue il provisioning di un nuovo servizio esterno, viene visualizzato un avviso per informare che i servizi vengono fatturati separatamente:

![Avviso di acquisto di Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Eseguire l'aggiornamento a un piano con [pagamento a consumo](billing-upgrade-azure-subscription.md) prezzi.
