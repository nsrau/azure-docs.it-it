---
title: API del servizio di misurazione del Marketplace - Domande frequenti Azure Marketplace
description: Generare l'utilizzo di un'offerta SaaS in Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275782"
---
# <a name="marketplace-metering-service-apis---faq"></a>API del servizio di misurazione di Marketplace - domande frequenti

Dopo che un utente di Azure sottoscrive un servizio SaaS che include la fatturazione a consumo, si terrà traccia del consumo per ogni dimensione di fatturazione usata dal cliente. Se il consumo supera le quantità incluse impostate per il termine selezionato dal cliente, il servizio genererà eventi di uso a Microsoft.

## <a name="emit-usage-events"></a>Generare eventi di uso

>[!Note]
>Questa sezione è applicabile solo alle offerte SaaS, in cui almeno uno dei piani ha dimensioni del servizio di misurazione definite al momento della pubblicazione dell'offerta.

![Generare eventi di uso](media/isv-emits-usage-event.png)

Vedere l'API degli eventi di [uso batch SaaS](./marketplace-metering-service-apis.md#batch-usage-event) per informazioni sul contratto API per la generazione di eventi di uso.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Con quale frequenza si prevede di emettere l'utilizzo?

Idealmente, ci si aspetta di emettere l'utilizzo ogni ora per l'ora passata, solo se c'è utilizzo nell'ora precedente.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Qual è il ritardo massimo tra il momento in cui si verifica un evento e il momento in cui un evento di uso viene inviato a Microsoft?

Idealmente, l'evento di uso viene generato ogni ora per gli eventi che si sono verificati nell'ultima ora. Tuttavia, sono previsti ritardi. Il ritardo massimo consentito è 24 ore, dopo le quali gli eventi di uso non verranno accettati.

Ad esempio, se un evento di uso si verifica alle 13:00 di un giorno, è necessario fino alle 13:00 del giorno successivo per generare un evento di uso associato a questo evento. Ciò significa che nel caso in cui il sistema che emette l'utilizzo ha un tempo di inattività, può recuperare e quindi inviare l'evento di uso per l'intervallo di ore in cui si è verificato l'utilizzo, senza perdita di fedeltà.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Cosa succede quando si inviano più eventi di uso nella stessa ora?

Viene accettato un solo evento di uso per l'intervallo di ore. L'intervallo di ore inizia al minuto 0 e termina al minuto 59.  Se vengono generati più eventi di uso per lo stesso intervallo di ore, tutti gli eventi di uso successivi vengono eliminati come duplicati.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Cosa succede quando si emette l'utilizzo per una sottoscrizione SaaS già annullata?

Qualsiasi evento di uso generato nella piattaforma del marketplace non verrà accettato dopo l'eliminazione di una sottoscrizione SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>È possibile ottenere un elenco di tutti gli abbonamenti SaaS, incluse le sottoscrizioni attive e non sottoscritte?

Sì, quando si `GET /saas/subscriptions` chiama l'API include un elenco di tutte le sottoscrizioni SaaS. Il campo dello stato nella risposta per ogni sottoscrizione SaaS acquisisce se la sottoscrizione è attiva o annullata. La chiamata all'elenco Sottoscrizioni restituisce un massimo di 100 sottoscrizioni al momento.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere API del [servizio di controllo Marketplace.See Marketplace metering service APIs](./marketplace-metering-service-apis.md) for more information.
