---
title: Probe di Azure Service FabricAzure Service Fabric probes
description: Come modellare Liveness Probe in Azure Service Fabric usando i file del manifesto dell'applicazione e del servizio.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431215"
---
# <a name="liveness-probe"></a>Sonda di liveness
A partire da 7.1 Service Fabric supporta il meccanismo Liveness Probe per le applicazioni [containerizzate.][containers-introduction-link] Liveness Probe aiuta ad annunciare la vivacità dell'applicazione containerizzata e quando non rispondono in modo tempestivo, si tradurrà in un riavvio.
Questo articolo fornisce una panoramica su come definire una sonda di liveness tramite file manifesto.

Prima di procedere con questo articolo, è consigliabile acquisire familiarità con il modello di [applicazione di Service Fabric][application-model-link] e il modello di hosting di Service [Fabric][hosting-model-link].

> [!NOTE]
> Liveness Probe è supportato solo per i contenitori in modalità di rete NAT.

## <a name="semantics"></a>Semantics
È possibile specificare solo 1 Sonda di Liveness per contenitore e controllare il suo comportamento con questi campi:You can specify only 1 Liveness Probe per container and can control it behavior with these fields:

* `initialDelaySeconds`: ritardo iniziale in secondi per l'avvio dell'esecuzione del probe una volta avviato il contenitore. Il valore supportato è int. Il valore predefinito è 0.Supported value is int. Default is 0. Il valore minimo è 0.

* `timeoutSeconds`: Periodo in secondi dopo il quale consideriamo il probe come non riuscito se non è stato completato correttamente. Il valore supportato è int. Il valore predefinito è 1.Supported value is int. Default is 1. Il valore minimo è 1.

* `periodSeconds`: Periodo in secondi per specificare la frequenza con cui sondiamo. Il valore supportato è int. Il valore predefinito è 10.Supported value is int. Default is 10. Il valore minimo è 1.

* `failureThreshold`: Una volta raggiunto FailureThreshold, il contenitore verrà riavviato. Il valore supportato è int. Il valore predefinito è 3.Supported value is int. Default is 3. Il valore minimo è 1.

* `successThreshold`: in caso di errore, affinché il probe venga considerato riuscito deve essere eseguito correttamente per SuccessThreshold. Il valore supportato è int. Il valore predefinito è 1.Supported value is int. Default is 1. Il valore minimo è 1.

Ci sarà al massimo 1 sonda al contenitore in un momento di tempo. Se il probe non viene completato in **timeoutSecondi,** continuiamo ad attenderlo e a contarlo verso **failureThreshold**. 

Inoltre, ServiceFabric genererà i report di integrità probe seguenti in DeployedServicePackage:Additionally, ServiceFabric will raise following probe [Health Reports][health-introduction-link] on DeployedServicePackage:

* `Ok`: se il probe ha esito positivo per **successThreshold,** il probe viene segnalato come OK.

* `Error`: se il valore di failureCount del probe è **un valore in**cui si riavvia, prima di riavviare il contenitore viene segnalato l'errore.

* `Warning`: 
    1. Se il probe ha esito negativo e failureCount < **failureThreshold** si segnala avviso. Questo report di integrità rimane fino a quando failureCount raggiunge **failureThreshold** o **successThreshold**.
    2. In caso di esito positivo, riportiamo ancora Avviso ma con l'esito positivo consecutivo aggiornato.

## <a name="specifying-liveness-probe"></a>Specifica della sonda di liveness

È possibile specificare probe nel file ApplicationManifest.xml in ServiceManifestImport:You can specify probe in the ApplicationManifest.xml under ServiceManifestImport:

La sonda può utilizzare uno dei due:

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>Sonda HTTP

Per il probe HTTP, Service Fabric invierà una richiesta HTTP alla porta e al percorso specificati. Il codice restituito maggiore o uguale a 200 e minore di 400 indica l'esito positivo.

Di seguito è riportato un esempio di come specificare il probe HttpGet:Here is an example of how to specify HttpGet probe:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

Il probe HttpGet ha proprietà aggiuntive che è possibile impostare:HttpGet probe has additional properties you can set:

* `path`: percorso di accesso alla richiesta HTTP.

* `port`: porta di accesso per le sonde. L'intervallo è compreso tra 1 e 65535. Mandatory.

* `scheme`: schema da utilizzare per la connessione al pacchetto di codice. Se impostato su HTTPS, la verifica del certificato viene ignorata. Il valore predefinito è HTTP

* `httpHeader`: intestazioni da impostare nella richiesta. È possibile specificarne più.

* `host`: IP host a cui connettersi.

## <a name="tcp-probe"></a>Sonda TCP

Per il probe TCP, Service Fabric tenterà di aprire un socket nel contenitore con la porta specificata. Se è in grado di stabilire una connessione, il probe viene considerato positivo. Di seguito è riportato un esempio di come specificare il probe che utilizza il socket TCP:Here is an example of how to specify probe which uses TCP socket:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Sonda Exec

Questo probe emetterà un exec nel contenitore e attenderà il completamento del comando.

> [!NOTE]
> Exec comando accetta una stringa con separatore virgola. Il comando seguente nell'esempio funzionerà per il contenitore Linux.The following command in the example will work for Linux container.
> Se si sta provando windows container, utilizzare <Command>cmd</Command>

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni correlate, vedere gli articoli seguenti.
* [Service Fabric e contenitori.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

