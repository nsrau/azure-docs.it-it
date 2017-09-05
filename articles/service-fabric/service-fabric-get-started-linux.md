---
title: Configurare l'ambiente di sviluppo in Linux | Microsoft Docs
description: "Installare il runtime e l'SDK e creare un cluster di sviluppo locale in Linux. Al termine della configurazione, sarà possibile iniziare a sviluppare applicazioni."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/23/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 4f51030446d2d2a5a11018b1fce7d7e9193f3dfc
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="prepare-your-development-environment-on-linux"></a>Preparare l'ambiente di sviluppo in Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Per distribuire ed eseguire [applicazioni di Azure Service Fabric](service-fabric-application-model.md) in un computer di sviluppo Linux, installare il runtime e l'SDK comune. È anche possibile installare SDK facoltativi per Java e .NET Core.

## <a name="prerequisites"></a>Prerequisiti

Per lo sviluppo, sono supportati i sistemi operativi seguenti:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>Aggiornare le origini APT
Per installare l'SDK e il pacchetto di runtime associato tramite lo strumento da riga di comando apt-get, è prima necessario aggiornare le origini APT (Advanced Packaging Tool).

1. Aprire un terminale.
2. Aggiungere il repository di Service Fabric all'elenco di origini.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Aggiungere il repository `dotnet` all'elenco di origini.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Aggiungere la nuova chiave Gnu Privacy Guard (GnuPG o GPG) al keyring di APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Aggiungere la chiave GPG Docker ufficiale al keyring di APT.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Configurare il repository di Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Aggiornare l'elenco dei pacchetti in base ai repository appena aggiunti.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-local-cluster-setup"></a>Installare e configurare l'SDK per la configurazione del cluster locale

Dopo aver aggiornato le origini, è possibile installare l'SDK. Installare il pacchetto Service Fabric SDK, confermare l'installazione e accettare il contratto di licenza.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   I comandi seguenti permettono di automatizzare l'accettazione della licenza per i pacchetti di Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>Configurare un cluster locale
  Se l'installazione ha esito positivo, sarà possibile avviare un cluster locale.

  1. Eseguire lo script di configurazione del cluster.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Aprire un Web browser e passare a [Service Fabric Explorer](http://localhost:19080/Explorer). Se il cluster è stato avviato, verrà visualizzato il dashboard di Service Fabric Explorer.

      ![Service Fabric Explorer in Linux][sfx-linux]

  A questo punto, è possibile distribuire pacchetti di applicazione di Service Fabric predefiniti o nuovi pacchetti basati su contenitori o eseguibili guest. Per creare nuovi servizi usando gli SDK per Java o .NET Core, seguire le procedure di configurazione facoltative riportate nelle sezioni successive.


  > [!NOTE]
  > I cluster autonomi non sono supportati in Linux. L'anteprima supporta solo cluster con un solo computer e più computer Linux di Azure.
  >

## <a name="set-up-the-service-fabric-cli"></a>Configurare l'interfaccia della riga di comando di Service Fabric

L'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md) include comandi per l'interazione con entità di Service Fabric, come cluster e applicazioni. È basato su python, quindi occorre assicurarsi che python e pip siano installati prima di continuare con il comando seguente:

```bash
pip install sfctl
```

## <a name="install-and-set-up-the-generators-for-containers-and-guest-executables"></a>Installare e configurare i generatori per contenitori ed eseguibili guest
Service Fabric offre gli strumenti di scaffolding che consentono di creare applicazioni Service Fabric dal terminale tramite il generatore di modelli Yeoman. Seguire questa procedura per assicurarsi che nella macchina virtuale sia disponibile il generatore di modelli Yeoman di Service Fabric.

1. Installare nodejs e NPM nella macchina virtuale

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Installare il generatore di modelli [Yeoman](http://yeoman.io/) nella macchina virtuale da NPM

  ```bash
  sudo npm install -g yo
  ```
3. Installare il generatore di contenitori Yeo per Service Fabric e il generatore di eseguibili guest da NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Al termine dell'installazione dei generatori precedenti, dovrebbe essere possibile creare app con eseguibili guest o servizi contenitore eseguendo `yo azuresfguest` o `yo azuresfcontainer` rispettivamente.

## <a name="install-the-necessary-java-artifacts-optional-if-you-want-to-use-the-java-programming-models"></a>Installare gli elementi Java necessari (facoltativo se si vogliono usare i modelli di programmazione Java)

Per creare servizi di Service Fabric usando Java, assicurarsi che JDK 1.8 sia installato insieme a Gradle, usato per l'esecuzione di attività di compilazione. Il frammento di codice seguente installa Open JDK 1.8 insieme a Gradle. Il pull delle librerie Java per Service Fabric viene eseguito da Maven.

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Installare il plug-in Eclipse Neon (facoltativo)

È possibile installare il plug-in Eclipse per Service Fabric dall'**IDE di Eclipse per sviluppatori Java**. È possibile usare Eclipse per creare applicazioni contenitore e applicazioni eseguibili guest di Service Fabric in aggiunta alle applicazioni Java di Service Fabric.

1. In Eclipse assicurarsi che sia installata l'ultima versione di Eclipse Neon e di Buildship (1.0.17 o versione successiva). Per controllare le versioni dei componenti installati, selezionare **Help** > **Installation Details** (? > Dettagli installazione). È possibile aggiornare Buildship seguendo le istruzioni riportate in [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: plug-in Eclipse per Gradle).

2. Per installare il plug-in Service Fabric, selezionare **Help** > **Install New Software** (? > Installa nuovo software).

3. Nella casella **Work with** (Usa) digitare **http://dl.microsoft.com/eclipse**.

4. Fare clic su **Aggiungi**.

    ![Pagina relativa al software disponibile][sf-eclipse-plugin]

5. Selezionare il plug-in **ServiceFabric** e quindi fare clic su **Next** (Avanti).

6. Completare la procedura di installazione e quindi accettare il contratto di licenza con l'utente finale.

Se il plug-in Eclipse per Service Fabric è già installato, verificare che la versione sia la più recente. Per controllare, selezionare **Help** > **Installation Details** (? > Dettagli installazione) e quindi cercare Service Fabric nell'elenco dei plug-in installati. Se è disponibile una versione più recente, selezionare **Update** (Aggiorna).

Per altre informazioni, vedere [Plug-in Service Fabric per lo sviluppo di applicazioni Java in Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>Installare .NET Core SDK (facoltativo, per usare i modelli di programmazione .NET Core)
.NET Core SDK offre le librerie e i modelli necessari per creare servizi di Service Fabric con .NET Core. Installare il pacchetto .NET Core SDK eseguendo questo comando:

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

## <a name="update-the-sdk-and-runtime"></a>Aggiornare SDK e runtime

Per aggiornare l'SDK e il runtime alla versione più recente, eseguire questi comandi deselezionando gli SDK da non aggiornare:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp
```
Per aggiornare i file binari Java SDK da Maven, è necessario aggiornare i dettagli della versione del file binario corrispondente nel file ``build.gradle`` in modo che facciano riferimento alla versione più recente. Per individuare esattamente il punto in cui è necessario aggiornare la versione, è possibile vedere qualsiasi file ``build.gradle`` negli esempi introduttivi di Service Fabric , disponibili [qui](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> L'aggiornamento dei pacchetti può causare l'arresto dell'esecuzione del cluster di sviluppo locale. Riavviare il cluster locale dopo un aggiornamento seguendo le istruzioni disponibili in questa pagina.

## <a name="next-steps"></a>Passaggi successivi

* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse](service-fabric-get-started-eclipse.md)
* [Creare la prima applicazione CSharp in Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Preparare l'ambiente di sviluppo in OSX](service-fabric-get-started-mac.md)
* [Usare l'interfaccia della riga di comando di Service Fabric per gestire le applicazioni](service-fabric-application-lifecycle-sfctl.md)
* [Differenze in Service Fabric tra Windows e Linux](service-fabric-linux-windows-differences.md)
* [Introduzione all'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

