---
title: Modello di dati di Azure Application Insights Telemetry | Microsoft Docs
description: Panoramica sul modello di dati di Application Insights
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
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Modello di dati di Application Insights Telemetry

Application Insights definisce il modello di dati di telemetria per Application Performance Management (APM). Questo modello standardizza la raccolta di dati e consente la creazione di scenari di monitoraggio indipendenti dal linguaggio e dalla piattaforma. I dati raccolti da Application Insights creano il tipico modelli di esecuzione dell'applicazione:

![Modello di applicazione di Application Insights](./media/application-insights-data-model/application-insights-data-model.png)

Esistono due tipi di applicazioni, le applicazioni con un endpoint che ricevono ***richieste*** esterne, ad esempio applicazioni Web, e le applicazioni che periodicamente "si riattivano" per elaborare dati archiviati in una posizione, ad esempio Processi Web e Funzioni. In entrambi i casi, si chiama ***operazione*** un'esecuzione univoca. L'operazione ha esito positivo o negativo tramite ***eccezione*** o può dipendere da altri servizi o risorse di archiviazione per eseguire la logica di business. Per riflettere questi concetti, il modello di dati di Application Insights definisce tre tipi di telemetria: [delle richieste](./application-insights-data-model-request-telemetry.md), [delle eccezioni](/application-insights-data-model-exception-telemetry.md) e [delle dipendenze](/application-insights-data-model-dependency-telemetry.md).

Questi tipi sono in genere definiti dal framework dell'applicazione e vengono raccolti automaticamente dall'SDK. `ASP.NET MVC` definisce la nozione di esecuzione di una richiesta nell'impianto modello-visualizzazione-controller. Contrassegna l'inizio e la fine di una richiesta. Le chiamate di dipendenza a SQL sono definite da `System.Data`. Le chiamate agli endpoint HTTP sono definite da `System.Net`. È possibile estendere i tipi di telemetria raccolti da una piattaforma e un framework specifici usando misure e proprietà personalizzate. Vi sono tuttavia casi in cui si vuole segnalare dati di telemetria personalizzati. Si può implementare il log di diagnostica usando un framework di strumentazione con cui si ha familiarità, ad esempio `Log4Net` o `System.Diagnostics`. Potrebbe in alternativa essere necessario acquisire l'interazione dell'utente con il servizio per analizzare i modelli di utilizzo. Application Insights riconosce tre tipi di dati aggiuntivi: [traccia](/application-insights-data-model-trace-telemetry.md), [evento](/application-insights-data-model-event-telemetry.md), e [metrica](/application-insights-data-model-metric-telemetry.md) per questi scenari del modello.

Il modello di dati di Application Insights Telemetry definisce la modalità di [correlazione](/correlation.md) dei dati di telemetria all'operazione di cui fanno parte. Una richiesta può ad esempio di effettuare chiamate a un database SQL e registrare informazioni di diagnostica. È possibile impostare il contesto di correlazione per gli elementi di telemetria che lo lega alla telemetria delle richieste.

## <a name="schema-improvements"></a>Miglioramenti allo schema

Il modello di dati di Application Insights rappresenta un modo semplice e pratico, ma potente, per modellare i dati di telemetria dell'applicazione. Microsoft si impegna a mantenere il modello semplice ed essenziale in modo da supportare gli scenari essenziali e da consentire l'estensione dello schema per un uso avanzato.

Per segnalare problemi e suggerimenti relativi al modello di dati e allo schema, usare il repository di GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema).

## <a name="next-steps"></a>Passaggi successivi

- Verificare quali [piattaforme](/app-insights-platforms.md) supportano Application Insights.
- Informazioni su come [estendere e filtrare i dati di telemetria](/app-insights-api-filtering-sampling.md).
- Usare il [campionamento](/app-insights-sampling.md) per ridurre al minimo la quantità di dati di telemetria in base al modello di dati.

