---
title: "Monitoraggio dell'integrità e dell'utilizzo di un'app con Application Insights"
description: "Introduzione a Application Insights. Analizzare l'uso, la disponibilità e le prestazioni delle applicazioni locali o Microsoft Azure."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 9efe10fa35c6a7c84e0d448bbe53127d16d20870
ms.contentlocale: it-it
ms.lasthandoff: 09/21/2017

---
# <a name="monitor-performance-in-web-applications"></a>Monitorare le prestazioni di applicazioni Web


Questo prodotto consente di accertarsi che le prestazioni della propria applicazione siano ottimali e di scoprire rapidamente eventuali errori. [Application Insights][start] rileverà eventuali eccezioni e problemi relativi alle prestazioni e aiuterà a individuare e diagnosticare le cause principali.

Application Insights può monitorare sia le applicazioni web Java e ASP.NET che i servizi, i servizi WCF. Possono essere ospitati in locale, su macchine virtuali o come siti Web di Microsoft Azure. 

Sul lato client Application Insights può acquisire dati di telemetria da pagine Web e da un'ampia gamma di dispositivi, tra cui le app per iOS e Android e di Windows Store.

>[!Note]
> È ora disponibile una nuova funzionalità per la ricerca delle pagine a basse prestazioni nell'applicazione Web. Se non si ha accesso alla funzionalità, abilitarla configurando le opzioni di anteprima usando il [pannello Anteprima](app-insights-previews.md). Leggere le informazioni su questa nuova funzionalità in [Individuare e risolvere i colli di bottiglia delle prestazioni con l'analisi interattiva delle prestazioni](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation).

## <a name="setup"></a>Configurare il monitoraggio delle prestazioni
Se Application Insights non è stato ancora aggiunto al progetto (vale a dire, se ApplicationInsights.config non è presente), scegliere uno dei modi seguenti per iniziare:

* [App Web ASP.NET](app-insights-asp-net.md)
  * [Aggiungere il monitoraggio delle eccezioni](app-insights-asp-net-exceptions.md)
  * [Aggiungere il monitoraggio delle dipendenze](app-insights-monitor-performance-live-website-now.md)
* [App web J2EE](app-insights-java-get-started.md)
  * [Aggiungere il monitoraggio delle dipendenze](app-insights-java-agent.md)

## <a name="view"></a>Esplorare le metriche delle prestazioni
Nel [portale di Azure](https://portal.azure.com), passare alla risorsa di Application Insights impostata per la propria applicazione. Il pannello della panoramica mostra i dati delle prestazioni di base:

Fare clic su un riquadro qualsiasi per visualizzare altri dettagli e per vedere i risultati relativi a un periodo più lungo. Ad esempio, fare clic sul riquadro delle richieste e quindi selezionare un intervallo di tempo:

![Fare clic per visualizzare più dati e selezionare un intervallo di tempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Fare clic su un grafico per scegliere quali metriche visualizzare oppure aggiungere un nuovo grafico e selezionarne le metriche:

![Fare clic su un grafico per scegliere le metriche](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Deselezionare tutte le metriche** per visualizzare la selezione completa disponibile. Le metriche sono suddivise in gruppi; quando si seleziona qualsiasi membro di un gruppo, vengono visualizzati solo gli altri membri di quel gruppo.

## <a name="metrics"></a>Interpretazione dei dati riquadri e report sulle prestazioni
È possibile ottenere diverse metriche delle prestazioni. Vengono analizzate innanzitutto quelle visualizzate per impostazione predefinita nel pannello dell'applicazione.

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

La selezione di una metrica disabilita le altre metriche che non possono essere visualizzate nello stesso grafico.

## <a name="set-alerts"></a>Impostazione di avvisi
Per ricevere tramite posta elettronica una notifica relativa a valori insoliti di una metrica, aggiungere un avviso. È possibile scegliere di inviare il messaggio di posta elettronica agli amministratori di account o a indirizzi di posta elettronica specifici.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Impostare la risorsa prima delle altre proprietà. Non scegliere le risorse webtest se si desidera impostare avvisi sulle metriche relative a prestazioni o utilizzo.

Prendere nota delle unità in cui viene chiesto di immettere il valore soglia.

*Il pulsante Aggiungi avviso non è visibile.* Si tratta di un account di gruppo al quale è possibile accedere in sola lettura? Rivolgersi all'amministratore dell'account.

## <a name="diagnosis"></a>Diagnosi dei problemi
Di seguito vengono riportati alcuni suggerimenti su come trovare e diagnosticare i problemi di prestazioni:

* Configurare i [test Web][availability] in modo da ricevere un avviso se il sito Web non risponde o risponde in maniera non corretta o lentamente. 
* Confrontare il conteggio delle richieste con altre metriche per verificare se gli errori o la risposta lenta sono collegati al carico.
* [Inserire e cercare istruzioni di traccia][diagnostic] nel codice per individuare i problemi.
* Monitorare l'applicazione Web in esecuzione con [Live Metrics Stream][livestream].
* Acquisire lo stato dell'applicazione .Net con il [debugger di snapshot][snapshot].

>[!Note]
> Microsoft sta trasferendo l'analisi delle prestazioni di Application Insights a un'esperienza a schermo intero e interattiva. La documentazione seguente descrive prima di tutto la nuova esperienza e quindi esamina quella precedente, per i casi in cui è necessario continuare ad accedervi, mentre è ancora disponibile durante la transizione.

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>Trovare e correggere i colli di bottiglia con un'esperienza di analisi delle prestazioni a schermo intero e interattiva

È possibile usare la nuova analisi interattiva delle prestazioni di Application Insights per esaminare le operazioni con prestazioni insoddisfacenti nell'app Web. È possibile selezionare rapidamente un'operazione lenta specifica e usare [Profiler](app-insights-profiler.md) per individuare la causa principale della lentezza fino al codice. Usando la nuova distribuzione della durata visualizzata per l'operazione selezionata, è possibile valutare rapidamente e immediatamente il livello di inadeguatezza dell'esperienza per i clienti. In effetti, per ogni operazione lenta è possibile visualizzare il numero di interazioni utente interessate. Nell'esempio seguente si vuole osservare più da vicino l'operazione GET su clienti e dettagli. Nella distribuzione della durata è possibile osservare tre picchi. Il picco più a sinistra è di circa 400 millisecondi e rappresenta un'esperienza con velocità di risposta ottimale. Il picco al centro è di circa 1,2 secondi e rappresenta un'esperienza mediocre. Infine, è presente un altro piccolo picco a 3,6 secondi, che rappresenta un'esperienza al 99° percentile e che può provocare l'abbandono dei clienti per insoddisfazione. Questa esperienza è dieci volte più lenta dell'esperienza ottimale per la stessa operazione. 

![Tre picchi di durata per l'operazione GET su clienti e dettagli](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Per ottenere un'idea più accurata delle esperienze utente per questa operazione, è possibile selezionare un intervallo di tempo maggiore. È anche possibile ridurre il tempo in base a un intervallo specifico in cui l'operazione si è rivelata particolarmente lenta. Nell'esempio seguente l'intervallo di tempo è stato modificato da quello predefinito di 24 ore a uno di 7 giorni, concentrandosi sull'intervallo tra le 9.47 e le 12.47 tra martedì 12 e mercoledì 13. Si noti che a destra sono stati aggiornati sia la distribuzione della durata sia il numero di campioni e di tracce del profiler.

![Tre picchi di durata dell'operazione GET su clienti e dettagli in un intervallo di 7 giorni](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Per limitare l'ambito alle esperienze con prestazioni lente, si concentra la visualizzazione sulle durate comprese tra il 95° e il 99° percentile. Queste rappresentano il 4% delle interazioni utente particolarmente lente.

![Tre picchi di durata dell'operazione GET su clienti e dettagli in un intervallo di 7 giorni](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

È ora possibile esaminare i campioni rappresentativi, facendo clic sul pulsante Samples (Campioni), o le tracce del profiler rappresentative, facendo clic sul pulsante Profiler traces (Tracce profiler). Questo esempio contiene quattro tracce che sono state raccolte per l'operazione GET su clienti e dettagli nell'intervallo di tempo e nella durata di interesse.

A volte il problema non riguarda il codice, ma una dipendenza chiamata dal codice. È possibile passare alla scheda delle dipendenze nella visualizzazione di valutazione delle prestazioni per analizzare le dipendenze lente. Si noti che per impostazione predefinita la visualizzazione delle prestazioni è costituita dalla media delle tendenze, ma il dato più interessante è il 95° percentile (o il 99°, se si sta monitorando un servizio più vecchio). Nell'esempio seguente viene esaminata la dipendenza lenta relativa al BLOB di Azure, in cui viene eseguita un'operazione PUT su fabrikamaccount. Le esperienze positive si aggirano intorno ai 40 millisecondi, mentre le chiamate meno veloci alla stessa dipendenza sono tre volte più lente, raggruppandosi intorno ai 120 millisecondi. Non sono necessarie molte di queste chiamate per provocare un notevole rallentamento della rispettiva operazione. È possibile esaminare più dettagliatamente le tracce del profiler e i campioni rappresentativi, allo stesso modo che nella scheda delle operazioni.

![Tre picchi di durata dell'operazione GET su clienti e dettagli in un intervallo di 7 giorni](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

Un'altra funzionalità molto potente che rappresenta una novità dell'esperienza di analisi delle prestazioni a schermo intero e interattiva è l'integrazione con le informazioni dettagliate. Application Insights è in grado di rilevare e restituire come informazioni dettagliate le regressioni della velocità di risposta e può aiutare a identificare le proprietà comuni nel set di campioni su cui si sceglie di concentrarsi. Il modo migliore per esaminare tutte le informazioni dettagliate disponibili consiste nel passare a un intervallo di tempo di 30 giorni e quindi selezionare Overall (Complessive) per visualizzare le informazioni dettagliate su tutte le operazioni per l'ultimo mese.

![Tre picchi di durata dell'operazione GET su clienti e dettagli in un intervallo di 7 giorni](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

Con la nuova visualizzazione di valutazione delle prestazioni, Application Insights è letteralmente in grado di trovare l'ago nel pagliaio che causa le esperienze insoddisfacenti per gli utenti dell'app Web.

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>Funzionalità deprecata: individuazione e correzione dei colli di bottiglia delle prestazioni con un'analisi delle prestazioni legacy a riquadri di dimensioni limitate

È possibile usare l'esperienza di analisi delle prestazioni legacy a riquadri di Application Insights per individuare le aree dell'app Web che rallentano le prestazioni complessive. È possibile trovare le pagine specifiche che rallentano le prestazioni e usare [Profiler](app-insights-profiler.md) per tracciare la causa principale dei problemi, fino al codice. 

### <a name="create-a-list-of-slow-performing-pages"></a>Creare un elenco delle pagine lente 

La prima operazione da eseguire per individuare i problemi di prestazioni consiste nell'ottenere un elenco delle pagine che rispondono lentamente. Lo screenshot seguente illustra l'uso del pannello Prestazioni per ottenere un elenco delle possibili pagine che meritano un'ulteriore analisi. Nella pagina è immediatamente evidente che si è verificato un rallentamento nel tempo di risposta dell'app alle 18:00 e di nuovo alle 22:00. È anche possibile osservare che l'operazione GET customer/details (Ottieni clienti/dettagli) ha incluso operazioni di lunga durata con un tempo di risposta medio di 507,05 millisecondi. 

![Analisi interattiva delle prestazioni di Application Insights](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>Eseguire il drill-down in pagine specifiche

Dopo aver ottenuto uno snapshot delle prestazioni dell'app, è possibile visualizzare altri dettagli sulle singole operazioni lente. Fare clic su un'operazione nell'elenco per visualizzare i dettagli come illustrato di seguito. Dal grafico è possibile determinare se le prestazioni erano basate su una dipendenza. È anche possibile visualizzare il numero di utenti per i quali si sono verificati i diversi tempi di risposta. 

![Pannello delle operazioni di Application Insights](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>Eseguire il drill-in un periodo di tempo specifico

Dopo aver identificato un periodo di tempo da analizzare, eseguire il drill-down per visualizzare le operazioni specifiche che potrebbero aver causato il rallentamento delle prestazioni. Quando si fa clic in un periodo di tempo specifico vengono visualizzati i dettagli della pagina come illustrato di seguito. Nell'esempio seguente sono elencate le operazioni per ogni periodo di tempo con i codici di risposta del server e la durata dell'operazione. È disponibile anche l'URL per aprire un elemento di lavoro TFS per inviare queste informazioni al team di sviluppo.

![Intervallo di tempo di Application Insights](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>Eseguire il drill-in un'operazione specifica

Dopo aver identificato un periodo di tempo da analizzare, eseguire il drill-down per visualizzare le operazioni specifiche che potrebbero aver causato il rallentamento delle prestazioni. Fare clic su un'operazione nell'elenco per visualizzare i dettagli dell'operazione, come illustrato di seguito. In questo esempio l'operazione non è stata eseguita e Application Insights ha visualizzato i dettagli dell'eccezione generata dall'applicazione. È di nuovo possibile creare un elemento di lavoro TFS da questo pannello.

![Pannello dell'operazione di Application Insights](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>Passaggi successivi
[Test Web][availability]: possibilità di inviare richieste Web all'applicazione a intervalli regolari da tutto il mondo.

[Acquisire e cercare tracce diagnostiche][diagnostic]: possibilità di inserire chiamate di traccia ed esaminare i risultati per individuare i problemi.

[Monitorare l'utilizzo][usage]: possibilità di scoprire come le persone usano l'applicazione.

[Domande e risposte e risoluzione dei problemi][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md




