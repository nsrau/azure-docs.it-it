---
title: Esempi di manifesto dell'applicazione Reliable Services di Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare le impostazioni del manifesto dell'applicazione e del servizio per un'applicazione Reliable Services di Service Fabric.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6c4c8f0ee6aa12c58e02f71b42312cd6872076aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60719162"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Esempi di manifesti dell'applicazione e del servizio per Reliable Services
Di seguito sono riportati esempi dei manifesti dell'applicazione e del servizio per un'applicazione di Service Fabric con un front-end Web ASP.NET Core e un back-end con stato. Lo scopo di questi esempi è mostrare le impostazioni disponibili e come usarle. Questi manifesti dell'applicazione e del servizio sono basati sui manifesti descritti nella [guida introduttiva su .NET per Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/).

Sono disponibili le funzionalità seguenti:

|Manifesto|Funzionalità|
|---|---|
|[Manifesto dell'applicazione](#application-manifest)| [governance delle risorse](service-fabric-resource-governance.md), [eseguire un servizio come account amministratore locale](service-fabric-application-runas-security.md), [applicare un criterio predefinito a tutti i pacchetti di codice del servizio](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [creare entità per utenti e gruppi](service-fabric-application-runas-security.md), condividere un pacchetto di dati tra istanze del servizio, [eseguire l'override degli endpoint del servizio](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|Manifesto del servizio FrontEndService| [Eseguire uno script all'avvio del servizio](service-fabric-run-script-at-service-startup.md), [definire un endpoint HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifesto del servizio BackEndService| [Dichiarare un pacchetto di configurazione](service-fabric-application-and-service-manifests.md), [dichiarare un pacchetto di dati](service-fabric-application-and-service-manifests.md), [configurare un endpoint](service-fabric-service-manifest-resources.md)| 

Per altre informazioni sugli elementi XML specifici, vedere [Elementi del manifesto dell'applicazione](#application-manifest-elements), [Elementi del manifesto del servizio VotingWeb](#votingweb-service-manifest-elements) e [Elementi del manifesto del servizio VotingData](#votingdata-service-manifest-elements).

## <a name="application-manifest"></a>Manifesto dell'applicazione

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>Manifesto del servizio VotingWeb

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>Manifesto del servizio VotingData

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
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

### <a name="resourceoverrides-element"></a>Elemento ResourceOverrides
Specifica gli override di risorse per gli endpoint dichiarati nelle risorse del manifesto del servizio. Per altre informazioni, vedere [Elemento ResourceOverrides](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Elemento Endpoints
Uno o più endpoint di cui eseguire l'override. Per altre informazioni, vedere [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
Endpoint, dichiarato nel manifesto del servizio, di cui eseguire l'override. Per altre informazioni, vedere [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Elemento Policies
Descrive i criteri (binding degli endpoint, condivisione dei pacchetti, RunAs e accesso sicuro) da applicare nel manifesto del servizio importato. Per altre informazioni, vedere [Elemento Policies](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Elemento ServicePackageResourceGovernancePolicy
Definisce i criteri di governance delle risorse applicati a livello dell'intero pacchetto del servizio. Per altre informazioni, vedere [Elemento ServicePackageResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento ResourceGovernancePolicy
Specifica i limiti di risorse per un pacchetto di codice. Per altre informazioni, vedere [Elemento ResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>Elemento PackageSharingPolicy
Indica se un pacchetto di codice, di configurazione o di dati deve essere condiviso tra le istanze del servizio dello stesso tipo di servizio. Per altre informazioni, vedere [Elemento PackageSharingPolicy](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>Elemento SecurityAccessPolicy
Concede a un entità di sicurezza le autorizzazioni di accesso per una risorsa (ad esempio, un endpoint) definita in un manifesto del servizio. È in genere molto utile per controllare e limitare l'accesso dei servizi a diverse risorse e ridurre così al minimo i rischi per la sicurezza. È importante soprattutto quando l'applicazione è basata su una raccolta di servizi di un marketplace sviluppati da altri sviluppatori. Per altre informazioni, vedere [Elemento SecurityAccessPolicy](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Elemento RunAsPolicy
Specifica l'account utente o di sistema locale con cui verrà eseguito il pacchetto di codice di un servizio. Gli account di dominio sono supportati nelle distribuzioni di Windows Server in cui è disponibile Azure Active Directory. Per impostazione predefinita, le applicazioni vengono eseguite con l'account con cui viene eseguito il processo Fabric.exe. Possono essere eseguite anche con altri account, che devono essere dichiarati nella sezione Principals. Se si applicano criteri RunAs a un servizio e il manifesto del servizio dichiara risorse endpoint con il protocollo HTTP, è necessario specificare anche un elemento SecurityAccessPolicy per garantire che le porte allocate a questi endpoint siano correttamente inserite nell'elenco di controllo di accesso per l'account utente RunAs con cui viene eseguito il servizio. Per un endpoint HTTPS, è necessario anche definire un elemento EndpointBindingPolicy per indicare il nome del certificato da restituire al client. Per altre informazioni, vedere [Elemento RunAsPolicy](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>Elemento DefaultServices
Dichiara le istanze dei servizi create automaticamente ogni volta che viene creata un'istanza di un'applicazione sulla base di questo tipo di applicazione. Per altre informazioni, vedere [Elemento DefaultServices](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento Service
Dichiara un servizio da creare automaticamente quando viene creata un'istanza dell'applicazione. Per altre informazioni, vedere [Elemento Service](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Elemento StatefulService
Definisce un servizio con stato. Per altre informazioni, vedere [Elemento StatefulService](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Elemento StatelessService
Definisce un servizio senza stato. Per altre informazioni, vedere [Elemento StatelessService](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Elemento Principals
Descrive le entità di sicurezza (utenti o gruppi) necessarie per l'applicazione allo scopo di eseguire i servizi e proteggere le risorse. Alle entità di sicurezza viene fatto riferimento nelle sezioni dei criteri. Per altre informazioni, vedere [Elemento Principals](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Elemento Groups
Dichiara un set di gruppi come entità di sicurezza a cui può essere fatto riferimento nei criteri. I gruppi sono utili quando sono presenti più utenti per punti di ingresso del servizio differenti che devono avere determinati privilegi comuni disponibili a livello di gruppo. Per altre informazioni, vedere [Elemento Groups](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Elemento Group
Dichiara un gruppo come entità di sicurezza a cui può essere fatto riferimento nei criteri. Per altre informazioni, vedere [Elemento Group](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Elemento Membership
 Per altre informazioni, vedere [Elemento Membership](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>Elemento SystemGroup
 Per altre informazioni, vedere [Elemento SystemGroup](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Elemento Users
Dichiara un set di utenti come entità di sicurezza a cui può essere fatto riferimento nei criteri. Per altre informazioni, vedere [Elemento Users](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Elemento User
Dichiara un utente come entità di sicurezza a cui può essere fatto riferimento nei criteri. Per altre informazioni, vedere [Elemento User](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>Elemento MemberOf
Gli utenti possono essere aggiunti a qualsiasi gruppo di appartenenza esistente, ereditando così tutte le proprietà e le impostazioni di sicurezza di tale gruppo di appartenenza. Il gruppo di appartenenze può essere usato per proteggere le risorse esterne che devono essere accessibili da diversi servizi o dallo stesso servizio, ma su un computer diverso. Per altre informazioni, vedere [Elemento MemberOf](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>Elemento SystemGroup
Gruppo di sistema a cui aggiungere l'utente.  Il gruppo di sistema deve essere definito nella sezione Groups. Per altre informazioni, vedere [Elemento SystemGroup](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Elemento Group
Gruppo a cui aggiungere l'utente.  Il gruppo deve essere definito nella sezione Groups. Per altre informazioni, vedere [Elemento Group](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Elemento Policies
Descrive i criteri (raccolta dei log, account RunAs predefinito, integrità e accesso sicuro) da applicare a livello di applicazione. Per altre informazioni, vedere [Elemento Policies](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>Elemento DefaultRunAsPolicy
Specifica un account utente predefinito per tutti i pacchetti di codice di servizi per cui non è definito un elemento RunAsPolicy specifico nella sezione ServiceManifestImport. Per altre informazioni, vedere [Elemento DefaultRunAsPolicy](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementi del manifesto del servizio VotingWeb
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Descrive in modo dichiarativo il tipo di servizio e la versione. Elenca i pacchetti di codice, di configurazione e di dati aggiornabili in modo indipendente che insieme costituiscono un pacchetto del servizio per il supporto di uno o più tipi di servizio. Vengono specificati anche le risorse, le impostazioni di diagnostica e i metadati del servizio, ad esempio il tipo di servizio, le proprietà di integrità e le metriche di bilanciamento del carico. Per altre informazioni, vedere [Elemento ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento ServiceTypes
Definisce i tipi di servizio supportati da un elemento CodePackage nel manifesto. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I tipi di servizi sono dichiarati a livello di manifesto e non a livello di pacchetto di codice. Per altre informazioni, vedere [Elemento ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento StatelessServiceType
Descrive un tipo di servizio senza stato. Per altre informazioni, vedere [Elemento StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento CodePackage
Descrive un pacchetto di codice che supporta un tipo di servizio definito. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I processi risultanti devono registrare i tipi di servizi supportati in fase di esecuzione. Quando sono presenti più pacchetti di codice, vengono tutti attivati ogni volta che il sistema cerca uno dei tipi di servizio dichiarati. Per altre informazioni, vedere [Elemento CodePackage](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>Elemento SetupEntryPoint
Punto di ingresso con privilegi che viene eseguito per impostazione predefinita con le stesse credenziali di Service Fabric (in genere l'account NETWORKSERVICE) prima di qualsiasi altro punto di ingresso. L'eseguibile specificato da EntryPoint è in genere l'host servizio a esecuzione prolungata. La presenza di un punto di ingresso di configurazione separato consente di evitare di dover eseguire l'host del servizio con privilegi elevati per lunghi periodi di tempo. Per altre informazioni, vedere [Elemento SetupEntryPoint](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Per altre informazioni, vedere [Elemento ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Elemento Program
Nome dell'eseguibile.  Ad esempio, "MySetup.bat" o "MyServiceHost.exe". Per altre informazioni, vedere [Elemento Program](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Elemento Arguments
 Per altre informazioni, vedere [Elemento Arguments](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento WorkingFolder
Directory di lavoro per il processo nel pacchetto di codice sul nodo del cluster in cui viene distribuita l'applicazione. È possibile specificare tre valori: Work (predefinito), CodePackage o CodeBase. CodeBase specifica che la directory di lavoro è impostata sulla directory in cui è definito il file EXE nel pacchetto di codice. CodePackage imposta la directory di lavoro sulla radice del pacchetto di codice, indipendentemente dalla posizione in cui è definito il file EXE nella directory del pacchetto di codice. Work imposta la directory di lavoro su una cartella univoca creata nel nodo.  Questa cartella è la stessa per l'intera istanza dell'applicazione. Per impostazione predefinita, la directory di lavoro di tutti i processi nell'applicazione è impostata sulla cartella di lavoro dell'applicazione. Si tratta della posizione in cui i processi possono scrivere i dati. La scrittura dei dati nel pacchetto di codice o nella base di codice non è consigliata, perché tali cartelle possono essere condivise tra diverse istanze dell'applicazione e potrebbero essere eliminate. Per altre informazioni, vedere [Elemento WorkingFolder](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Elemento ConsoleRedirection

> [!WARNING]
> Non usare il reindirizzamento della console in un'applicazione di produzione, ma solo per attività di sviluppo e debug in locale. Reindirizza l'output della console dallo script di avvio a un file di output nella cartella dell'applicazione denominata "log" nel nodo del cluster in cui viene distribuita ed eseguita l'applicazione. Per altre informazioni, vedere [Elemento ConsoleRedirection](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>Elemento EntryPoint
L'eseguibile specificato da EntryPoint è in genere l'host servizio a esecuzione prolungata. La presenza di un punto di ingresso di configurazione separato consente di evitare di dover eseguire l'host del servizio con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato da EntryPoint viene eseguito dopo che SetupEntryPoint termina correttamente. Il processo risultante viene monitorato e riavviato (iniziando di nuovo con SetupEntryPoint) se termina o si arresta in modo anomalo. Per altre informazioni, vedere [Elemento EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Per altre informazioni, vedere [Elemento ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Dichiara una cartella, denominata dall'attributo Name in PackageRoot, che contiene un file Settings.xml. Questo file contiene sezioni di impostazioni di coppie chiave-valore che possono essere lette dal processo in fase di esecuzione. Se durante un aggiornamento viene modificata solo la versione del pacchetto di configurazione, il processo in esecuzione non viene riavviato. Un callback piuttosto notifica al processo che le impostazioni di configurazione sono cambiate affinché vengano ricaricate in modo dinamico. Per altre informazioni, vedere [Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento Resources
Descrive le risorse usate dal servizio, che possono essere dichiarate senza modificare il codice compilato ed essere modificate quando il servizio viene distribuito. L'accesso a queste risorse è controllato tramite le sezioni Principals e Policies del manifesto dell'applicazione. Per altre informazioni, vedere [Elemento Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento Endpoints
Definisce gli endpoint per il servizio. Per altre informazioni, vedere [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
Endpoint, dichiarato nel manifesto del servizio, di cui eseguire l'override. Per altre informazioni, vedere [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Elementi del manifesto del servizio VotingData
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Descrive in modo dichiarativo il tipo di servizio e la versione. Elenca i pacchetti di codice, di configurazione e di dati aggiornabili in modo indipendente che insieme costituiscono un pacchetto del servizio per il supporto di uno o più tipi di servizio. Vengono specificati anche le risorse, le impostazioni di diagnostica e i metadati del servizio, ad esempio il tipo di servizio, le proprietà di integrità e le metriche di bilanciamento del carico. Per altre informazioni, vedere [Elemento ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento ServiceTypes
Definisce i tipi di servizio supportati da un elemento CodePackage nel manifesto. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I tipi di servizi sono dichiarati a livello di manifesto e non a livello di pacchetto di codice. Per altre informazioni, vedere [Elemento ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>Elemento StatefulServiceType
Descrive un tipo di servizio con stato. Per altre informazioni, vedere [Elemento StatefulServiceType](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento CodePackage
Descrive un pacchetto di codice che supporta un tipo di servizio definito. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I processi risultanti devono registrare i tipi di servizi supportati in fase di esecuzione. Quando sono presenti più pacchetti di codice, vengono tutti attivati ogni volta che il sistema cerca uno dei tipi di servizio dichiarati. Per altre informazioni, vedere [Elemento CodePackage](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento EntryPoint
L'eseguibile specificato da EntryPoint è in genere l'host servizio a esecuzione prolungata. La presenza di un punto di ingresso di configurazione separato consente di evitare di dover eseguire l'host del servizio con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato da EntryPoint viene eseguito dopo che SetupEntryPoint termina correttamente. Il processo risultante viene monitorato e riavviato (iniziando di nuovo con SetupEntryPoint) se termina o si arresta in modo anomalo. Per altre informazioni, vedere [Elemento EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Per altre informazioni, vedere [Elemento ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Elemento Program
Nome dell'eseguibile.  Ad esempio, "MySetup.bat" o "MyServiceHost.exe". Per altre informazioni, vedere [Elemento Program](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento WorkingFolder
Directory di lavoro per il processo nel pacchetto di codice sul nodo del cluster in cui viene distribuita l'applicazione. È possibile specificare tre valori: Work (predefinito), CodePackage o CodeBase. CodeBase specifica che la directory di lavoro è impostata sulla directory in cui è definito il file EXE nel pacchetto di codice. CodePackage imposta la directory di lavoro sulla radice del pacchetto di codice, indipendentemente dalla posizione in cui è definito il file EXE nella directory del pacchetto di codice. Work imposta la directory di lavoro su una cartella univoca creata nel nodo.  Questa cartella è la stessa per l'intera istanza dell'applicazione. Per impostazione predefinita, la directory di lavoro di tutti i processi nell'applicazione è impostata sulla cartella di lavoro dell'applicazione. Si tratta della posizione in cui i processi possono scrivere i dati. La scrittura dei dati nel pacchetto di codice o nella base di codice non è consigliata, perché tali cartelle possono essere condivise tra diverse istanze dell'applicazione e potrebbero essere eliminate. Per altre informazioni, vedere [Elemento WorkingFolder](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Dichiara una cartella, denominata dall'attributo Name in PackageRoot, che contiene un file Settings.xml. Questo file contiene sezioni di impostazioni di coppie chiave-valore che possono essere lette dal processo in fase di esecuzione. Se durante un aggiornamento viene modificata solo la versione del pacchetto di configurazione, il processo in esecuzione non viene riavviato. Un callback piuttosto notifica al processo che le impostazioni di configurazione sono cambiate affinché vengano ricaricate in modo dinamico. Per altre informazioni, vedere [Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento DataPackage
Dichiara una cartella, denominata dall'attributo Name in PackageRoot, che contiene i file di dati statici che devono essere usati dal processo in fase di esecuzione. In caso di aggiornamento di uno dei pacchetti di dati elencati nel manifesto del servizio, Service Fabric riciclerà tutti i file EXE e DLLHOST specificati nei pacchetti host e per il supporto. Per altre informazioni, vedere [Elemento DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento Resources
Descrive le risorse usate dal servizio, che possono essere dichiarate senza modificare il codice compilato ed essere modificate quando il servizio viene distribuito. L'accesso a queste risorse è controllato tramite le sezioni Principals e Policies del manifesto dell'applicazione. Per altre informazioni, vedere [Elemento Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento Endpoints
Definisce gli endpoint per il servizio. Per altre informazioni, vedere [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
Endpoint, dichiarato nel manifesto del servizio, di cui eseguire l'override. Per altre informazioni, vedere [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

