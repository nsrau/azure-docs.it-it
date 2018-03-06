---
title: Creare un'applicazione contenitore Windows di Azure Service Fabric | Microsoft Docs
description: In questa guida introduttiva viene creata la prima applicazione contenitore Windows in Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/18
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 7a8d28ef842ba77355628c79c20fa7fd3c693380
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>Guida introduttiva: Distribuire un'applicazione contenitore Windows in Azure Service Fabric
Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili. 

Per eseguire un'applicazione esistente in un contenitore Windows in un cluster di Service Fabric non è necessario apportare modifiche all'applicazione. Questa guida introduttiva spiega come distribuire un'immagine del contenitore Docker predefinita in un'applicazione di Service Fabric. Al termine, saranno in esecuzione Nano Server per Windows Server 2016 e un contenitore IIS. Questa guida introduttiva descrive la distribuzione di un contenitore Windows. Vedere [questa guida introduttiva](service-fabric-quickstart-containers-linux.md) per distribuire un contenitore Linux.

![Pagina Web predefinita di IIS][iis-default]

Con guida introduttiva si apprende come:
> [!div class="checklist"]
> * Creare un pacchetto di un contenitore di un'immagine Docker
> * Configurare la comunicazione
> * Compilare l'applicazione di Service Fabric e creare il pacchetto
> * Distribuire l'applicazione del contenitore in Azure

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. È possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un computer di sviluppo che esegue:
  * Visual Studio 2015 o Visual Studio 2017.
  * [SDK e strumenti di Service Fabric](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Creare un pacchetto di un contenitore di un'immagine Docker con Visual Studio
L'SDK e gli strumenti di Service Fabric offrono un modello di servizio che permette di distribuire un contenitore in un cluster di Service Fabric.

Avviare Visual Studio come "Amministratore".  Selezionare **File** > **Nuovo** > **Progetto**.

Selezionare l'**applicazione di Service Fabric**, denominarla "MyFirstContainer" e fare clic su **OK**.

Selezionare **Contenitore** dai modelli **Applicazioni e contenitori ospitati**.

In **Nome immagine** immettere "microsoft/iis:nanoserver", l'[immagine di base di IIS e Nano Server per Windows Server](https://hub.docker.com/r/microsoft/iis/). 

Assegnare al servizio il nome "MyContainerService" e fare clic su **OK**.

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurare la comunicazione e il mapping tra porta del contenitore e porta dell'host
Il servizio richiede un endpoint per la comunicazione.  Per questa guida introduttiva il servizio in contenitori è in ascolto sulla porta 80.  In Esplora soluzioni aprire *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml*.  Aggiornare l'elemento `Endpoint` esistente nel file ServiceManifest.xml e aggiungere il protocollo, la porta e lo schema URI: 

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```
Se si specifica `UriScheme`, l'endpoint del contenitore viene registrato automaticamente con Service Fabric Naming Service per l'individuazione. Alla fine di questo articolo viene fornito un file di esempio completo di ServiceManifest.xml. 

Configurare il mapping dalla porta all'host del contenitore in modo che per le richieste in ingresso per il servizio sulla porta 80 venga eseguito il mapping alla porta 80 del contenitore.  In Esplora soluzioni aprire *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml* e specificare i criteri `PortBinding` in `ContainerHostPolicies`.  Per questa guida introduttiva, `ContainerPort` è 80 ed `EndpointRef` è "MyContainerServiceTypeEndpoint" (l'endpoint definito nel manifesto del servizio).    

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

Alla fine di questo articolo viene fornito un file di esempio completo di ApplicationManifest.xml.

## <a name="create-a-cluster"></a>Creare un cluster
Per distribuire l'applicazione in un cluster in Azure, è possibile aggiungere un party cluster. I cluster di entità sono cluster Service Fabric gratuiti e disponibili per un periodo di tempo limitato ospitati in Azure e gestiti dal team di Service Fabric, in cui chiunque può distribuire applicazioni e ottenere informazioni sulla piattaforma. Il cluster usa un solo certificato autofirmato per la sicurezza da nodo a nodo e da client a nodo. 

Eseguire l'accesso e [aggiungere un cluster Windows](http://aka.ms/tryservicefabric). Scaricare il certificato PFX nel computer facendo clic sul collegamento **PFX**. Il certificato e il valore di **Endpoint connessione** vengono usati nei passaggi seguenti.

![Certificato PFX ed endpoint connessione](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

In un computer Windows installare il certificato PFX nell'archivio certificati *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:
\CurrentUser\My


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Ricordare l'identificazione personale per il passaggio seguente.  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Distribuire l'applicazione in Azure usando Visual Studio
Ora che l'applicazione è pronta, è possibile distribuirla in un cluster direttamente da Visual Studio.

Fare clic con il pulsante destro del mouse su **MyFirstContainer** in Esplora soluzioni e scegliere **Pubblica**. Verrà visualizzata la finestra di dialogo Pubblica.

Copiare l'**endpoint della connessione** dalla pagina del party cluster nel campo **Endpoint connessione**. Ad esempio, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Fare clic su **Parametri di connessione avanzati** e verificare le informazioni sui parametri di connessione.  I valori di *FindValue* e *ServerCertThumbprint* devono corrispondere all'identificazione personale del certificato installato nel passaggio precedente. 

![Finestra di dialogo Pubblica](./media/service-fabric-quickstart-containers/publish-app.png)

Fare clic su **Pubblica**.

Ogni applicazione nel cluster deve avere un nome univoco.  Un party cluster è tuttavia un ambiente pubblico condiviso e potrebbe verificarsi un conflitto con un'applicazione esistente.  Se è presente un conflitto di nomi, ridenominare il progetto di Visual Studio e distribuirlo di nuovo.

Aprire un browser e passare a http://zwin7fh14scd.westus.cloudapp.azure.com:80. Verrà visualizzata la pagina Web predefinita di IIS: ![Pagina Web predefinita di IIS][iis-default]

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifesti di esempio completi del servizio e dell'applicazione di Service Fabric
Di seguito sono riportati i manifesti completi del servizio e dell'applicazione usati in questa guida introduttiva.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>microsoft/iis:nanoserver</ImageName>
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
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
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
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come:
> [!div class="checklist"]
> * Creare un pacchetto di un contenitore di un'immagine Docker
> * Configurare la comunicazione
> * Compilare l'applicazione di Service Fabric e creare il pacchetto
> * Distribuire l'applicazione del contenitore in Azure

* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-containers-overview.md).
* Vedere l'esercitazione [Distribuire un'applicazione .NET in un contenitore](service-fabric-host-app-in-a-container.md).
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
* Vedere gli [esempi di codice del contenitore di Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) in GitHub.

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
