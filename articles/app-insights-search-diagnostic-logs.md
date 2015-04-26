<properties 
	pageTitle="Ricerca nei registri di diagnostica" 
	description="Cercare i log generati con Trace, NLog o Log4Net." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>
 
# Ricerca diagnostica in Application Insights

Uno dei metodi di debugging più tradizionali prevede l'inserimento di righe di codice che generano un log di traccia. [Application Insights][start] è in grado di acquisire i log dei server Web e ne facilita la ricerca e il filtro. Se si usa già log4Net, NLog o System.Diagnostics.Trace, è possibile acquisire questi log con l'adattatore. In alternativa, è possibile usare i metodi TrackTrace ed TrackException integrati in Application Insights SDK.

I risultati della ricerca possono includere inoltre i normali eventi di visualizzazione delle pagine e di richiesta usati per creare i report di [utilizzo][usage] e [prestazioni][perf], insieme a eventuali [chiamate TrackEvent personalizzate][track] che sono state scritte.


2. [Installare un adattatore per il framework di registrazione](#capture)
+ [Inserire chiamate di log di diagnostica](#pepper)
+ [Eccezioni](#exceptions)
+ [Visualizzare i dati del registro](#view)
+ [Eseguire la ricerca di dati di log](#search)
+ [Risoluzione dei problemi](#questions)
+ [Passaggi successivi](#next)



## <a name="capture"></a> Installare un adattatore per il framework di registrazione

Se non si è ancora [installato Application Insights nel progetto][start], è possibile farlo ora.

Se verranno usate le chiamate Application Insights SDK Track*() integrate, non è necessario un adattatore ed è possibile [passare alla sezione successiva](#pepper).

Per eseguire la ricerca dei log generati con log4Net, NLog o System.Diagnostics.Trace, installare l'adattatore appropriato:

1. Se si intende usare log4Net o NLog, installarlo nel progetto. 
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**..
3. Selezionare Online > Tutti, quindi selezionare **Includi versione preliminare** e cercare "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter](./media/appinsights/appinsights-36nuget.png)

4. Selezionare il pacchetto appropriato tra:
  + Microsoft.ApplicationInsights.TraceListener (per acquisire le chiamate System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Il pacchetto NuGet installa gli assembly necessari e modifica inoltre web.config o app.config.

## <a name="pepper"></a>3. Inserire chiamate di log di diagnostica

Inserire chiamate di registrazione eventi usando il framework di registrazione preferito. 

Ad esempio, se si usa Application Insights SDK, si potrebbe inserire:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Se invece si usa System.Diagnostics.Trace:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se si preferisce log4net o NLog:

    logger.Warn("Slow response - database01");

Eseguire l'app in modalità di debug e distribuirla nel server Web.

### <a name="exceptions"></a>Eccezioni

Per inviare eccezioni al log:

JavaScript nel client

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C# nel server

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB nel server

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

I parametri delle proprietà e delle misurazioni sono facoltativi, ma sono utili per filtrare e aggiungere altre informazioni. Ad esempio, per un'app in grado di eseguire diversi giochi, è possibile trovare tutti i report di eccezione correlati a un gioco specifico. È possibile aggiungere qualsiasi numero di elementi a ogni dizionario.

## <a name="view"></a>4. Visualizzare i dati del registro


1. In Application Insights aprire la ricerca diagnostica.

    ![Open diagnostic search](./media/appinsights/appinsights-30openDiagnostics.png)
   
2. Impostare il filtro per i tipi di eventi da visualizzare.

    ![Open diagnostic search](./media/appinsights/appinsights-331filterTrace.png)


I tipi di eventi sono i seguenti:

* **Traccia**: ricerca di log di diagnostica che sono stati acquisiti dal server Web. Sono incluse le chiamate log4Net, NLog, System.Diagnostic.Trace e ApplicationInsights TrackTrace.
* **Richiesta**: ricerca di richieste HTTP ricevute dal componente server dell'app Web, incluse le pagine delle richieste, le richieste di dati, le immagini e così via. Gli eventi visualizzati sono i dati di telemetria inviati dall'SDK del server di Application Insights, che vengono usati per creare il report relativo ai conteggi delle richieste.
* **Visualizzazione Pagina**: ricerca di eventi di visualizzazione pagina. Questi eventi vengono inviati al client Web e sono usati per creare report di visualizzazioni di pagine. Se non viene visualizzato alcun contenuto, configurare il [monitoraggio del client Web][usage].
* **Evento personalizzato**: se sono state inserite chiamate a TrackEvent() e TrackMetric() per [monitorare l'utilizzo][track], è possibile cercarle qui.

Selezionare un registro eventi per visualizzarne i dettagli. 

![Open diagnostic search](./media/appinsights/appinsights-32detail.png)

È possibile usare stringhe normali, senza caratteri jolly, per filtrare i dati dei campi in un elemento.

I campi disponibili dipendono dal framework di registrazione e dai parametri usati nella chiamata.


## <a name="search"></a>5. Cercare i dati

Impostare un intervallo di tempo e cercare i termini. Le ricerche in un intervallo breve risultano più veloci. 

![Open diagnostic search](./media/appinsights/appinsights-311search.png)

Si noti che si stanno cercando termini, non sottostringhe. I termini sono stringhe alfanumeriche che includono segni di punteggiatura quali '.' e '_'. ad esempio:

<table>
  <tr><th>termine</th><th>non trova corrispondenza con</th><th>ma trova corrispondenza con</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>Nuovo ritardo</td><td>w r</td><td>nuovo<br/>ritardo<br/>n* AND r*</td></tr>
</table>

È possibile usare espressioni di ricerca quali le seguenti:

<table>
                    <tr>
                      <th>
                        <p>Query di esempio</p>
                      </th>
                      <th>
                        <p>Effetto</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">lento</span>
                        </p>
                      </td>
                      <td>
                        <p>Individuazione di tutti gli eventi nell'intervallo di date i cui campi includono il termine "lento".</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">database??</span>
                        </p>
                      </td>
                      <td>
                        <p>Corrispondenza con database01, databaseAB, ...</p>
                        <p>? non può essere usato all'inizio di un termine di ricerca.</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">database*</span>
                        </p>
                      </td>
                      <td>
                        <p>Corrispondenza con database, database01, databaseNNNN</p>
                        <p>* non può essere usato all'inizio del termine di ricerca.</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">mela AND banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Individuazione di eventi che contengono entrambi i termini. Usare "AND" in lettere maiuscole, non "and".</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">mela OR banana</span>
                        </p>
                        <p>
                          <span class="code">mela banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Individuazione di eventi che contengono uno dei termini.. Usare "OR", non "or".</p>
                        <p>Forma breve.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">mela NOT banana</span>
                        </p>
                        <p>
                          <span class="code">mela-banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Individuazione di eventi che contengono un termine ma non l'altro.</p>
                        <p>Forma breve.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>me* AND banana NOT (uva OR pera)</p>
                        <p>
                          <span class="code">me* AND banana-(uva pera)</span>
                        </p>
                      </td>
                      <td>
                        <p>Operatori logici e parentesi.</p>
                        <p>Forma più breve.</p>
                      </td>
                    </tr>
       <!-- -- fielded search feature not ready yet --
                    <tr>
                      <td>
                        <p>
                          <span class="code">message:slow</span>
                        </p>
                        <p>
                          <span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>Corrispondenza con il campo specificato. Per impostazione predefinita, la ricerca viene eseguita in tutti i campi. Per visualizzare i campi disponibili, selezionare un evento per verificarne i dettagli.</p>
                      </td>
                    </tr>
 -->
</table>


## <a name="questions"></a>Domande e risposte

### <a name="emptykey"></a>Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"

Risulta che l'utente abbia installato il pacchetto Nuget dell'adattatore di registrazione senza aver installato Application Insights.

In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. Verrà visualizzata una finestra di dialogo che invita ad accedere ad Azure e a creare una risorsa Application Insights o a riusarne una esistente. Il problema verrà in tal modo risolto.

### <a name="limits"></a>Quanti dati vengono conservati?

Fino a 500 eventi al secondo da ciascuna applicazione. Gli eventi vengono conservati per sette giorni.

### <a name="cani"></a>È possibile...?

- Impostare avvisi su eventi ed eccezioni
- Esportare log per ulteriori analisi
- Eseguire ricerche su proprietà specifiche

Inoltre, tutte queste funzionalità sono incluse nel backlog.

## <a name="add"></a>Passaggi successivi

* [Configurare i test per la disponibilità e la velocità di risposta][availability]
* [Risoluzione dei problemi][qna]





[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=46--> 

<!--HONumber=46--> 
