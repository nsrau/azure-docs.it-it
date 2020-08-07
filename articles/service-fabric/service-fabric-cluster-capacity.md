---
title: Considerazioni sulla pianificazione della capacità del cluster Service Fabric
description: Tipi di nodo, durabilità, affidabilità e altri aspetti da considerare durante la pianificazione del cluster Service Fabric.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 4949a83ac2aac664c19be46a367fce2bbff4cb02
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904820"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerazioni sulla pianificazione della capacità del cluster Service Fabric

La pianificazione della capacità del cluster è importante per ogni ambiente di produzione Service Fabric. Le considerazioni principali includono:

* **Numero iniziale e proprietà dei *tipi di nodo* del cluster**

* **Livello di *durabilità* di ogni tipo di nodo**, che determina Service Fabric privilegi della VM nell'infrastruttura di Azure

* **Livello di *affidabilità* del cluster**, che determina la stabilità dei servizi di sistema Service Fabric e della funzione complessiva del cluster

In questo articolo vengono illustrati i punti decisionali significativi per ognuna di queste aree.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>Numero iniziale e proprietà dei tipi di nodo del cluster

Un *tipo di nodo* definisce dimensioni, numero e proprietà per un set di nodi (macchine virtuali) del cluster. Ogni tipo di nodo definito in un cluster di Service Fabric è mappato a un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md).

Poiché ogni tipo di nodo è un set di scalabilità distinto, può essere ridimensionato in modo indipendente, avere diversi set di porte aperte e avere metriche di capacità diverse. Per altre informazioni sulla relazione tra i tipi di nodo e i set di scalabilità di macchine virtuali, vedere [Service Fabric tipi di nodo del cluster](service-fabric-cluster-nodetypes.md).

Ogni cluster richiede un **tipo di nodo primario**, che esegue i servizi di sistema critici che forniscono Service Fabric funzionalità della piattaforma. Sebbene sia possibile usare i tipi di nodo primari anche per eseguire le applicazioni, è consigliabile dedicarle esclusivamente ai servizi di sistema in esecuzione.

I **tipi di nodo non primari** possono essere usati per definire i ruoli applicazione, ad esempio i servizi *front-end* e *back-end* , e per isolare fisicamente i servizi all'interno di un cluster. I cluster Service Fabric possono avere zero o più tipi di nodo non primari.

Il tipo di nodo primario viene configurato usando l' `isPrimary` attributo sotto la definizione del tipo di nodo nel modello di distribuzione Azure Resource Manager. Per l'elenco completo delle proprietà del tipo di nodo, vedere l' [oggetto NodeTypeDescription](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) . Ad esempio, è possibile aprire qualsiasi *AzureDeploy.js* di file in [Service Fabric esempi di cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) e trovare la ricerca della *pagina* per l' `nodetTypes` oggetto.

### <a name="node-type-planning-considerations"></a>Considerazioni sulla pianificazione del tipo di nodo

Il numero di nodi iniziali dipende dallo scopo del cluster e dalle applicazioni e dai servizi in esecuzione su di esso. Prendere in considerazione le domande seguenti:

* ***L'applicazione ha più servizi, alcuni dei quali devono essere pubblici o per Internet?***

    Le applicazioni tipiche contengono un servizio gateway front-end che riceve l'input da un client e uno o più servizi back-end che comunicano con i servizi front-end, con reti separate tra i servizi front-end e back-end. Questi casi richiedono in genere tre tipi di nodo: un tipo di nodo primario e due tipi di nodo non primari (uno per il servizio front-end e back-end).

* ***I servizi che compongono l'applicazione hanno esigenze di infrastruttura diverse, ad esempio maggiore RAM o più cicli di CPU?***

    Spesso il servizio front-end può essere eseguito in VM più piccole (dimensioni di VM come D2) con porte aperte a Internet.  Potrebbe essere necessario eseguire servizi back-end a elevato utilizzo di calcolo in macchine virtuali di dimensioni maggiori (con dimensioni di VM come D4, D6, D15) che non sono connesse a Internet. La definizione di tipi di nodo diversi per questi servizi consente di rendere più efficiente e sicuro l'uso delle macchine virtuali Service Fabric sottostanti e consente loro di ridimensionarle in modo indipendente. Per altre informazioni sulla stima della quantità di risorse necessarie, vedere [pianificazione della capacità per applicazioni Service Fabric](service-fabric-capacity-planning.md)

* ***I servizi per le applicazioni dovranno essere scalati oltre 100 nodi?***

    Un tipo di nodo singolo non può essere ridimensionato in modo affidabile oltre 100 nodi per set di scalabilità di macchine virtuali per Service Fabric applicazioni. Per l'esecuzione di più di 100 nodi sono necessari set di scalabilità di macchine virtuali aggiuntivi (e quindi tipi di nodo aggiuntivi).

* ***Il cluster si estenderà su zone di disponibilità?***

    Service Fabric supporta i cluster che si estendono tra [zone di disponibilità](../availability-zones/az-overview.md) distribuendo i tipi di nodo aggiunti a zone specifiche, garantendo una disponibilità elevata delle applicazioni. Zone di disponibilità richiedono la pianificazione del tipo di nodo e i requisiti minimi. Per informazioni dettagliate, vedere [topologia consigliata per il tipo di nodo primario di Service Fabric cluster](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones)che si estendono tra zone di disponibilità. 

Quando si determinano il numero e le proprietà dei tipi di nodo per la creazione iniziale del cluster, tenere presente che è sempre possibile aggiungere, modificare o rimuovere i tipi di nodo (non primari) dopo che il cluster è stato distribuito. I [tipi di nodo primari possono anche essere modificati](service-fabric-scale-up-node-type.md) nei cluster in esecuzione (anche se tali operazioni richiedono una grande quantità di pianificazione e attenzione negli ambienti di produzione).

Un'ulteriore considerazione per le proprietà del tipo di nodo è il livello di durabilità, che determina i privilegi che le VM di un tipo di nodo hanno all'interno dell'infrastruttura di Azure. Usare le dimensioni delle VM selezionate per il cluster e il numero di istanze assegnato per i singoli tipi di nodo per determinare il livello di durabilità appropriato per ogni tipo di nodo, come descritto di seguito.

## <a name="durability-characteristics-of-the-cluster"></a>Caratteristiche di durabilità del cluster

Il *livello di durabilità* designa i privilegi che le macchine virtuali Service Fabric hanno con l'infrastruttura di Azure sottostante. Questo privilegio consente Service Fabric di sospendere qualsiasi richiesta di infrastruttura a livello di macchina virtuale, ad esempio il riavvio, la ricreazione dell'immagine o la migrazione, che influisca sui requisiti del quorum per i servizi di sistema Service Fabric e i servizi con stato.

> [!IMPORTANT]
> Il livello di durabilità è impostato per ogni tipo di nodo. Se non viene specificato alcun valore, verrà usato il livello *Bronze* , che tuttavia non fornisce aggiornamenti automatici del sistema operativo. La durabilità *Silver* o *Gold* è consigliata per i carichi di lavoro di produzione.

La tabella seguente elenca Service Fabric livelli di durabilità, i rispettivi requisiti e affordances.

| Livello di durabilità  | Numero minimo richiesto di macchine virtuali | Dimensioni delle macchine virtuali supportate                                                                  | Aggiornamenti al set di scalabilità di macchine virtuali                               | Aggiornamenti e manutenzione avviati da Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Oro             | 5                              | Dimensioni del nodo completo dedicate a un singolo cliente (ad esempio, L32s, GS5, G5, DS15_v2 D15_v2) | Può essere ritardato fino all'approvazione da parte del cluster di Service Fabric | Può essere sospeso per 2 ore per dominio di aggiornamento, in modo da consentire ulteriore tempo per il ripristino delle repliche da errori precedenti |
| Argento           | 5                              | VM di un core singolo o superiore con almeno 50 GB di unità SSD locale                      | Può essere ritardato fino all'approvazione da parte del cluster di Service Fabric | Non può essere ritardato per un periodo di tempo significativo                                                    |
| Bronzo          | 1                              | VM con almeno 50 GB di unità SSD locale                                              | Non verrà ritardato dal cluster di Service Fabric           | Non può essere ritardato per un periodo di tempo significativo                                                    |

> [!WARNING]
> Con la durabilità Bronze, l'aggiornamento automatico delle immagini del sistema operativo non è disponibile. Sebbene [patch Orchestration Application](service-fabric-patch-orchestration-application.md) (destinata solo ai cluster non ospitati da Azure) *non sia consigliata* per i livelli di durabilità Silver o superiore, è l'unica opzione per automatizzare gli aggiornamenti di Windows rispetto ai domini di aggiornamento Service Fabric.

> [!IMPORTANT]
> Indipendentemente dal livello di durabilità, l'esecuzione di un'operazione di [deallocazione](/rest/api/compute/virtualmachinescalesets/deallocate) in un set di scalabilità di macchine virtuali eliminerà definitivamente il cluster.

### <a name="bronze"></a>Bronzo

I tipi di nodo in esecuzione con durabilità Bronze non ottengono privilegi. Ciò significa che i processi di infrastruttura che influiscano sui carichi di lavoro con stato non verranno interrotti o posticipati. Usare la durabilità Bronze per i tipi di nodo che eseguono solo carichi di lavoro senza stato. Per i carichi di lavoro di produzione è consigliabile eseguire il livello Silver o superiore.

### <a name="silver-and-gold"></a>Argento e oro

Usare la durabilità Silver o Gold per tutti i tipi di nodo che ospitano i servizi con stato che si prevede di ridimensionare spesso e dove si desidera che le operazioni di distribuzione siano ritardate e la capacità venga ridotta a favore della semplificazione del processo. Gli scenari di scalabilità orizzontale non devono influire sulla scelta del livello di durabilità.

#### <a name="advantages"></a>Vantaggi

* Riduce il numero di passaggi necessari per le operazioni di scalabilità (la disattivazione del nodo e Remove-ServiceFabricNodeState vengono chiamati automaticamente).
* Riduce il rischio di perdita di dati a causa di operazioni di modifica delle dimensioni della macchina virtuale sul posto e di operazioni di infrastruttura di Azure.

#### <a name="disadvantages"></a>Svantaggi

* Le distribuzioni nei set di scalabilità di macchine virtuali e in altre risorse di Azure correlate possono avere un timeout, essere posticipate o essere interamente bloccate da problemi nel cluster o a livello di infrastruttura.
* Le disattivazioni automatizzate dei nodi durante le operazioni sull'infrastruttura di Azure aumentano il numero di [eventi del ciclo di vita della replica](service-fabric-reliable-services-lifecycle.md), ad esempio di scambi di nodi primari.
* Accetta nodi fuori servizio per determinati periodi di tempo durante le attività di manutenzione dell'hardware o gli aggiornamenti del software della piattaforma Azure. Quando queste attività sono in corso, possono essere visibili nodi con stato Disabilitazione in corso o Disabilitato. Ciò riduce temporaneamente la capacità del cluster, ma non dovrebbe influire sulla disponibilità del cluster o delle applicazioni.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Procedure consigliate per i tipi di nodo durabilità Silver e Gold

Seguire questi consigli per la gestione dei tipi di nodo con durabilità Silver o Gold:

* Mantenere sempre integri il cluster e le applicazioni e verificare che le applicazioni rispondano in modo tempestivo a tutti gli [eventi del ciclo di vita della replica del servizio](service-fabric-reliable-services-lifecycle.md), ad esempio al blocco della replica in compilazione.
* Adottare modi più sicuri per apportare una modifica alle dimensioni della macchina virtuale (scalabilità verticale/verticale). Per modificare le dimensioni della VM di un set di scalabilità di macchine virtuali, è necessario prestare attenzione. Per informazioni dettagliate, vedere [aumentare il livello di un nodo di tipo Service Fabric](service-fabric-scale-up-node-type.md)
* Mantenere un numero minimo di cinque nodi per tutti i set di scalabilità di macchine virtuali con livello di durabilità Gold o Silver abilitato. Se si ridimensiona al di sotto di questa soglia, il cluster immetterà lo stato di errore e sarà necessario eseguire manualmente la pulizia dello stato ( `Remove-ServiceFabricNodeState` ) per i nodi rimossi.
* Ogni set di scalabilità di macchine virtuali con livello di durabilità Silver o Gold deve essere mappato al proprio tipo di nodo del cluster di Service Fabric. Il mapping di più set di scalabilità di macchine virtuali a un singolo tipo di nodo impedirà il corretto funzionamento del coordinamento tra il cluster di Service Fabric e l'infrastruttura di Azure.
* Non eliminare istanze di VM casuali. usare sempre la funzionalità di scalabilità del set di scalabilità di macchine virtuali. L'eliminazione di istanze di VM casuali può creare squilibri nell'istanza di macchina virtuale distribuita in domini di [aggiornamento](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) e [domini di errore](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains). Questo squilibrio potrebbe influire negativamente sulla capacità del sistema di bilanciare correttamente il carico tra le istanze del servizio o le repliche del servizio.
* Se si usa la scalabilità automatica, impostare le regole in modo che le operazioni di ridimensionamento (rimozione di istanze di VM) vengano eseguite solo in un nodo alla volta. La riduzione delle prestazioni di più di un'istanza per volta non è sicura.
* Se si eliminano o deallocano macchine virtuali nel tipo di nodo primario, non ridurre mai il numero di macchine virtuali allocate al di sotto di quanto richiesto dal livello di affidabilità. Queste operazioni verranno bloccate indefinitamente in un set di scalabilità con un livello di durabilità di Silver o Gold.

### <a name="changing-durability-levels"></a>Modifica dei livelli di durabilità

All'interno di determinati vincoli, è possibile modificare il livello di durabilità del tipo di nodo:

* Non è possibile effettuare il downgrade in bronzo dei tipi di nodo con livelli di durabilità Silver o Gold.
* L'aggiornamento dal livello Bronze al livello Silver o Gold può richiedere alcune ore.
* Quando si modifica il livello di durabilità, assicurarsi di aggiornarlo sia nella configurazione dell'estensione Service Fabric nella risorsa del set di scalabilità di macchine virtuali che nella definizione del tipo di nodo nella risorsa cluster Service Fabric. Questi valori devono corrispondere.

Un'altra considerazione quando la pianificazione della capacità è il livello di affidabilità per il cluster, che determina la stabilità dei servizi di sistema e del cluster complessivo, come descritto nella sezione successiva.

## <a name="reliability-characteristics-of-the-cluster"></a>Caratteristiche di affidabilità del cluster

Il *livello di affidabilità* del cluster determina il numero di repliche dei servizi di sistema in esecuzione sul tipo di nodo primario del cluster. Maggiore è il numero di repliche, più affidabili sono i servizi di sistema e, di conseguenza, il cluster nel suo complesso.

> [!IMPORTANT]
> Il livello di affidabilità viene impostato a livello di cluster e determina il numero minimo di nodi del tipo di nodo primario. I carichi di lavoro di produzione richiedono un livello di affidabilità Silver (maggiore o uguale a cinque nodi) o superiore.  

Il livello di affidabilità può avere i valori seguenti:

* **Platinum** : i servizi di sistema vengono eseguiti con il numero di set di repliche di destinazione
* I servizi **Gold** -System vengono eseguiti con il numero di set di repliche di destinazione di sette
* Servizi di sistema **Silver** eseguiti con numero di set di repliche di destinazione pari a cinque
* **Bronzo** -servizi di sistema eseguiti con numero di set di repliche di destinazione pari a tre

Ecco la raccomandazione per la scelta del livello di affidabilità. Il numero di nodi di inizializzazione viene anche impostato sul numero minimo di nodi per un livello di affidabilità.


| **Numero di nodi** | **Livello di affidabilità** |
| --- | --- |
| 1 | *Non specificare il `reliabilityLevel` parametro: viene calcolato dal sistema.* |
| 3 | Bronzo |
| 5 o 6| Argento |
| 7 o 8 | Oro |
| Da 9 in su | Platinum |

Quando si aumentano o si riducono le dimensioni del cluster (la somma delle istanze di VM in tutti i tipi di nodo), è consigliabile aggiornare l'affidabilità del cluster da un livello a un altro. Così facendo si attivano gli aggiornamenti del cluster necessari per modificare il totale di set di repliche dei servizi di sistema. Attendere che l'aggiornamento in corso venga completato prima di apportare altre modifiche al cluster, come l'aggiunta di nodi.  È possibile monitorare lo stato di avanzamento dell'aggiornamento in Service Fabric Explorer oppure eseguendo [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

### <a name="capacity-planning-for-reliability"></a>Pianificazione della capacità per l'affidabilità

Le esigenze di capacità del cluster verranno determinate in base al carico di lavoro e ai requisiti di affidabilità specifici. In questa sezione vengono fornite indicazioni generali per iniziare a pianificare la capacità.

#### <a name="virtual-machine-sizing"></a>Dimensionamento delle macchine virtuali

**Per i carichi di lavoro di produzione, le dimensioni della VM consigliate (SKU) sono [D2_V2 standard](../virtual-machines/dv2-dsv2-series.md) (o equivalenti) con un minimo di 50 GB di unità SSD locale, 2 core e 4 GiB di memoria.** È consigliabile disporre di un minimo di 50 GB di unità SSD locale, tuttavia alcuni carichi di lavoro, ad esempio quelli che eseguono i contenitori di Windows, richiederanno dischi di dimensioni maggiori. Quando si scelgono altre [dimensioni di VM](../virtual-machines/sizes-general.md) per i carichi di lavoro di produzione, tenere presenti i vincoli seguenti:

- Le dimensioni delle VM Core parziali, ad esempio a0 standard, non sono supportate.
- *Serie A* Le dimensioni delle macchine virtuali non sono supportate per motivi di prestazioni.
- Le macchine virtuali con priorità bassa non sono supportate.

#### <a name="primary-node-type"></a>Tipo di nodo primario

I **carichi di lavoro di produzione** in Azure richiedono almeno cinque nodi primari (istanze VM) e il livello di affidabilità Silver. Si consiglia di dedicare il tipo di nodo primario del cluster ai servizi di sistema e di usare vincoli di posizionamento per distribuire l'applicazione nei tipi di nodo secondari.

I **carichi di lavoro di test** in Azure possono eseguire almeno uno o tre nodi primari. Per configurare un cluster a un nodo, assicurarsi che l' `reliabilityLevel` impostazione venga completamente omessa nel modello di gestione risorse (specificando un valore di stringa vuoto per `reliabilityLevel` non è sufficiente). Se si configura il cluster a un nodo configurato con portale di Azure, questa configurazione viene eseguita automaticamente.

> [!WARNING]
> I cluster a un nodo vengono eseguiti con una configurazione speciale senza affidabilità e in cui la scalabilità orizzontale non è supportata.

#### <a name="non-primary-node-types"></a>Tipi di nodo non primari

Il numero minimo di nodi per un tipo di nodo non primario dipende dal [livello di durabilità](#durability-characteristics-of-the-cluster) specifico del tipo di nodo. È necessario pianificare il numero di nodi (e il livello di durabilità) in base al numero di repliche di applicazioni o servizi che si desidera eseguire per il tipo di nodo e a seconda che il carico di lavoro sia con stato o senza stato. Tenere presente che è possibile aumentare o ridurre il numero di macchine virtuali in un tipo di nodo in qualsiasi momento dopo la distribuzione del cluster.

##### <a name="stateful-workloads"></a>Carichi di lavoro con stato

Per i carichi di lavoro di produzione con stato che usano Service Fabric [Reliable Collections o](service-fabric-choose-framework.md)Reliable Actors, è consigliabile un numero di repliche di destinazione minimo e pari a cinque. Con questo, nello stato stabile si finisce con una replica (da un set di repliche) in ogni dominio di errore e dominio di aggiornamento. In generale, usare il livello di affidabilità impostato per i servizi di sistema come guida per il numero di repliche usato per i servizi con stato.

##### <a name="stateless-workloads"></a>Carichi di lavoro senza stato

Per i carichi di lavoro di produzione senza stato, la dimensione minima supportata per il tipo di nodo non primario è tre per mantenere il quorum, ma è consigliata una dimensione del tipo di nodo pari a cinque.

## <a name="next-steps"></a>Passaggi successivi

Prima di configurare il cluster, rivedere i `Not Allowed` [criteri di aggiornamento del cluster](service-fabric-cluster-fabric-settings.md) per attenuare la necessità di ricreare il cluster in un secondo momento a causa di impostazioni di configurazione del sistema altrimenti non modificabili.

Per ulteriori informazioni sulla pianificazione dei cluster, vedere:

* [Pianificazione e scalabilità dell'ambiente di calcolo](service-fabric-best-practices-capacity-scaling.md)
* [Pianificazione della capacità per le applicazioni Service Fabric](service-fabric-capacity-planning.md)
* [Pianificazione del ripristino di emergenza](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
