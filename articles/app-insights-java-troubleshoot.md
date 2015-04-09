<properties 
	pageTitle="Risoluzione dei problemi di Application Insights in un progetto Web Java" 
	description="Guida alla risoluzione dei problemi e domande e risposte." 
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
	ms.date="03/24/2015" 
	ms.author="awills"/>
 
# Risoluzione dei problemi e domande e risposte per Application Insights per Java

Domande o problemi con [Application Insights di Visual Studio in Java][java]? Ecco alcuni suggerimenti.


## Errori di compilazione

*Quando si aggiunge Application Insights SDK In Eclipse tramite Maven o Gradle, vengono visualizzati errori di convalida relativi a compilazione o checksum.*

* Se l'elemento di dipendenza <version> usa un modello con caratteri jolly (ad esempio <version>[0.9,)</version>), provare a indicare una versione specifica, come <version>0.9.1</version>

## Dati non presenti 

*Dopo avere aggiunto Application Insights correttamente ed avere eseguito l'app, nel portale non vengono visualizzati dati.*

* Attendere un minuto, quindi fare clic su Aggiorna. Al momento, l'aggiornamento non avviene automaticamente.
* Verificare di disporre di una chiave di strumentazione definita nel file ApplicationInsights.xml (presente nella cartella resources del progetto).
* Verificare che non vi siano nodi `<DisableTelemetry>true</DisableTelemetry>` nel file xml.
* Nel firewall, potrebbe essere necessario aprire le porte TCP 80 e 443 per il traffico in uscita verso dc.services.visualstudio.com e f5.services.visualstudio.com.
* Nella schermata iniziale di Microsoft Azure osservare la mappa dello stato dei servizi. Se ci sono indicazioni di avviso, attendere che tornino alla normalità, quindi chiudere e riaprire il pannello dell'applicazione di Application Insights.
* Attivare la registrazione nella finestra della console IDE aggiungendo un elemento <SDKLogger /> sotto il nodo radice nel file ApplicationInsights.xml (nella cartella resources del progetto) e verificare la presenza di voci con prefisso [Error].


## Dati di utilizzo non presenti

*I dati relativi a richieste e tempi di risposta vengono visualizzati, ma non sono presenti dati relativi alla visualizzazione di pagine, ai browser o dati relativi agli utenti.*

L'app è stata correttamente configurata per inviare dati di telemetria dal server. Il passaggio successivo è [configurare le pagine Web per inviare dati di telemetria dal Web browser][usage].

In alternativa, se il client è un'app in un [telefono o altro dispositivo][platforms], è possibile inviare i dati di telemetria da tali dispositivi. 

Usare la chiave di strumentazione per impostare la telemetria sia sul client che sul server. I dati verranno visualizzati nella stessa risorsa di Application Insights e sarà possibile correlare eventi dal client e dal server.



## Disabilitazione della telemetria

*In che modo è possibile disabilitare la raccolta di dati di telemetria?*

Nel codice:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Oppure** 

Aggiornare ApplicationInsights.xml (nella cartella resources del progetto). Aggiungere la riga seguente sotto il nodo radice:

    <DisableTelemetry>true</DisableTelemetry>

Usando il metodo XML, dopo la modifica di questo valore sarà necessario riavviare l'applicazione.

## Modifica della destinazione

*In che modo è possibile modificare la risorsa di Azure che riceve i dati del progetto?*

* [Ottenere la chiave di strumentazione della nuova risorsa.][java]
* Se si è aggiunto Application Insights al progetto usando Azure Toolkit for Eclipse, fare clic con il pulsante destro del mouse sul progetto Web, selezionare **Azure**, **Configura Application Insights**, quindi modificare la chiave.
* Oppure, aggiornare la chiave nel file ApplicationInsights.xml presente nella cartella resources del progetto.


## Schermata iniziale di Azure

*Guardando nel [portale di Azure](http://portal.azure.com): la mappa contiene informazioni sull'app?*

No, la mappa visualizza lo stato di integrità dei server Azure nelle varie parti del mondo.

*Falla schermata iniziale (home) di Azure, in che modo è possibile trovare i dati relativi all'app?*

Supponendo che [l'app sia stata impostata per Application Insights][java], fare clic su Sfoglia, selezionare Application Insights, quindi selezionare la risorsa dell'app creata per l'app specifica. Per raggiungere questa area più velocemente in futuro, è possibile aggiungere l'app alla schermata iniziale.

## Server Intranet

*È possibile monitorare un server nella Intranet?*

Sì, purché il server possa inviare dati di telemetria al portale di Application Insights tramite la rete Internet pubblica. 

Nel firewall, potrebbe essere necessario aprire le porte TCP 80 e 443 per il traffico in uscita verso dc.services.visualstudio.com e f5.services.visualstudio.com.

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

[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=49-->