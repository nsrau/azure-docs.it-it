---
title: Descrizione del cluster di Resource Balancer | Documentazione Microsoft
description: "Descrizione di un cluster di Service Fabric specificando i domini di errore, i domini di aggiornamento, le proprietà del nodo e le capacità del nodo per Cluster Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: cfc38cecfaf0a0bdaae043fc35dcfed743459823
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="describing-a-service-fabric-cluster"></a>Descrizione di un cluster di Service Fabric
Cluster Resource Manager di Service Fabric fornisce alcuni meccanismi per descrivere un cluster. Durante la fase di esecuzione, Cluster Resource Manager usa queste informazioni per assicurare la disponibilità elevata dei servizi in esecuzione sul cluster. Applicando queste regole importanti, tenta anche di ottimizzare il consumo di risorse del cluster.

## <a name="key-concepts"></a>Concetti chiave
Cluster Resource Manager supporta diverse funzionalità che descrivono un cluster:

* Domini di errore
* Domini di aggiornamento
* Proprietà del nodo
* Capacità del nodo

## <a name="fault-domains"></a>Domini di errore
Un dominio di errore è un'area di errore coordinato. Un singolo computer è un dominio di errore, in quanto il computer si può arrestare da solo per vari motivi, da interruzioni dell'alimentazione a errori delle unità o firmware NIC non valido. I computer connessi allo stesso commutatore Ethernet si trovano nello stesso dominio di errore, analogamente ai computer che condividono una singola fonte di alimentazione. Poiché è naturale che gli errori hardware si sovrappongano, i domini di errore sono intrinsecamente gerarchici e sono rappresentati come URI in Service Fabric.

Quando si configura un proprio cluster, è necessario valutare tali diverse aree di errore. È importante che i domini di errore siano impostati correttamente poiché Service Fabric usa queste informazioni per posizionare in modo sicuro i servizi. Service Fabric non desidera posizionare i servizi in modo che la perdita di un dominio di errore (a causa dell'errore di qualche componente) causi l'interruzione dei servizi stessi. Service Fabric, nell'ambiente di Azure, usa le informazioni del dominio di errore fornite dall'ambiente per configurare in modo corretto i nodi nel cluster per conto dell'utente.

Nella figura seguente tutte le entità che contribuiscono ai domini di errore sono colorate e sono elencati tutti i diversi domini di errore risultanti. In questo esempio sono presenti data center ("DC"), rack ("R") e pannelli ("B"). Se ogni pannello include più macchine virtuali, è possibile che la gerarchia del dominio di errore includa un altro livello.

<center>
![Nodi organizzati tramite domini di errore][Image1]
</center>

Durante la fase di esecuzione Cluster Resource Manager di Service Fabric prende in considerazione i domini di errore del cluster e pianifica un layout.  Tenta di distribuire le repliche con stato o le istanze senza stato per un determinato servizio in modo che si trovino in domini di errore diversi. Questo processo assicura che, in caso di problemi in un dominio di errore a qualsiasi livello della gerarchia, la disponibilità del servizio non verrà compromessa.

Cluster Resource Manager di Service Fabric non si cura del numero di livelli presenti nella gerarchia del dominio di errore. Tenta invece di garantire che la perdita di una parte della gerarchia non inciderà sui servizi in esecuzione sopra essa. Per questo motivo è preferibile che sia presente lo stesso numero di nodi a ogni livello di profondità della gerarchia del dominio di errore. Mantenendo i livelli bilanciati si impedisce che una parte della gerarchia contenga più servizi di altre. Facendo altrimenti si contribuisce a creare sbilanciatamenti durante il caricamento dei singoli nodi, rendendo l'errore di determinati domini più critico rispetto ad altri.

Se la "struttura" dei domini di errore è sbilanciata nel cluster, è più difficile per Cluster Resource Manager determinare la migliore allocazione dei servizi. Layout sbilanciati dei domini di errore comportano che la perdita di un determinato dominio può incidere maggiormente sulla disponibilità del cluster rispetto ad altri. Di conseguenza Cluster Resource Manager si trova a dover scegliere fra due obiettivi: usare i computer del dominio più dotato posizionando i servizi in esso o posizionare i servizi in modo che la perdita di un dominio non causi problemi. Come si presenta questa situazione?

<center>
![Due layout diversi per i cluster][Image2]
</center>

Nel diagramma precedente sono illustrati due diversi layout di esempio per un cluster. Nel primo esempio i nodi sono distribuiti uniformemente tra i domini di errore. Nell'altro un dominio di un errore ha molti più nodi. Se si crea un cluster personalizzato in locale o in un altro ambiente, è necessario prendere in considerazione questo problema.

In Azure la scelta di quale dominio di errore deve contenere un nodo viene gestita automaticamente. Tuttavia, a seconda del numero di nodi di cui si esegue il provisioning, si possono comunque ottenere domini di errore con più nodi di altri. Si immagini ad esempio di avere cinque domini di errore e di eseguire il provisioning di sette nodi per un determinato NodeType. In questo caso i primi due domini di errore ottengono più nodi. Se si continua a distribuire altri NodeType con solo un paio di istanze, il problema peggiora.

## <a name="upgrade-domains"></a>Domini di aggiornamento
I domini di aggiornamento sono un'altra funzionalità che aiuta Cluster Resource Manager di Service Fabric a comprendere il layout del cluster, in modo da pianificare una strategia per la gestione degli errori. I domini di aggiornamento definiscono set di nodi che vengono aggiornati contemporaneamente.

I domini di aggiornamento sono molto simili ai domini di errore, ma con un paio di differenze essenziali. Innanzitutto, mentre i domini di errore sono rigorosamente definiti dalle aree di errori hardware coordinati, i domini di aggiornamento sono definiti dai criteri. Con i domini di aggiornamento è possibile decidere quanti se ne vogliono, in quanto la decisione non viene presa dall'ambiente. Un'altra differenza consiste nel fatto che, almeno attualmente, i domini di aggiornamento non sono gerarchici. Sono molto più simili a un semplice tag.

Il diagramma seguente mostra tre domini di aggiornamento distribuiti su tre domini di errore. Illustra anche una sola posizione possibile per tre repliche diverse di un servizio con stato, dove ciascuna finisce in diversi domini di errore e di aggiornamento. Questo posizionamento ci consente di perdere un dominio di errore mentre è in corso un aggiornamento del servizio, mantenendo comunque una copia del codice e dei dati.

<center>
![Posizionamento con domini di errore e di aggiornamento][Image3]
</center>

L'uso di un numero elevato di domini di aggiornamento presenta vantaggi e svantaggi. Con molti domini di aggiornamento, ogni passaggio dell'aggiornamento è più dettagliato e quindi influisce su un numero minore di nodi o servizi. Questo si traduce nella necessità di spostare meno servizi contemporaneamente nonché in una minore varianza del sistema. Anche l'affidabilità tende a migliorare, poiché una parte minore del servizio è interessata da eventuali problemi introdotto durante l'aggiornamento. La presenza di più domini di aggiornamento significa anche che è necessario un minore overhead in altri nodi per gestire l'impatto dell'aggiornamento. Ad esempio, se si dispone di cinque domini di aggiornamento, i nodi di ciascuno gestiscono circa il 20% del traffico. Se è necessario bloccare il dominio di aggiornamento per un aggiornamento, il suo carico deve essere trasferito altrove. La presenza di più domini di aggiornamento comporta meno overhead da mantenere in altri nodi del cluster.

Lo svantaggio di avere molti domini di aggiornamento è che gli aggiornamenti tendono a richiedere più tempo. Service Fabric attende un breve periodo di tempo dopo il completamento di un dominio di aggiornamento prima di procedere. Questo ritardo è tale che i problemi introdotti dall'aggiornamento hanno la possibilità di presentarsi ed essere individuati. Questo compromesso è accettabile perché evita che modifiche non valide abbiano un impatto eccessivo sul servizio in un determinato momento.

Anche l'uso di un numero troppo ridotto di domini di aggiornamento ha conseguenze specifiche: mentre ogni singolo dominio di aggiornamento è inattivo e in fase di aggiornamento, una parte elevata della capacità complessiva non risulta disponibile. Ad esempio, se sono presenti solo tre domini di aggiornamento, 1/3 circa della capacità complessiva del servizio o del cluster non sarà disponibile in un determinato momento. Avere una parte così grande del servizio inattiva contemporaneamente non è auspicabile poiché è necessario disporre di capacità sufficiente nel resto del cluster per gestire il carico di lavoro. Mantenere un tale buffer significa che normalmente quei nodi sono meno caricati di quanto dovrebbero, aumentando il costo dell'esecuzione del servizio.

Non è previsto alcun limite effettivo per il numero totale di domini di errore o di aggiornamento in un ambiente e non sono previsti vincoli per le relative sovrapposizioni. Le strutture comuni che abbiamo visto sono:

* Corrispondenza 1:1 fra domini di errore e domini di aggiornamento
* Un dominio di aggiornamento per nodo (istanza del sistema operativo fisico o virtuale)
* Un modello con "striping" o a "matrice" in cui i domini di errore e i domini di aggiornamento formano una matrice e i computer sono disposti lungo le diagonali

<center>
![Layout dei domini di errore e di aggiornamento][Image4]
</center>

Non esiste un layout ottimale, ogni layout presenta vantaggi e svantaggi. Ad esempio, il modello di tipo 1FD:1UD è abbastanza semplice da configurare. Il modello con 1 UD per nodo è probabilmente quello che veniva usato più di frequente in passato per la gestione di insiemi ridotti di computer, in cui ogni computer viene disattivato in modo indipendente.

Il modello più comune, e quello utilizzato in Azure, è basato sulla matrice FD/UD, in cui i domini di errore e i domini di aggiornamento formano una tabella e i nodi vengono posizionati a partire dalla diagonale. Se questa configurazione si presenta come sparsa o compressa dipende dal numero totale di nodi rispetto al numero di domini di errore e domini di aggiornamento. In altre parole, quando i cluster sono sufficientemente grandi, si ha quasi sempre un modello a matrice denso, mostrato nel riquadro inferiore destro della figura sopra.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Vincoli del dominio di errore e di aggiornamento e comportamento risultante
Cluster Resource Manager considera come un vincolo il desiderio di mantenere un servizio bilanciato tra domini di errore e di aggiornamento. È possibile trovare altre informazioni sui vincoli [in questo articolo](service-fabric-cluster-resource-manager-management-integration.md). I vincoli di dominio di errore e di aggiornamento stabiliscono: "Per una data partizione di servizio non deve esistere una differenza *maggiore di uno* nel numero di oggetti servizio (istanze di servizio senza stato o repliche con stato) tra due domini".  Ciò significa che per un determinato servizio alcuni spostamenti o disposizioni non saranno validi in quanto violano i vincoli del dominio di errore o di aggiornamento.

Esaminiamo un esempio. Si supponga di avere un cluster con sei nodi, configurato con cinque domini di errore e cinque domini di aggiornamento.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Ora si supponga di creare un servizio con un valore TargetReplicaSetSize pari a cinque. Le repliche avvengono in N1 N5. N6 non sarà mai utilizzato indipendentemente dal numero di servizi creati. Ma perché? Esaminiamo la differenza tra il layout corrente e ciò che accadrebbe se si scegliesse N6.

Ecco il layout ottenuto e il numero totale di repliche per ogni dominio di errore e di aggiornamento:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Questo layout è equilibrato in termini di nodi per dominio di errore e dominio di aggiornamento. È anche bilanciato in termini di numero di repliche per dominio di errore e dominio di aggiornamento. Ogni dominio ha lo stesso numero di nodi e lo stesso numero di repliche.

A questo punto vediamo cosa accadrebbe se usassimo N6 invece di N2. Come sarebbero state distribuite le repliche?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Cosa si può notare? Questo layout viola la definizione del vincolo di dominio di errore. FD0 ha due repliche, mentre FD1 ne ha zero, perciò la differenza tra FD0 e FD1 è due. Cluster Resource Manager non consente questa disposizione. Allo stesso modo, se si scegliesse N2 e N6 (anziché N1 e N2) si otterrebbe:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Mentre questo layout è bilanciato in termini di domini di errore, ora viola il vincolo del dominio di aggiornamento (poiché UD0 ha zero repliche mentre UD1 ne ha due). Anche questo layout non è valido

## <a name="configuring-fault-and-upgrade-domains"></a>Configurazione dei domini di errore e di aggiornamento
La definizione dei domini di errore e dei domini di aggiornamento viene eseguita automaticamente nelle distribuzioni di Service Fabric ospitate in Azure. Service Fabric recupera semplicemente le informazioni sull'ambiente da Azure e le usa.

Se si sta creando il proprio cluster (o si desidera seguire una particolare topologia), specificare le informazioni di dominio di errore e dominio di aggiornamento manualmente. In questo esempio definiamo un cluster di sviluppo locale con nove nodi che si estende su tre "data center" (ognuno con tre rack). Il cluster ha anche tre domini di aggiornamento distribuiti sui tre data center. Nel modello del manifesto del cluster questa configurazione ha un aspetto analogo al seguente:

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

mediante ClusterConfig.json per le distribuzioni autonome

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Nelle distribuzioni di Azure i domini di errore e quelli di aggiornamento vengono assegnati da Azure. Di conseguenza la definizione dei nodi e dei ruoli all'interno dell'opzione di infrastruttura di Azure non include informazioni sui domini di errore o di aggiornamento.
>
>

## <a name="placement-constraints-and-node-properties"></a>Vincoli di posizionamento e proprietà dei nodi
In alcuni casi, essenzialmente nella maggior parte dei casi, si vuole assicurare che determinati carichi di lavoro vengano eseguiti solo su determinati nodi o set di nodi nel cluster. Ad esempio, è possibile che alcuni carichi di lavoro richiedano GPU o SSD, mentre altri no. Un ottimo esempio di uso dell'hardware per carichi di lavoro specifici è dato da quasi tutte le architettura a più livelli. In queste architetture alcuni computer servono come lato front-end/interfaccia dell'applicazione (e pertanto probabilmente sono esposti a Internet). Altri gruppi di computer (spesso con risorse hardware diverse) gestiscono il lavoro dei livelli di calcolo o archiviazione (e in genere non sono esposti a Internet). Service Fabric si aspetta che anche in uno scenario con microservizi si verifichino situazioni in cui determinati carichi di lavoro dovranno essere eseguiti in configurazioni hardware specifiche, ad esempio:

* Un'applicazione esistente con n livelli è stata "elevata e spostata" in un ambiente Service Fabric.
* Un carico di lavoro deve essere eseguito su hardware specifico per finalità di prestazioni, scalabilità o isolamento di sicurezza.
* Un carico di lavoro deve essere isolato da altri carichi di lavoro per motivi relativi ai criteri o all'uso delle risorse

Per supportare questi tipi di configurazione, Service Fabric dispone di tag estremamente efficienti applicabili ai nodi. Questi sono denominati vincoli di posizionamento. I vincoli di posizionamento possono essere usati per indicare dove occorre eseguire determinati servizi. Il set di vincoli è estendibile: si può usare qualsiasi coppia chiave/valore.

<center>
![Layout di cluster con carichi di lavoro diversi][Image5]
</center>

I diversi tag chiave/valore sui nodi sono chiamati *proprietà* di posizionamento del nodo (o semplicemente proprietà dei nodi). Il valore specificato nella proprietà del nodo può essere una stringa, un valore booleano o un valore lungo firmato. L'istruzione a livello di servizio è chiamata *vincolo* di posizionamento in quanto vincola la posizione in cui il servizio può essere eseguito nel cluster. Il vincolo può essere qualsiasi istruzione booleana che opera sulle diverse proprietà dei nodi nel cluster. I selettori validi in queste istruzioni booleane sono:

1) controlli condizionali per la creazione di istruzioni specifiche

| Istruzione | Sintassi |
| --- |:---:|
| "uguale a" | "==" |
| "diverso da" | "!=" |
| "maggiore di" | ">" |
| "maggiore o uguale a" | ">=" |
| "minore di" | "<" |
| "minore o uguale a" | "<=" |

2) istruzioni booleane per il raggruppamento e le operazioni logiche

| Istruzione | Sintassi |
| --- |:---:|
| "and" | "&&" |
| "or" | "&#124;&#124;" |
| "not" | "!" |
| "raggruppamento come singola istruzione" | "()" |

Di seguito sono riportati alcuni esempi di semplici istruzioni di vincolo.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Solo sui nodi in cui l'istruzione generale restituisce "True" è possibile posizionare il servizio. I nodi per i quali sono state definite proprietà non corrispondono ad alcun vincolo di posizionamento che contiene proprietà.

Service Fabric definisce alcune proprietà predefinite dei nodi che possono essere successivamente usate automaticamente, senza alcun intervento da parte dell'utente. Al momento della stesura di questo articolo, le proprietà predefinite specificate a livello di ogni nodo sono **NodeType** e **NodeName**. Ad esempio è possibile scrivere un vincolo di posizionamento come `"(NodeType == NodeType03)"`. NodeType è una delle proprietà più usate. È utile perché corrisponde in modalità 1:1 con un tipo di computer, che a sua volta corrisponde a un tipo di carico di lavoro in un'architettura applicativa tradizionale a più livelli.

<center>
![Vincoli di posizionamento e proprietà dei nodi][Image6]
</center>

Si supponga che siano state definite le proprietà seguenti per un tipo di nodo specifico:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per cluster ospitati in Azure. Nel modello di Azure Resource Manager per un cluster, elementi come il nome del tipo di nodo sono probabilmente parametrizzate e hanno un aspetto simile a "[parameters('vmNodeType1Name')]" anziché a "NodeType01".

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

È possibile creare *vincoli* di posizionamento del servizio per un servizio in modo analogo al seguente:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se si è certi che tutti i nodi di NodeType01 siano validi, si può anche scegliere quel tipo di nodo.

Uno dei vantaggi dei vincoli di posizionamento di un servizio consiste nel fatto che possono essere aggiornati in modo dinamico durante la fase di esecuzione. Se necessario, è quindi possibile spostare un servizio nel cluster, aggiungere e rimuovere requisiti e così via. Service Fabric assicura che il servizio sia sempre attivo e disponibile, anche durante l'applicazione di questi tipi di modifiche.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

I vincoli di posizionamento, oltre a molti altri controlli di orchestrazione che verranno illustrati, vengono specificati per ogni istanza diversa del servizio. Gli aggiornamenti sostituiscono (sovrascrivono) sempre quanto specificato in precedenza.

Le proprietà in un nodo vengono definite tramite la definizione del cluster e quindi non possono essere aggiornate senza aggiornare il cluster. L'aggiornamento delle proprietà di un nodo richiede che ogni nodo interessato venga disattivato e poi riattivato.

## <a name="capacity"></a>Capacità
Uno dei processi più importanti di un agente di orchestrazione consiste nel semplificare la gestione dell'utilizzo delle risorse del cluster. L'ultima cosa che si vuole se si sta tentando di eseguire i servizi in modo efficiente è avere un gruppo di nodi soggetti a uso frequente e altri poco usati. I nodi più usati si contendono le risorse offrendo prestazioni scarse mentre i nodi poco usati sono uno spreco di risorse e costi. Prima di parlare di bilanciamento del carico, non sarebbe il caso di assicurarsi in primo luogo che i nodi non esauriscano le risorse?

Service Fabric rappresenta le risorse come `Metrics`. Per metrica si intende qualsiasi risorsa logica o fisica che deve essere descritta per Service Fabric, ad esempio "WorkQueueDepth" o "MemoryInMb". Per informazioni sulla configurazione delle metriche e il loro utilizzo, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)

Le metriche sono diverse dai vincoli di posizionamento e dalle proprietà del nodo. Le proprietà del nodo sono descrittori statici dei nodi stessi, mentre le metriche sono relative alle risorse del nodo usate dai servizi durante l'esecuzione in un nodo. Una proprietà del nodo potrebbe essere "HasSSD" e potrebbe essere impostata su true o false. La quantità di spazio disponibile su tale unità SSD (e utilizzata dai servizi) sarebbe una metrica come "DriveSpaceInMb". La capacità del nodo per "DriveSpaceInMb" sarebbe impostata sulla quantità di spazio totale non riservato nell'unità. I servizi segnalerebbe la quantità della metrica utilizzata in fase di esecuzione.

È importante notare che, come per i vincoli di posizionamento e le proprietà del nodo, Cluster Resource Manager di Service Fabric non sa cosa significano i nomi delle metriche. I nomi delle metriche sono semplicemente stringhe. È consigliabile dichiarare l'unità come parte del nome della metrica creato quando potrebbe essere ambigua.

Se si disattivasse completamente il *bilanciamento*di tutte le risorse, Cluster Resource Manager di Service Fabric tenterebbe comunque di assicurare che nessun nodo superi la rispettiva capacità. In genere questo è possibile, a meno che il cluster non sia complessivamente troppo pieno. La capacità è un altro *vincolo* usato da Cluster Resource Manager per conoscere la quantità di una risorsa posseduta da un nodo. La capacità rimanente verrà registrata anche per il cluster nel suo complesso. Sia la capacità che l'utilizzo a livello di servizio sono espressi in termini di metriche. Ad esempio la metrica potrebbe essere "MemoryInMb" e un determinato nodo potrebbe avere una capacità "MemoryInMb " di 2048. Alcuni servizi in esecuzione su tale nodo possono dichiarare di consumare attualmente 64 di "MemoryInMb".

Durante la fase di esecuzione, Cluster Resource Manager verifica la quantità di ogni risorsa presente in ciascun nodo e quanta ne riamane. Per farlo sottrae dalla capacità del nodo l'uso dichiarato da ogni servizio eseguito su quel nodo. Queste informazioni consentono a Cluster Resource Manager di Service Fabric di individuare la posizione ottimale per l'inserimento o lo spostamento di repliche, in modo che i nodi non superino le rispettive capacità.

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```
<center>
![Nodi e capacità del cluster][Image7]
</center>

È possibile vedere le capacità definite nel manifesto del cluster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="DiskInMb" Value="512000"/>
      </Capacities>
    </NodeType>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per cluster ospitati in Azure. Nel modello di Azure Resource Manager per un cluster, elementi come il nome del tipo di nodo sono probabilmente parametrizzati e hanno un aspetto simile a "[parameters('vmNodeType2Name')]" anziché a "NodeType02".

```json
"nodeTypes": [
    {
        "name": "NodeType02",
        "capacities": {
            "MemoryInMb": "2048",
            "DiskInMb": "512000"
        }
    }
],
```

È anche possibile, e comune, che il carico di un servizio cambi in modo dinamico. Si supponga che il carico di una replica sia stato modificato da 64 a 1024, ma il nodo in cui era in esecuzione in quel momento avesse solo una disponibilità residua di 512 (della metrica "Memoryinmb"). Ora il posizionamento della replica o dell'istanza non è valido, poiché in quel nodo non c'è spazio sufficiente. Ciò può verificarsi anche se l'utilizzo combinato delle repliche e delle istanze sul nodo supera la capacità del nodo. In entrambi i casi Cluster Resource Manager deve intervenire e riportare il nodo sotto il valore di capacità massimo. Lo fa spostando su altri nodi una o più repliche o istanze presenti nel nodo. Quando sposta le repliche, Cluster Resource Manager tenta di ridurre al minimo il costo di tali spostamenti. Il costo dello spostamento è trattato in [questo articolo](service-fabric-cluster-resource-manager-movement-cost.md).

## <a name="cluster-capacity"></a>Capacità del cluster
Se si vuole impedire che complessivamente il cluster sia troppo pieno, Con il carico dinamico Cluster Resource Manager non può fare molto. I servizi possono subire picchi di carico indipendentemente dalle azioni eseguite da Cluster Resource Manager. Di conseguenza un cluster che dispone di molta capacità oggi può non essere abbastanza potente domani se l'utilizzo aumenta. Detto ciò, ci sono alcuni controlli integrati per evitare problemi di base. È prima di tutto possibile impedire la creazione di nuovi carichi di lavoro che riempirebbero il cluster.

Si supponga di creare un servizio senza stato con alcuni carichi associati. La creazione di report sul carico predefinito e dinamico verrà illustrata in seguito. Si immagini che il servizio monitori la metrica "DiskSpaceInMb". Si supponga anche che consumerà cinque unità di "DiskSpaceInMb" per ogni istanza del servizio. Si vogliono creare tre istanze del servizio. L'installazione è riuscita. È quindi necessario che 15 unità di "DiskSpaceInMb" siano presenti nel cluster per consentire la mera creazione di queste istanze del servizio. Cluster Resource Manager calcola costantemente la capacità e l'utilizzo globale di ogni metrica, perciò può determinare facilmente se c'è spazio sufficiente nel cluster. Se non c'è spazio sufficiente, Cluster Resource Manager rifiuta la chiamata di creazione del servizio.

Poiché l'unico requisito è che ci siano 15 unità disponibili, questo spazio può essere allocato in molti modi diversi. Ad esempio potrebbe esserci un'unità di capacità rimanente in 15 nodi diversi oppure tre unità di capacità rimanenti in cinque nodi diversi. Se Cluster Resource Manager è in grado di sistemare le cose in modo che ci siano tre unità disponibili in tre nodi, alla fine posizionerà il servizio. Questa riorganizzazione è quasi sempre possibile, a meno che il cluster nel complesso non sia quasi pieno, i servizi non siano tutti molto "ingombranti" o entrambe le cose.

## <a name="buffered-capacity"></a>Capacità in buffering
Un'altra funzione di Cluster Resource Manager che può semplificare la gestione della capacità complessiva del cluster è il concetto di buffer riservato per la capacità specificata in ogni nodo. Il buffer di capacità consente di riservare una parte della capacità globale dei nodi e usarla solo per posizionare i servizi durante gli aggiornamenti e gli errori dei nodi. Attualmente il buffer viene specificato a livello globale per ogni metrica per tutti i nodi tramite la definizione del cluster. Il valore scelto per la capacità riservata è una funzione del numero di domini di errore e di aggiornamento presenti nel cluster e della quantità di overhead desiderata. Quando è presente un numero maggiore di domini di errore e di aggiornamento è possibile scegliere un valore inferiore per il buffer di capacità. Se si dispone di molti domini, ci si può aspettare la mancata disponibilità di porzioni inferiori del cluster durante gli aggiornamenti e gli errori. Specificare la percentuale del buffer risulta utile solo se è stata specificata anche la capacità del nodo per una metrica.

Di seguito è riportato un esempio di come specificare la capacità di memorizzazione nel buffer:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "DiskSpace",
          "value": "0.10"
      },
      {
          "name": "Memory",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

La creazione di nuovi servizi non riesce quando il cluster esaurisce il buffer di capacità per una metrica. Ciò garantisce che il cluster mantenga un overhead di riserva sufficiente da evitare il superamento della capacità dei nodi a causa di aggiornamenti o errori. Il buffer di capacità è un'opzione facoltativa ma consigliata per tutti i cluster che definiscono una capacità per una metrica.

Cluster Resource Manager espone queste informazioni mediante PowerShell e le API Query. Ciò consente di vedere le impostazioni del buffer di capacità, la capacità totale e il consumo corrente per ogni metrica in uso nel cluster. Ecco un esempio dell'output:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sull'architettura e sul flusso di informazioni in Cluster Resource Manager, vedere [questo articolo](service-fabric-cluster-resource-manager-architecture.md)
* Definire la metrica di deframmentazione rappresenta un modo per consolidare il carico sui nodi anziché distribuirlo. Per informazioni su come configurare la deframmentazione, vedere [questo articolo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Partire dall'inizio e vedere l' [introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

