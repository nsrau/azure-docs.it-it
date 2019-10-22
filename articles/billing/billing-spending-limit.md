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
ms.openlocfilehash: 97e6e04bf7fab3a48f1ce0552e1b653b208be77b
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375442"
---
# <a name="azure-spending-limit"></a>Limite di spesa di Azure

Il limite di spesa in Azure impedisce spese superiori all'importo del credito. Per tutti i nuovi clienti che eseguono l'iscrizione per un account Azure gratuito o per tipi di sottoscrizioni che includono crediti per più mesi, il limite di spesa è attivato per impostazione predefinita. Il limite di spesa corrisponde all'importo del credito e non può essere modificato. Ad esempio, se è stata effettuata l'iscrizione per un account Azure gratuito, il limite di spesa è pari a $ 200 e non è possibile aumentarlo a $ 500. È tuttavia possibile rimuovere il limite di spesa. Di conseguenza, o non è presente alcun limite oppure è presente un limite pari all'importo del credito. Questo preclude la maggior parte delle tipologie di spesa. Il limite di spesa non è disponibile per le sottoscrizioni con piani di impegno e piani che prevedono prezzi con pagamento in base al consumo. Vedere l'[elenco completo dei tipi di sottoscrizioni di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Raggiungimento di un limite di spesa

Quando l'utilizzo effettuato genera addebiti che esauriscono il limite di spesa, i servizi distribuiti vengono disabilitati per i giorni del periodo di fatturazione rimanenti.

Se, ad esempio, si spende tutto il credito incluso nell'account Azure gratuito, le risorse di Azure distribuite vengono rimosse dalla produzione e le macchine virtuali di Azure vengono arrestate e deallocate. I dati negli account di archiviazione sono disponibili in sola lettura.

Se il tipo di sottoscrizione include crediti per più mesi, la sottoscrizione verrà riabilitata automaticamente all'inizio del periodo di fatturazione successivo. È possibile quindi ridistribuire le risorse di Azure e avere accesso completo agli account e ai database di archiviazione.

Al raggiungimento del limite di spesa, Azure invia notifiche di posta elettronica. Accedere al [Portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) per visualizzare le notifiche relative alle sottoscrizioni che hanno raggiunto il limite di spesa.

Se si usa un account Azure gratuito e si raggiunge il limite di spesa, è possibile eseguire l'aggiornamento a un piano con [pagamento in base al consumo](billing-upgrade-azure-subscription.md) per rimuovere il limite di spesa e riabilitare automaticamente la sottoscrizione.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>Rimuovere il limite di spesa nel portale di Azure

È possibile rimuovere il limite di spesa in qualsiasi momento, purché alla sottoscrizione di Azure sia associato un metodo di pagamento valido. Per i tipi di sottoscrizioni con credito su più mesi, ad esempio Visual Studio Enterprise e Visual Studio Professional, è anche possibile abilitare il limite di spesa all'inizio del periodo di fatturazione successivo.

Per rimuovere il limite di spesa, seguire questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selezionare una sottoscrizione. Se viene raggiunto il limite di spesa della sottoscrizione, verrà disabilitata.
1. Selezionare **Rimuovere il limite di spesa** nella parte superiore della pagina.
1. Seleziona l'opzione appropriata.

![Selezione di un'opzione per la rimozione del limite di spesa](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opzione | Effetto |
| --- | --- |
| Rimuovi il limite di spesa per un periodo illimitato | Rimuove il limite di spesa senza riattivarlo automaticamente all'inizio del periodo di fatturazione successivo. |
| Rimuovi il limite di spesa per il periodo di fatturazione corrente | Rimuove il limite di spesa in modo che si riattivi automaticamente all'inizio del periodo di fatturazione successivo. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Perché può essere necessario rimuovere il limite di spesa

Il limite di spesa può impedire all'utente di distribuire o usare determinati servizi Microsoft e di terze parti. Ecco le situazioni in cui è consigliabile rimuovere il limite di spesa per la sottoscrizione.

-  Se si prevede di distribuire immagini proprietarie come Oracle o servizi come Azure DevOps Services. Questa situazione comporta il superamento quasi immediato del limite di spesa e la disabilitazione della sottoscrizione.
- Se si usano servizi che non devono essere interrotti. Quando si raggiunge il limite di spesa, le risorse di Azure distribuite vengono rimosse dall'ambiente di produzione e le macchine virtuali di Azure vengono arrestate e deallocate. Per evitare che i servizi usati vengano interrotti, è necessario rimuovere il limite di spesa.
- Se si usano servizi e risorse con impostazioni come indirizzi IP virtuali che non si vuole perdere. Queste impostazioni vanno perse quando si raggiunge il limite di spesa e le risorse e i servizi vengono deallocati.

## <a name="turn-on-the-spending-limit-after-removing"></a>Attivare il limite di spesa dopo la rimozione

Questa funzionalità è disponibile solo quando il limite di spesa è stato rimosso per un periodo illimitato per i tipi di sottoscrizioni che includono crediti su più mesi. È possibile usare questa funzionalità per attivare automaticamente il limite di spesa all'inizio del periodo di fatturazione successivo.

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
