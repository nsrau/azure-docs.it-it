<properties 
	pageTitle="Risoluzione dei problemi di Application Insights in dispositivi Windows" 
	description="Guida alla risoluzione dei problemi e domande e risposte." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="awills"/>
 
# Risoluzione dei problemi e domande e risposte per Application Insights per dispositivi Windows

Domande o problemi con [Visual Studio Application Insights in Windows][windows]? Ecco alcuni suggerimenti.



## Dati non presenti 

*Dopo avere aggiunto Application Insights correttamente ed avere eseguito l'app, nel portale non vengono visualizzati dati.*

* Attendere un minuto, quindi fare clic su Aggiorna. Al momento, l'aggiornamento non avviene automaticamente.
* Verificare di disporre di una chiave di strumentazione definita nel file ApplicationInsights.config e che la chiave corrisponda a quella disponibile nel portale di Application Insights. Per visualizzare la chiave, fare clic su Informazioni di base nel pannello della panoramica.
* Assicurarsi che l'applicazione [richieda l'accesso di rete in uscita](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx).
* È presente un firewall tra l'emulatore o il dispositivo di test e il portale di Application Insights? Potrebbe essere necessario aprire le porte TCP 80 e 443 per il traffico in uscita verso dc.services.visualstudio.com e f5.services.visualstudio.com.
* Nella schermata iniziale di Microsoft Azure osservare la mappa dello stato dei servizi. Se ci sono indicazioni di avviso, attendere che tornino alla normalità, quindi chiudere e riaprire il pannello dell'applicazione di Application Insights.


#### Non vengono più visualizzati i dati disponibili in precedenza

* Controllare il [blog sullo stato](http://blogs.msdn.com/b/applicationinsights-status/).
* È stata raggiunta la quota mensile relativa ai punti dati? Per saperlo, aprire Impostazioni/Quota e Prezzi. Se la quota è stata raggiunta, è possibile aggiornare il piano oppure pagare per disporre di ulteriore capacità. Vedere lo [schema dei prezzi](http://azure.microsoft.com/pricing/details/application-insights/).


## Come si aggiunge Application Insights a un'app universale?

Aggiungere manualmente i pacchetti NuGet a ogni progetto per dispositivo nella soluzione. Vedere [Introduzione - App universali][universal].

## Disabilitazione della telemetria

*In che modo è possibile disabilitare la raccolta di dati di telemetria?*

Nel codice:

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## Modifica della risorsa di destinazione

*In che modo è possibile modificare la risorsa di Application Insights che riceve i dati del progetto?*

Nel nuovo pannello di anteprima di Application Insights aprire Informazioni di base e copiare la chiave di chiave di strumentazione.

Incollare la chiave nel file ApplicationInsights.config in corrispondenza del nodo `<InstrumentationKey>`.

Per modificare invece le risorse di destinazione in fase di esecuzione, usare:

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## Come si esegue il monitoraggio di un'app client-server?

È possibile procedere in due modi:

* Creare due risorse di Application Insights (con diverse chiavi di strumentazione) per il client e il server, ma nello stesso gruppo di risorse di Azure. Ciò permetterà di passare da una risorsa all'altra con maggiore facilità.
* Usare una risorsa di Application Insights e inserire la rispettiva chiave di strumentazione nei progetti client e server. Sarà quindi possibile correlare le metriche e gli eventi dalle due origini.

Per semplificare la correlazione degli eventi nel client e nel server, generare un ID operazione per ogni richiesta. Trasmetterlo tra client e server e aggiungerlo alla telemetria a entrambe le estremità:

    telemetry.Context.OperationId = opid;


## Schermata iniziale di Azure

*Guardando nel [portale di Azure:](http://portal.azure.com): la mappa contiene informazioni sull'app?*

No, la mappa visualizza lo stato di integrità dei server Azure nelle varie parti del mondo.

*Dalla schermata iniziale (home) di Azure, in che modo è possibile trovare i dati relativi all'app?*

Supponendo che [l'app sia già stata impostata per Application Insights][windows], fare clic su Sfoglia, selezionare Application Insights, quindi selezionare la risorsa creata per l'app specifica. Per raggiungere questa area più velocemente in futuro, è possibile aggiungere la risorsa alla schermata iniziale.

## Conservazione dei dati 

*Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?*

Vedere l'argomento relativo a [conservazione dei dati e privacy][data].

## Passaggi successivi

*Application Insights è stato correttamente impostato per l'app server Java. Cos'altro è possibile fare?*

* [Monitorare la disponibilità delle pagine Web][availability]
* [Monitorare l'utilizzo delle pagine Web][usage]
* [Tenere traccia dell'utilizzo delle app per dispositivi e diagnosticarne i problemi][platforms]
* [Scrivere codice per tenere traccia dell'utilizzo dell'app][track]
* [Acquisire i log di diagnostica][javalogs]


## Ottenere aiuto

* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-custom-events-metrics-api.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->