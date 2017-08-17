---
title: Specifica degli endpoint di servizio di Service Fabric | Documentazione Microsoft
description: Come descrivere le risorse di endpoint in un manifesto del servizio, inclusa l'impostazione di endpoint HTTPS
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9cfdb94d1e030fe9d467389acf8894d79efd17d1
ms.contentlocale: it-it
ms.lasthandoff: 03/29/2017

---
# <a name="specify-resources-in-a-service-manifest"></a>Specificare le risorse in un manifesto del servizio
## <a name="overview"></a>Panoramica
Il manifesto del servizio consente alle risorse di essere usate dal servizio per essere dichiarate/modificate senza modificare il codice compilato. Azure Service Fabric supporta la configurazione delle risorse dell'endpoint del servizio. È possibile controllare l'accesso alle risorse specificate nel manifesto del servizio tramite SecurityGroup nel manifesto dell'applicazione. La dichiarazione delle risorse consente a queste ultime di essere modificate in fase di distribuzione, in questo modo il servizio non deve introdurre un nuovo meccanismo di configurazione. La definizione dello schema per il file ServiceManifest.xml viene installata con l'SDK e gli strumenti di Service Fabric in *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Endpoint
Quando una risorsa dell'endpoint viene definita nel manifesto del servizio, Service Fabric assegna le porte dall'intervallo di porte riservate dell'applicazione se la porta non è esplicitamente specificata. Ad esempio, esaminare l'endpoint *ServiceEndpoint1* specificato nel frammento di manifesto fornito dopo questo paragrafo. Inoltre, i servizi possono richiedere anche una porta specifica in una risorsa. Alle repliche del servizio in esecuzione sui diversi nodi del cluster possono essere assegnati diversi numeri di porta, mentre le repliche di un servizio in esecuzione nello stesso nodo condividono la porta. Le repliche del servizio possono quindi usare queste porte in base alle esigenze per la replica e l'ascolto delle richieste client.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Per altre informazioni sugli endpoint di riferimento del file delle impostazioni del pacchetto di configurazione (settings.xml), vedere [Configurazione di Reliable Services con stato](service-fabric-reliable-services-configuration.md) .

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Esempio: specificare un endpoint HTTP per il servizio
Il manifesto del servizio seguente definisce una risorsa di endpoint TCP e due risorse di endpoint HTTP nell'elemento &lt;Risorse&gt;.

Gli endpoint HTTP vengono automaticamente inseriti nell'elenco di controllo di accesso da Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Esempio: specificare un endpoint HTTPS per il servizio
Il protocollo HTTPS fornisce l’autenticazione del server e viene anche usato per crittografare la comunicazione del client-server. Per abilitare il protocollo HTTPS nel servizio di Service Fabric, specificare il protocollo nella sezione *Risorse -> Endpoint -> Endpoint* del manifesto del servizio, come illustrato in precedenza per l'endpoint *ServiceEndpoint3*.

> [!NOTE]
> Un protocollo del servizio non può essere modificato durante l'aggiornamento dell'applicazione. Se viene modificato durante l'aggiornamento, si tratta di una modifica importante.
> 
> 

Di seguito è riportato un esempio ApplicationManifest che è necessario impostare per il protocollo HTTPS. È necessario fornire l'identificazione personale per il certificato. EndpointRef è un riferimento a EndpointResource in ServiceManifest per cui si imposta il protocollo HTTPS. È possibile aggiungere più Endpointcertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

