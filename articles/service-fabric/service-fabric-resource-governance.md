---
title: Governance delle risorse per contenitori e servizi
description: Service Fabric di Azure consente di specificare le richieste e i limiti delle risorse per i servizi in esecuzione come processi o contenitori.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 889fce77c1a3a743e9805ec482a9c87b9bf8da65
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172863"
---
# <a name="resource-governance"></a>Governance delle risorse

Quando si eseguono più servizi nello stesso nodo o cluster, è possibile che un servizio possa usare più risorse, affamando altri servizi nel processo. Questo problema prende il nome di "noisy neighbor effect". Azure Service Fabric consente allo sviluppatore di controllare questo comportamento specificando le richieste e i limiti per servizio per limitare l'utilizzo delle risorse.

> Prima di procedere con questo articolo, è consigliabile acquisire familiarità con il [modello di applicazione di Service Fabric][application-model-link] e il [modello di hosting di Service Fabric][hosting-model-link].
>

## <a name="resource-governance-metrics"></a>Metriche di governance delle risorse

In Service Fabric la governance delle risorse è supportata in base al [pacchetto servizio][application-model-link]. Le risorse assegnate al pacchetto servizio possono essere ulteriormente divise tra pacchetti di codice. Service Fabric supporta la governance della CPU e della memoria per ogni pacchetto di servizio, con due [metriche](service-fabric-cluster-resource-manager-metrics.md)predefinite:

* *CPU* (nome della metrica `servicefabric:/_CpuCores` ): un core logico disponibile nel computer host. Tutti i core in tutti i nodi hanno lo stesso peso.

* *Memoria* (nome `servicefabric:/_MemoryInMB` della metrica): la memoria è espressa in megabyte e viene mappata alla memoria fisica disponibile nel computer.

Per queste due metriche, [cluster gestione risorse (CRM)][cluster-resource-manager-description-link] rileva la capacità totale del cluster, il carico su ogni nodo nel cluster e le risorse rimanenti nel cluster. Queste due metriche sono equivalenti a qualsiasi altra metrica utente o personalizzata. Possono essere usate tutte le funzionalità esistenti:

* Il cluster può essere [bilanciato](service-fabric-cluster-resource-manager-balancing.md) in base a queste due metriche (comportamento predefinito).
* Il cluster può essere [deframmentato](service-fabric-cluster-resource-manager-defragmentation-metrics.md) in base a queste due metriche.
* Quando si [descrive un cluster][cluster-resource-manager-description-link], è possibile impostare il buffer di capacità per queste due metriche.

> [!NOTE]
> La [creazione di report di carico dinamico](service-fabric-cluster-resource-manager-metrics.md) non è supportata per queste metriche. i caricamenti per queste metriche vengono definiti al momento della creazione.

## <a name="resource-governance-mechanism"></a>Meccanismi di governance delle risorse

A partire dalla versione 7,2, Service Fabric Runtime supporta la specifica di richieste e limiti per le risorse di CPU e memoria.

> [!NOTE]
> Service Fabric versioni di runtime precedenti a 7,2 supportano solo un modello in cui un singolo valore funge sia da **richiesta** che da **limite** per una determinata risorsa (CPU o memoria). Questa operazione viene descritta come specifica **RequestsOnly** in questo documento.

* *Richieste:* I valori della richiesta di CPU e memoria rappresentano i carichi usati dal [Cluster gestione risorse (CRM)][cluster-resource-manager-description-link] per le `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metriche e. In altre parole, CRM considera il consumo di risorse di un servizio uguale ai valori di richiesta e usa questi valori quando si prendono decisioni di selezione host.

* *Limiti:* I valori limite di CPU e memoria rappresentano i limiti effettivi delle risorse applicati quando un processo o un contenitore viene attivato in un nodo.

Service Fabric consente **RequestsOnly, LimitsOnly** e entrambe le specifiche **RequestsAndLimits** per CPU e memoria.
* Quando si usa la specifica RequestsOnly, Service Fabric usa anche i valori della richiesta come limiti.
* Quando si usa la specifica LimitsOnly, Service Fabric considera i valori della richiesta come 0.
* Quando si utilizza la specifica RequestsAndLimits, i valori limite devono essere maggiori o uguali ai valori della richiesta.

Per comprendere meglio il meccanismo di governance delle risorse, esaminiamo uno scenario di posizionamento di esempio con una specifica **RequestsOnly** per la risorsa CPU (il meccanismo per la governance della memoria è equivalente). Si consideri un nodo con due core CPU e due pacchetti del servizio che verranno posizionati su di esso. Il primo pacchetto del servizio da inserire è costituito da un solo pacchetto di codice contenitore e specifica solo una richiesta di un core CPU. Il secondo pacchetto del servizio da inserire è costituito da un solo pacchetto di codice basato su processo e specifica solo una richiesta di un core CPU. Poiché entrambi i pacchetti del servizio hanno una specifica RequestsOnly, i relativi valori limite vengono impostati sui valori della richiesta.

1. Prima di tutto, il pacchetto del servizio basato su contenitore che richiede un core CPU viene inserito nel nodo. Il runtime attiva il contenitore e imposta il limite della CPU su un core. Il contenitore non potrà usare più di un core.

2. Successivamente, il pacchetto del servizio basato su processo che richiede un core CPU viene inserito nel nodo. Il runtime attiva il processo del servizio e imposta il limite della CPU su un core.

A questo punto, la somma delle richieste è uguale alla capacità del nodo. CRM non inserirà altri contenitori o processi di servizio con richieste di CPU in questo nodo. Nel nodo un processo e un contenitore sono in esecuzione con un core ciascuno e non si contendono l'uno con l'altro per la CPU.

Ora rivisitiamo l'esempio con una specifica **RequestsAndLimits** . Questa volta il pacchetto del servizio basato su contenitori specifica una richiesta di un core CPU e un limite di due core CPU. Il pacchetto del servizio basato su processo specifica sia una richiesta che un limite di un core CPU.
  1. Prima di tutto il pacchetto del servizio basato su contenitore viene inserito nel nodo. Il runtime attiva il contenitore e imposta il limite della CPU su due core. Il contenitore non potrà usare più di due core.
  2. Successivamente, il pacchetto del servizio basato su processo viene inserito nel nodo. Il runtime attiva il processo del servizio e imposta il limite della CPU su un core.

  A questo punto, la somma delle richieste di CPU dei pacchetti del servizio posizionati sul nodo è uguale alla capacità della CPU del nodo. CRM non inserirà altri contenitori o processi di servizio con richieste di CPU in questo nodo. Tuttavia, nel nodo, la somma dei limiti (due core per il contenitore + un core per il processo) supera la capacità di due core. Se il contenitore e il processo si eshanno nello stesso tempo, è possibile che si verifichi una contesa per la risorsa CPU. Tale contesa verrà modificato dal sistema operativo sottostante per la piattaforma. Per questo esempio, il contenitore può aumentare fino a due core CPU, causando la mancata garanzia della richiesta del processo di un core CPU.

> [!NOTE]
> Come illustrato nell'esempio precedente, i valori della richiesta per CPU e memoria **non comporteranno la prenotazione di risorse in un nodo**. Questi valori rappresentano il consumo di risorse che il cluster Gestione risorse considera per prendere decisioni relative al posizionamento. I valori limite rappresentano i limiti effettivi delle risorse applicati quando un processo o un contenitore viene attivato in un nodo.


Ci sono alcune situazioni in cui potrebbe esserci una contesa per la CPU. In queste situazioni, il processo e il contenitore dell'esempio potrebbero riscontrare un problema vicino al rumore:

* *Uso combinato di servizi e contenitori regolamentati e non regolamentati*: se un utente crea un servizio senza specificare la governance delle risorse, il runtime ritiene che non consumi risorse e può inserirlo nel nodo dell'esempio precedente. In questo caso, il nuovo processo consuma CPU a spese dei servizi già in esecuzione sul nodo. Esistono due soluzioni a questo problema. Evitare l'uso combinato di servizi regolamentati e non regolamentati nello stesso cluster oppure usare [vincoli di selezione host](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) in modo che questi due tipi di servizi non vengano inseriti nello stesso set di nodi.

* *Quando un altro processo viene avviato nel nodo, al di fuori di Service Fabric (ad esempio, un servizio del sistema operativo)*: in questa situazione, anche il processo esterno a Service Fabric si contenderà la CPU con i servizi esistenti. Per risolvere il problema, impostare correttamente le capacità del nodo per tenere conto del sovraccarico del sistema operativo, come descritto nella sezione seguente.

* *Quando le richieste non sono uguali ai limiti*: come descritto nell'esempio RequestsAndLimits precedente, le richieste non comportano la prenotazione di risorse in un nodo. Quando un servizio con limiti superiori a richieste viene inserito in un nodo, può utilizzare le risorse (se disponibili) fino al limite massimo. In questi casi, altri servizi nel nodo potrebbero non essere in grado di utilizzare le risorse fino ai valori della richiesta.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configurazione del cluster per l'abilitazione della governance delle risorse

Quando un nodo viene avviato e aggiunto al cluster, Service Fabric rileva la quantità di memoria disponibile e il numero di core disponibili e quindi imposta le capacità del nodo per le due risorse.

Per lasciare lo spazio del buffer per il sistema operativo e per altri processi che potrebbero essere in esecuzione nel nodo, Service Fabric utilizza solo il 80% delle risorse disponibili sul nodo. Questa percentuale è configurabile e può essere modificata nel manifesto del cluster.

Di seguito è fornito un esempio di come impostare Service Fabric per usare il 50% della CPU disponibile e il 70% della memoria disponibile:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Per la maggior parte dei clienti e degli scenari, il rilevamento automatico delle capacità dei nodi per CPU e memoria è la configurazione consigliata (il rilevamento automatico è attivato per impostazione predefinita). Tuttavia, se è necessaria un'installazione manuale completa delle capacità del nodo, è possibile configurarle per ogni tipo di nodo usando il meccanismo per la descrizione dei nodi nel cluster. Di seguito è riportato un esempio di come configurare il tipo di nodo con quattro core e 2 GB di memoria:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Se è abilitato il rilevamento automatico delle risorse disponibili e le capacità del nodo sono definite manualmente nel manifesto del cluster, Service Fabric controlla che il nodo abbia risorse sufficienti per soddisfare la capacità definita dall'utente:

* Se le capacità del nodo definite nel manifesto sono inferiori o uguali alle risorse disponibili nel nodo, Service Fabric usa le capacità specificate nel manifesto.

* Se le capacità del nodo definite nel manifesto sono superiori alle risorse disponibili, Service Fabric usa le risorse disponibili come capacità del nodo.

Se non è necessario, è possibile disattivare il rilevamento automatico delle risorse disponibili. Per disattivarlo, modificare l'impostazione seguente:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Per ottenere prestazioni ottimali, nel manifesto del cluster è necessario attivare anche l'impostazione seguente:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> A partire da Service Fabric versione 7,0, è stata aggiornata la regola relativa alla modalità di calcolo delle capacità delle risorse del nodo nei casi in cui l'utente fornisca manualmente i valori per le capacità delle risorse del nodo. Si prenda in considerazione lo scenario seguente:
>
> * Sul nodo sono presenti un totale di 10 core CPU
> * SF è configurato per l'uso del 80% delle risorse totali per i servizi utente (impostazione predefinita), che lascia un buffer del 20% per gli altri servizi in esecuzione nel nodo (incl. Service Fabric servizi di sistema)
> * L'utente decide di eseguire manualmente l'override della capacità della risorsa del nodo per la metrica core CPU e di impostarla su 5 Core
>
> È stata modificata la regola per il modo in cui la capacità disponibile per Service Fabric Servizi utente viene calcolata nel modo seguente:
>
> * Prima di Service Fabric 7,0, la capacità disponibile per i servizi utente verrebbe calcolata su **5 Core** (il buffer di capacità del 20% verrà ignorato)
> * A partire da Service Fabric 7,0, la capacità disponibile per i servizi utente verrebbe calcolata su **4 core** (il buffer di capacità del 20% non verrà ignorato)

## <a name="specify-resource-governance"></a>Specificare la governance delle risorse

Le richieste e i limiti di governance delle risorse vengono specificati nel manifesto dell'applicazione (sezione ServiceManifestImport). Di seguito vengono forniti alcuni esempi:

**Esempio 1: specifica RequestsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

In questo esempio, l' `CpuCores` attributo viene usato per specificare una richiesta di 1 core CPU per **ServicePackageA**. Poiché il limite di CPU ( `CpuCoresLimit` attributo) non è specificato, Service Fabric usa anche il valore di richiesta specificato di 1 core come limite della CPU per il pacchetto del servizio.

**ServicePackageA** verrà inserito solo in un nodo in cui la capacità di CPU rimanente dopo la sottrazione della **somma delle richieste di CPU di tutti i pacchetti del servizio posizionati su tale nodo** è maggiore o uguale a 1 core. Nel nodo, il pacchetto del servizio sarà limitato a un core. Il pacchetto del servizio contiene due pacchetti di codice (**CodeA1** e **CodeA2**) ed entrambi specificano l' `CpuShares` attributo. La proporzione di CpuShares 512:256 viene usata per calcolare i limiti della CPU per i singoli pacchetti di codice. CodeA1 sarà quindi limitato a due terzi di un core e CodeA2 sarà limitato a un terzo di un core. Se CpuShares non è specificato per tutti i pacchetti di codice, Service Fabric divide equamente il limite della CPU.

Mentre CpuShares specificati per i pacchetti di codice rappresentano la percentuale relativa del limite complessivo della CPU del pacchetto del servizio, i valori di memoria per i pacchetti di codice sono specificati in termini assoluti. In questo esempio, l' `MemoryInMB` attributo viene usato per specificare le richieste di memoria di 1024 MB sia per CodeA1 che per CodeA2. Poiché il limite di memoria ( `MemoryInMBLimit` attributo) non è specificato, Service Fabric usa anche i valori della richiesta specificati come limiti per i pacchetti di codice. La richiesta di memoria (e il limite) per il pacchetto del servizio viene calcolata come somma dei valori di richiesta di memoria (e limite) dei pacchetti di codice costitutivi. Per **ServicePackageA**, quindi, la richiesta di memoria e il limite vengono calcolati come 2048 MB.

**ServicePackageA** verrà inserito solo in un nodo in cui la capacità di memoria rimanente dopo la sottrazione della **somma delle richieste di memoria di tutti i pacchetti del servizio posizionati su tale nodo** è maggiore o uguale a 2048 MB. Nel nodo entrambi i pacchetti di codice saranno limitati a 1024 MB di memoria. I pacchetti di codice (contenitori o processi) non saranno in grado di allocare una quantità di memoria superiore a tale limite e il tentativo di eseguire questa operazione comporterà eccezioni di memoria insufficiente.

**Esempio 2: specifica LimitsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
Questo esempio usa `CpuCoresLimit` `MemoryInMBLimit` gli attributi e, che sono disponibili solo nelle versioni SF 7,2 e successive. L'attributo CpuCoresLimit viene usato per specificare un limite di CPU di 1 core per **ServicePackageA**. Poiché la richiesta di CPU ( `CpuCores` attributo) non è specificata, viene considerata come 0. `MemoryInMBLimit` l'attributo viene usato per specificare i limiti di memoria di 1024 MB per CodeA1 e CodeA2 e, poiché le richieste ( `MemoryInMB` attributo) non vengono specificate, vengono considerate come 0. La richiesta e il limite di memoria per **ServicePackageA** vengono quindi calcolati rispettivamente come 0 e 2048. Poiché le richieste di CPU e di memoria per **ServicePackageA** sono pari a 0, non viene visualizzato alcun carico da considerare per il posizionamento, per le `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metriche e. Pertanto, dal punto di vista della governance delle risorse, **ServicePackageA** può essere inserito in qualsiasi nodo **indipendentemente dalla capacità rimanente**. Analogamente all'esempio 1, nel nodo CodeA1 sarà limitato a due terzi di un core e 1024 MB di memoria e CodeA2 sarà limitato a un terzo di un core e 1024 MB di memoria.

**Esempio 3: specifica RequestsAndLimits**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
Basandosi sui primi due esempi, in questo esempio viene illustrato come specificare sia le richieste che i limiti per CPU e memoria. **ServicePackageA** presenta rispettivamente richieste di CPU e memoria di 1 core e 3072 (1024 + 2048) MB. Può essere posizionata solo in un nodo con almeno 1 Core (e 3072 MB) di capacità rimanente dopo la sottrazione della somma di tutte le richieste di CPU e memoria di tutti i pacchetti del servizio posizionati sul nodo dalla capacità totale di CPU (e memoria) del nodo. Nel nodo CodeA1 sarà limitato a due terzi di 2 core e 3072 MB di memoria, mentre CodeA2 sarà limitato a un terzo di 2 core e 4096 MB di memoria.

### <a name="using-application-parameters"></a>Uso dei parametri di applicazione

Quando si specificano le impostazioni di governance delle risorse, è possibile usare i [parametri dell'applicazione](service-fabric-manage-multiple-environment-app-configuration.md) per gestire più configurazioni di app. L'esempio seguente illustra l'uso dei parametri di applicazione:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

In questo esempio sono impostati valori di parametro predefiniti per l'ambiente di produzione, dove ogni pacchetto del servizio ottiene 4 core e 2 GB di memoria. È possibile modificare i valori predefiniti con i file di parametri di applicazione. Nell'esempio seguente un solo file di parametri può essere usato per il test dell'applicazione in locale, dove ottiene una minore quantità di risorse rispetto all'ambiente di produzione:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> È possibile usare i parametri di applicazione per definire la governance delle risorse a partire dalla versione 6.1 di Service Fabric.<br>
>
> Se per definire la governance delle risorse si usano parametri di applicazione, non è possibile eseguire il downgrade di Service Fabric a una versione precedente la 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Applicazione dei limiti delle risorse per i servizi utente

Mentre l'applicazione della governance delle risorse ai servizi Service Fabric garantisce che i servizi gestiti dalle risorse non possano superare la quota di risorse, molti utenti dovranno ancora eseguire alcuni dei servizi Service Fabric in modalità non governata. Quando si usano servizi di Service Fabric non governati, è possibile che si verifichino situazioni in cui i servizi non gestiti "Runaway" utilizzano tutte le risorse disponibili nei nodi del Service Fabric, che possono causare gravi problemi, ad esempio:

* Inedia delle risorse di altri servizi in esecuzione nei nodi (inclusi Service Fabric servizi di sistema)
* Nodi che terminano in uno stato non integro
* API di gestione cluster Service Fabric non risponde

Per evitare che si verifichino queste situazioni, Service Fabric consente di *imporre i limiti delle risorse per tutti i servizi utente Service fabric in esecuzione nel nodo* , sia governati che non governati, per garantire che i servizi utente non usino mai più della quantità specificata di risorse. Questa operazione viene eseguita impostando il valore per la configurazione EnforceUserServiceMetricCapacities nella sezione PlacementAndLoadBalancing di ClusterManifest su true. Questa impostazione è disattivata per impostazione predefinita.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Osservazioni aggiuntive:

* L'imposizione del limite di risorse si applica solo alle `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metriche delle risorse e
* L'imposizione del limite di risorse funziona solo se le capacità del nodo per le metriche delle risorse sono disponibili per Service Fabric, tramite il meccanismo di rilevamento automatico o tramite gli utenti che specificano manualmente le capacità dei nodi, come illustrato nella sezione [configurazione del cluster per l'abilitazione della governance delle risorse](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) .Se le capacità del nodo non sono configurate, non è possibile usare la funzionalità di imposizione del limite di risorse perché Service Fabric non è in grado di conoscere la quantità di risorse da riservare per i servizi utente.Service Fabric genererà un avviso di integrità se "EnforceUserServiceMetricCapacities" è true, ma le capacità del nodo non sono configurate.

## <a name="other-resources-for-containers"></a>Altre risorse per i contenitori

Oltre alla CPU e alla memoria, è possibile specificare i limiti di altre risorse per i contenitori. Questi limiti sono specificati a livello di pacchetto di codice e vengono applicati quando il contenitore viene avviato. A differenza di CPU e memoria, Cluster Resource Manager non riconosce queste risorse e non esegue verifiche della capacità o il bilanciamento del carico.

* *MemorySwapInMB*: quantità di memoria di scambio che un contenitore può usare.
* *MemoryReservationInMB*: limite flessibile per la governance della memoria che viene applicato solo quando si verificano problemi di contesa della memoria sul nodo.
* *CpuPercent*: percentuale di CPU che il contenitore può usare. Se per il pacchetto del servizio vengono specificate le richieste o i limiti della CPU, questo parametro viene ignorato.
* *MaximumIOps*: numero massimo di IOPS che un contenitore può usare (lettura e scrittura).
* *MaximumIOBytesps*: I/O massimo (byte al secondo) che un contenitore può usare (lettura e scrittura).
* *BlockIOWeight*: peso di I/O del blocco rispetto agli altri contenitori.

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

* Per altre informazioni su Cluster Resource Manager, vedere [Introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Per altre informazioni sul modello dell'applicazione, i pacchetti servizio e i pacchetti di codice, nonché sul mapping delle repliche a tali elementi, vedere [Modellare un'applicazione in Service Fabric][application-model-link].

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
