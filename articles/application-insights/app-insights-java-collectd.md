<properties 
	pageTitle="collectd: statistiche sulle prestazioni per Java in Unix in Application Insights" 
	description="Monitoraggio esteso del sito Web Java con il plug-in CollectD per Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# collectd: metriche delle prestazioni Unix in Application Insights

*Application Insights è disponibile in Anteprima.*

Per esplorare le metriche delle prestazioni del sistema Unix in [Application Insights](app-insights-overview.md), installare [collectd](http://collectd.org/), insieme al rispettivo plug-in di Application Insights. Questa soluzione open source raccoglie alcune statistiche relative al sistema e alla rete.

In genere, si usa se [il servizio Web Java è già stato instrumentato con Application Insights][java], in modo che siano disponibili più dati per semplificare il miglioramento delle prestazioni dell'app o la diagnosi dei problemi.

![Grafici di esempio](./media/app-insights-java-collectd/sample.png)

## Ottenere la chiave di strumentazione

Nel [portale di Microsoft Azure](http://portal.azure.com) aprire la risorsa [Application Insights](start) in cui devono essere visualizzati i dati. In alternativa, [creare una nuova risorsa](app-insights-create-new-resource.md).

Copiare la chiave di strumentazione, che identifica la risorsa.

![Sfogliare tutto, aprire la risorsa, quindi nell'elenco a discesa Informazioni di base selezionare e copiare la chiave di strumentazione.](./media/app-insights-java-collectd/02-props.png)



## Installare collectd e il plug-in

Nei computer server Unix:

1. Installare [collectd](http://collectd.org/) versione 5.4.0 o successive.
2. Scaricare il [plug-in di scrittura collectd di Application Insights](http://go.microsoft.com/fwlink/?LinkId=618634). Annotare il numero di versione.
3. Copiare il file JAR del plug-in in `/usr/share/collectd/java`.
3. Modificare `/etc/collectd/collectd.conf`:
 * Assicurarsi che il [plug-in Java](https://collectd.org/wiki/index.php/Plugin:Java) sia abilitato.
 * Aggiornare JVMArg per java.class.path in modo da includere il file JAR seguente. Aggiornare il numero di versione in modo che corrisponda a quello scaricato:
  * `/usr/share/collectd/java/applicationinsights-collectd-0.9.4.jar`
 * Aggiungere questo frammento di codice usando la chiave di strumentazione dalla risorsa:

```

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Di seguito è riportata una parte di un file di configurazione di esempio:

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-0.9.4.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
                
      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
. ...

Configurare altri [plug-in collectd](https://collectd.org/wiki/index.php/Table_of_Plugins), che permettono di raccogliere svariati dati da origini diverse.

Riavviare collectd, come indicato nel rispettivo [manuale](https://collectd.org/wiki/index.php/First_steps).

## Visualizzare i dati in Application Insights

Nella risorsa di Application Insights aprire [Esplora metriche e aggiungere grafici][metrics], selezionando le metriche da visualizzare dalla categoria personalizzata.

![](./media/app-insights-java-collectd/result.png)

Per impostazione predefinita, le metriche vengono aggregate per tutti i computer host da cui vengono raccolte le metriche. Per visualizzare le metriche dei singoli host, nel pannello di dettagli del grafico attivare l'opzione Raggruppamento e quindi scegliere di eseguire il raggruppamento in base a CollectD-Host.


## Per escludere il caricamento di statistiche specifiche

Per impostazione predefinita, il plug-in di Application Insights invierà tutti i dati raccolti da tutti i plug-in di tipo 'read' di collectd.

Per escludere dati da plug-in specifici oppure origini dati specifiche:

* Modificare il file di configurazione. 
* In `<Plugin ApplicationInsightsWriter>` aggiungere righe di direttive analoghe alle seguenti:

Direttiva | Effetto
---|---
`Exclude disk` | Esclusione di tutti i dati raccolti dal plug-in `disk`.
`Exclude disk:read,write` | Esclusione delle origini denominate `read` e `write` dal plug-in `disk`.

Separare le direttive con un valore NewLine.


## Problemi?

*I dati non vengono visualizzati nel portale*

* Aprire [Cerca][diagnostic] per verificare se gli eventi non elaborati sono stati ricevuti. In alcuni casi necessitano di più tempo per la visualizzazione in Esplora metriche.
* Abilitare la traccia nel plug-in di Application Insights. Aggiungere questa riga in `<Plugin ApplicationInsightsWriter>`:
 *  `SDKLogger true`
* Aprire un terminale e avviare collectd in modalità dettagliata, per visualizzare eventuali problemi segnalati:
 * `sudo collectd -f`




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO4-->