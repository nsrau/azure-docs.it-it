---
title: Risolvere i problemi degli strumenti di analisi utente-applicazione Azure Insights
description: Guida alla risoluzione dei problemi relativi all'analisi dell'utilizzo di siti e app con Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8d2e573f34895207a455838b5fc64f95560943d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670917"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Risolvere i problemi relativi agli strumenti di analisi del comportamento degli utenti in Application Insights
Questo articolo prende in esame le domande degli utenti sugli [strumenti di analisi del comportamento degli utenti in Application Insights](usage-overview.md), ad esempio [Utenti, Sessioni, Eventi](usage-segmentation.md), [Imbuti](usage-funnels.md), [Flussi utente](usage-flows.md), [Conservazione](usage-retention.md) e Coorti, offrendo alcune utili risposte.

## <a name="counting-users"></a>Conteggio degli utenti
**Gli strumenti di analisi del comportamento dell'utente mostrano che l'app dispone di un utente/sessione, ma è noto che l'app ha molti utenti/sessioni. Come è possibile risolvere questi conteggi non corretti?**

Tutti gli eventi di telemetria in Application Insights hanno un [ID utente anonimo](../../azure-monitor/app/data-model-context.md) e un [ID di sessione](../../azure-monitor/app/data-model-context.md) come proprietà standard. Per impostazione predefinita, tutti gli strumenti di analisi dell'utilizzo eseguono il conteggio degli utenti e delle sessioni in base a questi ID. Se in queste proprietà standard non vengono inseriti ID univoci per ogni utente e sessione dell'app, negli strumenti di analisi dell'utilizzo si vedrà un conteggio errato degli utenti e delle sessioni.

Se si sta monitorando un'app Web, la soluzione più semplice consiste nell'aggiungere [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md) all'app e verificare che il frammento di script sia caricato in ogni pagina da monitorare. JavaScript SDK genera automaticamente ID utente anonimi e ID di sessione e quindi li inserisce negli eventi di telemetria quando vengono inviati dall'app.

Se si sta monitorando un servizio Web (senza interfaccia utente), [creare un inizializzatore di telemetria per impostare le proprietà ID utente anonimo e ID di sessione](usage-send-user-context.md) in base alle nozioni di sessioni e utenti univoci del servizio.

Se l'app invia [ID utente autenticati](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), è possibile eseguire il conteggio in base agli ID utente autenticati nello strumento Utenti. Nell'elenco a discesa "Mostra" scegliere "Utenti autenticati".

Gli strumenti di analisi del comportamento degli utenti non supportano attualmente il conteggio di utenti o sessioni in base a proprietà diverse da ID utente anonimo, ID utente autenticato o ID di sessione.

## <a name="naming-events"></a>Denominazione degli eventi
**L'app dispone di migliaia di visualizzazioni pagina e di nomi di eventi personalizzati diversi. È difficile distinguere tra di essi e gli strumenti di analisi del comportamento degli utenti spesso non rispondono. Come è possibile risolvere questi problemi di denominazione?**

I nomi di visualizzazione pagina e quelli di evento personalizzato vengono usati in tutti gli strumenti di analisi del comportamento degli utenti. La corretta denominazione degli eventi è essenziale per poter ottenere risultati ottimali da questi strumenti. L'obiettivo è un equilibrio tra un numero troppo basso di nomi generici ("pulsante selezionato") e con troppi nomi eccessivamente specifici ("pulsante Modifica selezionato su http:\//www.contoso.com/index").

Per apportare modifiche ai nomi di visualizzazione pagina e a quelli di evento personalizzato inviati dall'app, è necessario modificare il codice sorgente dell'app ed eseguire nuovamente la distribuzione. **Tutti i dati di telemetria disponibili in Application Insights vengono archiviati per 90 giorni e non possono essere eliminati**. Di conseguenza, le eventuali modifiche apportate ai nomi di evento saranno completamente visibili dopo 90 giorni. Per i 90 giorni successivi alle operazioni di modifica, nei dati di telemetria verranno visualizzati sia i nomi di evento precedenti sia quelli nuovi. Sarà pertanto opportuno modificare le query e comunicare all'interno dei team tenendo presente questo aspetto.

Se l'app invia troppi nomi di visualizzazione pagina, verificare se questi nomi vengono specificati manualmente nel codice o se vengono inviati automaticamente da Application Insights JavaScript SDK:

* Se i nomi delle visualizzazioni di pagina vengono specificati manualmente nel codice usando l' [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), modificare il nome in modo che sia meno specifico. Evitare gli errori più comuni come quello di inserire l'URL nel nome della visualizzazione pagina. Al contrario, usare il parametro URL dell'API `trackPageView`. Spostare altri dettagli dal nome di visualizzazione pagina nelle proprietà personalizzate.

* Se Application Insights JavaScript SDK invia i nomi di visualizzazione pagina in modo automatico, è possibile modificare i titoli delle pagine oppure passare all'invio manuale di questi nomi. Per impostazione predefinita, l'SDK invia il [titolo](https://developer.mozilla.org/docs/Web/HTML/Element/title) di ogni pagina come nome di visualizzazione pagina. È possibile modificare i titoli in modo da renderli più generici, ma è necessario tenere presente gli effetti che questa modifica può avere sull'ottimizzazione SEO e su altri aspetti. I nomi di visualizzazione pagina specificati manualmente con l'API `trackPageView` sostituiscono i nomi raccolti automaticamente, pertanto è possibile inviare nomi più generici nei dati di telemetria senza modificare i titoli di pagina.   

Se l'app invia troppi nomi di evento personalizzato, modificare il nome nel codice in modo da renderlo meno specifico. Evitare nuovamente di inserire gli URL e altre informazioni dinamiche o di pagina direttamente nei nomi di evento personalizzato. Al contrario, spostare questi dettagli nelle proprietà personalizzate dell'evento personalizzato tramite l'API `trackEvent`. Ad esempio, anziché `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, è consigliabile usare `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sugli strumenti di analisi del comportamento degli utenti](usage-overview.md)

## <a name="get-help"></a>Ottenere aiuto
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

