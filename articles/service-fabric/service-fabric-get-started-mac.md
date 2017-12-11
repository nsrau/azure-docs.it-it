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
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 328b2778a68e32d95b666124bf7bba969a5f52a6
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurare l'ambiente di sviluppo in Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

È possibile creare applicazioni di Azure Service Fabric da eseguire in cluster Linux usando Mac OS X. Questo documento illustra come configurare il computer Mac per lo sviluppo.

## <a name="prerequisites"></a>Prerequisiti
Azure Service Fabric non viene eseguito in modo nativo in Mac OS X. Per eseguire un cluster di Service Fabric locale, viene offerta un'immagine del contenitore Docker preconfigurata. Prima di iniziare, sono necessari:

* Almeno 4 GB di RAM.
* La versione più recente di [Docker](https://www.docker.com/).
* Accesso all'[immagine del contenitore Docker onebox](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) di Service Fabric.

>[!TIP]
>
>Per installare Docker nel computer Mac, è possibile seguire la procedura descritta nella [documentazione di Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Al termine, [verificare l'installazione](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Creare un contenitore locale e configurare Service Fabric
Per configurare un contenitore Docker locale ed eseguirvi un cluster di Service Fabric, seguire questa procedura:

1. Eseguire il pull dell'immagine del contenitore onebox di Service Fabric dal repository Docker Hub:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Aggiornare la configurazione del daemon Docker nell'host con le impostazioni seguenti e riavviare il daemon Docker: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    È possibile aggiornare queste impostazioni direttamente nel file daemon.json nel percorso di installazione di Docker.
    
    >[!NOTE]
    >
    >Il percorso del file daemon.json può essere diverso a seconda del computer, ad esempio ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >
    >È consigliabile modificare direttamente le impostazioni di configurazione del daemon in Docker. Selezionare l'**icona Docker** e quindi selezionare **Preferences** (Preferenze) > **Daemon** > **Advanced** (Avanzate).
    >

3. Avviare un'istanza del contenitore onebox di Service Fabric e usare l'immagine di cui si è eseguito il pull nel primo passaggio:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >Specificare un nome per l'istanza del contenitore per poterla gestire in modo più leggibile. 
    >
    >Se l'applicazione è in ascolto su determinate porte, le porte devono essere specificate usando tag `-p` aggiuntivi. Se ad esempio l'applicazione è in ascolto sulla porta 8080, aggiungere il tag `-p` seguente:
    >
    >`run docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox`
    >

4. Accedere al contenitore Docker in modalità SSH interattiva:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Eseguire lo script di configurazione per recuperare le dipendenze necessarie e avviare il cluster nel contenitore:

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Al termine del passaggio 5, passare a `http://localhost:19080` dal Mac. Verrà visualizzato Service Fabric Explorer.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurare l'interfaccia della riga di comando di Service Fabric (sfctl) in un computer Mac

Per installare l'interfaccia della riga di comando di Service Fabric (`sfctl`) in un computer Mac, seguire le istruzioni riportate in [Interfaccia della riga di comando di Azure Service Fabric](service-fabric-cli.md#cli-mac).
I comandi dell'interfaccia della riga di comando supportano l'interazione con entità di Service Fabric come cluster, applicazioni e servizi.

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Creare l'applicazione in un computer Mac usando Yeoman

Service Fabric fornisce strumenti di scaffolding che consentono di creare un'applicazione di Service Fabric dal terminale usando il generatore di modelli Yeoman. Usare la procedura seguente per assicurarsi che nel computer sia disponibile il generatore di modelli Yeoman di Service Fabric:

1. Node.js e Node Package Manager (NPM) devono essere installati nel Mac. Il software può essere installato usando [HomeBrew](https://brew.sh/), come segue:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installare il generatore di modelli [Yeoman](http://yeoman.io/) nel computer da NPM:

    ```bash
    npm install -g yo
    ```
3. Installare il generatore Yeoman preferito seguendo la procedura disponibile nella [documentazione](service-fabric-get-started-linux.md) introduttiva. Per creare applicazioni di Service Fabric con Yeoman, seguire questa procedura:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Per compilare un'applicazione Java di Service Fabric nel computer Mac, JDK versione 1.8 e Gradle devono essere installati nel computer host. Il software può essere installato usando [HomeBrew](https://brew.sh/), come segue: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Distribuire l'applicazione nel computer Mac dal terminale

Dopo aver creato e compilato l'applicazione di Service Fabric, è possibile distribuirla con l'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md#cli-mac):

1. Connettersi al cluster di Service Fabric in esecuzione nell'istanza del contenitore nel computer Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Dalla directory di progetto eseguire lo script di installazione:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurare lo sviluppo .NET Core 2.0

Installare il [.NET Core 2.0 SDK per Mac](https://www.microsoft.com/net/core#macos) per avviare la [creazione di applicazioni C# di Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md). I pacchetti per le applicazioni .NET Core 2.0 di Service Fabric sono ospitati in NuGet.org, attualmente in anteprima.

## <a name="install-the-service-fabric-plug-in-for-eclipse-neon-on-your-mac"></a>Installare il plug-in Service Fabric per Eclipse Neon nel computer Mac

Azure Service Fabric fornisce un plug-in per Eclipse Neon per l'ambiente IDE Java. Il plug-in semplifica il processo di creazione, compilazione e distribuzione di servizi Java. Per installare o aggiornare il plug-in Service Fabric per Eclipse alla versione più recente, seguire [questa procedura](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon). Sono applicabili anche gli altri passaggi della [documentazione di Service Fabric per Eclipse](service-fabric-get-started-eclipse.md): compilare un'applicazione, aggiungere un servizio a un'applicazione, disinstallare un'applicazione e così via.

L'ultimo passaggio prevede la creazione di un'istanza del contenitore con un percorso condiviso con l'host. Il plug-in richiede che questo tipo di creazione di istanza usi il contenitore Docker nel Mac, ad esempio:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```

Gli attributi sono definiti come segue:
* `/Users/sayantan/work/workspaces/mySFWorkspace` è il percorso completo dell'area di lavoro nel Mac.
* `/tmp/mySFWorkspace` è il percorso all'interno del contenitore a cui eseguire il mapping dell'area di lavoro.

>[!NOTE]
> 
>Se l'area di lavoro ha un nome/percorso diverso, aggiornare questi valori nel comando `docker run`.
> 
>Se si avvia il contenitore con un nome diverso da `sfonebox`, aggiornare il valore del nome nel file testclient.sh nell'applicazione Java di Service Fabric Actors.
>

## <a name="next-steps"></a>Passaggi successivi
<!-- Links -->
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse](service-fabric-get-started-eclipse.md)
* [Creare un cluster di Service Fabric nel portale di Azure](service-fabric-cluster-creation-via-portal.md)
* [Creare un cluster di Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Informazioni sul modello applicativo di Service Fabric](service-fabric-application-model.md)
* [Usare l'interfaccia della riga di comando di Service Fabric per gestire le applicazioni](service-fabric-application-lifecycle-sfctl.md)
* [Prepare a Linux development environment on Windows (Preparare un ambiente di sviluppo Linux in Windows)](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
