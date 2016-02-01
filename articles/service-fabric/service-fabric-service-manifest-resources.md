<properties
   pageTitle="Specificare gli endpoint di servizio di Service Fabric | Microsoft Azure"
   description="Come descrivere le risorse di endpoint in un manifesto del servizio, inclusa l'impostazione di endpoint HTTPS"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="sumukhs"/>

# Specificare le risorse in un manifesto del servizio

## Panoramica

Il manifesto del servizio consente alle risorse di essere usate dal servizio per essere dichiarate/modificate senza modificare il codice compilato. Azure Service Fabric supporta la configurazione delle risorse dell'endpoint del servizio. È possibile controllare l'accesso alle risorse specificate nel manifesto del servizio tramite SecurityGroup nel manifesto dell'applicazione. La dichiarazione delle risorse consente a queste ultime di essere modificate in fase di distribuzione, in questo modo il servizio non deve introdurre un nuovo meccanismo di configurazione.

## Endpoint

Quando una risorsa dell'endpoint viene definita nel manifesto del servizio, Service Fabric assegna le porte dall'intervallo di porte dell'applicazione riservato se non viene specificata una porta esplicita. Fare riferimento all'endpoint *ServiceEndpoint1* seguente. Inoltre, i servizi possono richiedere anche una porta specifica in una risorsa. Alle repliche del servizio in esecuzione sui diversi nodi del cluster possono essere assegnati diversi numeri di porta, mentre le repliche dello stesso servizio in esecuzione nello stesso nodo condividono la stessa porta. Tali porte possono essere usate per le repliche del servizio per vari scopi, ad esempio la replica, l'attesa delle richieste del client e così via.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Per informazioni sugli endpoint di riferimento del file delle impostazioni del pacchetto di configurazione (settings.xml), vedere [Configurazione di Reliable Services con stato](../Service-Fabric/service-fabric-reliable-services-configuration.md).

## Esempio: specificare un endpoint HTTP per il servizio

Il manifesto del servizio seguente definisce una risorsa di endpoint TCP e due risorse di endpoint HTTP nell'elemento &lt;Resources&gt;.

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

## Esempio: specificare un endpoint HTTPS per il servizio

Il protocollo HTTPS fornisce l’autenticazione del server e viene anche usato per crittografare la comunicazione del client-server. Per abilitare questa opzione nel servizio di Service Fabric, quando si definisce il servizio, specificare il protocollo nella sezione *Risorse -> Endpoint -> Endpoint* del manifesto del servizio, come illustrato in precedenza per l'endpoint *ServiceEndpoint3*.

>[AZURE.NOTE]Un protocollo del servizio non può essere modificato durante l'aggiornamento dell'applicazione, poiché il risultato sarà una modifica sostanziale.


Di seguito è riportato un esempio ApplicationManifest che è necessario impostare per il protocollo HTTPS. È necessario fornire l'identificazione personale per il certificato. EndpointRef è un riferimento a EndpointResource in ServiceManifest per cui si imposta il protocollo HTTPS. È possibile aggiungere più Endpointcertificate.

```
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

<!---HONumber=AcomDC_0121_2016-->