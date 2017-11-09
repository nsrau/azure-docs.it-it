---
title: Creare il primo microservizio di Azure affidabile in Java | Documentazione Microsoft
description: "Introduzione alla creazione di un’applicazione dell’infrastruttura di servizi di Microsoft Azure con i servizi con e senza stato."
services: service-fabric
documentationcenter: java
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 1ebabe4844732412e04bab8c277f7ebbc4a5737c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-reliable-services"></a>Introduzione a Reliable Services
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-quick-start.md)
> * [Java su Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Questo articolo illustra le nozioni fondamentali di Reliable Services di Azure Service Fabric e le procedure per creare e distribuire una semplice applicazione Reliable Services scritta in Java. Questo video di Microsoft Virtual Academy illustra anche come creare un servizio Reliable Services senza stato: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

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
Iniziare a creare un'applicazione di Service Fabric. Service Fabric SDK per Linux include un generatore Yeoman per la creazione dello scaffolding per un'applicazione di Service Fabric con un servizio senza stato. Iniziare eseguendo il comando Yeoman seguente:

```bash
$ yo azuresfjava
```

Seguire le istruzioni per creare un **servizio di Reliable Service senza stato**. Per questa esercitazione, denominare l'applicazione "HelloWorldApplication" e il servizio "HelloWorld". Il risultato include le directory per `HelloWorldApplication` e `HelloWorld`.

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
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
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

Questa esercitazione si concentra sul metodo `runAsync()` del punto di ingresso. In questo punto è possibile iniziare immediatamente a eseguire il codice.

### <a name="runasync"></a>RunAsync
La piattaforma chiama questo metodo quando si inserisce un'istanza di un servizio pronta per l'esecuzione. Per un servizio senza stato, la piattaforma chiama il metodo quando l'istanza del servizio viene aperta. Viene fornito un token di annullamento che determina quando è necessario chiudere l'istanza del servizio. In Service Fabric questo ciclo di apertura e chiusura di un'istanza del servizio può verificarsi più volte per tutta la durata del servizio nel suo complesso. Questa situazione può verificarsi per vari motivi, tra cui:

* Il sistema sposta le istanze del servizio per il bilanciamento delle risorse.
* Si verificano errori nel codice.
* Viene aggiornato il sistema o l'applicazione.
* Si verifica un'interruzione nell'hardware sottostante.

Questa orchestrazione viene gestita da Service Fabric per assicurare l'elevata disponibilità e il corretto bilanciamento del servizio.

`runAsync()` non deve bloccarsi in modo sincrono. L'implementazione di runAsync deve restituire un valore CompletableFuture per consentire al runtime di procedere. Se il carico di lavoro deve implementare un'attività a esecuzione prolungata, tale operazione deve essere completata entro il valore specificato da CompletableFuture.

#### <a name="cancellation"></a>Annullamento
L'annullamento del carico di lavoro è un'operazione cooperativa coordinata dal token di annullamento fornito. Il sistema attende la fine dell'attività (per esito positivo, annullamento o errore) prima di continuare. È importante rispettare il token di annullamento, completare le operazioni e chiudere `runAsync()` il più rapidamente possibile quando viene richiesto l'annullamento dal sistema. L'esempio seguente mostra come gestire un evento di annullamento:

```java
    @Override
    protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

        // TODO: Replace the following sample code with your own logic
        // or remove this runAsync override if it's not needed in your service.

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
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
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Lo scaffolding Yeoman include uno script Gradle per compilare l'applicazione e script Bash per distribuire ed eliminare l'applicazione. Per eseguire l'applicazione, prima di tutto compilarla con Gradle:

```bash
$ gradle
```

Questa operazione genera un pacchetto dell'applicazione Service Fabric che può essere distribuito tramite l'interfaccia della riga di comando di Service Fabric.

### <a name="deploy-with-service-fabric-cli"></a>Distribuire con l'interfaccia della riga di comando di Service Fabric

Lo script install.sh contiene i comandi dell'interfaccia della riga di comando di Service Fabric necessari per distribuire il pacchetto dell'applicazione. Eseguire lo script install.sh per distribuire l'applicazione.

```bash
$ ./install.sh
```

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)
