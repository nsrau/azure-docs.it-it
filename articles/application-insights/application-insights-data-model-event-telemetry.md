---
title: Modello di dati di Azure Application Insights Telemetry - Telemetria degli eventi | Microsoft Docs
description: Modello di dati di Application Insights per la telemetria degli eventi
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 1cad3594be32e59ea6bd3d3ba2289d391bd92c0b
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017

---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria degli eventi: modello di dati di Application Insights

È possibile creare elementi di telemetria degli eventi (in [Application Insights](app-insights-overview.md)) per rappresentare un evento che si è verificato nell'applicazione. Si tratta in genere di un'interazione dell'utente, ad esempio il clic su un pulsante o il completamento della transazione di un ordine. Può inoltre essere un evento del ciclo di vita dell'applicazione come l'inizializzazione o l'aggiornamento della configurazione. 

A livello semantico gli eventi possono essere correlati o meno a richieste. Se usata correttamente, la telemetria degli eventi è tuttavia più importante delle richieste o delle tracce. Gli eventi rappresentano dati di telemetria business e devono essere oggetto di [campionamento](app-insights-api-filtering-sampling.md) separato, meno rigido.

## <a name="name"></a>Nome

Nome evento. Per consentire un raggruppamento adeguato e metriche utili, limitare l'applicazione in modo che generi un numero ridotto di nomi di eventi distinti. Ad esempio, non usare un nome distinto per ogni istanza generata di un evento.

Lunghezza massima: 512 caratteri

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Misure personalizzate

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](application-insights-data-model.md).
- [Scrivere dati di telemetria di eventi personalizzati](app-insights-api-custom-events-metrics.md#trackevent)
- Verificare quali [piattaforme](app-insights-platforms.md) supportano Application Insights.

