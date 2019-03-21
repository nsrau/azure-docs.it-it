---
title: Configurare un cluster Linux di Azure Service Fabric in Windows| Microsoft Docs
description: In questo articolo si descrive come configurare un cluster Linux di Azure Service Fabric in esecuzione su computer di sviluppo Windows. Ciò è particolarmente utile per lo sviluppo multipiattaforma.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819736"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurazione di un cluster Linux di Service Fabric sul computer di sviluppo Windows

Nel presente documento si spiega la configurazione di un Service Fabric Linux su computer di sviluppo Windows. La configurazione di un cluster Linux è utile per testare rapidamente le applicazioni di destinazione per i cluster Linux che sono però sviluppate su un computer Windows.

## <a name="prerequisites"></a>Prerequisiti
I cluster di Service Fabric basati su Linux non vengono eseguiti in modo nativo in Windows. Per eseguire un cluster di Service Fabric locale, viene offerta un'immagine del contenitore Docker preconfigurata. Prima di iniziare, sono necessari:

* Almeno 4 GB di RAM
* Ultima versione di [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker deve essere in esecuzione in modalità di Linux

>[!TIP]
> * Per installare Docker nel computer Windows, è possibile seguire la procedura descritta nella [documentazione](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) ufficiale di Docker. 
> * Al termine dell'installazione, verificare che sia stata eseguita correttamente seguendo la procedura descritta [qui](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Creare un contenitore locale e configurare Service Fabric
Per configurare un contenitore Docker locale ed eseguirvi un cluster di Service Fabric, seguire questa procedura in PowerShell:


1. Aggiornare la configurazione del daemon Docker nell'host con il codice seguente e riavviare il daemon Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Per eseguire l'aggiornamento, è consigliabile andare all'icona di Docker > Impostazioni > Daemon > Avanzate. Riavviare quindi il daemon Docker per rendere effettive le modifiche. 

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

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Questa operazione richiederà del tempo, ma è necessaria eseguirla solo una volta.

4. Ora è possibile iniziare rapidamente una copia locale di Service Fabric, quando necessario, eseguendo:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Specificare un nome per l'istanza del contenitore per poterla gestire in modo più leggibile. 
    >
    >Se l'applicazione è in ascolto su determinate porte, le porte devono essere specificate usando tag `-p` aggiuntivi. Se ad esempio l'applicazione è in ascolto sulla porta 8080, aggiungere il tag `-p` seguente:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Il cluster richiederà poco tempo per l'avvio. È possibile visualizzare i log usando il comando seguente o passare al dashboard per visualizzare l'integrità del cluster [http://localhost:19080](http://localhost:19080):

    ```powershell 
    docker logs sftestcluster
    ```

6. Dopo il completamento del passaggio 5, è possibile accedere a ``http://localhost:19080`` dal computer Windows e visualizzare Service Fabric Explorer. A questo punto, è possibile connettersi a questo cluster usando qualunque strumento del computer di sviluppo Windows e distribuire l'applicazione destinata i cluster Service Fabric di Linux. 

    > [!NOTE]
    > Il plug-in per Eclipse non è attualmente supportato in Windows. 

7. Al termine, arrestare e pulire il contenitore con questo comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitazioni note 
 
 Le seguenti sono limitazioni note del cluster locale eseguito in un contenitore per Mac: 
 
 * Il servizio DNS non viene eseguito e non è supportato [Problema 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Passaggi successivi
* Introduzione a [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Fare riferimento ad altri [esempi di Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
