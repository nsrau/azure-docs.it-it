---
title: Distribuire un'applicazione Spring Boot in Azure Service Fabric | Microsoft Docs
description: In questa guida introduttiva viene distribuita un'applicazione Spring Boot per Azure Service Fabric usando un'applicazione Spring Boot di esempio.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 6c84b60018ec03b7f9bc572db9181b8a47a0c595
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365407"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>Guida introduttiva: Distribuire un'applicazione Spring Boot Java in Azure
Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori. 

Questa guida introduttiva illustra come distribuire un'applicazione Spring Boot in Service Fabric con un computer di sviluppo Mac o Linux usando il progetto di esempio [Introduzione](https://spring.io/guides/gs/spring-boot/) disponibile nel sito Web di Spring. Usando i comuni strumenti da riga di comando, questa guida introduttiva fornisce le istruzioni per distribuire l'applicazione Spring Boot di esempio come applicazione di Service Fabric. Al termine del processo, il progetto di esempio Introduzione a Spring Boot sarà in esecuzione in Service Fabric. 

![Screenshot dell'applicazione](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

In questa guida introduttiva si apprende come:

* Distribuire un'applicazione Spring Boot in Service Fabric
* Distribuire l'applicazione nel cluster locale 
* Distribuire l'applicazione in un cluster in Azure
* Scalare orizzontalmente l'applicazione in più nodi
* Eseguire il failover del servizio senza compromettere la disponibilità

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida introduttiva:
1. Installare Service Fabric SDK e l'interfaccia della riga di comando di Service Fabric

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

2. [Installare Git](https://git-scm.com/)
3. Installare Yeoman

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. Configurare l'ambiente Java

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Scaricare l'esempio
In una finestra del terminale eseguire il comando seguente per clonare l'app di esempio Introduzione a Spring Boot nel computer locale.
```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Compilare l'applicazione Spring Boot 
1. All'interno della directory `gs-spring-boot/complete` eseguire il comando seguente per compilare l'applicazione. 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Creare il pacchetto dell'applicazione Spring Boot 
1. All'interno della directory `gs-spring-boot` nel clone, eseguire il comando `yo azuresfguest`. 

2. Rispondere alle richieste specificando le informazioni seguenti. 

    ![Voci di Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. Nella cartella `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` creare un file denominato `entryPoint.sh`. Aggiungere il testo seguente al file `entryPoint.sh`. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

4. Aggiungere la risorsa **Endpoints** nel file `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml`.

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    Il file **ServiceManifest.xml** sarà ora simile al seguente: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

In questa fase è stata creata un'applicazione di Service Fabric per il progetto di esempio Introduzione a Spring Boot che è possibile distribuire in Service Fabric.

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale
1. Per avviare il cluster locale nei computer Ubuntu, eseguire il comando seguente:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Se si usa un Mac, avviare il cluster locale dall'immagine Docker (si presuppone che siano stati seguiti i [prerequisiti](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) per configurare il cluster locale per Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
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

I cluster di entità sono cluster di Service Fabric gratuiti disponibili per un periodo di tempo limitato, ospitati in Azure ed eseguiti dal team di Service Fabric. È possibile usare i cluster di entità per distribuire le applicazioni e ottenere informazioni sulla piattaforma. Il cluster usa un solo certificato autofirmato per la sicurezza da nodo a nodo e da client a nodo.

Eseguire l'accesso e aggiungere un [cluster Linux](http://aka.ms/tryservicefabric). Scaricare il certificato PFX nel computer facendo clic sul collegamento **PFX**. Fare clic sul collegamento **ReadMe** (Leggimi) per trovare la password del certificato e le istruzioni per configurare diversi ambienti per usare il certificato. Tenere aperte entrambe le pagine **Welcome** (Benvenuto) e **ReadMe** (Leggimi) perché si useranno alcune delle istruzioni nei passaggi seguenti. 

> [!Note]
> È disponibile un numero limitato di cluster di entità ogni ora. Se viene restituito un errore quando si prova a registrarsi a un cluster di entità, è possibile attendere e riprovare in seguito oppure seguire i passaggi descritti in [Creare un cluster di Service Fabric in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) per creare un cluster nella propria sottoscrizione. 
>
> Il servizio Spring Boot è configurato per l'ascolto del traffico in ingresso sulla porta 8080. Assicurarsi che tale porta sia aperta nel cluster. Se si usa il cluster di entità, questa porta è aperta.
>

Service Fabric fornisce numerosi strumenti che è possibile usare per gestire un cluster e le applicazioni:

- Service Fabric Explorer, uno strumento basato su browser.
- Interfaccia della riga di comando di Service Fabric, la cui esecuzione si basa sull'interfaccia della riga di comando di Azure 2.0.
- Comandi di PowerShell. 

In questa guida introduttiva si usano l'interfaccia della riga di comando di Service Fabric e Service Fabric Explorer. 

Per usare l'interfaccia della riga di comando, è necessario creare un file PEM basato sul file PFX scaricato. Per convertire il file, usare il comando seguente. Per i cluster di entità, è possibile copiare un comando specifico nel file PFX dalle istruzioni della pagina **ReadMe** (Leggimi).

```bash
openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
``` 

Per usare Service Fabric Explorer, è necessario importare il file PFX del certificato scaricato dal sito Web del cluster di entità nell'archivio certificati (Windows o Mac) oppure nel browser stesso (Ubuntu). È necessaria la password della chiave privata PFX, che è possibile ottenere dalla pagina **ReadMe** (Leggimi).

Usare il metodo preferito per importare il certificato nel sistema. Ad esempio: 

- In Windows: fare doppio clic sul file PFX e seguire i prompt per installare il certificato nell'archivio personale, `Certificates - Current User\Personal\Certificates`. In alternativa, è possibile usare il comando di PowerShell nelle istruzioni di **ReadMe** (Leggimi).
- In Mac: fare doppio clic sul file PFX e seguire i prompt per installare il certificato nel keychain.
- In Ubuntu: Mozilla Firefox è il browser predefinito in Ubuntu 16.04. Per importare il certificato in Firefox, fare clic sul pulsante di menu nell'angolo in alto a destra del browser, quindi fare clic su **Opzioni**. Nella pagina **Preferenze** usare la casella di ricerca per cercare "certificati". Fare clic su **Mostra certificati**, selezionare la scheda **Certificati personali**, fare clic su **Importa** e seguire i prompt per importare il certificato.
 
   ![Installare il certificato in Firefox](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png) 


### <a name="deploy-the-application-using-cli"></a>Distribuire l'applicazione tramite l'interfaccia della riga di comando
Ora che l'applicazione e il cluster sono pronti, è possibile procedere alla distribuzione in un cluster direttamente dalla riga di comando.

1. Passare alla cartella `gs-spring-boot/SpringServiceFabric`.
2. Eseguire il comando seguente per connettersi al cluster di Azure. 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Eseguire lo script `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Aprire il Web browser e accedere all'applicazione tramite **http://\<ConnectionIPOrUrl>:8080**. 

    ![Front-end locale dell'applicazione](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
È ora possibile accedere all'applicazione Spring Boot in esecuzione in un cluster di Service Fabric in Azure.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Ridimensionare applicazioni e servizi in un cluster
I servizi possono essere facilmente ridimensionati in un cluster per supportare le modifiche del carico sui servizi. È possibile ridimensionare un servizio modificando il numero di istanze in esecuzione nel cluster. Sono disponibili diversi sistemi per garantire il ridimensionamento dei servizi. È ad esempio possibile usare gli script o i comandi dell'interfaccia della riga di comando di Service Fabric (sfctl). I passaggi seguenti usano Service Fabric Explorer.

Service Fabric Explorer è in esecuzione in tutti i cluster di Service Fabric ed è accessibile da un browser passando alla porta di gestione HTTP (19080) del cluster, ad esempio `http://localhost:19080`.

Per ridimensionare il servizio front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://localhost:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo **fabric:/SpringServiceFabric/SpringGettingStarted** nella visualizzazione struttura ad albero e scegliere **Scale Service** (Ridimensiona servizio).

    ![Ridimensionamento dei servizi in Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    È ora possibile scegliere di modificare il numero di istanze del servizio.

3. Impostare il numero su **3** e fare clic su **Scale Service** (Ridimensiona servizio).

    Di seguito è illustrato un modo alternativo per modificare il numero di istanze del servizio tramite la riga di comando.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Fare clic sul nodo **fabric:/SpringServiceFabric/SpringGettingStarted** nella visualizzazione struttura ad albero ed espandere il nodo della partizione (rappresentato da un GUID).

    ![Ridimensionamento dei servizi in Service Fabric Explorer completato](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Il servizio ha tre istanze. Nella visualizzazione ad albero viene indicato in quali nodi vengono eseguite.

Con questa semplice attività di gestione, sono state raddoppiate le risorse disponibili per il servizio front-end per l'elaborazione del carico utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="fail-over-services-in-a-cluster"></a>Servizi di failover in un cluster 
Per dimostrare il failover del servizio, viene simulato il riavvio di un nodo tramite Service Fabric Explorer. Verificare che sia in esecuzione una sola istanza del servizio. 

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://localhost:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo in esecuzione sull'istanza del servizio e riavviare il nodo. 

    ![Riavvio del nodo con Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. L'istanza del servizio verrà spostata in un altro nodo e nell'applicazione non si verificheranno tempi di inattività. 

    ![Riavvio del nodo eseguito con successo con Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come:

* Distribuire un'applicazione Spring Boot in Service Fabric
* Distribuire l'applicazione nel cluster locale 
* Distribuire l'applicazione in un cluster in Azure
* Scalare orizzontalmente l'applicazione in più nodi
* Eseguire il failover del servizio senza compromettere la disponibilità

Per altre informazioni sull'uso di app Java in Service Fabric, continuare con l'esercitazione sulle app Java.

> [!div class="nextstepaction"]
> [Distribuire un'app Java](./service-fabric-tutorial-create-java-app.md)
