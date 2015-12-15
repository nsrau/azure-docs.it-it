<properties 
	pageTitle="Impostare gli avvisi in Application Insights" 
	description="Ricevere messaggi di posta elettronica per arresti anomali, eccezioni, modifiche delle metriche." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>
 
# Impostare gli avvisi in Application Insights

[Visual Studio Application Insights][start] segnala le variazioni nelle metriche di utilizzo o delle prestazioni nell'app.

Application Insights esegue il monitoraggio dell'app attiva in un'[ampia varietà di piattaforme][platforms] per consentire di diagnosticare i problemi di prestazioni e acquisire informazioni sui modelli di utilizzo.

Esistono due tipi di avvisi:
 
* I **test Web** indicano quando il sito non è disponibile in Internet o risponde lentamente. [Altre informazioni][availability]
* Gli **avvisi delle metriche** indicano quando una qualsiasi metrica supera un valore di soglia per un determinato periodo, ad esempio, numero di errori, memoria o visualizzazioni di pagina. 

Poiché è disponibile una [pagina separata sui test Web][availability], qui verranno descritti gli avvisi delle metriche.

## Avvisi delle metriche

Se non si è ancora provveduto a configurare Application Insights per l'app, [eseguire prima questa operazione][start].

Per ricevere un messaggio di posta elettronica quando una metrica supera una soglia, iniziare da Esplora metriche o dal riquadro Regole avvisi nel pannello Panoramica.

![Nel pannello Regole di avviso scegliere Aggiungi avviso. Impostare l'app come la risorsa da misurare, specificare un nome per l'avviso e scegliere una metrica.](./media/app-insights-alerts/01-set-metric.png)

Impostare la risorsa prima delle altre proprietà. **Scegliere la risorsa "(components)"** per impostare avvisi sulle metriche relative a prestazioni e utilizzo.

Prendere nota delle unità in cui viene richiesto di immettere il valore di soglia.

Il nome assegnato all'avviso deve essere univoco all'interno del gruppo di risorse (non solo dell'applicazione).

*Il pulsante Aggiungi avviso non viene visualizzato*: si sta usando un account aziendale? È possibile impostare gli avvisi se si ha l'accesso come proprietario o come collaboratore a questa risorsa dell'applicazione. Controllare in Impostazioni -> Utenti. [Informazioni sul controllo di accesso][roles].

## Visualizzare gli avvisi

Si riceve un messaggio di posta elettronica quando lo stato dell'avviso passa da inattivo ad attivo e viceversa.

Lo stato corrente di ogni avviso viene visualizzato nel pannello delle regole di avviso.

Nell'elenco a discesa Avvisi è disponibile un riepilogo delle attività recenti:

![](./media/app-insights-alerts/010-alert-drop.png)

La cronologia delle modifiche di stato si trova nel log eventi delle operazioni:

![Nella parte inferiore del pannello Panoramica fare clic su 'Eventi nella settimana precedente'](./media/app-insights-alerts/09-alerts.png)

*Questi "eventi" sono correlati a eventi di telemetria o a eventi personalizzati?*

* No. Questi eventi operativi sono semplicemente un log delle operazioni che si sono verificate in questa risorsa dell'applicazione. 


## Funzionamento degli avvisi

* Per un avviso esistono due possibili stati: "avviso" e "integro". 

* Quando lo stato di un avviso cambia, viene inviato un messaggio di posta elettronica.

* Un avviso viene valutato ogni volta che arriva una metrica, ma non altrimenti.

* La valutazione aggrega la metrica per il periodo precedente e quindi la confronta con la soglia per determinare il nuovo stato.

* Il periodo scelto specifica l'intervallo per il quale vengono aggregate le metriche. Non influisce sulla frequenza con cui viene valutato l'avviso: ciò dipende dalla frequenza di arrivo delle metriche.

* Se per un periodo di tempo non arrivano dati per una determinata metrica, il divario comporta diversi effetti sulla valutazione dell'avviso e sui grafici in Esplora metriche. Se in Esplora metriche non viene visualizzato alcun dato per un periodo più lungo dell'intervallo di campionamento del grafico, il grafico mostrerà un valore pari a 0. Un avviso basato sulla metrica stessa non verrà valutato nuovamente e lo stato dell'avviso rimarrà invariato.

    Quando infine arrivano i dati, il grafico verrà riportato su un valore diverso da zero. L'avviso verrà valutato in base ai dati disponibili per il periodo specificato. Se il nuovo punto dati è l'unico disponibile nel periodo, la funzione di aggregazione si baserà solo su di esso.

* Un avviso può spesso passare velocemente dallo stato di avviso a quello integro e viceversa, anche se si imposta un periodo prolungato. Questa situazione può verificarsi se il valore della metrica si aggira intorno alla soglia. Non esiste alcuna isteresi nella soglia: la transizione allo stato di avviso si verifica in corrispondenza dello stesso valore della transizione allo stato integro.



## Avvisi di disponibilità

È possibile configurare test Web che consentono di testare qualsiasi sito Web in qualunque parte del mondo. [Altre informazioni][availability].

## Quali sono gli avvisi corretti da impostare?

Dipende dall'applicazione. Per iniziare, è consigliabile non impostare un numero eccessivo di metriche. Esaminare i grafici delle metriche mentre l'app è in esecuzione per acquisire informazioni sul comportamento normale. Sarà così possibile trovare un modo per migliorare le prestazioni. Impostare quindi gli avvisi per ricevere le notifiche quando le metriche superano la zona normale.

Gli avvisi più diffusi includono:

* I [test Web][availability], importanti se l'applicazione è un sito Web o un servizio Web visibile sulla rete Internet pubblica. Indicano se il sito è inattivo o se risponde lentamente, anche se si tratta di un problema dell'operatore anziché dell'app. Sono tuttavia test sintetici e quindi non misurano l'esperienza effettiva degli utenti.
* Le [metriche del browser][client], soprattutto i **tempi di caricamento delle pagine** del browser, ottimali per le applicazioni Web. Se la pagina presenta molti script, è opportuno prestare attenzione alle **eccezioni del browser**. Per ottenere queste metriche e questi avvisi, è necessario configurare il [monitoraggio delle pagine Web][client].
* Il **tempo di risposta del server** e le **richieste non riuscite** per il lato server delle applicazioni Web. Oltre a impostare gli avvisi, prestare attenzione a queste metriche per vedere se variano in modo sproporzionato in caso di frequenza elevata delle richieste: questa situazione può indicare che l'app sta esaurendo le risorse.
* **Eccezioni del server**: per visualizzarle, è necessario effettuare alcuni passaggi di [configurazione aggiuntivi](app-insights-asp-net-exceptions.md).

## Impostare gli avvisi tramite PowerShell

Per la maggior parte dei casi, è sufficiente impostare manualmente gli avvisi. Per creare automaticamente gli avvisi delle metriche, è necessario usare PowerShell.

#### Installazione singola

Se non si è mai usato PowerShell con la sottoscrizione di Azure:

Installare il modulo Azure Powershell nel computer in cui verranno eseguiti gli script.

 * Installare [Installazione guidata piattaforma Web Microsoft (v5 o versione successiva)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Usarla per installare Microsoft Azure PowerShell.


#### Connettersi ad Azure

Avviare Azure PowerShell e [connettersi alla sottoscrizione](powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


#### Ottenere gli avvisi

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### Aggiungere un avviso


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



#### Esempio 1

Invia un messaggio di posta elettronica se la risposta del server alle richieste HTTP, calcolate su una media di 5 minuti, richiede più di 1 secondo. La risorsa di Application Insights è denominata IceCreamWebApp e si trova nel gruppo di risorse Fabrikam. Sono il proprietario della sottoscrizione di Azure.

Il GUID è l'ID sottoscrizione (non la chiave di strumentazione dell'applicazione).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

#### Esempio 2

Ho un'applicazione in cui uso [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) per segnalare una metrica denominata "salesPerHour". Invia un messaggio di posta elettronica ai miei colleghi, se "salesPerHour" scende sotto il valore 100, calcolato su una media di 24 ore.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

È possibile usare la stessa regola per la metrica segnalata usando il [parametro di misura](app-insights-api-custom-events-metrics.md#properties) di un altra chiamata di rilevamento, ad esempio TrackEvent o trackPageView.

#### Nomi delle metriche

Nome metrica | Nome schermata | Descrizione
---|---|---
`basicExceptionBrowser.count`|Eccezioni del browser|Conteggio delle eccezioni non rilevate generate nel browser.
`basicExceptionServer.count`|Eccezioni del server|Conteggio delle eccezioni non gestite generate dall'applicazione
`clientPerformance.clientProcess.value`|Tempo di elaborazione client|Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione.
`clientPerformance.networkConnection.value`|Tempo di connessione alla rete per il caricamento della pagina| Tempo impiegato dal browser per connettersi alla rete. Può essere 0 se memorizzato nella cache.
`clientPerformance.receiveRequest.value`|Tempo per la ricezione della risposta| Tempo tra l'invio di richiesta del browser e l'inizio della ricezione della risposta.
`clientPerformance.sendRequest.value`|Tempo per l'invio della richiesta| Tempo impiegato dal browser per inviare la richiesta.
`clientPerformance.total.value`|Tempo di caricamento della pagina del browser|Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini.
`performanceCounter.available_bytes.value`|Memoria disponibile|Memoria fisica immediatamente disponibile per l'allocazione a un processo o utilizzabile dal sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Velocità di elaborazione I/O|Numero totale di byte al secondo letti e scritti in file, rete e dispositivi.
`performanceCounter.number_of_exceps_thrown_per_sec`|Frequenza di eccezioni|Eccezioni generate al secondo.
`performanceCounter.percentage_processor_time.value`|CPU processo|Percentuale di tempo trascorso di tutti i thread di processo usati dal processore per le istruzioni di esecuzione relative al processo dell'applicazione.
`performanceCounter.percentage_processor_total.value`|Tempo processore|Percentuale di tempo che il processore dedica a thread non inattivi.
`performanceCounter.process_private_bytes.value`|Byte privati processo|Memoria assegnata in modo esclusivo ai processi dell'applicazione monitorata.
`performanceCounter.request_execution_time.value`|Tempo di esecuzione della richiesta di ASP.NET|Tempo di esecuzione della richiesta più recente.
`performanceCounter.requests_in_application_queue.value`|Richieste ASP.NET nella coda di esecuzione|Lunghezza della coda di richieste dell'applicazione.
`performanceCounter.requests_per_sec`|Percentuale di richieste ASP.NET|Percentuale di tutte le richieste effettuate all'applicazione da ASP.NET in un secondo.
`remoteDependencyFailed.durationMetric.count`|Errori di dipendenze|Conteggio delle chiamate non riuscite effettuate dall'applicazione server alle risorse esterne.
`request.duration`|Tempo di risposta del server|Tempo compreso tra la ricezione di una richiesta HTTP e il completamento dell'invio della risposta.
`request.rate`|Frequenza di richieste|Percentuale di tutte le richieste effettuate all'applicazione in un secondo.
`requestFailed.count`|Richieste non riuscite|Conteggio delle richieste HTTP per cui è stato restituito un codice di risposta maggiore o uguale a 400 
`view.count`|Visualizzazioni pagina|Conteggio delle richieste utente del client per una pagina Web. Il traffico sintetico è filtrato.
{nome metrica personalizzato}|{nome metrica}|Valore della metrica segnalato da [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) o il [parametro delle misurazioni di una chiamata di rilevamento](app-insights-api-custom-events-metrics.md#properties).

Le metriche vengono inviate da moduli di telemetria diversi:

Gruppo metrica | Modulo dell'agente di raccolta
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>view | [JavaScript browser](app-insights-javascript.md)
performanceCounter | [Prestazioni](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dipendenza](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request,<br/>requestFailed|[Richiesta server](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)


## Vedere anche


* [Creare risorse Application Insights e test web da modelli](app-insights-powershell.md)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_1125_2015-->