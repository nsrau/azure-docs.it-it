---
title: Pianificazione della capacità del cluster di Service Fabric | Documentazione Microsoft
description: Considerazioni sulla pianificazione della capacità del cluster Service Fabric. Tipi di nodo, operazioni, livelli di affidabilità e durabilità
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: chackdan
ms.openlocfilehash: bd76658c939496f27bf3751060c18d17968acd15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386798"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerazioni sulla pianificazione della capacità del cluster Service Fabric
La pianificazione della capacità è un passaggio importante per qualsiasi distribuzione di produzione. Ecco alcuni aspetti da considerare nell'ambito di tale processo.

* Numero di tipi di nodo con cui il cluster deve iniziare
* Proprietà di ciascun tipo di nodo (dimensione, primario, per Internet, numero di VM e così via)
* Caratteristiche di affidabilità e durabilità del cluster

> [!NOTE]
> Durante la pianificazione, è necessario come minimo rivedere tutti i valori **Non consentiti** dei criteri di aggiornamento, per assicurarsi che i valori siano stati impostati in modo corretto e ridurre l'uso del cluster in un secondo momento a causa di impostazioni di configurazione di sistema non modificabili. 
> 

Ogni aspetto verrà ora esaminato brevemente.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Numero di tipi di nodo con cui il cluster deve iniziare
Prima di tutto è necessario stabilire per che cosa verrà usato il cluster che si sta creando.  Che tipo di applicazioni si prevede di distribuire nel cluster? Se lo scopo del cluster non è chiaro, è molto probabile che non si sia ancora pronti per iniziare il processo di pianificazione della capacità.

Stabilire il numero di tipi di nodo con cui il cluster deve iniziare.  Di ogni tipo di nodo viene eseguito il mapping a un set di scalabilità di macchine virtuali. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. Quindi la decisione relativa al numero di tipi di nodo si basa essenzialmente sulle considerazioni seguenti:

* L'applicazione ha più servizi, alcuni dei quali devono essere pubblici o per Internet? Le applicazioni tipiche contengono un servizio gateway front-end che riceve l'input da un client e uno o più servizi back-end che comunicano con i servizi front-end. In questo caso quindi si finirà per avere almeno due tipi di nodi.
* I servizi (che costituiscono l'applicazione) hanno esigenze diverse per l'infrastruttura, ad esempio cicli di CPU più elevati o una quantità maggiore di RAM? Ad esempio, si supponga che l'applicazione che si vuole distribuire contenga un servizio front-end e un servizio back-end. Il servizio front-end può essere eseguito in VM più piccole (ad esempio, di dimensioni D2), con porte aperte a Internet,  Il servizio back-end è invece a elevato utilizzo di calcolo e deve essere eseguito in VM più grandi (ad esempio di dimensione D4, D6, D15) senza connessione a Internet.
  
  In questo esempio, anche se è possibile decidere di inserire tutti i servizi in un unico tipo di nodo, è consigliabile inserirli in un cluster con due tipi di nodo,  perché in questo modo ogni tipo di nodo può avere proprietà distinte, ad esempio la connettività Internet o le dimensioni delle VM. Il numero di VM può essere ridimensionato anche in modo indipendente.  
* Poiché non è possibile prevedere cosa accadrà in futuro, è importante basarsi sulle condizioni attuali per scegliere il numero di tipi di nodo necessari alle applicazioni per iniziare. Sarà sempre possibile aggiungere o rimuovere i tipi di nodi in seguito. Un cluster di Service Fabric deve avere almeno un tipo di nodo.

## <a name="the-properties-of-each-node-type"></a>Proprietà di ogni tipo di nodo
I **tipi di nodo** possono essere paragonati ai ruoli in Servizi cloud, poiché definiscono le dimensioni delle VM, il numero di VM e le relative proprietà. Ogni tipo di nodo definito in un cluster di Service Fabric è mappato a un [set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Per ogni tipo di nodo, che rappresenta un set di scalabilità distinto, è possibile aumentare o ridurre le prestazioni in modo indipendente e avere diversi set di porte aperte e metriche per la capacità diverse. Per altre informazioni sulle relazioni tra i tipi di nodo e i set di scalabilità di macchine virtuali, su come connettersi tramite RDP a una delle istanze, come aprire nuove porte e così via, vedere la pagina relativa ai [tipi di nodo del cluster di Service Fabric](service-fabric-cluster-nodetypes.md).

Un cluster di Service Fabric può comprendere più di un tipo di nodo. In tal caso, il cluster è costituito da un tipo di nodo primario e uno o più tipi di nodo non primari.

Non è possibile per un tipo di nodo singolo ridimensionare in modo affidabile oltre 100 nodi per ciascun set di scalabilità di macchine virtuali, impostati per applicazioni Service Fabric. Per raggiungere in modo affidabile più di 100 nodi, è necessario aggiungere altri set di scalabilità di macchine virtuali.

### <a name="primary-node-type"></a>Tipo di nodo primario

I servizi di sistema di Service Fabric (ad esempio, il servizio Gestione cluster o il servizio di archiviazione immagini) sono posizionati nel tipo di nodo primario. 

![Screenshot di un cluster con due tipi di nodo][SystemServices]

* Le **dimensioni minime delle macchine virtuali** per il tipo di nodo primario sono determinate dal **livello di durabilità** scelto. Il livello di durabilità predefinito è Bronze. Per informazioni dettagliate, vedere [Caratteristiche di durabilità del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Il **numero minimo delle macchine virtuali** per il tipo di nodo primario è determinato dal **livello di affidabilità** scelto. Il livello di affidabilità predefinito è Silver. Per informazioni dettagliate, vedere [Caratteristiche di affidabilità del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster).  

Dal modello di Azure Resource Manager, il tipo di nodo primario è configurato con l'attributo `isPrimary` nella [definizione del tipo di nodo](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Tipo di nodo non primario

In un cluster con più tipi di nodo è presente un tipo di nodo primario, mentre gli altri saranno non primari.

* Le **dimensioni minime delle macchine virtuali** per i tipi di nodo non primari sono determinate dal **livello di durabilità** scelto. Il livello di durabilità predefinito è Bronze. Per altre informazioni, vedere [Caratteristiche di durabilità del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Il **numero minimo di macchine virtuali** per i tipi di nodo non primari corrisponde a uno. È tuttavia consigliabile scegliere questo numero in base al numero di repliche dell'applicazione o dei servizi che si vuole eseguire in questo tipo di nodo. Il numero di VM in un tipo di nodo può essere aumentato dopo avere distribuito il cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>Caratteristiche di durabilità del cluster
Il livello di durabilità viene usato per indicare al sistema i privilegi delle VM rispetto all'infrastruttura di Azure sottostante. Nel tipo di nodo primario questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di VM (ad esempio il riavvio di una VM, il re-imaging di una VM o la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi di sistema e i servizi con stato. Nei tipi di nodo non primari questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di macchina virtuale (ad esempio, il riavvio di una VM, il re-imaging di una VM e la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi con stato.

| Livello di durabilità  | Numero minimo richiesto di macchine virtuali | SKU di macchine virtuali supportati                                                                  | Aggiornamenti al set di scalabilità di macchine virtuali                               | Aggiornamenti e manutenzione avviati da Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | SKU con tutti i nodi dedicati a un singolo cliente (ad esempio, L32s, GS5, G5, DS15_v2, D15_v2) | Può essere ritardato fino all'approvazione da parte del cluster di Service Fabric | Può essere sospeso per 2 ore per ogni dominio di aggiornamento, per concedere più tempo per il ripristino delle repliche da errori precedenti |
| Silver           | 5                              | Macchine virtuali con singolo core o superiori                                                        | Può essere ritardato fino all'approvazione da parte del cluster di Service Fabric | Non può essere ritardato per un periodo di tempo significativo                                                    |
| Bronze           | 1                              | Tutti                                                                                | Non verrà ritardato dal cluster di Service Fabric           | Non può essere ritardato per un periodo di tempo significativo                                                    |

> [!WARNING]
> I tipi di nodo in esecuzione con durabilità Bronze _non ottengono privilegi_. Ciò significa che i processi di infrastruttura che influiscono sui carichi di lavoro senza stato non verranno interrotti o posticipati, il che potrebbe influire sui carichi di lavoro. Usare il livello Bronze solo per i tipi di nodo che eseguono esclusivamente carichi di lavoro senza stato. Per i carichi di lavoro di produzione è consigliabile eseguire il livello Silver o superiore. 
> 
> Indipendentemente dal livello di durabilità, l'operazione di [deallocazione](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) nel set di scalabilità delle macchine virtuali comporta l'eliminazione del cluster

**Vantaggi dell'uso di livelli di durabilità Silver o Gold**
 
- Riducono il numero dei passaggi necessari in un'operazione di riduzione del numero di istanze. La disattivazione del nodo e Remove-ServiceFabricNodeState vengono chiamati automaticamente.
- Riducono il rischio di perdita di dati a causa di un'operazione di modifica sul posto dello SKU di VM avviata dal cliente o di operazioni sull'infrastruttura di Azure.

**Svantaggi dell'uso di livelli di durabilità Silver o Gold**
 
- Le distribuzioni nel set di scalabilità di macchine virtuali e in altre risorse di Azure correlate possono subire ritardi o timeout oppure essere interamente bloccate da problemi nel cluster o a livello di infrastruttura. 
- Le disattivazioni automatizzate dei nodi durante le operazioni sull'infrastruttura di Azure aumentano il numero di [eventi del ciclo di vita della replica](service-fabric-reliable-services-lifecycle.md), ad esempio di scambi di nodi primari.
- Accetta nodi fuori servizio per determinati periodi di tempo durante le attività di manutenzione dell'hardware o gli aggiornamenti del software della piattaforma Azure. Quando queste attività sono in corso, possono essere visibili nodi con stato Disabilitazione in corso o Disabilitato. Ciò riduce temporaneamente la capacità del cluster, ma non dovrebbe influire sulla disponibilità del cluster o delle applicazioni.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Raccomandazioni su quando usare i livelli di durabilità Silver o Gold

Usare la durabilità Silver o Gold per tutti i tipi di nodo che ospitano servizi con stato di cui si prevede di ridurre frequentemente il numero di istanze di VM, quando è preferibile ritardare le operazioni di distribuzione o ridurre la capacità a favore della semplificazione di queste operazioni di riduzione del numero di istanze. Gli scenari di aumento del numero di istanze di VM non influiscono sulla scelta del livello di durabilità. Solo la riduzione del numero di istanze ha un impatto su tale scelta.

### <a name="changing-durability-levels"></a>Modifica dei livelli di durabilità
- Non è possibile effettuare il downgrade al livello Bronze dei tipi di nodo con i livelli di durabilità Silver o Gold.
- L'aggiornamento dal livello Bronze al livello Silver o Gold può richiedere alcune ore.
- Quando si modifica il livello di durabilità, assicurarsi di aggiornarlo sia nella configurazione dell'estensione di Service Fabric nella risorsa del set di scalabilità di macchine virtuali che nella definizione del tipo di nodo nella risorsa cluster di Service Fabric. Questi valori devono corrispondere.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Raccomandazioni operative per il tipo di nodo impostato sul livello di durabilità Silver o Gold.

- Mantenere sempre integri il cluster e le applicazioni e verificare che le applicazioni rispondano in modo tempestivo a tutti gli [eventi del ciclo di vita della replica del servizio](service-fabric-reliable-services-lifecycle.md), ad esempio al blocco della replica in compilazione.
- Adottare modi più sicuri per eseguire una modifica dello SKU di VM (aumento/riduzione delle prestazioni): La modifica dello SKU di VM di un set di scalabilità di macchine virtuali è un'operazione di tipo unsafe intrinsecamente e, pertanto dovrebbe essere evitata, se possibile. Di seguito è illustrato il processo che è possibile seguire per evitare i problemi comuni.
    - **Per i tipi di nodo non primario:** Si consiglia di creare nuovi set di scalabilità di macchine virtuali, modificare il vincolo di posizionamento del servizio per includere il nuovo tipo di nodo/set di scalabilità macchina virtuale e quindi ridurre il vecchio numero istanza set di scalabilità macchina virtuale da zero, un nodo alla volta (in questo modo viene che per la rimozione dei nodi non influisca l'affidabilità del cluster).
    - **Per il tipo di nodo primario:** Si consiglia di non cambiare SKU di VM di tipo di nodo primario. La modifica dello SKU del tipo di nodo primario non è supportata. Se lo scopo del nuovo SKU è la capacità, è consigliabile aggiungere altre istanze. Se non è possibile, creare un nuovo cluster e [ripristinare lo stato dell'applicazione](service-fabric-reliable-services-backup-restore.md) (se applicabile) dal cluster precedente. Non è necessario ripristinare lo stato dei servizi di sistema, perché verrà ricreato quando si distribuiscono le applicazioni nel nuovo cluster. Se si eseguono applicazioni senza stato nel cluster, distribuire le applicazioni al nuovo cluster.  Non sono necessarie operazioni di ripristino. Se si opta per la modalità non supportata e si vuole modificare lo SKU di VM, apportare le modifiche alla definizione del modello di set di scalabilità di macchine virtuali per riflettere il nuovo SKU. Se il cluster include un solo tipo di nodo, verificare che tutte le applicazioni con stato rispondano in modo tempestivo a tutti gli [eventi del ciclo di vita della replica del servizio](service-fabric-reliable-services-lifecycle.md), ad esempio al blocco della replica in compilazione, e che la durata della ricompilazione della replica del servizio sia inferiore a cinque minuti (per il livello di durabilità Silver). 
    
- Mantenere un numero minimo di cinque nodi per tutti i set di scalabilità di macchine virtuali con livello di durabilità Gold o Silver abilitato.
- Ogni set di scalabilità di macchine virtuali con livello di durabilità Silver o Gold deve essere mappato al proprio tipo di nodo del cluster di Service Fabric. Il mapping di più set di scalabilità di macchine virtuali a un singolo tipo di nodo impedirà il corretto funzionamento del coordinamento tra il cluster di Service Fabric e l'infrastruttura di Azure.
- Non eliminare istanze di VM casuali. Usare sempre la funzionalità di riduzione delle prestazioni del set di scalabilità di macchine virtuali. L'eliminazione di istanze di VM causali può creare squilibri nella distribuzione delle istanze di VM tra domini di aggiornamento e domini di errore. Tale squilibrio potrebbe influire negativamente sulla capacità del sistema di bilanciare correttamente il carico tra istanze dei servizi e repliche dei servizi.
- Se si usa la scalabilità automatica, impostare le regole in modo che la riduzione del numero di istanze (ossia la rimozione di istanze di VM) venga eseguita un solo nodo alla volta. La riduzione delle prestazioni di più di un'istanza per volta non è sicura.
- In caso di eliminazione o deallocazione delle macchine virtuali nel tipo di nodo primario, non ridurre mai il conteggio delle macchine virtuali allocate al di sotto del numero richiesto dal livello di affidabilità. Queste operazioni verranno bloccate indefinitamente in un set di scalabilità con un livello di durabilità di Silver o Gold.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Caratteristiche di affidabilità del cluster
Il livello di affidabilità viene usato per impostare il numero di repliche dei servizi di sistema che si vuole eseguire in questo cluster nel tipo di nodo primario. I servizi di sistema nel cluster sono tanto più affidabili quanto più elevato è il numero di repliche.  

Il livello di affidabilità può avere i valori seguenti:

* Platinum: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a sette
* Gold: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a sette
* Silver: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a cinque 
* Bronze: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a tre

> [!NOTE]
> Il livello di affidabilità scelto determina il numero minimo di nodi che deve avere il tipo di nodo primario. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Raccomandazioni per il livello di affidabilità

Quando si aumentano o riducono le dimensioni del cluster (la somma delle istanze di VM in tutti i tipi di nodi), è necessario aggiornare l'affidabilità del cluster da un livello a un altro. Così facendo si attivano gli aggiornamenti del cluster necessari per modificare il totale di set di repliche dei servizi di sistema. Attendere che l'aggiornamento in corso venga completato prima di apportare altre modifiche al cluster, come l'aggiunta di nodi.  È possibile monitorare lo stato di avanzamento dell'aggiornamento in Service Fabric Explorer oppure eseguendo [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

Ecco la raccomandazione per la scelta del livello di affidabilità.  Il numero di nodi di inizializzazione viene anche impostato sul numero minimo di nodi per un livello di affidabilità.  Per un cluster con affidabilità Gold, ad esempio, esistono 7 nodi di inizializzazione.

| **Numero di nodi del cluster** | **Livello di affidabilità** |
| --- | --- |
| 1 |Non specificare il parametro Livello di affidabilità perché viene calcolato automaticamente dal sistema |
| 3 |Bronze |
| 5 o 6|Silver |
| 7 o 8 |Gold |
| Da 9 in su |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Tipo di nodo primario: guida alla capacità

Ecco le indicazioni per pianificare la capacità del tipo di nodo primario:

- **Numero di istanze di macchina virtuale per eseguire qualsiasi carico di lavoro di produzione in Azure:** È necessario specificare una dimensione minima del tipo del nodo primario pari a 5 e un livello di affidabilità di Silver.  
- **Numero di istanze delle VM per eseguire carichi di lavoro di test in Azure:** è possibile specificare una dimensione minima del tipo di nodo primario pari a 1 o 3. Il cluster a un nodo viene eseguito con una configurazione speciale, pertanto la scalabilità orizzontale di tale cluster non è supportata. Il cluster a un nodo non ha alcun livello di affidabilità, di conseguenza nel modello di Resource Manager è necessario rimuovere/non specificare tale configurazione (non è sufficiente non impostare il valore di configurazione). Se si configura il cluster a un nodo tramite il portale, la configurazione viene gestita automaticamente. I cluster a uno e tre nodi non sono supportati per l'esecuzione di carichi di lavoro di produzione. 
- **SKU DI VM:** Tipo di nodo primario è in esecuzione i servizi di sistema, quindi lo SKU di VM si sceglie, è necessario tenere in considerazione il massimo totale caricare si prevede di inserire in cluster. Per comprendere questo concetto, si pensi all'analogia seguente: il tipo di nodo primario è come i polmoni che forniscono ossigeno al cervello e quindi, se il cervello non riceve abbastanza ossigeno, il corpo ne risente. 

Poiché le esigenze in termini di capacità di un cluster dipendono dal carico di lavoro che si prevede di eseguire nel cluster, non è possibile offrire una guida valida per un carico di lavoro specifico. Di seguito sono tuttavia riportate indicazioni generali per poter iniziare.

Per i carichi di lavoro di produzione: 

- Si consiglia di dedicare i cluster di tipo di nodo primario a servizi di sistema e utilizzare i vincoli di posizionamento per distribuire l'applicazione ai tipi di nodo secondario.
- Lo SKU per le VM consigliato è Standard D3 o Standard D3_V2 o equivalente con un'unità SSD locale di almeno 14 GB.
- La versione minima supportata dello SKU per le VM è Standard D1 o Standard D1_V2 o equivalente con un'unità SSD locale di almeno 14 GB. 
- L'unità SSD locale di 14 GB è un requisito minimo. È consigliabile disporre di un'unità SSD di almeno 50 GB. Per i carichi di lavoro in esecuzione, in particolare quando eseguono contenitori Windows, sono necessari dischi di maggiori dimensioni. 
- Gli SKU per VM con core parziali, ad esempio Standard A0, non sono supportati per i carichi di lavoro di produzione.
- Lo SKU Standard A1 non è supportato per i carichi di lavoro di produzione per motivi di prestazioni.
- Le macchine virtuali con priorità bassa non sono supportate.

> [!WARNING]
> Modificare le dimensioni di SKU di VM in un nodo primario in un cluster in esecuzione è un'operazione di ridimensionamento illustrata nella documentazione relativa a [Scalare orizzontalmente il set di scalabilità di macchine virtuali](virtual-machine-scale-set-scale-node-type-scale-out.md).

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Tipo di nodo non primario: guida alla capacità per carichi di lavoro con stato

Queste linee guida sono relative ai carichi di lavoro con stato che usano [Reliable Collections o Reliable Actors](service-fabric-choose-framework.md) di Service Fabric in esecuzione nel tipo di nodo non primario.

**Numero di istanze di macchina virtuale:** Per i carichi di lavoro con stato, è consigliabile che vengono eseguiti con un numero di repliche minimo e di destinazione pari a 5. Nello stato stabile viene quindi creata una replica (da un set di repliche) in ogni dominio di errore e in ogni dominio di aggiornamento. L'intero concetto di livello di affidabilità per il tipo di nodo primario è un modo per specificare questa impostazione per i servizi di sistema. La stessa considerazione è quindi valida anche per i servizi con stato.

Per i carichi di lavoro di produzione, la dimensione minima consigliata per il tipo di nodo non primario è quindi 5, se si eseguono carichi di lavoro con stato.

**SKU DI VM:** Si tratta del tipo di nodo in cui sono in esecuzione i servizi dell'applicazione, in modo che lo SKU di VM si sceglie, necessario prendere in considerazione il carico di picco che si intende inserire in ogni nodo. Poiché le esigenze in termini di capacità del tipo di nodo dipendono dal carico di lavoro che si prevede di eseguire nel cluster, non è possibile offrire una guida valida per un carico di lavoro specifico. Di seguito sono tuttavia riportate indicazioni generali per poter iniziare

Per i carichi di lavoro di produzione 

- Lo SKU per le VM consigliato è Standard D3 o Standard D3_V2 o equivalente con un'unità SSD locale di almeno 14 GB.
- La versione minima supportata dello SKU per le VM è Standard D1 o Standard D1_V2 o equivalente con un'unità SSD locale di almeno 14 GB. 
- Gli SKU per VM con core parziali, ad esempio Standard A0, non sono supportati per i carichi di lavoro di produzione.
- Lo SKU Standard A1 non è supportato per i carichi di lavoro di produzione per motivi di prestazioni.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Tipo di nodo non primario: guida alla capacità per carichi di lavoro senza stato

Linee guida per i carichi di lavoro senza stato in esecuzione nel tipo di nodo non primario.

**Numero di istanze di macchina virtuale:** Per i carichi di lavoro senza stato, la dimensione di tipo minimo supportato di nodo non primario è 2. Ciò consente di eseguire due istanze senza stato dell'applicazione e consente al servizio di resistere alla perdita di un'istanza di una VM. 

**SKU DI VM:** Si tratta del tipo di nodo in cui sono in esecuzione i servizi dell'applicazione, in modo che lo SKU di VM si sceglie, necessario prendere in considerazione il carico di picco che si intende inserire in ogni nodo. Le esigenze di capacità del tipo di nodo sono determinate dal carico di lavoro che si prevede di eseguire nel cluster. Non è possibile offrire una guida valida per il carico di lavoro specifico.  Di seguito sono tuttavia riportate le indicazioni generali per iniziare.

Per i carichi di lavoro di produzione 

- Lo SKU per le VM consigliato è Standard D3 o Standard D3_V2 o equivalente. 
- La versione minima supportata dello SKU per le VM è Standard D1 o Standard D1_V2 o equivalente. 
- Gli SKU per VM con core parziali, ad esempio Standard A0, non sono supportati per i carichi di lavoro di produzione.
- Lo SKU Standard A1 non è supportato per i carichi di lavoro di produzione per motivi di prestazioni.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passaggi successivi
Dopo avere completato la pianificazione della capacità e configurato un cluster, vedere quanto segue:

* [Sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md)
* [Ridimensionamento di un cluster di Service Fabric](service-fabric-cluster-scaling.md)
* [Pianificazione del ripristino di emergenza](service-fabric-disaster-recovery.md)
* [Relazione tra i tipi di nodo di Service Fabric e i set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
