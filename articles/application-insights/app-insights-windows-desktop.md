<properties 
	pageTitle="Application Insights per servizi e app desktop di Windows" 
	description="Analizzare l'uso e le prestazioni dell'app desktop di Windows con Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="awills"/>

# Application Insights su servizi e app desktop di Windows

*Application Insights è disponibile in anteprima.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights consente di monitorare un'applicazione distribuita in base all'uso e alle prestazioni.

Il supporto per i servizi e le app desktop di Windows viene fornito dall'SDK di base di Application Insights. Tale SDK fornisce il supporto API completo per tutti i dati di telemetria ma non offre la funzionalità di raccolta automatica di questi dati.


## <a name="add"></a> Creare una risorsa Application Insights


1.  Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Come tipo di applicazione scegliere l'app di Windows Store. 

    ![Fare clic su Nuovo, Application Insights](./media/app-insights-windows-desktop/01-new.png)

    La scelta del tipo di applicazione imposta il contenuto del pannello Panoramica e le proprietà disponibili nell'[area di esplorazione delle metriche][metrics].

2.  Eseguire una copia della chiave di strumentazione.

    ![Fare clic su Proprietà, selezionare il tasto e premere Ctrl+C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Installare SDK nell'applicazione


1. In Visual Studio è possibile modificare i pacchetti NuGet del progetto di app desktop. ![Fare clic con il pulsante destro del mouse sul progetto e selezionare Gestisci pacchetti Nuget](./media/app-insights-windows-desktop/03-nuget.png)

2. Installare il pacchetto di Application Insights SDK.

    ![Cercare "Application Insights"](./media/app-insights-windows-desktop/04-core-nuget.png)

3. Modificare ApplicationInsights.config (che è stato aggiunto dall'installazione di NuGet). Inserire questo comando immediatamente prima del tag di chiusura:

    `<InstrumentationKey>*the key you copied*</InstrumentationKey>`

    In alternativa è possibile ottenere lo stesso effetto con il codice seguente:
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`


## <a name="telemetry"></a>Inserire le chiamate di telemetria

Creare un'`TelemetryClient` istanza e quindi [usarla per inviare dati di telemetria][api].

Usare `TelemetryClient.Flush()` per inviare messaggi prima di chiudere l'app L’SDK di base utilizza un buffer in memoria. Il metodo flush garantisce che questo buffer venga svuotato e che non si verifichi alcuna perdita di dati all'arresto del processo. (Non è consigliato per altri tipi di app. Gli SDK delle piattaforme implementano questo comportamento automaticamente).

Ad esempio, in un'applicazione Windows Form, è possibile scrivere:

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps
            }
            base.OnClosing(e);
        }

```

Usare l'[API di Application Insights][api] per inviare dati di telemetria. Nelle applicazioni Desktop di Windows, non vengono inviati automaticamente i dati di telemetria. In genere è necessario usare:

* TrackPageView(pageName) nel passare a moduli, pagine o tabelle
* TrackEvent(eventName) per altre azioni utente
* TrackMetric(name, value) in un'attività in background per inviare report periodici delle metriche non associate a eventi specifici.
* TrackTrace(logEvent) per la [registrazione diagnostica][diagnostic]
* TrackException(exception) in clausole catch

#### Inizializzatori di contesto

Per visualizzare i conteggi di utenti e sessioni, è possibile impostare i valori in ciascuna istanza di `TelemetryClient`. In alternativa, è possibile usare un inizializzatore di contesto per eseguire questa aggiunta per tutti i client:

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Eseguire il progetto

[Eseguire l'applicazione con F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) e usarla in modo da generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati inviati.

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>Visualizzare i dati monitorati

Tornare al pannello dell'applicazione nel portale di Azure.

I primi eventi verranno visualizzati in Ricerca diagnostica.

Se si prevedono più dati, fare clic su Aggiorna dopo pochi secondi.

Se si usa TrackMetric o il parametro delle misurazioni di TrackEvent, aprire l'[area di esplorazione delle metriche][metrics] e aprire il pannello Filtri, in cui verranno visualizzate le metriche.



## <a name="usage"></a>Passaggi successivi

[Tenere traccia dell'utilizzo dell'app][knowUsers]

[Acquisire ed eseguire ricerche nei log di diagnostica][diagnostic]

[Risoluzione dei problemi][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=62-->