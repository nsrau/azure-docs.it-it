---
title: Modello di dati di Azure Application Insights Telemetry - Telemetria degli eventi | Microsoft Docs
description: Modello di dati di Application Insights per la telemetria degli eventi
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671881"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria degli eventi: modello di dati di Application Insights

È possibile creare elementi di telemetria degli eventi (in [Application Insights](../../azure-monitor/app/app-insights-overview.md)) per rappresentare un evento che si è verificato nell'applicazione. Si tratta in genere di un'interazione dell'utente, ad esempio il clic su un pulsante o il completamento della transazione di un ordine. Può inoltre essere un evento del ciclo di vita dell'applicazione come l'inizializzazione o l'aggiornamento della configurazione. 

A livello semantico gli eventi possono essere correlati o meno a richieste. Se usata correttamente, la telemetria degli eventi è tuttavia più importante delle richieste o delle tracce. Gli eventi rappresentano dati di telemetria business e devono essere oggetto di [campionamento](../../azure-monitor/app/api-filtering-sampling.md) separato, meno rigido.

## <a name="name"></a>Name

Nome evento. Per consentire un raggruppamento adeguato e metriche utili, limitare l'applicazione in modo che generi un numero ridotto di nomi di eventi distinti. Ad esempio, non usare un nome distinto per ogni istanza generata di un evento.

Lunghezza massima: 512 caratteri

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Misure personalizzate

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](data-model.md).
- [Scrivere dati di telemetria di eventi personalizzati](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Verificare quali [piattaforme](../../azure-monitor/app/platforms.md) supportano Application Insights.
