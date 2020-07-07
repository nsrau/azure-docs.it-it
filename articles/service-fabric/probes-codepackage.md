---
title: Probe di Service Fabric di Azure
description: Come modellare un probe di Livenza in Azure Service Fabric usando i file manifesto dell'applicazione e del servizio.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82137587"
---
# <a name="liveness-probe"></a>Probe di liveity
A partire dalla versione 7,1, Azure Service Fabric supporta un meccanismo di probe di Livezza per le applicazioni incluse in [contenitori][containers-introduction-link] . Un probe di Livenza consente di segnalare la durata di un'applicazione in contenitori, che verrà riavviata se non risponde velocemente.
Questo articolo fornisce una panoramica su come definire un probe di Livezza usando i file manifesto.

Prima di procedere con questo articolo, acquisire familiarità con il [modello di applicazione Service Fabric][application-model-link] e con il [modello di hosting Service Fabric][hosting-model-link].

> [!NOTE]
> Il probe di liveity è supportato solo per i contenitori in modalità di rete NAT.

## <a name="semantics"></a>Semantica
È possibile specificare un solo Probe di liveity per ogni contenitore e controllarne il comportamento usando i campi seguenti:

* `initialDelaySeconds`: Ritardo iniziale in secondi per l'esecuzione del probe dopo l'avvio del contenitore. Il valore supportato è **int**. Il valore predefinito è 0 e il valore minimo è 0.

* `timeoutSeconds`: Il periodo in secondi dopo il quale il probe viene considerato non riuscito, se non è stato completato correttamente. Il valore supportato è **int**. Il valore predefinito è 1 e il valore minimo è 1.

* `periodSeconds`: Periodo in secondi per specificare la frequenza del probe. Il valore supportato è **int**. Il valore predefinito è 10 e il valore minimo è 1.

* `failureThreshold`: Quando si raggiunge questo valore, il contenitore viene riavviato. Il valore supportato è **int**. Il valore predefinito è 3 e il valore minimo è 1.

* `successThreshold`: In caso di errore, perché il probe venga considerato riuscito, deve essere eseguito correttamente per questo valore. Il valore supportato è **int**. Il valore predefinito è 1 e il valore minimo è 1.

In qualsiasi momento può essere presente, al massimo, un probe per un contenitore. Se il probe non termina nel tempo impostato in **timeoutSeconds**, attendere e contare il tempo verso il **failureThreshold**. 

Inoltre, Service Fabric genererà i report sull' [integrità][health-introduction-link] dei probe seguenti in **DeployedServicePackage**:

* `OK`: Il probe ha esito positivo per il valore impostato in **successThreshold**.

* `Error`: Il probe **failureCount**  ==   **failureThreshold**prima che il contenitore venga riavviato.

* `Warning`: 
    * Il probe ha esito negativo e **failureCount**  <  **failureThreshold**. Questo report sull'integrità rimane attivo fino a quando **failureCount** raggiunge il valore impostato in **failureThreshold** o **successThreshold**.
    * In caso di esito negativo, l'avviso rimane ma con successi consecutivi aggiornati.

## <a name="specifying-a-liveness-probe"></a>Specifica di un probe di liveity

È possibile specificare un probe nel file di ApplicationManifest.xml in **ServiceManifestImport**.

Il Probe può essere per uno dei seguenti elementi:

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>Probe HTTP

Per un probe HTTP, Service Fabric invierà una richiesta HTTP alla porta e al percorso specificati. Un codice restituito maggiore o uguale a 200 e minore di 400 indica l'esito positivo.

Di seguito è riportato un esempio di come specificare un probe HTTP:

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

Il probe HTTP ha proprietà aggiuntive che è possibile impostare:

* `path`: Percorso da usare nella richiesta HTTP.

* `port`: Porta da usare per i probe. Questa proprietà è obbligatoria. L'intervallo è compreso tra 1 e 65535.

* `scheme`: Schema da utilizzare per la connessione al pacchetto di codice. Se questa proprietà è impostata su HTTPS, la verifica del certificato viene ignorata. L'impostazione predefinita è HTTP.

* `httpHeader`: Le intestazioni da impostare nella richiesta. È possibile specificare più intestazioni.

* `host`: Indirizzo IP dell'host a cui connettersi.

### <a name="tcp-probe"></a>Probe TCP

Per un probe TCP, Service Fabric tenterà di aprire un socket sul contenitore utilizzando la porta specificata. Se è possibile stabilire una connessione, il probe viene considerato riuscito. Di seguito è riportato un esempio di come specificare un probe che usa un socket TCP:

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

### <a name="exec-probe"></a>Probe Exec

Questo Probe emetterà un comando **Exec** nel contenitore e attenderà il completamento del comando.

> [!NOTE]
> Il comando **Exec** accetta una stringa separata da virgole. Il comando nell'esempio seguente funzionerà per un contenitore Linux.
> Se si sta provando a eseguire il probe di un contenitore di Windows, usare **cmd**.

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
Per informazioni correlate, vedere l'articolo seguente:
* [Service Fabric e contenitori][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

