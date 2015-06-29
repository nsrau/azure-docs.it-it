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
* Selezionare **Microsoft.ApplicationInsights.Web** e scegliere **Aggiorna** \(verranno aggiornati anche tutti i pacchetti dipendenti\).
* Confrontare ApplicationInsights.config con la copia precedente. La maggior parte delle modifiche visualizzate è dovuta al fatto che alcuni moduli sono stati rimossi e altri sono stati resi parametrizzabili. Ripristinare eventuali personalizzazioni apportate al file precedente.
* Ricompilare la soluzione.

## Versione 0.17
- Rimozione dipendenza EventSource NuGet per le applicazioni del framework 4.5.
- I cookie di utente e sessione anonimi non verranno generati sul lato server. I moduli di telemetria ```WebSessionTrackingTelemetryModule``` e ```WebUserTrackingTelemetryModule``` non sono più supportati e sono stati rimossi dal file ApplicationInsights.config. Verranno rispettati i cookie provenienti da JavaScript SDK.
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

 

<!---HONumber=58_postMigration-->