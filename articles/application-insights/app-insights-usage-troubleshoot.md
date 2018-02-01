---
title: Risolvere i problemi relativi all'analisi di utilizzo in Azure Application Insights
description: Guida alla risoluzione dei problemi relativi all'analisi dell'utilizzo di siti e app con Application Insights.
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/16/2018
ms.author: mbullwin
ms.openlocfilehash: cb5f3052301b23eb10cd6b84ab6fae98bcc7ea18
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-usage-analytics-in-application-insights"></a>Risolvere i problemi relativi all'analisi di utilizzo in Application Insights
Questo articolo prende in esame le domande degli utenti sugli [strumenti di analisi dell'utilizzo in Application Insights](app-insights-usage-overview.md), ad esempio [Utenti, Sessioni, Eventi](app-insights-usage-segmentation.md), [Imbuti](usage-funnels.md), [Flussi utente](app-insights-usage-flows.md), [Conservazione](app-insights-usage-retention.md) e Coorti, offrendo alcune utili risposte.

## <a name="counting-users"></a>Conteggio degli utenti
**Dagli strumenti di analisi dell'utilizzo risulta che l'app ha un solo utente e una sola sessione, mentre in realtà ha molti utenti e molte sessioni. Come si possono correggere questi errori nei conteggi?**

Tutti gli eventi di telemetria in Application Insights hanno un [ID utente anonimo](application-insights-data-model-context.md) e un [ID di sessione](application-insights-data-model-context.md) come proprietà standard. Per impostazione predefinita, tutti gli strumenti di analisi dell'utilizzo eseguono il conteggio degli utenti e delle sessioni in base a questi ID. Se in queste proprietà standard non vengono inseriti ID univoci per ogni utente e sessione dell'app, negli strumenti di analisi dell'utilizzo si vedrà un conteggio errato degli utenti e delle sessioni.

Se si sta monitorando un'app Web, la soluzione più semplice consiste nell'aggiungere [Application Insights JavaScript SDK](app-insights-javascript.md) all'app e verificare che il frammento di script sia caricato in ogni pagina da monitorare. JavaScript SDK genera automaticamente ID utente anonimi e ID di sessione e quindi li inserisce negli eventi di telemetria quando vengono inviati dall'app.

Se si sta monitorando un servizio Web (senza interfaccia utente), [creare un inizializzatore di telemetria per impostare le proprietà ID utente anonimo e ID di sessione](app-insights-usage-send-user-context.md) in base alle nozioni di sessioni e utenti univoci del servizio.

Se l'app invia [ID utente autenticati](app-insights-api-custom-events-metrics.md#authenticated-users), è possibile eseguire il conteggio in base agli ID utente autenticati nello strumento Utenti. Nell'elenco a discesa "Mostra" scegliere "Utenti autenticati".

Gli strumenti di analisi dell'utilizzo non supportano attualmente il conteggio di utenti o sessioni in base a proprietà diverse da ID utente anonimo, ID utente autenticato o ID di sessione.

## <a name="naming-events"></a>Denominazione degli eventi
**Nell'app sono presenti migliaia di nomi diversi di visualizzazione pagina e di evento personalizzato. È difficile distinguere tra questi nomi e gli strumenti di analisi dell'utilizzo spesso smettono di rispondere. Come si possono risolvere questi problemi di denominazione?**

I nomi di visualizzazione pagina e quelli di evento personalizzato vengono usati in tutti gli strumenti di analisi dell'utilizzo. La corretta denominazione degli eventi è essenziale per poter ottenere risultati ottimali da questi strumenti. L'obiettivo è trovare un equilibrio tra un limitato numero di nomi eccessivamente generici ("Button clicked") e un elevato numero di nomi eccessivamente specifici ("Edit button clicked on http://www.contoso.com/index").

Per apportare modifiche ai nomi di visualizzazione pagina e a quelli di evento personalizzato inviati dall'app, è necessario modificare il codice sorgente dell'app ed eseguire nuovamente la distribuzione. **Tutti i dati di telemetria disponibili in Application Insights vengono archiviati per 90 giorni e non possono essere eliminati**. Di conseguenza, le eventuali modifiche apportate ai nomi di evento saranno completamente visibili dopo 90 giorni. Per i 90 giorni successivi alle operazioni di modifica, nei dati di telemetria verranno visualizzati sia i nomi di evento precedenti sia quelli nuovi. Sarà pertanto opportuno modificare le query e comunicare all'interno dei team tenendo presente questo aspetto.

Se l'app invia troppi nomi di visualizzazione pagina, verificare se questi nomi vengono specificati manualmente nel codice o se vengono inviati automaticamente da Application Insights JavaScript SDK:

* Se i nomi di visualizzazione pagina vengono specificati manualmente nel codice tramite l'[API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) `trackPageView`, modificare il nome in modo da renderlo meno specifico. Evitare gli errori più comuni come quello di inserire l'URL nel nome della visualizzazione pagina. Al contrario, usare il parametro URL dell'API `trackPageView`. Spostare altri dettagli dal nome di visualizzazione pagina nelle proprietà personalizzate.

* Se Application Insights JavaScript SDK invia i nomi di visualizzazione pagina in modo automatico, è possibile modificare i titoli delle pagine oppure passare all'invio manuale di questi nomi. Per impostazione predefinita, l'SDK invia il [titolo](https://developer.mozilla.org/docs/Web/HTML/Element/title) di ogni pagina come nome di visualizzazione pagina. È possibile modificare i titoli in modo da renderli più generici, ma è necessario tenere presente gli effetti che questa modifica può avere sull'ottimizzazione SEO e su altri aspetti. I nomi di visualizzazione pagina specificati manualmente con l'API `trackPageView` sostituiscono i nomi raccolti automaticamente, pertanto è possibile inviare nomi più generici nei dati di telemetria senza modificare i titoli di pagina.   

Se l'app invia troppi nomi di evento personalizzato, modificare il nome nel codice in modo da renderlo meno specifico. Evitare nuovamente di inserire gli URL e altre informazioni dinamiche o di pagina direttamente nei nomi di evento personalizzato. Al contrario, spostare questi dettagli nelle proprietà personalizzate dell'evento personalizzato tramite l'API `trackEvent`. Ad esempio, anziché `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, è consigliabile usare `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dell'analisi di utilizzo](app-insights-usage-overview.md)

## <a name="get-help"></a>Ottenere aiuto
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

