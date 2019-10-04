---
title: API del servizio di misurazione del Marketplace-domande frequenti | Azure Marketplace
description: Creare l'utilizzo di un'offerta SaaS in Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: ccab7e59eaa925df4ba46447cef458111dc7e60a
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869573"
---
# <a name="marketplace-metering-service-apis---faq"></a>API del servizio di misurazione di Marketplace - domande frequenti

Quando un utente di Azure sottoscrive un servizio SaaS che include la fatturazione a consumo, si tiene traccia del consumo per ogni dimensione di fatturazione usata dal cliente. Se il consumo supera le quantità incluse impostate per il termine selezionato dal cliente, il servizio emetterà gli eventi di utilizzo a Microsoft.

## <a name="emit-usage-events"></a>Generare eventi di utilizzo

>[!Note]
>Questa sezione è applicabile solo alle offerte SaaS, in cui almeno uno dei piani ha dimensioni del servizio di misurazione definite al momento della pubblicazione dell'offerta.

![Generare eventi di utilizzo](media/isv-emits-usage-event.png)

Vedere l' [API dell'evento utilizzo batch Saas](./marketplace-metering-service-apis.md#batch-usage-event) per informazioni sul contratto API per la creazione di eventi di utilizzo.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Con quale frequenza si prevede di emettere utilizzo?

Idealmente, è previsto che l'utilizzo venga emesso ogni ora nell'ultima ora, solo se è presente un utilizzo nell'ora precedente.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Qual è il ritardo massimo tra il momento in cui si verifica un evento e il momento in cui viene generato un evento di utilizzo a Microsoft?

Idealmente, l'evento Usage viene generato ogni ora per gli eventi che si sono verificati nell'ultima ora. Tuttavia, sono previsti ritardi. Il ritardo massimo consentito è di 24 ore, dopo i quali gli eventi di utilizzo non verranno accettati.

Se, ad esempio, un evento di utilizzo si verifica alle ore 1 di un giorno, il giorno successivo per emettere un evento di utilizzo associato a questo evento sarà necessario attendere il giorno 1. Ciò significa che, nel caso in cui l'utilizzo dell'emissione del sistema abbia un tempo di inattività, può ripristinare e quindi inviare l'evento di utilizzo per l'intervallo di ore in cui si è verificato l'utilizzo, senza perdita di fedeltà.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Cosa accade quando si invia più di un evento di utilizzo nella stessa ora?

Per l'intervallo di ore è accettato un solo evento Usage. L'intervallo di ore inizia al minuto 0 e termina al minuto 59.  Se viene generato più di un evento di utilizzo per lo stesso intervallo di ore, eventuali eventi di utilizzo successivi vengono eliminati come duplicati.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Cosa accade quando si emette l'utilizzo per una sottoscrizione SaaS di cui è già stata annullata la sottoscrizione?

Tutti gli eventi di utilizzo emessi alla piattaforma Marketplace non verranno accettati dopo l'eliminazione di una sottoscrizione SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>È possibile ottenere un elenco di tutte le sottoscrizioni SaaS, incluse le sottoscrizioni attive e non sottoscritte?

Sì, quando si chiama l' `GET /saas/subscriptions` API include un elenco di tutte le sottoscrizioni Saas. Il campo stato nella risposta per ogni sottoscrizione SaaS acquisisce se la sottoscrizione è attiva o non sottoscritta. La chiamata alle sottoscrizioni List restituisce al massimo 100 sottoscrizioni al momento.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md) .
