---
title: Comunicazione per i ruoli in servizi Cloud | Documentazione Microsoft
description: Le istanze del ruolo in servizi Cloud possono avere endpoint (http, https, tcp, udp) definiti appositamente che comunicano con l&quot;esterno oppure tra le altre istanze del ruolo.
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 7b1b50f71158e62da6b8ceb741a5b8cf9795e3a4
ms.openlocfilehash: 8e171d56bb67c971337fa383014988074ec828b1


---
# <a name="enable-communication-for-role-instances-in-azure"></a>Abilitare la comunicazione delle istanze del ruolo in azure
I ruoli del servizio cloud comunicano tramite connessioni interne ed esterne. Le connessioni esterne vengono chiamate **endpoint di input** mentre le connessioni interne vengono chiamate **endpoint interni**. In questo argomento viene descritto come modificare la [definizione del servizio](cloud-services-model-and-package.md#csdef) per creare gli endpoint.

## <a name="input-endpoint"></a>Endpoint di input
L'endpoint di input viene utilizzato quando si desidera esporre una porta all'esterno. Specificare il tipo di protocollo e porta dell'endpoint che vengono poi applicati per le porte interne ed esterne per l'endpoint. Se si desidera, è possibile specificare una porta interna diversa per l'endpoint con l’attributo [localPort](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) .

L'endpoint di input può utilizzare i seguenti protocolli: **http, https, tcp, udp**.

Per creare un endpoint di input, aggiungere elemento figlio **InputEndpoint** all'elemento **Endpoints** del ruolo di lavoro o del ruolo Web.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Endpoint di input dell'istanza
Gli endpoint di input dell'istanza sono simili agli endpoint di input ma consentono di eseguire il mapping di specifiche porte pubbliche per ogni singola istanza del ruolo mediante il port forwarding nel servizio di bilanciamento del carico. È possibile specificare una singola porta pubblica o un intervallo di porte.

L'endpoint di input dell’istanza può usare solo i protocolli **tcp** o **udp**.

Per creare un endpoint di input dell'istanza, aggiungere l'elemento figlio **InstanceInputEndpoint** all'elemento **Endpoints** del ruolo di lavoro o del ruolo Web.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Endpoint interno
Gli endpoint interni sono disponibili per la comunicazione da istanza a istanza. La porta è facoltativa e se omessa, viene assegnata una porta dinamica all'endpoint. Può essere utilizzato un intervallo di porte. Esiste un limite di cinque endpoint interni per ogni ruolo.

L'endpoint interno può utilizzare i seguenti protocolli: **http, https, tcp, udp**.

Per creare un endpoint di input interno, aggiungere l'elemento figlio **InternalEndpoint** all'elemento **Endpoints** del ruolo di lavoro o del ruolo Web.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

È possibile inoltre utilizzare un intervallo di porte.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Ruoli di lavoro a confronto con Ruoli Web
Esiste una piccola differenza tra gli endpoint quando si lavora con i ruoli di lavoro e i ruoli  web. Il ruolo web deve disporre almeno di un singolo endpoint di input che utilizzi il protocollo **HTTP** .

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Uso di .NET SDK per accedere ad un endpoint
La libreria gestita di Azure fornisce metodi per la comunicazione di istanze del ruolo in fase di esecuzione. Dal codice eseguito all'interno di un'istanza del ruolo, è possibile recuperare informazioni sull'esistenza di altre istanze del ruolo e sui relativi endpoint, nonché le informazioni sull'istanza del ruolo corrente.

> [!NOTE]
> È possibile recuperare solo le informazioni sulle istanze dei ruoli che sono in esecuzione nel servizio cloud e che definiscono almeno un endpoint interno. Non è possibile ottenere dati sulle istanze dei ruoli in esecuzione in un altro servizio.
> 
> 

È possibile utilizzare la proprietà [Istanze](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) per recuperare le istanze di un ruolo. Usare prima di tutto [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) per restituire un riferimento all'istanza del ruolo corrente, quindi usare la proprietà [Role](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) per restituire un riferimento al ruolo stesso.

Quando ci si connette a un'istanza del ruolo a livello di programmazione tramite il SDK di .NET, è relativamente semplice accedere alle informazioni relative all’endpoint. Ad esempio, dopo essersi connessi a un ambiente di ruolo specifico, è possibile ottenere la porta di un endpoint specifico con il seguente codice:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

La proprietà **Instances** restituisce una raccolta di oggetti **RoleInstance**. Tale raccolta contiene sempre l'istanza corrente. Se il ruolo non definisce un endpoint interno, la raccolta include l'istanza corrente ma non altre istanze. Il numero di istanze del ruolo nella raccolta sarà sempre 1 nel caso in cui non sia stato definito alcun endpoint interno per il ruolo. Se il ruolo definisce un endpoint interno, le relative istanze sono individuabili in fase di esecuzione e il numero di istanze nella raccolta corrisponderà al numero di istanze specificato per il ruolo nel file di configurazione del servizio.

> [!NOTE]
> La libreria gestita di Azure non rappresenta un mezzo per determinare lo stato di altre istanze del ruolo, ma è possibile implementare tali valutazioni manualmente se il servizio necessita di tale funzionalità. È possibile utilizzare la [Diagnostica di Azure](cloud-services-dotnet-diagnostics.md) per ottenere informazioni sull'esecuzione di istanze del ruolo.
> 
> 

Per determinare il numero di porta per un endpoint interno in un'istanza del ruolo, è possibile utilizzare la proprietà [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) per restituire un oggetto Dictionary che contenga i nomi degli endpoint e i corrispondenti indirizzi IP e porte. La proprietà [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) restituisce l'indirizzo IP e la porta per un endpoint specificato. La proprietà **PublicIPEndpoint** restituisce la porta per un endpoint con carico bilanciato. La parte relativa all’indirizzo IP della proprietà **PublicIPEndpoint** non viene utilizzata.

Di seguito è riportato un esempio che consente di eseguire l’iterazione delle istanze del ruolo.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Di seguito è riportato un esempio di un ruolo di lavoro che ottiene l'endpoint esposto tramite la definizione del servizio e avvia l'ascolto per le connessioni.

> [!WARNING]
> Questo codice funziona solo per un servizio distribuito. Durante l'esecuzione nell'emulatore di calcolo di Azure, gli elementi di configurazione del servizio che creano endpoint porte dirette (elementi**InstanceInputEndpoint** ) vengono ignorati.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regole di traffico di rete per controllare la comunicazione di ruolo
Dopo aver definito gli endpoint interni, è possibile aggiungere regole del traffico di rete (in base agli endpoint che sono stati creati) per controllare il modo in cui le istanze del ruolo possono comunicare tra loro. Nel diagramma seguente vengono illustrati alcuni scenari comuni relativi al controllo della comunicazione del ruolo:

![Scenari di regole del traffico di rete](./media/cloud-services-enable-communication-role-instances/scenarios.png "Scenari di regole del traffico di rete")

Il seguente esempio di codice mostra le definizioni di ruolo per i ruoli illustrati nel diagramma precedente. Ogni definizione di ruolo include almeno un endpoint interno definito:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> La restrizione della comunicazione tra ruoli può verificarsi con endpoint interni di porte fisse o assegnate automaticamente.
> 
> 

Per impostazione predefinita, dopo aver definito un endpoint interno, la comunicazione può avvenire tra qualsiasi ruolo e l’endpoint interno di un ruolo senza restrizioni. Per limitare la comunicazione, è necessario aggiungere un elemento **NetworkTrafficRules** all'elemento **ServiceDefinition** nel file di definizione del servizio.

### <a name="scenario-1"></a>Scenario 1
Consentire solo il traffico di rete da **WebRole1** a **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenario 2
Consente solo il traffico di rete da **WebRole1** a **WorkerRole1** e **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenario 3
Consente solo il traffico di rete da **WebRole1** a **WorkerRole1** e da **WorkerRole1** a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenario 4
Consente solo il traffico di rete da **WebRole1** a **WorkerRole1**, **WebRole1** a **WorkerRole2** e da **WorkerRole1** a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Un riferimento allo schema XML per gli elementi utilizzati in precedenza è reperibile [qui](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sul [modello](cloud-services-model-and-package.md)del servizio Cloud.




<!--HONumber=Dec16_HO3-->


