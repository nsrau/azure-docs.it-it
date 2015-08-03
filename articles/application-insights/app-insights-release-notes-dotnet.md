<properties 
	pageTitle="Note sulla versione per Application Insights" 
	description="Gli aggiornamenti più recenti." 
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
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# Note sulla versione per Application Insights SDK per NET

[Application Insights SDK per .NET](app-insights-start-monitoring-app-health-usage.md) invia dati di telemetria sull'app attiva ad [Application Insights](http://azure.microsoft.com/services/application-insights/), dove è possibile analizzarne l'utilizzo e le prestazioni.


#### Per installare l'SDK nell'applicazione

Vedere [Introduzione ad Application Insights per .NET](app-insights-start-monitoring-app-health-usage.md).

#### Per eseguire l'aggiornamento all'SDK più recente 

* Dopo l'aggiornamento, è necessario unire nuovamente le personalizzazioni apportate al file ApplicationInsights.config. Se non si è certi che sia stato personalizzato, creare un nuovo progetto, aggiungervi Application Insights e confrontare il file.config con quello nel nuovo progetto. Prendere nota di eventuali differenze.
* In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
* Impostare il filtro in modo da visualizzare i pacchetti installati. 
* Selezionare **Microsoft.ApplicationInsights.Web** e scegliere **Aggiorna** (verranno aggiornati anche tutti i pacchetti dipendenti).
* Confrontare ApplicationInsights.config con la copia precedente. La maggior parte delle modifiche visualizzate è dovuta al fatto che alcuni moduli sono stati rimossi e altri sono stati resi parametrizzabili. Ripristinare eventuali personalizzazioni apportate al file precedente.
* Ricompilare la soluzione.

## Versione 1.1

- È stato aggiunto il nuovo tipo di telemetria `DependencyTelemetry`, che può essere usato per inviare informazioni sulle chiamate di dipendenza dall'applicazione, ad esempio chiamate di tipo SQL, HTTP e così via.
- È stato aggiunto un nuovo metodo di overload `TelemetryClient.TrackDependency` che permette di inviare informazioni sulle chiamate di dipendenza.
- È stata corretta l'eccezione NullReferenceException generata dal modulo di diagnostica quando si usa TelemetryConfiguration.CreateDefault.

## Versione 1.0

- Gli inizializzatori e i moduli di telemetria sono stati spostati da spazi dei nomi secondari separati allo spazio dei nomi radice `Microsoft.ApplicationInsights.Extensibility.Web`.
- Il prefisso "Web" è stato rimosso dai nomi degli inizializzatori e dei moduli di telemetria, poiché è già incluso nel nome dello spazio dei nomi `Microsoft.ApplicationInsights.Extensibility.Web`.
- `DeviceContextInitializer` è stato spostato dall'assembly `Microsoft.ApplicationInsights` all'assembly `Microsoft.ApplicationInsights.Extensibility.Web` ed è stato convertito in un `ITelemetryInitializer`.
- Cambiare i nomi dello spazio dei nomi e dell'assembly da `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` a `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` per coerenza con il nome del pacchetto NuGet.
- Rinominare `RemoteDependencyModule` in `DependencyTrackingTelemetryModule`.
- Rinominare `CustomPerformanceCounterCollectionRequest` in `PerformanceCounterCollectionRequest`.

## Versione 0.17
- Rimozione dipendenza EventSource NuGet per le applicazioni del framework 4.5.
- I cookie di utente e sessione anonimi non verranno generati sul lato server. Per implementare il rilevamento di utenti e sessioni per le app Web, è ora necessaria la strumentazione con JS SDK. I cookie da JavaScript SDK verranno comunque rispettati. I moduli di telemetria ```WebSessionTrackingTelemetryModule``` e ```WebUserTrackingTelemetryModule``` non sono più supportati e sono stati rimossi dal file ApplicationInsights.config. Si noti che questa modifica potrebbe provocare una rideterminazione significativa dei conteggi relativi a utenti e sessioni, poiché vengono ora contate solo le sessioni originate dagli utenti.
- Il valore OSVersion non viene più popolato dall'SDK per impostazione predefinita. Se vuoti, i valori per OS e OSVersion vengono calcolati dalla pipeline di Application Insights, in base all'agente utente. 
- Il canale di persistenza ottimizzato per scenari di carico elevato viene usato per l'SDK Web. Problema "spirale della morte" risolto. La "spirale della morte" è una condizione in cui il picco nel numero di elementi di telemetria, che supera in misura significativa la soglia di limitazione sull'endpoint, conduce a nuovi tentativi dopo un determinato periodo di tempo e sarà limitato nuovamente durante i nuovi tentativi.
- La modalità di sviluppo è ottimizzata per la produzione. Se attivata per errore non causa un sovraccarico importante come in precedenza nel provare a dare un output di informazioni aggiuntive.
- Per impostazione predefinita, la modalità di sviluppo verrà attivata solo quando l'applicazione è nel debugger. È possibile eseguirne l'override usando la proprietà ```DeveloperMode``` dell'interfaccia ```ITelemetryChannel```.

## Versione 0.16 

Anteprima del 28/04/2015

- L'SDK supporta ora la piattaforma di destinazione DNX per consentire il monitoraggio delle applicazioni per [.NET Core Framework](http://www.dotnetfoundation.org/NETCore5).
- Le istanze di ```TelemetryClient``` non memorizzano più nella cache la chiave di strumentazione. Ora, se la chiave di strumentazione non è stata impostata in modo esplicito in ```TelemetryClient```, ```InstrumentationKey``` restituirà Null. In questo modo viene risolto un problema per cui quando si imposta ```TelemetryConfiguration.Active.InstrumentationKey``` dopo che sono già stati raccolti alcuni dati di telemetria, i moduli di telemetria come agente di raccolta dipendenze, raccolta dati delle richieste Web e agente di raccolta contatori delle prestazioni useranno una nuova chiave di strumentazione.

## Versione 0.15

- La nuova proprietà ```Operation.SyntheticSource``` è ora disponibile in ```TelemetryContext```. Ora è possibile contrassegnare gli elementi di telemetria come "traffico utente non reale" e specificare il modo in cui il traffico è stato generato. Ad esempio impostando questa proprietà è possibile distinguere il traffico che deriva dall'automazione dei test da quello che deriva dal test di carico.
- La logica di canale è stata spostata in un altro pacchetto NuGet denominato Microsoft.ApplicationInsights.PersistenceChannel. Il canale predefinito è ora denominato InMemoryChannel
- Il nuovo metodo ```TelemetryClient.Flush``` consente di scaricare gli elementi di telemetria dal buffer in modo sincrono

## Versione 0.13

Per le versioni precedenti non sono disponibili le note sulla versione.

 

<!---HONumber=July15_HO4-->