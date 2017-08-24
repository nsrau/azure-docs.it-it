---
title: Creare il primo microservizio di Azure basato su attori in Java | Documentazione Microsoft
description: Questa esercitazione illustra la procedura per la creazione, il debug e la distribuzione di un semplice servizio basato su attore usando Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d31dc8ab-9760-4619-a641-facb8324c759
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 450c60abeaaf96c7d82152d425265a6b6714f689
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="getting-started-with-reliable-actors"></a>Introduzione a Reliable Actors
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-actors-get-started.md)
> * [Java su Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

Questo articolo illustra le nozioni fondamentali di Azure Service Fabric Reliable Actors e le procedure per creare e distribuire una semplice applicazione Reliable Actors in Java.

## <a name="installation-and-setup"></a>Installazione e configurazione
Prima di iniziare, assicurarsi che nel computer sia configurato l'ambiente di sviluppo di Service Fabric.
Se è necessario configurarlo, andare alla [guida introduttiva per Mac](service-fabric-get-started-mac.md) o alla [guida introduttiva per Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concetti di base
Per iniziare a usare Reliable Actors, è sufficiente comprendere solo alcuni concetti di base:

* **Servizio attore**. Reliable Actors viene fornito in pacchetti di servizi Reliable Services che possono essere distribuiti nell'infrastruttura Service Fabric. Le istanze di Actors vengono attivate in un'istanza del servizio denominata.
* **Registrazione attore**. Come con Reliable Services, un servizio Reliable Actor deve essere registrato con il runtime di Service Fabric. In più, il tipo di attore deve essere registrato con il runtime di Actor.
* **Interfaccia attore**. Questa interfaccia viene usata per definire l'interfaccia pubblica fortemente tipizzata di un attore. In base alla terminologia modello di Reliable Actors, questa interfaccia definisce i tipi di messaggi che l'attore può comprendere ed eseguire. L'interfaccia attore viene usata da altri attori o applicazioni client per "inviare" messaggi all'attore (in modo asincrono). Reliable Actors può implementare più interfacce.
* **Classe ActorProxy**. La classe ActorProxy viene usata dalle applicazioni client per richiamare i metodi esposti tramite l'interfaccia attore. La classe ActorProxy fornisce due funzionalità importanti:
  
  * Risoluzione dei nomi: la classe è in grado di individuare l'attore nel cluster, ossia trovare il nodo del cluster in cui è ospitato.
  * Gestione degli errori: la classe può ripetere le chiamate al metodo e risolvere nuovamente la posizione dell'attore, ad esempio dopo un errore che richiede lo spostamento dell'attore in un altro nodo del cluster.

È opportuno citare le regole seguenti relative alle interfacce dell'attore:

* I metodi di interfaccia dell'attore non possono essere sottoposti a overload.
* I metodi di interfaccia dell'attore non accettano parametri facoltativi, out o ref.
* Non sono supportate interfacce generiche.

## <a name="create-an-actor-service"></a>Creare un servizio Actor
Per iniziare, creare una nuova applicazione di Service Fabric. Service Fabric SDK per Linux include un generatore Yeoman per la creazione dello scaffolding per un'applicazione di Service Fabric con un servizio senza stato. Iniziare eseguendo il comando Yeoman seguente:

```bash
$ yo azuresfjava
```

Seguire le istruzioni per creare un **servizio Actor senza stato**. Per questa esercitazione, denominare l'applicazione "HelloWorldActorApplication" e il servizio Actor "HelloWorldActor". Verrà creato lo scaffolding seguente:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Blocchi predefiniti di base di Reliable Actors
I concetti fondamentali descritti in precedenza si traducono nei blocchi predefiniti di base di un servizio Reliable Actor.

### <a name="actor-interface"></a>Interfaccia dell'attore
Questo blocco include la definizione dell'interfaccia per l'attore. Questa interfaccia definisce il contratto dell'attore che è condiviso dall'implementazione dell'attore e i client che chiamano l'attore, quindi è in genere opportuno definirlo in una posizione separata rispetto all'implementazione dell'attore e può essere condiviso da molti altri servizi o applicazioni client.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Servizio Actor
Questo blocco contiene l'implementazione dell'attore e il codice di registrazione dell'attore. La classe dell'attore implementa l'interfaccia dell'attore. Questa è la posizione in cui l'attore svolge il suo lavoro.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registrazione dell'attore
Il servizio Actor deve essere registrato con un tipo di servizio nel runtime di Service Fabric. Perché il servizio Actor possa eseguire le istanze degli attori, è necessario che anche il proprio tipo di attore sia registrato con il servizio Actor. Il metodo di registrazione `ActorRuntime` esegue questa attività per gli attori.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Client di prova
Questa è una semplice applicazione client di test che è possibile eseguire separatamente dall'applicazione di Service Fabric per testare il servizio attore. Questo è un esempio della posizione in cui è possibile usare ActorProxy per attivare e comunicare con le istanze dell'attore. Non viene distribuito con il servizio.

### <a name="the-application"></a>Applicazione
Infine, l'applicazione che include il servizio dell'attore e qualsiasi altro servizio aggiunto in futuro per la distribuzione. Contiene il file *ApplicationManifest.xml* e segnaposto per il pacchetto del servizio dell'attore.

## <a name="run-the-application"></a>Eseguire l'applicazione

Lo scaffolding Yeoman include uno script Gradle per compilare l'applicazione e script Bash per distribuire ed eliminare l'applicazione. Per distribuire l'applicazione, prima di tutto compilarla con Gradle:

```bash
$ gradle
```

Si otterrà un pacchetto dell'applicazione di Service Fabric che può essere distribuito tramite gli strumenti dell'interfaccia della riga di comando di Service Fabric.

### <a name="deploy-with-xplat-cli"></a>Eseguire la distribuzione con l'interfaccia della riga di comando di XPlat

Se si usa l'interfaccia della riga di comando di XPlat, lo script install.sh contiene i comandi dell'interfaccia della riga di comando di Azure necessari per distribuire il pacchetto dell'applicazione. Eseguire lo script install.sh per distribuire l'applicazione.

```bash
$ ./install.sh
```

### <a name="deploy-with-azure-cli-20"></a>Eseguire la distribuzione con l'interfaccia della riga di comando di Azure 2.0

Se si usa l'interfaccia della riga di comando di Azure 2.0, vedere la documentazione di riferimento sulla gestione di un [ciclo di vita dell'applicazione tramite l'interfaccia della riga di comando di Azure 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md).

## <a name="related-articles"></a>Articoli correlati

* [Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introduzione all'interfaccia della riga di comando di XPlat per Service Fabric](service-fabric-azure-cli.md)

