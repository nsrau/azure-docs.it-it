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
	ms.date="02/19/2016" 
	ms.author="awills"/>
 
# Impostare gli avvisi in Application Insights

[Visual Studio Application Insights][start] segnala le variazioni nelle metriche di utilizzo o delle prestazioni nell'app.

Application Insights esegue il monitoraggio dell'app attiva in un'[ampia varietà di piattaforme][platforms] per consentire di diagnosticare i problemi di prestazioni e acquisire informazioni sui modelli di utilizzo.

Esistono due tipi di avvisi:
 
* I **test Web** indicano quando il sito non è disponibile in Internet o risponde lentamente. [Altre informazioni][availability]
* Gli **avvisi delle metriche** indicano quando una qualsiasi metrica supera un valore di soglia per un determinato periodo, ad esempio, numero di errori, memoria o visualizzazioni di pagina. 

Poiché è disponibile una [pagina separata sui test Web][availability], qui verranno descritti gli avvisi delle metriche.

> [AZURE.NOTE] Si potrebbero ricevere messaggi di posta elettronica anche dal [rilevamento proattivo](app-insights-proactive-detection.md), che avvisano di anomalie nelle prestazioni dell'applicazione. A differenza degli avvisi, queste notifiche vengono eseguite senza che sia necessario impostarle. Hanno lo scopo di ottimizzare le prestazioni dell'applicazione e non di segnalare problemi immediati.

## Avvisi delle metriche

Se non è stato ancora configurato Application Insights per l'app [eseguire prima questa operazione][start].

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

È possibile configurare test Web che consentono di testare qualsiasi sito Web in qualunque parte del mondo. [Altre informazioni][availability]

## Quali sono gli avvisi corretti da impostare?

Dipende dall'applicazione. Per iniziare, è consigliabile non impostare un numero eccessivo di metriche. Esaminare i grafici delle metriche mentre l'app è in esecuzione per acquisire informazioni sul comportamento normale. Sarà così possibile trovare un modo per migliorare le prestazioni. Impostare quindi gli avvisi per ricevere le notifiche quando le metriche superano la zona normale.

Gli avvisi più diffusi includono:

* I [test Web][availability], importanti se l'applicazione è un sito Web o un servizio Web visibile sulla rete Internet pubblica. Indicano se il sito è inattivo o se risponde lentamente, anche se si tratta di un problema dell'operatore anziché dell'app. Sono tuttavia test sintetici e quindi non misurano l'esperienza effettiva degli utenti.
* Le [metriche del browser][client], soprattutto i **tempi di caricamento delle pagine** del browser, ottimali per le applicazioni Web. Se la pagina presenta molti script, è opportuno prestare attenzione alle **eccezioni del browser**. Per ottenere queste metriche e questi avvisi, è necessario configurare il [monitoraggio delle pagine Web][client].
* Il **tempo di risposta del server** e le **richieste non riuscite** per il lato server delle applicazioni Web. Oltre a impostare gli avvisi, prestare attenzione a queste metriche per vedere se variano in modo sproporzionato in caso di frequenza elevata delle richieste: questa situazione può indicare che l'app sta esaurendo le risorse.
* **Eccezioni del server**: per visualizzarle, è necessario effettuare alcuni passaggi di [configurazione aggiuntivi](app-insights-asp-net-exceptions.md).

## Automazione

* [Usare PowerShell per automatizzare la configurazione degli avvisi](app-insights-powershell-alerts.md)
* [Usare webhook per automatizzare la risposta agli avvisi](../azure-portal/insights-webhooks-alerts.md)

## Vedere anche

* [Test Web di disponibilità](app-insights-monitor-web-app-availability.md)
* [Automatizzare la configurazione degli avvisi](app-insights-powershell-alerts.md)
* [Rilevamento proattivo](app-insights-proactive-detection.md) 



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0224_2016-->