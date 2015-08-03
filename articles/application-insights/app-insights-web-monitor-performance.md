<properties 
	pageTitle="Monitoraggio dell'integrità e dell'utilizzo di un'app con Application Insights" 
	description="Introduzione a Application Insights. Analizzare l'uso, la disponibilità e le prestazioni delle applicazioni locali o Microsoft Azure." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="awills"/>
 
# Monitorare le prestazioni di applicazioni Web

*Application Insights è disponibile in anteprima.*


Questo prodotto consente di accertarsi che le prestazioni della propria applicazione siano ottimali e di scoprire rapidamente eventuali errori. [Application Insights][start] rileverà qualsiasi problema ed eccezione relativi alle prestazioni e aiuterà a individuare e diagnosticare le cause principali.

Application Insights è in grado di monitorare i servizi WCF e le applicazioni Web ASP.NET ospitate localmente o su macchine virtuali, oltre ai siti Web di Microsoft Azure.


## <a name="setup"></a>Configurare il monitoraggio delle prestazioni

Se Application Insights non è stato ancora aggiunto al progetto (vale a dire, se ApplicationInsights.config non è presente), scegliere uno dei modi seguenti per iniziare:

* [Aggiungere Application Insights al progetto in Visual Studio][greenbrown]: metodo consigliato. Oltre al monitoraggio passivo delle prestazioni, è possibile inserire registrazioni diagnostiche e tenere traccia dell'uso.
* [Monitorare ora le prestazioni di un sito Web live][redfield]: in questo modo non è necessario aggiornare il progetto dell'applicazione o ridistribuire il sito Web.
* [Per un sito Web di Microsoft Azure](../insights-how-to-customize-monitoring.md) le metriche sono già visibili nel filtro di monitoraggio di tale sito. 

Per visualizzare rapidamente i dati nel pannello della panoramica in Application Insights, usare uno dei metodi seguenti.


## <a name="view"></a>Esplorazione delle metriche

Fare clic su un riquadro qualsiasi per visualizzare altri dettagli e i risultati relativi a un periodo più lungo. Ad esempio, fare clic sul riquadro delle richieste e quindi selezionare un intervallo di tempo:


![Fare clic per visualizzare più dati e selezionare un intervallo di tempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Fare clic su un grafico per scegliere quali metriche visualizzare oppure aggiungere un nuovo grafico e selezionarne le metriche:

![Fare clic su un grafico per scegliere le metriche](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [AZURE.NOTE]**Deselezionare tutte le metriche** per visualizzare la selezione completa disponibile. Le metriche sono suddivise in gruppi; quando si seleziona qualsiasi membro di un gruppo, vengono visualizzati solo gli altri membri di quel gruppo.


## <a name="metrics"></a>Interpretazione dei dati Riquadri e report sulle prestazioni

È disponibile una vasta gamma di metriche sulle prestazioni. Vengono analizzate innanzitutto quelle visualizzate per impostazione predefinita nel pannello dell'applicazione.


### Richieste

Il numero di richieste HTTP ricevute in un periodo specificato. Confrontare questo numero con i risultati di altri report per analizzare il comportamento dell'app al variare del carico.

Le richieste HTTP includono tutte le richieste GET o POST di pagine, dati e immagini.

Fare clic sul riquadro per visualizzare i conteggi per URL specifici.

### Tempo medio di risposta

Misura il tempo intercorso tra la ricezione di una richiesta Web da parte dell'applicazione e la risposta restituita.

I punti mostrano una media mobile. Se le richieste sono numerose, alcune di queste potrebbero deviare dalla media senza mostrare un picco o un calo evidente nel grafico.

Cercare picchi inconsueti. In genere, il tempo di risposta aumenta con l'aumento delle richieste. Se l'aumento è sproporzionato, l'app potrebbe aver raggiunto un limite di risorsa, ad esempio dovuto alla CPU o alla capacità di un servizio che utilizza.

Fare clic sul riquadro per visualizzare i tempi per URL specifici.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)


### Richieste lente

![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Mostra quali richieste potrebbero necessitare di un'ottimizzazione delle prestazioni.


### Richieste non riuscite

![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

La quantità di richieste che hanno restituito eccezioni non rilevate.

Fare clic sul riquadro per visualizzare i dettagli di errori specifici e selezionare una singola richiesta per visualizzarne i dettagli.

Viene conservato solo un campione di errori rappresentativi per l'analisi individuale.

### Altre metriche

Per sapere quali altri metriche è possibile visualizzare, fare clic su un grafico e deselezionare tutte le metriche per vedere l'intero set disponibile. Fare clic su (i) per visualizzare la definizione di ciascuna metrica.

![Deselezionare tutte le metriche per visualizzare l'intero set](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)


La selezione di una metrica disabiliterà le altre metriche che non possono essere visualizzate nello stesso grafico.

## Raccogliere altri contatori delle prestazioni

Alcune delle metriche selezionabili derivano dai [contatori delle prestazioni](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters). Oltre ai numerosi contatori delle prestazioni inclusi in Windows, è possibile definire contatori personalizzati.

Se l'elenco non include i contatori desiderati, è possibile aggiungerli al set di quelli raccolti dall'SDK. Aprire il file ApplicationInsights.config e modificare la direttiva dell'agente di raccolta delle prestazioni:

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(electronics)# Items Sold" ReportAs="Item sales"/>
      </Counters>
    </Add>

Il formato è `\Category(instance)\Counter"` oppure, nel caso delle categorie per cui non esistono istanze, solo `\Category\Counter`.

`ReportAs` è obbligatorio per i nomi di contatori che contengono caratteri diversi da lettere, parentesi tonde, barre, trattini, caratteri di sottolineatura, spazi e punti.

Se si specifica un'istanza, questa verrà raccolta come proprietà "CounterInstanceName" della metrica indicata.

Se si preferisce, è possibile scrivere codice per ottenere lo stesso effetto:

    var perfCollector = new PerformanceCollectorModule();
    perfCollector.Counters = new List<CustomPerformanceCounterCollectionRquest>();
    perfCollector.Counters.Add(new CustomPerformanceCounterCollectionRquest(
      @"\Sales(electronics)# Items Sold", "Items sold"));
    perfCollector.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryModules.Add(perfCollector);



## Impostazione di avvisi

Per ricevere tramite posta elettronica una notifica dei valori insoliti di una metrica, aggiungere un avviso. È possibile scegliere di inviare il messaggio di posta elettronica agli amministratori di account o a indirizzi di posta elettronica specifici.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Impostare la risorsa prima delle altre proprietà. Non scegliere le risorse webtest se si desidera impostare avvisi sulle metriche relative a prestazioni o utilizzo.

Prendere nota delle unità in cui viene chiesto di immettere il valore soglia.

*Il pulsante Aggiungi avviso non è visibile.* Si tratta di un account di gruppo al quale è possibile accedere in sola lettura? Rivolgersi all'amministratore dell'account.

## <a name="diagnosis"></a>Diagnosi dei problemi

Di seguito vengono riportati alcuni suggerimenti su come trovare e diagnosticare i problemi di prestazioni:

* Configurare i [test Web][availability] in modo da ricevere un avviso se il sito Web non risponde o risponde in maniera non corretta o lentamente. 
* Confrontare il conteggio delle richieste con altre metriche per verificare se gli errori o la risposta lenta sono collegati al carico.
* [Inserire e cercare istruzioni di traccia][diagnostic] nel codice per individuare i problemi.

## <a name="next"></a>Passaggi successivi

[Test Web][availability]: possibilità di inviare richieste Web all'applicazione a intervalli regolari da tutto il mondo.

[Acquisire e cercare tracce diagnostiche][diagnostic]: possibilità di inserire chiamate di traccia ed esaminare i risultati per individuare i problemi.

[Monitoraggio dell'utilizzo][usage]: possibilità di scoprire come le persone usano l'applicazione.

[Risoluzione dei problemi][qna] e domande e risposte

## Video

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO4-->