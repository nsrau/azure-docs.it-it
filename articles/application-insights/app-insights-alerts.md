<properties 
	pageTitle="Impostare gli avvisi in Application Insights" 
	description="Ricevere messaggi di posta elettronica per arresti anomali, eccezioni, modifiche alla metrica." 
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
	ms.date="09/14/2015" 
	ms.author="awills"/>
 
# Impostare gli avvisi in Application Insights

[Visual Studio Application Insights][start] può segnalare le modifiche nelle metriche delle prestazioni o di utilizzo nell'app.

Application Insights monitora l'app attiva in un'[ampia varietà di piattaforme][platforms] per consentire di diagnosticare i problemi di prestazioni e comprendere i criteri di utilizzo.

Vi sono due tipi di avvisi:
 
* I **test Web** indicano quando il sito non è disponibile su Internet o risponde lentamente. [Altre informazioni][availability]
* Gli **avvisi delle metriche** indicano quando una qualsiasi metrica supera un valore di soglia per un determinato periodo, ad esempio, numero di errori, memoria o visualizzazioni di pagina. 

Esiste una [pagina separata sui test Web][availability], pertanto qui vengono illustrati gli avvisi delle metriche.

## Avvisi delle metriche

Se non è stato ancora configurato Application Insights per l'app [eseguire prima questa operazione][start].

Per ricevere un messaggio di posta elettronica quando una metrica supera una soglia, iniziare da Esplora metriche o dal riquadro Regole avvisi nel pannello Panoramica.

![Nel pannello Regole avvisi scegliere Aggiungi avviso. Impostare l'app come risorsa per misurare, specificare un nome per l'avviso e scegliere una metrica.](./media/app-insights-alerts/01-set-metric.png)

Impostare la risorsa prima delle altre proprietà. **Scegliere la risorsa "(components)"** per impostare avvisi sulle metriche relative a prestazioni e utilizzo.

Prendere nota delle unità in cui viene chiesto di immettere il valore soglia.

Il nome che si dà all'avviso deve essere univoco all'interno del gruppo di risorse (non solo dell'applicazione).

*Il pulsante Aggiungi avviso non viene visualizzato*: si sta usando un account aziendale? È possibile impostare gli avvisi se si ha l'accesso come proprietario o come collaboratore a questa risorsa dell'applicazione. Controllare in Impostazioni -> Utenti. [Informazioni sul controllo di accesso][roles].

## Visualizzare gli avvisi

Si ottiene un messaggio di posta elettronica quando un avviso modifica lo stato tra attivo e inattivo.

Lo stato corrente di ogni avviso viene visualizzato nel pannello delle regole di avviso.

La cronologia delle modifiche di stato è nel registro eventi di operazioni:

![Nella parte inferiore del pannello Panoramica fare clic su 'Eventi nell'ultima settimana'](./media/app-insights-alerts/09-alerts.png)

*Questi "eventi" sono correlati a eventi di telemetria o a eventi personalizzati?*

* No. Questi eventi operativi sono semplicemente un log delle operazioni che si sono verificate in questa risorsa dell'applicazione. 

## Avvisi di disponibilità

È possibile configurare i test Web che testano qualsiasi sito Web ovunque nel mondo. [Altre informazioni][availability]

## Quali sono gli avvisi corretti da impostare?

Dipende dall'applicazione. Per cominciare, è consigliabile non impostare un numero eccessivo di metriche. Esaminare i grafici delle metriche mentre è in esecuzione l'app per avere un'idea del comportamento normale. Sarà così possibile trovare un modo per migliorare le prestazioni. Impostare quindi gli avvisi affinché indichino quando le metriche superano la zona normale.

Tra gli avvisi più diffusi sono compresi:

* I [test Web][availability] che sono importanti se l'applicazione è un sito Web o un servizio Web visibile sulla rete Internet pubblica. Indicano se il sito è inattivo o se risponde lentamente, anche se è un problema del gestore telefonico anziché dell'app. Sono tuttavia test sintetici, per cui non misurano l'esperienza effettiva degli utenti.
* Le [metriche del browser][client], soprattutto i tempi di caricamento delle pagine del browser, che sono ideali per le applicazioni Web. Se la pagina presenta molti script, è opportuno fare attenzione alle eccezioni del browser. Per ottenere queste metriche e avvisi, è necessario configurare il [monitoraggio delle pagine Web][client].
* Il tempo di risposta del server e le richieste non riuscite per il lato server delle applicazioni Web. Oltre a impostare gli avvisi, prestare attenzione a queste metriche per vedere se variano in modo sproporzionato con frequenza delle richieste elevata: questa situazione potrebbe indicare che l'app sta esaurendo le risorse.

## Impostare avvisi tramite PowerShell

Per la maggior parte dei casi, è sufficiente impostare manualmente gli avvisi. Ma se si desidera creare automaticamente avvisi delle metriche, è possibile farlo tramite PowerShell.

#### Installazione singola

Se non si è utilizzato prima PowerShell con la sottoscrizione di Azure:

1. Installare il modulo Azure Powershell nel computer in cui si desidera eseguire gli script. 
 * Installare [Installazione guidata piattaforma Web Microsoft (v5 o versione successiva)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Usarla per installare Microsoft Azure PowerShell.
2. Avviare Azure PowerShell e [connettersi alla sottoscrizione](powershell-install-configure.md):

    ```
    Add-AzureAccount
    ```

#### Ottenere avvisi

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### Aggiunta di una regola


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

Inviare un messaggio di posta elettronica se la risposta del server alle richieste HTTP, calcolate su una media di 5 minuti, è più lenta di 1 secondo. La risorsa di Application Insights è denominata IceCreamWebApp ed è nel gruppo di risorse Fabrikam. Sono il proprietario della sottoscrizione Azure.

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

Ho un'applicazione in cui ho utilizzato [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) per segnalare una metrica denominata "salesPerHour". Inviare che un messaggio di posta elettronica ai miei colleghi, se "salesPerHour" è inferiore a 100, su una media calcolata di più di 24 ore.

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

La stessa regola può essere utilizzata per la metrica riportata utilizzando il [parametro misura](app-insights-api-custom-events-metrics.md#properties) di un altra chiamata di rilevamento come ad esempio TrackEvent o trackPageView.

#### Nomi delle metriche

Nome delle metriche | Nome della schermata | Descrizione
---|---|---
`basicExceptionBrowser.count`|Eccezioni del browser|Numero di eccezioni non rilevate nel browser.
`basicExceptionServer.count`|Eccezioni del server|Numero di eccezioni non gestite generate dall'applicazione
`clientPerformance.clientProcess.value`|Tempo di elaborazione del client|Tempo tra la ricezione dell’ultimo byte di un documento fino a quando non viene caricato il DOM. Si stanno ancora elaborando le richieste asincrone.
`clientPerformance.networkConnection.value`|Tempo di connessione della rete di caricamento della pagina| Tempo che impiega il browser per connettersi alla rete. Può essere 0 se memorizzato nella cache.
`clientPerformance.receiveRequest.value`|Tempo di risposta della ricezione| Tempo tra l'invio di richiesta di del browser e l’inizio di ricezione della risposta.
`clientPerformance.sendRequest.value`|Tempo di richiesta di invio| Tempo impiegato dal browser per inviare una richiesta.
`clientPerformance.total.value`|Tempo di caricamento della pagina del browser|Tempo di richiesta dell'utente finché non vengono caricate DOM, fogli di stile, script e immagini.
`performanceCounter.available_bytes.value`|Memoria disponibile|Memoria fisica immediatamente disponibile per un processo o per il sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Velocità di elaborazione IO|Byte totali al secondo per la lettura e la scrittura in file, rete e dispositivi.
`performanceCounter.number_of_exceps_thrown_per_sec`|frequenza (eccezione)|Eccezioni generate al secondo.
`performanceCounter.percentage_processor_time.value`|Processo della CPU|La percentuale di tempo trascorso di tutti i thread di processo utilizzati dal processore per eseguire istruzioni per il processo di applicazioni.
`performanceCounter.percentage_processor_total.value`|Tempo del processore|La percentuale di tempo impiegato dal processore in thread non-Idle.
`performanceCounter.process_private_bytes.value`|Byte privati del processo|Memoria assegnata esclusivamente ai processi dell'applicazione monitorata.
`performanceCounter.request_execution_time.value`|Tempo di esecuzione della richiesta di ASP.NET|Tempo di esecuzione della richiesta più recente.
`performanceCounter.requests_in_application_queue.value`|Richieste ASP.NET nella coda di esecuzione|Lunghezza della coda di richiesta dell'applicazione.
`performanceCounter.requests_per_sec`|Frequenza di richieste ASP.NET|Frequenza di tutte le richieste all'applicazione al secondo da ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Errori di dipendenze|Conteggio delle chiamate non riuscite effettuate dall'applicazione del server a risorse esterne.
`request.duration`|Tempo di risposta del server.|Tempo tra la ricezione di una richiesta HTTP e l’invio finale della risposta.
`request.rate`|Frequenza di richieste|Frequenza di tutte le richieste all'applicazione al secondo.
`requestFailed.count`|Richieste non riuscite|Numero di richieste HTTP che hanno generato un codice di risposta > = 400 
`view.count`|Visualizzazioni pagina|Numero di richieste dell’utente client per una pagina web. Il traffico sintetico è filtrato.
{nome metrico personalizzato}|{nome metrico}|Il valore della metrica segnalato da [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) o il [parametro delle misurazioni di una chiamata di rilevamento](app-insights-api-custom-events-metrics.md#properties).

   


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=Sept15_HO3-->