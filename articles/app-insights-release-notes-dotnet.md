<properties 
	pageTitle="Note sulla versione per Application Insights" 
	description="Gli aggiornamenti più recenti." 
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
	ms.date="04/28/2015" 
	ms.author="sergkanz"/>
 
# Note sulla versione per Application Insights SDK per NET

[Uso di SDK per NET](app-insights-start-monitoring-app-health-usage.md)

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

<!---HONumber=58-->