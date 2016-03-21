<properties
   pageTitle="Cluster Resource Manager di Service Fabric: Integrazione della gestione"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>


# Integrazione della gestione
Pur non essendo il componente principale di Service Fabric che si occupa delle operazioni di gestione, come gli aggiornamenti dell'applicazione, Resource Manager è tuttavia coinvolto. Il primo modo in cui Resource Manager contribuisce alla gestione è tracciando lo stato desiderato del cluster e dei servizi al suo interno da una prospettiva di assegnazione delle risorse e bilanciamento del carico per poi procedere all'alterazione tramite il sottosistema di integrità di Service Fabric quando rileva degli errori. Un altro fattore di integrazione riguarda il funzionamento degli aggiornamenti. In particolare durante gli aggiornamenti, alcune modalità di funzionamento di Resource Manager cambiano e vengono richiamati alcuni comportamenti speciali. Parleremo di entrambi questi aspetti di seguito.

## Integrazione di integrità
Resource Manager tiene costantemente traccia delle regole definite per i servizi e genera errori ed avvisi di integrità se non riesce a soddisfare tali regole. Ad esempio, se un nodo è oltre la sua capacità e Resource Manager non riesce a correggere la situazione, verrà generato un avviso di integrità che indica quale nodo supera la capacità e per quali metriche.

Un altro esempio di situazione in cui Resource Manager genera avvisi di integrità è se è stato definito un vincolo di posizionamento (ad esempio “NodeColor == Blue”) e viene rilevata una violazione di tale vincolo. Questo avviene per i vincoli personalizzati e per i vincoli predefiniti, quali distribuzione di domini di errore e aggiornamento, che Resource Manager applica per conto dell'utente. Di seguito è riportato un esempio di rapporto di integrità. Questo rapporto di integrità riguarda una delle partizioni di servizio del sistema perché le repliche di tale partizione sono temporaneamente compresse in un numero insufficiente di domini di errore, condizione che potrebbe verificarsi a causa di una serie di errori:

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_FaultDomain', HealthState='Warning', ConsiderWarningAsError=false.

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
                        Property              : ReplicaConstraintViolation_FaultDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: FaultDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Ecco che cosa indica questo messaggio di stato:

1.	Tutte le repliche sono integre (questa è la priorità principale di Service Fabric)
2.	Il vincolo di distribuzione dominio di errore è stato violato, vale a dire che in un particolare dominio di errore c'è un numero di repliche superiore a quello previsto per la partizione
3.	Il nodo che contiene la replica che causa la violazione (il nodo con ID 3d1a4a68b2592f55125328cd0f8ed477)
4.	Quando si è verificato il problema (10/8/2015 19:13:02). Questi dati sono ideali per un avviso generato nell'ambiente di produzione perché comunicano all'utente che si è verificato un errore e che è opportuno indagare. In questo caso, ad esempio, sarebbe opportuno riuscire a scoprire perché Resource Manager ha scelto di comprimere le repliche nel dominio di errore. Possibili spiegazioni sono che tutti i nodi degli altri domini di errore erano inattivi e che non erano disponibili sufficienti altri domini inutilizzati oppure, se il numero di domini attivi era sufficiente, un altro elemento ha reso non validi i nodi di questi altri domini di errore, ad esempio, un criterio InvalidDomain nel servizio.

Supponiamo, tuttavia, di voler creare un servizio o che Resource Manager stia tentando di trovare una posizione in cui inserire alcuni servizi, ma che non ci siano soluzioni accettabili. Questa situazione potrebbe accadere per diversi motivi, ma in genere è dovuta a una delle due condizioni seguenti:

1.	Una qualche condizione temporanea ha reso impossibile inserire correttamente questa istanza del servizio o replica
2.	I requisiti del servizio non sono configurati correttamente e non possono essere soddisfatti.

In ognuna di queste condizioni si disporrà di un rapporto di integrità di Resource Manager con informazioni utili per determinare che cosa sta succedendo e perché il servizio non può essere inserito. Questo processo viene definito "sequenza di eliminazione del vincolo". Durante la procedura si esaminano i singoli vincoli che agiscono sul servizio e si vede che cosa eliminano. In questo modo, quando non si riesce a inserire gli elementi, è possibile visualizzare i nodi eliminati e perché. Esaminiamo ciascuno dei vincoli indicati nei rapporti di integrità per capire che cosa ognuno verifica. Si noti che, nella maggior parte dei casi, non si assisterà all'eliminazione dei nodi da parte di questi vincoli poiché, per impostazione predefinita, i vincoli sono al livello soft o ottimizzato (come indicato in precedenza). Sarebbe possibile vederli se fossero capovolti o considerati vincoli rigidi. Vengono pertanto presentati qui per motivi di completezza:
-	ReplicaExclusionStatic e ReplicaExclusionDynamic: vincolo interno che indica che durante la ricerca è stato deciso che due repliche dovevano essere posizionate sul nodo, operazione che non è consentita. ReplicaExclusionStatic e ReplicaExclusionDynamic sono regole quasi identiche. Il vincolo ReplicaExclusionDynamic afferma che è stato impossibile collocare nel nodo questa replica perché l'unica soluzione proposta già aveva inserito una replica. Questa affermazione è diversa dall'esclusione di ReplicaExclusionStatic che indica non un possibile ma un effettivo conflitto: esiste già una replica nel nodo. È ambiguo? Sì. È molto importante? No. Basti sapere che se si nota una sequenza di eliminazione del vincolo contenente ReplicaExclusionStatic o ReplicaExclusionDynamic significa che Resource Manager ritiene che non vi siano nodi sufficienti per inserire tutte le repliche. Gli altri vincoli possono, in genere, indicarci come è possibile che il numero di nodi sia insufficiente.
-	PlacementConstraint: se viene visualizzato questo messaggio significa che sono stati eliminati alcuni nodi perché non rispettavano i vincoli di posizionamento del servizio. I vincoli di posizionamento attualmente configurati vengono indicati come parte di questo messaggio.
-	NodeCapacity: se viene visualizzato questo vincolo significa che non è stato possibile inserire le repliche nei nodi indicati perché l'operazione avrebbe fatto superare la capacità del nodo
-	Affinity: questo vincolo indica che non è stato possibile inserire la replica nei nodi interessati perché questa operazione avrebbe generato una violazione del vincolo Affinity.
-	FaultDomain & UpgradeDomain: questo vincolo elimina i nodi se l'inserimento della replica nei nodi indicati comporterebbe la compressione in un particolare dominio di aggiornamento o di errore
-	PreferredLocation: in genere non si dovrebbe assistere alla rimozione di nodi dalla soluzione da parte di questo vincolo perché, per impostazione predefinita, è riservato all'ottimizzazione. Inoltre, il vincolo PreferredLocation è presente, in genere, solo durante gli aggiornamenti, quando viene usato per riportare le repliche alla posizione originaria in cui si trovavano all'inizio dell'aggiornamento, anche se è possibile trovarlo in altri ambiti.

## Aggiornamenti
Resource Manager contribuisce anche alle operazioni di aggiornamento di applicazioni e cluster per garantire che l'aggiornamento avvenga correttamente e che le regole e le prestazioni del cluster non vengano compromesse.

### Applicazione continua delle regole
La cosa principale da tenere presente è che le regole, vale a dire i rigidi controlli su aspetti come il posizionamento, vengono applicate anche durante gli aggiornamenti. Si potrebbe pensare che questa affermazione è scontata, ma viene ribadita per chiarezza. Il risvolto positivo di questa situazione è che se ci si era premurati di fare in modo che alcuni carichi di lavoro non venissero eseguiti su determinati nodi, si può avere la certezza che questo non avverrà nemmeno in fase di aggiornamento senza richiedere l'intervento di Operations. Se il proprio ambiente è configurato con un numero elevato di vincoli, è possibile che gli aggiornamenti richiedano molto tempo perché non vi saranno molte opzioni in termini di dove spostare un servizio che ha bisogno di essere disattivato per l'applicazione di patch.

### Sostituzioni intelligenti
Quando inizia un aggiornamento, Resource Manager crea un'istantanea della disposizione attuale dei cluster e tenta di ripristinarla quando l'aggiornamento si è concluso. La logica è semplice: in primo luogo si assicura che l'aggiornamento comporti solo due transizioni per ogni servizio, vale a dire lo spostamento al di fuori del nodo e lo spostamento al suo interno. In secondo luogo, si assicura che l'aggiornamento non abbia un impatto sul layout del cluster. Se il cluster era organizzato in modo efficiente prima dell'aggiornamento, lo sarà anche dopo.

### Varianza ridotta
Un'altra cosa che si verifica durante gli aggiornamenti è che Resource Manager disattiva il bilanciamento del carico per l'entità da aggiornare. Pertanto, se si dispone di due istanze diverse dell'applicazione e si avvia l'aggiornamento su una di esse, il bilanciamento del carico verrà sospeso per tale istanza dell'applicazione, ma non per l'altra. Impedendo il bilanciamento reattivo si impedisce una reazione avversa all'aggiornamento da parte di Resource Manager ("Oh no! Un nodo vuoto! Riempiamolo con qualsiasi cosa!") che comporterebbe una notevole quantità di movimenti aggiuntivi per i servizi del cluster che verrebbero poi vanificati nel momento in cui i servizi devono essere riportati nei nodi al termine dell'aggiornamento. Se si tratta di un aggiornamento del cluster, il bilanciamento del carico dell'intero cluster viene sospeso per la durata dell'aggiornamento. Le verifiche dei vincoli, che garantiscono che le regole vengano applicate, restano attive.

### Regole meno severe
Quello che accade spesso durante gli aggiornamenti è che si desidera che l'aggiornamento venga portato a termine anche se il cluster è nel complesso piuttosto pieno o carico di vincoli. L'aspetto di come farlo è già stato affrontato ma durante gli aggiornamenti questa necessità è ancora più importante perché in genere la percentuale del cluster resa inattiva in un dato momento è tra il 5 e il 20 percento e il carico di lavoro deve essere spostato da qualche parte. È qui che entra in gioco il concetto di capacità di memorizzazione nel buffer che abbiamo citato prima. Mentre la capacità di memorizzazione nel buffer viene rispettata durante il normale funzionamento, durante gli aggiornamenti Resource Manager sfrutta la capacità totale del buffer.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Cenni iniziali su Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

<!---HONumber=AcomDC_0309_2016-->