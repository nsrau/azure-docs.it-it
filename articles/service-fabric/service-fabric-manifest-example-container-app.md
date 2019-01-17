---
title: Esempi di manifesto dell'applicazione contenitore di Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare le informazioni del manifesto dell'applicazione e del manifesto del servizio per un'applicazione di Service Fabric con più contenitori.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2018
ms.author: ryanwi
ms.openlocfilehash: 010fd442419f57f8b53705be8d3f49fdb84e28fd
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262453"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Esempi di manifesti dell'applicazione e del servizio per applicazioni multicontenitore
Di seguito sono riportati alcuni esempi di manifesto dell'applicazione e del servizio per un'applicazione di Service Fabric con più contenitori. Lo scopo di questi esempi è mostrare le impostazioni disponibili e come usarle. Questi manifesti dell'applicazione e del servizio sono basati sui manifesti descritti nell'[esempio di contenitore di Windows Server 2016](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows).

Sono disponibili le funzionalità seguenti:
|Manifesto|Funzionalità|
|---|---|
|[Manifesto dell'applicazione](#application-manifest)| [override delle variabili di ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurazione del mapping da porta a host del contenitore](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [configurazione dell'autenticazione del registro contenitori](service-fabric-get-started-containers.md#configure-container-registry-authentication), [governance delle risorse](service-fabric-resource-governance.md), [impostazione della modalità di isolamento](service-fabric-get-started-containers.md#configure-isolation-mode), [specifica delle immagini del contenitore specifiche delle build del sistema operativo](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifesto del servizio FrontEndService](#frontendservice-service-manifest)| [impostazione delle variabili di ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurazione di un endpoint](service-fabric-get-started-containers.md#configure-communication), passaggio di comandi al contenitore, [importazione di un certificato in un contenitore](service-fabric-securing-containers.md)| 
|[Manifesto del servizio BackEndService](#backendservice-service-manifest)|[impostazione delle variabili di ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurazione di un endpoint](service-fabric-get-started-containers.md#configure-communication), [configurazione del driver volume](service-fabric-containers-volume-logging-drivers.md)| 

Per altre informazioni sugli elementi XML specifici, vedere [Elementi del manifesto dell'applicazione](#application-manifest-elements), [Elementi del manifesto del servizio FrontEndService](#frontendservice-service-manifest-elements) e [Elementi del manifesto del servizio BackEndService](#backendservice-service-manifest-elements).

## <a name="application-manifest"></a>Manifesto dell'applicazione

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>Manifesto del servizio FrontEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>Manifesto del servizio BackEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="application-manifest-elements"></a>Elementi del manifesto dell'applicazione
### <a name="applicationmanifest-element"></a>Elemento ApplicationManifest
Descrive in modo dichiarativo il tipo di applicazione e la versione. Per creare un tipo di applicazione viene fatto riferimento a uno o più manifesti di servizi costitutivi. È possibile eseguire l'override delle impostazioni di configurazione dei servizi costitutivi usando impostazioni dell'applicazione con parametri. È anche possibile dichiarare i servizi, i modelli di servizio, le entità di sicurezza, i criteri, la configurazione di diagnostica e i certificati predefiniti a livello di applicazione. Per altre informazioni, vedere [Elemento ApplicationManifest](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Elemento Parameters
Dichiara i parametri usati nel manifesto dell'applicazione. Il valore di questi parametri può essere specificato durante la creazione di un'istanza dell'applicazione e può essere usato per eseguire l'override delle impostazioni di configurazione dell'applicazione o del servizio. Per altre informazioni, vedere [Elemento Parameters](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Elemento Parameter
Parametro dell'applicazione da usare nel manifesto. Il valore del parametro può essere modificato durante la creazione di un'istanza dell'applicazione. Se non viene specificato alcun valore, viene usato quello predefinito. Per altre informazioni, vedere [Elemento Parameter](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>Elemento ServiceManifestImport
Importa un manifesto del servizio creato dallo sviluppatore di servizi. È necessario importare un manifesto del servizio per ogni servizio che costituisce l'applicazione. Per il manifesto del servizio è possibile dichiarare criteri e override di configurazione. Per altre informazioni, vedere [Elemento ServiceManifestImport](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>Elemento ServiceManifestRef
Importa il manifesto del servizio per riferimento. Attualmente, il file manifesto del servizio (ServiceManifest.xml) deve essere incluso nel pacchetto della build. Per altre informazioni, vedere [Elemento ServiceManifestRef](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="policies-element"></a>Elemento Policies
Descrive i criteri (binding degli endpoint, condivisione dei pacchetti, RunAs e accesso sicuro) da applicare nel manifesto del servizio importato. Per altre informazioni, vedere [Elemento Policies](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Elemento ServicePackageResourceGovernancePolicy
Definisce i criteri di governance delle risorse applicati a livello dell'intero pacchetto del servizio. Per altre informazioni, vedere [Elemento ServicePackageResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento ResourceGovernancePolicy
Specifica i limiti di risorse per un pacchetto di codice. Per altre informazioni, vedere [Elemento ResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>Elemento ContainerHostPolicies
Specifica i criteri per l'attivazione degli host dei contenitori. Per altre informazioni, vedere [Elemento ContainerHostPolicies](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>Elemento RepositoryCredentials
Credenziali per il repository di immagini contenitore da cui eseguire il pull di immagini. Per altre informazioni, vedere [Elemento RepositoryCredentials](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Elemento PortBinding
Specifica la risorsa endpoint da associare alla porta del contenitore esposta. Per altre informazioni, vedere [Elemento PortBinding](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Elemento Volume
Specifica il volume da associare al contenitore. Per altre informazioni, vedere [Elemento Volume](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>Elemento DriverOption
Opzioni del driver da passare al driver. Per altre informazioni, vedere [Elemento DriverOption](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>Elemento ImageOverrides
I contenitori di Windows Server potrebbero non essere compatibili nelle diverse versioni del sistema operativo.  È possibile specificare più immagini del sistema operativo per ogni contenitore e contrassegnarle con le versioni della build del sistema operativo. Ottenere la versione della build del sistema operativo eseguendo "winver" al prompt dei comandi di Windows. Se la versione della build del sistema operativo sottostante è 16299 (Windows Server versione 1709), Service Fabric seleziona l'immagine del contenitore contrassegnata da Os="16299". Si presuppone che se un'immagine contenitore non è contrassegnata possa funzionare in tutte le versioni del sistema operativo e possa eseguire l'override dell'immagine specificata nel manifesto del servizio. Per altre informazioni, vedere [Elemento ImageOverrides](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Elemento immagine
Immagine del contenitore corrispondente al numero di versione della build del sistema operativo da avviare. Se l'attributo del sistema operativo non è specificato, si presuppone che l'immagine del contenitore possa funzionare in tutte le versioni del sistema operativo ed eseguire l'override dell'immagine specificata nel manifesto del servizio. Per altre informazioni, vedere [Elemento Image](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>Elemento EnvironmentOverrides
 Per altre informazioni, vedere [Elemento EnvironmentOverrides](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Elemento EnvironmentVariable
Variabile di ambiente. Per altre informazioni, vedere [Elemento EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>Elemento CertificateRef
Specifica le informazioni su un certificato X509 che deve essere esposto all'ambiente del contenitore. Questo certificato deve essere installato nell'archivio LocalMachine di tutti i nodi del cluster.
All'avvio dell'applicazione, il runtime legge il certificato e genera un file PFX e una password (in Windows) o un file PEM (in Linux).
Il file PFX e la password sono accessibili nel contenitore usando le variabili di ambiente Certificates_ServicePackageName_CodePackageName_CertName_PFX e Certificates_ServicePackageName_CodePackageName_CertName_Password. Il file PFX è accessibile nel contenitore usando le variabili di ambiente Certificates_ServicePackageName_CodePackageName_CertName_PEM e Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Per altre informazioni, vedere [Elemento CertificateRef](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>Elemento DefaultServices
Dichiara le istanze dei servizi create automaticamente ogni volta che viene creata un'istanza di un'applicazione sulla base di questo tipo di applicazione. Per altre informazioni, vedere [Elemento DefaultServices](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento Service
Dichiara un servizio da creare automaticamente quando viene creata un'istanza dell'applicazione. Per altre informazioni, vedere [Elemento Service](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>Elemento StatelessService
Definisce un servizio senza stato. Per altre informazioni, vedere [Elemento StatelessService](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Elementi del manifesto del servizio FrontEndService
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Descrive in modo dichiarativo il tipo di servizio e la versione. Elenca i pacchetti di codice, di configurazione e di dati aggiornabili in modo indipendente che insieme costituiscono un pacchetto del servizio per il supporto di uno o più tipi di servizio. Vengono specificati anche le risorse, le impostazioni di diagnostica e i metadati del servizio, ad esempio il tipo di servizio, le proprietà di integrità e le metriche di bilanciamento del carico. Per altre informazioni, vedere [Elemento ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento ServiceTypes
Definisce i tipi di servizio supportati da un elemento CodePackage nel manifesto. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I tipi di servizi sono dichiarati a livello di manifesto e non a livello di pacchetto di codice. Per altre informazioni, vedere [Elemento ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento StatelessServiceType
Descrive un tipo di servizio senza stato. Per altre informazioni, vedere [Elemento StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento CodePackage
Descrive un pacchetto di codice che supporta un tipo di servizio definito. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I processi risultanti devono registrare i tipi di servizi supportati in fase di esecuzione. Quando sono presenti più pacchetti di codice, vengono tutti attivati ogni volta che il sistema cerca uno dei tipi di servizio dichiarati. Per altre informazioni, vedere [Elemento CodePackage](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento EntryPoint
L'eseguibile specificato da EntryPoint è in genere l'host servizio a esecuzione prolungata. La presenza di un punto di ingresso di configurazione separato consente di evitare di dover eseguire l'host del servizio con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato da EntryPoint viene eseguito dopo che SetupEntryPoint termina correttamente. Il processo risultante viene monitorato e riavviato (iniziando di nuovo con SetupEntryPoint) se termina o si arresta in modo anomalo. Per altre informazioni, vedere [Elemento EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento ContainerHost
 Per altre informazioni, vedere [Elemento ContainerHost](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento ImageName
Repository e immagine in https://hub.docker.com o in Registro Azure Container. Per altre informazioni, vedere [Elemento ImageName](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento EnvironmentVariables
Passa le variabili di ambiente al contenitore o all'eseguibile.  Per altre informazioni, vedere [Elemento EnvironmentVariables](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento EnvironmentVariable
Variabile di ambiente. Per altre informazioni, vedere [Elemento EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Dichiara una cartella, denominata dall'attributo Name, che contiene un file Settings.xml. Questo file contiene sezioni di impostazioni di coppie chiave-valore che possono essere lette dal processo in fase di esecuzione. Se durante un aggiornamento viene modificata solo la versione del pacchetto di configurazione, il processo in esecuzione non viene riavviato. Un callback piuttosto notifica al processo che le impostazioni di configurazione sono cambiate affinché vengano ricaricate in modo dinamico. Per altre informazioni, vedere [Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento DataPackage
Dichiara una cartella, denominata dall'attributo Name, che contiene file di dati statici. In caso di aggiornamento di uno dei pacchetti di dati elencati nel manifesto del servizio, Service Fabric riciclerà tutti i file EXE e DLLHOST specificati nei pacchetti host e per il supporto. Per altre informazioni, vedere [Elemento DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento Resources
Descrive le risorse usate dal servizio, che possono essere dichiarate senza modificare il codice compilato ed essere modificate quando il servizio viene distribuito. L'accesso a queste risorse è controllato tramite le sezioni Principals e Policies del manifesto dell'applicazione. Per altre informazioni, vedere [Elemento Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento Endpoints
Definisce gli endpoint per il servizio. Per altre informazioni, vedere [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
Per altre informazioni, vedere [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Elementi del manifesto del servizio BackEndService
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Descrive in modo dichiarativo il tipo di servizio e la versione. Elenca i pacchetti di codice, di configurazione e di dati aggiornabili in modo indipendente che insieme costituiscono un pacchetto del servizio per il supporto di uno o più tipi di servizio. Vengono specificati anche le risorse, le impostazioni di diagnostica e i metadati del servizio, ad esempio il tipo di servizio, le proprietà di integrità e le metriche di bilanciamento del carico. Per altre informazioni, vedere [Elemento ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento ServiceTypes
Definisce i tipi di servizio supportati da un elemento CodePackage nel manifesto. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I tipi di servizi sono dichiarati a livello di manifesto e non a livello di pacchetto di codice. Per altre informazioni, vedere [Elemento ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento StatelessServiceType
Descrive un tipo di servizio senza stato. Per altre informazioni, vedere [Elemento StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento CodePackage
Descrive un pacchetto di codice che supporta un tipo di servizio definito. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I processi risultanti devono registrare i tipi di servizi supportati in fase di esecuzione. Quando sono presenti più pacchetti di codice, vengono tutti attivati ogni volta che il sistema cerca uno dei tipi di servizio dichiarati. Per altre informazioni, vedere [Elemento CodePackage](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento EntryPoint
L'eseguibile specificato da EntryPoint è in genere l'host servizio a esecuzione prolungata. La presenza di un punto di ingresso di configurazione separato consente di evitare di dover eseguire l'host del servizio con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato da EntryPoint viene eseguito dopo che SetupEntryPoint termina correttamente. Il processo risultante viene monitorato e riavviato (iniziando di nuovo con SetupEntryPoint) se termina o si arresta in modo anomalo. Per altre informazioni, vedere [Elemento EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento ContainerHost
Per altre informazioni, vedere [Elemento ContainerHost](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento ImageName
Repository e immagine in https://hub.docker.com o in Registro Azure Container. Per altre informazioni, vedere [Elemento ImageName](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Elemento Commands
Passa un elenco di comandi delimitato da virgole al contenitore. Per altre informazioni, vedere [Elemento Commands](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento EnvironmentVariables
Passa le variabili di ambiente al contenitore o all'eseguibile.  Per altre informazioni, vedere [Elemento EnvironmentVariables](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento EnvironmentVariable
Variabile di ambiente. Per altre informazioni, vedere [Elemento EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Dichiara una cartella, denominata dall'attributo Name, che contiene un file Settings.xml. Questo file contiene sezioni di impostazioni di coppie chiave-valore che possono essere lette dal processo in fase di esecuzione. Se durante un aggiornamento viene modificata solo la versione del pacchetto di configurazione, il processo in esecuzione non viene riavviato. Un callback piuttosto notifica al processo che le impostazioni di configurazione sono cambiate affinché vengano ricaricate in modo dinamico. Per altre informazioni, vedere [Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento Resources
Descrive le risorse usate dal servizio, che possono essere dichiarate senza modificare il codice compilato ed essere modificate quando il servizio viene distribuito. L'accesso a queste risorse è controllato tramite le sezioni Principals e Policies del manifesto dell'applicazione. Per altre informazioni, vedere [Elemento Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento Endpoints
Definisce gli endpoint per il servizio. Per altre informazioni, vedere [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
 Per altre informazioni, vedere [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

