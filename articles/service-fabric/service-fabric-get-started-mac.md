---
title: Configurare l'ambiente di sviluppo in Mac OS X per l'interazione con Azure Service Fabric| Microsoft Docs
description: Installare il runtime, l'SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a creare applicazioni in Mac OS X.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: linux
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: suhuruli
ms.openlocfilehash: 4dac40e9fad8361c0e6c8a8758028743f2506f56
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428092"
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

>[!TIP]
>
>Per installare Docker nel computer Mac, è possibile seguire la procedura descritta nella [documentazione di Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Al termine, [verificare l'installazione](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Creare un contenitore locale e configurare Service Fabric
Per configurare un contenitore Docker locale ed eseguirvi un cluster di Service Fabric, seguire questa procedura:

1. Aggiornare la configurazione del daemon Docker nell'host con le impostazioni seguenti e riavviare il daemon Docker: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    È possibile aggiornare queste impostazioni direttamente nel file daemon.json nel percorso di installazione di Docker. È possibile modificare direttamente le impostazioni di configurazione del daemon in Docker. Selezionare l'**icona Docker** e quindi selezionare **Preferences** (Preferenze) > **Daemon** > **Advanced** (Avanzate).
    
    >[!NOTE]
    >
    >È consigliato modificare il daemon direttamente in Docker perché il percorso del file daemon.json può variare da computer a computer, ad esempio ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >È consigliabile aumentare le risorse allocate per Docker durante il test di applicazioni di grandi dimensioni. Questa operazione può essere eseguita selezionando l'**icona Docker**, quindi selezionando **Advanced** (Avanzate) per modificare il numero di core e la quantità di memoria.

2. In una nuova directory creare un file denominato `Dockerfile` per creare l'immagine di Service Fabric:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >È possibile adattare questo file per aggiungere altri programmi o dipendenze nel contenitore.
    >Ad esempio, se si aggiunge `RUN apt-get install nodejs -y` sarà possibile supportare le applicazioni `nodejs` come eseguibili guest.
    
    >[!TIP]
    > Per impostazione predefinita, verrà eseguito il pull dell'immagine con la versione più recente di Service Fabric. Per revisioni specifiche, vedere la pagina dell'[hub Docker](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

3. Per creare un'immagine riutilizzabile da `Dockerfile` aprire un terminale e `cd` alla directory che contiene `Dockerfile` e quindi eseguire:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Questa operazione richiederà del tempo, ma è necessaria eseguirla solo una volta.

4. Ora è possibile iniziare rapidamente una copia locale di Service Fabric, quando necessario, eseguendo:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Specificare un nome per l'istanza del contenitore per poterla gestire in modo più leggibile. 
    >
    >Se l'applicazione è in ascolto su determinate porte, le porte devono essere specificate usando tag `-p` aggiuntivi. Se ad esempio l'applicazione è in ascolto sulla porta 8080, aggiungere il tag `-p` seguente:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. L'avvio del cluster richiederà qualche minuto. Quando è in esecuzione, è possibile visualizzare i log usando il comando seguente o passare al dashboard per visualizzare l'integrità del cluster [http://localhost:19080](http://localhost:19080):

    ```bash 
    docker logs sftestcluster
    ```



6. Per arrestare e pulire il contenitore, usare il comando seguente. Tuttavia, questo contenitore verrà usato nel passaggio successivo.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitazioni note 
 
 Le seguenti sono limitazioni note del cluster locale eseguito in un contenitore per Mac: 
 
 * Il servizio DNS non viene eseguito e non è supportato [Problema 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurare l'interfaccia della riga di comando di Service Fabric (sfctl) in un computer Mac

Per installare l'interfaccia della riga di comando di Service Fabric (`sfctl`) in un computer Mac, seguire le istruzioni riportate in [Interfaccia della riga di comando di Azure Service Fabric](service-fabric-cli.md#cli-mac).
I comandi dell'interfaccia della riga di comando supportano l'interazione con entità di Service Fabric come cluster, applicazioni e servizi.

1. Eseguire questo comando per connettersi al cluster prima di distribuire le applicazioni. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

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
3. Installare il generatore Yeoman preferito seguendo la procedura disponibile nella [documentazione](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) introduttiva. Per creare applicazioni di Service Fabric con Yeoman, seguire questa procedura:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Al termine dell'installazione dei generatori, creare i servizi contenitore o eseguibili guest eseguendo rispettivamente `yo azuresfcontainer` o `yo azuresfguest`.

5. Per compilare un'applicazione Java di Service Fabric nel computer Mac, JDK versione 1.8 e Gradle devono essere installati nel computer host. Il software può essere installato usando [HomeBrew](https://brew.sh/), come segue: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    >[!TIP]
    > Verificare che sia installata la versione corretta di JDK. 

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

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Installare il plug-in Service Fabric per Eclipse nel computer Mac

Azure Service Fabric fornisce un plug-in per Eclipse Neon (o versione successiva) per l'ambiente IDE Java. Il plug-in semplifica il processo di creazione, compilazione e distribuzione di servizi Java. Per installare o aggiornare il plug-in Service Fabric per Eclipse alla versione più recente, seguire [questa procedura](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). Sono applicabili anche gli altri passaggi della [documentazione di Service Fabric per Eclipse](service-fabric-get-started-eclipse.md): compilare un'applicazione, aggiungere un servizio a un'applicazione, disinstallare un'applicazione e così via.

L'ultimo passaggio prevede la creazione di un'istanza del contenitore con un percorso condiviso con l'host. Il plug-in richiede che questo tipo di creazione di istanza usi il contenitore Docker nel Mac, Ad esempio: 

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
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
