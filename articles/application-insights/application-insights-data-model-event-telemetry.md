---
title: Modello di dati di Azure Application Insights Telemetry - Telemetria degli eventi | Microsoft Docs
description: Modello di dati di Application Insights per la telemetria degli eventi
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria degli eventi: modello di dati di Application Insights

Un evento rappresenta un'azione in un punto nel tempo che si è verificata nell'applicazione. Si tratta in genere di un'interazione dell'utente, ad esempio il clic su un pulsante o l'estrazione di un ordine. Può inoltre essere un evento del ciclo di vita dell'applicazione come l'inizializzazione o l'aggiornamento della configurazione. Il nome dell'evento è in genere una breve stringa di cardinalità bassa. 

A livello semantico gli eventi possono ora essere correlati o meno a richieste. Se usata correttamente, la telemetria degli eventi è tuttavia più importante delle richieste o delle tracce. Gli eventi rappresentano dati di telemetria business e devono essere oggetto di campionamento separato, meno rigido.

## <a name="name"></a>Nome

Nome evento. Usare la cardinalità bassa per consentire un raggruppamento adeguato e metriche utili.

Lunghezza massima: 512 caratteri

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Misure personalizzate

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](/application-insights-data-model.md).
- Informazioni su come usare l'[API di Application Insights per metriche ed eventi personalizzati](/app-insights-asp-net-dependencies.md).
- Verificare quali [piattaforme](/app-insights-platforms.md) supportano Application Insights.

