<properties
   pageTitle="Configurare l'ambiente di sviluppo in Linux | Microsoft Azure"
   description="Installare il runtime e l'SDK e creare un cluster di sviluppo locale in Linux. Al termine della configurazione, sarà possibile iniziare a sviluppare applicazioni."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# Preparare l'ambiente di sviluppo in Linux


> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Per distribuire ed eseguire [applicazioni di Azure Service Fabric](service-fabric-application-model.md) in un computer di sviluppo Linux, installare il runtime e l'SDK comune. È anche possibile installare SDK facoltativi per Java e .NET Core.

## Prerequisiti
### Versioni del sistema operativo supportate
Per lo sviluppo, sono supportati i sistemi operativi seguenti:

- Ubuntu 16.04 (Xenial Xerus)

## Aggiornare le origini apt

Per installare l'SDK e il pacchetto di runtime associato tramite apt-get, è prima necessario aggiornare le origini apt.

1. Aprire un terminale.
2. Aggiungere il repository di Service Fabric all'elenco di origini.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Aggiungere la nuova chiave GPG al keyring di apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Aggiornare l'elenco dei pacchetti in base ai repository appena aggiunti.

    ```bash
    sudo apt-get update
    ```

## Installare e configurare l'SDK

Dopo aver aggiornato le origini, è possibile installare l'SDK.

1. Installare il pacchetto Service Fabric SDK. Verrà richiesto di confermare l'installazione e di accettare un contratto di licenza.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Eseguire lo script di configurazione dell'SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## Configurare l'interfaccia della riga di comando multipiattaforma di Azure

L'[interfaccia della riga di comando multipiattaforma di Azure][azure-xplat-cli-github] include comandi per l'interazione con entità di Service Fabric, come cluster e applicazioni. Dato che è basata su Node.js, prima di procedere con le istruzioni riportate di seguito [assicurarsi di avere installato Node][install-node].

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

## Configurare un cluster locale

Se tutti gli elementi sono stati installati, sarà possibile avviare un cluster locale.

1. Eseguire lo script di configurazione del cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Aprire un Web browser e passare a http://localhost:19080/Explorer. Se il cluster è stato avviato, verrà visualizzato il dashboard di Service Fabric Explorer.

    ![Service Fabric Explorer in Linux][sfx-linux]

A questo punto, è possibile distribuire pacchetti di applicazione di Service Fabric predefiniti o nuovi pacchetti basati su contenitori o eseguibili guest. Per creare nuovi servizi usando gli SDK per Java o .NET Core, seguire le procedure di configurazione facoltative riportate di seguito.

## Installare Java SDK e il plug-in Eclipse Neon (facoltativo)

Java SDK offre le librerie e i modelli necessari per creare servizi di Service Fabric con Java.

1. Installare il pacchetto Java SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Eseguire lo script di configurazione dell'SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

È possibile installare il plug-in Eclipse per Service Fabric dall'IDE di Eclipse Neon.

1. In Eclipse assicurarsi che sia installata la versione 1.0.17 di Buildship o una versione successiva. Per controllare le versioni dei componenti installati, scegliere **Help > Installation Details** (? > Dettagli installazione). È possibile aggiornare Buildship seguendo le istruzioni riportate [qui][buildship-update].

2. Per installare il plug-in Service Fabric, scegliere **Help > Install New Software** (? > Installa nuovo software).

3. Nella casella di testo "Work with" (Usa) immettere http://dl.windowsazure.com/eclipse/servicefabric.

4. Fare clic su Aggiungi.

    ![Plug-in Eclipse][sf-eclipse-plugin]

5. Scegliere il plug-in Service Fabric e fare clic su Next (Avanti).

6. Procedere con l'installazione e accettare il contratto di licenza con l'utente finale.

## Installare .NET Core SDK (facoltativo)

.NET Core SDK offre le librerie e i modelli necessari per creare servizi di Service Fabric con .NET Core multipiattaforma.

1. Installare il pacchetto .NET Core SDK.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Eseguire lo script di configurazione dell'SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## Passaggi successivi

- [Creare la prima applicazione Java in Linux](service-fabric-create-your-first-linux-application-with-java.md)

- [Preparare l'ambiente di sviluppo in OSX](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

<!---HONumber=AcomDC_0928_2016-->