<properties 
	pageTitle="Introduzione ad Application Insights con Java in Eclipse" 
	description="Usare il plug-in Eclipse per aggiungere il monitoraggio delle prestazioni e l'uso nel sito Web Java con Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="awills"/>
 
# Introduzione ad Application Insights con Java in Eclipse

SDK di Application Insights invia dati di telemetria dall'applicazione Web Java in modo da poter analizzare l'uso e le prestazioni. Il plug-in Eclipse per Application Insights installa automaticamente SDK nel progetto in modo da ottenere i dati di telemetria predefiniti, oltre a un'API che è possibile usare per scrivere dati di telemetria personalizzati.


## Prerequisiti

Attualmente il plug-in funziona per Dynamic Web Project in Eclipse. [Aggiungere Application Insights ad altri tipi di progetti Java][java].

Sono necessari gli elementi seguenti:

* Oracle JRE 1.6 o versione successiva
* Una sottoscrizione a [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
* [ Eclipse IDE per sviluppatori Java EE](http://www.eclipse.org/downloads/), Indigo o versioni successive.
* Windows 7 o versioni successive o Windows Server 2008 o versioni successive

## Installare SDK su Eclipse \(una volta\)

È sufficiente eseguire questa operazione una volta per ogni macchina. Con questo passaggio viene installato un toolkit che può quindi aggiungere SDK a ciascun progetto Web dinamico.Con questo passaggio viene installato un toolkit che può quindi aggiungere SDK a ciascun Dynamic Web Project.

1. Dal menu Help di Eclipse scegliere Install New Software.

    ![Guida in linea, Installa nuovo Software](./media/app-insights-java-eclipse/0-plugin.png)

2. SDK è in http://dl.msopentech.com/eclipse, in Azure Toolkit.
3. Deselezionare l'opzione **Contatta tutti i siti di aggiornamento...**

    ![Per Application Insights SDK deselezionare tutti i siti di aggiornamento Contatta tutti](./media/app-insights-java-eclipse/1-plugin.png)

Seguire i passaggi rimanenti per ogni progetto Java.

## Ottenere una chiave di strumentazione di Application Insights

Verrà visualizzata l'analisi di utilizzo e delle prestazioni in una risorsa di Azure nel portale Web di Azure. In questo passaggio è possibile impostare una risorsa di Azure per l'applicazione.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com). È necessaria una [sottoscrizione di Azure](http://azure.microsoft.com/).
2. Creare una nuova risorsa di Application Insights

    ![Fare clic su + e scegliere Application Insights](./media/app-insights-java-get-started/01-create.png)
3. Impostare il tipo di applicazione nell'applicazione Web Java.

    ![Inserire un nome, scegliere l'app Web Java e fare clic su Crea](./media/app-insights-java-get-started/02-create.png)
4. Ottenere la chiave di strumentazione della nuova risorsa. È necessario incollarla nel progetto in Eclipse.

    ![Nella panoramica della nuova risorsa, fare clic su Proprietà e copiare la chiave di strumentazione](./media/app-insights-java-get-started/03-key.png)

## Aggiungere SDK al progetto Java

1. Aggiungere Application Insights dal menu di scelta rapida del progetto Web.

    ![Nella panoramica della nuova risorsa, fare clic su Proprietà e copiare la chiave di strumentazione](./media/app-insights-java-eclipse/4-addai.png)
2. Incollare la chiave di strumentazione recuperata dal portale di Azure.

    ![Nella panoramica della nuova risorsa, fare clic su Proprietà e copiare la chiave di strumentazione](./media/app-insights-java-eclipse/5-config.png)


La chiave viene inviata insieme a tutti gli elementi di dati di telemetria e indica ad Application Insights di visualizzarla nella risorsa.

## Eseguire l'applicazione e visualizzare le metriche

Eseguire l'applicazione.

Tornare alla risorsa di Application Insights in Microsoft Azure.

Nel pannello Panoramica verranno visualizzati i dati delle richieste HTTP. Se non sono visualizzati, attendere alcuni secondi e quindi fare clic su Aggiorna.

![Risposta del server, conteggi delle richieste ed errori](./media/app-insights-java-track-http-requests/5-results.png)
 

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate.

![Numero di richieste per nome](./media/app-insights-java-track-http-requests/6-barchart.png)


[Altre informazioni sulle metriche.][metrics]

 

E quando si visualizzano le proprietà di una richiesta, è possibile visualizzare gli eventi di telemetria associati, ad esempio le richieste e le eccezioni.
 
![Tutte le tracce per questa richiesta](./media/app-insights-java-track-http-requests/7-instance.png)


## Telemetria sul lato client

Dal riquadro Avvio rapido nel pannello Panoramica, è possibile ottenere uno script da aggiungere alle pagine Web.

Le metriche di visualizzazioni pagine, utenti e sessioni verranno visualizzate nel pannello Panoramica:

![Sessioni, utenti e visualizzazioni pagina](./media/appinsights/appinsights-47usage-2.png)

[Altre informazioni sull'impostazione della telemetria sul lato client.][usage]

## Test Web di disponibilità

Application Insights può testare il sito Web a intervalli regolari per verificare che funzioni e risponda correttamente. Selezionare il grafico del test Web vuoto nel pannello Panoramica e fornire l'URL pubblico.

Se il sito è inattivo, si otterranno grafici dei tempi di risposta, nonché notifiche di posta elettronica.

![Esempio di test Web](./media/appinsights/appinsights-10webtestresult.png)

[Altre informazioni sui test Web di disponibilità.][availability]

## Log di diagnostica

Se si usa Logback o Log4J \(v1.2 o v2.0\) per la traccia, è possibile inviare automaticamente i log di traccia ad Application Insights dove è possibile esplorarli e eseguirvi ricerche.

[Altre informazioni sui log di diagnostica][javalogs]

## Telemetria personalizzata 

Inserire alcune righe di codice nell'applicazione Web Java per scoprire come viene usato dagli utenti o per agevolare la diagnosi dei problemi.

È possibile inserire il codice sia nella pagina Web JavaScript che in Java lato server.

[Informazioni su dati di telemetria personalizzati][track]



## Passaggi successivi

#### Rilevare e diagnosticare i problemi

* [Aggiungere telemetria client Web][usage] per ottenere dati di telemetria delle prestazioni dal client Web.
* [Configurare i test Web][availability] in modo da assicurarsi che l'applicazione sia disponibile e reattiva.
* [Cercare eventi e log][diagnostic] per facilitare la diagnosi dei problemi.
* [Acquisire le tracce di Log4J o Logback][javalogs]

#### Tenere traccia dell'utilizzo

* [Aggiungere dati di telemetria al client Web][usage] per monitorare le visualizzazioni pagina e le metriche utente di base.
* [Tenere traccia di eventi personalizzati e metriche][track] per altre informazioni sulle modalità di uso dell'applicazione, sia sul lato client che server.


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54-->