<properties
   pageTitle="Cluster Resource Manager di Service Fabric - Integrazione della gestione | Microsoft Azure"
   description="Panoramica dei punti di integrazione tra Cluster Resource Manager e le funzionalità di gestione di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>  


# Integrazione di Cluster Resource Manager con la gestione dei cluster di Service Fabric
Pur non essendo il componente principale di Service Fabric che si occupa delle operazioni di gestione, come gli aggiornamenti dell'applicazione, Cluster Resource Manager partecipa tuttavia al processo. Il primo modo in cui Cluster Resource Manager contribuisce alla gestione consiste nel monitorare lo stato desiderato del cluster e dei servizi in esso contenuti da una prospettiva di bilanciamento e assegnazione delle risorse e nell'inviare report sull'integrità quando non è possibile applicare al cluster la configurazione desiderata. Un esempio può essere una capacità insufficiente oppure regole in conflitto in merito al posizionamento di un servizio. Un altro fattore di integrazione riguarda il funzionamento degli aggiornamenti: durante gli aggiornamenti, il comportamento di Cluster Resource Manager cambia. Parleremo di entrambi questi aspetti di seguito.

## Integrazione di integrità
Cluster Resource Manager tiene costantemente traccia delle regole definite per i servizi e delle capacità disponibili sui nodi e nel cluster e genera errori e avvisi di integrità se non riesce a soddisfare tali regole o se la capacità non è sufficiente. Ad esempio, se un nodo è oltre la sua capacità e Cluster Resource Manager non riesce a correggere la situazione, verrà generato un avviso di integrità che indica quale nodo supera la capacità e per quali metriche.

Un altro esempio di situazione in cui Resource Manager genera avvisi di integrità è se è stato definito un vincolo di posizionamento (ad esempio “NodeColor == Blue”) e viene rilevata una violazione di tale vincolo. Questo avviene per i vincoli personalizzati e per i vincoli predefiniti, come la distribuzione automatica di domini di errore e di aggiornamento.

Di seguito è riportato un esempio di rapporto di integrità. Questo report sull'integrità riguarda una delle partizioni di servizio del sistema perché le repliche di tale partizione sono temporaneamente compresse in un numero insufficiente di domini di aggiornamento, condizione che potrebbe verificarsi a causa di una serie di errori:

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Ecco che cosa indica questo messaggio di stato:

1.	Tutte le repliche sono integre (questa è la priorità principale di Service Fabric)
2.	Il vincolo di distribuzione del dominio di aggiornamento è stato violato, vale a dire che in un particolare dominio di aggiornamento è presente un numero di repliche superiore a quello previsto per la partizione
3.	Il nodo che contiene la replica che causa la violazione (il nodo con ID 3d1a4a68b2592f55125328cd0f8ed477)
4.	Quando si è verificato il problema (10/8/2015 19:13:02: 00)

Questi dati sono ideali per un avviso generato nell'ambiente di produzione perché comunicano all'utente che si è verificato un errore e che è opportuno indagare. In questo caso, ad esempio, sarebbe opportuno comprendere perché Resource Manager ha scelto di comprimere le repliche nel dominio di aggiornamento. Possibili spiegazioni sono che tutti i nodi degli altri domini di aggiornamento erano inattivi e che non erano disponibili altri domini inutilizzati oppure, se il numero di domini attivi era sufficiente, un altro elemento ha reso non validi i nodi di questi altri domini di aggiornamento, ad esempio un criterio di posizionamento del servizio o una capacità insufficiente.

Supponiamo, tuttavia, di voler creare un servizio o che Resource Manager stia tentando di trovare una posizione in cui inserire alcuni servizi, ma che non ci siano soluzioni accettabili. Questa situazione potrebbe accadere per diversi motivi, ma in genere è dovuta a una delle due condizioni seguenti:

1.	Una qualche condizione temporanea ha reso impossibile inserire correttamente questa istanza del servizio o replica
2.	I requisiti del servizio non sono configurati correttamente e non possono essere soddisfatti.

In ognuna di queste condizioni si disporrà di un rapporto di integrità di Cluster Resource Manager con informazioni utili per determinare che cosa sta succedendo e perché il servizio non può essere inserito. Questo processo viene definito "sequenza di eliminazione del vincolo". Durante la procedura il sistema esamina i singoli vincoli che agiscono sul servizio e registra ciò che eliminano. In questo modo, quando non si riesce a inserire i servizi, è possibile visualizzare i nodi eliminati e la relativa motivazione.

## Tipi di vincolo
Esaminiamo ciascuno dei vincoli indicati nei rapporti di integrità per capire che cosa ognuno verifica. Si noti che, nella maggior parte dei casi, non si assisterà all'eliminazione dei nodi da parte di alcuni di questi vincoli poiché, per impostazione predefinita, i vincoli sono al livello soft o ottimizzato (all'interno dell'articolo sono disponibili maggiori informazioni sulle priorità dei vincoli). È tuttavia possibile visualizzare messaggi di integrità correlati a questi vincoli se sono configurati come vincoli "Hard" o nei rari casi in cui determinano l'eliminazione dei nodi. Per questo motivo, vengono qui presentati per completezza:

-	ReplicaExclusionStatic e ReplicaExclusionDynamic: vincolo interno che indica che durante la ricerca si è verificata una situazione in cui due repliche con stato o istanze senza stato dalla stessa partizione dovevano essere posizionate sullo stesso nodo, operazione non consentita. ReplicaExclusionStatic e ReplicaExclusionDynamic sono regole quasi identiche. Il vincolo ReplicaExclusionDynamic afferma che è stato impossibile collocare nel nodo questa replica perché l'unica soluzione proposta già aveva inserito una replica. Questa affermazione è diversa dall'esclusione di ReplicaExclusionStatic che indica non un possibile ma un effettivo conflitto: esiste già una replica nel nodo. È ambiguo? Sì. È molto importante? No. Basti sapere che se si nota una sequenza di eliminazione del vincolo contenente ReplicaExclusionStatic o ReplicaExclusionDynamic significa che Cluster Resource Manager ritiene che non vi siano nodi sufficienti per inserire tutte le repliche. Gli altri vincoli possono, in genere, indicarci come è possibile che il numero di nodi sia insufficiente.
-	PlacementConstraint: se viene visualizzato questo messaggio significa che sono stati eliminati alcuni nodi perché non rispettavano i vincoli di posizionamento del servizio. I vincoli di posizionamento attualmente configurati vengono indicati come parte di questo messaggio. Questo è generalmente normale se si dispone di un vincolo di posizionamento. Tuttavia, questo risultato potrebbe essere visualizzato se è presente un bug nel vincolo di posizionamento che determina l'eliminazione di troppi nodi.
-	NodeCapacity: se viene visualizzato questo vincolo significa che non è stato possibile inserire le repliche nei nodi indicati perché l'operazione avrebbe fatto superare la capacità del nodo
-	Affinity: questo vincolo indica che non è stato possibile inserire la replica nei nodi interessati perché questa operazione avrebbe generato una violazione del vincolo Affinity.
-	FaultDomain & UpgradeDomain: questo vincolo elimina i nodi se l'inserimento della replica nei nodi indicati comporterebbe la compressione in un particolare dominio di aggiornamento o di errore. Alcuni esempi che illustrano questo vincolo sono presentati nell'argomento sui [vincoli dei domini di aggiornamento e di errore e il relativo comportamento](service-fabric-cluster-resource-manager-cluster-description.md)
-	PreferredLocation: in genere non si dovrebbe assistere alla rimozione di nodi dalla soluzione da parte di questo vincolo perché, per impostazione predefinita, è riservato all'ottimizzazione. Inoltre, il vincolo PreferredLocation è presente, in genere, solo durante gli aggiornamenti, quando viene usato per riportare le repliche alla posizione originaria in cui si trovavano all'inizio dell'aggiornamento, anche se è possibile trovarlo in altri ambiti.

### Priorità dei vincoli
Dato l'elevato numero di questi vincoli è possibile ritenere che i vincoli di posizionamento siano i più importanti del sistema. Si può pensare che sia preferibile violare altri vicoli, ad esempio affinità e capacità, pur di non violare mai i vincoli di posizionamento.

Questa operazione è possibile. I vincoli possono essere configurati con diversi livelli di imposizione, ma si riducono a "Hard" (0), "Soft" (1), "Optimization" (2) e "Off" (-1). La maggior parte dei vincoli è definita come "Hard" per impostazione predefinita perché, ad esempio, la maggior parte degli utenti non ritiene normalmente che si possa scendere a compromessi sulla capacità. Quasi tutti i vincoli sono in ogni caso definiti come "Hard" o "Soft". Nelle situazioni complesse è tuttavia possibile usare altre impostazioni. Se ad esempio si vuole assicurare che l'affinità venga sempre violata per risolvere problemi di capacità del nodo, è possibile impostare la priorità del vincolo di affinità su "Soft" (1) e lasciare il vincolo di capacità impostato su "Hard" (0). Le diverse priorità dei vincoli sono anche il motivo per il quale alcuni avvisi di violazione dei vincoli vengono visualizzati più spesso di altri: esistono vincoli che l'utente è disposto a violare temporaneamente. Si noti che questi livelli non implicano che uno specifico vincolo non verrà mai o verrà sempre violato, ma solo che esiste un ordine in cui vengono imposti per poter raggiungere i compromessi adeguati se è impossibile soddisfare tutti i vincoli.

La configurazione e i valori di priorità predefiniti per i diversi vincoli sono elencati di seguito:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

Si noti che sono presenti vincoli definiti per i domini di aggiornamento e di errore e che il vincolo del dominio di aggiornamento è "Soft". È anche presente il vincolo "PreferredLocation" con una priorità. Cos'è?

Viene prima di tutto attuata l'intenzione di mantenere i servizi distribuiti tra domini di errore e di aggiornamento come vincolo all'interno del motore di Resource Manager. In passato ci sono state magari alcune occasioni in cui era necessario essere particolarmente rigidi in merito al posizionamento per i domini di errore e di aggiornamento e un paio di casi in cui è stato necessario ignorare completamente questo aspetto, anche se per poco tempo. La possibilità di scelta e la flessibilità dell'infrastruttura dei vincoli sono quindi aspetti positivi. Nella maggior parte dei casi, tuttavia, i vincoli del dominio di aggiornamento sono impostati come "Soft", vale a dire che se Resource Manager deve comprimere temporaneamente alcune repliche di un dominio di aggiornamento per gestire, ad esempio, un aggiornamento, una serie di errori concomitanti o altre violazioni di vincoli "Hard", ciò non costituirà un problema. Ciò in genere non accade, a meno che non siano presenti numerosi errori o altre discrepanze nel sistema che impediscono il posizionamento corretto; se l'ambiente è configurato correttamente, il vincolo del dominio di aggiornamento viene sempre rispettato.

Il vincolo PreferredLocation è leggermente diverso ed è quindi l'unico vincolo impostato su "Optimization". Usiamo questo vincolo durante gli aggiornamenti in corso per provare a posizionare nuovamente i servizi dove si trovavano in origine prima dell'aggiornamento. Esistono molti motivi per i quali questo approccio potrebbe non funzionare nella pratica, ma si tratta comunque di un'utile opzione di ottimizzazione. Verranno fornite altre informazioni nell'argomento che illustra in che modo Cluster Resource Manager consente di gestire gli aggiornamenti.

## Aggiornamenti
Cluster Resource Manager contribuisce anche alle operazioni di aggiornamento di applicazioni e cluster per garantire che l'aggiornamento avvenga correttamente e che le regole e le prestazioni del cluster non vengano compromesse.

### Applicazione continua delle regole
La cosa principale da tenere presente è che le regole, vale a dire i rigidi vincoli su aspetti come il posizionamento, vengono applicate anche durante gli aggiornamenti. Si potrebbe pensare che questa affermazione è scontata, ma viene ribadita per chiarezza. Il risvolto positivo è che se si intende garantire che alcuni carichi di lavoro non vengano eseguiti su determinati nodi, queste regole verranno imposte automaticamente anche durante l'aggiornamento. Se il proprio ambiente è configurato con un numero elevato di vincoli, è possibile che gli aggiornamenti richiedano molto tempo perché non vi saranno molte opzioni di posizionamento di un servizio se questo o il nodo in cui risiede deve essere disattivato per un aggiornamento.

### Sostituzioni intelligenti
Quando inizia un aggiornamento, Resource Manager crea un'istantanea della disposizione attuale dei cluster e tenta di ripristinarla quando l'aggiornamento si è concluso. La logica è semplice: in primo luogo si assicura che l'aggiornamento comporti solo due transizioni per ogni replica o istanza del servizio, vale a dire lo spostamento al di fuori del nodo e lo spostamento al suo interno. Si assicura in secondo luogo che l'aggiornamento non abbia un impatto sul layout del cluster. Se il cluster era organizzato in modo efficiente prima dell'aggiornamento, lo sarà anche dopo.

### Varianza ridotta
Un altro evento che si verifica durante gli aggiornamenti è che Cluster Resource Manager disattiva il bilanciamento del carico per l'entità da aggiornare. Pertanto, se si dispone di due istanze diverse dell'applicazione e si avvia l'aggiornamento su una di esse, il bilanciamento del carico verrà sospeso per tale istanza dell'applicazione, ma non per l'altra. Impedendo il bilanciamento reattivo si impedisce una reazione non necessaria all'aggiornamento stesso ("Oh no! Un nodo vuoto! Occupiamolo con qualsiasi cosa!") che comporterebbe una notevole quantità di movimenti aggiuntivi per i servizi del cluster che verrebbero poi vanificati nel momento in cui i servizi devono essere riportati nei nodi al termine dell'aggiornamento. Se si tratta di un aggiornamento del cluster, il bilanciamento del carico dell'intero cluster viene sospeso per la durata dell'aggiornamento. Le verifiche dei vincoli, che garantiscono che le regole vengano applicate, restano attive e solo il ribilanciamento proattivo viene disabilitato.

### Regole meno severe
Quello che accade spesso durante gli aggiornamenti è che si desidera che l'aggiornamento venga portato a termine anche se il cluster è nel complesso piuttosto pieno o carico di vincoli. Durante gli aggiornamenti la necessità di gestire la capacità del cluster è ancora più importante perché in genere la percentuale del cluster resa inattiva in un dato momento è tra il 5 e il 20 percento e il carico di lavoro deve essere spostato da qualche parte. È qui che entra in gioco il concetto di [capacità in buffering](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity). Mentre la capacità in buffering viene rispettata durante il normale funzionamento, negli aggiornamenti Cluster Resource Manager sfrutta la capacità totale del buffer.

## Passaggi successivi
- Partire dall'inizio e vedere l'[introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

<!---HONumber=AcomDC_0824_2016-->