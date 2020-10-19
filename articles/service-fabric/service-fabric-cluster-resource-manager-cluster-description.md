---
title: Descrivere un cluster usando Gestione risorse cluster
description: Descrivere un cluster di Service Fabric specificando i domini di errore, i domini di aggiornamento, le proprietà del nodo e le capacità del nodo per Gestione risorse del cluster.
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 71629ebf1397c00face500f0bfd9c8e92deacc5e
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173052"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descrivere un cluster di Service Fabric usando cluster Gestione risorse

La funzionalità cluster Gestione risorse di Azure Service Fabric fornisce diversi meccanismi per la descrizione di un cluster:

* Domini di errore
* Domini di aggiornamento
* Proprietà del nodo
* Capacità del nodo

Durante il runtime, il cluster Gestione risorse usa queste informazioni per garantire la disponibilità elevata dei servizi in esecuzione nel cluster. Quando si applicano queste importanti regole, tenta anche di ottimizzare l'utilizzo delle risorse all'interno del cluster.

## <a name="fault-domains"></a>Domini di errore

Un dominio di errore (FD, Fault Domain) è un'area di errore coordinato. Un singolo computer è un dominio di errore. Il problema può avere esito negativo per diversi motivi, a causa di errori dell'alimentatore per evitare errori nel firmware NIC non valido.

I computer connessi allo stesso comswitch Ethernet si trovano nello stesso dominio di errore. Sono pertanto computer che condividono una singola fonte di alimentazione o in un'unica posizione.

Poiché è naturale per la sovrapposizione degli errori hardware, i domini di errore sono intrinsecamente gerarchici. Sono rappresentati come URI in Service Fabric.

È importante che i domini di errore siano configurati correttamente perché Service Fabric usa queste informazioni per collocare in modo sicuro i servizi. Service Fabric non desidera posizionare i servizi in modo che la perdita di un dominio di errore (causata dall'errore di alcuni componenti) provochi l'arresto di un servizio.

Nell'ambiente Azure Service Fabric usa le informazioni sul dominio di errore fornite dall'ambiente per configurare correttamente i nodi nel cluster per conto dell'utente. Per le istanze autonome di Service Fabric, i domini di errore vengono definiti al momento della configurazione del cluster.

> [!WARNING]
> È importante che le informazioni sul dominio di errore fornite per Service Fabric siano accurate. Si consideri, ad esempio, che i nodi del cluster Service Fabric sono in esecuzione in 10 macchine virtuali, in esecuzione su 5 host fisici. In questo caso, anche se sono presenti 10 macchine virtuali, sono presenti solo 5 domini di errore diversi (livello superiore). La condivisione dello stesso host fisico fa sì che le macchine virtuali condividano lo stesso dominio di errore radice, in quanto le VM riscontrano un errore coordinato se l'host fisico non riesce  
>
> Service Fabric prevede che il dominio di errore di un nodo non venga modificato. Altri meccanismi per garantire la disponibilità elevata delle macchine virtuali, ad esempio le [VM](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10))a disponibilità elevata, possono causare conflitti con Service Fabric. Questi meccanismi usano la migrazione trasparente delle macchine virtuali da un host a un altro. Non riconfigurano né inviano notifiche al codice in esecuzione all'interno della macchina virtuale. Di conseguenza, non sono *supportati* come ambienti per l'esecuzione di cluster Service Fabric. 
>
> Service Fabric deve essere l'unica tecnologia di disponibilità elevata in uso. Non sono necessari meccanismi come la migrazione in tempo reale delle macchine virtuali e i San. Se questi meccanismi vengono usati insieme ai Service Fabric, _riducono_ la disponibilità e l'affidabilità dell'applicazione. Il motivo è che introducono complessità aggiuntive, aggiungono origini centralizzate di errori e utilizzano strategie di affidabilità e disponibilità che sono in conflitto con quelle Service Fabric.
>
>

Nell'immagine seguente vengono colorate tutte le entità che contribuiscono ai domini di errore ed elencati tutti i diversi domini di errore risultanti. In questo esempio sono presenti data center ("DC"), rack ("R") e pannelli ("B"). Se ogni pannello contiene più di una macchina virtuale, potrebbe essere presente un altro livello nella gerarchia di domini di errore.

<center>
![Nodi organizzati tramite domini di errore][Image1]
</center>

Durante il runtime, Service Fabric cluster Gestione risorse considera i domini di errore nel cluster e i layout dei piani. Le repliche con stato o le istanze senza stato per un servizio vengono distribuite in modo che si trovino in domini di errore distinti. La distribuzione del servizio tra domini di errore garantisce che la disponibilità del servizio non venga compromessa quando un dominio di errore ha esito negativo a qualsiasi livello della gerarchia.

Il Gestione risorse del cluster non interessa il numero di livelli presenti nella gerarchia del dominio di errore. Tenta di garantire che la perdita di una parte della gerarchia non influisca sui servizi in esecuzione.

È preferibile che lo stesso numero di nodi si trovi a ogni livello di profondità nella gerarchia di domini di errore. Se il "albero" dei domini di errore è sbilanciato nel cluster, è più difficile per il cluster Gestione risorse individuare la migliore allocazione dei servizi. Il layout di un dominio di errore sbilanciato significa che la perdita di alcuni domini influiscono sulla disponibilità dei servizi più di altri domini. Di conseguenza, il Gestione risorse del cluster è diviso tra due obiettivi:

* Desidera utilizzare i computer nel dominio "pesante" inserendo i servizi su di essi. 
* Desidera inserire i servizi in altri domini in modo che la perdita di un dominio non causi problemi.

Come appaiono i domini sbilanciati? Nel diagramma seguente vengono illustrati due diversi layout del cluster. Nel primo esempio i nodi vengono distribuiti in modo uniforme tra i domini di errore. Nel secondo esempio, un dominio di errore ha molti più nodi rispetto agli altri domini di errore.

<center>
![Due layout diversi per i cluster][Image2]
</center>

In Azure la scelta del dominio di errore contenente un nodo viene gestita per l'utente. Tuttavia, a seconda del numero di nodi di cui si esegue il provisioning, è comunque possibile finire con domini di errore che contengono più nodi rispetto ad altri.

Si immagini ad esempio di avere cinque domini di errore nel cluster, ma di effettuare il provisioning di sette nodi per un tipo di nodo (**NodeType**). In questo caso, i primi due domini di errore finiscono con più nodi. Se si continua a distribuire più istanze di **NodeType** con solo un paio di istanze, il problema si aggrava. Per questo motivo, è consigliabile che il numero di nodi in ogni tipo di nodo sia un multiplo del numero di domini di errore.

## <a name="upgrade-domains"></a>Domini di aggiornamento

I domini di aggiornamento sono un'altra funzionalità che consente di Service Fabric cluster Gestione risorse comprendere il layout del cluster. I domini di aggiornamento definiscono i set di nodi che vengono aggiornati contemporaneamente. I domini di aggiornamento aiutano il cluster Gestione risorse comprendere e orchestrare le operazioni di gestione come gli aggiornamenti.

I domini di aggiornamento sono molto simili ai domini di errore, ma con due differenze principali. In primo luogo, le aree di errori hardware coordinati definiscono i domini di errore. I domini di aggiornamento, d'altra parte, sono definiti dai criteri. È necessario decidere il numero desiderato, anziché consentire all'ambiente di determinare il numero. È possibile avere tutti i domini di aggiornamento per i nodi. Un'altra differenza tra domini di errore e domini di aggiornamento è che i domini di aggiornamento non sono gerarchici. Sono invece più simili a un semplice tag.

Il diagramma seguente mostra tre domini di aggiornamento con striping in tre domini di errore. Viene inoltre mostrata una posizione possibile per tre repliche diverse di un servizio con stato, dove ciascuna di esse si trova in domini di errore e di aggiornamento diversi. Questa selezione host consente la perdita di un dominio di errore mentre è in corso un aggiornamento del servizio e dispone ancora di una copia del codice e dei dati.  

<center>
![Posizionamento con domini di errore e di aggiornamento][Image3]
</center>

Esistono vantaggi e svantaggi per avere un numero elevato di domini di aggiornamento. Più domini di aggiornamento indicano che ogni passaggio dell'aggiornamento è più granulare e influiscono su un numero minore di nodi o servizi. Un minor numero di servizi deve spostarsi alla volta, introducendo meno varianza nel sistema. Ciò tende a migliorare l'affidabilità, perché il servizio è influenzato da eventuali problemi introdotti durante l'aggiornamento. Un maggior numero di domini di aggiornamento significa anche che è necessario un buffer meno disponibile negli altri nodi per gestire l'effetto dell'aggiornamento.

Se, ad esempio, si dispone di cinque domini di aggiornamento, i nodi in ognuna gestiscono approssimativamente il 20% del traffico. Se è necessario arrestare il dominio di aggiornamento per un aggiornamento, il carico di solito deve andare da qualche parte. Poiché si dispone di quattro domini di aggiornamento rimanenti, ognuno deve disporre di spazio per circa il 25% del traffico totale. Più domini di aggiornamento significano che è necessario meno buffer nei nodi del cluster.

Prendere in considerazione la presenza di 10 domini di aggiornamento. In tal caso, ogni dominio di aggiornamento gestisce solo il 10% del traffico totale. Quando si esegue un aggiornamento per il cluster, è necessario che ogni dominio disponga di spazio solo sull'11% del traffico totale. Più domini di aggiornamento consentono in genere di eseguire i nodi a un utilizzo più elevato, perché è necessaria una capacità meno riservata. Lo stesso vale per i domini di errore.  

Il lato negativo della presenza di molti domini di aggiornamento consiste nel fatto che gli aggiornamenti tendono a richiedere più tempo. Service Fabric attende un breve periodo di tempo dopo il completamento di un dominio di aggiornamento ed esegue i controlli prima di avviare l'aggiornamento di quello successivo. Questi ritardi consentono di rilevare i problemi introdotti in seguito all'aggiornamento prima che l'aggiornamento continui. Questo compromesso è accettabile perché evita che modifiche non valide abbiano un impatto eccessivo sul servizio in un determinato momento.

La presenza di un numero troppo elevato di domini di aggiornamento presenta molti effetti collaterali negativi. Mentre ogni dominio di aggiornamento è inattivo e aggiornato, una parte elevata della capacità complessiva non è disponibile. Se, ad esempio, sono presenti solo tre domini di aggiornamento, si sta disattivando circa un terzo della capacità complessiva del servizio o del cluster alla volta. La maggior parte del servizio in una sola volta non è auspicabile perché è necessaria una capacità sufficiente nel resto del cluster per gestire il carico di lavoro. Il mantenimento di tale buffer significa che durante il normale funzionamento, i nodi sono meno caricati rispetto a quelli che sarebbero altrimenti. Questo approccio implica un aumento del costo di esecuzione del servizio.

Non è previsto alcun limite effettivo per il numero totale di domini di errore o di aggiornamento in un ambiente e non sono previsti vincoli per le relative sovrapposizioni. Esistono tuttavia modelli comuni:

* Domini di errore e domini di aggiornamento con mapping 1:1
* Un dominio di aggiornamento per nodo (istanza fisica o virtuale del sistema operativo)
* Modello "con striping" o "matrice" in cui i domini di errore e i domini di aggiornamento formano una matrice con computer che in genere eseguono le diagonali

<center>
![Layout dei domini di errore e di aggiornamento][Image4]
</center>

Non esiste una risposta ottimale per il layout da scegliere. Ognuna presenta vantaggi e svantaggi. Ad esempio, il modello di tipo 1FD:1UD è semplice da configurare. Il modello di un dominio di aggiornamento per ogni modello di nodo è più simile a quello usato dagli utenti. Durante gli aggiornamenti, ogni nodo viene aggiornato in modo indipendente. È simile alla modalità con la quale un piccolo gruppo di computer veniva aggiornato manualmente in passato.

Il modello più comune è la matrice FD/UD, in cui i domini di errore e i domini di aggiornamento formano una tabella e i nodi vengono posizionati a partire dalla diagonale. È il modello usato per impostazione predefinita nei cluster di Service Fabric in Azure. Per i cluster con molti nodi, tutto finisce come un modello di matrice dense.

> [!NOTE]
> Service Fabric cluster ospitati in Azure non supportano la modifica della strategia predefinita. Solo i cluster autonomi offrono tale personalizzazione.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Vincoli del dominio di errore e di aggiornamento e comportamento risultante
### <a name="default-approach"></a>Approccio predefinito

Per impostazione predefinita, il cluster Gestione risorse mantiene i servizi bilanciati tra domini di errore e di aggiornamento. Tale equilibrio è modellato come un [vincolo](service-fabric-cluster-resource-manager-management-integration.md). Il vincolo per i domini di errore e di aggiornamento dichiara: "per una data partizione del servizio, non dovrebbe esistere una differenza maggiore di uno nel numero di oggetti servizio (istanze del servizio senza stato o repliche del servizio con stato) tra due domini nello stesso livello di gerarchia".

Supponiamo che questo vincolo fornisca una garanzia di "differenza massima". Il vincolo per i domini di errore e di aggiornamento impedisce determinati spostamenti o disposizioni che violano la regola.

Ad esempio, supponiamo di avere un cluster con sei nodi, configurato con cinque domini di errore e cinque domini di aggiornamento.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

A questo punto è possibile creare un servizio con un valore di **TargetReplicaSetSize** (o, per un servizio senza stato, **InstanceCount**) pari a cinque. Le repliche avvengono in N1 N5. N6 non verrà mai usato indipendentemente dal numero di servizi simili creati. Ma perché? Esaminiamo la differenza tra il layout corrente e ciò che accadrebbe se si scegliesse N6.

Ecco il layout ottenuto e il numero totale di repliche per ogni dominio di errore e di aggiornamento:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Questo layout è bilanciato in termini di nodi per dominio di errore e dominio di aggiornamento. Viene anche bilanciato in termini di numero di repliche per dominio di errore e di aggiornamento. Ogni dominio ha lo stesso numero di nodi e lo stesso numero di repliche.

A questo punto vediamo cosa accadrebbe se usassimo N6 invece di N2. Come sarebbero state distribuite le repliche?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Questo layout viola la definizione della garanzia di "differenza massima" per il vincolo di dominio di errore. FD0 dispone di due repliche, mentre FD1 è zero. La differenza tra FD0 e FD1 è un totale di due, che è maggiore della differenza massima di uno. Poiché il vincolo viene violato, il cluster Gestione risorse non consente questa disposizione.

Analogamente, se sono state selezionate N2 e N6 (anziché N1 e N2), si otterrebbe:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Questo layout è bilanciato in termini di domini di errore. Ma ora sta violando il vincolo di dominio di aggiornamento, perché UD0 ha zero repliche e UD1 ne ha due. Questo layout non è valido e non viene prelevato dal cluster Gestione risorse.

Questo approccio alla distribuzione delle repliche con stato o delle istanze senza stato offre la migliore tolleranza di errore possibile. Se un dominio diventa inattivo, il numero minimo di repliche/istanze viene perso.

D'altro canto, questo approccio può essere troppo limitato e non consentire al cluster di usare tutte le risorse. Per determinate configurazioni del cluster non è possibile usare alcuni nodi. Questo può causare Service Fabric non collocare i servizi, ottenendo messaggi di avviso. Nell'esempio precedente, alcuni nodi del cluster non possono essere usati (N6 nell'esempio). Anche se sono stati aggiunti nodi al cluster (N7-N10), le repliche e le istanze verrebbero posizionate solo in N1 – N5 a causa di vincoli sui domini di errore e di aggiornamento.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>Approccio alternativo

Gestione risorse cluster supporta un'altra versione del vincolo per i domini di errore e di aggiornamento. Consente la selezione host garantendo comunque un livello minimo di sicurezza. Il vincolo alternativo può essere dichiarato come segue: "per una determinata partizione del servizio, la distribuzione delle repliche tra domini deve garantire che la partizione non subisca una perdita di quorum". Supponiamo che questo vincolo fornisca una garanzia di "sicurezza del quorum".

> [!NOTE]
> In un servizio con stato si parla di *perdita del quorum* in una situazione in cui la maggioranza delle repliche di partizione è contemporaneamente inattiva. Se, ad esempio, **TargetReplicaSetSize** è cinque, un set di tre repliche rappresenta il quorum. Analogamente, se **TargetReplicaSetSize** è sei, sono necessarie quattro repliche per il quorum. In entrambi i casi, non più di due repliche possono essere inattive contemporaneamente se la partizione vuole continuare a funzionare normalmente.
>
> Per un servizio senza stato, non c'è nulla di simile alla *perdita del quorum*. I servizi senza stato continuano a funzionare normalmente anche se la maggior parte delle istanze si arresta nello stesso momento. Quindi, ci concentreremo sui servizi con stato nella parte restante di questo articolo.
>

Tornare all'esempio precedente. Con la versione "quorum safe" del vincolo, tutti e tre i layout sarebbero validi. Anche se FD0 non è riuscito nel secondo layout o UD1 ha avuto esito negativo nel terzo layout, la partizione avrebbe ancora quorum. La maggior parte delle repliche sarebbe ancora attiva. Con questa versione del vincolo, N6 può quasi sempre essere utilizzato.

L'approccio "quorum safe" offre una maggiore flessibilità rispetto all'approccio "differenza massima". Il motivo è che è più semplice trovare le distribuzioni di replica valide in quasi tutte le topologie di cluster. Tuttavia, questo approccio non garantisce le caratteristiche di tolleranza di errore migliori perché alcuni errori sono più gravi di altri.

Nel peggiore dei casi, la maggior parte delle repliche può andare persa con l'errore di un dominio e una replica aggiuntiva. Ad esempio, anziché tre errori richiesti per perdere il quorum con cinque repliche o istanze, è ora possibile perdere una maggioranza con solo due errori.

### <a name="adaptive-approach"></a>Approccio adattivo

Poiché entrambi gli approcci hanno punti di forza e debolezza, abbiamo introdotto un approccio adattivo che combina queste due strategie.

> [!NOTE]
> Questo è il comportamento predefinito a partire dalla versione di Service Fabric 6,2.
>
> L'approccio adattivo usa la logica della "differenza massima" per impostazione predefinita, per passare alla logica di "sicurezza del quorum" solo quando necessario. Il Gestione risorse del cluster rileva automaticamente la strategia necessaria esaminando la configurazione del cluster e dei servizi.
>
> Il Gestione risorse cluster deve usare la logica "basata sul quorum" per un servizio entrambe le condizioni sono vere:
>
> * **TargetReplicaSetSize** per il servizio è equamente divisibile per il numero di domini di errore e per il numero di domini di aggiornamento.
> * Il numero di nodi è minore o uguale al numero di domini di errore moltiplicato per il numero di domini di aggiornamento.
>
> Tenere presente che il Gestione risorse del cluster utilizzerà questo approccio per i servizi con e senza stato, anche se la perdita del quorum non è pertinente per i servizi senza stato.

Torniamo all'esempio precedente e presupponga che un cluster ora disponga di otto nodi. Il cluster è ancora configurato con cinque domini di errore e cinque domini di aggiornamento e il valore **TargetReplicaSetSize** di un servizio ospitato nel cluster rimane cinque.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Poiché tutte le condizioni necessarie sono soddisfatte, il Gestione risorse cluster utilizzerà la logica "basata sul quorum" per la distribuzione del servizio. Questo consente l'uso di N6-N8. Una possibile distribuzione del servizio in questo caso potrebbe essere simile alla seguente:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se il valore **TargetReplicaSetSize** del servizio è ridotto a quattro (ad esempio), il Gestione risorse del cluster noterà che la modifica. Riprenderà usando la logica di "differenza massima" perché **TargetReplicaSetSize** non è più divisibile dal numero di domini di errore e domini di aggiornamento. Di conseguenza, si verificheranno alcuni movimenti di replica per distribuire le quattro repliche rimanenti nei nodi N1-N5. In questo modo, la versione "differenza massima" della logica del dominio di errore e del dominio di aggiornamento non viene violata.

Nel layout precedente, se il valore **TargetReplicaSetSize** è cinque e N1 viene rimosso dal cluster, il numero di domini di aggiornamento diventa pari a quattro. Anche in questo caso, Gestione risorse cluster inizia a usare la logica di "differenza massima" perché il numero di domini di aggiornamento non divide più il valore **TargetReplicaSetSize** del servizio. Di conseguenza, la replica R1, quando viene ricompilata, deve atterrare su N4, in modo che il vincolo per il dominio di errore e di aggiornamento non venga violato.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurazione di domini di errore e di aggiornamento

Nelle distribuzioni di Service Fabric ospitate da Azure, i domini di errore e i domini di aggiornamento vengono definiti automaticamente. Service Fabric recupera semplicemente le informazioni sull'ambiente da Azure e le usa.

Se si sta creando un cluster (o si vuole eseguire una particolare topologia nello sviluppo), è possibile fornire le informazioni sul dominio di errore e sul dominio di aggiornamento manualmente. In questo esempio viene definito un cluster di sviluppo locale a nove nodi che si estende su tre Data Center (ognuno con tre rack). Questo cluster dispone anche di tre domini di aggiornamento con striping tra questi tre Data Center. Di seguito è riportato un esempio di configurazione in ClusterManifest.xml:

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

Questo esempio USA ClusterConfig.json per le distribuzioni autonome:

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
> Quando si definiscono i cluster tramite Azure Resource Manager, Azure assegna domini di errore e domini di aggiornamento. Quindi, la definizione dei tipi di nodo e dei set di scalabilità di macchine virtuali nel modello di Azure Resource Manager non include informazioni sul dominio di errore o sul dominio di aggiornamento.
>

## <a name="node-properties-and-placement-constraints"></a>Proprietà dei nodi e vincoli di posizionamento

In alcuni casi (in realtà, la maggior parte del tempo) è opportuno assicurarsi che determinati carichi di lavoro vengano eseguiti solo su determinati tipi di nodi nel cluster. Ad esempio, alcuni carichi di lavoro potrebbero richiedere GPU o SSD e altri no.

Un ottimo esempio di destinazione dell'hardware per determinati carichi di lavoro è quasi ogni architettura a più livelli. Alcuni computer fungono da front-end o lato API dell'applicazione e vengono esposti ai client o a Internet. Altri computer, spesso con risorse hardware diverse, gestiscono il lavoro dei livelli di calcolo o archiviazione. In genere _non_ sono esposti direttamente a Internet o ai client.

Service Fabric prevede che in alcuni casi, potrebbe essere necessario eseguire determinati carichi di lavoro in particolari configurazioni hardware. Ad esempio:

* Un'applicazione a più livelli esistente è stata "sollevata e spostata" in un ambiente Service Fabric.
* Un carico di lavoro deve essere eseguito su hardware specifico per motivi di prestazioni, scalabilità o isolamento della sicurezza.
* Un carico di lavoro deve essere isolato da altri carichi di lavoro per motivi di utilizzo di criteri o risorse.

Per supportare questi tipi di configurazioni, Service Fabric include tag che è possibile applicare ai nodi. Questi tag vengono chiamati *proprietà del nodo*. I *vincoli di posizionamento* sono le istruzioni collegate ai singoli servizi selezionati per una o più proprietà del nodo. Definiscono la posizione in cui i servizi devono essere eseguiti. Il set di vincoli è estensibile. Qualsiasi coppia chiave/valore può funzionare.

<center>
![Carichi di lavoro diversi per un layout cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Proprietà predefinite del nodo

Service Fabric definisce alcune proprietà predefinite del nodo che possono essere usate automaticamente, pertanto non è necessario definirle. Le proprietà predefinite definite in ogni nodo sono **NodeType** e **NodeName**.

Ad esempio, è possibile scrivere un vincolo di posizionamento come `"(NodeType == NodeType03)"` . **NodeType** è una proprietà di uso comune. È utile perché corrisponde a 1:1 con un tipo di computer. Ogni tipo di computer corrisponde a un tipo di carico di lavoro in un'applicazione tradizionale a più livelli.

<center>
![Vincoli di posizionamento e proprietà del nodo][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Vincoli di posizionamento e sintassi delle proprietà del nodo

Il valore specificato nella proprietà del nodo può essere una stringa, un valore booleano o una lunghezza con segno. L'istruzione nel servizio è denominata *vincolo* di posizionamento perché vincola la posizione in cui il servizio può essere eseguito nel cluster. Il vincolo può essere qualsiasi istruzione booleana che agisce sulle proprietà del nodo nel cluster. I selettori validi in queste istruzioni booleane sono:

* Controlli condizionali per la creazione di istruzioni specifiche:

  | Istruzione | Sintassi |
  | --- |:---:|
  | "uguale a" | "==" |
  | "diverso da" | "!=" |
  | "maggiore di" | ">" |
  | "maggiore o uguale a" | ">=" |
  | "minore di" | "<" |
  | "minore o uguale a" | "<=" |

* Istruzioni booleane per il raggruppamento e le operazioni logiche:

  | Istruzione | Sintassi |
  | --- |:---:|
  | "and" | "&&" |
  | "or" | "&#124;&#124;" |
  | "not" | "!" |
  | "raggruppamento come singola istruzione" | "()" |

Di seguito sono riportati alcuni esempi di istruzioni di vincolo di base:

* `"Value >= 5"`
* `"NodeColor != green"`
* `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

È possibile posizionare il servizio solo sui nodi in cui l'istruzione del vincolo di posizionamento generale restituisce "True". I nodi che non dispongono di una proprietà definita non corrispondono ad alcun vincolo di posizionamento che contiene la proprietà.

Si direbbe che le proprietà del nodo seguenti sono state definite per un tipo di nodo in ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Nell'esempio seguente vengono illustrate le proprietà dei nodi definite tramite ClusterConfig.json per le distribuzioni autonome o Template.jssu per i cluster ospitati da Azure.

> [!NOTE]
> Nel modello di Azure Resource Manager, il tipo di nodo viene in genere parametrizzato. Sembrerebbe `"[parameters('vmNodeType1Name')]"` piuttosto che NodeType01.
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

È possibile creare *vincoli* di posizionamento del servizio per un servizio come indicato di seguito:

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

Se tutti i nodi di NodeType01 sono validi, è anche possibile selezionare il tipo di nodo con il vincolo `"(NodeType == NodeType01)"` .

I vincoli di posizionamento di un servizio possono essere aggiornati dinamicamente in fase di esecuzione. Se necessario, è possibile spostare un servizio in tutto il cluster, aggiungere e rimuovere i requisiti e così via. Service Fabric garantisce che il servizio rimanga attivo e disponibile anche quando vengono apportati questi tipi di modifiche.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

I vincoli di posizionamento vengono specificati per ogni istanza del servizio denominata. Gli aggiornamenti sostituiscono (sovrascrivono) sempre quanto specificato in precedenza.

La definizione del cluster definisce le proprietà in un nodo. Per modificare le proprietà di un nodo è necessario un aggiornamento alla configurazione del cluster. L'aggiornamento delle proprietà di un nodo richiede che ogni nodo in questione venga riavviato per segnalare le nuove proprietà. Service Fabric gestisce questi aggiornamenti in sequenza.

## <a name="describing-and-managing-cluster-resources"></a>Descrizione e gestione delle risorse cluster

Uno dei processi più importanti di un agente di orchestrazione consiste nel semplificare la gestione dell'utilizzo delle risorse del cluster. Gestione delle risorse del cluster può avere significati diversi.

Innanzitutto, è necessario verificare che i computer non siano sovraccarichi, ovvero che non eseguano più servizi di quanti ne possano gestire.

In secondo luogo, si verificano bilanciamento e ottimizzazione, essenziali per l'esecuzione efficiente dei servizi. Le offerte di servizi convenienti o sensibili alle prestazioni non possono consentire l'accesso ad alcuni nodi mentre altri sono a freddo. I nodi sensibili causano conflitti di risorse e prestazioni insoddisfacenti. I nodi a freddo rappresentano risorse sprecate e costi più elevati.

Service Fabric rappresenta le risorse come *metriche*. Per metrica si intende qualsiasi risorsa logica o fisica che deve essere descritta per Service Fabric, Esempi di metriche sono "WorkQueueDepth" o "MemoryInMb". Per informazioni sulle risorse fisiche che Service Fabric possono governare sui nodi, vedere [governance delle risorse](service-fabric-resource-governance.md). Per informazioni sulle metriche predefinite usate dal Gestione risorse del cluster e su come configurare le metriche personalizzate, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md).

Le metriche sono diverse dai vincoli di posizionamento e dalle proprietà dei nodi. Le proprietà dei nodi sono descrittori statici dei nodi stessi. Le metriche descrivono le risorse che i nodi hanno e i servizi utilizzano quando vengono eseguiti in un nodo. Una proprietà del nodo potrebbe essere **HasSSD** e potrebbe essere impostata su true o false. La quantità di spazio disponibile su tale unità SSD e la quantità di spazio utilizzata dai servizi sarebbero una metrica come "DriveSpaceInMb".

Proprio come per i vincoli di posizionamento e le proprietà dei nodi, Service Fabric cluster Gestione risorse non comprende il significato dei nomi delle metriche. I nomi delle metriche sono semplicemente stringhe. È consigliabile dichiarare le unità come parte dei nomi di metrica creati quando potrebbero essere ambigui.

## <a name="capacity"></a>Capacità

Se è stato disattivato tutto il *bilanciamento*delle risorse, Service Fabric cluster gestione risorse assicurerà comunque che nessun nodo superi la propria capacità. Gestire i sovraccarichi di capacità è possibile, a meno che il cluster non sia pieno o il carico di lavoro sia maggiore rispetto a qualsiasi altro nodo. La capacità è un altro *vincolo* che cluster gestione risorse USA per comprendere la quantità di risorse di un nodo. La capacità rimanente verrà registrata anche per il cluster nel suo complesso.

Sia la capacità che l'utilizzo a livello di servizio sono espressi in termini di metriche. Ad esempio, la metrica potrebbe essere "ClientConnections" e un nodo potrebbe avere una capacità per "ClientConnections" di 32.768. Altri nodi possono avere altri limiti. Un servizio in esecuzione su tale nodo può dire che sta attualmente utilizzando 32.256 della metrica "ClientConnections".

Durante il runtime, il cluster Gestione risorse tiene traccia della capacità rimanente nel cluster e nei nodi. Per tenere traccia della capacità, il cluster Gestione risorse sottrae l'utilizzo di ogni servizio dalla capacità di un nodo in cui viene eseguito il servizio. Con queste informazioni, cluster Gestione risorse possibile individuare dove posizionare o spostare le repliche in modo che i nodi non superino la capacità.

<center>
![Nodi e capacità del cluster][Image7]
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

È possibile visualizzare le capacità definite nel manifesto del cluster. Di seguito è riportato un esempio per ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Di seguito è riportato un esempio di capacità definite tramite ClusterConfig.json per le distribuzioni autonome o Template.jsper i cluster ospitati in Azure:

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

Il carico di un servizio cambia spesso in modo dinamico. Si dice che il carico di una replica di "ClientConnections" è stato modificato da 1.024 a 2.048. Il nodo in cui era in esecuzione aveva una capacità di soli 512 rimanenti per tale metrica. Ora che la posizione della replica o dell'istanza non è valida, perché non c'è spazio sufficiente su tale nodo. Il cluster Gestione risorse deve riportare il nodo sotto la capacità. Riduce il carico sul nodo che supera la capacità spostando una o più repliche o istanze da tale nodo ad altri nodi.

Il Gestione risorse del cluster tenta di ridurre al minimo i costi di trasferimento delle repliche. È possibile ottenere altre informazioni sui [costi di spostamento](service-fabric-cluster-resource-manager-movement-cost.md) e sul [ribilanciamento di strategie e regole](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacità del cluster

In che modo il cluster di Service Fabric Gestione risorse evitare che il cluster sia troppo pieno? Con il carico dinamico, non c'è molto da fare. I servizi possono avere il picco di carico indipendentemente dalle azioni accettate dal cluster Gestione risorse. Di conseguenza, il cluster con una grande quantità di spazio disponibile oggi potrebbe essere sottoalimentato se si verifica un picco domani.

I controlli del cluster Gestione risorse aiutano a prevenire i problemi. La prima cosa da fare è impedire la creazione di nuovi carichi di lavoro che potrebbero comportare la piena esecuzione del cluster.

Si immagini di creare un servizio senza stato a cui è associato un carico. Il servizio si occupa della metrica "DiskSpaceInMb". Il servizio utilizzerà cinque unità di "DiskSpaceInMb" per ogni istanza del servizio. Si vogliono creare tre istanze del servizio. Ciò significa che è necessario che siano presenti 15 unità di "DiskSpaceInMb" nel cluster per creare anche queste istanze del servizio.

Il Gestione risorse del cluster calcola continuamente la capacità e il consumo di ogni metrica, in modo che possa determinare la capacità rimanente nel cluster. Se lo spazio disponibile non è sufficiente, il cluster Gestione risorse rifiuta la chiamata per creare un servizio.

Poiché il requisito è solo che saranno disponibili 15 unità, è possibile allocare questo spazio in molti modi diversi. Ad esempio, potrebbe essere presente un'unità di capacità rimanente su 15 nodi diversi o tre unità di capacità rimanenti su cinque nodi diversi. Se Gestione risorse del cluster è in grado di riorganizzare gli elementi in modo che siano disponibili cinque unità in tre nodi, il servizio viene inserito. In genere la riorganizzazione del cluster è possibile, a meno che il cluster non sia quasi pieno o che, per qualsiasi motivo, non sia possibile consolidare i servizi esistenti.

## <a name="node-buffer-and-overbooking-capacity"></a>Buffer dei nodi e capacità di sovrarubrica

Se viene specificata una capacità del nodo per una metrica, il cluster Gestione risorse non inserisce né sposta le repliche in un nodo se il carico totale supera la capacità del nodo specificata. Questo può talvolta impedire il posizionamento di nuove repliche o la sostituzione di repliche non riuscite se il cluster è quasi pieno e una replica con un carico elevato deve essere posizionata, sostituita o spostata.

Per garantire una maggiore flessibilità, è possibile specificare il buffer del nodo o la capacità di sovrarubrica. Quando per una metrica viene specificato il buffer del nodo o la capacità di sovrarubrica, il Gestione risorse del cluster tenterà di posizionare o spostare le repliche in modo tale che la capacità del buffer o della sovrarubrica rimanga inutilizzata, ma consente l'utilizzo della capacità del buffer o della sovrarubrica se necessario per le azioni che aumentano la disponibilità del servizio, ad esempio:

* Nuovo posizionamento delle repliche o sostituzione delle repliche non riuscite
* Selezione host durante gli aggiornamenti
* Correzione di violazioni di vincoli soft e hard
* Deframmentazione

La capacità del buffer dei nodi rappresenta una parte riservata della capacità al di sotto della capacità del nodo specificata e la capacità di sovrarubrica rappresenta una parte della capacità aggiuntiva superiore alla capacità del nodo specificata. In entrambi i casi, il Gestione risorse del cluster tenterà di rendere disponibile la capacità.

Se, ad esempio, un nodo ha una capacità specificata per la metrica *CpuUtilization* di 100 e la percentuale di buffer del nodo per tale metrica è impostata sul 20%, le capacità totale e senza buffer saranno rispettivamente 100 e 80 e il cluster gestione risorse non distribuirà più di 80 unità di carico nel nodo durante le circostanze normali.

![Capacità totale uguale alla capacità del nodo (buffer del nodo + non memorizzato nel buffer)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

Il buffer del nodo deve essere usato quando si vuole riservare una parte della capacità del nodo che verrà usata solo per le azioni che aumentano la disponibilità del servizio citata in precedenza.

D'altra parte, se si usa la percentuale di sovrarubrica del nodo e si imposta il 20%, le capacità totali e senza buffer saranno rispettivamente 120 e 100.

![Capacità totale uguale alla capacità di sovrarubrica più capacità del nodo (overbooking + senza buffer)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

È consigliabile usare la capacità di sovrarubrica quando si vuole consentire al cluster Gestione risorse di inserire le repliche in un nodo, anche se l'utilizzo totale delle risorse supera la capacità. Questa operazione può essere utilizzata per fornire disponibilità aggiuntiva per i servizi a scapito delle prestazioni. Se si utilizza l'overbooking, la logica dell'applicazione utente deve essere in grado di funzionare con un minor numero di risorse fisiche rispetto a quanto potrebbe richiedere.

Se vengono specificate le capacità del buffer del nodo o della sovrarubrica, il Gestione risorse cluster non sposterà o inserirà le repliche se il carico totale nel nodo di destinazione supera la capacità totale (capacità del nodo nel caso del buffer del nodo e della capacità del nodo + sovrascrivendo la capacità in caso di sovraprenotazione).

La capacità di sovrarubrica può essere specificata anche come infinita. In questo caso, il Gestione risorse del cluster tenterà di evitare il carico totale sul nodo al di sotto della capacità del nodo specificata, ma è consentito potenzialmente inserire un carico molto maggiore sul nodo, causando un grave calo delle prestazioni.

Per una metrica non è possibile specificare contemporaneamente il buffer del nodo e la capacità di sovrarubrica.

Di seguito è riportato un esempio di come specificare il buffer del nodo o le capacità di sovrarubrica in *ClusterManifest.xml*:

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

Di seguito è riportato un esempio di come specificare il buffer del nodo o le capacità di overbooking tramite *ClusterConfig.json* per le distribuzioni autonome o *Template.js* per i cluster ospitati da Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'architettura e sul flusso di informazioni all'interno di Gestione risorse cluster, vedere [Cenni preliminari sull'architettura gestione risorse cluster](service-fabric-cluster-resource-manager-architecture.md).
* La definizione delle metriche di deframmentazione è un modo per consolidare il carico sui nodi anziché distribuirlo. Per informazioni su come configurare la deframmentazione, vedere [deframmentazione delle metriche e carico in Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Iniziare dall'inizio e [ottenere un'introduzione a Service Fabric Cluster gestione risorse](service-fabric-cluster-resource-manager-introduction.md).
* Per informazioni sul modo in cui il cluster Gestione risorse gestisce e bilancia il carico nel cluster, vedere [bilanciamento del cluster di Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
