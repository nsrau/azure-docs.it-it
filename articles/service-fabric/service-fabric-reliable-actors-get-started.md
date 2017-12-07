---
title: Creare il primo microservizio di Azure basato su attori in C# | Documentazione Microsoft
description: Questa esercitazione illustra la procedura per la creazione, il debug e la distribuzione di un semplice servizio basato su attore usando Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: vturecek
ms.openlocfilehash: ea17cf744779f390fe4b3f4049deb0c1ad985024
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="getting-started-with-reliable-actors"></a>Introduzione a Reliable Actors
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-actors-get-started.md)
> * [Java su Linux](service-fabric-reliable-actors-get-started-java.md)

Questo articolo illustra le nozioni fondamentali per creare ed eseguire il debug di una semplice applicazione Reliable Actors in Visual Studio. Per altre informazioni su Reliable Actors, vedere [Introduzione a Reliable Actors in Service Fabric](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi che nel computer sia configurato l'ambiente di sviluppo di Service Fabric, incluso Visual Studio. Per informazioni, vedere[Configurare l'ambiente di sviluppo](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Creare un nuovo progetto in Visual Studio

Avviare Visual Studio 2015 come amministratore e creare un nuovo progetto di **Applicazione di Service Fabric**:

![Strumenti di Service Fabric per Visual Studio - nuovo progetto][1]

Nella finestra di dialogo successiva scegliere **Servizio Actor** e immettere un nome per il servizio.

![Modelli di progetto di Service Fabric][5]

Il progetto creato avrà la struttura seguente:

![Struttura del progetto di Service Fabric][2]

## <a name="examine-the-solution"></a>Esaminare la soluzione

La soluzione contiene tre progetti:

* **Il progetto di applicazione (MyApplication)**, in cui sono inclusi tutti i servizi per la distribuzione. Contiene il file *ApplicationManifest.xml* e gli script di PowerShell per gestire l'applicazione.

* **Il progetto dell'interfaccia (HelloWorld.Interfaces)**, in cui è inclusa la definizione dell'interfaccia per l'attore. Le interfacce dell'attore possono essere definite in qualsiasi progetto con qualsiasi nome.  L'interfaccia definisce il contratto dell'attore che è condiviso dall'implementazione dell'attore e dai client che chiamano l'attore.  Poiché da essa dipendono i progetti client, in genere si consiglia di definirla in un assembly separato dall'implementazione dell'attore.

* **Il progetto di servizio Actor (HelloWorld)**, usato per definire il servizio di Service Fabric che ospiterà l'attore. Contiene l'implementazione dell'attore, *HellowWorld.cs*. L'implementazione di un attore è una classe che deriva dal tipo di base `Actor` e implementa le interfacce definite nel progetto *MyActor.Interfaces*. Una classe attore deve anche implementare un costruttore che accetta un'istanza `ActorService` e un `ActorId` e li passa alla classe `Actor` di base.
    
    Questo progetto contiene inoltre *Program.cs*, che registra le classi attore con il runtime di Service Fabric usando `ActorRuntime.RegisterActorAsync<T>()`. La classe `HelloWorld` è già registrata. Ulteriori implementazioni aggiunte al progetto devono essere registrate nel metodo `Main()`.

## <a name="customize-the-helloworld-actor"></a>Personalizzare l'attore HelloWorld

Il modello di progetto definisce alcuni metodi nell'interfaccia `IHelloWorld` e li implementa nell'implementazione dell'attore `HelloWorld`.  Sostituire questi metodi in modo che il servizio dell'attore restituisca una semplice stringa "Hello World".

Nel file *IHelloWorld.cs* del progetto *HelloWorld.Interfaces* sostituire la definizione dell'interfaccia nel modo seguente:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

Nel file **IHelloWorld.cs** del progetto **HelloWorld** sostituire l'intera definizione della classe nel modo seguente:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Premere **CTRL+MAIUSC+B** per compilare il progetto.

## <a name="add-a-client"></a>Aggiungere un client

Creare una semplice applicazione console per chiamare il servizio attore.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Aggiungi** > **Nuovo progetto...**.

2. Nei tipi di progetto **.NET Core** scegliere **App console (.NET Core)**.  Assegnare al progetto il nome *ActorClient*.
    
    ![Finestra di dialogo Aggiunta di un nuovo progetto][6]    
    
    > [!NOTE]
    > Un'applicazione console non è il tipo di app che in genere si usa come client in Service Fabric, ma è un esempio utile per il debug e il testing con il cluster Service Fabric locale.

3. L'applicazione console deve essere un'applicazione a 64 bit per garantire la compatibilità con il progetto interfaccia e le altre dipendenze.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ActorClient** e quindi scegliere **Proprietà**.  Nella scheda **Build**impostare **Piattaforma di destinazione** su **x64**.
    
    ![Proprietà di compilazione][8]

4. Il progetto client richiede il pacchetto NuGet di Reliable Actors.  Fare clic su **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.  Nella Console di Gestione pacchetti immettere il comando seguente:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Assicurarsi che il pacchetto NuGet e tutte le relative dipendenze siano installati nel progetto ActorClient.

5. Il progetto client richiede inoltre un riferimento al progetto interfaccia.  Nel progetto ActorClient fare clic con il pulsante destro del mouse su **Dipendenze** e quindi scegliere **Aggiungere riferimento...**.  Se l'opzione non è già selezionata, selezionare **Progetti > Soluzione** e quindi selezionare la casella di controllo accanto a **HelloWorld.Interfaces**.  Fare clic su **OK**.
    
    ![Finestra di dialogo Aggiunta riferimento][7]

6. Nel progetto ActorClient sostituire l'intero contenuto del file *Program.cs* con il codice seguente:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Esecuzione e debug

Premere **F5** per compilare, distribuire ed eseguire l'applicazione in locale nel cluster di sviluppo di Service Fabric.  Durante il processo di distribuzione è possibile visualizzare lo stato di avanzamento nella finestra **Output** .

![Finestra di output del debug di Service Fabric][3]

Quando l'output contiene il testo *L'applicazione è pronta*, è possibile testare il servizio usando l'applicazione ActorClient.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ActorClient** e quindi scegliere **Debug** > **Avvia nuova istanza**.  L'applicazione della riga di comando dovrebbe visualizzare l'output del servizio dell'attore.

![Output dell'applicazione][9]

> [!TIP]
> Il runtime Actors di Service Fabric emette alcuni [eventi e contatori delle prestazioni correlati ai metodi degli attori](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

## <a name="next-steps"></a>Passaggi successivi
Acquisire altre informazioni con [Descrizione del modo in cui Reliable Actors usa la piattaforma Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png