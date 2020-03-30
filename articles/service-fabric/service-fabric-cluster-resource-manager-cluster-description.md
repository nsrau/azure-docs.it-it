---
title: Descrivere un cluster usando Gestione risorse cluster
description: Descrivere un cluster di Service Fabric specificando domini di errore, domini di aggiornamento, proprietà del nodo e capacità dei nodi per Gestione risorse cluster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258772"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descrivere un cluster di Service Fabric tramite Gestione risorse clusterDescribe a Service Fabric cluster by using Cluster Resource Manager
La funzionalità Gestione risorse cluster di Azure Service Fabric offre diversi meccanismi per la descrizione di un cluster:The Cluster Resource Manager feature of Azure Service Fabric provides several mechanisms for describing a cluster:

* Domini di errore
* Domini di aggiornamento
* Proprietà del nodo
* Capacità del nodo

Durante il runtime, Gestione risorse cluster utilizza queste informazioni per garantire la disponibilità elevata dei servizi in esecuzione nel cluster. Durante l'applicazione di queste regole importanti, tenta anche di ottimizzare l'utilizzo delle risorse all'interno del cluster.

## <a name="fault-domains"></a>Domini di errore
Un dominio di errore (FD, Fault Domain) è un'area di errore coordinato. Un singolo computer è un dominio di errore. Può fallire da solo per vari motivi, da guasti dell'alimentazione per guidare i guasti al firmware NIC difettoso. 

Le macchine connesse allo stesso switch Ethernet si trovano nello stesso dominio di errore. Così sono le macchine che condividono una singola fonte di energia o in un'unica posizione. 

Poiché è naturale che gli errori hardware si sovrappongano, i domini di errore sono intrinsecamente gerarchici. Sono rappresentati come URI in Service Fabric.

È importante che i domini di errore siano impostati correttamente perché Service Fabric usa queste informazioni per posizionare i servizi in modo sicuro. Service Fabric non vuole posizionare servizi in modo che la perdita di un dominio di errore (causata dall'errore di alcuni componenti) provochi l'arresto di un servizio. 

Nell'ambiente Azure Service Fabric usa le informazioni sul dominio di errore fornite dall'ambiente per configurare correttamente i nodi nel cluster per conto dell'utente. Per le istanze autonome di Service Fabric, i domini di errore vengono definiti al momento della configurazione del cluster. 

> [!WARNING]
> È importante che le informazioni sul dominio di errore fornite a Service Fabric siano accurate. Si supponga, ad esempio, che i nodi del cluster Di Service Fabric siano in esecuzione all'interno di 10 macchine virtuali, in esecuzione su 5 host fisici. In questo caso, anche se sono presenti 10 macchine virtuali, sono presenti solo 5 domini di errore diversi (livello superiore). La condivisione dello stesso host fisico fa sì che le macchine virtuali condividano lo stesso dominio di errore radice, perché le macchine virtuali riscontrano un errore coordinato in caso di errore dell'host fisico.  
>
> Service Fabric prevede che il dominio di errore di un nodo non venga modificato. Altri meccanismi per garantire la disponibilità elevata delle macchine virtuali, ad esempio [LE macchine virtuali HA,](https://technet.microsoft.com/library/cc967323.aspx)potrebbero causare conflitti con Service Fabric. Questi meccanismi usano la migrazione trasparente delle macchine virtuali da un host a un altro. Non riconfigurano né notificano il codice in esecuzione all'interno della macchina virtuale. Di conseguenza, *non* sono supportati come ambienti per l'esecuzione di cluster di Service Fabric.As such, they't supported as environments for running Service Fabric clusters. 
>
> Service Fabric deve essere l'unica tecnologia di disponibilità elevata in uso. Meccanismi come la migrazione di macchine virtuali in tempo reale e reti SAN non sono necessari. Se questi meccanismi vengono utilizzati insieme a Service Fabric, _riducono_ la disponibilità e l'affidabilità dell'applicazione. Il motivo è che introducono ulteriore complessità, aggiungono origini centralizzate di errore e usano strategie di affidabilità e disponibilità in conflitto con quelle di Service Fabric. 
>
>

Nel grafico seguente, coloriamo tutte le entità che contribuiscono ai domini di errore ed elenchiamo tutti i diversi domini di errore risultanti. In questo esempio sono presenti data center ("DC"), rack ("R") e pannelli ("B"). Se ogni blade contiene più di una macchina virtuale, potrebbe essere presente un altro livello nella gerarchia del dominio di errore.

<center>

![Nodi organizzati tramite domini di errore][Image1]
</center>

Durante il runtime, Service Fabric Cluster Resource Manager considera i domini di errore nel cluster e pianifica i layout. Le repliche con stato o istanze senza stato per un servizio vengono distribuite in modo che si trovino in domini di errore separati. La distribuzione del servizio tra domini di errore garantisce che la disponibilità del servizio non venga compromessa quando un dominio di errore non riesce a qualsiasi livello della gerarchia.

Gestione risorse cluster non interessa il numero di livelli presenti nella gerarchia dei domini di errore. Tenta di garantire che la perdita di una parte della gerarchia non influisca sui servizi in esecuzione in essa. 

È consigliabile se lo stesso numero di nodi è a ogni livello di profondità nella gerarchia del dominio di errore. Se l'"albero" dei domini di errore non è bilanciato nel cluster, è più difficile per Gestione risorse cluster individuare la migliore allocazione dei servizi. Layout di dominio di errore sbilanciati significano che la perdita di alcuni domini influisce sulla disponibilità di servizi più di altri domini. Di conseguenza, Gestione risorse cluster è diviso tra due obiettivi:As a result, Cluster Resource Manager is distorn; 

* Si desidera utilizzare le macchine in quel dominio "pesante" mettendo servizi su di loro. 
* Desidera inserire i servizi in altri domini in modo che la perdita di un dominio non causi problemi. 

Come appaiono i domini sbilanciati? Il diagramma seguente mostra due diversi layout del cluster. Nel primo esempio, i nodi vengono distribuiti in modo uniforme tra i domini di errore. Nel secondo esempio, un dominio di errore ha molti più nodi rispetto agli altri domini di errore. 

<center>

![Due diversi layout di cluster][Image2]
</center>

In Azure viene gestita la scelta del dominio di errore contenente un nodo. Tuttavia, a seconda del numero di nodi di cui si esegue il provisioning, è comunque possibile finire con domini di errore che contantono più nodi rispetto ad altri. 

Si supponga, ad esempio, di disporre di cinque domini di errore nel cluster, ma di eseguire il provisioning di sette nodi per un tipo di nodo (**NodeType**). In questo caso, i primi due domini di errore finiscono con più nodi. Se si continua a distribuire più **Istanze NodeType** con solo un paio di istanze, il problema peggiora. Per questo motivo, è consigliabile che il numero di nodi in ogni tipo di nodo sia un multiplo del numero di domini di errore.

## <a name="upgrade-domains"></a>Domini di aggiornamento
I domini di aggiornamento sono un'altra funzionalità che consente a Service Fabric Cluster Resource Manager di comprendere il layout del cluster. I domini di aggiornamento definiscono set di nodi che vengono aggiornati contemporaneamente. I domini di aggiornamento consentono a Gestione risorse cluster di comprendere e orchestrare le operazioni di gestione, ad esempio gli aggiornamenti.

I domini di aggiornamento sono molto simili ai domini di errore, ma con un paio di differenze fondamentali. In primo luogo, le aree di errori hardware coordinati definiscono i domini di errore. I domini di aggiornamento, d'altra parte, sono definiti dai criteri. Si arriva a decidere quanti si desidera, invece di lasciare che l'ambiente dettare il numero. È possibile avere tutti i domini di aggiornamento come si fa nodi. Un'altra differenza tra i domini di errore e i domini di aggiornamento è che i domini di aggiornamento non sono gerarchici. Invece, sono più simili a un semplice tag. 

Nel diagramma seguente vengono illustrati tre domini di aggiornamento con striping in tre domini di errore. Mostra inoltre una possibile posizione per tre diverse repliche di un servizio con stato, in cui ognuna finisce in domini di errore e di aggiornamento diversi. Questo posizionamento consente la perdita di un dominio di errore durante l'aggiornamento di un servizio e dispone ancora di una copia del codice e dei dati.  

<center>

![Posizionamento con domini di errore e di aggiornamento][Image3]
</center>

Ci sono pro e contro di avere un gran numero di domini di aggiornamento. Più domini di aggiornamento significano che ogni passaggio dell'aggiornamento è più granulare e influisce su un numero inferiore di nodi o servizi. Meno servizi devono muoversi alla volta, introducendo meno varianza nel sistema. Questo tende a migliorare l'affidabilità, perché meno del servizio è interessato da qualsiasi problema introdotto durante l'aggiornamento. Più domini di aggiornamento significano anche che è necessario meno buffer disponibile su altri nodi per gestire l'impatto dell'aggiornamento. 

Ad esempio, se si dispone di cinque domini di aggiornamento, i nodi in ognuno gestiscono circa il 20% del traffico. Se è necessario arrestare il dominio di aggiornamento per un aggiornamento, il carico in genere deve andare da qualche parte. Poiché si dispone di quattro domini di aggiornamento rimanenti, ognuno deve avere spazio per circa il 5% del traffico totale. Più domini di aggiornamento significano che è necessario meno buffer sui nodi del cluster. 

Valutare se si avevano 10 domini di aggiornamento. In tal caso, ogni dominio di aggiornamento gestirà solo circa il 10% del traffico totale. Quando un aggiornamento passa attraverso il cluster, ogni dominio deve avere spazio solo per circa l'1,1% del traffico totale. Più domini di aggiornamento consentono in genere di eseguire i nodi con un utilizzo più elevato, perché è necessaria una capacità meno riservata. Lo stesso vale per i domini di errore.  

Lo svantaggio di avere molti domini di aggiornamento è che gli aggiornamenti tendono a richiedere più tempo. Service Fabric attende un breve periodo dopo il completamento di un dominio di aggiornamento ed esegue i controlli prima di iniziare l'aggiornamento di quello successivo. Questi ritardi consentono di rilevare i problemi introdotti in seguito all'aggiornamento prima che l'aggiornamento continui. Questo compromesso è accettabile perché evita che modifiche non valide abbiano un impatto eccessivo sul servizio in un determinato momento.

La presenza di troppo pochi domini di aggiornamento ha molti effetti collaterali negativi. Mentre ogni dominio di aggiornamento è inattivo e viene aggiornato, gran parte della capacità complessiva non è disponibile. Ad esempio, se si dispone solo di tre domini di aggiornamento, si sta rimuovendo circa un terzo della capacità complessiva del servizio o del cluster alla volta. Avere così tanto del servizio inattivo in una sola volta non è auspicabile perché è necessaria capacità sufficiente nel resto del cluster per gestire il carico di lavoro. Mantenendo tale buffer significa che durante il normale funzionamento, tali nodi sono meno caricati di quanto sarebbero altrimenti. Questo approccio implica un aumento del costo di esecuzione del servizio.

Non è previsto alcun limite effettivo per il numero totale di domini di errore o di aggiornamento in un ambiente e non sono previsti vincoli per le relative sovrapposizioni. Ma ci sono modelli comuni:

- Domini di errore e domini di aggiornamento mappati 1:1
- Un dominio di aggiornamento per nodo (istanza fisica o virtuale del sistema operativo)
- Un modello "a strisce" o "matrice" in cui i domini di errore e i domini di aggiornamento formano una matrice con macchine che in genere scorrono le diagonali

<center>

![Layout dei domini di errore e aggiornamento][Image4]
</center>

Non c'è una risposta migliore per quale layout scegliere. Ognuna presenta vantaggi e svantaggi. Ad esempio, il modello di tipo 1FD:1UD è semplice da configurare. Il modello di un dominio di aggiornamento per ogni modello di nodo è più simile a quello a cui gli utenti sono abituati. Durante gli aggiornamenti, ogni nodo viene aggiornato in modo indipendente. È simile alla modalità con la quale un piccolo gruppo di computer veniva aggiornato manualmente in passato.

Il modello più comune è la matrice FD/UD, in cui i domini di errore e i domini di aggiornamento formano una tabella e i nodi vengono posizionati a partire dalla diagonale. È il modello usato per impostazione predefinita nei cluster di Service Fabric in Azure. Per i cluster con molti nodi, tutto finisce per sembrare un modello a matrice densa.

> [!NOTE]
> I cluster di Service Fabric ospitati in Azure non supportano la modifica della strategia predefinita. Solo i cluster autonomi offrono tale personalizzazione.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Vincoli del dominio di errore e di aggiornamento e comportamento risultante
### <a name="default-approach"></a>Approccio predefinito
Per impostazione predefinita, Gestione risorse cluster mantiene i servizi bilanciati tra domini di errore e aggiornamento. Tale equilibrio è modellato come un [vincolo](service-fabric-cluster-resource-manager-management-integration.md). Il vincolo per i domini di errore e aggiornamento afferma: "Per una determinata partizione del servizio, non deve mai essere presente una differenza maggiore di uno nel numero di oggetti servizio (istanze del servizio senza stato o repliche del servizio con stato) tra due domini qualsiasi nello stesso livello di gerarchia.

Si supponga che questo vincolo fornisca una garanzia di "differenza massima". Il vincolo per i domini di errore e aggiornamento impedisce determinati spostamenti o disposizioni che violano la regola.

Si supponga, ad esempio, di avere un cluster con sei nodi, configurato con cinque domini di errore e cinque domini di aggiornamento.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (in uso)** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Si supponga ora di creare un servizio con un **TargetReplicaSetSize** (o, per un servizio senza stato, **InstanceCount**) valore di cinque. Le repliche avvengono in N1 N5. N6 non verrà mai usato indipendentemente dal numero di servizi simili creati. Ma perché? Esaminiamo la differenza tra il layout corrente e ciò che accadrebbe se si scegliesse N6.

Ecco il layout che abbiamo ottenuto e il numero totale di repliche per dominio di errore e aggiornamento:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (in uso)** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Questo layout è bilanciato in termini di nodi per dominio di errore e dominio di aggiornamento. È inoltre bilanciato in termini di numero di repliche per dominio di errore e aggiornamento. Ogni dominio ha lo stesso numero di nodi e lo stesso numero di repliche.

A questo punto vediamo cosa accadrebbe se usassimo N6 invece di N2. Come sarebbero state distribuite le repliche?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (in uso)** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Questo layout viola la definizione della garanzia di "differenza massima" per il vincolo di dominio di errore. FD0 ha due repliche, mentre FD1 ha zero. La differenza tra FD0 e FD1 è un totale di due, che è maggiore della differenza massima di uno. Poiché il vincolo viene violato, Gestione risorse cluster non consente questa disposizione. Allo stesso modo, se abbiamo scelto N2 e N6 (invece di N1 e N2), otterremmo:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (in uso)** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Questo layout è bilanciato in termini di domini di errore. Ma ora sta violando il vincolo di dominio di aggiornamento, perché UD0 ha zero repliche e UD1 ne ha due. Anche questo layout non è valido e non verrà selezionato da Gestione risorse cluster.

Questo approccio alla distribuzione delle repliche con stato o delle istanze senza stato offre la migliore tolleranza di errore possibile. Se un dominio si arresta, il numero minimo di repliche/istanze viene perso. 

D'altro canto, questo approccio può essere troppo limitato e non consentire al cluster di usare tutte le risorse. Per determinate configurazioni del cluster non è possibile usare alcuni nodi. In questo modo Service Fabric non inserirà i servizi, generando messaggi di avviso. Nell'esempio precedente, alcuni dei nodi del cluster non possono essere utilizzati (N6 nell'esempio). Anche se sono stati aggiunti nodi a tale cluster (N7-N10), le repliche/istanze verranno inserite solo in N1–N5 a causa di vincoli sui domini di errore e di aggiornamento. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (in uso)** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Approccio alternativo

Gestione risorse cluster supporta un'altra versione del vincolo per i domini di errore e di aggiornamento. Permette il posizionamento pur garantendo un livello minimo di sicurezza. Il vincolo alternativo può essere dichiarato come segue: "Per una determinata partizione del servizio, la distribuzione della replica tra domini deve garantire che la partizione non subisca una perdita del quorum". Si supponga che questo vincolo fornisca una garanzia di "quorum safe". 

> [!NOTE]
> In un servizio con stato si parla di *perdita del quorum* in una situazione in cui la maggioranza delle repliche di partizione è contemporaneamente inattiva. Ad esempio, se **TargetReplicaSetSize** è cinque, un set di tre repliche qualsiasi rappresenta il quorum. Analogamente, se **TargetReplicaSetSize** è sei, sono necessarie quattro repliche per il quorum. In entrambi i casi, non più di due repliche possono essere inattivi contemporaneamente se la partizione desidera continuare a funzionare normalmente. 
>
> Per un servizio senza stato, non esiste alcuna operazione come la *perdita*del quorum . I servizi senza stato continuano a funzionare normalmente anche se la maggior parte delle istanze si arresta contemporaneamente. Quindi, ci concentreremo sui servizi con stato nel resto di questo articolo.
>

Tornare all'esempio precedente. Con la versione "quorum safe" del vincolo, tutti e tre i layout sarebbero validi. Anche se FD0 non ha esito negativo nel secondo layout o UD1 non è riuscito nel terzo layout, la partizione avrà comunque quorum. (La maggior parte delle repliche sarebbe ancora attiva.) Con questa versione del vincolo, N6 può essere quasi sempre utilizzato.

L'approccio "quorum safe" offre una maggiore flessibilità rispetto all'approccio "differenza massima". Il motivo è che è più semplice trovare distribuzioni di repliche valide in quasi tutte le topologie di cluster. Tuttavia, questo approccio non garantisce le caratteristiche di tolleranza di errore migliori perché alcuni errori sono più gravi di altri. 

Nel peggiore dei casi, la maggior parte delle repliche può andare persa con l'errore di un dominio e di una replica aggiuntiva. Ad esempio, invece di tre errori necessari per perdere il quorum con cinque repliche o istanze, è ora possibile perdere la maggioranza con solo due errori. 

### <a name="adaptive-approach"></a>Approccio adattivo
Poiché entrambi gli approcci hanno punti di forza e di debolezza, abbiamo introdotto un approccio adattivo che combina queste due strategie.

> [!NOTE]
> Questo è il comportamento predefinito a partire da Service Fabric versione 6.2.This is the default behavior starting with Service Fabric version 6.2. 
> 
> L'approccio adattivo usa la logica della "differenza massima" per impostazione predefinita, per passare alla logica di "sicurezza del quorum" solo quando necessario. Gestione risorse cluster individua automaticamente la strategia necessaria esaminando la configurazione del cluster e dei servizi.
> 
> Gestione risorse cluster deve utilizzare la logica "basato su quorum" per un servizio in entrambe le condizioni seguenti:Cluster Resource Manager should use the "quorum based" logic for a service both of these conditions are true:
>
> * **TargetReplicaSetSize** per il servizio è divisibile in modo uniforme per il numero di domini di errore e il numero di domini di aggiornamento.
> * Il numero di nodi è minore o uguale al numero di domini di errore moltiplicato per il numero di domini di aggiornamento.
>
> Tenere presente che Gestione risorse cluster utilizzerà questo approccio sia per i servizi senza stato che per i servizi con stato, anche se la perdita del quorum non è rilevante per i servizi senza stato.

Torniamo all'esempio precedente e supponiamo che un cluster abbia ora otto nodi. Il cluster è ancora configurato con cinque domini di errore e cinque domini di aggiornamento e il valore **TargetReplicaSetSize** di un servizio ospitato in tale cluster rimane cinque. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (in uso)** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Poiché tutte le condizioni necessarie sono soddisfatte, Gestione risorse cluster utilizzerà la logica "basata sul quorum" nella distribuzione del servizio. Ciò consente l'utilizzo di N6-N8. Una possibile distribuzione del servizio in questo caso potrebbe essere simile alla seguente:One possible service distribution in this case might look like this:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (in uso)** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se il valore **TargetReplicaSetSize** del servizio viene ridotto a quattro (ad esempio), Gestione risorse cluster noterà tale modifica. Riprenderà usando la logica di "differenza massima" perché **TargetReplicaSetSize** non è più divitale in base al numero di domini di errore e domini di aggiornamento. Di conseguenza, si verificheranno alcuni movimenti di replica per distribuire le quattro repliche rimanenti nei nodi N1-N5. In questo modo, la versione "differenza massima" del dominio di errore e la logica del dominio di aggiornamento non viene violata. 

Nel layout precedente, se il valore **di TargetReplicaSetSize** è cinque e N1 viene rimosso dal cluster, il numero di domini di aggiornamento diventa uguale a quattro. Anche in questo caso, Gestione risorse cluster inizia a utilizzare la logica di "differenza massima" perché il numero di domini di aggiornamento non divide più in modo uniforme il valore **TargetReplicaSetSize** del servizio. Di conseguenza, la replica R1, quando viene creata di nuovo, deve atterrare su N4 in modo che il vincolo per il dominio di errore e aggiornamento non venga violato.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (in uso)** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurazione di domini di errore e di aggiornamento
Nelle distribuzioni di Azure ospitato di Service Fabric, i domini di errore e i domini di aggiornamento vengono definiti automaticamente. Service Fabric recupera semplicemente le informazioni sull'ambiente da Azure e le usa.

Se si sta creando il proprio cluster (o si desidera eseguire una topologia specifica in fase di sviluppo), è possibile fornire manualmente il dominio di errore e aggiornare le informazioni sul dominio. In questo esempio viene definito un cluster di sviluppo locale a nove nodi che si estende su tre data center (ciascuno con tre rack). Questo cluster dispone anche di tre domini di aggiornamento con striping in questi tre data center. Di seguito è riportato un esempio della configurazione in ClusterManifest.xml:Here's an example of the configuration in ClusterManifest.xml:

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

In questo esempio viene utilizzato ClusterConfig.json per le distribuzioni autonome:This example uses ClusterConfig.json for standalone deployments:

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
> Quando si definiscono i cluster tramite Azure Resource Manager, Azure assegna domini di errore e domini di aggiornamento. Pertanto, la definizione dei tipi di nodo e dei set di scalabilità delle macchine virtuali nel modello di Azure Resource Manager non include informazioni sul dominio di errore o sul dominio di aggiornamento.
>

## <a name="node-properties-and-placement-constraints"></a>Proprietà dei nodi e vincoli di posizionamento
A volte (in realtà, la maggior parte delle volte) è consigliabile assicurarsi che determinati carichi di lavoro vengano eseguiti solo su determinati tipi di nodi nel cluster. Ad esempio, alcuni carichi di lavoro potrebbero richiedere GPU o SSD, mentre altri potrebbero non essere eseguiti. 

Un ottimo esempio di destinazione dell'hardware a carichi di lavoro specifici è quasi ogni architettura a più livelli. Alcune macchine fungono da lato front-end o API dell'applicazione e sono esposte ai client o a Internet. Altri computer, spesso con risorse hardware diverse, gestiscono il lavoro dei livelli di calcolo o archiviazione. In genere _non_ sono esposti direttamente a Internet o ai client. 

Service Fabric prevede che in alcuni casi, determinati carichi di lavoro potrebbero dover essere eseguiti in configurazioni hardware specifiche. Ad esempio:

* Un'applicazione a più livelli esistente è stata "sollevata e spostata" in un ambiente Service Fabric.An existing n-tier application has been "lifted and shifted" into a Service Fabric environment.
* Un carico di lavoro deve essere eseguito su hardware specifico per motivi di prestazioni, scalabilità o isolamento della sicurezza.
* Un carico di lavoro deve essere isolato da altri carichi di lavoro per motivi di consumo di criteri o risorse.

Per supportare questi tipi di configurazioni, Service Fabric include tag che è possibile applicare ai nodi. Questi tag vengono chiamati *proprietà del nodo*. *I vincoli* di posizionamento sono le istruzioni associate ai singoli servizi selezionati per una o più proprietà del nodo. Definiscono la posizione in cui i servizi devono essere eseguiti. Il set di vincoli è estensibile. Qualsiasi coppia chiave/valore può funzionare. 

<center>

![Carichi di lavoro diversi per un layout cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Proprietà dei nodi predefinite
Service Fabric definisce alcune proprietà predefinite del nodo che possono essere usate automaticamente in modo che non sia necessario definirle. Le proprietà predefinite definite in ogni nodo sono **NodeType** e **NodeName**. 

Ad esempio, è possibile scrivere `"(NodeType == NodeType03)"`un vincolo di posizionamento come . **NodeType** è una proprietà di uso comune. È utile perché corrisponde a 1:1 con un tipo di macchina. Ogni tipo di computer corrisponde a un tipo di carico di lavoro in un'applicazione tradizionale a più livelli.

<center>

![Vincoli di posizionamento e proprietà dei nodi][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Vincoli di posizionamento e sintassi delle proprietà dei nodi 
Il valore specificato nella proprietà node può essere una stringa, booleano o lungo segno. L'istruzione nel servizio è denominata vincolo di *posizionamento* perché vincola la posizione in cui il servizio può essere eseguito nel cluster. Il vincolo può essere qualsiasi istruzione booleana che opera sulle proprietà del nodo nel cluster. I selettori validi in queste istruzioni booleane sono:

* Controlli condizionali per la creazione di istruzioni particolari:Conditional checks for creating particular statements:

  | . | Sintassi |
  | --- |:---:|
  | "uguale a" | "==" |
  | "diverso da" | "!=" |
  | "maggiore di" | ">" |
  | "maggiore o uguale a" | ">=" |
  | "minore di" | "<" |
  | "minore o uguale a" | "<=" |

* Istruzioni booleane per il raggruppamento e le operazioni logiche:

  | . | Sintassi |
  | --- |:---:|
  | "and" | "&&" |
  | "or" | "&#124;&#124;" |
  | "not" | "!" |
  | "raggruppamento come singola istruzione" | "()" |

Di seguito sono riportati alcuni esempi di istruzioni di vincolo di base:Here are some examples of basic constraint statements:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

È possibile posizionare il servizio solo sui nodi in cui l'istruzione del vincolo di posizionamento generale restituisce "True". I nodi che non dispongono di una proprietà definita non corrispondono ad alcun vincolo di posizionamento che contiene la proprietà.

Si supponga che le seguenti proprietà del nodo siano state definite per un tipo di nodo in ClusterManifest.xml:Let's say that the following node properties were defined for a node type in ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

L'esempio seguente mostra le proprietà dei nodi definite tramite ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure.The following example shows node properties defined via ClusterConfig.json for standalone deployments or Template.json for Azure-hosted clusters. 

> [!NOTE]
> Nel modello di Azure Resource Manager il tipo di nodo è in genere parametrizzato. Sarebbe simile `"[parameters('vmNodeType1Name')]"` anziché NodeType01.
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

È possibile creare *vincoli* di posizionamento del servizio per un servizio come indicato di seguito:You can create service placement constraints for a service as follows:

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

Se tutti i nodi di NodeType01 sono validi, `"(NodeType == NodeType01)"`è anche possibile selezionare tale tipo di nodo con il vincolo .

I vincoli di posizionamento di un servizio possono essere aggiornati dinamicamente durante il runtime. Se necessario, è possibile spostare un servizio nel cluster, aggiungere e rimuovere requisiti e così via. Service Fabric assicura che il servizio rimanga aggiornato e disponibile anche quando vengono apportati questi tipi di modifiche.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

I vincoli di posizionamento vengono specificati per ogni istanza del servizio denominata. Gli aggiornamenti sostituiscono (sovrascrivono) sempre quanto specificato in precedenza.

La definizione del cluster definisce le proprietà in un nodo. La modifica delle proprietà di un nodo richiede un aggiornamento alla configurazione del cluster. L'aggiornamento delle proprietà di un nodo richiede che ogni nodo in questione venga riavviato per segnalare le nuove proprietà. Service Fabric gestisce questi aggiornamenti in sequenza.

## <a name="describing-and-managing-cluster-resources"></a>Descrizione e gestione delle risorse cluster
Uno dei processi più importanti di un agente di orchestrazione consiste nel semplificare la gestione dell'utilizzo delle risorse del cluster. Gestione delle risorse del cluster può avere significati diversi. 

Innanzitutto, è necessario verificare che i computer non siano sovraccarichi, ovvero che non eseguano più servizi di quanti ne possano gestire. 

In secondo luogo, c'è bilanciamento e ottimizzazione, che sono fondamentali per l'esecuzione efficiente dei servizi. Le offerte di servizi convenienti o sensibili alle prestazioni non possono consentire ad alcuni nodi di essere caldi mentre altri sono freddi. I nodi attivi causano contese di risorse e prestazioni ridotte. I nodi freddi rappresentano lo spreda delle risorse e l'aumento dei costi. 

Service Fabric rappresenta le risorse come *metriche.* Per metrica si intende qualsiasi risorsa logica o fisica che deve essere descritta per Service Fabric, Esempi di metriche sono "WorkQueueDepth" o "MemoryInMb". Per informazioni sulle risorse fisiche che Service Fabric può governare nei nodi, vedere [Governance delle risorse.](service-fabric-resource-governance.md) Per informazioni sulle metriche predefinite usate da Gestione risorse cluster e su come configurare metriche personalizzate, vedere [questo articolo.](service-fabric-cluster-resource-manager-metrics.md)

Le metriche sono diverse dai vincoli di posizionamento e dalle proprietà dei nodi. Le proprietà dei nodi sono descrittori statici dei nodi stessi. Le metriche descrivono le risorse di cui dispongono i nodi e che i servizi utilizzano quando vengono eseguiti in un nodo. Una proprietà del nodo può essere **HasSSD** e potrebbe essere impostata su true o false. La quantità di spazio disponibile su tale SSD e quanto viene utilizzato dai servizi sarebbe una metrica come "DriveSpaceInMb". 

Analogamente ai vincoli di posizionamento e alle proprietà dei nodi, Gestione risorse cluster di Service Fabric non comprende il significato dei nomi delle metriche. I nomi delle metriche sono semplicemente stringhe. È consigliabile dichiarare le unità come parte dei nomi di metrica creati quando potrebbero essere ambigui.

## <a name="capacity"></a>Capacity
Se si disattiva il *bilanciamento delle*risorse, Gestione risorse cluster di service-fabric garantisca comunque che nessun nodo superi la capacità. Gestire i sovraccarichi di capacità è possibile, a meno che il cluster non sia pieno o il carico di lavoro sia maggiore rispetto a qualsiasi altro nodo. La capacità è un altro *vincolo* utilizzato da Gestione risorse cluster per comprendere la quantità di una risorsa di un nodo. La capacità rimanente verrà registrata anche per il cluster nel suo complesso. 

Sia la capacità che l'utilizzo a livello di servizio sono espressi in termini di metriche. Ad esempio, la metrica potrebbe essere "ClientConnections" e un nodo potrebbe avere una capacità per "ClientConnections" di 32.768.For example, the metric might be "ClientConnections" and a node might have a capacity for "ClientConnections" of 32,768. Altri nodi possono avere altri limiti. Un servizio in esecuzione su tale nodo può dire che sta attualmente consumando 32.256 della metrica "ClientConnections".

Durante il runtime, Gestione risorse cluster tiene traccia della capacità rimanente nel cluster e nei nodi. Per tenere traccia della capacità, Gestione risorse cluster sottrae l'utilizzo di ogni servizio dalla capacità di un nodo in cui viene eseguito il servizio. Con queste informazioni, Gestione risorse cluster è in grado di individuare o spostare le repliche in modo che i nodi non superino la capacità.

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

È possibile visualizzare le capacità definite nel manifesto del cluster. Di seguito è riportato un esempio per ClusterManifest.xml:Here's an example for ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Ecco un esempio di capacità definite tramite ClusterConfig.json per distribuzioni autonome o Template.json per cluster ospitati in Azure:Here's an example of capacities defined via ClusterConfig.json for standalone deployments or Template.json for Azure-hosted clusters: 

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

Il carico di un servizio spesso cambia in modo dinamico. Si supponga che il carico di una replica di "ClientConnections" sia cambiato da 1.024 a 2.048. Il nodo su cui era in esecuzione aveva una capacità di soli 512 rimanenti per quella metrica. Ora la posizione della replica o dell'istanza non è valida, perché non c'è spazio sufficiente su quel nodo. Gestione risorse cluster deve riportare il nodo al di sotto della capacità. Riduce il carico sul nodo che supera la capacità spostando una o più repliche o istanze da tale nodo ad altri nodi. 

Gestione risorse cluster tenta di ridurre al minimo il costo dello spostamento delle repliche. Per saperne di più sui costi di [movimento](service-fabric-cluster-resource-manager-movement-cost.md) e sulle strategie e le regole di [ribilanciamento.](service-fabric-cluster-resource-manager-metrics.md)

## <a name="cluster-capacity"></a>Capacità del cluster
In che modo Gestione risorse cluster dell'infrastruttura di servizi impedisce che il cluster complessivo sia troppo pieno? Con il carico dinamico, non c'è molto che possa fare. I servizi possono avere il loro picco di carico indipendentemente dalle azioni eseguite da Gestione risorse cluster. Di conseguenza, il tuo cluster con un sacco di spazio per la testa oggi potrebbe essere sottopotenziato se c'è un picco domani. 

I controlli in Gestione risorse cluster consentono di evitare problemi. La prima cosa da fare è impedire la creazione di nuovi carichi di lavoro che causerebbero il cluster per diventare pieno.

Si supponga di creare un servizio senza stato e a esso sia associato un carico. Il servizio si preoccupa della metrica "DiskSpaceInMb". Il servizio utilizzerà cinque unità di "DiskSpaceInMb" per ogni istanza del servizio. Si vogliono creare tre istanze del servizio. Ciò significa che sono necessarie 15 unità di "DiskSpaceInMb" per essere presenti nel cluster per poter creare anche queste istanze del servizio.

Cluster Resource Manager calcola continuamente la capacità e il consumo di ogni metrica in modo da poter determinare la capacità rimanente nel cluster. Se lo spazio disponibile non è sufficiente, Gestione risorse cluster rifiuta la chiamata per creare un servizio.

Poiché il requisito è solo che 15 unità saranno disponibili, è possibile allocare questo spazio in molti modi diversi. Ad esempio, potrebbe essere presente un'unità di capacità rimanente in 15 nodi diversi o tre unità di capacità rimanenti in cinque nodi diversi. Se Gestione risorse cluster è in grado di ridisporre le cose in modo che siano disponibili cinque unità su tre nodi, il servizio verrà collocato. In genere la riorganizzazione del cluster è possibile, a meno che il cluster non sia quasi pieno o che, per qualsiasi motivo, non sia possibile consolidare i servizi esistenti.

## <a name="buffered-capacity"></a>Capacità memorizzata nel buffer
La capacità memorizzata nel buffer è un'altra funzionalità di Gestione risorse cluster. che consente di riservare parti della capacità complessiva del nodo. Questo buffer di capacità viene utilizzato solo per posizionare i servizi durante gli aggiornamenti e gli errori dei nodi. 

La capacità memorizzata nel buffer viene specificata globalmente per ogni metrica per tutti i nodi. Il valore scelto per la capacità riservata è una funzione del numero di domini di errore e di aggiornamento disponibili nel cluster. Ulteriori domini di errore e aggiornamento consentono di scegliere un numero inferiore per la capacità memorizzata nel buffer. Se si dispone di molti domini, ci si può aspettare la mancata disponibilità di porzioni inferiori del cluster durante gli aggiornamenti e gli errori. Specificare la capacità memorizzata nel buffer ha senso solo se è stata specificata anche la capacità del nodo per una metrica.

Di seguito è riportato un esempio di come specificare la capacità memorizzata nel buffer in ClusterManifest.xml:Here's an example of how to specify buffered capacity in ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Ecco un esempio di come specificare la capacità memorizzata nel buffer tramite ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:Here's an example of how to specify buffered capacity via ClusterConfig.json for standalone deployments or Template.json for Azure-hosted clusters:

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

La creazione di nuovi servizi non riesce quando il cluster esaurisce il buffer di capacità per una metrica. Impedire la creazione di nuovi servizi per conservare il buffer garantisce che gli errori e gli aggiornamenti non causino il superamento della capacità dei nodi. La capacità memorizzata nel buffer è facoltativa, ma è consigliabile in qualsiasi cluster che definisce una capacità per una metrica.

Gestione risorse cluster espone queste informazioni sul carico. Per ogni metrica, le informazioni includono: 
- Impostazioni di capacità memorizzate nel buffer.
- La capacità totale.
- Il consumo corrente.
- Se ogni metrica viene considerata bilanciata o meno.
- Statistiche sulla deviazione standard.
- I nodi con il carico maggiore e minimo.  
  
Il codice seguente mostra un esempio di tale output:The following code shows an example of that output:

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
* Per informazioni sull'architettura e sul flusso di informazioni in Gestione risorse cluster, vedere Cenni preliminari [sull'architettura](service-fabric-cluster-resource-manager-architecture.md)di Gestione risorse cluster.
* La definizione delle metriche di deframmentazione è un modo per consolidare il carico sui nodi anziché distribuirlo. Per informazioni su come configurare la deframmentazione, vedere [Deframmentazione delle metriche e caricamento in Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Iniziare dall'inizio e [ottenere un'introduzione a Gestione risorse cluster di Service Fabric.](service-fabric-cluster-resource-manager-introduction.md)
* Per informazioni su come Gestione risorse cluster gestisce e bilancia il carico nel cluster, vedere [Bilanciamento del cluster di Service Fabric.](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
