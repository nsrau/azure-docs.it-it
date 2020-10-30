---
title: Metriche supportate da griglia di eventi di Azure
description: Questo articolo fornisce le metriche di monitoraggio di Azure supportate dal servizio griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 87c91077c8eeca2134da53774979c212a82e3b7d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042133"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metriche supportate da griglia di eventi di Azure
Questo articolo fornisce elenchi di metriche di griglia di eventi categorizzate in base agli spazi dei nomi. 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|Argomento, ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Argomento|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
| AdvancedFilterEvaluationCount | Sì | Valutazioni avanzate del filtro | Conteggio | Totale | Totale filtri avanzati valutati nelle sottoscrizioni di eventi | EventSubscriptionName |



## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Error, ErrorType|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Nessuna dimensione|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Nessuna dimensione|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Nessuna dimensione|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason, EventSubscriptionName|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|
| AdvancedFilterEvaluationCount | Sì | Valutazioni avanzate del filtro | Conteggio | Totale | Totale filtri avanzati valutati nelle sottoscrizioni di eventi | EventSubscriptionName |



## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason, EventSubscriptionName|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|
| AdvancedFilterEvaluationCount | Sì | Valutazioni avanzate del filtro | Conteggio | Totale | Totale filtri avanzati valutati nelle sottoscrizioni di eventi | Argomento, EventSubscriptionName, DomainEventSubscriptionName |

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente: [log di diagnostica](diagnostic-logs.md)
