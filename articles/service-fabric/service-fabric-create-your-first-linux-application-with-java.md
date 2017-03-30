---
title: Creare la prima app di microservizi di Azure in Linux con Java | Microsoft Docs
description: Creare e distribuire un&quot;applicazione di Service Fabric con Java
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: eedddf7a40acfba7513efd810d115f1afe2f224d
ms.lasthandoff: 03/23/2017


---
# <a name="create-your-first-azure-service-fabric-application"></a>Creare la prima applicazione di Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric mette a disposizione SDK per la compilazione di servizi su Linux in .NET Core e Java. In questa esercitazione viene creata un'applicazione per Linux e viene compilato un servizio con Java.  

> [!NOTE]
> Java è supportato come linguaggio di programmazione predefinito avanzato solo per l'anteprima Linux (il supporto per Windows è pianificato). Tutte le applicazioni che includono applicazioni Java, tuttavia, possono essere eseguite come eseguibili guest o all'interno di contenitori in Windows o Linux. Per altre informazioni, vedere gli articoli su come [distribuire un eseguibile esistente in Azure Service Fabric](service-fabric-deploy-existing-app.md) e su come [distribuire contenitori in Service Fabric](service-fabric-deploy-container.md).
>

## <a name="video-tutorial"></a>Esercitazione video

Il video di Microsoft Virtual Academy seguente illustra il processo di creazione di un'app Java in Linux:  
<center><a target="\_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di avere [configurato l'ambiente di sviluppo di Linux](service-fabric-get-started-linux.md). Se si usa Mac OS X è possibile [configurare un ambiente con un solo computer Linux in una macchina virtuale usando Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Creazione dell'applicazione
Un'applicazione Infrastruttura di servizi può contenere uno o più servizi, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. Service Fabric SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che semplifica la creazione del primo servizio e l'aggiunta dei successivi. Verrà usato Yeoman per creare un'applicazione con un solo servizio.

1. In un terminale digitare ``yo azuresfjava``.
2. Assegnare un nome all'applicazione.
3. Scegliere il tipo del primo servizio e assegnargli un nome. Ai fini di questa esercitazione viene scelto un servizio Reliable Actor.

   ![Generatore Yeoman di Service Fabric per Java][sf-yeoman]

> [!NOTE]
> Per altre informazioni sulle opzioni, vedere [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md).
>

## <a name="build-the-application"></a>Compilare l'applicazione.
I modelli Yeoman di Service Fabric includono uno script di compilazione per [Gradle](https://gradle.org/), che è possibile usare per compilare l'applicazione dal terminale.

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Distribuire l'applicazione
Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale tramite l'interfaccia della riga di comando di Azure.

1. Connettersi al cluster locale di Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```

2. Usare lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    ./install.sh
    ```

3. Aprire un browser e passare a Service Fabric Explorer all'indirizzo http://localhost:19080/Explorer. Sostituire localhost con l'indirizzo IP privato della macchina virtuale se si usa Vagrant in Mac OS X.

4. Espandere il nodo delle applicazioni, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.

## <a name="start-the-test-client-and-perform-a-failover"></a>Avviare il client di test ed eseguire un failover
I progetti Actor non eseguono alcuna operazione in modo indipendente. Richiedono un altro servizio o client per l'invio dei messaggi. Il modello Actor include un semplice script di test che è possibile usare per interagire con il servizio Actor.

1. Eseguire lo script tramite l'utilità delle espressioni di controllo per visualizzare l'output del servizio Actor.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. In Service Fabric Explorer individuare il nodo che ospita la replica primaria del servizio Actor. Nello screenshot seguente si tratta del nodo 3.

    ![Ricerca della replica primaria in Service Fabric Explorer][sfx-primary]

3. Fare clic sul nodo trovato nel passaggio precedente, quindi selezionare **Disattiva (riavvio)** dal menu Azioni. Questa azione consente di riavviare uno dei cinque nodi nel cluster locale e forzare il failover in una delle repliche secondarie in esecuzione in un altro nodo. Durante l'operazione, prestare attenzione all'output del client di test e notare che l'incremento del contatore prosegue nonostante il failover.

## <a name="create-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Creare e distribuire un'applicazione con il plug-in Eclipse Neon

Service Fabric offre anche la possibilità di creare, compilare e distribuire l'applicazione Java di Service Fabric usando Eclipse. Quando si installa Eclipse, scegliere l'**IDE di Eclipse per sviluppatori Java**. Attualmente Service Fabric supporta il plug-in per Eclipse **Neon**. Per informazioni, vedere la documentazione dettagliata in [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md) (Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse)

## <a name="adding-more-services-to-an-existing-application"></a>Aggiunta di altri servizi a un'applicazione esistente

### <a name="using-command-line-utility"></a>Uso dell'utilità della riga di comando
Per aggiungere un altro servizio a un'applicazione già creata mediante `yo`, seguire questa procedura:
1. Modificare la directory impostandola sulla radice dell'applicazione esistente.  Ad esempio, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` è l'applicazione creata da Yeoman.
2. Eseguire `yo azuresfjava:AddService`

### <a name="using-service-fabric-eclipse-plugin-for-java-on-linux"></a>Uso del plug-in Service Fabric Eclipse per Java in Linux
Per aggiungere un servizio a un'applicazione esistente creata con il plug-in Eclipse per Service Fabric, vedere la documentazione [qui](service-fabric-get-started-eclipse.md#add-a-service-fabric-service-to-your-service-fabric-application).

## <a name="next-steps"></a>Passaggi successivi
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md) (Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse)
* [Altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Uso dell'interfaccia della riga di comando di Azure per interagire con un cluster di Service Fabric](service-fabric-azure-cli.md)
* [Risoluzione dei problemi relativi alla distribuzione](service-fabric-azure-cli.md#troubleshooting)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

