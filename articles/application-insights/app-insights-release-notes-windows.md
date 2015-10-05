<properties 
	pageTitle="Note sulla versione per Application Insights per Windows" 
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
	ms.date="09/18/2015" 
	ms.author="sergkanz"/>
 
# Note sulla versione per Application Insights SDK per Windows Phone e Store

[Application Insights SDK](app-insights-windows-get-started.md) invia dati di telemetria sull'app attiva ad [Application Insights](http://azure.microsoft.com/services/application-insights/), dove è possibile analizzarne l'utilizzo e le prestazioni.


#### Per installare l'SDK nell'applicazione

Vedere [Introduzione ad Application Insights per app di Windows Phone e Store](app-insights-windows-get-started.md).

#### Per eseguire l'aggiornamento all'SDK più recente 

* Eseguire una copia del file ApplicationInsights.config per mantenere eventuali personalizzazioni eseguite.
* In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
* Impostare il filtro in modo da visualizzare i pacchetti installati. 
* Selezionare i pacchetti di Application Insights installati e scegliere Aggiorna.
* Confrontare la vecchia e la nuova versione di ApplicationInsights.xml. Unire di nuovo eventuali personalizzazioni apportate alla versione precedente.
* Ricompilare la soluzione.

## Versione 1.2

### Windows App SDK

- Correggere un’eccezione FileNotFound che impediva l’invio delle telemetrie persistenti dopo la riapertura dell'applicazione.

### SDK di base

- La prima versione di SDK di Application Insights inviata da [github](http://github.com/microsoft/ApplicationInsights-dotnet)

## Versione 1.1

### SDK di base

- SDK ora rende disponibile un nuovo tipo di telemetria ```DependencyTelemetry``` che contiene informazioni sulla dipendenza chiamata dall'applicazione
- Il nuovo metodo ```TelemetryClient.TrackDependency``` consente di inviare informazioni sulla dipendenza chiamata dall'applicazione

## Versione 1.0.0

### Windows App SDK

- Nuova inizializzazione per le applicazioni Windows. La nuova classe `WindowsAppInitializer` con il metodo `InitializeAsync()` permette l'inizializzazione bootstrap della raccolta di SDK. Questa modifica offre un controllo più preciso e miglioramenti significativi alle prestazioni dell'inizializzazione dell'app rispetto alla tecnica precedente di ApplicationInsights.config.
- La modalità DeveloperMode non viene più impostata automaticamente. Per modificare il comportamento di DeveloperMode, è necessario specificarlo nel codice.
- Il pacchetto NuGet non inserisce più ApplicationInsights.config. È consigliabile usare il nuovo WindowsAppInitializer quando si aggiunge manualmente il pacchetto NuGet.
- ApplicationInsights.config legge solo `<InstrumentationKey>`. Tutte le altre impostazioni vengono ignorate a favore delle impostazioni di WindowsAppInitializer.
- Store Market verrà raccolto automaticamente dall'SDK.
- Molte correzioni di bug, miglioramenti alla stabilità e ottimizzazioni delle prestazioni.

### SDK di base

- Il file ApplicationInsights.config non è più necessario e non viene aggiunto dal pacchetto NuGet. La configurazione può essere specificata completamente nel codice.
- Il pacchetto NuGet non aggiungerà più file di destinazione alla soluzione. Verrà quindi rimossa l'impostazione automatica di DeveloperMode durante una compilazione di debug. DeveloperMode deve essere impostato manualmente nel codice.

## Versione 0.17

### Windows App SDK

- Windows App SDK supporta ora le app di Windows universali create nell'anteprima tecnica di Windows 10 e con VS 2015 RC.

### SDK di base

- Il valore predefinito per TelemetryClient comporta l'inizializzazione con InMemoryChannel.
- È stata aggiunta una nuova API, `TelemetryClient.Flush()`. Questo metodo Flush attiverà un caricamento bloccante immediato di tutti i dati di telemetria registrati in tale client. Ciò permette l'attivazione manuale del caricamento prima dell'arresto del processo.
- Il pacchetto NuGet ha aggiunto una destinazione .NET 4.5. Questa destinazione non presenta dipendenze esterne. Le dipendenze BCL ed EventSource sono state rimosse.

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

<!---HONumber=Sept15_HO4-->