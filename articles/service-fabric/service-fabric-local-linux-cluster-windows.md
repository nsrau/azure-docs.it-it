---
title: Configurare un cluster Linux di Azure Service Fabric in Windows
description: In questo articolo si descrive come configurare un cluster Linux di Azure Service Fabric in esecuzione su computer di sviluppo Windows. Questo approccio è utile per lo sviluppo multipiattaforma.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: e25c6adf5e5f5101025aa883ef2ff9750c113a76
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164109"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurazione di un cluster Linux di Service Fabric sul computer di sviluppo Windows

Questo documento illustra come configurare un cluster Linux Service Fabric locale in un computer di sviluppo Windows. La configurazione di un cluster Linux è utile per testare rapidamente le applicazioni di destinazione per i cluster Linux che sono però sviluppate su un computer Windows.

## <a name="prerequisites"></a>Prerequisiti
I cluster di Service Fabric basati su Linux non vengono eseguiti in Windows, ma per abilitare la prototipazione multipiattaforma è stato fornito un contenitore Docker di Linux Service Fabric una casella del cluster, che può essere distribuito tramite Docker per Windows.

Prima di iniziare, sono necessari:

* Almeno 4 GB di RAM
* Ultima versione di [Docker per Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker deve essere in esecuzione in modalità contenitori Linux

>[!TIP]
> Per installare Docker nel computer Windows, seguire la procedura descritta nella [documentazione di Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions). Al termine, [verificare l'installazione](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Creare un contenitore locale e configurare Service Fabric
Per configurare un contenitore Docker locale e un cluster Service Fabric in esecuzione, eseguire la procedura seguente:


1. Aggiornare la configurazione del daemon Docker nell'host con il codice seguente e riavviare il daemon Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Il modo consigliato per aggiornare consiste nell'accedere a: 

    * Impostazioni > dell'icona Docker > motore Docker
    * Aggiungere i nuovi campi elencati sopra
    * Apply & restart-Riavviare il daemon Docker per rendere effettive le modifiche.

2. Avviare il cluster tramite PowerShell.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Per impostazione predefinita, verrà eseguito il pull dell'immagine con la versione più recente di Service Fabric. Per le revisioni particolari, visitare la pagina dell' [Hub Docker](https://hub.docker.com/r/microsoft/service-fabric-onebox/) .



3. Facoltativo: compilare l'immagine di Service Fabric estesa.

    In una nuova directory creare un file denominato `Dockerfile` per compilare l'immagine personalizzata:

    >[!NOTE]
    >È possibile adattare l'immagine precedente con una Dockerfile per aggiungere altri programmi o dipendenze nel contenitore.
    >Ad esempio, se si aggiunge `RUN apt-get install nodejs -y` sarà possibile supportare le applicazioni `nodejs` come eseguibili guest.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Per impostazione predefinita, verrà eseguito il pull dell'immagine con la versione più recente di Service Fabric. Per le revisioni particolari, visitare la pagina dell' [Hub Docker](https://hub.docker.com/r/microsoft/service-fabric-onebox/) .

    Per compilare l'immagine riutilizzabile dalla `Dockerfile` , aprire un terminale e `cd` al tenendo premuto il `Dockerfile` percorso seguente:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Questa operazione richiederà del tempo, ma è necessaria eseguirla solo una volta.

    A questo punto è possibile avviare rapidamente una copia locale di Service Fabric ogni volta che è necessaria eseguendo:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Specificare un nome per l'istanza del contenitore per poterla gestire in modo più leggibile. 
    >
    >Se l'applicazione è in ascolto su determinate porte, le porte devono essere specificate usando tag `-p` aggiuntivi. Se ad esempio l'applicazione è in ascolto sulla porta 8080, aggiungere il tag `-p` seguente:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. Il cluster richiederà poco tempo per l'avvio. È possibile visualizzare i log usando il comando seguente o passare al dashboard per visualizzare l'integrità del cluster `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

5. Al termine della distribuzione del cluster come osservato nel passaggio 4, è possibile passare a ``http://localhost:19080`` dal computer Windows per trovare il dashboard Service Fabric Explorer. A questo punto, è possibile connettersi a questo cluster usando gli strumenti del computer per sviluppatori Windows e distribuire le applicazioni destinate ai cluster Linux Service Fabric. 

    > [!NOTE]
    > Il plug-in per Eclipse non è attualmente supportato in Windows. 

6. Al termine, arrestare e pulire il contenitore con questo comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitazioni note 
 
 Le seguenti sono limitazioni note del cluster locale eseguito in un contenitore per Mac: 
 
 * Il servizio DNS non è in esecuzione e non è attualmente supportato nel contenitore. [#132 problema](https://github.com/Microsoft/service-fabric/issues/132)
 * Per l'esecuzione di app basate su contenitori è necessario eseguire SF in un host Linux. Le app contenitore annidate non sono attualmente supportate.

## <a name="next-steps"></a>Passaggi successivi
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* Introduzione a [Eclipse](./service-fabric-get-started-eclipse.md)
* Fare riferimento ad altri [esempi di Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* Informazioni sulle [Opzioni di supporto Service Fabric](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
