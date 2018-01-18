---
title: Creare il primo microservizio di Azure affidabile in Java | Documentazione Microsoft
description: "Introduzione alla creazione di un’applicazione dell’infrastruttura di servizi di Microsoft Azure con i servizi con e senza stato."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: e885a482edcba48c18e425c54f4acc28ee650ddd
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
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

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

In questo esempio di servizio senza stato il conteggio è archiviato in una variabile locale. Tuttavia, poiché si tratta di un servizio senza stato, il valore archiviato esiste soltanto per il ciclo di vita corrente dell'istanza del servizio. Quando si posta o si riavvia il servizio, il valore viene perso.

## <a name="create-a-stateful-service"></a>Creare un servizio con stato
Service Fabric introduce un nuovo tipo di servizio con stato. Un servizio con stato può mantenere lo stato affidabile all'interno del servizio stesso, con percorso condiviso con il codice che lo usa. L'elevata disponibilità dello stato è assicurata da Service Fabric, senza dover rendere persistente lo stato mediante un archivio esterno.

Per convertire un valore del contatore da senza stato a elevata disponibilità e persistenza anche quando il servizio viene spostato o riavviato, è necessario un servizio con stato.

Nella stessa directory dell'applicazione HelloWorld è possibile aggiungere un nuovo servizio eseguendo il comando `yo azuresfjava:AddService`. Specificare il "Servizio Reliable con stato" per il framework e assegnare al servizio il nome "HelloWorldStateful". 

A questo punto l'applicazione ha due servizi: il servizio senza stato HelloWorld e il servizio con stato HelloWorldStateful.

Un servizio con stato ha gli stessi punti di ingresso di un servizio senza stato. La differenza principale è data dalla disponibilità di un provider di stato che può archiviare lo stato in modo affidabile. Service Fabric include un'implementazione del provider di stato denominata Reliable Collections, che permette di creare strutture di dati replicate tramite Reliable State Manager. Un servizio Reliable Services con stato usa questo provider di stato per impostazione predefinita.

Aprire HelloWorldStateful.java in **HelloWorldStateful -> src**, che contiene il metodo RunAsync seguente:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` funziona in modo simile sia nei servizi con stato che nei servizi senza stato. In un servizio con stato la piattaforma esegue tuttavia operazioni aggiuntive per conto dell'utente prima di eseguire `RunAsync()`. Tali operazioni possono includere la verifica che Reliable State Manager e Reliable Collections siano pronti per l'uso.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Reliable Collections e Reliable State Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) è un'implementazione di dizionario che permette di archiviare in modo affidabile lo stato nel servizio. Grazie a Service Fabric e a Reliable Hashmaps, è possibile archiviare i dati direttamente nel servizio, senza la necessità di un archivio esterno persistente. Reliable Hashmaps garantisce la disponibilità elevata dei dati. A tale scopo, Service Fabric crea e gestisce automaticamente più *repliche* del servizio. Offre anche un'API che consente di evitare le complessità di gestione di tali repliche e delle relative transizioni di stato.

Le raccolte Reliable Collections possono archiviare qualsiasi tipo Java, inclusi quelli personalizzati, con alcuni avvertimenti:

* Service Fabric garantisce la disponibilità elevata dello stato *replicando* lo stato nei nodi, mentre Reliable Hashmaps archivia i dati nel disco locale a ogni replica. Questo significa che tutti gli elementi archiviati in Reliable Hashmaps devono essere *serializzabili*. 
* Quando si esegue il commit di transazioni in Reliable Hashmaps, gli oggetti vengono replicati per assicurare disponibilità elevata. Gli oggetti archiviati in Reliable Hashmaps vengono conservati nella memoria locale del servizio. Ciò significa che è presente un riferimento locale all'oggetto.
  
   È importante non apportare modifiche alle istanze locali degli oggetti senza prima eseguire un'operazione di aggiornamento sulla raccolta Reliable Collections in una transazione. Le modifiche apportate alle istanze locali di oggetti, infatti, non vengono replicate automaticamente. È necessario inserire nuovamente l'oggetto nel dizionario oppure usare uno dei metodi di *aggiornamento* nel dizionario.

Reliable State Manager gestisce automaticamente Reliable Hashmaps. In qualunque momento e in qualsiasi posizione del servizio è possibile chiedere a Reliable State Manager una raccolta Reliable Collections indicandone il nome. Reliable State Manager restituirà un riferimento. Non si consiglia di salvare riferimenti alle istanze di raccolte Reliable Collections in proprietà o variabili membri di classe. Prestare particolare attenzione per assicurarsi che il riferimento sia sempre impostato su un'istanza durante il ciclo di vita del servizio. Reliable State Manager gestisce queste operazioni automaticamente ed è ottimizzato per le visite ripetute.


### <a name="transactional-and-asynchronous-operations"></a>Operazioni transazionali e asincrone
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Le operazioni su Reliable Hashmaps sono asincrone. Questo avviene perché le operazioni di scrittura sulle raccolte Reliable Collections eseguono operazioni di I/O per replicare e rendere persistenti i dati su disco.

Le operazioni su Reliable Hashmaps sono *transazionali* e consentono di mantenere lo stato coerente tra più Reliable Hashmaps e operazioni. Ad esempio, è possibile ottenere un elemento di lavoro da un oggetto Reliable Dictionary, eseguire un'operazione su tale elemento e salvare il risultato in un altro oggetto Reliable Hashmap, il tutto all'interno di una singola transazione. Questa viene considerata come un'operazione atomica e garantisce la riuscita o il rollback dell'intera operazione. Se si verifica un errore dopo aver rimosso l'elemento dalla coda ma prima di aver salvato il risultato, viene eseguito il rollback dell'intera transazione e l'elemento rimane nella coda per l'elaborazione.


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
