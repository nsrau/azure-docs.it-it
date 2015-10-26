<properties
   pageTitle="Configurazione di Application Insights per l'applicazione di Service Fabric"
   description="Ricevere eventi di Service Fabric per l'applicazione in Application Insights."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/20/2015"
   ms.author="mattrow"/>

# Configurazione di Application Insights per l'applicazione di Service Fabric
 Questo articolo illustrerà in dettaglio l'abilitazione di Application Insights per l'applicazione di Service Fabric.

## Prerequisiti

Questo articolo presuppone che in Visual Studio sia già stata creata un'applicazione di Service Fabric. Per scoprire come, [fare clic qui](service-fabric-reliable-services-quick-start.md).

## Installazione del pacchetto NuGet
Come parte di Service Fabric SDK viene rilasciata una versione non definitiva del pacchetto NuGet Microsoft.ServiceFabric.Telemetry.ApplicationInsights. Tale pacchetto associa gli eventi EventSource di Service Fabric ad Application Insights per fornire la strumentazione automatizzata dell'applicazione di Service Fabric. Il pacchetto continuerà a essere aggiornato con nuovi eventi che verranno emessi automaticamente dall'applicazione.

Per installare il pacchetto, è possibile seguire questa procedura:

1. Aprire Gestione pacchetti NuGet per l'applicazione di Service Fabric. A tale scopo, fare clic con il pulsante destro del mouse sul progetto in Visual Studio e scegliere "Gestisci pacchetti NuGet".
2. Per ottenere l'elenco dei pacchetti inclusi in Service Fabric SDK, sarà necessario selezionare "Microsoft Azure Service Fabric" come origine. ![Gestione pacchetti NuGet VS2015](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. Selezionare il pacchetto Microsoft.ServiceFabric.Telemetry.ApplicationInsights a sinistra.
4. Fare clic su Installa per avviare il processo di installazione.
5. Leggere e accettare il contratto di licenza con l'utente finale.

## Abilitazione degli eventi di Service Fabric
Per ricevere automaticamente gli eventi di Service Fabric in Application Insights, sarà necessario abilitare l'apposito listener. A tale scopo, inserire la riga di codice seguente nell'applicazione.

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### Esempio per StatefulActor\\Program.cs:

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

Per informazioni sugli eventi emessi dal runtime di Reliable Actors [fare clic qui](service-fabric-reliable-actors-diagnostics.md), mentre per quelli emessi dal runtime di Reliable Services [fare clic qui](service-fabric-reliable-services-diagnostics.md).

Si noti che, per ottenere le chiamate ai metodi del runtime di Reliable Actors, è necessario usare EventLevel.Verbose, come mostrato negli esempi precedenti.

## Configurazione di Application Insights
L'applicazione di Service Fabric è associata alla risorsa di Application Insights mediante una chiave di strumentazione. Per informazioni su come ottenere tale chiave, vedere la [guida di Application Insight](../app-insights-create-new-resource.md#create-an-application-insights-resource). Quando si creano risorse, selezionare "Altro" come tipo di applicazione.

![Selezionare Altro come tipo di applicazione per Application Insights](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

Dopo aver ottenuto la chiave di strumentazione, è possibile inserirla nel file ApplicationInsights.config come mostrato di seguito:

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## Visualizzazione dei dati
È possibile [personalizzare il pannello di Application Insights](../app-insights-metrics-explorer.md) in base alle proprie esigenze. La maggior parte degli eventi di Service Fabric verrà visualizzata come "eventi personalizzati", mentre le chiamate RunAsync() ai servizi e le chiamate ai metodi di Fabric Actors verranno visualizzate come richieste. La modellazione di tali eventi come richieste consente di usare la dimensione "nome richiesta" e la metrica "durata richiesta" durante la creazione dei grafici. Continueranno a essere aggiunti nuovi grafici, metriche ed eventi che sarà possibile sfruttare in futuro.

## Passaggi successivi
Ottenere altre informazioni sull'uso di Application Insights per instrumentare le applicazioni di Service Fabric.

- [Introduzione ad Application Insights](../app-insights-get-started.md)
- [Informazioni su come creare metriche ed eventi personalizzati](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=Oct15_HO3-->