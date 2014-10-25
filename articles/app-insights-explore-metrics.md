<properties title="Explore your metrics" pageTitle="Explore your metrics" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Esplorare le metriche

Se non si è ancora [configurato il progetto Web per Application Insights][configurato il progetto Web per Application Insights], è possibile farlo ora.

Ecco i report che è possibile visualizzare nel pannello dell'applicazione in Application Insights. Per passare alla pagina da Visual Studio, fare clic con il pulsante destro del mouse sul progetto Web e scegliere Apri Application Insights. Per accedervi dalla scheda iniziale di anteprima di Microsoft Azure, scegliere Sfoglia, Application Insights e quindi selezionare il progetto.

-   [Integrità dell'applicazione][Integrità dell'applicazione]
-   [Analisi dell'utilizzo][Analisi dell'utilizzo]
-   [Passaggi successivi][Passaggi successivi]

## <a name="health"></a>Integrità dell'applicazione

L'integrità dell'applicazione viene monitorata instrumentando il codice dell'applicazione.

![][]

### Tempo di risposta medio

Misura il tempo trascorso tra l'arrivo di una richiesta Web all'applicazione e la restituzione della risposta.

I punti indicano una media mobile. Se le richieste sono molte, alcune potrebbero deviare dalla media senza picchi o flessioni evidenti nel grafico.

Cercare i picchi anomali. In genere, il tempo di risposta aumenta all'aumentare delle richieste. Se l'aumento è sproporzionato, l'app potrebbe raggiungere il limite di una risorsa, ad esempio la CPU o la capacità di un servizio usato.

### Richieste

Numero di richieste ricevute in un periodo specificato. Confrontarlo con i risultati di altri grafici per verificare il comportamento dell'app al variare del carico.

### Test Web

![][1]

I [test Web][test Web] mostrano i risultati delle richieste Web inviate al server a intervalli regolari dai server Application Insights di tutto il mondo.

Verificare se i risultati cambiano a seconda del conteggio delle richieste.

[Come configurare i test Web][test Web].

### Richieste più lente

![][2]

Mostra le richieste che potrebbero richiedere l'ottimizzazione delle prestazioni.

### Ricerca diagnostica

![][3]

Se si è [configurata la registrazione dei dati di diagnostica][configurata la registrazione dei dati di diagnostica], fare clic per visualizzare gli ultimi eventi.

### Richieste non riuscite

![][4]

Conteggio delle richieste che hanno generato eccezioni non identificate.

## <a name="usage"></a>Analisi dell'utilizzo

![][5]

I dati sull'utilizzo provengono in parte dal server e in parte dagli [script nelle pagine Web][configurato il progetto Web per Application Insights].

### Sessioni per browser

Una *sessione* è un periodo che inizia quando un utente apre una pagina del sito Web e termina dopo un timeout di 30 minuti dall'ultimo invio di una richiesta Web da parte dell'utente.

Fare clic per fare zoom avanti sul grafico.

### Prime visualizzazioni pagina

Mostra i conteggi totali nelle ultime 24 ore.

Fare clic per visualizzare i grafici delle visualizzazioni pagina dell'ultima settimana.

### Ridisporre i riquadri

![Scegliere le impostazioni e personalizzare][Scegliere le impostazioni e personalizzare]

## <a name="next"></a>Passaggi successivi

[Configurare i test Web][test Web]

[Acquisire ed eseguire ricerche nei log di diagnostica][configurata la registrazione dei dati di diagnostica]

[Risoluzione dei problemi][Risoluzione dei problemi]

## Altre informazioni

-   [Application Insights][Application Insights]
-   [Aggiungere Application Insights al progetto][configurato il progetto Web per Application Insights]
-   [Monitorare un server Web attivo][Monitorare un server Web attivo]
-   [Esaminare le metriche in Application Insights][Esaminare le metriche in Application Insights]
-   [Ricerche nei log di diagnostica][configurata la registrazione dei dati di diagnostica]
-   [Verifica della disponibilità mediante test Web][test Web]
-   [Verifica dell'utilizzo con eventi e metriche][Verifica dell'utilizzo con eventi e metriche]
-   [Domande e risposte e risoluzione dei problemi][Risoluzione dei problemi]

<!--Link references-->

  [configurato il progetto Web per Application Insights]: ../app-insights-monitor-application-health-usage/
  [Integrità dell'applicazione]: #health
  [Analisi dell'utilizzo]: #usage
  [Passaggi successivi]: #next
  []: ./media/appinsights/appinsights-42reqs.png
  [1]: ./media/appinsights/appinsights-43webtests.png
  [test Web]: ../app-insights-monitor-web-app-availability/
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-45diagnostic.png
  [configurata la registrazione dei dati di diagnostica]: ../app-insights-search-diagnostic-logs/
  [4]: ./media/appinsights/appinsights-46failed.png
  [5]: ./media/appinsights/appinsights-47usage.png
  [Scegliere le impostazioni e personalizzare]: ./media/appinsights/appinsights-21-customizeblade.png
  [Risoluzione dei problemi]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Monitorare un server Web attivo]: ../app-insights-monitor-performance-live-website-now/
  [Esaminare le metriche in Application Insights]: ../app-insights-explore-metrics/
  [Verifica dell'utilizzo con eventi e metriche]: ../app-insights-track-usage-custom-events-metrics/
