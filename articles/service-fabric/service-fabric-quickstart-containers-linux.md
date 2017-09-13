---
title: Creare un'applicazione contenitore di Azure Service Fabric in Linux | Microsoft Docs
description: Creare la prima applicazione contenitore Linux in Azure Service Fabric.  Compilare un'immagine Docker con l'applicazione, eseguire il push dell'immagine in un registro contenitori e compilare e distribuire un'applicazione contenitore di Service Fabric.
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
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Distribuire un'applicazione contenitore Linux in Azure Service Fabric
Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili. 

Per eseguire un'applicazione esistente in un contenitore Linux in un cluster di Service Fabric non è necessario apportare modifiche all'applicazione. Questa guida introduttiva illustra come distribuire un'immagine del contenitore Docker predefinita in un'applicazione di Service Fabric. Al termine, sarà disponibile un contenitore nginx in esecuzione.  Questa guida introduttiva descrive la distribuzione di un contenitore Linux. Vedere [questa guida introduttiva](service-fabric-quickstart-containers.md) per distribuire un contenitore Windows.

![Nginx][nginx]

In questa guida introduttiva si apprende come:
> [!div class="checklist"]
> * Creare un pacchetto di un contenitore di un'immagine Docker
> * Configurare la comunicazione
> * Compilare l'applicazione di Service Fabric e creare il pacchetto
> * Distribuire l'applicazione del contenitore in Azure

## <a name="prerequisites"></a>Prerequisiti
Installare [Service Fabric SDK, l'interfaccia della riga di comando di Service Fabric e i generatori di modelli yeoman di Service Fabric](service-fabric-get-started-linux.md).
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Creare un pacchetto di un contenitore di un'immagine Docker con Yeoman
Service Fabric SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che semplifica la creazione dell'applicazione e l'aggiunta di un'immagine contenitore. 

Per creare un'applicazione contenitore di Service Fabric, aprire una finestra del terminale ed eseguire `yo azuresfcontainer`.  

Assegnare all'applicazione il nome "MyFirstContainer" e al servizio dell'applicazione il nome "MyContainerService".

Specificare il nome dell'immagine del contenitore "nginx:latest" (l'[immagine del contenitore nginx](https://hub.docker.com/r/_/nginx/) nell'hub Docker). 

Poiché per l'immagine è stato definito un punto di ingresso del carico di lavoro, è necessario specificare in modo esplicito i comandi di input. 

Specificare "1" come numero di istanze.

![Generatore Yeoman di Service Fabric per i contenitori][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurare la comunicazione e il mapping tra porta del contenitore e porta dell'host
Configurare un endpoint HTTP in modo che i client possano comunicare con il servizio.  Aprire il file *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* e dichiarare una risorsa endpoint nell'elemento **ServiceManifest**.  Aggiungere il protocollo, la porta e il nome. Per questa guida introduttiva il servizio è in ascolto sulla porta 80: 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
Se si specifica `UriScheme`, l'endpoint del contenitore viene registrato automaticamente con Service Fabric Naming Service per l'individuazione. Alla fine di questo articolo viene fornito un file di esempio completo di ServiceManifest.xml. 

Eseguire il mapping di un porta del contenitore a un `Endpoint` di servizio usando un criterio `PortBinding` in `ContainerHostPolicies` del file ApplicationManifest.xml.  Per questa guida introduttiva, il valore di `ContainerPort` è 80, perché il contenitore espone la porta 80, e il valore di `EndpointRef` è "myserviceTypeEndpoint", ossia l'endpoint definito prima nel manifesto del servizio.  Per le richieste in ingresso per il servizio sulla porta 80 viene eseguito il mapping alla porta 80 del contenitore.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Compilare l'applicazione di Service Fabric e creare il pacchetto
I modelli Yeoman di Service Fabric includono uno script di compilazione per [Gradle](https://gradle.org/), che può essere usato per compilare l'applicazione dal terminale. Salvare tutte le modifiche.  Per compilare l'applicazione e creare il pacchetto, eseguire questo comando:

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>Creare un cluster
Per distribuire l'applicazione in un cluster in Azure, è possibile scegliere di creare un proprio cluster oppure usare un cluster di entità.

I cluster di entità sono cluster Service Fabric gratuiti e disponibili per un periodo di tempo limitato ospitati in Azure e gestiti dal team di Service Fabric, in cui chiunque può distribuire applicazioni e ottenere informazioni sulla piattaforma. Per ottenere l'accesso a un cluster di entità, [seguire le istruzioni](http://aka.ms/tryservicefabric).  

Per informazioni sulla creazione di un cluster, vedere [Creare il primo cluster di Service Fabric di Azure](service-fabric-get-started-azure-cluster.md).

Prendere nota dell'endpoint connessione, che viene usato nel passaggio seguente.

## <a name="deploy-the-application-to-azure"></a>Distribuzione dell'applicazione in Azure
Dopo avere compilato l'applicazione, è possibile distribuirla nel cluster di Azure usando l'interfaccia della riga di comando di Service Fabric.

Connettersi al cluster di Service Fabric in Azure.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

Usare lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

```bash
./install.sh
```

Aprire un browser e passare a Service Fabric Explorer all'indirizzo http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer. Espandere il nodo delle applicazioni, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.

![Service Fabric Explorer][sfx]

Connettersi al contenitore in esecuzione.  Aprire un Web browser puntando all'indirizzo IP restituito sulla porta 80, ad esempio "lnxt10vkfz6.westus.cloudapp.azure.com:80". Nel browser verrà visualizzata la home page di nginx.

![Nginx][nginx]

## <a name="clean-up"></a>Eseguire la pulizia
Usare lo script di disinstallazione incluso nel modello per eliminare l'istanza dell'applicazione dal cluster e annullare la registrazione del tipo di applicazione.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifesti di esempio completi del servizio e dell'applicazione di Service Fabric
Di seguito sono riportati i manifesti completi del servizio e dell'applicazione usati in questa guida introduttiva.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si apprende come:
> [!div class="checklist"]
> * Creare un pacchetto di un contenitore di un'immagine Docker
> * Configurare la comunicazione
> * Compilare l'applicazione di Service Fabric e creare il pacchetto
> * Distribuire l'applicazione del contenitore in Azure

* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-containers-overview.md).
* Vedere l'esercitazione [Distribuire un'applicazione .NET in un contenitore](service-fabric-host-app-in-a-container.md).
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
* Vedere gli [esempi di codice del contenitore di Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) in GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

