---
title: Specifica degli endpoint di servizio Service Fabric
description: Come descrivere le risorse di endpoint in un manifesto del servizio, inclusa l'impostazione di endpoint HTTPS
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: c0c3c45c47447390901e5e0d60e77ab6b85a6a0d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91354760"
---
# <a name="specify-resources-in-a-service-manifest"></a>Specificare le risorse in un manifesto del servizio
## <a name="overview"></a>Panoramica
Service Fabric le applicazioni e i servizi vengono definiti e sottoutilizzati con il controllo delle versioni tramite file manifesto. Per una panoramica di livello superiore di ServiceManifest.xml e ApplicationManifest.xml, vedere [Service Fabric manifesti dell'applicazione e del servizio](service-fabric-application-and-service-manifests.md).

Il manifesto del servizio consente alle risorse usate dal servizio di essere dichiarate o modificate senza modificare il codice compilato. Service Fabric supporta la configurazione delle risorse endpoint per il servizio. È possibile controllare l'accesso alle risorse specificate nel manifesto del servizio tramite SecurityGroup nel manifesto dell'applicazione. La dichiarazione delle risorse consente a queste ultime di essere modificate in fase di distribuzione, in questo modo il servizio non deve introdurre un nuovo meccanismo di configurazione. La definizione dello schema per il file di ServiceManifest.xml viene installata con Service Fabric SDK e gli strumenti di per *C:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*ed è documentata nella [documentazione dello schema ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

## <a name="endpoints"></a>Endpoint
Quando una risorsa dell'endpoint viene definita nel manifesto del servizio, Service Fabric assegna le porte dall'intervallo di porte riservate dell'applicazione se la porta non è esplicitamente specificata. Ad esempio, esaminare l'endpoint *ServiceEndpoint1* specificato nel frammento di manifesto fornito dopo questo paragrafo. Inoltre, i servizi possono richiedere anche una porta specifica in una risorsa. Alle repliche del servizio in esecuzione sui diversi nodi del cluster possono essere assegnati diversi numeri di porta, mentre le repliche di un servizio in esecuzione nello stesso nodo condividono la porta. Le repliche del servizio possono quindi usare queste porte in base alle esigenze per la replica e l'ascolto delle richieste client.

Quando si attiva un servizio che specifica un endpoint HTTPS, Service Fabric imposta la voce di controllo di accesso per la porta, associa il certificato server specificato alla porta e concede anche l'identità che il servizio è in esecuzione come autorizzazioni per la chiave privata del certificato. Il flusso di attivazione viene richiamato ogni volta che viene avviato Service Fabric o quando la dichiarazione del certificato dell'applicazione viene modificata tramite un aggiornamento. Il certificato dell'endpoint verrà monitorato anche per le modifiche o i rinnovi e le autorizzazioni verranno riapplicate periodicamente secondo necessità.

Al termine del servizio, Service Fabric pulisce la voce di controllo di accesso dell'endpoint e rimuove l'associazione del certificato. Tuttavia, qualsiasi autorizzazione applicata alla chiave privata del certificato non verrà pulita.

> [!WARNING] 
> Le porte statiche di progettazione non devono sovrapporsi all'intervallo di porte dell'applicazione specificato in ClusterManifest. Se si specifica una porta statica, assegnarla al di fuori dell'intervallo di porte dell'applicazione. in caso contrario, verrà generato un conflitto tra porte. Con la versione 6.5 CU2 verrà emesso un **avviso di integrità** quando si rileva un conflitto di questo tipo, ma si lascia che la distribuzione continui a essere sincronizzata con il comportamento 6,5 fornito. Tuttavia, potrebbe impedire la distribuzione dell'applicazione dalle versioni principali successive.
>
> Con la versione 7,0 verrà emesso un **avviso di integrità** quando si rileva che l'utilizzo dell'intervallo di porte dell'applicazione va oltre HostingConfig:: ApplicationPortExhaustThresholdPercentage (valore predefinito 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Se sono presenti più pacchetti di codice in un singolo pacchetto del servizio, è necessario fare riferimento anche al pacchetto di codice nella sezione **Endpoint**.  Ad esempio, se **ServiceEndpoint2a** e **ServiceEndpoint2b** sono endpoint dello stesso pacchetto del servizio che fanno riferimento a pacchetti di codice diversi, il pacchetto di codice corrispondente a ogni endpoint viene definito come indicato di seguito:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Per altre informazioni sugli endpoint di riferimento del file delle impostazioni del pacchetto di configurazione (settings.xml), vedere [Configurazione di Reliable Services con stato](service-fabric-reliable-services-configuration.md) .

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Esempio: specificare un endpoint HTTP per il servizio
Il manifesto del servizio seguente definisce una risorsa di endpoint TCP e due risorse di endpoint HTTP nell'elemento &lt;Risorse&gt;.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
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
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

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

> [!WARNING] 
> Quando si usa HTTPS, non usare la stessa porta e lo stesso certificato per diverse istanze del servizio (indipendenti dall'applicazione) distribuite nello stesso nodo. L'aggiornamento di due servizi diversi mediante la stessa porta in istanze dell'applicazione diverse comporterà un errore di aggiornamento. Per altre informazioni, vedere [Aggiornamento di più applicazioni con endpoint HTTPS](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Di seguito è riportato un esempio di ApplicationManifest che illustra la configurazione necessaria per un endpoint HTTPS. Il certificato server/endpoint può essere dichiarato tramite identificazione personale o nome comune del soggetto ed è necessario fornire un valore. EndpointRef è un riferimento a EndpointResource in ServiceManifest e il cui protocollo deve essere stato impostato sul protocollo "https". È possibile aggiungere più Endpointcertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
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
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Per i cluster Linux, l'archivio **MY** predefinito è la cartella **/var/lib/sfcerts**.

Per un esempio di applicazione completa che usa un endpoint HTTPS, vedere [aggiungere un endpoint HTTPS a un servizio front-end API Web ASP.NET Core usando gheppio](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-dotnet-app-enable-https-endpoint#define-an-https-endpoint-in-the-service-manifest).

## <a name="port-acling-for-http-endpoints"></a>ACLing porta per endpoint HTTP
Service Fabric verranno automaticamente ACL gli endpoint HTTP (S) specificati per impostazione predefinita. **Non** eseguirà la ACLing automatica se a un endpoint non è associato un [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md) e Service Fabric è configurato per essere eseguito con un account con privilegi di amministratore.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Override degli endpoint in ServiceManifest.xml

In ApplicationManifest aggiungere una sezione ResourceOverrides, che sarà una sezione di pari livello a ConfigOverrides. In questa sezione è possibile specificare l'override per la sezione Endpoint nella sezione delle risorse specificata in ServiceManifest. L'override degli endpoint è supportato nel runtime 5.7.217/SDK 2.7.217 e versioni successive.

Per eseguire l'override dell'EndPoint in ServiceManifest usando ApplicationParameters, modificare il tipo di oggetto ApplicationManifest in questo modo:

Nella sezione ServiceManifestImport aggiungere una nuova sezione "ResourceOverrides".

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

In Parameters aggiungere quanto riportato di seguito:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Durante la distribuzione dell'applicazione, è possibile passare questi valori come ApplicationParameters.  Ad esempio:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Nota: se il valore specificato per un determinato ApplicationParameter è vuoto, viene restituito il valore predefinito specificato in ServiceManifest per il corrispondente EndPointname.

Ad esempio:

Se in ServiceManifest è stato specificato

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Si supponga che il valore PORT1 e Protocol1 per i parametri dell'applicazione sia null o vuoto. La porta verrà decisa da ServiceFabric e il protocollo sarà TCP.

Si supponga di specificare un valore errato. Supponiamo che per Port sia stato specificato un valore stringa "foo" invece di int.  Il comando New-ServiceFabricApplication avrà esito negativo con un errore: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come definire gli endpoint nel manifesto del servizio Service Fabric. Per esempi più dettagliati, vedere:

> [!div class="nextstepaction"]
> [Esempi di manifesti dell'applicazione e del servizio](service-fabric-manifest-examples.md)

Per una procedura dettagliata per la creazione di pacchetti e la distribuzione di un'applicazione esistente in un cluster Service Fabric, vedere:

> [!div class="nextstepaction"]
> [Creare il pacchetto e distribuire un eseguibile esistente in Service Fabric](service-fabric-deploy-existing-app.md)