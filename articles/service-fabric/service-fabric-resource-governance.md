---
title: Governance delle risorse di Azure Service Fabric per contenitori e i servizi | Microsoft Docs
description: Azure Service Fabric consente di specificare limiti di risorse per i servizi in esecuzione all'interno o all'esterno di contenitori.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>Governance delle risorse 

Quando si eseguono più servizi sullo stesso nodo o cluster, è possibile che uno di essi consumi molte risorse a scapito degli altri. Questo problema prende il nome di noisy neighbor effect. Service Fabric consente allo sviluppatore di specificare prenotazioni e limiti per servizio in modo da garantire le risorse e limitarne l'utilizzo. 

## <a name="resource-governance-metrics"></a>Metriche di governance delle risorse 

In Service Fabric la governance delle risorse è supportata per [pacchetto del servizio](service-fabric-application-model.md). Le risorse assegnate a un pacchetto del servizio possono essere ulteriormente divise tra pacchetti di codice. I limiti di risorse specificati implicano anche la prenotazione delle stesse. Service Fabric supporta la specifica di CPU e memoria per pacchetto del servizio mediante due [metriche](service-fabric-cluster-resource-manager-metrics.md) predefinite:

* CPU (nome della metrica `ServiceFabric:/_CpuCores`): una memoria centrale è una memoria centrale logica disponibile nel computer host. Tutte le memorie centrali in tutti i nodi vengono ponderate nello stesso modo.
* Memoria (nome della metrica `ServiceFabric:/_MemoryInMB`): la memoria viene espressa in megabyte ed esegue il mapping alla memoria fisica disponibile nel computer.

Vengono fornite solo garanzie di prenotazione flessibile: il runtime rifiuta l'apertura di nuovi pacchetti del servizio se vengono superate le risorse disponibili. Tuttavia, se nel nodo viene posizionato un altro file eseguibile o un contenitore, tale elemento può superare le garanzie di prenotazione originali.

Per queste due metriche, [Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) rileva la capacità totale del cluster, il carico presente su ciascun nodo e le restanti risorse del cluster. Queste due metriche sono equivalenti a qualsiasi altra metrica dell'utente o personalizzata e possono essere usate con tutte le funzionalità esistenti:
* Il cluster può essere [bilanciato](service-fabric-cluster-resource-manager-balancing.md) in base a queste due metriche (comportamento predefinito).
* Il cluster può essere [deframmentato](service-fabric-cluster-resource-manager-defragmentation-metrics.md) in base a queste due metriche.
* Quando si [descrive un cluster](service-fabric-cluster-resource-manager-cluster-description.md), è possibile impostare il buffer di capacità per queste due metriche.

La [creazione di report sul carico dinamico](service-fabric-cluster-resource-manager-metrics.md) non è supportata per queste metriche e i relativi carichi vengono definiti al momento della creazione.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Configurazione del cluster per l'abilitazione della governance delle risorse

È necessario definire manualmente la capacità in ogni tipo di nodo del cluster nel modo indicato di seguito:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
La governance delle risorse è consentita solo per i servizi utente, non per qualsiasi servizio di sistema. Quando si specifica la capacità, è necessario lasciare non allocate alcune memorie centrali e parte della memoria per i servizi di sistema. Per ottenere prestazioni ottimali, nel manifesto del cluster è necessario attivare anche l'impostazione seguente: 

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


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su Cluster Resource Manager, leggere questo [articolo](service-fabric-cluster-resource-manager-introduction.md).
* Per altre informazioni sul modello dell'applicazione, i pacchetti del servizio e i pacchetti di codice, nonché sul mapping delle repliche a tali elementi, leggere questo [articolo](service-fabric-application-model.md).

