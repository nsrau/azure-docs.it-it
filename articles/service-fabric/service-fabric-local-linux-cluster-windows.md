---
title: Configurare un cluster Linux di Azure Service Fabric in Windows| Microsoft Docs
description: "In questo articolo si descrive come configurare un cluster Linux di Azure Service Fabric in esecuzione su computer di sviluppo Windows. Ciò è particolarmente utile per lo sviluppo multipiattaforma."
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: db6ad8b83ce34a8b86de822bc074e8a13345a1b4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurazione di un cluster Linux di Service Fabric sul computer di sviluppo Windows

Nel presente documento si spiega la configurazione di un Service Fabric Linux su computer di sviluppo Windows. La configurazione di un cluster Linux è utile per testare rapidamente le applicazioni di destinazione per i cluster Linux che sono però sviluppate su un computer Windows.

## <a name="prerequisites"></a>prerequisiti
I cluster di Service Fabric basati su Linux non vengono eseguiti in modo nativo in Windows. Per eseguire un cluster di Service Fabric locale, viene offerta un'immagine del contenitore Docker preconfigurata. Prima di iniziare, sono necessari:

* Almeno 4 GB di RAM
* Ultima versione di [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)

>[!TIP]
> * Per installare Docker nel computer Windows, è possibile seguire la procedura descritta nella [documentazione](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) ufficiale di Docker. 
> * Al termine dell'installazione, verificare che sia stata eseguita correttamente seguendo la procedura descritta [qui](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Creare un contenitore locale e configurare Service Fabric
Per configurare un contenitore Docker locale ed eseguirvi un cluster di Service Fabric, seguire questa procedura:

1. Eseguire il pull dell'immagine dal repository dell'hub Docker:

    ```powershell
    docker pull microsoft/service-fabric-onebox
    ```

2. Aggiornare la configurazione del daemon Docker nell'host con il codice seguente e riavviare il daemon Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Per eseguire l'aggiornamento, è consigliabile andare all'icona di Docker > Impostazioni > Daemon > Avanzate. Riavviare quindi il daemon Docker per rendere effettive le modifiche. 

3. Avviare un'istanza del contenitore onebox di Service Fabric con l'immagine:

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    > * Specificando un nome per l'istanza del contenitore, è possibile gestirla in modo più leggibile. 
    > * Se l'applicazione è in ascolto su determinate porte, deve essere specificata usando tag -p aggiuntivi. Se ad esempio l'applicazione è in ascolto sulla porta 8080, eseguire docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox

4. Accedere al contenitore Docker in modalità SSH interattiva:

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Eseguire lo script di configurazione, che recupererà le dipendenze necessarie e successivamente avvierà il cluster nel contenitore.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Dopo il completamento del passaggio 5, è possibile accedere a ``http://localhost:19080`` dal computer Windows e visualizzare Service Fabric Explorer. A questo punto, è possibile connettersi a questo cluster usando qualunque strumento del computer di sviluppo Windows e distribuire l'applicazione destinata i cluster Service Fabric di Linux. 

    > [!NOTE]
    > Il plug-in per Eclipse non è attualmente supportato in Windows. 

## <a name="next-steps"></a>Passaggi successivi
* Introduzione a [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Fare riferimento ad altri [esempi di Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png