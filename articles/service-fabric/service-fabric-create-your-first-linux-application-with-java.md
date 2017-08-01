---
title: Creare un'applicazione Java Reliable Actors di Azure Service Fabric in Linux | Microsoft Docs
description: Informazioni su come creare e distribuire un'applicazione Java Reliable Actors di Service Fabric in cinque minuti.
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 254f38a600ea4026120bc411368eeb01310e56b2
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Creare la prima applicazione Java Reliable Actors di Service Fabric in Linux

Questa guida introduttiva illustra come creare la prima applicazione Java di Azure Service Fabric in un ambiente di sviluppo Linux in pochi minuti.  Al termine, si avrà una semplice applicazione Java con un singolo servizio in esecuzione nel cluster di sviluppo locale.  

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, installare Service Fabric SDK e l'interfaccia della riga di comando di Azure e configurare un cluster di sviluppo nell'[ambiente di sviluppo Linux](service-fabric-get-started-linux.md). Se si usa Mac OS X, è possibile [configurare un ambiente di sviluppo Linux in una macchina virtuale usando Vagrant](service-fabric-get-started-mac.md).

Sarà anche possibile configurare l'[interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md) (consigliato) o l'[interfaccia della riga di comando di XPlat](service-fabric-azure-cli.md) per distribuire l'applicazione.

## <a name="create-the-application"></a>Creazione dell'applicazione
Un'applicazione di Service Fabric contiene uno o più servizi, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. Service Fabric SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che semplifica la creazione del primo servizio e l'aggiunta dei successivi.  È anche possibile creare, compilare e distribuire applicazioni Java di Service Fabric usando un plug-in per Eclipse. Vedere [Creare e distribuire la prima applicazione Java usando Eclipse](service-fabric-get-started-eclipse.md). Per questa guida introduttiva, usare Yeoman per creare un'applicazione con un unico servizio per archiviare e ottenere un valore del contatore.

1. In un terminale digitare ``yo azuresfjava``.
2. Assegnare un nome all'applicazione. 
3. Scegliere il tipo del primo servizio e assegnargli un nome. Per questa esercitazione, scegliere un servizio Reliable Actor. Per altre informazioni sugli altri tipi di servizi, vedere [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md).
   ![Generatore Yeoman di Service Fabric per Java][sf-yeoman]

## <a name="build-the-application"></a>Compilare l'applicazione.
I modelli Yeoman di Service Fabric includono uno script di compilazione per [Gradle](https://gradle.org/), che può essere usato per compilare l'applicazione dal terminale. Per compilare l'applicazione e creare il pacchetto, eseguire questo comando:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Distribuire l'applicazione
Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale.

### <a name="using-xplat-cli"></a>Uso dell'interfaccia della riga di comando di XPlat

1. Connettersi al cluster locale di Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```

2. Eseguire lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    ./install.sh
    ```

### <a name="using-azure-cli-20"></a>Tramite l'interfaccia della riga di comando di Azure 2.0

La distribuzione dell'applicazione compilata è uguale a quella di qualsiasi altra applicazione di Service Fabric. Per istruzioni dettagliate, vedere la documentazione sulla [gestione di un'applicazione di Service Fabric con l'interfaccia della riga di comando di Azure](service-fabric-application-lifecycle-azure-cli-2-0.md).

I parametri per questi comandi si trovano nei manifesti generati nel pacchetto dell'applicazione.

Dopo la distribuzione dell'applicazione, aprire un browser e passare a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) all'indirizzo [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Espandere quindi il nodo **Applicazioni**, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.

## <a name="start-the-test-client-and-perform-a-failover"></a>Avviare il client di test ed eseguire un failover
Gli attori non eseguono alcuna operazione in modo indipendente, ma richiedono un altro servizio o client per l'invio dei messaggi. Il modello Actor include un semplice script di test che è possibile usare per interagire con il servizio Actor.

1. Eseguire lo script tramite l'utilità delle espressioni di controllo per visualizzare l'output del servizio Actor.  Lo script di test chiama il metodo `setCountAsync()` nell'attore per incrementare un contatore, chiama il metodo `getCountAsync()` nell'attore per ottenere il nuovo valore del contatore e visualizza tale valore nella console.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. In Service Fabric Explorer individuare il nodo che ospita la replica primaria del servizio Actor. Nello screenshot seguente si tratta del nodo 3. La replica di servizi primaria gestisce le operazioni di lettura e scrittura.  Le modifiche allo stato del servizio vengono quindi replicate nelle repliche secondarie, in esecuzione nei nodi 0 e 1 nello screenshot seguente.

    ![Ricerca della replica primaria in Service Fabric Explorer][sfx-primary]

3. In **Nodi** fare clic sul nodo trovato nel passaggio precedente e quindi scegliere **Disattiva (riavvio)** dal menu Azioni. Questa azione consente di riavviare il nodo che esegue la replica di servizi primaria e di forzare il failover in una delle repliche secondarie in esecuzione in un altro nodo.  Questa replica secondaria viene alzata di livello a primaria, un'altra replica secondaria viene creata in un nodo diverso e la replica primaria inizia a eseguire operazioni di lettura/scrittura. Durante il riavvio del nodo, osservare l'output dal client di test e notare che l'incremento del contatore prosegue nonostante il failover.

## <a name="remove-the-application"></a>Rimuovere l'applicazione
Usare lo script di disinstallazione incluso nel modello per eliminare l'istanza dell'applicazione, annullare la registrazione del pacchetto dell'applicazione e rimuovere tale pacchetto dall'archivio immagini del cluster.

```bash
./uninstall.sh
```

In Service Fabric Explorer si noterà che l'applicazione e il tipo di applicazione non sono più visualizzati nel nodo **Applicazioni**.

## <a name="next-steps"></a>Passaggi successivi
* [Creare la prima applicazione Java di Service Fabric in Linux usando Eclipse](service-fabric-get-started-eclipse.md)
* [Altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Uso dell'interfaccia della riga di comando di Azure per interagire con un cluster di Service Fabric](service-fabric-azure-cli.md)
* [Risoluzione dei problemi relativi alla distribuzione](service-fabric-azure-cli.md#troubleshooting)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)

## <a name="related-articles"></a>Articoli correlati

* [Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introduzione all'interfaccia della riga di comando di XPlat per Service Fabric](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

