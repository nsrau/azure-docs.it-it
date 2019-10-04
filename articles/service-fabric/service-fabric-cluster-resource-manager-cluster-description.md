---
title: Descrivere un cluster usando Cluster Resource Manager | Microsoft Docs
description: Descrivere un cluster di Service Fabric specificando i domini di errore, i domini di aggiornamento, le proprietà dei nodi e le capacità del nodo per Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271638"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descrivere un cluster di Service Fabric usando Cluster Resource Manager
La funzionalità di Cluster Resource Manager di Azure Service Fabric offre diversi meccanismi per descrivere un cluster:

* Domini di errore
* Domini di aggiornamento
* Proprietà del nodo
* Capacità del nodo

Durante la fase di esecuzione, Cluster Resource Manager Usa queste informazioni per garantire un'elevata disponibilità dei servizi in esecuzione nel cluster. Applicando queste regole importanti, tenta anche di ottimizzare il consumo di risorse all'interno del cluster.

## <a name="fault-domains"></a>Domini di errore
Un dominio di errore (FD, Fault Domain) è un'area di errore coordinato. Un singolo computer è un dominio di errore. Si può arrestare da solo per vari motivi, dalla potenza interruzioni dell'alimentazione a errori delle unità o firmware NIC non valido. 

I computer connessi allo stesso commutatore Ethernet sono nello stesso dominio di errore. Pertanto, sono le macchine che condividono una singola fonte di alimentazione o in un'unica posizione. 

Poiché è naturale per errori hardware si sovrappongano, i domini di errore sono intrinsecamente gerarchici. Sono rappresentati come URI in Service Fabric.

È importante che domini di errore siano impostati correttamente poiché Service Fabric Usa queste informazioni per posizionare in modo sicuro i servizi. Service Fabric non desidera posizionare i servizi in modo che la perdita di un dominio di errore (a causa dell'errore di qualche componente), un servizio diventano inattivi. 

Nell'ambiente di Azure, Service Fabric Usa le informazioni di dominio di errore fornite dall'ambiente per configurare correttamente i nodi del cluster per tuo conto. Per istanze autonome di Service Fabric, i domini di errore sono definiti nel momento in cui è configurato il cluster. 

> [!WARNING]
> È importante che le informazioni di dominio di errore fornite a Service Fabric siano accurate. Ad esempio, si supponga che i nodi del cluster di Service Fabric siano in esecuzione all'interno di 10 macchine virtuali, in esecuzione in host fisici 5. In questo caso, anche se sono presenti 10 macchine virtuali, sono presenti solo 5 domini di errore diversi (livello superiore). Condividono lo stesso host fisico fa sì che le macchine virtuali di condividere lo stesso dominio di errore radice, poiché le macchine virtuali verificano un errore coordinato se ha esito negativo relativo host fisico.  
>
> Service Fabric prevede che il dominio di errore di un nodo non di modificarli. Altri meccanismi per garantire la disponibilità elevata delle macchine virtuali, ad esempio [macchine virtuali a disponibilità elevata-](https://technet.microsoft.com/library/cc967323.aspx), potrebbe causare conflitti con Service Fabric. Questi meccanismi adottano la migrazione trasparente delle macchine virtuali da un host a altro. Non riconfigurare o notificare il codice in esecuzione nella macchina virtuale. Di conseguenza, risultano *non è supportato* come ambienti per l'esecuzione di Service Fabric cluster. 
>
> Service Fabric deve essere l'unica tecnologia di disponibilità elevata in uso. Non sono necessari i meccanismi, ad esempio live migration di macchine Virtuali e nomi alternativi del soggetto. Se questi meccanismi vengono utilizzati in combinazione con Service Fabric, essi _ridurre_ della disponibilità dell'applicazione e l'affidabilità. Il motivo è che introducono complessità aggiuntive, aggiungono origini centralizzate di errore e usare strategie di disponibilità e affidabilità che sono in conflitto con quelle in Service Fabric. 
>
>

Nella figura seguente, vengono colorate tutte le entità che contribuiscono ai domini di errore ed elencare tutti i diversi domini di errore risultanti. In questo esempio sono presenti data center ("DC"), rack ("R") e pannelli ("B"). Se ogni pannello include più di una macchina virtuale, potrebbe esserci un altro livello nella gerarchia di domini di errore.

<center>

![Nodi organizzati tramite domini di errore][Image1]
</center>

Durante la fase di esecuzione, Cluster Resource Manager di Service Fabric prende in considerazione i domini di errore nel cluster e pianifica i layout. Le repliche con state o istanze senza stato per un servizio vengono distribuite in modo che siano in domini di errore. La distribuzione del servizio tra domini di errore assicura che non è compromessa la disponibilità del servizio quando si verifica un errore di un dominio di errore a qualsiasi livello della gerarchia.

Cluster Resource Manager non è rilevante il numero di livelli presenti nella gerarchia di domini di errore. Tenta di garantire che la perdita di una parte della gerarchia non influiscono su servizi in esso eseguiti. 

È consigliabile se è lo stesso numero di nodi a ogni livello di profondità della gerarchia di dominio di errore. Se la "struttura" dei domini di errore non viene bilanciata nel cluster, risulta più difficile per Cluster Resource Manager per determinare la migliore allocazione dei servizi. Layout dei domini di errore sbilanciati significa che la perdita di alcuni domini influisca sulla disponibilità dei servizi più di altri domini. Di conseguenza, Cluster Resource Manager trova a dover scegliere fra due obiettivi: 

* Vuole usare le macchine nel dominio "pesante" posizionando i servizi su di essi. 
* Desidera posizionare i servizi negli altri domini in modo che la perdita di un dominio non causi problemi. 

Come appaiono i domini sbilanciati? Il diagramma seguente mostra due layout di cluster diverso. Nel primo esempio, i nodi sono distribuiti uniformemente tra i domini di errore. Nel secondo esempio, un dominio di errore ha molti più nodi di altri domini di errore. 

<center>

![Due layout di cluster diversi][Image2]
</center>

In Azure, la scelta di quale errore dominio contiene un nodo viene gestita automaticamente. Tuttavia, a seconda del numero di nodi che si esegue il provisioning, è comunque possibile ritrovarsi con domini di errore con più nodi in esse rispetto in altri casi. 

Ad esempio, si dispone di cinque domini di errore del cluster ma il provisioning di sette nodi per un tipo di nodo (**NodeType**). In questo caso, i primo due domini di errore avranno più nodi. Se si continua a distribuire più **NodeType** istanze con solo un paio di istanze, il problema peggiora. Per questo motivo, è consigliabile che il numero di nodi in ogni tipo di nodo è un multiplo del numero di domini di errore.

## <a name="upgrade-domains"></a>Domini di aggiornamento
Domini di aggiornamento sono un'altra funzionalità che aiuta Cluster Resource Manager di Service Fabric a comprendere il layout del cluster. Domini di aggiornamento definiscono set di nodi che vengono aggiornati contemporaneamente. Domini di aggiornamento aiutano Cluster Resource Manager di comprendere e orchestrare le operazioni di gestione, ad esempio gli aggiornamenti.

I domini di aggiornamento sono molto come domini di errore, ma con un paio di differenze essenziali. In primo luogo, le aree di errori hardware coordinati definiscono domini di errore. I domini di aggiornamento, d'altra parte, vengono definiti dai criteri. È possibile decidere quanti, invece di consentire l'ambiente di dettare un numero. È possibile avere tanti domini di aggiornamento di nodi. Un'altra differenza tra domini di errore e domini di aggiornamento è che i domini di aggiornamento non sono gerarchici. Al contrario, risultano più simile a un semplice tag. 

Il diagramma seguente mostra tre domini di aggiornamento distribuiti in tre domini di errore. Viene inoltre una sola posizione possibile per tre repliche diverse di un servizio con stato, dove ciascuna finisce in diversi domini di errore e aggiornamento. Questo posizionamento consente la perdita di un dominio di errore mentre è in corso un aggiornamento del servizio, ancora una copia del codice e dati.  

<center>

![Posizionamento con domini][Image3]
</center>

Esistono vantaggi e svantaggi alla presenza di un numero elevato di domini di aggiornamento. Più domini di aggiornamento significa che ogni passaggio dell'aggiornamento è più dettagliato e influisce su un numero minore di nodi o servizi. Un minor numero di servizi sono necessario spostare contemporaneamente, Introduzione a minore varianza nel sistema. Tende a migliorare l'affidabilità, perché minore del servizio è influenzato da eventuali problemi introdotto durante l'aggiornamento. Più domini di aggiornamento significa anche che è necessario meno buffer disponibile negli altri nodi per gestire l'impatto dell'aggiornamento. 

Ad esempio, se si dispone di cinque domini di aggiornamento, i nodi di ciascuno gestiscono circa il 20% del traffico. Se è necessario bloccare il dominio di aggiornamento per un aggiornamento, che il carico deve in genere trasferita da qualche parte. Poiché si dispone di quattro domini di aggiornamento rimanenti, ognuno deve disporre di spazio per circa il 5% del traffico totale. Più domini di aggiornamento significa che è necessario meno buffer nei nodi del cluster. 

Prendere in considerazione se invece era 10 domini di aggiornamento. In tal caso, ogni dominio di aggiornamento verrebbe gestito solo circa il 10% del traffico totale. Quando un aggiornamento tramite il cluster, ogni dominio dovrà disporre di spazio per solo 1.1% del traffico totale. Più domini di aggiornamento in genere consentono di eseguire i nodi a un utilizzo più elevato, perché è necessaria una capacità di riserva minore. Lo stesso vale per i domini di errore.  

Lo svantaggio di avere molti domini di aggiornamento è che gli aggiornamenti tendono a richiedere più tempo. Service Fabric attende un breve periodo dopo che un dominio di aggiornamento è stato completato ed esegue i controlli prima di iniziare a eseguire l'aggiornamento successivo. Questi ritardi consentono di rilevare i problemi introdotti in seguito all'aggiornamento prima che l'aggiornamento continui. Questo compromesso è accettabile perché evita che modifiche non valide abbiano un impatto eccessivo sul servizio in un determinato momento.

La presenza di un numero troppo ridotto di domini di aggiornamento ha effetti collaterali negativi. Ogni dominio di aggiornamento verso il basso mentre è in corso l'aggiornamento, una parte elevata della capacità complessiva non è disponibile. Ad esempio, se si dispone solo tre domini di aggiornamento, sta assumendo verso il basso di circa un terzo complessiva del servizio o la capacità del cluster alla volta. Gran parte del servizio che verso il basso in una sola volta non è auspicabile poiché necessaria una capacità sufficiente nel resto del cluster per gestire il carico di lavoro. Gestire che quel buffer significa che durante il normale funzionamento, questi nodi sono che meno caricati di quanto sarebbe in caso contrario. Questo approccio implica un aumento del costo di esecuzione del servizio.

Non è previsto alcun limite effettivo per il numero totale di domini di errore o di aggiornamento in un ambiente e non sono previsti vincoli per le relative sovrapposizioni. Ma sono disponibili modelli comuni:

- Domini di errore e domini di aggiornamento eseguito il mapping 1:1
- Un dominio di aggiornamento per nodo (istanza del sistema operativo fisico o virtuale)
- Un modello "con striping" o "matrice" in cui i domini di errore e domini di aggiornamento formano una matrice con computer che eseguono in genere disposti lungo le diagonali

<center>

![Layout dei domini di errore e di aggiornamento][Image4]
</center>

Non vi è alcuna risposta migliore per scegliere quale layout. Ognuna presenta vantaggi e svantaggi. Ad esempio, il modello di tipo 1FD:1UD è semplice da configurare. Il modello di un dominio di aggiornamento per ogni modello di nodo è più, ad esempio quali utenti vengono utilizzati per. Durante gli aggiornamenti, ogni nodo viene aggiornato in modo indipendente. È simile alla modalità con la quale un piccolo gruppo di computer veniva aggiornato manualmente in passato.

Il modello più comune è sulla matrice FD/UD, in cui i domini di errore e domini di aggiornamento formano una tabella e i nodi vengono posizionati a partire dalla diagonale. È il modello usato per impostazione predefinita nei cluster di Service Fabric in Azure. Per i cluster con più nodi, tutto ciò che termina come indicato di un modello di matrice a densità elevata.

> [!NOTE]
> Cluster di Service Fabric ospitate in Azure non supporta la modifica della strategia predefinita. Solo i cluster autonomi offrono la personalizzazione.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Vincoli del dominio di errore e di aggiornamento e comportamento risultante
### <a name="default-approach"></a>Approccio predefinito
Per impostazione predefinita, Gestione risorse Cluster mantiene bilanciati i servizi tra domini di errore e di aggiornamento. Tale equilibrio è modellato come un [vincolo](service-fabric-cluster-resource-manager-management-integration.md). Il vincolo per gli stati di domini di errore e di aggiornamento: "Per una data partizione di servizio, si deve esistere una differenza maggiore di uno il numero di oggetti servizio (istanze del servizio senza stato o repliche con stato) tra due domini dello stesso livello della gerarchia."

Si supponga che questo vincolo fornisca una garanzia di "differenza massima". Il vincolo per i domini di errore e di aggiornamento impedisce determinati spostamenti o disposizioni che violano la regola.

Ad esempio, si supponga di che avere un cluster con sei nodi, configurato con cinque domini di errore e cinque domini di aggiornamento.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Ora si supponga di creare un servizio con un **TargetReplicaSetSize** (oppure, per un servizio senza stato, **InstanceCount**) valore di cinque. Le repliche avvengono in N1 N5. N6 non verrà mai usato indipendentemente dal numero di servizi simili creati. Ma perché? Esaminiamo la differenza tra il layout corrente e ciò che accadrebbe se si scegliesse N6.

Ecco il layout ottenuti e il numero totale di repliche per ogni dominio di errore e di aggiornamento:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Questo layout è equilibrato in termini di nodi per ogni dominio di errore e dominio di aggiornamento. È anche bilanciato in termini di numero di repliche per ogni dominio di errore e di aggiornamento. Ogni dominio ha lo stesso numero di nodi e lo stesso numero di repliche.

A questo punto vediamo cosa accadrebbe se usassimo N6 invece di N2. Come sarebbero state distribuite le repliche?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Questo layout viola la definizione di garanzia di "differenza massima" per il vincolo di dominio di errore. FD0 ha due repliche, mentre FD1 ne ha zero. La differenza tra FD0 e FD1 è due, che è maggiore della differenza massima di uno. Poiché il vincolo è violato, Cluster Resource Manager non consente questa disposizione. Analogamente, se si scegliesse N2 e N6 (anziché N1 e N2), si otterrebbe:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Questo layout è equilibrato in termini di domini di errore. Ma ora viene violato il vincolo di dominio di aggiornamento, poiché UD0 ha zero repliche e ud1 ne ha due. Questo layout inoltre non è valido e non verrà scelto da Cluster Resource Manager.

Questo approccio alla distribuzione delle repliche con stato o delle istanze senza stato offre la migliore tolleranza di errore possibile. Se un dominio diventa inattivo, il numero minimo di repliche/istanze viene perso. 

D'altro canto, questo approccio può essere troppo limitato e non consentire al cluster di usare tutte le risorse. Per determinate configurazioni del cluster non è possibile usare alcuni nodi. Ciò può provocare Service Fabric non posizionare i servizi, risultante in messaggi di avviso. Nell'esempio precedente, alcuni dei nodi del cluster non possono essere usati (N6 nell'esempio). Anche se sono stati aggiunti i nodi cluster (N7-N10), le repliche o le istanze verranno inserite solo in N1-N5 a causa di vincoli nei domini di errore e di aggiornamento. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Approccio alternativo

Cluster Resource Manager supporta un'altra versione del vincolo per i domini di errore e di aggiornamento. Consente il posizionamento garantendo comunque un livello minimo di sicurezza. Il vincolo alternativo può essere dichiarato come segue: "Per una data partizione di servizio, distribuzione delle repliche tra domini deve garantire che la partizione non subisca una perdita di quorum." Si supponga che questo vincolo fornisca una garanzia di "sicurezza del quorum". 

> [!NOTE]
> In un servizio con stato si parla di *perdita del quorum* in una situazione in cui la maggioranza delle repliche di partizione è contemporaneamente inattiva. Ad esempio, se **TargetReplicaSetSize** è pari a cinque, un set di tre repliche rappresenta il quorum. Analogamente, se **TargetReplicaSetSize** è 6 e quattro repliche sono necessari per il quorum. In entrambi i casi, non più di due repliche possono essere verso il basso nello stesso momento la partizione possa continuare a funzionare normalmente. 
>
> Per un servizio senza stato, non è disponibile alcun equivalente di *perdita del quorum*. I servizi senza stato continuano a funzionare normalmente anche se si disattivano la maggior parte delle istanze nello stesso momento. Quindi, ci concentreremo sui servizi con stati nella parte restante di questo articolo.
>

Tornare all'esempio precedente. Con la versione "sicurezza del quorum" del vincolo, tutte e tre i layout sono validi. Anche se non è stato possibile FD0 nel secondo layout o non è stato possibile UD1 nel terzo layout, la partizione disporrebbe comunque quorum. (La maggioranza delle repliche sarebbe sempre attiva.) Con questa versione del vincolo N6 quasi sempre possono essere utilizzate.

L'approccio di "sicurezza del quorum" offre una maggiore flessibilità rispetto all'approccio di "differenza massima". Il motivo è che è più facile trovare distribuzioni replica valide in quasi qualsiasi topologia del cluster. Tuttavia, questo approccio non garantisce le caratteristiche di tolleranza di errore migliori perché alcuni errori sono più gravi di altri. 

Nel peggiore dei casi, la maggioranza delle repliche può essere persa con l'errore di un dominio e una sola replica aggiuntiva. Ad esempio, invece di tre errori per la perdita del quorum con 5 repliche o istanze, è ora possibile perdere una parte ingente con appena due errori. 

### <a name="adaptive-approach"></a>Approccio adattivo
Poiché entrambi gli approcci presentano vantaggi e svantaggi, è stato introdotto un approccio adattivo che combina queste due strategie.

> [!NOTE]
> Questo è il comportamento predefinito a partire da Service Fabric versione 6.2. 
> 
> L'approccio adattivo usa la logica della "differenza massima" per impostazione predefinita, per passare alla logica di "sicurezza del quorum" solo quando necessario. Cluster Resource Manager determina automaticamente la strategia è necessaria, esaminando come i cluster e i servizi configurati.
> 
> Cluster Resource Manager devono usare la logica "basata sul quorum" per un servizio che entrambe queste condizioni sono true:
>
> * **TargetReplicaSetSize** per il servizio non è divisibile uniformemente per il numero di domini di errore e il numero di domini di aggiornamento.
> * Il numero di nodi è minore o uguale al numero di domini di errore moltiplicati per il numero di domini di aggiornamento.
>
> Tenere presente che Cluster Resource Manager usa questo approccio per i servizi con e senza stati, anche se una perdita di quorum non è rilevante per i servizi senza stati.

Tornare all'esempio precedente e basate sul presupposto che un cluster disponga ora otto nodi. Il cluster è ancora configurato con cinque domini di errore e cinque domini di aggiornamento e il **TargetReplicaSetSize** valore di un servizio ospitato in tale cluster rimane cinque. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Perché vengono soddisfatte tutte le condizioni necessarie, Cluster Resource Manager utilizzerà per la logica "basata sul quorum" distribuzione del servizio. Ciò consente l'uso di N6 N8. Una distribuzione del servizio possibile in questo caso potrebbe essere simile al seguente:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se il servizio **TargetReplicaSetSize** valore viene ridotto a quattro (ad esempio), Cluster Resource Manager noterà tale modifica. L'operazione verrà ripresa utilizzando la logica di "differenza massima" poiché **TargetReplicaSetSize** non è più divisibile per il numero di domini di errore e domini di aggiornamento più. Di conseguenza, si verificherà alcuni movimenti di replica per distribuire le quattro repliche rimanenti nei nodi N1-N5. In questo modo, la versione "differenza massima" Errore aggiornamento e logica di dominio non sia violata. 

Nel layout precedente, se il **TargetReplicaSetSize** valore almeno pari a cinque e N1 viene rimosso dal cluster, il numero di domini di aggiornamento diventa uguale a quattro. Anche in questo caso, Cluster Resource Manager inizia con la logica di "differenza massima" perché il numero di domini di aggiornamento non divide uniformemente del servizio **TargetReplicaSetSize** più valore. Di conseguenza, replica R1, quando compilata di nuovo, deve cadere su N4 in modo che non sia violato il vincolo per il dominio di errore e di aggiornamento.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurazione di domini di errore e di aggiornamento
Nelle distribuzioni di Service Fabric ospitato in Azure, i domini di errore e domini di aggiornamento vengono definiti automaticamente. Service Fabric recupera semplicemente le informazioni sull'ambiente da Azure e le usa.

Se si sta creando il proprio cluster (o si desidera eseguire una particolare topologia in fase di sviluppo), è possibile fornire il dominio di errore e aggiornare manualmente le informazioni sul dominio. In questo esempio definiamo un cluster di sviluppo locale nove nodi che si estende su tre Data Center (ognuno con tre rack). Il cluster ha anche tre domini di aggiornamento striping su questi tre Data Center. Ecco un esempio di configurazione in Clustermanifest:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
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

Questo esempio Usa clusterconfig. JSON per le distribuzioni autonome:

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
> Quando si definiscono i cluster tramite Azure Resource Manager, Azure assegna domini di errore e domini di aggiornamento. In modo che imposta la definizione dei tipi di nodo e scalabilità di macchine virtuali nel modello di Azure Resource Manager non include informazioni sui domini di errore o dominio di aggiornamento.
>

## <a name="node-properties-and-placement-constraints"></a>Proprietà dei nodi e vincoli di posizionamento
In alcuni casi (in effetti, la maggior parte dei casi) si desidera assicurare che determinati carichi di lavoro eseguiti solo su determinati tipi di nodi del cluster. Ad esempio, alcuni carichi di lavoro potrebbero richiedere GPU o SSD e altri utenti potrebbero non. 

Un ottimo esempio di uso dell'hardware per determinati carichi di lavoro è quasi tutte le architettura a più livelli. Alcuni computer fungono dal lato di gestione API dell'applicazione o un front-end e vengono esposti al client o a internet. Altri computer, spesso con risorse hardware diverse, gestiscono il lavoro dei livelli di calcolo o archiviazione. In genere _non_ sono esposti direttamente a Internet o ai client. 

Service Fabric prevede che in alcuni casi, potrebbe essere necessario determinati carichi di lavoro per l'esecuzione in configurazioni hardware specifiche. Ad esempio:

* Un'applicazione a più livelli esistente è stata "elevata e spostata" in un ambiente di Service Fabric.
* Un carico di lavoro deve essere eseguito su hardware specifico per motivi di isolamento delle prestazioni, scalabilità o sicurezza.
* Un carico di lavoro deve essere isolato da altri carichi di lavoro per motivi di utilizzo dei criteri o risorsa.

Per supportare questi tipi di configurazione, Service Fabric include i tag che è possibile applicare ai nodi. Questi tag vengono chiamati *proprietà del nodo*. *I vincoli di posizionamento* sono istruzioni collegate ai singoli servizi selezionati per uno o più proprietà del nodo. Definiscono la posizione in cui i servizi devono essere eseguiti. Il set di vincoli è estendibile. Può usare qualsiasi coppia chiave/valore. 

<center>

![Diversi carichi di lavoro per un layout di cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Proprietà del nodo predefinito
Service Fabric definisce alcune proprietà predefinite dei nodi che possono essere utilizzati automaticamente in modo che non è necessario definirli. Le proprietà predefinite specificate in ogni nodo vengono **NodeType** e **NodeName**. 

Ad esempio, è possibile scrivere un vincolo di posizionamento come `"(NodeType == NodeType03)"`. **Tipo di nodo** è una proprietà di uso comune. È utile perché corrisponde 1:1 con un tipo di computer. Ogni tipo di computer corrisponde a un tipo di carico di lavoro in un'applicazione tradizionale a più livelli.

<center>

![I vincoli di posizionamento e proprietà dei nodi][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>I vincoli di posizionamento e sintassi di proprietà di nodo 
Il valore specificato nella proprietà del nodo può essere una stringa, booleano, o lungo firmato. L'istruzione nel servizio viene chiamata di posizionamento *vincolo* perché vincola in cui è possibile eseguire il servizio nel cluster. Il vincolo può essere qualsiasi istruzione booleana che opera sulle proprietà del nodo del cluster. I selettori validi in queste istruzioni booleane sono:

* Controlli condizionali per la creazione di istruzioni specifiche:

  | Istruzione | Sintassi |
  | --- |:---:|
  | "uguale a" | "==" |
  | "diverso da" | "!=" |
  | "maggiore di" | ">" |
  | "maggiore o uguale a" | ">=" |
  | "minore di" | "<" |
  | "minore o uguale a" | "<=" |

* Istruzioni booleane per le operazioni di raggruppamento e logiche:

  | Istruzione | Sintassi |
  | --- |:---:|
  | "and" | "&&" |
  | "or" | "&#124;&#124;" |
  | "not" | "!" |
  | "raggruppamento come singola istruzione" | "()" |

Di seguito sono riportati alcuni esempi di istruzioni base di limitazione:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

È possibile posizionare il servizio solo sui nodi in cui l'istruzione del vincolo di posizionamento generale restituisce "True". Nodi che non hanno una proprietà definita non corrispondono alcun vincolo di posizionamento che contiene la proprietà.

Si supponga che per un tipo di nodo in Clustermanifest siano state definite le proprietà seguenti:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Nell'esempio seguente mostra le proprietà di nodo definite mediante clusterconfig. JSON per le distribuzioni autonome o template. JSON per i cluster ospitati in Azure. 

> [!NOTE]
> Nel modello di Azure Resource Manager, il tipo di nodo è in genere con parametri. Sarà simile a `"[parameters('vmNodeType1Name')]"` anziché NodeType01.
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

È possibile creare il posizionamento del servizio *vincoli* per un servizio come indicato di seguito:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se tutti i nodi di NodeType01 siano validi, è anche possibile selezionare quel tipo di nodo con il vincolo `"(NodeType == NodeType01)"`.

I vincoli di posizionamento del servizio possono essere aggiornati in modo dinamico in fase di esecuzione. Se si desidera, è possibile spostare un servizio nel cluster, aggiungere e rimuovere requisiti e così via. Service Fabric assicura che il servizio sia sempre attivi e disponibili anche quando questi tipi di modifiche vengono apportati.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

I vincoli di posizionamento vengono specificati per ogni istanza del servizio denominato. Gli aggiornamenti sostituiscono (sovrascrivono) sempre quanto specificato in precedenza.

La definizione del cluster definisce le proprietà in un nodo. Modifica delle proprietà di un nodo richiede un aggiornamento per la configurazione del cluster. L'aggiornamento delle proprietà di un nodo richiede che ogni nodo in questione venga riavviato per segnalare le nuove proprietà. Service Fabric gestisce questi aggiornamenti in sequenza.

## <a name="describing-and-managing-cluster-resources"></a>Descrizione e gestione delle risorse del cluster
Uno dei processi più importanti di un agente di orchestrazione consiste nel semplificare la gestione dell'utilizzo delle risorse del cluster. Gestione delle risorse del cluster può avere significati diversi. 

Innanzitutto, è necessario verificare che i computer non siano sovraccarichi, ovvero che non eseguano più servizi di quanti ne possano gestire. 

In secondo luogo, c'è bilanciamento del carico e ottimizzazione, che sono fondamentali per l'esecuzione efficiente dei servizi. Offerte di servizi conveniente o sensibili alle prestazioni non possono consentire di alcuni nodi sia attivo e altri poco usati. Nodi attivo causano una riduzione delle prestazioni e contesa delle risorse. I nodi poco usati rappresentano uno spreco di risorse e aumento dei costi. 

Service Fabric rappresenta le risorse come *metriche*. Per metrica si intende qualsiasi risorsa logica o fisica che deve essere descritta per Service Fabric, Esempi di metriche sono "WorkQueueDepth" o "MemoryInMb". Per informazioni sulle risorse fisiche che Service Fabric può gestire sui nodi, vedere [governance delle risorse](service-fabric-resource-governance.md). Per informazioni sulla configurazione delle metriche personalizzate e il relativo utilizzo, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md).

Le metriche sono diverse dalle proprietà del nodo e i vincoli di posizionamento. Le proprietà dei nodi sono descrittori statici dei nodi stessi. Le metriche descrivono le risorse di cui dispongono i nodi e dai servizi durante l'esecuzione in un nodo. Potrebbe essere una proprietà del nodo **HasSSD** e potrebbe essere impostato su true o false. La quantità di spazio disponibile su tale unità SSD e quantità usata dai servizi sarebbe una metrica come "DriveSpaceInMb". 

Esattamente come per i vincoli di posizionamento e proprietà dei nodi, Cluster Resource Manager di Service Fabric non ha capito che i nomi della media delle metriche. I nomi delle metriche sono semplicemente stringhe. È consigliabile dichiarare l'unità come parte del nome della metrica creato quando potrebbe essere ambigue.

## <a name="capacity"></a>Capacity
Se sono state disattivate tutte le risorse *bilanciamento del carico*, Cluster Resource Manager di Service Fabric tenta comunque di assicurare che nessun nodo supera la capacità. Gestire i sovraccarichi di capacità è possibile, a meno che il cluster non sia pieno o il carico di lavoro sia maggiore rispetto a qualsiasi altro nodo. La capacità è un'altra *vincolo* che Cluster Resource Manager usa per comprendere la quantità di una risorsa di un nodo. La capacità rimanente verrà registrata anche per il cluster nel suo complesso. 

Sia la capacità che l'utilizzo a livello di servizio sono espressi in termini di metriche. Ad esempio, la metrica potrebbe essere "ClientConnections" e un nodo potrebbe avere una capacità "Clientconnections" di 32.768. Gli altri nodi possono avere altri limiti. Un servizio in esecuzione su tale nodo può ad esempio che dichiarare di consumare attualmente 32,256 della metrica "ClientConnections".

Durante la fase di esecuzione, Cluster Resource Manager tiene traccia delle capacità residua nel cluster e nei nodi. Per tenere traccia della capacità, Cluster Resource Manager sottrae sull'utilizzo di ogni servizio dalla capacità del nodo in cui viene eseguito il servizio. Queste informazioni, Cluster Resource Manager può individuare la posizione di inserimento o lo spostamento di repliche in modo che i nodi non superino la capacità.

<center>

![I nodi del cluster e la capacità][Image7]
</center>

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

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

È possibile visualizzare le capacità definite nel manifesto del cluster. Di seguito è riportato un esempio per Clustermanifest:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Di seguito è riportato un esempio di capacità definite mediante clusterconfig. JSON per le distribuzioni autonome o template. JSON per i cluster ospitati in Azure: 

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

Spesso le modifiche apportate il carico in modo dinamico un servizio. Si supponga che il carico di una replica di "ClientConnections" modificato da 1.024 a 2048. Il nodo che era in esecuzione su quindi abbia una capacità pari a 512 unica rimanente per tale metrica. Ora che la replica o la posizione dell'istanza non è valido, poiché non c'è spazio sufficiente su tale nodo. Cluster Resource Manager deve ottenere il nodo torna di sotto della capacità. Riduce il carico sul nodo che presenta una capacità in spostando una o più repliche o istanze di quel nodo ad altri nodi. 

Cluster Resource Manager prova a ridurre al minimo il costo dello spostamento di repliche. Per ulteriori informazioni sulle [costo dello spostamento](service-fabric-cluster-resource-manager-movement-cost.md) e circa [ribilanciamento strategie e le regole](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacità del cluster
In che modo Cluster Resource Manager di Service Fabric conservare il cluster complessivo dalla troppo piena? Con il carico dinamico, non esiste una grande quantità che può eseguire. I servizi possono avere picchi di carico indipendentemente dalle azioni che Cluster Resource Manager accetta. Di conseguenza, il cluster di molta capacità oggi stesso potrebbe essere ridotta se si verifica un picco in futuro. 

Controlli in Cluster Resource Manager consentono di prevenire i problemi. La prima cosa che è possibile eseguire è impedire la creazione di nuovi carichi di lavoro che riempirebbero il cluster risulta pieno.

Si supponga che si crea un servizio senza stato, e dispone di alcuni carichi associati. Il servizio si impegna la metrica "DiskSpaceInMb". Il servizio consumerà cinque unità di "DiskSpaceInMb" per ogni istanza del servizio. Si vogliono creare tre istanze del servizio. Pertanto, che è necessario che 15 unità di "DiskSpaceInMb" siano presenti nel cluster anche creare queste istanze del servizio.

Cluster Resource Manager calcola continuamente la capacità e l'utilizzo di ogni metrica in modo che sia possibile determinare la capacità residua nel cluster. Se non c'è spazio sufficiente, Cluster Resource Manager rifiuta la chiamata per creare un servizio.

Poiché il requisito è solo che 15 unità saranno disponibili, è possibile allocare questo spazio in molti modi diversi. Ad esempio, si potrebbe essere un'unità di capacità in 15 nodi diversi oppure tre unità di capacità in cinque diversi nodi rimanenti. Se Cluster Resource Manager possono sistemare le cose in modo che siano presenti cinque unità disponibili in tre nodi, viene inserito il servizio. In genere la riorganizzazione del cluster è possibile, a meno che il cluster non sia quasi pieno o che, per qualsiasi motivo, non sia possibile consolidare i servizi esistenti.

## <a name="buffered-capacity"></a>Capacità in buffering
La capacità in buffering è un'altra funzionalità di Cluster Resource Manager. che consente di riservare parti della capacità complessiva del nodo. Questo buffer di capacità viene usato solo per posizionare i servizi durante gli aggiornamenti e gli errori dei nodi. 

La capacità in buffering è specificata a livello globale per ogni metrica per tutti i nodi. Il valore scelto per la capacità riservata è una funzione del numero di domini di errore e di aggiornamento presenti nel cluster. Più domini di errore e di aggiornamento significa che è possibile scegliere un numero inferiore per il buffer di capacità. Se si dispone di molti domini, ci si può aspettare la mancata disponibilità di porzioni inferiori del cluster durante gli aggiornamenti e gli errori. Specificare capacità in buffering risulta utile solo se è stata specificata anche la capacità del nodo per una metrica.

Ecco un esempio di come specificare la capacità in buffering in Clustermanifest:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Di seguito è riportato un esempio di come specificare capacità in buffering mediante clusterconfig. JSON per le distribuzioni autonome o template. JSON per i cluster ospitati in Azure:

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

La creazione di nuovi servizi non riesce quando il cluster esaurisce il buffer di capacità per una metrica. Impedire la creazione di nuovi servizi per conservare il buffer garantisce che gli errori e gli aggiornamenti non causino il superamento della capacità dei nodi. La capacità in buffering è facoltativa, ma è consigliabile usarlo in qualsiasi cluster che definiscono una capacità per una metrica.

Cluster Resource Manager espone queste informazioni di carico. Per ogni metrica, le informazioni includono: 
- Le impostazioni di capacità in buffering.
- Capacità totale.
- Il consumo corrente.
- Indica se ogni metrica può essere considerata bilanciata oppure No.
- Statistiche relative alla deviazione standard.
- I nodi che presentano più e meno carico.  
  
Il codice seguente illustra un esempio dell'output:

```PowerShell
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
* Per informazioni sull'architettura e le informazioni sul flusso all'interno di Cluster Resource Manager, vedere [Cenni preliminari sull'architettura di Cluster Resource Manager](service-fabric-cluster-resource-manager-architecture.md).
* Definizione delle metriche di deframmentazione è un modo per consolidare il carico sui nodi anziché distribuirlo. Per informazioni su come configurare la deframmentazione, vedere [deframmentazione delle metriche e carico in Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Dall'inizio e [fornita un'introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Per informazioni su come Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere [bilanciamento del cluster di Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
