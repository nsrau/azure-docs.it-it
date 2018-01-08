---
title: Distribuire un'applicazione Spring Boot in Azure Service Fabric | Microsoft Docs
description: Distribuire un'applicazione Spring Boot in Azure Service Fabric usando Introduzione a Spring Boot.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 544f189e79733c6476bf71e9ce39ab5f35e3d032
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-a-spring-boot-application"></a>Distribuire un'applicazione Spring Boot
Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori. 

Questa guida introduttiva illustra come distribuire un'applicazione Spring Boot in Service Fabric usando il progetto di esempio [Introduzione](https://spring.io/guides/gs/spring-boot/) disponibile nel sito Web di Spring. Usando i comuni strumenti da riga di comando, questa guida introduttiva fornisce le istruzioni per distribuire l'applicazione Spring Boot di esempio come applicazione di Service Fabric. Al termine del processo, il progetto di esempio Introduzione a Spring Boot sarà in esecuzione in Service Fabric. 

![Screenshot dell'applicazione](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

In questa guida introduttiva si apprende come:

> [!div class="checklist"]
> * Distribuire un'applicazione Spring Boot in Service Fabric
> * Distribuire l'applicazione nel cluster locale 
> * Distribuire l'applicazione in un cluster in Azure
> * Scalare orizzontalmente l'applicazione in più nodi
> * Eseguire il failover del servizio senza compromettere la disponibilità

## <a name="prerequisites"></a>prerequisiti
Per completare questa guida introduttiva:
1. [Installare Service Fabric SDK e l'interfaccia della riga di comando di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Installare Git](https://git-scm.com/)
3. [Installare Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Configurare l'ambiente Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Scaricare l'esempio
In una finestra di comando eseguire il comando seguente per clonare l'app di esempio Introduzione a Spring Boot nel computer locale.
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Creare il pacchetto dell'applicazione Spring Boot 
1. All'interno della directory `gs-spring-boot` clonata, eseguire il comando `yo azuresfguest`. 

2. Rispondere alle richieste specificando le informazioni seguenti. 

    ![Voci di Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. Nella cartella `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` creare un file denominato `entryPoint.sh`. Aggiungere il codice seguente al file. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

In questa fase è stata creata un'applicazione di Service Fabric per il progetto di esempio Introduzione a Spring Boot che è possibile distribuire in Service Fabric.

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale
1. Avviare il cluster locale eseguendo il comando seguente:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    L'avvio del cluster locale può richiedere tempo. Per verificare se il cluster è in esecuzione, accedere a Service Fabric Explorer in **http://localhost:19080**. I cinque nodi integri indicano che il cluster locale è in esecuzione. 
    
    ![Cluster locale integro](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Passare alla cartella `gs-spring-boot/SpringServiceFabric`.
3. Eseguire il comando seguente per connettersi al cluster locale. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Eseguire lo script `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Aprire il Web browser preferito e accedere all'applicazione tramite **http://localhost:8080**. 

    ![Front-end locale dell'applicazione](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
È ora possibile accedere all'applicazione Spring Boot distribuita in un cluster di Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Distribuzione dell'applicazione in Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurare il cluster di Azure Service Fabric
Per distribuire l'applicazione in un cluster di Azure, creare un cluster personale.

I party cluster sono cluster di Service Fabric gratuiti disponibili per un periodo di tempo limitato, ospitati in Azure. Sono gestiti dal team di Service Fabric e consentono a chiunque di distribuirvi applicazioni e imparare a usare la piattaforma. Per ottenere l'accesso a un cluster di entità, [seguire le istruzioni](http://aka.ms/tryservicefabric). 

Per informazioni sulla creazione di un cluster, vedere l'articolo su come [creare un cluster di Service Fabric in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> Il servizio Spring Boot è configurato per l'ascolto del traffico in ingresso sulla porta 8080. Assicurarsi che tale porta sia aperta nel cluster. Se si usa il cluster di entità, questa porta è aperta.
>

### <a name="deploy-the-application-using-cli"></a>Distribuire l'applicazione tramite l'interfaccia della riga di comando
Ora che l'applicazione e il cluster sono pronti, è possibile procedere alla distribuzione in un cluster direttamente dalla riga di comando.

1. Passare alla cartella `gs-spring-boot/SpringServiceFabric`.
2. Eseguire il comando seguente per connettersi al cluster di Azure. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Se il cluster è protetto con un certificato autofirmato, il comando da eseguire sarà: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Eseguire lo script `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Aprire il Web browser preferito e accedere all'applicazione tramite **http://\<ConnectionIPOrUrl>:8080**. 

    ![Front-end locale dell'applicazione](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
È ora possibile accedere all'applicazione Spring Boot distribuita in un cluster di Service Fabric.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Ridimensionare applicazioni e servizi in un cluster
I servizi possono essere facilmente ridimensionati in un cluster per supportare le modifiche del carico sui servizi. È possibile ridimensionare un servizio modificando il numero di istanze in esecuzione nel cluster. Sono disponibili diversi sistemi per garantire la scalabilità dei servizi: è infatti possibile usare gli script o i comandi dell'interfaccia della riga di comando di Service Fabric (sfctl). In questo esempio si usa Service Fabric Explorer.

Service Fabric Explorer è in esecuzione in tutti i cluster di Service Fabric ed è accessibile da un browser, passando alla porta di gestione HTTP (19080) del cluster, ad esempio `http://localhost:19080`.

Per scalare il servizio front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://localhost:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo **fabric:/SpringServiceFabric/SpringGettingStarted** nella visualizzazione struttura ad albero e scegliere **Scale Service** (Ridimensiona servizio).

    ![Ridimensionamento dei servizi in Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    È ora possibile scegliere di modificare il numero di istanze del servizio.

3. Impostare il numero su **3** e fare clic su **Scale Service** (Ridimensiona servizio).

    Di seguito è illustrato un modo alternativo per modificare il numero di istanze del servizio tramite la riga di comando.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Fare clic sul nodo **fabric:/SpringServiceFabric/SpringGettingStarted** nella visualizzazione struttura ad albero ed espandere il nodo della partizione (rappresentato da un GUID).

    ![Ridimensionamento dei servizi in Service Fabric Explorer completato](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Il servizio ha tre istanze. Nella visualizzazione ad albero viene indicato in quali nodi vengono eseguite.

Con questa semplice attività di gestione vengono aumentate le risorse disponibili per il servizio Spring per l'elaborazione del carico dell'utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="fail-over-services-in-a-cluster"></a>Servizi di failover in un cluster 
Per dimostrare il failover del servizio, viene simulato il riavvio di un nodo tramite Service Fabric Explorer. Verificare che sia in esecuzione una sola istanza del servizio. 

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://localhost:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo in esecuzione sull'istanza del servizio e riavviare il nodo. 

    ![Riavvio del nodo con Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. L'istanza del servizio verrà spostata in un altro nodo e nell'applicazione non si verificheranno tempi di inattività. 

    ![Riavvio del nodo eseguito con successo con Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come:

> [!div class="checklist"]
> * Distribuire un'applicazione Spring Boot in Service Fabric
> * Distribuire l'applicazione nel cluster locale 
> * Distribuire l'applicazione in un cluster in Azure
> * Scalare orizzontalmente l'applicazione in più nodi
> * Eseguire il failover del servizio senza compromettere la disponibilità

* Altre informazioni sulla [creazione di microservizi Java usando i modelli di programmazione di Service Fabric](service-fabric-quickstart-java-reliable-services.md)
* Altre informazioni sulla [configurazione di integrazione e distribuzione continue tramite Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* Fare riferimento ad altri [esempi di Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)