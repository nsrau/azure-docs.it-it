---
title: Governance delle risorse di Azure Service Fabric per contenitori e i servizi | Microsoft Docs
description: Azure Service Fabric consente di specificare limiti di risorse per i servizi in esecuzione all'interno o all'esterno di contenitori.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 62ce17533f8c195b873431089e1f1f47cb0bcbec
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="resource-governance"></a>Governance delle risorse 

Quando si eseguono più servizi sullo stesso nodo o cluster, è possibile che uno di essi consumi molte risorse a scapito degli altri. Questo problema prende il nome di noisy neighbor effect. Service Fabric consente allo sviluppatore di specificare prenotazioni e limiti per servizio in modo da garantire le risorse e limitarne l'utilizzo.

>
> Prima di procedere con questo articolo, è necessario acquisire familiarità con [il modello applicativo di Service Fabric](service-fabric-application-model.md) e con [il modello di hosting di Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metriche di governance delle risorse 

In Service Fabric la governance delle risorse è supportata per [pacchetto del servizio](service-fabric-application-model.md). Le risorse assegnate a un pacchetto del servizio possono essere ulteriormente divise tra pacchetti di codice. I limiti di risorse specificati implicano anche la prenotazione delle stesse. Service Fabric supporta la specifica di CPU e memoria per pacchetto del servizio mediante due [metriche](service-fabric-cluster-resource-manager-metrics.md) predefinite:

* CPU (nome della metrica `servicefabric:/_CpuCores`): una memoria centrale è una memoria centrale logica disponibile nel computer host. Tutte le memorie centrali in tutti i nodi vengono ponderate nello stesso modo.
* Memoria (nome della metrica `servicefabric:/_MemoryInMB`): la memoria viene espressa in megabyte ed esegue il mapping alla memoria fisica disponibile nel computer.

Per queste due metriche, [Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) rileva la capacità totale del cluster, il carico presente su ciascun nodo e le restanti risorse del cluster. Queste due metriche sono equivalenti a qualsiasi altra metrica dell'utente o personalizzata e possono essere usate con tutte le funzionalità esistenti:
* Il cluster può essere [bilanciato](service-fabric-cluster-resource-manager-balancing.md) in base a queste due metriche (comportamento predefinito).
* Il cluster può essere [deframmentato](service-fabric-cluster-resource-manager-defragmentation-metrics.md) in base a queste due metriche.
* Quando si [descrive un cluster](service-fabric-cluster-resource-manager-cluster-description.md), è possibile impostare il buffer di capacità per queste due metriche.

La [creazione di report sul carico dinamico](service-fabric-cluster-resource-manager-metrics.md) non è supportata per queste metriche e i relativi carichi vengono definiti al momento della creazione.

## <a name="resource-governance-mechanism"></a>Meccanismi di governance delle risorse

Il runtime di Service Fabric attualmente non fornisce la prenotazione delle risorse. Quando si apre un processo o un contenitore, il runtime imposta i limiti delle risorse sui carichi definiti al momento della creazione. Inoltre, il runtime non esegue l'apertura di nuovi pacchetti del servizio se vengono superate le risorse disponibili. Per comprendere meglio il funzionamento del processo, si prenda come esempio un nodo con 2 core CPU (il meccanismo per governance della memoria è lo stesso):

1. In primo luogo, un contenitore viene posizionato sul nodo. È richiesto 1 core CPU. Il runtime apre il contenitore e imposta il limite di CPU su 1 core. Il contenitore non potrà usare più di 1 core.
2. Successivamente, una replica del servizio viene posizionata sul nodo e il pacchetto del servizio corrispondente specifica un limite di 1 core CPU. Il runtime apre il pacchetto di codice e imposta il limite di CPU su 1 core.

A quel punto, la somma dei limiti corrisponde alla capacità del nodo. Un processo e un contenitore sono in esecuzione, ciascuno con 1 core, e non interferiscono uno con l'altro. Service Fabric non posiziona altri contenitori o repliche nel caso in cui è specificato il limite di CPU. Tuttavia, ci sono due situazioni in cui altri processi potrebbero contendersi CPU e un processo e un contenitore dell'esempio precedente potrebbero avere dei problemi:

* L'uso combinato di servizi e contenitori regolamentati e non regolamentati. Se un utente crea un servizio senza specificare la governance delle risorse, il runtime considera che il servizio non consuma risorse e sarà in grado di posizionarlo sul nodo dell'esempio precedente. In questo caso, il nuovo processo consumerà della CPU a spese dei servizi già in esecuzione sul nodo. Per risolvere il problema, evitare l'uso combinato di servizi regolati e non regolati nello stesso cluster oppure usare dei [vincoli di posizionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) in modo che non vengano posizionati nello stesso set di nodi.
* Nel caso in cui un altro processo sia stato avviato sul nodo, al di fuori di Service Fabric (ad esempio, un servizio del sistema operativo), quel processo si contenderà la CPU con i servizi esistenti. Per risolvere il problema, impostare correttamente le capacità del nodo per tenere conto del sovraccarico del sistema operativo, come descritto nella sezione seguente.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Configurazione del cluster per l'abilitazione della governance delle risorse

Quando il nodo viene avviato e viene aggiunto al cluster, Service Fabric individua la quantità di memoria disponibile e il numero di core disponibili e imposta le capacità del nodo per quelle due risorse. Per lasciare dello spazio di buffer per il sistema operativo e per altri processi che potrebbero essere eseguiti sul nodo, Service Fabric usa solo l'80% delle risorse disponibili sul nodo. Questa percentuale è configurabile e può essere modificata nel manifesto del cluster. Di seguito è fornito un esempio di come impostare Service Fabric per usare il 50% della CPU disponibile e il 70% della memoria disponibile: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Nel caso in cui fosse necessario configurare manualmente le capacità del nodo, è possibile usare il meccanismo standard per descrivere i nodi nel cluster. Di seguito è riportato un esempio di configurazione del nodo con 4 core e 2 GB di memoria: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Se è abilitato il rilevamento automatico delle risorse disponibili e le capacità del nodo sono definite manualmente nel manifesto del cluster, Service Fabric verificherà che il nodo abbia risorse sufficienti per soddisfare la capacità definita dall'utente:
* Se le capacità del nodo definite nel manifesto sono inferiori o uguali alle risorse disponibili sul nodo, Service Fabric userà le capacità specificate nel manifesto.
* Se le capacità del nodo definite nel manifesto sono superiori delle risorse disponibili, Service Fabric userà le risorse disponibili come capacità del nodo.

Nel caso in cui il rilevamento automatico delle risorse disponibili non sia necessario, è possibile disabilitarlo completamente modificando questa impostazione:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Per ottenere prestazioni ottimali, nel manifesto del cluster è necessario attivare anche l'impostazione seguente: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>Specifica della governance delle risorse 

I limiti di governance delle risorse vengono specificati nel manifesto dell'applicazione (sezione ServiceManifestImport) come mostrato nell'esempio seguente:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
In questo esempio il pacchetto del servizio ServicePackageA ottiene una memoria centrale sui nodi in cui è posizionato. Il pacchetto del servizio contiene due pacchetti di codice, CodeA1 e CodeA2, che specificano entrambi il parametro `CpuShares`. La proporzione CpuShares 512:256 divide la memoria centrale tra i due pacchetti di codice. Di conseguenza, in questo esempio CodeA1 ottiene due terzi di una memoria centrale e CodeA2 ne ottiene un terzo, oltre a una prenotazione con garanzia flessibile sulla stessa. Nel caso in cui CpuShares non sia specificato per i pacchetti di codice, Service Fabric divide le memorie centrali in parti uguali tra di essi.

I limiti di memoria sono assoluti, motivo per cui i pacchetti di codice sono limitati a 1024 MB di memoria, con prenotazione a garanzia flessibile. I pacchetti di codice (contenitori o pacchetti) non sono in grado di allocare una quantità di memoria superiore a questo limite. Un'operazione di questo tipo genererebbe un'eccezione di memoria esaurita. Perché l'imposizione di un limite di risorse funzioni, è necessario che tutti i pacchetti di codice inclusi in un pacchetto del servizio abbiano limiti di memoria specificati.

## <a name="other-resources-for-containers"></a>Altre risorse per i contenitori
Oltre alla CPU e alla memoria, è possibile specificare i limiti di altre risorse per i contenitori. Questi limiti sono specificati a livello del pacchetto di codice e verranno applicati quando il contenitore viene avviato. A differenza di CPU e memoria, Cluster Resource Manager non riconoscerà queste risorse e non eseguirà verifiche della capacità o bilanciamento del carico. 

* MemorySwapInMB: la quantità di memoria di scambio che un contenitore può usare.
* MemoryReservationInMB: il limite flessibile per la governance della memoria che viene applicato solo quando si verificano problemi di contesa della memoria sul nodo.
* CpuPercent: la percentuale di CPU che il contenitore può usare. Se vengono specificati dei limiti di CPU per il pacchetto del servizio, questo parametro viene ignorato.
* MaximumIOps: il numero massimo di IOPS che un contenitore può usare (lettura e scrittura).
* MaximumIOBytesps: IO massimo (byte al secondo) che un contenitore può usare (lettura e scrittura).
* BlockIOWeight: bloccare il peso IO rispetto agli altri contenitori.

Queste risorse possono essere usate in combinazione con CPU e memoria. Di seguito è riportato un esempio di come specificare risorse aggiuntive per i contenitori:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su Cluster Resource Manager, leggere questo [articolo](service-fabric-cluster-resource-manager-introduction.md).
* Per altre informazioni sul modello dell'applicazione, i pacchetti del servizio e i pacchetti di codice, nonché sul mapping delle repliche a tali elementi, leggere questo [articolo](service-fabric-application-model.md).

