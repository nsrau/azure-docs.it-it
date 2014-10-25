<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Eseguire ricerche nei registri di diagnostica tramite Application Insights

È possibile acquisire e cercare dati di diagnostica da System.Diagnostics.Trace, NLog e Log4Net. Application Insights offre uno strumento efficiente e facile da usare per la raccolta e l'investigazione di eventi registrati da una o più origini, arricchendo così le funzionalità di monitoraggio dello stato dell'applicazione.

L'applicazione Web monitorata può essere ospitata in locale o in una macchina virtuale oppure può essere un sito Web di Microsoft Azure.

1.  [Aggiungere un adattatore di registrazione][Aggiungere un adattatore di registrazione]

-   [Configurare la raccolta diagnostica][Configurare la raccolta diagnostica]
-   [Inserire istruzioni per la registrazione, compilare e distribuire][Inserire istruzioni per la registrazione, compilare e distribuire]
-   [Visualizzare i dati del registro][Visualizzare i dati del registro]
-   [Eseguire ricerche nei dati][Eseguire ricerche nei dati]
-   [Passaggi successivi][Passaggi successivi]

## <a name="add"></a>1. Aggiungere un adattatore di registrazione

1.  Se questa operazione non è stata ancora eseguita, [aggiungere Application Insights al progetto del servizio Web][aggiungere Application Insights al progetto del servizio Web] in Visual Studio.

    Se si aggiunge Application Insights dopo l'aggiunta della registrazione al progetto, l'adattatore di registrazione sarà già stato installato e configurato. Sarà sufficiente [ridistribuire il progetto][Inserire istruzioni per la registrazione, compilare e distribuire] e [visualizzare i dati][Visualizzare i dati del registro].

2.  In Esplora soluzioni scegliere **Gestisci pacchetti NuGet** nel menu di scelta rapida del progetto.
3.  Selezionare Online \> Tutti, quindi selezionare **Includi versione preliminare** e cercare "Microsoft.ApplicationInsights".

    ![Ottenere la versione preliminare dell'adattatore appropriato][Ottenere la versione preliminare dell'adattatore appropriato]

4.  Selezionare la versione preliminare del pacchetto appropriato. I pacchetti disponibili sono:

-   Microsoft.ApplicationInsights.TraceListener
-   Microsoft.ApplicationInsights.NLogTarget
-   Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a>2. Configurare la raccolta diagnostica

### Per System.Diagnostics.Trace

In Web.config inserire il codice seguente nella sezione `<configuration>`:

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### Per NLog

In Nlog.config unire i frammenti di codice seguenti nelle sezioni `<extensions>`, `<targets>` e `<rules>`. Creare queste sezioni, se necessario.

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 

    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>

    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>

### Per Log4Net

In Web.config unire i frammenti di codice seguenti nelle sezioni `<configsections>` e `<log4net>`:

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>

    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>

## <a name="deploy"></a>3. Inserire istruzioni per la registrazione, compilare e distribuire

Inserire chiamate di registrazione eventi usando il framework di registrazione preferito. Se ad esempio si usa Log4Net, è possibile che siano effettuate chiamate analoghe alle seguenti:

    log.Warn("Slow response - database01");

Gli eventi registrati verranno inviati ad Application Insights nell'ambiente di sviluppo e di produzione.

## <a name="view"></a>4. Visualizzare i dati del registro

In Application Insights aprire la ricerca diagnostica.

![Aprire la ricerca diagnostica][Aprire la ricerca diagnostica]

Selezionare un registro eventi per visualizzarne i dettagli.

![Aprire la ricerca diagnostica][1]

I campi disponibili dipendono dal framework di registrazione e dai parametri usati nella chiamata.

È possibile usare stringhe normali, senza caratteri jolly, per filtrare i dati dei campi in un elemento.

## <a name="search"></a>5. Eseguire ricerche nei dati

Impostare un intervallo di tempo e cercare i termini. Le ricerche in un intervallo breve risultano più veloci.

![Aprire la ricerca diagnostica][2]

Si noti che si stanno cercando termini, non sottostringhe. I termini sono stringhe alfanumeriche che includono segni di punteggiatura quali '.' e '\_'. Ad esempio:

<table>
  <tr><th>Termine</th><th>NON corrisponde a</th><th>ma corrisponde a</th></tr>
  <tr><td>ControllerHome.Info</td><td>info<br/>home</td><td>h*info<br/>home*</td></tr>
  <tr><td>&Egrave;Locale</td><td>locale<br/>&egrave;<br/>*local</td><td>&egrave;l*<br/>&egrave;local<br/>&egrave;*l</td></tr>
  <tr><td>Nuovo ritardo</td><td>o r</td><td>nuovo<br/>ritardo<br/>n* AND r*</td></tr>
</table>

È possibile usare espressioni di ricerca quali le seguenti:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><p>Query di esempio</p></th>
<th align="left"><p>Effetto</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><span class="code">lento</span></p></td>
<td align="left"><p>Individuazione di tutti gli eventi nell'intervallo di dati i cui campi includono il termine &quot;lento&quot;.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">database??</span></p></td>
<td align="left"><p>Corrispondenza con database01, databaseAB, ...</p>
<p>? non può essere usato all'inizio del termine di ricerca.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">database*</span></p></td>
<td align="left"><p>Corrispondenza con database, database01, databaseNNNN</p>
<p>* non può essere usato all'inizio del termine di ricerca.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">mela AND banana</span></p></td>
<td align="left"><p>Individuazione di eventi che contengono entrambi i termini. Usare &quot;AND&quot; in lettere maiuscole, non &quot;and&quot;.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">mela OR banana</span></p>
<p><span class="code">mela banana</span></p></td>
<td align="left"><p>Individuazione degli eventi che contengono uno dei termini. Usare &quot;OR&quot;, non &quot;or&quot;.</p>
<p>Forma breve.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">mela NOT banana</span></p>
<p><span class="code">mela-banana</span></p></td>
<td align="left"><p>Individuazione di eventi che contengono un termine ma non l'altro.</p>
<p>Forma breve.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>me* AND banana NOT (uva OR pera)</p>
<p><span class="code">me* AND banana-(uva pera)</span></p></td>
<td align="left"><p>Operatori logici e parentesi.</p>
<p>Forma più breve.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">message:slow</span></p>
<p><span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span></p>
<p><span class="code">properties.logEventInfo.level:Error</span></p></td>
<td align="left"><p>Corrispondenza con il campo specificato. Per impostazione predefinita, la ricerca viene eseguita in tutti i campi. Per visualizzare i campi disponibili, selezionare un evento per verificarne i dettagli.</p></td>
</tr>
</tbody>
</table>

## <a name="add"></a>Passaggi successivi

-   [Aggiungere Application Insights al progetto][aggiungere Application Insights al progetto del servizio Web]
-   [Configurare i test per la disponibilità e la velocità di risposta][Configurare i test per la disponibilità e la velocità di risposta]
-   [Risoluzione dei problemi][Risoluzione dei problemi]

## Altre informazioni

-   [Application Insights][Application Insights]
-   [Aggiungere Application Insights al progetto][aggiungere Application Insights al progetto del servizio Web]
-   [Monitorare un server Web attivo][Monitorare un server Web attivo]
-   [Esaminare le metriche in Application Insights][Esaminare le metriche in Application Insights]
-   [Ricerche nei log di diagnostica][Ricerche nei log di diagnostica]
-   [Verifica della disponibilità mediante test Web][Configurare i test per la disponibilità e la velocità di risposta]
-   [Verifica dell'utilizzo con eventi e metriche][Verifica dell'utilizzo con eventi e metriche]
-   [Domande e risposte e risoluzione dei problemi][Risoluzione dei problemi]

<!--Link references-->

  [Aggiungere un adattatore di registrazione]: #add
  [Configurare la raccolta diagnostica]: #configure
  [Inserire istruzioni per la registrazione, compilare e distribuire]: #deploy
  [Visualizzare i dati del registro]: #view
  [Eseguire ricerche nei dati]: #search
  [Passaggi successivi]: #next
  [aggiungere Application Insights al progetto del servizio Web]: ../app-insights-monitor-application-health-usage/
  [Ottenere la versione preliminare dell'adattatore appropriato]: ./media/appinsights/appinsights-36nuget.png
  [Aprire la ricerca diagnostica]: ./media/appinsights/appinsights-30openDiagnostics.png
  [1]: ./media/appinsights/appinsights-32detail.png
  [2]: ./media/appinsights/appinsights-31search.png
  [Configurare i test per la disponibilità e la velocità di risposta]: ../app-insights-monitor-web-app-availability/
  [Risoluzione dei problemi]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Monitorare un server Web attivo]: ../app-insights-monitor-performance-live-website-now/
  [Esaminare le metriche in Application Insights]: ../app-insights-explore-metrics/
  [Ricerche nei log di diagnostica]: ../app-insights-search-diagnostic-logs/
  [Verifica dell'utilizzo con eventi e metriche]: ../app-insights-track-usage-custom-events-metrics/
