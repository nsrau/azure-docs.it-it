---
title: "Monitoraggio dell&quot;integrità e dell&quot;utilizzo di un&quot;app con Application Insights"
description: "Introduzione a Application Insights. Analizzare l&quot;uso, la disponibilità e le prestazioni delle applicazioni locali o Microsoft Azure."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 7dd1289453fe146b446322570fcb1c0df74b4db2


---
# <a name="monitor-performance-in-web-applications"></a>Monitorare le prestazioni di applicazioni Web


Questo prodotto consente di accertarsi che le prestazioni della propria applicazione siano ottimali e di scoprire rapidamente eventuali errori. [Application Insights][start] rileverà qualsiasi problema ed eccezione relativi alle prestazioni e aiuterà a individuare e diagnosticare le cause principali.

Application Insights può monitorare sia le applicazioni web Java e ASP.NET che i servizi, i servizi WCF. Possono essere ospitati in locale, su macchine virtuali o come siti Web di Microsoft Azure. 

Sul lato client, Application Insights può richiedere dati di telemetria di pagine web e un'ampia gamma di dispositivi, tra l’app Store iOS, Android e Windows.

## <a name="a-namesetupaset-up-performance-monitoring"></a><a name="setup"></a>Configurare il monitoraggio delle prestazioni
Se Application Insights non è stato ancora aggiunto al progetto (vale a dire, se ApplicationInsights.config non è presente), scegliere uno dei modi seguenti per iniziare:

* [App Web ASP.NET](app-insights-asp-net.md)
  * [Aggiungere il monitoraggio delle eccezioni](app-insights-asp-net-exceptions.md)
  * [Aggiungere il monitoraggio delle dipendenze](app-insights-monitor-performance-live-website-now.md)
* [App web J2EE](app-insights-java-get-started.md)
  * [Aggiungere il monitoraggio delle dipendenze](app-insights-java-agent.md)

## <a name="a-nameviewaexploring-performance-metrics"></a><a name="view"></a>Esplorare le metriche delle prestazioni
Nel [portale di Azure](https://portal.azure.com), passare alla risorsa di Application Insights impostata per la propria applicazione. Il pannello della panoramica mostra i dati delle prestazioni di base:

Fare clic su un riquadro qualsiasi per visualizzare altri dettagli e per vedere i risultati relativi a un periodo più lungo. Ad esempio, fare clic sul riquadro delle richieste e quindi selezionare un intervallo di tempo:

![Fare clic per visualizzare più dati e selezionare un intervallo di tempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Fare clic su un grafico per scegliere quali metriche visualizzare oppure aggiungere un nuovo grafico e selezionarne le metriche:

![Fare clic su un grafico per scegliere le metriche](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Deselezionare tutte le metriche** per visualizzare la selezione completa disponibile. Le metriche sono suddivise in gruppi; quando si seleziona qualsiasi membro di un gruppo, vengono visualizzati solo gli altri membri di quel gruppo.
> 
> 

## <a name="a-namemetricsawhat-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Interpretazione dei dati riquadri e report sulle prestazioni
È disponibile una vasta gamma di metriche sulle prestazioni. Vengono analizzate innanzitutto quelle visualizzate per impostazione predefinita nel pannello dell'applicazione.

### <a name="requests"></a>Richieste
Il numero di richieste HTTP ricevute in un periodo specificato. Confrontare questo numero con i risultati di altri report per analizzare il comportamento dell'app al variare del carico.

Le richieste HTTP includono tutte le richieste GET o POST di pagine, dati e immagini.

Fare clic sul riquadro per visualizzare i conteggi per URL specifici.

### <a name="average-response-time"></a>Tempo medio di risposta
Misura il tempo intercorso tra la ricezione di una richiesta Web da parte dell'applicazione e la risposta restituita.

I punti mostrano una media mobile. Se le richieste sono numerose, alcune di queste potrebbero deviare dalla media senza mostrare un picco o un calo evidente nel grafico.

Cercare picchi inconsueti. In genere, il tempo di risposta aumenta con l'aumento delle richieste. Se l'aumento è sproporzionato, l'app potrebbe aver raggiunto un limite di risorsa, ad esempio dovuto alla CPU o alla capacità di un servizio che utilizza.

Fare clic sul riquadro per visualizzare i tempi per URL specifici.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Richieste lente
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Mostra quali richieste potrebbero necessitare di un'ottimizzazione delle prestazioni.

### <a name="failed-requests"></a>Richieste non riuscite
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

La quantità di richieste che hanno restituito eccezioni non rilevate.

Fare clic sul riquadro per visualizzare i dettagli di errori specifici e selezionare una singola richiesta per visualizzarne i dettagli. 

Viene conservato solo un campione di errori rappresentativi per l'analisi individuale.

### <a name="other-metrics"></a>Altre metriche
Per sapere quali altri metriche è possibile visualizzare, fare clic su un grafico e deselezionare tutte le metriche per vedere l'intero set disponibile. Fare clic su (i) per visualizzare la definizione di ciascuna metrica.

![Deselezionare tutte le metriche per visualizzare l'intero set](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

La selezione di una metrica disabiliterà le altre metriche che non possono essere visualizzate nello stesso grafico.

## <a name="set-alerts"></a>Impostazione di avvisi
Per ricevere tramite posta elettronica una notifica relativa a valori insoliti di una metrica, aggiungere un avviso. È possibile scegliere di inviare il messaggio di posta elettronica agli amministratori di account o a indirizzi di posta elettronica specifici.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Impostare la risorsa prima delle altre proprietà. Non scegliere le risorse webtest se si desidera impostare avvisi sulle metriche relative a prestazioni o utilizzo.

Prendere nota delle unità in cui viene chiesto di immettere il valore soglia.

*Il pulsante Aggiungi avviso non è visibile.*  Si tratta di un account di gruppo al quale è possibile accedere in sola lettura? Rivolgersi all'amministratore dell'account.

## <a name="a-namediagnosisadiagnosing-issues"></a><a name="diagnosis"></a>Diagnosi dei problemi
Di seguito vengono riportati alcuni suggerimenti su come trovare e diagnosticare i problemi di prestazioni:

* Configurare i [test Web][availability] in modo da ricevere un avviso se il sito Web non risponde o risponde in maniera non corretta o lentamente. 
* Confrontare il conteggio delle richieste con altre metriche per verificare se gli errori o la risposta lenta sono collegati al carico.
* [Inserire e cercare istruzioni di traccia][diagnostic] nel codice per individuare i problemi.

## <a name="a-namenextanext-steps"></a><a name="next"></a>Passaggi successivi
[Test Web][availability]: possibilità di inviare richieste Web all'applicazione a intervalli regolari da tutto il mondo.

[Acquisire e cercare tracce diagnostiche][diagnostic]: possibilità di inserire chiamate di traccia ed esaminare i risultati per individuare i problemi.

[Monitorare l'utilizzo][usage]: possibilità di scoprire come le persone usano l'applicazione.

[Domande e risposte e risoluzione dei problemi][qna]

## <a name="video"></a>Video
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/222/player]


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md





<!--HONumber=Nov16_HO3-->


