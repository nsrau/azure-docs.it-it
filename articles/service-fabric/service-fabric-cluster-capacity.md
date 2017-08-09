---
title: "Pianificazione della capacità del cluster di Service Fabric | Documentazione Microsoft"
description: "Considerazioni sulla pianificazione della capacità del cluster Service Fabric. Nodetypes, livelli di affidabilità e durata"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 9d5a4bef0c22f637a35390c6a8a245967fb02118
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerazioni sulla pianificazione della capacità del cluster Service Fabric
La pianificazione della capacità è un passaggio importante per qualsiasi distribuzione di produzione. Ecco alcuni aspetti da considerare nell'ambito di tale processo.

* Numero di tipi di nodo con cui il cluster deve iniziare
* Proprietà di ciascun tipo di nodo (dimensione, primario, per Internet, numero di VM e così via)
* Caratteristiche di affidabilità e durabilità del cluster

Ogni aspetto verrà ora esaminato brevemente.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Numero di tipi di nodo con cui il cluster deve iniziare
Prima di tutto è necessario stabilire per che cosa verrà usato il cluster che si sta creando e quali tipologie di applicazioni si intende distribuire nel cluster. Se lo scopo del cluster non è chiaro, è molto probabile che non si sia ancora pronti per iniziare il processo di pianificazione della capacità.

Stabilire il numero di tipi di nodo con cui il cluster deve iniziare.  Di ogni tipo di nodo viene eseguito il mapping a un set di scalabilità di macchine virtuali. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. Quindi la decisione relativa al numero di tipi di nodo si basa essenzialmente sulle considerazioni seguenti:

* L'applicazione ha più servizi, alcuni dei quali devono essere pubblici o per Internet? Le applicazioni tipiche contengono un servizio gateway front-end che riceve l'input da un client e uno o più servizi back-end che comunicano con i servizi front-end. In questo caso quindi si finirà per avere almeno due tipi di nodi.
* I servizi (che costituiscono l'applicazione) hanno esigenze diverse per l'infrastruttura, ad esempio cicli di CPU più elevati o una quantità maggiore di RAM? Ad esempio, si supponga che l'applicazione che si vuole distribuire contenga un servizio front-end e un servizio back-end. Il servizio front-end può essere eseguito in VM più piccole (ad esempio, di dimensioni D2), con porte aperte a Internet,  Il servizio back-end è invece a elevato utilizzo di calcolo e deve essere eseguito in VM più grandi (ad esempio di dimensione D4, D6, D15) senza connessione a Internet.
  
  In questo esempio, anche se è possibile decidere di inserire tutti i servizi in un unico tipo di nodo, è consigliabile inserirli in un cluster con due tipi di nodo,  perché in questo modo ogni tipo di nodo può avere proprietà distinte, ad esempio la connettività Internet o le dimensioni delle VM. Il numero di VM può essere ridimensionato anche in modo indipendente.  
* Poiché non è possibile prevedere cosa accadrà in futuro, è importante basarsi sulle condizioni attuali per decidere il numero di tipi di nodo necessari alle applicazioni per iniziare. Sarà sempre possibile aggiungere o rimuovere i tipi di nodi in seguito. Un cluster di Service Fabric deve avere almeno un tipo di nodo.

## <a name="the-properties-of-each-node-type"></a>Proprietà di ogni tipo di nodo
I **tipi di nodo** possono essere paragonati ai ruoli in Servizi cloud, poiché definiscono le dimensioni delle VM, il numero di VM e le relative proprietà. Ogni tipo di nodo definito in un cluster di Service Fabric viene configurato come set di scalabilità di macchine virtuali distinto. Un set di scalabilità di macchine virtuali è una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Essendo definito come set di scalabilità di macchine virtuali distinto, ogni tipo di nodo può essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse.

Vedere [questo documento](service-fabric-cluster-nodetypes.md) per altri dettagli sulla relazione dei tipi di nodo con il set di scalabilità di macchine virtuali, su come connettersi a una delle istanze tramite RDP, aprire nuove porte e così via.

Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario, ovvero il primo che si definisce nel portale, deve essere costituito da almeno cinque macchine virtuali per i cluster usati per i carichi di lavoro di produzione (o da almeno tre VM per i cluster di test). Se si sta creando il cluster con un modello di Resource Manager, cercare l'attributo **Primario** nella definizione del tipo di nodo. Nel tipo di nodo primario vengono inseriti i servizi di sistema di Service Fabric.  

### <a name="primary-node-type"></a>Tipo di nodo primario
Nel caso di un cluster con più tipi di nodo, è necessario sceglierne uno come primario. Ecco le caratteristiche di un tipo di nodo primario:

* Le **dimensioni minime delle macchine virtuali** per il tipo di nodo primario sono determinate dal **livello di durabilità** scelto. Il valore predefinito per il livello di durabilità è Bronze. Per sapere che cos'è il livello di durabilità e su quali valori può essere impostato, scorrere verso il basso.  
* Il **numero minimo delle macchine virtuali** per il tipo di nodo primario è determinato dal **livello di affidabilità** scelto. Il valore predefinito per il livello di affidabilità è Silver. Per sapere che cos'è il livello di affidabilità e su quali valori può essere impostato, scorrere verso il basso. 

 

* I servizi di sistema di Service Fabric (ad esempio, il servizio Cluster Manager o il servizio Image Store) vengono inseriti nel tipo di nodo primario e quindi l'affidabilità e la durabilità del cluster vengono determinate dal valore del livello di affidabilità e dal valore del livello di durabilità selezionato per il tipo di nodo primario.

![Screenshot di un cluster con due tipi di nodo ][SystemServices]

### <a name="non-primary-node-type"></a>Tipo di nodo non primario
Per un cluster con più tipi di nodo, è presente un tipo di nodo primario, mentre gli altri saranno non primari. Ecco le caratteristiche di un tipo di nodo non primario:

* Le dimensioni minime delle macchine virtuali per questo tipo di nodo sono determinate dal livello di durabilità scelto. Il valore predefinito per il livello di durabilità è Bronze. Per sapere che cos'è il livello di durabilità e su quali valori può essere impostato, scorrere verso il basso.  
* Il numero minimo di VM per questo tipo di nodo può essere uno. È tuttavia consigliabile scegliere questo numero in base al numero di repliche dell'applicazione o dei servizi che si vuole eseguire in questo tipo di nodo. Il numero di VM in un tipo di nodo può essere aumentato dopo avere distribuito il cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>Caratteristiche di durabilità del cluster
Il livello di durabilità viene usato per indicare al sistema i privilegi delle VM rispetto all'infrastruttura di Azure sottostante. Nel tipo di nodo primario questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di VM (ad esempio il riavvio di una VM, il re-imaging di una VM o la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi di sistema e i servizi con stato. Nei tipi di nodo non primari questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di macchina virtuale (ad esempio il riavvio di una VM, il re-imaging di una VM, la migrazione di una VM e così via) che hanno effetto sui requisiti relativi al quorum per i servizi con stato in esecuzione.

Questo privilegio viene espresso con i valori seguenti:

* Gold: i processi dell'infrastruttura possono essere sospesi per una durata di due ore per ogni dominio di aggiornamento. La durata Gold può essere abilitata solo per gli SKU VM con tutti i nodi come D15_V2, G5 e così via.
* Silver: i processi dell'infrastruttura possono essere sospesi per una durata di 10 minuti per ogni dominio di aggiornamento. È disponibile per tutte le VM standard a core singolo e superiori.
* Bronze: nessun privilegio. È l'impostazione predefinita ed è consigliabile se nel cluster si eseguono solo carichi di lavoro senza stato.

È necessario scegliere il livello di durabilità per ogni tipo di nodo. Si può scegliere un livello di durabilità Gold o Silver per un tipo di nodo e il livello Bronze per un altro nello stesso cluster. **È necessario mantenere un conteggio minimo di 5 nodi per qualsiasi tipo di nodo con durabilità Gold o Silver**. 

**Vantaggi dell'uso di livelli di durabilità Silver o Gold**
 
1. Riducono il numero dei passaggi necessari in un'operazione di riduzione del numero di istanze. La disattivazione del nodo e Remove-ServiceFabricNodeState vengono chiamati automaticamente.
2. Riducono il rischio di perdita di dati a causa di un'operazione di modifica sul posto dello SKU di VM avviata dal cliente o di operazioni sull'infrastruttura di Azure.
     
**Svantaggi dell'uso di livelli di durabilità Silver o Gold**
 
1. Le distribuzioni nel set di scalabilità di macchine virtuali e in altre risorse di Azure correlate possono subire ritardi o timeout oppure essere interamente bloccate da problemi nel cluster o a livello di infrastruttura. 
2. Le disattivazioni automatizzate dei nodi durante le operazioni sull'infrastruttura di Azure aumentano il numero di [eventi del ciclo di vita della replica](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle ), ad esempio di scambi di nodi primari.



### <a name="recommendations-on-when-to-use-silver-or-gold-durability-levels"></a>Raccomandazioni su quando usare livelli di durabilità Silver o Gold

Usare la durabilità Silver o Gold per tutti i tipi di nodo che ospitano servizi con stato di cui si prevede di ridurre frequentemente il numero di istanze di VM, quando si preferisce ritardare le operazioni di distribuzione in favore di una semplificazione di tali operazioni di riduzione del numero di istanze. Gli scenari di aumento del numero di istanze di VM non influiscono sulla scelta del livello di durabilità. Solo la riduzione del numero di istanze ha un impatto su tale scelta.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Raccomandazioni operative per il tipo di nodo impostato sul livello di durabilità Silver o Gold

1. Mantenere sempre integri il cluster e le applicazioni e verificare che le applicazioni rispondano in modo tempestivo a tutti gli [eventi del ciclo di vita della replica del servizio](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle), ad esempio al blocco della replica in compilazione.
2. Adottare modi più sicuri per eseguire una modifica di uno SKU di VM (aumento/riduzione delle prestazioni): la modifica dello SKU di VM di un set di scalabilità di macchine virtuali è un'operazione intrinsecamente non sicura e, se possibile, dovrebbe essere evitata. Di seguito è illustrato il processo che è possibile seguire per evitare i problemi comuni.
    - **Per i tipi di nodo non primari:** è consigliabile creare un nuovo set di scalabilità di macchine virtuali, modificare il vincolo di posizionamento del servizio per poter includere il nuovo tipo di nodo/set di scalabilità di macchine virtuali e quindi ridurre il numero di istanze del set di scalabilità di macchine virtuali precedente a 0, un nodo alla volta (perché la rimozione dei nodi non influisca sull'affidabilità del cluster).
    - **Per il tipo di nodo primario:** è consigliabile non modificare il relativo SKU di VM. Se lo scopo del nuovo SKU è la capacità, è consigliabile aggiungere altre istanze o, se possibile, creare un nuovo cluster. Se non sono disponibili alternative, apportare modifiche alla definizione del modello del set di scalabilità di macchine virtuali in modo che rifletta il nuovo SKU. Se il cluster include un solo tipo di nodo, verificare che tutte le applicazioni con stato rispondano in modo tempestivo a tutti gli [eventi del ciclo di vita della replica del servizio](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle), ad esempio al blocco della replica in compilazione, e che la durata della ricompilazione della replica del servizio sia inferiore a cinque minuti (per il livello di durabilità Silver). 
3. Mantenere un numero minimo di cinque nodi per tutti i set di scalabilità di macchine virtuali con ruolo di gestione abilitato.
4. Non eliminare istanze di VM casuali. Usare sempre la funzionalità di riduzione delle prestazioni del set di scalabilità di macchine virtuali. L'eliminazione di istanze di VM causali può creare squilibri nella distribuzione delle istanze di VM tra domini di aggiornamento e domini di errore. Tale squilibrio potrebbe influire negativamente sulla capacità del sistema di bilanciare correttamente il carico tra istanze dei servizi e repliche dei servizi.
6. Se si usa la scalabilità automatica, impostare le regole in modo che la riduzione del numero di istanze (ossia la rimozione di istanze di VM) venga eseguita un solo nodo alla volta. La riduzione delle prestazioni di più di un'istanza per volta non è sicura.
7. Se si riducono le prestazioni di un tipo di nodo primario, è consigliabile non ridurle mai più di quanto consentito dal livello di affidabilità.


## <a name="the-reliability-characteristics-of-the-cluster"></a>Caratteristiche di affidabilità del cluster
Il livello di affidabilità viene usato per impostare il numero di repliche dei servizi di sistema che si vuole eseguire in questo cluster nel tipo di nodo primario. I servizi di sistema nel cluster sono tanto più affidabili quanto più elevato è il numero di repliche.  

Il livello di affidabilità può avere i valori seguenti:

* Platinum: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a 9
* Gold: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a 7
* Silver: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a 5 
* Bronze: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a 3

> [!NOTE]
> Il livello di affidabilità scelto determina il numero minimo di nodi che deve avere il tipo di nodo primario. 
> 
> 


### <a name="recommendations-for-the-reliability-tier"></a>Raccomandazioni per il livello di affidabilità.

 Quando si aumentano o riducono le dimensioni del cluster (la somma delle istanze di VM in tutti i tipi di nodi), è necessario aggiornare l'affidabilità del cluster da un livello a un altro. Così facendo si attivano gli aggiornamenti del cluster necessari per modificare il totale di set di repliche dei servizi di sistema. Attendere che l'aggiornamento in corso venga completato prima di apportare altre modifiche al cluster, come l'aggiunta di nodi.  È possibile monitorare lo stato di avanzamento dell'aggiornamento in Service Fabric Explorer oppure eseguendo [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

Ecco la raccomandazione per la scelta del livello di affidabilità.

| **Dimensione del cluster** | **Livello di affidabilità** |
| --- | --- |
| 1 |Non specificare il parametro Livello di affidabilità perché viene calcolato automaticamente dal sistema |
| 3 |Bronze |
| 5 o 6|Silver |
| 7 o 8 |Gold |
| Da 9 in su |Platinum |




## <a name="primary-node-type---capacity-guidance"></a>Tipo di nodo primario: guida alla capacità

Ecco le indicazioni per pianificare la capacità del tipo di nodo primario

1. **Numero di istanze delle VM per eseguire qualsiasi carico di lavoro di produzione in Azure:** è necessario specificare una dimensione minima del tipo di nodo primario pari a 5. 
2. **Numero di istanze delle VM per eseguire carichi di lavoro di test in Azure:** è possibile specificare una dimensione minima del tipo di nodo primario pari a 1 o 3. Il cluster a un nodo viene eseguito con una configurazione speciale, pertanto la scalabilità orizzontale di tale cluster non è supportata. Il cluster a un nodo non ha alcun livello di affidabilità, di conseguenza nel modello di Resource Manager è necessario rimuovere/non specificare tale configurazione (non è sufficiente non impostare il valore di configurazione). Se si configura il cluster a un nodo tramite il portale, la configurazione viene gestita automaticamente. I cluster a uno e tre nodi non sono supportati per l'esecuzione di carichi di lavoro di produzione. 
3. **SKU di VM:** nel tipo di nodo primario vengono eseguiti i servizi di sistema, quindi lo SKU della VM scelto deve tenere in considerazione il carico massimo totale che si prevede di inserire nel cluster. Per comprendere questo concetto, si pensi all'analogia seguente: il tipo di nodo primario è come i polmoni che forniscono ossigeno al cervello e quindi, se il cervello non riceve abbastanza ossigeno, il corpo ne risente. 

Poiché le esigenze in termini di capacità di un cluster dipendono dal carico di lavoro che si prevede di eseguire nel cluster, non è possibile offrire una guida valida per un carico di lavoro specifico. Di seguito sono tuttavia riportate indicazioni generali per poter iniziare.

Per i carichi di lavoro di produzione 


- Lo SKU per le VM consigliato è Standard D3 o Standard D3_V2 o equivalente con un'unità SSD locale di almeno 14 GB.
- La versione minima supportata dello SKU per le VM è Standard D1 o Standard D1_V2 o equivalente con un'unità SSD locale di almeno 14 GB. 
- Gli SKU per VM con core parziali, ad esempio Standard A0, non sono supportati per i carichi di lavoro di produzione.
- Lo SKU Standard A1 non è supportato per i carichi di lavoro di produzione per motivi di prestazioni.


## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Tipo di nodo non primario: guida alla capacità per carichi di lavoro con stato

Queste linee guida sono relative ai carichi di lavoro con stato che usano [Reliable Collections o Reliable Actors](service-fabric-choose-framework.md) di Service Fabric in esecuzione nel tipo di nodo non primario.


**Numero di istanze delle VM:** è consigliabile eseguire i carichi di lavoro di produzione con stato con un numero di repliche di destinazione di almeno 5 istanze. Nello stato stabile viene quindi creata una replica (da un set di repliche) in ogni dominio di errore e in ogni dominio di aggiornamento. L'intero concetto di livello di affidabilità per il tipo di nodo primario è un modo per specificare questa impostazione per i servizi di sistema. La stessa considerazione è quindi valida anche per i servizi con stato.

Per i carichi di lavoro di produzione, la dimensione minima consigliata per il tipo di nodo non primario è quindi 5, se si eseguono carichi di lavoro con stato.


**SKU di VM:** in questo tipo di nodo sono in esecuzione i servizi dell'applicazione, quindi lo SKU per le VM scelto deve tenere in considerazione il carico massimo che si prevede di inserire in ogni nodo. Poiché le esigenze in termini di capacità del tipo di nodo dipendono dal carico di lavoro che si prevede di eseguire nel cluster, non è possibile offrire una guida valida per un carico di lavoro specifico. Di seguito sono tuttavia riportate indicazioni generali per poter iniziare.

Per i carichi di lavoro di produzione 

- Lo SKU per le VM consigliato è Standard D3 o Standard D3_V2 o equivalente con un'unità SSD locale di almeno 14 GB.
- La versione minima supportata dello SKU per le VM è Standard D1 o Standard D1_V2 o equivalente con un'unità SSD locale di almeno 14 GB. 
- Gli SKU per VM con core parziali, ad esempio Standard A0, non sono supportati per i carichi di lavoro di produzione.
- Lo SKU Standard A1 in particolare non è supportato per i carichi di lavoro di produzione per motivi di prestazioni.


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Tipo di nodo non primario: guida alla capacità per carichi di lavoro senza stato

Linee guida per i carichi di lavoro senza stato in esecuzione nel tipo di nodo non primario.

**Numero di istanze delle VM:** per i carichi di lavoro di produzione senza stato, la dimensione minima supportata per il tipo di nodo non primario è pari a 2. Ciò consente di eseguire due istanze senza stato dell'applicazione e consente al servizio di resistere alla perdita di un'istanza di una VM. 

> [!NOTE]
> Se il cluster è in esecuzione in una versione di Service Fabric precedente alla 5.6, a causa di un difetto del runtime (corretto nella versione 5.6), passando a un tipo di nodo non primario inferiore a 5, l'integrità del cluster viene meno, finché non si chiama il [comando Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) con il nome di nodo appropriato. Per altre informazioni, vedere [Aumentare o ridurre il numero di istanze del cluster di Service Fabric](service-fabric-cluster-scale-up-down.md).
> 
>

**SKU di VM:** in questo tipo di nodo sono in esecuzione i servizi dell'applicazione, quindi lo SKU per le VM scelto deve tenere in considerazione il carico massimo che si prevede di inserire in ogni nodo. Poiché le esigenze in termini di capacità del tipo di nodo dipendono dal carico di lavoro che si prevede di eseguire nel cluster, non è possibile offrire una guida valida per un carico di lavoro specifico. Di seguito sono tuttavia riportate indicazioni generali per poter iniziare.

Per i carichi di lavoro di produzione 


- Lo SKU per le VM consigliato è Standard D3 o Standard D3_V2 o equivalente. 
- La versione minima supportata dello SKU per le VM è Standard D1 o Standard D1_V2 o equivalente. 
- Gli SKU per VM con core parziali, ad esempio Standard A0, non sono supportati per i carichi di lavoro di produzione.
- Lo SKU Standard A1 non è supportato per i carichi di lavoro di produzione per motivi di prestazioni.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passaggi successivi
Dopo avere completato la pianificazione della capacità e configurato un cluster, vedere quanto segue:

* [Sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md)
* [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)
* [Relazione tra i tipi di nodo di Service Fabric e i set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

