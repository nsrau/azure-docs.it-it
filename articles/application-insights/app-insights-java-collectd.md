---
title: 'Monitorare le prestazioni delle app Web in Linux: Azure | Documentazione Microsoft'
description: Monitoraggio esteso delle prestazioni delle applicazioni del sito Web Java con il plug-in CollectD per Application Insights.
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 5a39d4ec9bbf1c7672267c7e89c957ebc49f1f3a
ms.contentlocale: it-it
ms.lasthandoff: 04/13/2017

---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: metriche delle prestazioni Linux in Application Insights


Per esplorare le metriche delle prestazioni del sistema Linux in [Application Insights](app-insights-overview.md), installare [collectd](http://collectd.org/) insieme al rispettivo plug-in di Application Insights. Questa soluzione open source raccoglie diverse che relative al sistema e alla rete.

In genere, si usa collectd se è già stato [instrumentato il servizio Web Java con Application Insights][java]. Fornisce una maggiore quantità di dati che consentono di migliorare le prestazioni dell'app o diagnosticare i problemi. 

![Grafici di esempio](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>Ottenere la chiave di strumentazione
Nel [Portale di Microsoft Azure](https://portal.azure.com) aprire la risorsa [Application Insights](app-insights-overview.md) in cui devono essere visualizzati i dati. In alternativa, [creare una nuova risorsa](app-insights-create-new-resource.md).

Copiare la chiave di strumentazione, che identifica la risorsa.

![Visualizzare tutto, aprire la risorsa e quindi nell'elenco a discesa Informazioni di base selezionare e copiare la chiave di strumentazione](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Installare collectd e il plug-in
Nei computer server Linux:

1. Installare [collectd](http://collectd.org/) versione 5.4.0 o successive.
2. Scaricare il [plug-in di scrittura collectd di Application Insights](https://aka.ms/aijavasdk). Annotare il numero di versione.
3. Copiare il file JAR del plug-in in `/usr/share/collectd/java`.
4. Modificare `/etc/collectd/collectd.conf`:
   * Assicurarsi che il [plug-in Java](https://collectd.org/wiki/index.php/Plugin:Java) sia abilitato.
   * Aggiornare JVMArg per java.class.path in modo da includere il file JAR seguente. Aggiornare il numero di versione in modo che corrisponda a quello scaricato:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Aggiungere questo frammento di codice usando la chiave di strumentazione dalla risorsa:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Di seguito è riportata una parte di un file di configurazione di esempio:

```XML

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
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Configurare altri [plug-in collectd](https://collectd.org/wiki/index.php/Table_of_Plugins), che possono raccogliere diversi dati da origini diverse.

Riavviare collectd, come indicato nel rispettivo [manuale](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Visualizzare i dati in Application Insights
Nella risorsa di Application Insights aprire [Esplora metriche e aggiungere grafici][metrics], selezionando le metriche da visualizzare dalla categoria personalizzata.

![](./media/app-insights-java-collectd/result.png)

Per impostazione predefinita, le metriche vengono aggregate per tutti i computer host da cui vengono raccolte le metriche. Per visualizzare le metriche dei singoli host, nel pannello di dettagli del grafico attivare l'opzione Raggruppamento e quindi scegliere di eseguire il raggruppamento in base a CollectD-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Per escludere il caricamento di statistiche specifiche
Per impostazione predefinita, il plug-in di Application Insights invia tutti i dati raccolti da tutti i plug-in di tipo 'read' di collectd. 

Per escludere dati da plug-in specifici oppure origini dati specifiche:

* Modificare il file di configurazione. 
* In `<Plugin ApplicationInsightsWriter>`aggiungere righe di direttive analoghe alle seguenti:

| Direttiva | Effetto |
| --- | --- |
| `Exclude disk` |Esclusione di tutti i dati raccolti dal plug-in `disk` . |
| `Exclude disk:read,write` |Esclusione delle origini denominate `read` e `write` dal plug-in `disk`. |

Separare le direttive con un valore NewLine.

## <a name="problems"></a>Problemi?
*I dati non vengono visualizzati nel portale*

* Aprire [Cerca][diagnostic] per verificare se gli eventi non elaborati sono stati ricevuti. In alcuni casi necessitano di più tempo per la visualizzazione in Esplora metriche.
* Potrebbe essere necessario [impostare le eccezioni del firewall per i dati in uscita](app-insights-ip-addresses.md)
* Abilitare la traccia nel plug-in di Application Insights. Aggiungere questa riga in `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Aprire un terminale e avviare collectd in modalità dettagliata, per visualizzare eventuali problemi segnalati:
  * `sudo collectd -f`

## <a name="known-issue"></a>Problema noto

Il plug-in di scrittura di Application Insights non è compatibile con alcuni plug-in di lettura. Alcuni plug-in a volte inviano un errore "non un numero" quando il plug-in di Application Insights prevede un numero a virgola mobile.

Sintomo: Il log di collectd visualizza errori che includono "AI:... SyntaxError: N token non previsto".

Soluzione alternativa: escludere i dati raccolti dal plug-in di scrittura che causa il problema. 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md



