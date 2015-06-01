<properties 
	pageTitle="Eseguire l'aggiornamento dalla versione precedente di Visual Studio Online di Application Insights" 
	description="Aggiornare progetti esistenti"
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="awills"/>
 
# Eseguire l'aggiornamento dalla versione precedente di Visual Studio Online di Application Insights

Questo documento può interessare solo se si dispone di un progetto che usa ancora la versione precedente di Application Insights, che faceva parte di Visual Studio Online. Quella versione verrà disattivata a tempo debito e pertanto si consiglia di eseguire l'aggiornamento alla nuova versione, che è un servizio all'interno di Microsoft Azure.

## Come controllare la versione installata?

Se è stato aggiunto Application Insights al progetto mediante Visual Studio 2013 Update 3 o versione successiva, molto probabilmente viene usata la nuova versione di Azure.

Aprire ApplicationInsights.config. Se si dispone dei nodi `ActiveProfile` e `Profiles`, si possiede la versione precedente ed è necessario eseguire l'aggiornamento.

Esaminare il progetto in Esplora soluzioni di Visual Studio e in Riferimenti selezionare Microsoft.ApplicationInsights. Nella finestra Proprietà individuare la versione. Se è inferiore a 0.12, è consigliabile eseguire l'aggiornamento.

## Se si dispone di un progetto Visual Studio...

1. Aprire il progetto in Visual Studio 2013 Update 3 o versione successiva.
2. Eliminare ApplicationInsights.config 
3. Rimuovere i pacchetti NuGet di Application Insights dal progetto. A tale scopo, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Gestisci pacchetti NuGet.
4. SDK: fare clic con il pulsante destro del mouse sul progetto e scegliere [Aggiungi Application Insights][greenbrown]. In tal modo si aggiunge SDK al progetto e si crea anche una nuova risorsa di Application Insights in Azure.
5. Registrazione: se il codice include chiamate all'API precedente, ad esempio LogEvent (), queste verranno individuate quando si tenta di compilare la soluzione. Aggiornarle per [usare la nuova API][track].
6. Pagine Web: se il progetto include pagine Web, sostituire gli script nelle sezioni <head>. In genere esiste solo una copia in una pagina master, ad esempio Views\\Shared\_Layout.cshtml. [Ottenere il nuovo script dal pannello Quick Start nella risorsa Application Insights in Azure][usage]. Se le pagine Web includono chiamate di telemetria nel corpo, ad esempio logEvent o logPage, [aggiornarle per usare la nuova API][track].
7. Monitoraggio server: se l'app è un servizio in esecuzione in IIS, disinstallare Microsoft Monitoring Agent dal server e quindi [installare Application Insights Status Monitor][redfield].
8. Test Web: se si usavano i test di disponibilità Web [ricrearli nel nuovo portale][availability] con i relativi avvisi.
9. Avvisi: impostare [avvisi sulle metriche][alerts] nel portale di Azure.
10. Contatori delle prestazioni: se si usavano i contatori delle prestazioni, è possibile scrivere codice personalizzato per campionare periodicamente i contatori e inviarli tramite [TrackMetric()][track].

## Se si dispone di un servizio Web Java...

1. Nel computer server disabilitare l'agente precedente rimuovendo i riferimenti all'agente APM dal file di avvio del servizio Web. In un server TomCat modificare il file Catalina.bat. In un server JBoss modificare il file Run.bat. 
2. Riavviare il servizio Web.
3. Nel portale di Microsoft Azure [aggiungere una nuova risorsa di Application Insights][java]. Nel computer di sviluppo aggiungere [SDK per Java][java] al progetto Web. È ora possibile [inviare dati di telemetria personalizzati][track] dal codice server.
4. Sostituire gli script nelle sezioni <head> delle pagine Web. Potrebbe esserci solo una copia in un'inclusione sul lato server. [Ottenere il nuovo script dal pannello Quick Start nella nuova risorsa di Application Insights in Azure][usage]. Se le pagine Web includono chiamate di telemetria nel corpo, ad esempio logEvent o logPage, [aggiornarle per usare la nuova API][track].



<!--Link references-->

[alerts]: app-insights-alerts.md
[availability]: app-insights-monitor-web-app-availability.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54-->