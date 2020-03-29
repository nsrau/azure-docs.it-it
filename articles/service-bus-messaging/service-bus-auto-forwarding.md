---
title: Inoltro automatico delle entità di messaggistica del bus di servizio di AzureAuto-forwarding Azure Service Bus messaging entities
description: Questo articolo descrive come concatenare una coda o una sottoscrizione del bus di servizio di Azure a un altro argomento o coda.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8b8883b579233962de61e7247e6ac1cbcb2a6d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761050"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Concatenamento di entità del bus di servizio con l'inoltro automatico

La funzionalità di *inoltro automatico* del bus di servizio consente di concatenare una coda o una sottoscrizione a un'altra coda o a un altro argomento che fa parte dello stesso spazio dei nomi. Quando l'inoltro automatico è abilitato, il bus di servizio rimuove automaticamente i messaggi presenti nella prima coda o sottoscrizione (origine) e li inserisce nella seconda coda o argomento (destinazione). È comunque possibile inviare un messaggio direttamente all'entità di destinazione.

## <a name="using-autoforwarding"></a>Uso dell'inoltro automatico

Per abilitare l'inoltro automatico, è possibile impostare la proprietà [QueueDescription.ForwardTo][QueueDescription.ForwardTo] o [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] nell'oggetto [QueueDescription][QueueDescription] o [SubscriptionDescription][SubscriptionDescription] per l'origine, come illustrato nell'esempio seguente:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

L'entità di destinazione deve essere presente al momento della creazione dell'entità di origine. In caso contrario, il bus di servizio restituisce un'eccezione quando gli viene chiesto di creare l'entità di origine. 

È possibile usare l'inoltro automatico per aumentare il numero di istanze di un singolo argomento. Il bus di servizio limita il [numero delle sottoscrizioni per un determinato argomento](service-bus-quotas.md) a 2000. Creando argomenti di secondo livello, è possibile aggiungere altre sottoscrizioni. Pur non essendo vincolati dalla limitazione del bus di servizio relativa al numero delle sottoscrizioni, l'aggiunta di un secondo livello di argomenti consente complessivamente di migliorare la velocità effettiva dell'argomento.

![Scenario di inoltro automatico][0]

È possibile usare l'inoltro automatico anche per disaccoppiare i mittenti dei messaggi dai destinatari. Si consideri ad esempio un sistema ERP costituito da tre moduli: elaborazione degli ordini, gestione del magazzino e gestione dei rapporti con i clienti. Ognuno di questi moduli genera messaggi che vengono accodati in un argomento corrispondente. Due dei rappresentanti di vendita sono interessati a tutti i messaggi correlati ai propri clienti. Per ricevere questi messaggi, i due rappresentanti creano una coda personale e una sottoscrizione per ognuno degli argomenti ERP che inoltrano automaticamente tutti i messaggi alla rispettiva coda.

![Scenario di inoltro automatico][1]

Se uno dei rappresentanti si assenta, viene riempita la coda personale, ma non l'argomento ERP. In questo scenario, poiché un rappresentante di vendita non ha ricevuto alcun messaggio, nessuno degli argomenti ERP raggiunge la quota.

> [!NOTE]
> Quando è impostata l'inoltro automatico, il valore di AutoDeleteOnIdle **sia nell'origine che nella destinazione** viene impostato automaticamente sul valore massimo del tipo di dati.
> 
>   - Sul lato origine, l'inoltro automatico funge da operazione di ricezione. Quindi la fonte che ha l'impostazione di autoforwarding non è mai veramente "inattivo".
>   - Sul lato di destinazione, questa operazione viene eseguita per garantire che vi sia sempre una destinazione a cui inoltrare il messaggio.

## <a name="autoforwarding-considerations"></a>Considerazioni sull'inoltro automatico

Se l'entità di destinazione accumula troppi messaggi e supera la quota oppure è disabilitata, l'entità di origine aggiunge i messaggi alla relativa [coda dei messaggi non recapitabili](service-bus-dead-letter-queues.md) finché non si libera spazio nella destinazione o l'entità non viene riabilitata. I messaggi restano nella coda dei messaggi non recapitabili, pertanto sarà necessario riceverli ed elaborarli in modo esplicito da tale coda.

Se si concatenano singoli argomenti per ottenere un argomento composito con diverse sottoscrizioni, è consigliabile avere un certo numero di sottoscrizioni nell'argomento di primo livello e un numero più elevato di sottoscrizioni negli argomenti di secondo livello. Ad esempio, un argomento di primo livello con 20 sottoscrizioni, ognuna delle quali concatenata a un argomento di secondo livello con 200 sottoscrizioni, consente una velocità effettiva più alta rispetto a un argomento di primo livello con 200 sottoscrizioni, ognuna delle quali concatenata a un argomento di secondo livello con 20 sottoscrizioni.

Il bus di servizio addebita un'operazione per ogni messaggio inoltrato. Ad esempio, se viene inviato un messaggio a un argomento con 20 sottoscrizioni, ognuna delle quali configurata per l'inoltro automatico dei messaggi a una coda o un argomento differente, vengono addebitate 21 operazioni se tutte le sottoscrizioni di primo livello ricevono una copia del messaggio.

Per creare una sottoscrizione concatenata a un'altra coda o a un altro argomento, l'autore della sottoscrizione deve disporre delle autorizzazioni **di gestione** sia per l'entità di origine che per l'entità di destinazione. Per l'invio di messaggi solo all'argomento di origine sono necessarie le autorizzazioni di **invio** per l'argomento di origine.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sull'inoltro automatico, vedere gli argomenti di riferimento seguenti:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Per altre informazioni sui miglioramenti delle prestazioni del bus di servizio, vedere 

* [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica del bus di servizio](service-bus-performance-improvements.md)
* [Entità di messaggistica partizionate][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
