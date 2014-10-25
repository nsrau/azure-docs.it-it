<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Monitorare la velocità di risposta e la disponibilità dell'app Web

Dopo aver distribuito l'applicazione Web, è possibile configurare test per monitorarne la disponibilità e la velocità di risposta. Application Insights invierà richieste Web a intervalli regolari da diverse parti del mondo, inviando un avviso se l'applicazione risponde in modo lento o non risponde.

È possibile configurare test Web per qualsiasi endpoint HTTP accessibile dalla rete Internet pubblica.

1.  [Creare una nuova applicazione][Creare una nuova applicazione]

-   [Configurare un test Web][Configurare un test Web]
-   [Visualizzare i report di monitoraggio][Visualizzare i report di monitoraggio]
-   [Modificare o disabilitare un test][Modificare o disabilitare un test]
-   [Passaggi successivi][Passaggi successivi]

## <a name="create"></a>1. Creare una nuova applicazione

Ignorare questo passaggio se si sta già [monitorando lo stato e l'utilizzo][monitorando lo stato e l'utilizzo] dell'app Web e si vogliono visualizzare i dati sulla disponibilità nella stessa posizione.

Se invece si preferisce mantenere questi risultati separati, accedere all'anteprima di Microsoft Azure e creare una nuova applicazione in Application Insights.

![New \> Application Insights][New \> Application Insights]

## <a name="setup"></a>2. Configurare un test Web

Nel pannello principale dell'applicazione in Application Insights fare clic sul riquadro dei test Web vuoto.

![Fare clic sul test di disponibilità vuoto][Fare clic sul test di disponibilità vuoto]

> *Se si dispone già di test Web, fare clic nel riquadro dei test Web e scegliere Aggiungi test Web.*

Inserire un nome per il test e l'URL da testare. L'URL deve essere visibile dalla rete Internet pubblica.

![Fill at least the URL of your website][Fill at least the URL of your website]

-   Le posizioni di test sono le ubicazioni dai cui i server inviano richieste Web all'URL indicato. Sceglierne due o tre, in modo da poter distinguere i problemi del sito Web dai problemi di rete. Non è possibile selezionare più di tre posizioni.

-   Criteri di successo: specificare i codici HTTP restituiti accettabili. 200 è il valore comunemente usato.

    È anche possibile specificare una stringa che deve essere contenuta in ogni risposta corretta. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. Non dimenticare che se il contenuto cambia può essere necessario aggiornare la stringa.

-   Avvisi: per impostazione predefinita, se si verificano errori ripetuti nell'arco di 15 minuti viene inviato un avviso. È possibile modificare l'impostazione in modo da aumentare la sensibilità del test e modificare gli indirizzi di posta elettronica a cui inviare la notifica.

### Testare più URL

È possibile aggiungere altri test per il numero di URL desiderato. Ad esempio, oltre a testare la home page è possibile verificare che il database sia in esecuzione testando l'URL per una ricerca.

![On the web tests blade, choose Add][On the web tests blade, choose Add]

## <a name="monitor"></a>3. Visualizzare i report di monitoraggio

Dopo 1-2 minuti, chiudere e riaprire il pannello dell'applicazione. In questa versione l'aggiornamento non viene eseguito automaticamente.

![Summary results on the home blade][Summary results on the home blade]

Questo è un riepilogo. Se sono stati definiti diversi test Web per l'applicazione, saranno tutti combinati in questa pagina.

Fare clic sul pannello dei test Web per vedere un elenco dei test Web.

Aprire uno specifico test Web.

![Click a specific webtest][Click a specific webtest]

Nel pannello dello specifico test Web scorrere verso il basso fino a **Test non superati** e selezionare un risultato.

![Click a specific webtest][1]

Il risultato mostra il motivo dell'errore.

![Webtest run result][Webtest run result]

Per maggiori dettagli, scaricare il file dei risultati ed esaminarlo in Visual Studio.

## <a name="edit"></a>4. Modificare o disabilitare un test

Aprire un singolo test per modificarlo o disabilitarlo.

![Edit or disable a web test][Edit or disable a web test]

Disabilitare i test Web può essere utile durante l'esecuzione di operazioni di manutenzione sul servizio.

## <a name="next"></a>Passaggi successivi

[Eseguire ricerche nei log di diagnostica][Eseguire ricerche nei log di diagnostica]

[Risoluzione dei problemi][Risoluzione dei problemi]

## <a name="next"></a>Altre informazioni

-   [Application Insights][Application Insights]
-   [Aggiungere Application Insights al progetto][monitorando lo stato e l'utilizzo]
-   [Monitorare un server Web attivo][Monitorare un server Web attivo]
-   [Esaminare le metriche in Application Insights][Esaminare le metriche in Application Insights]
-   [Ricerche nei log di diagnostica][Eseguire ricerche nei log di diagnostica]
-   [Verifica della disponibilità mediante test Web][Verifica della disponibilità mediante test Web]
-   [Verifica dell'utilizzo con eventi e metriche][Verifica dell'utilizzo con eventi e metriche]
-   [Domande e risposte e risoluzione dei problemi][Risoluzione dei problemi]

<!--Link references-->

  [Creare una nuova applicazione]: #create
  [Configurare un test Web]: #setup
  [Visualizzare i report di monitoraggio]: #monitor
  [Modificare o disabilitare un test]: #edit
  [Passaggi successivi]: #next
  [monitorando lo stato e l'utilizzo]: ../app-insights-monitor-application-health-usage/
  [New \> Application Insights]: ./media/appinsights/appinsights-11newApp.png
  [Fare clic sul test di disponibilità vuoto]: ./media/appinsights/appinsights-12avail.png
  [Fill at least the URL of your website]: ./media/appinsights/appinsights-13availChoices.png
  [On the web tests blade, choose Add]: ./media/appinsights/appinsights-16anotherWebtest.png
  [Summary results on the home blade]: ./media/appinsights/appinsights-14availSummary.png
  [Click a specific webtest]: ./media/appinsights/appinsights-15webTestList.png
  [1]: ./media/appinsights/appinsights-17-availViewDetails.png
  [Webtest run result]: ./media/appinsights/appinsights-18-availDetails.png
  [Edit or disable a web test]: ./media/appinsights/appinsights-19-availEdit.png
  [Eseguire ricerche nei log di diagnostica]: ../app-insights-search-diagnostic-logs/
  [Risoluzione dei problemi]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Monitorare un server Web attivo]: ../app-insights-monitor-performance-live-website-now/
  [Esaminare le metriche in Application Insights]: ../app-insights-explore-metrics/
  [Verifica della disponibilità mediante test Web]: ../app-insights-monitor-web-app-availability/
  [Verifica dell'utilizzo con eventi e metriche]: ../app-insights-track-usage-custom-events-metrics/
