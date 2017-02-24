---
title: Cluster Resource Manager di Service Fabric - Integrazione della gestione | Microsoft Docs
description: "Panoramica dei punti di integrazione tra Cluster Resource Manager e le funzionalità di gestione di Service Fabric."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: b2428f93680c12c76000fa8eb1a7138f72a8efe7
ms.openlocfilehash: 9d67f089f4aba03e846a8fe020a91b6b1ac6ea48


---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integrazione di Cluster Resource Manager con la gestione dei cluster di Service Fabric
Pur non essendo il componente principale di Service Fabric che si occupa delle operazioni di gestione, come gli aggiornamenti dell'applicazione, Cluster Resource Manager partecipa tuttavia al processo. Il primo modo in cui Cluster Resource Manager facilita la gestione è monitorando lo stato desiderato del cluster e i servizi al suo interno. Cluster Resource Manager invia report di integrità quando non riesce ad attivare la configurazione del cluster desiderata. Ad esempio quando la capacità è insufficiente o ci sono regole conflittuali su dove inserire un servizio. Un altro aspetto dell'integrazione ha a che fare con il funzionamento degli aggiornamenti. Durante gli aggiornamenti Cluster Resource Manager modifica leggermente il suo comportamento. Parleremo di entrambi questi aspetti dell'integrazione di seguito.

## <a name="health-integration"></a>Integrazione di integrità
Cluster Resource Manager monitora costantemente le regole definite per i servizi e le capacità disponibili nei nodi e nel cluster. Se non riesce a soddisfare queste regole o se la capacità è insufficiente, vengono generati errori e avvisi di integrità. Ad esempio, se un nodo ha capacità in eccesso, Cluster Resource Manager tenterà di risolvere la situazione spostando i servizi. Se non riesce a risolvere la situazione, genera un avviso di integrità che indica quale nodo ha capacità in eccesso e per quali metriche.

Un altro esempio di avvisi di integrità di Resource Manager riguarda le violazioni dei vincoli di posizionamento. Se ad esempio è stato definito un vincolo di posizionamento (come `“NodeColor == Blue”`) e Resource Manager rileva una violazione del vincolo, genererà un avviso di integrità. Questo vale per i vincoli personalizzati e i vincoli predefiniti (ad esempio i vincoli di dominio per errori e aggiornamenti).

Di seguito è riportato un esempio di rapporto di integrità. In questo caso il report di integrità riguarda una delle partizioni del servizio di sistema. Il messaggio di integrità indica che le repliche di quella partizione sono temporaneamente compresse in un numero troppo basso di domini di aggiornamento.

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

1. Tutte le repliche sono integre (questa è la priorità principale di Service Fabric)
2. Il vincolo di distribuzione del dominio di aggiornamento è stato violato, vale a dire che in un particolare dominio di aggiornamento è presente un numero di repliche superiore a quello previsto per la partizione
3. Il nodo che contiene la replica che causa la violazione (il nodo con ID 3d1a4a68b2592f55125328cd0f8ed477)
4. Quando la segnalazione si è verificata (10/8/2015 19:13:02)

Informazioni come queste producono avvisi che vengono generati nell'ambiente di produzione per informare l'utente che qualcosa è andato storto. In questo caso sarebbe opportuno comprendere perché Resource Manager ha dovuto comprimere le repliche nel dominio di aggiornamento. È possibile ad esempio che i nodi negli altri domini di aggiornamento fossero inattivi.

Supponiamo di voler creare un servizio o che Resource Manager stia tentando di trovare una posizione in cui inserire alcuni servizi ma che non ci siano soluzioni accettabili. Questa situazione potrebbe accadere per diversi motivi, ma in genere è dovuta a una delle due condizioni seguenti:

1. Una qualche condizione temporanea ha reso impossibile inserire correttamente questa istanza del servizio o replica
2. I requisiti del servizio non sono configurati correttamente e non possono essere soddisfatti.

In ognuna di queste condizioni si disporrà di un report di integrità di Cluster Resource Manager con informazioni utili per determinare che cosa sta succedendo e perché il servizio non può essere inserito. Questo processo viene definito "sequenza di eliminazione del vincolo". Durante la procedura il sistema esamina i singoli vincoli che agiscono sul servizio e registra ciò che eliminano. In questo modo, quando non si riesce a inserire i servizi, è possibile visualizzare i nodi eliminati e la relativa motivazione.

## <a name="constraint-types"></a>Tipi di vincolo
Esaminiamo ciascuno dei vincoli indicati nei report di integrità. Nella maggior parte dei casi questi vincoli non eliminano nodi poiché sono di livello "Soft" o "Optimization" per impostazione predefinita. È tuttavia possibile visualizzare messaggi di integrità correlati a questi vincoli se sono configurati come vincoli "Hard" o nei rari casi in cui determinano l'eliminazione di nodi.

* **ReplicaExclusionStatic** e **ReplicaExclusionDynamic**: questo vincolo indica che due repliche con stato o istanze senza stato della stessa partizione dovevano essere posizionate sullo stesso nodo, operazione non consentita. ReplicaExclusionStatic e ReplicaExclusionDynamic sono regole quasi identiche. Il vincolo ReplicaExclusionDynamic indica che è stato impossibile posizionare nel nodo questa replica perché l'unica soluzione proposta vi aveva già inserito una replica. È diverso dal vincolo ReplicaExclusionStatic il quale non indica un possibile conflitto ma un effettivo conflitto. In questo caso esiste già una replica nel nodo. È ambiguo? Sì. È molto importante? No. Se si nota una sequenza di eliminazione vincolo contenente ReplicaExclusionStatic o ReplicaExclusionDynamic significa che Cluster Resource Manager ritiene che non vi siano nodi sufficienti. Gli altri vincoli possono indicare in genere perché il numero di nodi è insufficiente.
* **PlacementConstraint**: se viene visualizzato questo messaggio significa che sono stati eliminati alcuni nodi perché non rispettavano i vincoli di posizionamento del servizio. I vincoli di posizionamento attualmente configurati vengono indicati come parte di questo messaggio. Questo è normale se sono stati definiti vincoli di posizionamento. Tuttavia, se è presente un bug nel vincolo di posizionamento che causa l'eliminazione di troppi nodi, lo si noterà qui.
* **NodeCapacity**: questo vincolo significa che Cluster Resource Manager non è riuscito a inserire le repliche nei nodi indicati perché l'operazione avrebbe fatto superare la capacità del nodo.
* **Affinity**: questo vincolo indica che non è stato possibile inserire la replica nei nodi interessati perché l'operazione avrebbe generato una violazione del vincolo Affinity. Ulteriori informazioni sull'affinità sono riportate in [questo articolo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: questo vincolo elimina i nodi se il posizionamento della replica nei nodi indicati comporterebbe la compressione in un particolare dominio di aggiornamento o di errore. Alcuni esempi che illustrano questo vincolo sono presentati nell'argomento sui [vincoli dei domini di aggiornamento e di errore e il relativo comportamento](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: in genere non si dovrebbe assistere alla rimozione di nodi dalla soluzione da parte di questo vincolo perché, per impostazione predefinita, è impostato sul livello "Optimization". Inoltre il vincolo di posizione preferita di solito è presente durante gli aggiornamenti. Durante l'aggiornamento viene usato per spostare le repliche dove si trovavano quando l'aggiornamento è stato avviato.

### <a name="constraint-priorities"></a>Priorità dei vincoli
Dato l'elevato numero di questi vincoli si potrebbe ritenere che i vincoli di posizionamento siano i più importanti del sistema. Si può pensare che sia preferibile violare altri vicoli, ad esempio affinità e capacità, pur di non violare mai i vincoli di posizionamento.

Questa operazione è possibile. I vincoli possono essere configurati con diversi livelli di imposizione, ma si riducono a "Hard" (0), "Soft" (1), "Optimization" (2) e "Off" (-1). La maggior parte dei vincoli è definita come "Hard" per impostazione predefinita. Ad esempio, la maggior parte degli utenti non ritiene normalmente che si possa scendere a compromessi sulla capacità. Quasi tutti i vincoli sono in ogni caso definiti come "Hard" o "Soft".

Le diverse priorità dei vincoli sono il motivo per il quale alcuni avvisi di violazione dei vincoli sono visualizzati più spesso di altri: esistono vincoli che l'utente è disposto a violare temporaneamente. Questi livelli non significano che uno specifico vincolo sarà o non sarà violato ma solo che c'è un ordine preferibile in cui vengono applicati. Questo consente a Cluster Resource Manager di scegliere il compromesso ideale se è impossibile soddisfare tutti i vincoli.

In situazioni avanzate è possibile modificare le priorità di vincolo. Si immagini ad esempio di voler garantire che venga violata l'affinità per risolvere problemi di capacità dei nodi. A tale scopo è possibile impostare la priorità del vincolo di affinità su "Soft" (1) e lasciare il vincolo di capacità impostato su "Hard" (0).

I valori di priorità predefiniti per i diversi vincoli sono specificati in config:

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

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Vincoli di dominio di errore e di dominio di aggiornamento
Cluster Resource Manager attua l'intenzione di mantenere i servizi distribuiti tra domini di errore e di aggiornamento come vincolo all'interno del motore di Resource Manager. Per altre informazioni sul loro utilizzo, vedere l'articolo sulla [configurazione del cluster](service-fabric-cluster-resource-manager-cluster-description.md).

Ci sono stati casi in cui era necessario essere restrittivi sui domini di errore e di aggiornamento per impedire che succedesse qualcosa di sbagliato. Ci sono stati anche casi in cui era necessario ignorarli completamente (anche se per poco tempo!). In genere la flessibilità dell'infrastruttura di priorità dei vincoli funziona molto bene, ma non si usa molto spesso. La maggior parte delle volte ogni cosa è impostata sulla sua priorità predefinita. I domini di aggiornamento rimangono un vincolo "Soft". Cluster Resource Manager potrebbe dover comprimere un paio di repliche in un dominio di aggiornamento per far fronte ad aggiornamenti, errori o violazioni di vincoli. Ciò solitamente si verifica solo quando sono presenti molti errori o altra varianza nel sistema che impedisce il corretto posizionamento. Se l'ambiente è configurato correttamente, tutti i vincoli, compresi i vincoli di errore e di aggiornamento, vengono completamente rispettati, anche durante gli aggiornamenti. L'elemento fondamentale è che Cluster Resource Manager controlla i vincoli e segnala immediatamente le violazioni che rileva.

## <a name="the-preferred-location-constraint"></a>Il vincolo di posizione preferita
Il vincolo PreferredLocation è leggermente diverso ed è quindi l'unico vincolo impostato su "Optimization". Usiamo questo vincolo durante gli aggiornamenti in corso per provare a posizionare nuovamente i servizi dove si trovavano in origine prima dell'aggiornamento. Esistono molti motivi per i quali questo approccio potrebbe non funzionare nella pratica, ma si tratta comunque di un'utile opzione di ottimizzazione.

## <a name="upgrades"></a>Aggiornamenti
Cluster Resource Manager facilita le cose anche durante gli aggiornamenti di applicazioni e cluster, quando ha due compiti:

* assicurarsi che non vengano compromesse le regole e le prestazioni del cluster
* agevolare il completamento dell'aggiornamento senza intoppi

### <a name="keep-enforcing-the-rules"></a>Applicazione continua delle regole
La cosa principale da tenere presente è che le regole, vale a dire i rigidi vincoli su aspetti come il posizionamento, vengono applicate anche durante gli aggiornamenti. I vincoli di posizionamento assicurano che i carichi di lavoro siano eseguiti solo dove consentito, anche durante gli aggiornamenti. Se l'ambiente presenta molti vincoli, gli aggiornamenti potrebbero richiedere molto tempo. Ciò è dovuto al fatto che potrebbero esistere poche opzioni per spostare un servizio se esso (o il nodo in cui si trova) deve essere disattivato per un aggiornamento.

### <a name="smart-replacements"></a>Sostituzioni intelligenti
Quando viene avviato un aggiornamento, Resource Manager crea un'istantanea del layout corrente del cluster. Al completamento di ogni dominio di aggiornamento tenta di riportare le cose nella disposizione originale. In questo modo avvengono al massimo due transizioni durante l'aggiornamento (lo spostamento fuori del nodo e il ritorno nel nodo). Riportare il cluster come si trovava prima dell'aggiornamento assicura anche che l'aggiornamento non influenzi il layout del cluster. Se il cluster era organizzato bene prima dell'aggiornamento, sarà organizzato bene anche dopo, o almeno non peggio.

### <a name="reduced-churn"></a>Varianza ridotta
Un altro evento che si verifica durante gli aggiornamenti è che Cluster Resource Manager disattiva il bilanciamento del carico per l'entità da aggiornare. Pertanto, se si dispone di due istanze diverse dell'applicazione e si esegue l'aggiornamento su una di esse, il bilanciamento del carico verrà sospeso per tale istanza dell'applicazione, ma non per l'altra. Impedendo il bilanciamento si evitano inutili effetti collaterali dell'aggiornamento, come lo spostamento di servizi in nodi che sono stati svuotati per l'aggiornamento. Se l'aggiornamento in questione è un aggiornamento del cluster, viene disattivato il bilanciamento dell'intero cluster durante l'aggiornamento. I controlli dei vincoli, che garantiscono l'applicazione delle regole, rimangono attivi e viene disattivato solo il ribilanciamento.

### <a name="relaxed-rules"></a>Regole meno severe
In genere si vuole che l'aggiornamento sia completato anche se il cluster presenta vincoli o è completo. Poter gestire la capacità del cluster durante gli aggiornamenti è ancora più importante che durante il normale funzionamento. Questo perché in genere, mentre viene eseguito l'aggiornamento del cluster, una porzione fra il 5 e il 20% della capacità rimane inutilizzabile. Questa percentuale di lavoro di solito deve essere trasferita da qualche parte. E qui entra in gioco la nozione di [buffer di capacità](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity). Durante il normale funzionamento il buffer di capacità viene rispettato, ma durante gli aggiornamenti Cluster Resource Manager sfrutta la capacità totale, incluso il buffer.

## <a name="next-steps"></a>Passaggi successivi
* Partire dall'inizio e vedere l' [introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)



<!--HONumber=Feb17_HO2-->


