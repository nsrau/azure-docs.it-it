---
title: Descrizione del cluster di Cluster Resource Manager | Microsoft Docs
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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 369389f7f8ce56435dcbb9d9264c2db48a294d55
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---

# <a name="describing-a-service-fabric-cluster"></a>Descrizione di un cluster di Service Fabric
Cluster Resource Manager di Service Fabric fornisce alcuni meccanismi per descrivere un cluster. Durante la fase di esecuzione, Cluster Resource Manager usa queste informazioni per assicurare la disponibilità elevata dei servizi in esecuzione sul cluster. Applicando queste regole importanti, tenta anche di ottimizzare il consumo di risorse all'interno del cluster.

## <a name="key-concepts"></a>Concetti chiave
Cluster Resource Manager supporta diverse funzionalità che descrivono un cluster:

* Domini di errore
* Domini di aggiornamento
* Proprietà del nodo
* Capacità del nodo

## <a name="fault-domains"></a>Domini di errore
Un dominio di errore è un'area di errore coordinato. Un singolo computer è un dominio di errore, in quanto il computer si può arrestare da solo per vari motivi, da interruzioni dell'alimentazione a errori delle unità o firmware NIC non valido. I computer connessi allo stesso commutatore Ethernet si trovano nello stesso dominio di errore, analogamente ai computer che condividono una singola fonte di alimentazione o si trovano nella stessa posizione. Poiché è naturale che gli errori hardware si sovrappongano, i domini di errore sono intrinsecamente gerarchici e sono rappresentati come URI in Service Fabric.

È importante che i domini di errore siano impostati correttamente poiché Service Fabric usa queste informazioni per posizionare in modo sicuro i servizi. Service Fabric non posiziona i servizi in modo che la perdita di un dominio di errore (a causa dell'errore di qualche componente) causi l'interruzione di un servizio. Service Fabric, nell'ambiente di Azure, usa le informazioni del dominio di errore fornite dall'ambiente per configurare in modo corretto i nodi nel cluster per conto dell'utente. Nella versione autonoma di Service Fabric, i domini di errore vengono definiti al momento della configurazione del cluster. 

> [!WARNING]
> È importante che le informazioni sul dominio di errore fornite a Service Fabric siano accurate. Si supponga, ad esempio, che i nodi del cluster di Service Fabric siano in esecuzione all'interno di dieci macchine virtuali, a loro volta in esecuzione su cinque host fisici. In questo caso, anche se sono presenti dieci macchine virtuali, sono presenti solo cinque domini di errore diversi (livello superiore). La condivisione dello stesso host fisico comporta per le macchine virtuali anche la condivisione dello stesso dominio di errore radice, poiché sulle macchine virtuali si verifica un errore coordinato se sul relativo host fisico si verifica un errore,  
>
> poiché Service Fabric non prevede il cambiamento del dominio di errore di un nodo. Altri meccanismi per garantire una disponibilità elevata delle macchine virtuali, come illustrato nell'articolo relativo alle [Macchine virtuali a disponibilità elevata](https://technet.microsoft.com/en-us/library/cc967323.aspx), adottano la migrazione trasparente delle macchine virtuali da un host a un altro. Questi meccanismi non riconfigurano né notificano il codice in esecuzione all'interno della macchina virtuale. Di conseguenza, **non sono supportati** come ambienti per l'esecuzione dei cluster di Service Fabric. Service Fabric deve essere l'unica tecnologia di disponibilità elevata in uso. Non sono necessari meccanismi quali la migrazione in tempo reale della macchina virtuale, SAN, o altri. Se usati insieme a Service Fabric, questi meccanismi _riducono_ la disponibilità e l'affidabilità dell'applicazione, poiché introducono complessità aggiuntive, aggiungono origini centralizzate di errore e usano strategie di disponibilità e affidabilità che possono entrare in conflitto con quelle presenti in Service Fabric. 
>
>

Nella figura seguente tutte le entità che contribuiscono ai domini di errore sono colorate e sono elencati tutti i diversi domini di errore risultanti. In questo esempio sono presenti data center ("DC"), rack ("R") e pannelli ("B"). Se ogni pannello include più macchine virtuali, è possibile che la gerarchia del dominio di errore includa un altro livello.

<center>
![Nodi organizzati tramite domini di errore][Image1]
</center>

Durante la fase di esecuzione, Cluster Resource Manager di Service Fabric prende in considerazione i domini di errore del cluster e pianifica i layout. Vengono distribuite le repliche con stato o le istanze senza stato per un determinato servizio in modo che si trovino in domini di errore diversi. La distribuzione del servizio tra domini di errore assicura che la disponibilità del servizio non sia compromessa se si verifica un errore in un dominio di errore a qualsiasi livello della gerarchia.

Cluster Resource Manager di Service Fabric non si cura del numero di livelli presenti nella gerarchia del dominio di errore. Tenta invece di garantire che la perdita di una parte della gerarchia non incida sui servizi che vi vengono eseguiti. 

È preferibile che sia presente lo stesso numero di nodi a ogni livello di profondità della gerarchia del dominio di errore. Se l'albero dei domini di errore nel cluster è sbilanciato, è più difficile per Cluster Resource Manager determinare la migliore allocazione dei servizi. Layout sbilanciati dei domini di errore comportano che la perdita di alcuni domini possa incidere maggiormente sulla disponibilità del cluster rispetto ad altri. Di conseguenza Cluster Resource Manager si trova a dover scegliere fra due obiettivi: usare i computer del dominio più dotato posizionando i servizi in essi o posizionare i servizi in altri domini, così che la perdita di un dominio non causi problemi. 

Come appaiono i domini sbilanciati? La figura seguente illustra due diversi layout per un cluster. Nel primo esempio i nodi sono distribuiti uniformemente tra i domini di errore. Nel secondo esempio, un dominio di errore presenta molti più nodi degli altri. 

<center>
![Due layout diversi per i cluster][Image2]
</center>

In Azure la scelta di quale dominio di errore deve contenere un nodo viene gestita automaticamente. Tuttavia, a seconda del numero di nodi di cui si esegue il provisioning, si possono comunque ottenere domini di errore con più nodi di altri. Si immagini ad esempio di avere cinque domini di errore nel cluster e di eseguire il provisioning di sette nodi per un determinato NodeType. In questo caso i primi due domini di errore avranno più nodi. Se si continua a distribuire altri NodeType con solo un paio di istanze, il problema peggiora. Per questo motivo è consigliabile che il numero di nodi in ogni NodeType sia multiplo del numero di domini di errore.

## <a name="upgrade-domains"></a>Domini di aggiornamento
I domini di aggiornamento sono un'altra funzionalità che aiuta Cluster Resource Manager di Service Fabric a comprendere il layout del cluster. I domini di aggiornamento definiscono set di nodi che vengono aggiornati contemporaneamente. I domini di aggiornamento aiutano Cluster Resource Manager a comprendere e orchestrare le operazioni di gestione come gli aggiornamenti.

I domini di aggiornamento sono molto simili ai domini di errore, ma con un paio di differenze essenziali. Innanzitutto, i domini di errore vengono definiti da aree di errori hardware coordinati. I domini di aggiornamento invece vengono definiti dai criteri. È possibile deciderne il numero desiderato, in quanto la decisione non viene presa dall'ambiente. È possibile disporre dello stesso numero di domini di aggiornamento e di nodi. Un'altra differenza tra i domini di errore e i domini di aggiornamento è che questi ultimi non sono gerarchici. Sono invece più simili a un semplice tag. 

Il diagramma seguente mostra tre domini di aggiornamento distribuiti su tre domini di errore. Illustra anche una sola posizione possibile per tre repliche diverse di un servizio con stato, dove ciascuna finisce in diversi domini di errore e di aggiornamento. Questo posizionamento consente la perdita di un dominio di errore mentre è in corso un aggiornamento del servizio, mantenendo comunque una copia del codice e dei dati.  

<center>
![Posizionamento con domini di errore e di aggiornamento][Image3]
</center>

L'uso di un numero elevato di domini di aggiornamento presenta vantaggi e svantaggi. Se si hanno numerosi domini di aggiornamento, ogni passaggio dell'aggiornamento è più dettagliato e quindi influisce su un numero minore di nodi o servizi. Questo si traduce nella necessità di spostare meno servizi contemporaneamente nonché in una minore varianza del sistema. Anche l'affidabilità tende a migliorare, poiché una parte minore del servizio è interessata da eventuali problemi introdotti durante l'aggiornamento. La presenza di più domini di aggiornamento significa anche che è richiesto meno buffer disponibile negli altri nodi per gestire l'impatto dell'aggiornamento. Ad esempio, se si dispone di cinque domini di aggiornamento, i nodi di ciascuno gestiscono circa il 20% del traffico. Se è necessario bloccare il dominio di aggiornamento per un aggiornamento, il suo carico deve essere in genere trasferito altrove. Poiché sono presenti quattro domini di aggiornamento rimanenti, ognuno deve disporre di spazio per circa il 5% del traffico totale. Più domini di aggiornamento implicano la necessità di meno buffer nei nodi del cluster. Si consideri, ad esempio, di disporre invece di dieci domini di aggiornamento. In questo caso ogni dominio di aggiornamento dovrebbe gestire solo circa il 10% del traffico totale. Quando un aggiornamento passa per il cluster, ogni dominio deve avere spazio solo per circa l'1,1% del traffico totale. Un maggior numero di domini di aggiornamento in genere consente un maggiore uso dei nodi, poiché è necessaria una capacità di riserva minore. Lo stesso vale per i domini di errore.  

Lo svantaggio di avere molti domini di aggiornamento è che gli aggiornamenti tendono a richiedere più tempo. Service Fabric attende per un breve periodo dopo il completamento di un aggiornamento ed esegue i controlli prima di avviare l'aggiornamento successivo. Questi ritardi consentono di rilevare i problemi introdotti in seguito all'aggiornamento prima che l'aggiornamento continui. Questo compromesso è accettabile perché evita che modifiche non valide abbiano un impatto eccessivo sul servizio in un determinato momento.

Anche l'uso di un numero troppo ridotto di domini di aggiornamento ha effetti collaterali negativi: mentre ogni singolo dominio di aggiornamento è inattivo e in fase di aggiornamento, una parte elevata della capacità complessiva non risulta disponibile. Ad esempio, se sono presenti solo tre domini di aggiornamento, 1/3 circa della capacità complessiva del servizio o del cluster non sarà disponibile in un determinato momento. Avere una parte così grande del servizio inattiva contemporaneamente non è auspicabile poiché è necessario disporre di capacità sufficiente nel resto del cluster per gestire il carico di lavoro. Gestire quel buffer significa che durante il normale funzionamento i nodi avranno minor carico di quanto dovuto. Questo approccio implica un aumento del costo di esecuzione del servizio.

Non è previsto alcun limite effettivo per il numero totale di domini di errore o di aggiornamento in un ambiente e non sono previsti vincoli per le relative sovrapposizioni. Ciò premesso, sono disponibili vari modelli comuni:

- Corrispondenza 1:1 fra domini di errore e domini di aggiornamento
- Un dominio di aggiornamento per nodo (istanza del sistema operativo fisico o virtuale)
- Un modello con "striping" o a "matrice" in cui i domini di errore e i domini di aggiornamento formano una matrice e i computer sono disposti lungo le diagonali

<center>
![Layout dei domini di errore e di aggiornamento][Image4]
</center>

Non esiste un layout ottimale, ogni layout presenta vantaggi e svantaggi. Ad esempio, il modello di tipo 1FD:1UD è semplice da configurare. Il modello con un dominio di aggiornamento per nodo è probabilmente quello più noto. Durante l'aggiornamento, ogni nodo viene aggiornato in modo indipendente. È simile alla modalità con la quale un piccolo gruppo di computer veniva aggiornato manualmente in passato. 

Il modello più comune è basato sulla matrice FD/UD, in cui i domini di errore e i domini di aggiornamento formano una tabella e i nodi vengono posizionati a partire dalla diagonale. È il modello usato per impostazione predefinita nei cluster di Service Fabric in Azure. Per i cluster con molti nodi, il risultato è simile al complesso modello di matrice precedente.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Vincoli del dominio di errore e di aggiornamento e comportamento risultante
Cluster Resource Manager considera come un vincolo il desiderio di mantenere un servizio bilanciato tra domini di errore e di aggiornamento. È possibile trovare altre informazioni sui vincoli [in questo articolo](service-fabric-cluster-resource-manager-management-integration.md). I vincoli di dominio di errore e di aggiornamento stabiliscono: "Per una data partizione di servizio non deve esistere una differenza *maggiore di uno* nel numero di oggetti servizio (istanze di servizio senza stato o repliche con stato) tra due domini". Ciò impedisce determinati spostamenti o disposizioni che violano il vincolo.

Esaminiamo un esempio. Si supponga di avere un cluster con sei nodi, configurato con cinque domini di errore e cinque domini di aggiornamento.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Si supponga ora di creare un servizio con TargetReplicaSetSize (oppure, per un servizio senza stato InstanceCount) pari a cinque. Le repliche avvengono in N1 N5. N6 non verrà mai usato indipendentemente dal numero di servizi simili creati. Ma perché? Esaminiamo la differenza tra il layout corrente e ciò che accadrebbe se si scegliesse N6.

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

Questo layout viola la definizione del vincolo di dominio di errore. FD0 ha due repliche, mentre FD1 ne ha zero, perciò la differenza tra FD0 e FD1 è due. Cluster Resource Manager non consente questa disposizione. Allo stesso modo, se si scegliesse N2 e N6 (anziché N1 e N2) si otterrebbe:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Questo layout è bilanciato in termini di domini di errore. Viola tuttavia il vincolo del dominio di aggiornamento. Ciò avviene perché UD0 ha zero repliche mentre UD1 ne ha due. Questo layout inoltre non è valido e non verrà scelto da Cluster Resource Manager. 

## <a name="configuring-fault-and-upgrade-domains"></a>Configurazione dei domini di errore e di aggiornamento
La definizione dei domini di errore e dei domini di aggiornamento viene eseguita automaticamente nelle distribuzioni di Service Fabric ospitate in Azure. Service Fabric recupera semplicemente le informazioni sull'ambiente da Azure e le usa.

Se si sta creando il proprio cluster o si desidera seguire una particolare topologia, è possibile specificare manualmente le informazioni sul dominio di errore e sul dominio di aggiornamento. In questo esempio definiamo un cluster di sviluppo locale con nove nodi che si estende su tre "data center" (ognuno con tre rack). Il cluster ha anche tre domini di aggiornamento distribuiti sui tre data center. Di seguito viene riportato un esempio di configurazione: 

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
> Quando si definiscono i cluster con Azure Resource Manager, Azure assegna i domini di errore e i domini di aggiornamento. Pertanto, la definizione dei tipi di nodo e dei set di scalabilità delle macchine virtuali nel modello di Azure Resource Manager non include informazioni sul dominio di aggiornamento o sul dominio di errore.
>

## <a name="node-properties-and-placement-constraints"></a>Proprietà dei nodi e vincoli di posizionamento
Nella maggior parte dei casi, si vuole assicurare che determinati carichi di lavoro vengano eseguiti solo su determinati tipi di nodi nel cluster. Ad esempio, è possibile che alcuni carichi di lavoro richiedano GPU o SSD, mentre altri no. Un ottimo esempio di uso dell'hardware per carichi di lavoro specifici è dato da quasi tutte le architettura a più livelli. Alcuni computer fungono da lato front-end o API dell'applicazione e pertanto sono probabilmente esposti al client o a Internet. Altri computer, spesso con risorse hardware diverse, gestiscono il lavoro dei livelli di calcolo o archiviazione. In genere _non_ sono esposti direttamente a Internet o ai client. Service Fabric prevede alcune situazioni in cui determinati carichi di lavoro dovranno essere eseguiti in configurazioni hardware specifiche, ad esempio:

* Un'applicazione esistente con n livelli è stata "elevata e spostata" in un ambiente Service Fabric.
* Un carico di lavoro deve essere eseguito su hardware specifico per finalità di prestazioni, scalabilità o isolamento di sicurezza.
* Un carico di lavoro deve essere isolato da altri carichi di lavoro per motivi relativi ai criteri o all'uso delle risorse

Per supportare questi tipi di configurazione, Service Fabric dispone di tag estremamente efficienti applicabili ai nodi. Questi tag vengono chiamati **proprietà del nodo**. I **vincoli di posizionamento** sono le istruzioni collegate ai singoli servizi che selezionano una o più proprietà del nodo. Definiscono la posizione in cui i servizi devono essere eseguiti. Il set di vincoli è estendibile: si può usare qualsiasi coppia chiave/valore. 

<center>
![Layout di cluster con carichi di lavoro diversi][Image5]
</center>

### <a name="built-in-node-properties"></a>Proprietà predefinite del nodo
Service Fabric definisce alcune proprietà predefinite dei nodi che possono essere successivamente usate automaticamente, senza alcun intervento da parte dell'utente. Le proprietà predefinite specificate a livello di ogni nodo sono **NodeType** e **NodeName**. Ad esempio è possibile scrivere un vincolo di posizionamento come `"(NodeType == NodeType03)"`. NodeType è una delle proprietà più usate. È utile poiché ha una corrispondenza di 1:1 con un tipo di computer. Ogni tipo di computer corrisponde a un tipo di carico di lavoro in un'applicazione tradizionale a più livelli.

<center>
![Vincoli di posizionamento e proprietà dei nodi][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Vincoli di posizionamento e sintassi delle proprietà dei nodi 
Il valore specificato nella proprietà del nodo può essere una stringa, un valore booleano o un valore lungo firmato. L'istruzione a livello di servizio è chiamata *vincolo* di posizionamento in quanto vincola la posizione in cui il servizio può essere eseguito nel cluster. Il vincolo può essere qualsiasi istruzione booleana che opera sulle diverse proprietà dei nodi nel cluster. I selettori validi in queste istruzioni booleane sono:

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

È possibile posizionare il servizio solo sui nodi in cui l'istruzione del vincolo di posizionamento generale restituisce "True". I nodi per i quali sono state definite proprietà non corrispondono ad alcun vincolo di posizionamento che contiene proprietà.

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

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per cluster ospitati in Azure. 

> [!NOTE]
> Nel modello di Azure Resource Manager il tipo di nodo è in genere con parametri, e più simile a "[parameters('vmNodeType1Name')]" che non a "NodeType01".
>

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se tutti i nodi NodeType01 sono validi, è anche possibile selezionare un tipo di nodo con vincolo "(NodeType == NodeType01)".

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

I vincoli di posizionamento vengono specificati per ogni istanza del servizio denominata in modo differente. Gli aggiornamenti sostituiscono (sovrascrivono) sempre quanto specificato in precedenza.

La definizione del cluster definisce le proprietà in un nodo. Per modificare le proprietà di un nodo è necessario un aggiornamento della configurazione del cluster. L'aggiornamento delle proprietà di un nodo richiede che ogni nodo in questione venga riavviato per segnalare le nuove proprietà. Questi aggiornamenti in sequenza sono gestiti da Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Descrizione e gestione delle risorse del cluster
Uno dei processi più importanti di un agente di orchestrazione consiste nel semplificare la gestione dell'utilizzo delle risorse del cluster. Gestione delle risorse del cluster può avere significati diversi. Innanzitutto, è necessario verificare che i computer non siano sovraccarichi, ovvero che non eseguano più servizi di quanti ne possano gestire. In secondo luogo, il bilanciamento e l'ottimizzazione sono di fondamentale importanza per l'esecuzione efficiente dei servizi. Un'offerta di servizi conveniente o esigente in termini di prestazioni non può permettere un eccessivo sbilanciamento di utilizzo dei nodi. I nodi più usati si contendono le risorse offrendo prestazioni scarse mentre i nodi poco usati sono uno spreco di risorse e costi. 

Service Fabric rappresenta le risorse come `Metrics`. Per metrica si intende qualsiasi risorsa logica o fisica che deve essere descritta per Service Fabric, ad esempio "WorkQueueDepth" o "MemoryInMb". Per informazioni sulle risorse fisiche che Service Fabric può gestire sui nodi, vedere [governance delle risorse](service-fabric-resource-governance.md). Per informazioni sulla configurazione di metriche personalizzate e sul loro utilizzo, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)

Le metriche sono diverse dai vincoli di posizionamento e dalle proprietà del nodo. Le proprietà dei nodi sono descrittori statici dei nodi stessi. Le metriche descrivono le risorse di cui dispongono i nodi e che vengono consumate dai servizi quando vengono eseguiti in un nodo. Una proprietà del nodo potrebbe essere "HasSSD" e potrebbe essere impostata su true o false. La quantità di spazio disponibile su tale unità SSD (e la quantità usata dai servizi) può essere una metrica come "DriveSpaceInMb". 

È importante notare che, come per i vincoli di posizionamento e le proprietà del nodo, Cluster Resource Manager di Service Fabric non sa cosa significano i nomi delle metriche. I nomi delle metriche sono semplicemente stringhe. È consigliabile dichiarare l'unità come parte del nome della metrica creato quando potrebbe essere ambigua.

## <a name="capacity"></a>Capacità
Se si disattiva completamente il *bilanciamento*di tutte le risorse, Cluster Resource Manager di Service Fabric tenta comunque di assicurare che nessun nodo superi la propria capacità consentita. Gestire i sovraccarichi di capacità è possibile, a meno che il cluster non sia pieno o il carico di lavoro sia maggiore rispetto a qualsiasi altro nodo. La capacità è un altro *vincolo* usato da Cluster Resource Manager per conoscere la quantità di una risorsa posseduta da un nodo. La capacità rimanente verrà registrata anche per il cluster nel suo complesso. Sia la capacità che l'utilizzo a livello di servizio sono espressi in termini di metriche. Ad esempio la metrica potrebbe essere "ClientConnections" e un determinato nodo potrebbe avere una capacità "ClientConnections" di 32768. Gli altri nodi possono avere altri limiti. Alcuni servizi in esecuzione nel nodo possono dichiarare che esso consumi al momento 32256 della metrica "ClientConnections".

In fase di esecuzione, Cluster Resource Manager tiene traccia delle capacità rimanenti nel cluster e nei nodi. A tal fine, sottrae quanto usato da ciascun servizio dalla capacità del nodo in cui esso viene eseguito. Queste informazioni consentono a Cluster Resource Manager di Service Fabric di individuare la posizione ottimale per l'inserimento o lo spostamento di repliche, in modo che i nodi non superino le rispettive capacità.

<center>
![Nodi e capacità del cluster][Image7]
</center>

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

È possibile vedere le capacità definite nel manifesto del cluster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per cluster ospitati in Azure. 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

In genere il carico di un servizio cambia in modo dinamico. Si supponga che il carico di una replica di "ClientConnections" sia stato modificato da 1024 a 2048, ma il nodo in cui era in esecuzione in quel momento avesse solo una disponibilità residua della metrica pari a 512. Ora il posizionamento della replica o dell'istanza non è valido, poiché in quel nodo non c'è spazio sufficiente. Cluster Resource Manager deve intervenire e riportare il nodo sotto il valore di capacità massimo. A tal fine, deve spostare una o più repliche o istanze da quel nodo ad altri nodi. Quando sposta le repliche, Cluster Resource Manager tenta di ridurre al minimo il costo di tali spostamenti. I costi degli spostamenti vengono approfonditi in [questo articolo](service-fabric-cluster-resource-manager-movement-cost.md). Altre informazioni sulle strategie e le regole di ribilanciamento di Cluster Resource Manager sono disponibili [qui](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacità del cluster
In che modo Cluster Resource Manager di Service Fabric impedisce il caricamento eccessivo del cluster nel suo complesso? Con il carico dinamico non è possibile intervenire in modo netto. I servizi possono subire picchi di carico indipendentemente dalle azioni eseguite da Cluster Resource Manager. Di conseguenza un cluster che dispone di molta capacità oggi può non essere abbastanza potente domani se l'utilizzo aumenta. Detto ciò, sono stati integrati alcuni controlli per evitare problemi. È prima di tutto possibile impedire la creazione di nuovi carichi di lavoro che riempirebbero il cluster.

Si supponga di creare un servizio senza stato al quale è associato un carico. Si immagini che il servizio monitori la metrica "DiskSpaceInMb". Si supponga anche che consumerà cinque unità di "DiskSpaceInMb" per ogni istanza del servizio. Si vogliono creare tre istanze del servizio. L'installazione è riuscita. È quindi necessario che 15 unità di "DiskSpaceInMb" siano presenti nel cluster per consentire la mera creazione di queste istanze del servizio. Cluster Resource Manager calcola continuamente la capacità e il consumo di ogni metrica e può pertanto determinare la capacità residua nel cluster. Se non c'è spazio sufficiente, Cluster Resource Manager rifiuta la chiamata di creazione del servizio.

Poiché l'unico requisito è che ci siano 15 unità disponibili, questo spazio può essere allocato in molti modi diversi. Ad esempio potrebbe esserci un'unità di capacità rimanente in 15 nodi diversi oppure tre unità di capacità rimanenti in cinque nodi diversi. Se Cluster Resource Manager è in grado di sistemare le cose in modo che ci siano cinque unità disponibili in tre nodi, alla fine posizionerà il servizio. In genere la riorganizzazione del cluster è possibile, a meno che il cluster non sia quasi pieno o che, per qualsiasi motivo, non sia possibile consolidare i servizi esistenti.

## <a name="buffered-capacity"></a>Capacità in buffering
La capacità in buffering è un'altra funzionalità di Cluster Resource Manager, che consente di riservare parti della capacità complessiva del nodo. Questo buffer di capacità viene usato solo per posizionare i servizi durante gli aggiornamenti e gli errori del nodo. La capacità in buffering è specificata a livello globale per ogni metrica e per tutti i nodi. Il valore scelto per la capacità di riserva è una funzione del numero di domini di errore e di aggiornamento presenti nel cluster. Quando è presente un numero maggiore di domini di errore e di aggiornamento è possibile scegliere un valore inferiore per il buffer di capacità. Se si dispone di molti domini, ci si può aspettare la mancata disponibilità di porzioni inferiori del cluster durante gli aggiornamenti e gli errori. Specificare capacità in buffering risulta utile solo se è stata specificata anche la capacità del nodo per una metrica.

Di seguito è riportato un esempio di come specificare la capacità di memorizzazione nel buffer:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
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
          "name": "SomeMetric",
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

La creazione di nuovi servizi non riesce quando il cluster esaurisce il buffer di capacità per una metrica. Impedire la creazione di nuovi servizi per conservare il buffer garantisce che gli errori e gli aggiornamenti non causino il superamento della capacità dei nodi. Il buffer di capacità è un'opzione facoltativa ma consigliata per tutti i cluster che definiscono una capacità per una metrica.

Cluster Resource Manager espone le informazioni sul carico seguenti. Per ogni metrica, le informazioni includono: 
  - impostazioni della capacità di buffering
  - capacità totale
  - consumo corrente
  - se ogni metrica può essere considerata bilanciata oppure no
  - statistiche relative alla deviazione standard
  - nodi con più e meno carico  
  
Di seguito un esempio dell'output:

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

