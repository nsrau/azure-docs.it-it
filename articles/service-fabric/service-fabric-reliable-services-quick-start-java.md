---
title: Guida introduttiva a Reliable Services | Documentazione Microsoft
description: "Introduzione alla creazione di un’applicazione dell’infrastruttura di servizi di Microsoft Azure con i servizi con e senza stato."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 224538560a50f9ab24b8c2746e99de1ab87f084b


---
# <a name="get-started-with-reliable-services"></a>Introduzione a Reliable Services
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-quick-start.md)
> * [Java su Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Questo articolo illustra le nozioni fondamentali di Reliable Services di Azure Service Fabric e le procedure per creare e distribuire una semplice applicazione Reliable Services scritta in Java.

## <a name="installation-and-setup"></a>Installazione e configurazione
Prima di iniziare, assicurarsi che nel computer sia configurato l'ambiente di sviluppo di Service Fabric.
Se è necessario configurarlo, andare alla [guida introduttiva per Mac](service-fabric-get-started-mac.md) o alla [guida introduttiva per Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concetti di base
Per iniziare a usare Reliable Services, è sufficiente comprendere solo alcuni concetti di base:

* **Tipo di servizio**: si tratta dell'implementazione del servizio. Viene definito dalla classe scritta che estende `StatelessService` e qualsiasi altro codice o dipendenze usate, insieme al nome e al numero della versione.
* **Istanza di servizio denominata**: per eseguire il servizio, si creano le istanze denominate del tipo di servizio, analogamente al modo in cui si creano le istanze di un oggetto di un tipo di classe. Le istanze del servizio sono, di fatto, istanze di oggetto della classe del servizio che si scrive. 
* **Host del servizio**: le istanze del servizio denominate che si creano devono essere eseguite all'interno di un host. L'host del servizio è semplicemente un processo in cui eseguire le istanze del servizio.
* **Registrazione del servizio**: la registrazione raccoglie tutti gli elementi. Il tipo di servizio deve essere registrato con il runtime di Service Fabric in un host del servizio per consentire a Service Fabric di creare istanze per l'esecuzione.  

## <a name="create-a-stateless-service"></a>Creare un servizio senza stato
Iniziare a creare una nuova applicazione di Service Fabric. Service Fabric SDK per Linux include un generatore Yeoman per la creazione dello scaffolding per un'applicazione di Service Fabric con un servizio senza stato. Iniziare eseguendo il comando Yeoman seguente:

```bash
$ yo azuresfjava
```

Seguire le istruzioni per creare un **servizio di Reliable Service senza stato**. Per questa esercitazione, denominare l'applicazione "HelloWorldApplication" e il servizio "HelloWorld". Il risultato includerà le directory per `HelloWorldApplication` e `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementare il servizio
Aprire **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Questa classe definisce il tipo di servizio e può eseguire qualsiasi codice. L'API del servizio fornisce due punti di ingresso per il codice:

* Un metodo del punto di ingresso aperto denominato `runAsync()`, che consente di iniziare a eseguire qualsiasi carico di lavoro, inclusi carichi di lavoro di calcolo con esecuzione prolungata.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

* Un punto di ingresso di comunicazione a cui è possibile collegare lo stack di comunicazione desiderato. In questo punto è possibile iniziare a ricevere richieste da utenti e da altri servizi.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Questa esercitazione si concentra sul metodo del punto di ingresso `runAsync()` . In questo punto è possibile iniziare immediatamente a eseguire il codice.

### <a name="runasync"></a>RunAsync
La piattaforma chiama questo metodo quando si inserisce un'istanza di un servizio pronta per l'esecuzione. Il ciclo di apertura e chiusura di un'istanza del servizio può verificarsi più volte per tutta la durata del servizio nel suo complesso. Questa situazione può verificarsi per vari motivi, tra cui:

* Il sistema sposta le istanze del servizio per il bilanciamento delle risorse.
* Si verificano errori nel codice.
* Viene aggiornato il sistema o l'applicazione.
* Si verifica un'interruzione nell'hardware sottostante.

Questa orchestrazione viene gestita da Service Fabric per assicurare l'elevata disponibilità e il corretto bilanciamento del servizio.

#### <a name="cancellation"></a>Annullamento
È fondamentale che il codice in `runAsync()` possa interrompere l'esecuzione su segnalazione di Service Fabric. L'elemento `CompletableFuture` restituito da `runAsync()` viene annullato quando Service Fabric richiede l'arresto dell'esecuzione del servizio. L'esempio seguente mostra come gestire un evento di annullamento: 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);

        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });

        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });

        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Registrazione del servizio
I tipi del servizio devono essere registrati nel runtime di Service Fabric. Il tipo di servizio è definito nel `ServiceManifest.xml` e nella classe di servizio che implementa `StatelessService`. La registrazione del servizio viene eseguita nel punto di ingresso principale del processo. In questo esempio il punto di ingresso principale del processo è `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione
Lo scaffolding Yeoman include uno script Gradle per compilare l'applicazione e script bash per distribuire e annullare la distribuzione dell'applicazione. Per eseguire l'applicazione, prima di tutto compilarla con Gradle:

```bash
$ gradle
```

Si otterrà un pacchetto dell'applicazione di Service Fabric che può essere distribuito tramite l'interfaccia della riga di comando di Azure Service Fabric. Lo script install.sh contiene i comandi dell'interfaccia della riga di comando di Azure necessari per distribuire il pacchetto dell'applicazione. Per la distribuzione è sufficiente eseguire lo script install.sh:

```bask
$ ./install.sh
```



<!--HONumber=Nov16_HO3-->


