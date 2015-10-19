<properties
   pageTitle="Introduzione a Reliable Actors | Microsoft Azure"
   description="Questa esercitazione illustra i passaggi da seguire per creare, sottoporre al debug e distribuire un servizio HelloWorld canonico usando Service Fabric Reliable Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Reliable Actors: scenario dettagliato per la creazione di un servizio HelloWorld canonico
In questo articolo vengono illustrate le nozioni fondamentali di Service Fabric Reliable Actors e le procedure per creare, eseguire il debug e distribuire una semplice applicazione HelloWorld in Visual Studio.

## Installazione e configurazione
Prima di iniziare, assicurarsi che nel computer sia configurato l'ambiente di sviluppo di Service Fabric. Istruzioni dettagliate su come configurare l'ambiente di sviluppo sono disponibili [qui](service-fabric-get-started.md).

## Concetti di base
Per iniziare a usare Reliable Actors è sufficiente comprendere quattro concetti di base:

* **Servizio attore**. Reliable Actors viene fornito in pacchetti di servizi che possono essere distribuiti nell'infrastruttura Service Fabric. Un servizio può ospitare uno o più attori. Più avanti verranno fornite informazioni più dettagliate sui vantaggi e gli svantaggi legati alla scelta di uno o più attori per servizio. Per il momento si presuppone che sia necessario implementare un solo attore.
* **Interfaccia attore**. Questa interfaccia viene usata per definire l'interfaccia pubblica di un attore. Nella terminologia relativa al modello Actor, definisce il tipo di messaggi che l'attore è in grado di comprendere ed elaborare. L'interfaccia attore viene usata da altri attori o applicazioni client per inviare messaggi all'attore (in modo asincrono). Reliable Actors può implementare più interfacce, proprio come l'attore HelloWorld descritto più avanti può implementare l'interfaccia IHelloWorld ma anche un'interfaccia ILogging che definisce diversi messaggi o funzionalità.
* **Registrazione attore**. Nel servizio attore il tipo di attore deve essere registrato in modo che Service Fabric riconosca il nuovo tipo e possa usarlo per creare nuovi attori.
* **Classe ActorProxy**. La classe ActorProxy viene usata per eseguire il binding a un attore e richiamare i metodi esposti tramite le relative interfacce. La classe ActorProxy fornisce due funzionalità importanti:
	* Risoluzione dei nomi: la classe è in grado di rilevare l'attore nel cluster, ossia trovare in quale nodo del cluster è ospitato.
	* Gestione degli errori: la classe può ripetere le chiamate ai metodi e determinare nuovamente la posizione dell'attore, ad esempio dopo un errore che richiede lo spostamento dell'attore in un altro nodo del cluster.

## Creare un nuovo progetto in Visual Studio
Dopo aver installato Service Fabric Tools per Visual Studio, è possibile creare i tipi per un nuovo progetto. Questi tipi sono inclusi nella categoria Cloud della finestra di dialogo Nuovo progetto.


![Strumenti di Service Fabric per Visual Studio - nuovo progetto][1]

Nella finestra di dialogo successiva è possibile scegliere il tipo di progetto che si desidera creare.

![Modelli di progetto di Service Fabric][5]

Per il progetto HelloWorld si userà Service Fabric Actor Service.

Una volta creata la soluzione, verrà visualizzata la struttura seguente:

![Struttura del progetto di Service Fabric][2]

## Blocchi predefiniti di base di Reliable Actors

Una tipica soluzione Reliable Actors è costituita da 3 progetti:

* Il progetto dell'applicazione (HelloWorldApplication), in cui sono inclusi tutti i servizi per la distribuzione. Contiene il file ApplicationManifest.xml e gli script di PowerShell per gestire l'applicazione.

* Il progetto dell'interfaccia (HelloWorld.Interfaces), in cui è inclusa la definizione dell'interfaccia per l'attore. In questo progetto è possibile definire le interfacce che verranno usate dagli attori nella soluzione.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld.Interfaces
{
    public interface IHelloWorld : IActor
    {
        Task<string> SayHello(string greeting);
    }
}

```

* Il progetto del servizio (HelloWorld), usato per definire il servizio di Service Fabric che ospiterà l'attore. Contiene codice boilerplate, che nella maggior parte dei casi non richiede modifiche (ServiceHost.cs), e l'implementazione dell'attore. Quest'ultima implica l'implementazione di una classe che deriva da un tipo di base (Actor) e implementa le interfacce definite nel progetto .Interfaces.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using HelloWorld.Interfaces;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld
{
    public class HelloWorld : Actor, IHelloWorld
    {
        public Task<string> SayHello(string greeting)
        {
            return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
        }
    }
}

```

Il progetto Actor Service contiene il codice per creare un servizio di Service Fabric. Nella definizione del servizio sono registrati uno o più tipi di attore che possono essere usati per creare istanze di nuovi attori.

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(HelloWorld));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
}  

```

Se si inizia un nuovo progetto in Visual Studio ed è presente una sola definizione di attore, la registrazione viene inclusa per impostazione predefinita nel codice generato da Visual Studio. Se si definiscono altri attori nel servizio, è necessario aggiungere la registrazione attore, come indicato di seguito:

```csharp

fabricRuntime.RegisterActor(typeof(MyNewActor));


```

## Debug

In Service Fabric Tools per Visual Studio è supportato il debug nel computer locale. Per avviare una sessione di debug, premere F5. Visual Studio esegue la compilazione (se necessaria), crea i pacchetti, distribuisce l'applicazione nel cluster di Service Fabric locale e infine collega il debugger. L'esperienza è simile al debug di un'applicazione ASP.NET. Durante il processo di distribuzione è possibile visualizzare lo stato di avanzamento nella finestra di output.

![Finestra di output del debug del Service Fabric][3]


## Passaggi successivi

- [Introduzione a Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Documentazione di riferimento attori API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
- [Codice di esempio](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=Oct15_HO2-->