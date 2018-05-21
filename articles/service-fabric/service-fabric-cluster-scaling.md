---
title: Ridimensionamento di un cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni sul ridimensionamento dei cluster di Service Fabric in orizzontale o in verticale.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: f9e3f190decdc907cf57a0235b9d7142081bd2f1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="scaling-service-fabric-clusters"></a>Ridimensionamento dei cluster di Service Fabric
Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I cluster possono contenere migliaia di nodi. Dopo aver creato un cluster di Service Fabric, è possibile scalare il cluster in orizzontale (modificare il numero di nodi) o in verticale (modificare le risorse dei nodi).  È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.  Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

Perché ridimensionare il cluster? Le richieste delle applicazioni cambiano nel tempo.  Potrebbe essere necessario aumentare le risorse del cluster per supportare l'aumento del carico di lavoro delle applicazioni o del traffico di rete oppure ridurre le risorse del cluster quando le richieste si riducono.

### <a name="scaling-in-and-out-or-horizontal-scaling"></a>Ridimensionamento orizzontale
Modifica il numero di nodi nel cluster.  Dopo che i nuovi nodi sono stati aggiunti al cluster, [Gestione risorse cluster](service-fabric-cluster-resource-manager-introduction.md) sposta i servizi nei nuovi nodi, riducendo il carico totale sui nodi esistenti.  È anche possibile ridurre il numero di nodi del cluster, se le risorse del cluster non vengono usate in modo efficiente.  Quando i nodi vengono rimossi dal cluster, i servizi vengono spostati da tali nodi e il carico nei nodi rimanenti aumenta.  La riduzione del numero di nodi in un cluster in esecuzione in Azure consente un risparmio, poiché il pagamento è basato sul numero di macchine virtuali usate, non sul carico di lavoro in tali macchine virtuali.  

- Vantaggi: scalabilità infinita, in teoria.  Se l'applicazione è progettata per la scalabilità, è possibile abilitare una crescita illimitata aggiungendo più nodi.  Gli strumenti disponibili negli ambienti cloud consentono di aggiungere o rimuovere facilmente i nodi, in modo da regolare la capacità e pagare solo per le risorse usate.  
- Svantaggi: le applicazioni devono essere [progettate per la scalabilità](service-fabric-concepts-scalability.md).  Anche la persistenza e i database delle applicazioni possono richiedere attività aggiuntive a livello di architettura per la scalabilità.  Le raccolte [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) nei servizi con stato di Service Fabric, comunque, rendono molto più semplice ridimensionare i dati delle applicazioni.

### <a name="scaling-up-and-down-or-vertical-scaling"></a>Ridimensionamento verticale 
Modifica le risorse (CPU, memoria o archiviazione) dei nodi nel cluster.
- Vantaggi: il software e l'architettura dell'applicazione rimangono inalterati.
- Svantaggi: scalabilità finita, dal momento che la possibilità di aumento delle risorse nei singoli nodi è limitata. Tempo di inattività, perché sarà necessario portare offline i computer fisici o le macchine virtuali per aggiungere o rimuovere le risorse.

## <a name="scaling-an-azure-cluster-in-or-out"></a>Ridimensionamento orizzontale di un cluster di Azure
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. 

Quando si ridimensiona un cluster di Azure, tenere presenti le linee guida seguenti:
- I tipi di nodo primario che eseguono carichi di lavoro di produzione devono contenere sempre cinque o più nodi.
- I tipi di nodo non primario che eseguono carichi di lavoro di produzione con stato devono contenere sempre cinque o più nodi.
- I tipi di nodo non primario che eseguono carichi di lavoro di produzione senza stato devono contenere sempre due o più nodi.
- Qualsiasi tipo di nodo del [livello di durabilità](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Gold or Silver deve sempre contenere cinque o più nodi.
- Non rimuovere nodi/istanze di VM casuali da un tipo di nodo. Usare sempre la funzionalità di riduzione delle prestazioni del set di scalabilità di macchine virtuali. L'eliminazione di istanze di VM casuali può influenzare negativamente la capacità dei sistemi di eseguire il bilanciamento del carico in modo corretto.
- Se si usano regole di scalabilità automatica, impostare le regole in modo che la riduzione (rimozione di istanze di VM) venga eseguita un nodo alla volta. La riduzione delle prestazioni di più di un'istanza per volta non è sicura.

Poiché i tipi di nodi di Service Fabric nel cluster sono costituiti da set di scalabilità di macchine virtuali nel back-end, è possibile [configurare regole di ridimensionamento automatico o ridimensionare manualmente](service-fabric-cluster-scale-up-down.md) ogni tipo di nodo o set di scalabilità di macchine virtuali.

### <a name="programmatic-scaling"></a>Scalabilità a livello di codice
In molti scenari, le opzioni di [ridimensionamento di un cluster in modo manuale o mediante regole di scalabilità automatica](service-fabric-cluster-scale-up-down.md) rappresentano soluzioni valide. In scenari più avanzati, tuttavia, potrebbero non essere la scelta adatta. Alcuni potenziali svantaggi di questi approcci sono:

- La scalabilità manuale richiede l'accesso e la richiesta esplicita delle operazioni di scalabilità. Questo approccio potrebbe non essere una soluzione valida se le operazioni di scalabilità vengono richieste di frequente oppure in momenti imprevisti.
- Quando le regole di scalabilità automatica rimuovono un'istanza da un set di scalabilità di macchine virtuali, non rimuovono automaticamente le informazioni di questo nodo dal cluster Service Fabric associato, a meno che il tipo di nodo non disponga di un livello di durabilità Silver o Gold. Poiché funzionano a livello di set di scalabilità anziché a livello di Service Fabric, le regole di scalabilità automatica possono rimuovere i nodi Service Fabric senza arrestarli in modo normale. Questo tipo di rimozione dei nodi lascerà il nodo Service Fabric nello stato "ghost" dopo le operazioni di riduzione delle istanze. Un utente o un servizio dovrà pulire periodicamente lo stato del nodo rimosso nel cluster Service Fabric.
- Un tipo di nodo con un livello di durabilità Gold o Silver esegue automaticamente la pulizia dei nodi rimossi. Non è necessaria una pulizia aggiuntiva.
- Anche se le regole di scalabilità automatica supportano [molte metriche](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md), si tratta comunque di un set limitato. Se lo scenario richiede la scalabilità basata su alcune metriche non incluse in questo set, le regole di scalabilità automatica potrebbero non essere una soluzione valida.

L'approccio da scegliere per la scalabilità di Service Fabric dipende dallo scenario specifico. Se la scalabilità non è comune, la possibilità di aggiungere o rimuovere nodi manualmente è probabilmente sufficiente. Per gli scenari più complessi, gli SDK e le regole di scalabilità automatica che espongono la capacità di eseguire la scalabilità a livello di codice offrono alternative molto efficaci.

Esistono API Azure che consentono alle applicazioni di usare a livello di codice i set di scalabilità di macchine virtuali e i cluster Service Fabric. Se le opzioni di scalabilità automatica esistenti non funzionano per lo scenario specifico, queste API consentono di implementare una logica di scalabilità personalizzata. 

Un approccio all'implementazione di questa funzionalità di scalabilità automatica "interna" consiste nell'aggiungere un nuovo servizio senza stato all'applicazione Service Fabric per gestire le operazioni di scalabilità. La creazione di un proprio servizio di scalabilità offre il massimo livello di controllo e personalizzazione sul comportamento di scalabilità dell'applicazione. Questa condizione può essere utile negli scenari che richiedono un controllo preciso su quando e come aumentare o ridurre il numero di istanze dell'applicazione. Tuttavia, questo controllo implica un compromesso a livello di complessità del codice. Questo approccio richiede un codice di scalabilità proprio, il che non è semplice. All'interno del metodo `RunAsync` del servizio, un set di trigger può determinare se la scalabilità è necessaria, inclusi i parametri di controllo, come la dimensione massima di un cluster e i tempi di raffreddamento della scalabilità.   

L'API usata per le interazioni dei set di scalabilità di macchine virtuali, sia per verificare il numero corrente di istanze di macchine virtuali che per modificarlo, è la [libreria Azure Management Compute Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). La libreria Fluent fornisce un'API facile da usare per l'interazione con i set di scalabilità di macchine virtuali.  Per interagire con il cluster Service Fabric, usare [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Tuttavia, non è necessario che il codice di scalabilità sia in esecuzione come servizio nel cluster di cui modificare la scalabilità. Sia `IAzure` sia `FabricClient` possono connettersi in remoto alle risorse di Azure associate; il servizio di scalabilità può quindi essere facilmente un'applicazione console o il servizio Windows in esecuzione dall'esterno dell'applicazione Service Fabric.

Considerando queste limitazioni, è consigliabile [implementare più modelli di scalabilità automatica personalizzati](service-fabric-cluster-programmatic-scaling.md).


## <a name="scaling-a-standalone-cluster-in-or-out"></a>Ridimensionamento orizzontale di un cluster autonomo
I cluster autonomi consentono di distribuire un cluster Service Fabric locale o nel provider di servizi cloud che si preferisce.  I tipi di nodi sono costituiti da computer fisici o macchine virtuali, a seconda della distribuzione. Rispetto ai cluster in esecuzione in Azure, il processo di ridimensionamento di un cluster autonomo è leggermente più complesso.  È necessario modificare manualmente il numero di nodi nel cluster e quindi eseguire un aggiornamento della configurazione del cluster.

È possibile che con la rimozione di nodi vengano avviati più aggiornamenti in sequenza. Alcuni nodi sono contrassegnati con il tag `IsSeedNode=”true”` e possono essere identificati mediante query nel manifesto del cluster usando [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). La rimozione di tali nodi può richiedere più tempo perché comporta lo spostamento dei nodi di inizializzazione. Il cluster deve mantenere almeno tre nodi di tipo primario.

Quando si ridimensiona un cluster autonomo, tenere presenti le linee guida seguenti:
- La sostituzione dei nodi primari deve essere eseguita un nodo alla volta, anziché eseguire la rimozione e l'aggiunta in batch.
- Prima di rimuovere un tipo di nodo, verificare se sono presenti nodi che fanno riferimento al tipo di nodo stesso. Rimuovere tali nodi prima di rimuovere il tipo di nodo corrispondente. Dopo aver rimosso tutti i nodi corrispondenti, è possibile rimuovere il tipo di nodo (NodeType) dalla configurazione del cluster e avviare un aggiornamento della configurazione mediante [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Per altre informazioni, vedere [Ridimensionare un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-an-azure-cluster-up-or-down"></a>Ridimensionamento verticale di un cluster di Azure
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente.  Il ridimensionamento verticale di un tipo di nodo comporta la modifica dello SKU delle istanze di macchina virtuale nel set di scalabilità. 

> [!WARNING]
> È consigliabile non modificare lo SKU VM di un tipo di nodo o set di scalabilità, a meno che non sia in esecuzione al [livello di durabilità Silver o superiore](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). La modifica delle dimensioni della SKU della macchina virtuale è un'operazione dell'infrastruttura sul posto distruttiva per i dati. Senza la capacità di ritardare o monitorare questa modifica, è possibile che l'operazione causi una perdita di dati per i servizi con stato o provochi altri problemi operativi non previsti, anche per i carichi di lavoro senza stato. 
>

Quando si ridimensiona un cluster di Azure, tenere presenti le linee guida seguenti:
- Se si riducono le prestazioni di un tipo di nodo primario, è consigliabile non ridurle mai più di quanto consentito dal [livello di affidabilità](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster).

Il processo di ridimensionamento verticale di un tipo di nodo varia a seconda del fatto che si tratti di un tipo di nodo primario o non primario.

### <a name="scaling-non-primary-node-types"></a>Ridimensionamento di tipi di nodo non primari
Creare un nuovo tipo di nodo con le risorse necessarie.  Aggiornare i vincoli di posizionamento dei servizi in esecuzione in modo da includere il nuovo tipo di nodo.  Gradualmente (una alla volta), ridurre a zero il numero di istanze del tipo di nodo precedente, in modo che l'affidabilità del cluster rimanga invariata.  Quando il tipo di nodo precedente viene ritirato, verrà gradualmente eseguita la migrazione dei servizi nel nuovo tipo di nodo.

### <a name="scaling-the-primary-node-type"></a>Ridimensionamento del tipo di nodo primario
È consigliabile non modificare lo SKU VM del tipo di nodo primario. Se è necessario aumentare la capacità del cluster, è consigliabile aggiungere più istanze. 

Se non è possibile, creare un nuovo cluster e [ripristinare lo stato dell'applicazione](service-fabric-reliable-services-backup-restore.md) (se applicabile) dal cluster precedente. Non è necessario ripristinare lo stato dei servizi di sistema, perché verrà ricreato quando si distribuiscono le applicazioni nel nuovo cluster. Se nel cluster venivano eseguite solo applicazioni senza stato, è sufficiente distribuire le applicazioni nel nuovo cluster, non sono necessarie operazioni di ripristino. Se si opta per la modalità non supportata e si vuole modificare lo SKU di VM, apportare le modifiche alla definizione del modello di set di scalabilità di macchine virtuali per riflettere il nuovo SKU. Se il cluster include un solo tipo di nodo, verificare che tutte le applicazioni con stato rispondano in modo tempestivo a tutti gli [eventi del ciclo di vita della replica del servizio](service-fabric-reliable-services-lifecycle.md), ad esempio al blocco della replica in compilazione, e che la durata della ricompilazione della replica del servizio sia inferiore a cinque minuti (per il livello di durabilità Silver). 

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).
* [Ridimensionamento orizzontale di un cluster di Azure](service-fabric-tutorial-scale-cluster.md).
* [Ridimensionamento di un cluster di Azure a livello di codice](service-fabric-cluster-programmatic-scaling.md) tramite l'SDK di calcolo di Azure Fluent.
* [Ridimensionamento orizzontale di un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

