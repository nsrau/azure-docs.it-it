---
title: Creare la prima applicazione di Microsoft Azure Service Fabric in Linux con C#| Microsoft Docs
description: Informazioni su come creare e distribuire un'applicazione di Service Fabric con C# e .NET Core 2.0.
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: subramar
ms.openlocfilehash: f044aa006727fafb52c2c5e6c015c3e4cc70edf7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57882014"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Creare la prima applicazione di Azure Service Fabric
> [!div class="op_single_selector"]
> * [Java - Linux (Anteprima)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux (Anteprima)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric mette a disposizione SDK per la compilazione di servizi su Linux in .NET Core e Java. In questa esercitazione verrà esaminata la creazione di un'applicazione per Linux e la compilazione di un servizio con C# in .NET Core 2.0.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di avere [configurato l'ambiente di sviluppo di Linux](service-fabric-get-started-linux.md). Se si usa Mac OS X è possibile [configurare un ambiente con un solo computer Linux in una macchina virtuale usando Vagrant](service-fabric-get-started-mac.md).

È consigliabile installare anche l'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-c"></a>Installare e configurare i generatori per C#
Service Fabric offre gli strumenti di scaffolding che consentono di creare applicazioni Service Fabric dal terminale tramite i generatori di modelli Yeoman. Seguire questi passaggi per configurare i generatori di modelli Yeoman di Service Fabric per C#:

1. Installare nodejs e NPM nella macchina virtuale

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Installare il generatore di modelli [Yeoman](https://yeoman.io/) nella macchina virtuale da NPM

   ```bash
   npm install -g yo
   ```
3. Installare il generatore di applicazioni C# Yeoman di Service Fabric da NPM

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Creazione dell'applicazione
Un'applicazione Infrastruttura di servizi può contenere uno o più servizi, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. Il generatore [Yeoman](https://yeoman.io/) di Service Fabric per C#, installato nell'ultimo passaggio, semplifica la creazione del primo servizio e l'aggiunta di altri servizi in un secondo momento. Verrà usato Yeoman per creare un'applicazione con un solo servizio.

1. In un terminale, digitare il comando seguente per iniziare a creare lo scaffolding: `yo azuresfcsharp`
2. Assegnare un nome all'applicazione.
3. Scegliere il tipo del primo servizio e assegnargli un nome. Ai fini di questa esercitazione viene scelto un servizio Reliable Actor.

   ![Generatore Yeoman di Service Fabric per C#][sf-yeoman]

> [!NOTE]
> Per altre informazioni sulle opzioni, vedere [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Compilare l'applicazione
I modelli Yeoman di Service Fabric includono uno script di compilazione che è possibile usare per compilare l'app dal terminale (dopo il passaggio alla cartella dell'applicazione).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale.

1. Connettersi al cluster locale di Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Eseguire lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    ./install.sh
    ```

La distribuzione dell'applicazione compilata è uguale a quella di qualsiasi altra applicazione di Service Fabric. Per istruzioni dettagliate, vedere la documentazione sulla [gestione di un'applicazione di Service Fabric con l'interfaccia della riga di comando di Service Fabric](service-fabric-application-lifecycle-sfctl.md).

I parametri per questi comandi si trovano nei manifesti generati nel pacchetto dell'applicazione.

Dopo la distribuzione dell'applicazione, aprire un browser e passare a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) all'indirizzo [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Espandere quindi il nodo **Applicazioni**, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.

> [!IMPORTANT]
> Per distribuire l'applicazione a un cluster Linux protetto in Azure, è necessario configurare un certificato per convalidare l'applicazione con il runtime di Service Fabric. In questo modo i servizi di Reliable Services possono comunicare con le API di runtime di Service Fabric sottostanti. Per altre informazioni, vedere [Configurare un'app di Reliable Services da eseguire su cluster Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Avviare il client di test ed eseguire un failover
I progetti Actor non eseguono alcuna operazione in modo indipendente. Richiedono un altro servizio o client per l'invio dei messaggi. Il modello Actor include un semplice script di test che è possibile usare per interagire con il servizio Actor.

1. Eseguire lo script tramite l'utilità delle espressioni di controllo per visualizzare l'output del servizio Actor.

   In caso di MAC OS X è necessario copiare la cartella myactorsvcTestClient in un percorso all'interno del contenitore eseguendo i comandi aggiuntivi seguenti.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. In Service Fabric Explorer individuare il nodo che ospita la replica primaria del servizio Actor. Nello screenshot seguente si tratta del nodo 3.

    ![Ricerca della replica primaria in Service Fabric Explorer][sfx-primary]
3. Fare clic sul nodo trovato nel passaggio precedente, quindi selezionare **Disattiva (riavvio)** dal menu Azioni. Questa operazione consente di riavviare un nodo nel cluster locale forzando il failover in una replica secondaria in esecuzione in un altro nodo. Durante l'operazione, prestare attenzione all'output del client di test e notare che l'incremento del contatore prosegue nonostante il failover.

## <a name="adding-more-services-to-an-existing-application"></a>Aggiunta di altri servizi a un'applicazione esistente

Per aggiungere un altro servizio a un'applicazione già creata mediante `yo`, seguire questa procedura:
1. Modificare la directory impostandola sulla radice dell'applicazione esistente.  Ad esempio, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` è l'applicazione creata da Yeoman.
2. Eseguire `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Passaggi successivi

* [Interagire con un cluster di Service Fabric usando l'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)
* [Introduzione all'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
