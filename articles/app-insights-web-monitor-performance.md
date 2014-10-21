<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Monitoraggio delle prestazioni delle applicazioni Web

*Application Insights è disponibile in anteprima.*

Questo prodotto consente di accertarsi che le prestazioni della propria applicazione siano ottimali e di scoprire rapidamente eventuali errori. [Application Insights][] rileverà qualsiasi problema ed eccezione relativi alle prestazioni e aiuterà a individuare e diagnosticare le cause principali.

Application Insights è in grado di monitorare le applicazioni Web ASP.NET ospitate localmente o su macchine virtuali, oltre ai siti Web Microsoft Azure.

-   [Configurazione del monitoraggio delle prestazioni][]
-   [Visualizzazione dei dati][]
-   [Interpretazione dei dati][]
-   [Diagnostica dei problemi][]
-   [Passaggi successivi][]

## <a name="setup"></a>Configurazione del monitoraggio delle prestazioni

Se Application Insights non è stato ancora aggiunto al progetto (vale a dire, se ApplicationInsights.config non è presente), scegliere uno dei modi seguenti per iniziare:

-   [Aggiungere Application Insights al progetto dell'app in Visual Studio][Application Insights]: metodo consigliato. Oltre al monitoraggio passivo delle prestazioni, è possibile inserire registrazioni diagnostiche e tenere traccia dell'utilizzo.
-   [Monitorare ora le prestazioni di un sito Web live][]: in questo modo non è necessario aggiornare il progetto dell'applicazione o ridistribuire il sito Web.

## <a name="view"></a>Visualizzazione dei report

Eseguire l'applicazione con F5 e provarla aprendo diverse pagine.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati ricevuti.

![][]

Aprire Application Insights dal progetto.

![Fare clic con il pulsante destro del mouse sul progetto e aprire il portale di Azure][]

Cercare i dati nei riquadri **Integrità applicazione**. All'inizio si vedranno solo uno o due punti. Ad esempio:

![Fare clic per visualizzare altri dati][]

Quando si esegue la modalità debug, la telemetria viene velocizzata nella pipeline, quindi i dati vengono visualizzati in pochi secondi. Quando si distribuisce l'app, i dati si accumulano più lentamente.

Se all'inizio i dati non vengono visualizzati, attendere un minuto e quindi fare clic su Aggiorna.

### Esplorazione delle metriche

Fare clic su qualsiasi riquadro per visualizzare più dettagli e per vedere i risultati relativi a un periodo più lungo. Ad esempio, fare clic sul riquadro delle richieste e quindi selezionare un intervallo di tempo:

![Fare clic per visualizzare più dati e selezionare un intervallo di tempo][]

Fare clic su un grafico per scegliere quali metriche visualizzare:

![Fare clic su un grafico per scegliere le metriche][]

> [AZURE.NOTE] **Deselezionare tutte le metriche** per visualizzare la selezione completa disponibile. Le metriche sono suddivise in gruppi; quando si seleziona qualsiasi membro di un gruppo, vengono visualizzati solo gli altri membri di quel gruppo.

## <a name="metrics"></a>Interpretazione dei dati: riquadri e report sulle prestazioni

È disponibile una vasta gamma di metriche sulle prestazioni. Iniziamo da quelle che vengono visualizzate per impostazione predefinita nella falda dell'applicazione.

### Richieste

Il numero di richieste HTTP ricevute in un periodo specificato. Confrontare questo numero con i risultati di altri report per analizzare il comportamento dell'app al variare del carico.

Le richieste HTTP includono tutte le richieste GET o POST di pagine, dati e immagini.

Fare clic sul riquadro per visualizzare i conteggi per URL specifici.

### Tempo medio di risposta

Misura il tempo intercorso tra la ricezione di una richiesta Web da parte dell'applicazione e la risposta restituita.

I punti mostrano una media mobile. Se le richieste sono numerose, alcune di queste potrebbero deviare dalla media senza mostrare un picco o un calo evidente nel grafico.

Cercare picchi inconsueti. In genere, il tempo di risposta aumenta con l'aumento delle richieste. Se l'aumento è sproporzionato, l'app potrebbe aver raggiunto un limite di risorsa, ad esempio dovuto alla CPU o alla capacità di un servizio che utilizza.

Fare clic sul riquadro per visualizzare i tempi per URL specifici.

![][1]

### Richieste lente

![][2]

Mostra quali richieste potrebbero necessitare di un'ottimizzazione delle prestazioni.

### Richieste non riuscite

![][3]

La quantità di richieste che hanno restituito eccezioni non rilevate.

Fare clic sul riquadro per visualizzare i dettagli di errori specifici e selezionare una singola richiesta per visualizzarne i dettagli.

Viene conservato solo un campione di errori rappresentativi per l'analisi individuale.

### Altre metriche

Per sapere quali altri metriche è possibile visualizzare, fare clic su un grafico e deselezionare tutte le metriche per vedere l'intero set disponibile. Fare clic su (i) per visualizzare la definizione di ciascuna metrica.

![Deselezionare tutte le metriche per visualizzare l'intero set][]

La selezione di una metrica nasconderà le altre metriche che non possono essere visualizzate nello stesso grafico.

## <a name="diagnosis"></a>Diagnostica dei problemi

Di seguito vengono riportati alcuni suggerimenti su come trovare e diagnosticare i problemi di prestazioni:

-   Configurare i [test Web][] in modo da ricevere un avviso se il sito Web non risponde o risponde in maniera non corretta o lentamente.
-   Confrontare il conteggio delle richieste con altre metriche per verificare se gli errori o la risposta lenta sono collegati al carico.
-   [Inserire e cercare istruzioni di traccia][] nel codice per individuare i problemi.

## <a name="next"></a>Passaggi successivi

[Test Web][test Web]: possibilità di inviare richieste Web all'applicazione a intervalli regolari da tutto il mondo.

[Acquisire e cercare tracce diagnostiche][Inserire e cercare istruzioni di traccia]: possibilità di inserire chiamate di traccia ed esaminare i risultati per individuare i problemi.

[Monitoraggio dell'utilizzo][]: possibilità di scoprire come le persone usano l'applicazione.

[Risoluzione dei problemi][] e domande e risposte

## Ulteriori informazioni

-   [Application Insights - Guida introduttiva][Application Insights]
-   [Monitoraggio di un server Web live][Monitorare ora le prestazioni di un sito Web live]
-   [Monitoraggio delle prestazioni delle applicazioni Web][]
-   [Ricerca nei registri di diagnostica][Inserire e cercare istruzioni di traccia]
-   [Monitoraggio della disponibilità con test Web][test Web]
-   [Monitoraggio dell'utilizzo][]
-   [Domande e risposte e risoluzione dei problemi][Risoluzione dei problemi]

<!--Link references-->

  [Application Insights]: ../app-insights-start-monitoring-app-health-usage/
  [Configurazione del monitoraggio delle prestazioni]: #setup
  [Visualizzazione dei dati]: #view
  [Interpretazione dei dati]: #metrics
  [Diagnostica dei problemi]: #diagnosis
  [Passaggi successivi]: #next
  [Monitorare ora le prestazioni di un sito Web live]: ../app-insights-monitor-performance-live-website-now/
  []: ./media/appinsights/appinsights-09eventcount.png
  [Fare clic con il pulsante destro del mouse sul progetto e aprire il portale di Azure]: ./media/appinsights/appinsights-04-openPortal.png
  [Fare clic per visualizzare altri dati]: ./media/appinsights/appinsights-41firstHealth.png
  [Fare clic per visualizzare più dati e selezionare un intervallo di tempo]: ./media/appinsights/appinsights-48metrics.png
  [Fare clic su un grafico per scegliere le metriche]: ./media/appinsights/appinsights-61perfchoices.png
  [1]: ./media/appinsights/appinsights-42reqs.png
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-46failed.png
  [Deselezionare tutte le metriche per visualizzare l'intero set]: ./media/appinsights/appinsights-62allchoices.png
  [test Web]: ../app-insights-monitor-web-app-availability/
  [Inserire e cercare istruzioni di traccia]: ../app-insights-search-diagnostic-logs/
  [Monitoraggio dell'utilizzo]: ../app-insights-web-track-usage/
  [Risoluzione dei problemi]: ../app-insights-troubleshoot-faq/
  [Monitoraggio delle prestazioni delle applicazioni Web]: ../app-insights-web-monitor-performance/
