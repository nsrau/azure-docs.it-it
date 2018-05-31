---
title: Automatizzare i report personalizzati con i dati di Azure Application Insights
description: Automatizzare i report personalizzati giornalieri/settimanali/mensili con i dati di Azure Application Insights
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072658"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizzare i report personalizzati con i dati di Azure Application Insights

I report periodici aiutano a mantenere aggiornati i team relativamente al funzionamento dei servizi aziendali critici. La produttività di sviluppatori, team DevOps/SRE e dei propri responsabili può essere aumentata con report automatici che offrono una visione d'insieme affidabile senza richiedere a tutti gli utenti l'accesso al portale. Questi report possono facilitare l'identificazione di aumenti graduali delle latenze, dei carichi e della frequenza degli errori che possono non attivare le regole di avviso.

Ogni azienda ha esigenze di report univoche, ad esempio: 

* Specifiche aggregazioni di percentili delle metriche o metriche personalizzate in un report.
* Disporre di report diversi per riepiloghi giornalieri, settimanali e mensili dei dati per diverse tipologie di pubblico.
* Segmentazione in base ad attributi personalizzati, ad esempio area o ambiente. 
* Raggruppare alcune risorse Al in un singolo report, anche se si trovano in diverse sottoscrizioni o gruppi di risorse ecc.
* Distinguere report contenenti metriche riservate inviati a un pubblico selezionato.
* Report destinati a stakeholder che non hanno accesso alle risorse del portale.

> [!NOTE] 
> L'invio di un digest settimanale con Application Insight non consentiva alcuna personalizzazione; verrà sospeso a favore delle opzioni personalizzate elencate di seguito. L'ultimo digest settimanale verrà inviato l'11 giugno 2018. Configurare una delle opzioni seguenti per ottenere report personalizzati simili (usare la query consigliata).

## <a name="to-automate-custom-report-emails"></a>Per automatizzare l'invio di report personalizzati

È possibile [eseguire una query a livello di codice sui dati di Application Insights](https://dev.applicationinsights.io/) per generare report personalizzati in base alla pianificazione scelta. Le opzioni seguenti consentono di essere subito operativi:

* [Automatizzare i report con Microsoft Flow](app-insights-automate-with-flow.md)
* [Automatizzare i report con App per la logica](automate-with-logic-apps.md)
* Usare il modello di [funzione di Azure](https://azure.microsoft.com/services/functions/) del digest pianificato di Application Insights nello scenario di monitoraggio. Questa funzione usa SendGrid per recapitare il messaggio di posta elettronica. 

    ![Modello di funzione di Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Query di esempio per un digest di posta elettronica settimanale
La query seguente illustra l'unione di più set di dati in un report inviato come digest di posta elettronica settimanale. Può essere personalizzata in base alle esigenze e usata con le opzioni elencate in precedenza per automatizzare un report settimanale.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di [query di Analisi](app-insights-analytics-using.md).
- Altre informazioni sull'[esecuzione di query a livello di codice sui dati di Application Insights](https://dev.applicationinsights.io/)
- Altre informazioni su [App per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
- Altre informazioni su [Microsoft Flow](https://ms.flow.microsoft.com).


