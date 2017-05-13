---
title: Configurare l&quot;ambiente di sviluppo in Linux | Microsoft Docs
description: "Installare il runtime e l&quot;SDK e creare un cluster di sviluppo locale in Linux. Al termine della configurazione, sarà possibile iniziare a sviluppare applicazioni."
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
ms.date: 05/04/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d01e141ec8ee8da18d38a216f3b13c88f3632801
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


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

### <a name="supported-operating-system-versions"></a>Versioni del sistema operativo supportate
Per lo sviluppo, sono supportati i sistemi operativi seguenti:

* Ubuntu 16.04 (**"Xenial Xerus"**)

## <a name="update-your-apt-sources"></a>Aggiornare le origini apt
Per installare l'SDK e il pacchetto di runtime associato tramite apt-get, è prima necessario aggiornare le origini apt.

1. Aprire un terminale.
2. Aggiungere il repository di Service Fabric all'elenco di origini.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Aggiungere il repository **dotnet** all'elenco di origini.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```
4. Aggiungere la nuova chiave GPG al keyring di apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Aggiornare l'elenco dei pacchetti in base ai repository appena aggiunti.

    ```bash
    sudo apt-get update
    ```
## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Installare e configurare l'SDK per contenitori ed eseguibili guest
Dopo aver aggiornato le origini, è possibile installare l'SDK.

1. Installare il pacchetto Service Fabric SDK. Verrà richiesto di confermare l'installazione e di accettare un contratto di licenza.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
    Per automatizzare l'installazione è possibile ignorare la richiesta del contratto di licenza impostando le selezioni di debconf per i pacchetti di Service Fabric. È possibile eseguire i due comandi seguenti
    
    ```bash
    echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    ```

2. Eseguire lo script di configurazione dell'SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Al termine della procedura di installazione del pacchetto SDK comune, sarà possibile creare app con servizi contenitore o eseguibili guest eseguendo `yo azuresfguest`. Potrebbe essere necessario impostare la variabile di ambiente **$NODE_PATH** sul percorso in cui si trovano i moduli Node. 

    ```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
    ```

Se si usa l'ambiente come radice, potrebbe essere necessario impostare la variabile con il comando seguente:

    ```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
    ```

> [!TIP]
> Può essere opportuno aggiungere questi comandi al file ~/.bashrc per non dover impostare la variabile di ambiente a ogni accesso.
>

## <a name="set-up-the-azure-cross-platform-cli"></a>Configurare l'interfaccia della riga di comando multipiattaforma di Azure
L'[interfaccia della riga di comando multipiattaforma di Azure][azure-xplat-cli-github] include comandi per l'interazione con entità di Service Fabric, come cluster e applicazioni. Dato che è basata su Node.js, prima di procedere con le istruzioni riportate di seguito [assicurarsi di aver installato Node][install-node].

1. Clonare il repository di GitHub nel computer di sviluppo.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. Passare al repository clonato e installare le dipendenze dell'interfaccia della riga di comando usando Node Package Manager (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. Creare un collegamento simbolico dalla cartella bin/azure del repository clonato a /usr/bin/azure in modo che venga aggiunta al percorso dell'utente e che i comandi siano disponibili da qualsiasi directory.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. Abilitare infine il completamento automatico dei comandi di Service Fabric.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> I comandi di Service Fabric non sono ancora disponibili nell'interfaccia della riga di comando di Azure 2.0.


## <a name="set-up-a-local-cluster"></a>Configurare un cluster locale
Se tutti gli elementi sono stati installati, sarà possibile avviare un cluster locale.

1. Eseguire lo script di configurazione del cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Aprire un Web browser e andare su http://localhost:19080/Explorer. Se il cluster è stato avviato, verrà visualizzato il dashboard di Service Fabric Explorer.

    ![Service Fabric Explorer in Linux][sfx-linux]

A questo punto, è possibile distribuire pacchetti di applicazione di Service Fabric predefiniti o nuovi pacchetti basati su contenitori o eseguibili guest. Per creare nuovi servizi usando gli SDK per Java o .NET Core, seguire le procedure di configurazione facoltative riportate nelle sezioni successive.


> [!NOTE]
> In Linux non sono supportati cluster autonomi. Nell'anteprima sono supportati solo cluster con un solo computer e più computer Linux di Azure.
>

## <a name="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models"></a>Installare Java SDK (facoltativo, per usare i modelli di programmazione Java)
Java SDK offre le librerie e i modelli necessari per creare servizi di Service Fabric con Java.

1. Installare il pacchetto Java SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Eseguire lo script di configurazione dell'SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```
## <a name="install-the-eclipse-neon-plugin-optional"></a>Installare il plug-in Eclipse Neon (facoltativo)

È possibile installare il plug-in Eclipse per Service Fabric dall'**IDE di Eclipse per sviluppatori Java**. È possibile usare Eclipse per creare applicazioni contenitore e applicazioni eseguibili guest di Service Fabric in aggiunta alle applicazioni Java di Service Fabric.

> [!NOTE]
> L'installazione di Java SDK è un prerequisito per usare il plug-in Eclipse, anche se solo per creare e distribuire applicazioni contenitore ed eseguibili guest.
>

1. In Eclipse assicurarsi che sia installata la versione più recente di Buildship (1.0.17 o versione successiva) e di Eclipse **Neon**. Per controllare le versioni dei componenti installati, scegliere **? > Dettagli installazione**. È possibile aggiornare Buildship seguendo le istruzioni riportate [qui][buildship-update].
2. Per installare il plug-in Service Fabric, scegliere **Help > Install New Software...** (? > Installa nuovo software...).
3. Nella casella di testo "Work with" (Usa) immettere: http://dl.windowsazure.com/eclipse/servicefabric
4. Fare clic su Aggiungi.
    ![Plug-in Eclipse][sf-eclipse-plugin]
5. Scegliere il plug-in Service Fabric e fare clic su Next (Avanti).
6. Procedere con l'installazione e accettare il contratto di licenza con l'utente finale.

Se il plug-in Eclipse per Service Fabric è già installato, verificare che la versione sia la più recente. Per controllare, selezionare ``Help => Installation Details`` e cercare Service Fabric nell'elenco dei plug-in installati. Se è disponibile una versione più recente, selezionare l'aggiornamento. 

Per altre informazioni, vedere l'[introduzione a Service Fabric con Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models"></a>Installare .NET Core SDK (facoltativo, per usare i modelli di programmazione .NET Core)
.NET Core SDK offre le librerie e i modelli necessari per creare servizi di Service Fabric con .NET Core multipiattaforma.

1. Installare il pacchetto .NET Core SDK.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Eseguire lo script di configurazione dell'SDK.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>Aggiornamento di SDK e runtime

Per aggiornare l'SDK e il runtime alla versione più recente, seguire questa procedura (nell'elenco che non si vuole aggiornare o installare, rimuovere gli SDK):

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
   ```
   
> [!NOTE]
> L'aggiornamento dei pacchetti precedenti potrebbe provocare l'arresto del cluster di sviluppo locale. Riavviare il cluster locale dopo un aggiornamento seguendo le istruzioni disponibili in questa pagina
>
>

Per aggiornare l'interfaccia della riga di comando, passare alla directory in cui è stata clonata l'interfaccia della riga di comando ed eseguire `git pull` per l'aggiornamento.  Eventuali passaggi aggiuntivi necessari per l'aggiornamento saranno specificati nelle note sulla versione. 


## <a name="next-steps"></a>Passaggi successivi
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md) (Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse)
* [Creare la prima applicazione CSharp in Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Preparare l'ambiente di sviluppo in OSX](service-fabric-get-started-mac.md)
* [Usare l'interfaccia della riga di comando di Azure per gestire le applicazioni di Service Fabric](service-fabric-azure-cli.md)
* [Differenze in Service Fabric tra Windows e Linux](service-fabric-linux-windows-differences.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

