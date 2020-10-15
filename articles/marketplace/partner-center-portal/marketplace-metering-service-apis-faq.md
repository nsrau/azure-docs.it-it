---
title: Domande frequenti sulle API del servizio di misurazione-Marketplace commerciale Microsoft
description: Domande frequenti sulle API del servizio di misurazione per le offerte SaaS in Microsoft AppSource e in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320001"
---
# <a name="marketplace-metered-billing-apis---faq"></a>API di fatturazione a consumo del Marketplace-domande frequenti

Quando un cliente sottoscrive un servizio SaaS o applicazione Azure con un piano di app gestite, con fatturazione a consumo, si tiene traccia del consumo per ogni dimensione di fatturazione usata.  Se il consumo supera le quantità incluse impostate per il termine selezionato dal cliente, il servizio emetterà gli eventi di utilizzo a Microsoft.

## <a name="for-both-saas-offers-and-managed-apps"></a>Per le offerte SaaS e le app gestite

### <a name="how-often-is-it-expected-to-emit-usage"></a>Con quale frequenza si prevede di emettere utilizzo?

Idealmente, è previsto che l'utilizzo venga emesso ogni ora nell'ultima ora, solo se è presente un utilizzo nell'ora precedente.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>C'è un periodo massimo tra un'emissione e l'altra?

Non esiste alcuna limitazione di questo tipo. Generare l'utilizzo solo quando si verifica. Se, ad esempio, è necessario inviare solo un'unità di utilizzo per ogni durata della sottoscrizione, è possibile eseguire questa operazione.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Qual è il ritardo massimo tra il momento in cui si verifica un evento e il momento in cui viene generato un evento di utilizzo a Microsoft?

Idealmente, l'evento Usage viene generato ogni ora per gli eventi che si sono verificati nell'ultima ora. Tuttavia, sono previsti ritardi. Il ritardo massimo consentito è di 24 ore, dopo i quali gli eventi di utilizzo non verranno accettati. La procedura consigliata consiste nel raccogliere l'utilizzo orario e nell'emettere è come un evento alla fine dell'ora.

Se, ad esempio, un evento di utilizzo si verifica alle ore 1 di un giorno, il giorno successivo per emettere un evento di utilizzo associato a questo evento sarà necessario attendere il giorno 1.  Se l'utilizzo del sistema è inattivo, può ripristinare e quindi inviare l'evento di utilizzo per l'intervallo di ore in cui si è verificato l'utilizzo, senza perdita di fedeltà.

Se sono trascorse 24 ore dopo l'utilizzo effettivo, è comunque possibile emettere le unità utilizzate con gli eventi di utilizzo successivi.  Questa pratica potrebbe tuttavia danneggiare la credibilità dei report degli eventi di fatturazione per il cliente finale.  Si consiglia di evitare di inviare le emissioni del contatore una volta al giorno/settimana al mese.  Sarà più difficile comprendere l'utilizzo effettivo da parte di un cliente e risolvere problemi o domande che potrebbero essere generate in relazione agli eventi di utilizzo.

Un altro motivo per inviare l'utilizzo ogni ora è evitare situazioni in cui l'utente annulla la sottoscrizione prima che il server di pubblicazione invii l'evento di emissione giornaliero/settimanale/mensile.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>Cosa accade quando si invia più di un evento di utilizzo nella stessa ora?

Viene accettato un solo evento di utilizzo per l'intervallo di un'ora. L'intervallo di ore inizia al minuto 0 e termina al minuto 59.  Se viene generato più di un evento Usage per la stessa ora, eventuali eventi di utilizzo successivi vengono eliminati come duplicati.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>Cosa accade quando il cliente annulla l'acquisto entro il tempo consentito dai criteri di annullamento?

L'importo forfettario non verrà addebitato ma l'utilizzo in eccedenza sarà.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>È possibile usare piani di misurazione personalizzati per i pagamenti monouso?

Sì, è possibile definire una dimensione personalizzata come una sola unità di pagamento monouso e generarla una sola volta per ogni cliente.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>È possibile usare piani di misurazione personalizzati per il modello di determinazione prezzi a più livelli?

Sì, può essere implementato con ogni dimensione personalizzata che rappresenta un singolo livello di prezzo.

Ad esempio, Contoso desidera addebitare $0,5 per ogni messaggio di posta elettronica per i primi 1000 e-mail, $0,4 per messaggio di posta elettronica da 1000 a 5000 e $0,2 per messaggio di posta elettronica per i messaggi di posta elettronica sopra 5000. Possono definire tre dimensioni personalizzate, che corrispondono ai tre piani tariffari di posta elettronica. Creare unità della prima dimensione fino a quando il numero di messaggi di posta elettronica rimane sotto 1000, quindi le unità della seconda dimensione quando il numero di messaggi di posta elettronica è compreso tra 1000 e 5000 e infine le unità della terza dimensione per i messaggi di posta elettronica precedenti a 5000.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Cosa accade se il servizio di misurazione del Marketplace presenta un'interruzione?

Se l'ISV Invia un contatore personalizzato e riceve un errore, potrebbe essere stato causato da un problema sul lato Microsoft (in genere, nel caso in cui gli eventi simili fossero stati accettati prima senza errori), l'ISV dovrebbe attendere e ripetere l'emissione.

Se l'errore viene mantenuto, inviare nuovamente il contatore personalizzato l'ora successiva (accumulo la quantità). Continuare questo processo fino a quando non viene ricevuta una risposta non di errore.

## <a name="for-saas-offers-only"></a>Solo per le offerte SaaS

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Cosa accade quando si emette l'utilizzo per una sottoscrizione SaaS di cui è già stata annullata la sottoscrizione?

Tutti gli eventi di utilizzo generati nella piattaforma Marketplace non verranno accettati dopo l'eliminazione di una sottoscrizione SaaS.

L'utilizzo può essere emesso solo per le sottoscrizioni nello stato sottoscritto (e non per le sottoscrizioni in `PendingFulfillmentStart` `Suspended` stato, o `Unsubscribed` ).

L'unica eccezione è rappresentata dall'utilizzo del tempo prima dell'annullamento della sottoscrizione SaaS.

Ad esempio, il cliente ha annullato la sottoscrizione SaaS oggi alle 15.00. A questo punto, il server di pubblicazione può ancora emettere l'utilizzo per il periodo compreso tra le 18.00 di ieri e le 3 pm odierne per questa sottoscrizione SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>È possibile ottenere un elenco di tutte le sottoscrizioni SaaS, incluse le sottoscrizioni attive e non sottoscritte?

Sì, quando si chiama l' [API get subscriptions list](pc-saas-fulfillment-api-v2.md#subscription-apis) , perché include un elenco di tutte le sottoscrizioni Saas. Il campo stato nella risposta per ogni sottoscrizione SaaS acquisisce se la sottoscrizione è attiva o non sottoscritta.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>Le date di inizio e di fine del periodo di sottoscrizione SaaS e della relativa emissione di utilizzo sono connesse?

Gli eventi in eccedenza possono essere emessi in qualsiasi momento per la sottoscrizione SaaS esistente nello stato *sottoscritto* . È responsabilità dell'editore creare eventi di utilizzo in base ai criteri definiti nel piano di fatturazione. L'eccedenza deve essere calcolata in base alle date definite nel periodo di validità della sottoscrizione SaaS. 

Se, ad esempio, il server di pubblicazione definisce un piano SaaS che include 1000 di messaggi di posta elettronica per $100 in frequenza fissa mensile, ogni messaggio di posta elettronica sopra 1000 viene $1 fatturato in base alla dimensione personalizzata.

Quando il cliente acquista e attiva la sottoscrizione il 6 gennaio, l'indirizzo di posta elettronica 1000 incluso nella tariffa fissa verrà conteggiato a partire da questo giorno. Quindi, se fino al 5 febbraio (fine del primo mese della sottoscrizione) verranno inviati solo 900 messaggi di posta elettronica, il cliente pagherà la tariffa fissa solo per il primo mese della sottoscrizione e nessun evento di utilizzo eccedente verrà emesso dall'editore tra il 6 gennaio e il 5 febbraio. Il 6 febbraio la sottoscrizione verrà rinnovata automaticamente e il conteggio verrà riavviato. Se il 15 febbraio il cliente ha raggiunto 1000 di messaggi di posta elettronica inviati, il resto dei messaggi di posta elettronica inviati fino al 5 marzo verrà addebitato come eccedenza ($1 per posta elettronica) in base agli eventi di utilizzo eccedenti emessi dall'editore.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md).
