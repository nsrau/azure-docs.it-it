<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitoraggio dell'integrità e dell'utilizzo di un'app con Application Insights" description="Introduzione a Application Insights. Analizzare l'uso, la disponibilità e le prestazioni delle applicazioni locali o Microsoft Azure." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Monitoraggio delle prestazioni delle applicazioni Web

*Application Insights è disponibile in anteprima.*


Questo prodotto consente di accertarsi che le prestazioni della propria applicazione siano ottimali e di scoprire rapidamente eventuali errori. [Application Insights][start] rileverà eventuali problemi ed eccezioni sulle prestazioni e aiuterà a trovare e diagnosticare le cause radice.

Application Insights è in grado di monitorare le applicazioni Web ASP.NET ospitate localmente o su macchine virtuali, oltre ai siti Web di Microsoft Azure. 

* [Configurazione del monitoraggio delle prestazioni](#setup)
* [Visualizzazione dei dati](#view)
* [Interpretazione dei dati](#metrics)
* [Diagnostica dei problemi](#diagnosis)
* [Passaggi successivi](#next)

## <a name="setup"></a>Configurazione del monitoraggio delle prestazioni

Se Application Insights non è stato ancora aggiunto al progetto (vale a dire, se ApplicationInsights.config non è presente), scegliere uno dei modi seguenti per iniziare:

* [Aggiungere Application Insights al progetto dell'app in Visual Studio][greenbrown]: metodo consigliato. Oltre al monitoraggio passivo delle prestazioni, è possibile inserire registrazioni diagnostiche e tenere traccia dell'uso.
* [Monitorare le prestazioni di un sito Web attivo][redfield]: in questo modo non è necessario aggiornare il progetto dell'applicazione o ridistribuire il sito Web.
* [Per un sito Web di Microsoft Azure](../insights-how-to-customize-monitoring/)  è già possibile vedere le metriche nel filtro di monitoraggio di tale sito. 


## <a name="view"></a>Visualizzazione dei report

Eseguire l'applicazione con F5 e provarla aprendo diverse pagine.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati ricevuti.

![](./media/appinsights/appinsights-09eventcount.png)


Aprire Application Insights dal progetto.

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


Cercare i dati nei riquadri **Integrità applicazione**. All'inizio si vedranno solo uno o due punti. ad esempio:

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

Quando si esegue la modalità di debug, la telemetria viene velocizzata nella pipeline, quindi i dati vengono visualizzati in pochi secondi. Quando si distribuisce l'app, i dati si accumulano più lentamente.

Se all'inizio i dati non vengono visualizzati, attendere un minuto e quindi fare clic su Aggiorna.

### Esplorazione delle metriche

Fare clic su qualsiasi riquadro per visualizzare altri dettagli e per vedere i risultati relativi a un periodo più lungo. Ad esempio, fare clic sul riquadro delle richieste e quindi selezionare un intervallo di tempo:


![Click through to more data and select a time range](./media/appinsights/appinsights-48metrics.png)

Fare clic su un grafico per scegliere quali metriche visualizzare:

![Click a graph to choose metrics](./media/appinsights/appinsights-61perfchoices.png)

> [AZURE.NOTE] **Deselezionare tutte le metriche** per visualizzare la selezione completa disponibile. Le metriche sono suddivise in gruppi; quando si seleziona qualsiasi membro di un gruppo, vengono visualizzati solo gli altri membri di quel gruppo.


## <a name="metrics"></a>Interpretazione dei dati: Riquadri e report sulle prestazioni

È disponibile una vasta gamma di metriche sulle prestazioni. Ora verranno esaminate quelle che vengono visualizzate per impostazione predefinita nel pannello dell'applicazione.


### Richieste

Il numero di richieste HTTP ricevute in un periodo specificato. Confrontare questo numero con i risultati di altri report per analizzare il comportamento dell'app al variare del carico.

Le richieste HTTP includono tutte le richieste GET o POST di pagine, dati e immagini.

Fare clic sul riquadro per visualizzare i conteggi per URL specifici.

### Tempo medio di risposta

Misura il tempo intercorso tra la ricezione di una richiesta Web da parte dell'applicazione e la risposta restituita.

I punti indicano una media mobile. Se le richieste sono molte, alcune potrebbero deviare dalla media senza picchi o flessioni evidenti nel grafico.

Cercare i picchi anomali. In genere, il tempo di risposta aumenta con l'aumento delle richieste. Se l'aumento è sproporzionato, l'app potrebbe raggiungere il limite di una risorsa, ad esempio la CPU o la capacità di un servizio usato.

Fare clic sul riquadro per visualizzare i tempi per URL specifici.

![](./media/appinsights/appinsights-42reqs.png)


### Richieste lente

![](./media/appinsights/appinsights-44slowest.png)

Mostra quali richieste potrebbero necessitare di un'ottimizzazione delle prestazioni.


### Richieste non riuscite

![](./media/appinsights/appinsights-46failed.png)

La quantità di richieste che hanno restituito eccezioni non rilevate.

Fare clic sul riquadro per visualizzare i dettagli di errori specifici e selezionare una singola richiesta per visualizzarne i dettagli. 

Viene conservato solo un campione di errori rappresentativi per l'analisi individuale.

### Altre metriche

Per sapere quali altri metriche è possibile visualizzare, fare clic su un grafico e deselezionare tutte le metriche per vedere l'intero set disponibile. Fare clic su (i) per visualizzare la definizione di ciascuna metrica.

![Deselect all metrics to see the whole set](./media/appinsights/appinsights-62allchoices.png)


La selezione di una metrica nasconderà le altre metriche che non possono essere visualizzate nello stesso grafico.


## <a name="diagnosis"></a>Diagnostica dei problemi

Di seguito vengono riportati alcuni suggerimenti su come trovare e diagnosticare i problemi di prestazioni:

* Configurare i [test Web][availability] in modo da ricevere un avviso se il sito Web non risponde o risponde in maniera non corretta o lentamente. 
* Confrontare il conteggio delle richieste con altre metriche per verificare se gli errori o la risposta lenta sono collegati al carico.
* [Inserire e cercare istruzioni di traccia][diagnostic] nel codice per individuare i problemi.

## <a name="next"></a>Passaggi successivi

[Test Web][availability]: possibilità di inviare richieste Web all'applicazione a intervalli regolari da tutto il mondo.

[Acquisire e cercare tracce diagnostiche][diagnostic]: possibilità di inserire chiamate di traccia ed esaminare i risultati per individuare i problemi.

[Monitoraggio dell'utilizzo][usage]: possibilità di scoprire in che modo le persone usano l'applicazione.

[Risoluzione dei problemi][qna] e domande e risposte



[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



