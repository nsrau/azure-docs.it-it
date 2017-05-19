---
title: Creare un&quot;app contenitore di Azure Service Fabric | Microsoft Docs
description: Creare la prima app contenitore di Azure Service Fabric.  Compilare un&quot;immagine Docker con l&quot;app, effettuare il push dell&quot;immagine in un registro contenitori, compilare e distribuire un&quot;app contenitore di Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>Creare la prima app contenitore di Service Fabric
Per eseguire un'app esistente in un contenitore Windows in un cluster di Service Fabric non è necessario apportare modifiche all'app. Questa guida introduttiva illustra come creare un'immagine Docker contenente un'app Web, effettuare il push della nuova immagine nel registro contenitori di Azure, creare un'app contenitore di Service Fabric e distribuire l'app contenitore in un cluster di Service Fabric.  L'articolo presuppone una conoscenza di base di Docker. Per informazioni su Docker, vedere [Docker overview](https://docs.docker.com/engine/understanding-docker/) (Panoramica su Docker).

## <a name="prerequisites"></a>Prerequisiti
Un computer di sviluppo che esegue:
* Visual Studio 2015 o Visual Studio 2017.
* [SDK e strumenti di Service Fabric](service-fabric-get-started.md).
*  Docker per Windows.  [Scaricare Docker CE per Windows (stabile)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Dopo aver installato e avviato Docker, fare clic con il pulsante destro del mouse sull'icona nell'area di notifica e selezionare **Switch to Windows containers** (Passa ai contenitori Windows). Questa operazione è necessaria per eseguire le immagini Docker basate su Windows.

Un cluster di Windows con tre o più nodi in esecuzione in Windows Server 2016 con Contenitori. A questo scopo, [creare un cluster](service-fabric-get-started-azure-cluster.md) o [provare Service Fabric gratuitamente](http://tryazureservicefabric.westus.cloudapp.azure.com/). 

Un registro all'interno del registro contenitori di Azure. A questo scopo, [creare un registro contenitori](../container-registry/container-registry-get-started-portal.md) nella sottoscrizione di Azure. 

## <a name="create-a-simple-web-app"></a>Creare un'app Web semplice
Raccogliere tutte le risorse da caricare in un'immagine Docker in un'unica posizione. Per questa guida introduttiva, creare un'app Web "Hello World" nel computer di sviluppo.

1. Creare una directory, ad esempio *c:\temp\helloworldapp*.
2. Creare una sottodirectory *c:\temp\helloworldapp\content*.
3. Creare un file *index.html* in *c:\temp\helloworldapp\content*.
4. Modificare il file *index.html* e aggiungere la riga seguente:
    ```
    <h1>Hello World!</h1>
    ```
5. Salvare le modifiche a *index.html*.

## <a name="build-the-docker-image"></a>Compilare l'immagine Docker
Compilare un'immagine in base all'[immagine microsft/iis](https://hub.docker.com/r/microsoft/iis/) contenuta nell'hub Docker. L'immagine microsoft/iis deriva dall'immagine di base del sistema operativo Windows Server Core e contiene Internet Information Services (IIS).  L'esecuzione di questa immagine nel contenitore avvia automaticamente IIS e i siti Web installati.

Definire l'immagine Docker in un file Dockerfile. Il file Dockerfile contiene istruzioni per la compilazione dell'immagine e il caricamento dell'app da eseguire. Il file Dockerfile rappresenta l'input per il comando ```docker build``` che crea l'immagine. 

1. Creare un file *Dockerfile* senza alcuna estensione in *c:\temp\helloworldapp* e aggiungere quanto segue:

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    Questo file Dockerfile non contiene alcun comando ```ENTRYPOINT```, perché non è necessario. Quando si esegue Windows Server con IIS, il processo IIS rappresenta il punto di ingresso, configurato per l'avvio nell'immagine di base.

    Per altre informazioni, vedere [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) (Informazioni di riferimento su Dockerfile).

2. Eseguire il comando ```docker build``` per creare l'immagine che esegue l'app Web. Aprire una finestra di PowerShell e passare a *c:\temp\helloworldapp*. Eseguire il comando seguente:

    ```
    docker build -t helloworldapp .
    ```
    Questo comando compila la nuova immagine usando le istruzioni contenute nel file Dockerfile e denomina l'immagine "helloworldapp" , ovvero le assegna un tag -t. La compilazione di un'immagine effettua il pull dell'immagine di base dall'hub Docker e crea una nuova immagine che aggiunge l'app sopra l'immagine di base.  L'[immagine microsft/iis](https://hub.docker.com/r/microsoft/iis/) e le immagini di base del sistema operativo hanno una dimensione di 10,5 GB ed è necessario attendere qualche minuto per il download e l'estrazione nel computer di sviluppo.  Nel frattempo è possibile fare una pausa.  Il download richiede meno tempo se l'immagine di base del sistema operativo è stata estratta in precedenza nel computer di sviluppo.

3. Al termine dell'esecuzione del comando di compilazione, eseguire il comando `docker images` per visualizzare le informazioni sulla nuova immagine:

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>Verificare l'esecuzione dell'immagine in locale
Verifica l'immagine in locale prima di effettuarne il push nel registro contenitori.  

1. Avviare il contenitore usando il comando ```docker run```:

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    *name* assegna un nome al contenitore in esecuzione, anziché l'ID contenitore.

2. Dopo aver avviato il contenitore, trovare il relativo indirizzo IP per consentire la connessione al contenitore in esecuzione da un browser:
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. Connettersi al contenitore in esecuzione.  Aprire un Web browser puntando all'indirizzo IP restituito sulla porta 8000, ad esempio "http://172.31.194.61:8000". Verrà visualizzata l'intestazione "Hello World!" nel browser.

4. Per arrestare il contenitore, eseguire:

    ```
    docker stop my-web-site
    ```

5. Per eliminare il contenitore dal computer di sviluppo, eseguire:

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>Effettuare il push dell'immagine nel registro contenitori
Dopo aver verificato l'esecuzione del contenitore nel computer di sviluppo, effettuare il push dell'immagine nel registro all'interno del registro contenitori di Azure.

1. Eseguire ``docker login`` per accedere al registro di contenitori con le [credenziali del registro](../container-registry/container-registry-authentication.md).

    L'esempio seguente passa l'ID e la password di un'[entità servizio](../active-directory/active-directory-application-objects.md) di Azure Active Directory. Ad esempio, è possibile che sia stata assegnata un'entità servizio al registro per uno scenario di automazione.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. Il comando seguente crea un tag, o alias, dell'immagine, con un percorso completo del registro. Questo esempio inserisce l'immagine dello spazio dei nomi ```samples``` per evitare confusione nella radice del registro.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  Effettuare il push dell'immagine nel registro contenitori:

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>Compilare e creare il pacchetto del servizio in contenitori in Visual Studio
L'SDK e gli strumenti di Service Fabric offrono un modello di servizio che permette di distribuire un contenitore in un cluster di Service Fabric.

1. Avviare Visual Studio.  Selezionare **File** > **Nuovo** > **Progetto**.
2. Selezionare l'**applicazione di Service Fabric**, denominarla "MyFirstContainer" e fare clic su **OK**.
3. Scegliere **Contenitore guest** dall'elenco dei **modelli di servizio**.
4. In **Nome immagine** immettere "myregistry.azurecr.io/samples/helloworldapp", vale a dire l'immagine di cui è stato effettuato il push nel repository dei contenitori. 
5. Assegnare un nome al servizio e fare clic su **OK**.
6. Se il servizio in contenitori richiede un endpoint per la comunicazione, è ora possibile aggiungere il protocollo, la porta e il tipo a un ```Endpoint``` nel file ServiceManifest.xml. Per questa guida introduttiva, il servizio in contenitori è in ascolto sulla porta 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    Se si specifica ```UriScheme```, l'endpoint del contenitore viene registrato automaticamente con Service Fabric Naming Service per l'individuazione. Alla fine di questo articolo viene fornito un file di esempio completo di ServiceManifest.xml. 
7. Configurare il mapping tra la porta del contenitore e la porta dell'host specificando i criteri ```PortBinding``` in ```ContainerHostPolicies``` nel file ApplicationManifest.xml.  Per questa guida introduttiva, il valore di ```ContainerPort``` è 8000, vale a dire che il contenitore espone la porta 8000, come specificato nel file Dockerfile, e il valore di ```EndpointRef``` è "Guest1TypeEndpoint", ovvero l'endpoint definito nel manifesto del servizio.  Per le richieste in ingresso per il servizio sulla porta 80 viene eseguito il mapping alla porta 8000 del contenitore.  Se il contenitore deve eseguire l'autenticazione con un repository privato, aggiungere ```RepositoryCredentials```.  Per questa guida introduttiva, aggiungere il nome dell'account e la password per il registro contenitori myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    Alla fine di questo articolo viene fornito un file di esempio completo di ApplicationManifest.xml.
8. Configurare l'endpoint di connessione del cluster in modo che sia possibile pubblicare l'app nel cluster.  L'endpoint di connessione del client è disponibile nel pannello Panoramica per il cluster nel [portale di Azure](https://portal.azure.com). In Esplora soluzioni aprire *Cloud.xml* in **MyFirstContainer**->**PublishProfiles**.  Aggiungere il nome del cluster e la porta di connessione a **ClusterConnectionParameters**.  ad esempio:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Salvare tutti i file e compilare il progetto.  

10. Per creare il pacchetto dell'app, fare clic su **MyFirstContainer** in Esplora soluzioni e selezionare **Pacchetto**. 

## <a name="deploy-the-container-app"></a>Distribuire l'app contenitore
1. Per pubblicare l'app, fare clic su **MyFirstContainer** in Esplora soluzioni e selezionare **Pubblica**.

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) è uno strumento basato sul Web che permette di analizzare e gestire applicazioni e nodi in un cluster di Service Fabric. Aprire un browser Web e passare a http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ per seguire la distribuzione dell'app.  L'app viene distribuita, ma rimane in stato di errore fino a quando l'immagine non viene scaricata nei nodi del cluster. L'operazione può richiedere del tempo, a seconda delle dimensioni dell'immagine:  ![Errore][1]

3. L'app è pronta quando è in stato ```Ready```:  ![Pronto][2]

4. Aprire un browser Web e passare a http://containercluster.westus2.cloudapp.azure.com. Verrà visualizzata l'intestazione "Hello World!" nel browser.

## <a name="clean-up"></a>Eseguire la pulizia
Mentre il cluster è in esecuzione, continuano a essere addebitati i relativi costi. È quindi consigliabile [eliminare il cluster](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  I [party cluster](http://tryazureservicefabric.westus.cloudapp.azure.com/) vengono eliminati automaticamente dopo alcune ore.

Dopo aver effettuato il push dell'immagine nel registro contenitori, è possibile eliminare l'immagine locale dal computer di sviluppo:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifesti di esempio completi del servizio e dell'applicazione di Service Fabric
Di seguito sono riportati i manifesti completi del servizio e dell'applicazione usati in questa guida introduttiva.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-containers-overview.md).
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
* Vedere gli [esempi di codice del contenitore di Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) in GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

