---
title: Configurare l'ambiente di sviluppo in Mac OS X per l'interazione con Azure Service Fabric| Microsoft Docs
description: "Installare il runtime, l'SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a creare applicazioni in Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: saysa
ms.openlocfilehash: cf67c377cd5c17f7b540fb23af48a333a9cddf69
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurare l'ambiente di sviluppo in Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

È possibile creare applicazioni di Service Fabric da eseguire in cluster Linux usando Mac OS X. Questo articolo illustra come configurare il computer Mac per lo sviluppo.

## <a name="prerequisites"></a>Prerequisiti
Service Fabric non viene eseguito in modo nativo in OS X. Per eseguire un cluster di Service Fabric locale, viene offerta un'immagine del contenitore Docker preconfigurata. Prima di iniziare, sono necessari:

* Almeno 4 GB di RAM
* Ultima versione di [Docker](https://www.docker.com/)
* Accesso all'[immagine](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) del contenitore Docker onebox di Service Fabric

>[!TIP]
> * Per installare Docker nel computer Mac, è possibile seguire la procedura descritta nella [documentazione](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) ufficiale di Docker. 
> * Al termine dell'installazione, verificare che sia stata eseguita correttamente seguendo la procedura descritta [qui](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Creare un contenitore locale e configurare Service Fabric
Per configurare un contenitore Docker locale ed eseguirvi un cluster di Service Fabric, seguire questa procedura:

1. Eseguire il pull dell'immagine dal repository dell'hub Docker:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Avviare un'istanza del contenitore onebox di Service Fabric con l'immagine:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >Specificando un nome per l'istanza del contenitore, è possibile gestirla in modo più leggibile. 

3. Accedere al contenitore Docker in modalità SSH interattiva:

    ```bash
    docker exec -it sfonebox bash
    ```

4. Eseguire lo script di configurazione, che recupererà le dipendenze necessarie e successivamente avvierà il cluster nel contenitore.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

5. Dopo il completamento del passaggio 4, è possibile accedere a ``http://localhoist:19080`` dal computer Mac e visualizzare Service Fabric Explorer.


## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurare l'interfaccia della riga di comando di Service Fabric (sfctl) in un computer Mac

Per installare l'interfaccia della riga di comando di Service Fabric (`sfctl`) in un computer Mac, seguire le istruzioni riportate in [Interfaccia della riga di comando di Azure Service Fabric](service-fabric-cli.md#cli-mac).
I comandi dell'interfaccia della riga di comando consentono di interagire con entità di Service Fabric come cluster, applicazioni e servizi.

## <a name="create-application-on-your-mac-using-yeoman"></a>Creare un'applicazione in un computer Mac con Yeoman

Service Fabric offre strumenti di scaffolding che consentono di creare un'applicazione di Service Fabric dal terminale usando il generatore di modelli Yeoman. Seguire questa procedura per assicurarsi che nel computer sia disponibile il generatore di modelli Yeoman di Service Fabric.

1. È necessario che Node.js e NPM siano installati nel computer Mac. In caso contrario, è possibile installare Node.js e NPM usando Homebrew con il passaggio seguente. Per verificare le versioni di Node.js e NPM installate nel computer Mac, è possibile usare l'opzione ``-v``.

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installare il generatore di modelli [Yeoman](http://yeoman.io/) nel computer da NPM.

    ```bash
    npm install -g yo
    ```
3. Installare il generatore Yeoman da usare, seguendo la procedura disponibile nella [documentazione](service-fabric-get-started-linux.md) introduttiva. Per creare applicazioni di Service Fabric con Yeoman, seguire questa procedura:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Per compilare un'applicazione Java di Service Fabric nel computer Mac, è necessario che nel computer host siano installati JDK 1.8 e Gradle. Se non sono già presenti, è possibile eseguire l'installazione con [Homebrew](https://brew.sh/). 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>Distribuire l'applicazione nel computer Mac dal terminale

Dopo aver creato e compilato l'applicazione di Service Fabric, è possibile distribuirla con l'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md#cli-mac) seguendo questa procedura:

1. Connettersi al cluster di Service Fabric in esecuzione all'interno dell'istanza del contenitore nel computer Mac.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Passare alla directory di progetto ed eseguire lo script di installazione.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurare lo sviluppo .NET Core 2.0

Installare il [.NET Core 2.0 SDK per Mac](https://www.microsoft.com/net/core#macos) per avviare la [creazione di applicazioni C# di Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md). I pacchetti per le applicazioni .NET Core 2.0 di Service Fabric sono ospitati in NuGet.org, attualmente in anteprima.

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Installare il plug-in Service Fabric per Eclipse Neon nel computer Mac

Service Fabric offre un plug-in per **Eclipse Neon per l'ambiente IDE Java** che può semplificare il processo di creazione, compilazione e distribuzione di servizi Java. È possibile seguire la procedura di installazione illustrata in questa [documentazione](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) generale sull'installazione o l'aggiornamento all'ultima versione del plug-in Service Fabric per Eclipse.

Tutti gli altri passaggi indicati nella [documentazione sul plug-in Service Fabric per Eclipse](service-fabric-get-started-eclipse.md) per compilare un'applicazione, aggiungervi un servizio, installare/disinstallare l'applicazione e così via sono applicabili anche in questo contesto.

Oltre ai passaggi precedenti, per garantire il funzionamento del plug-in Service Fabric per Eclipse con il contenitore Docker nel computer è consigliabile creare un'istanza del contenitore con un percorso condiviso con l'host, come segue:
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
``/Users/sayantan/work/workspaces/mySFWorkspace`` è il percorso completo dell'area di lavoro in Mac e ``/tmp/mySFWorkspace`` è il percorso all'interno del contenitore, a cui deve essere mappato.

> [!NOTE]
>1. Se il nome o il percorso dell'area di lavoro è diverso, aggiornarlo di conseguenza nel comando ``docker run`` riportato sopra.
>2. Se si avvia il contenitore con un nome diverso da ``sfonebox``, aggiornare il nome nel file ``testclient.sh`` nell'applicazione Java di Service Fabric Actors.

## <a name="next-steps"></a>Passaggi successivi
<!-- Links -->
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md) (Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse)
* [Creare un cluster di Service Fabric nel portale di Azure](service-fabric-cluster-creation-via-portal.md)
* [Creare un cluster di Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Informazioni sul modello applicativo di Service Fabric](service-fabric-application-model.md)
* [Usare l'interfaccia della riga di comando di Service Fabric per gestire le applicazioni](service-fabric-application-lifecycle-sfctl.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
