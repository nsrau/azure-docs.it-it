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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 70c0cc37a1d362c937ab86bd630c5ab051e63870
ms.contentlocale: it-it
ms.lasthandoff: 09/15/2017

---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integrazione di Cluster Resource Manager con la gestione dei cluster di Service Fabric
Cluster Resource Manager di Service Fabric non gestisce gli aggiornamenti in Service Fabric ma partecipa al processo. Il primo modo in cui Cluster Resource Manager facilita la gestione è monitorando lo stato desiderato del cluster e i servizi al suo interno. Cluster Resource Manager invia report di integrità quando non riesce ad attivare la configurazione del cluster desiderata. Se ad esempio la capacità è insufficiente, Cluster Resource Manager invia avvisi ed errori relativi all'integrità che indicano il problema. Un altro aspetto dell'integrazione ha a che fare con il funzionamento degli aggiornamenti. Durante gli aggiornamenti Cluster Resource Manager modifica leggermente il suo comportamento.  

## <a name="health-integration"></a>Integrazione di integrità
Cluster Resource Manager monitora costantemente le regole definite per il posizionamento dei servizi. Monitora inoltre la capacità residua per ogni metrica nei nodi del cluster e nel cluster nel suo complesso. Se non riesce a soddisfare queste regole o se la capacità è insufficiente vengono generati errori e avvisi di integrità. Ad esempio, se un nodo ha capacità in eccesso, Cluster Resource Manager tenterà di risolvere la situazione spostando i servizi. Se non riesce a risolvere la situazione, genera un avviso di integrità che indica quale nodo ha capacità in eccesso e per quali metriche.

Un altro esempio di avvisi di integrità di Resource Manager riguarda le violazioni dei vincoli di posizionamento. Se ad esempio è stato definito un vincolo di posizionamento (come `“NodeColor == Blue”`) e Resource Manager rileva una violazione del vincolo, genera un avviso di integrità. Questo vale per i vincoli personalizzati e i vincoli predefiniti (ad esempio i vincoli di dominio di errore e di aggiornamento).

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
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Ecco che cosa indica questo messaggio di stato:

1. Le repliche stesse sono integre: ognuna presenta AggregatedHealthState: Ok
2. Il vincolo di distribuzione del dominio di aggiornamento è attualmente violato. Ciò significa che un particolare dominio di aggiornamento ha più repliche di questa partizione rispetto a quanto previsto.
3. Qual è il nodo che contiene la replica che causa la violazione. In questo caso è il nodo denominato "Node.8"
4. Se è in corso un aggiornamento per questa partizione ("Currently Upgrading -- false")
5. I criteri di distribuzione per questo servizio: "Distribution Policy -- Packing". Regolato dal [criterio di selezione](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing) `RequireDomainDistribution`. "Packing" indica che in questo caso DomainDistribution _non_ era necessario e quindi che per questo servizio il criterio di selezione non è stato specificato. 
6. Quando è stato inviato il report 10/8/2015 19:13:02

Informazioni come queste producono avvisi che vengono generati nell'ambiente di produzione per informare l'utente che qualcosa non ha funzionato e per rilevare e arrestare aggiornamenti non validi. In questo caso sarebbe opportuno comprendere perché Resource Manager ha dovuto comprimere le repliche nel dominio di aggiornamento. Di solito la compressione è temporanea ad esempio perché i nodi negli altri domini di aggiornamento erano inattivi.

Supponiamo che Cluster Resource Manager stia tentando di posizionare alcuni servizi ma che non ci siano soluzioni accettabili. Quando non è possibile posizionare i servizi, in genere il motivo è uno dei seguenti:

1. Una qualche condizione temporanea ha reso impossibile inserire correttamente questa istanza del servizio o replica
2. I requisiti di posizionamento del servizio non sono soddisfacenti.

In questi casi i report sull'integrità di Cluster Resource Manager consentono di determinare perché non è possibile posizionare il servizio. Questo processo viene definito sequenza di eliminazione del vincolo. Durante la procedura il sistema esamina i singoli vincoli che agiscono sul servizio e registra ciò che eliminano. In questo modo, quando non si riesce a inserire i servizi, è possibile visualizzare i nodi eliminati e la relativa motivazione.

## <a name="constraint-types"></a>Tipi di vincolo
Esaminiamo ciascuno dei vincoli presenti nei report di integrità. Verranno visualizzati messaggi di integrità relativi a questi vincoli quando non è possibile inserire le repliche.

* **ReplicaExclusionStatic** e **ReplicaExclusionDynamic**: questi vincoli indicano che è stata rifiutata una soluzione perché due oggetti di servizio della stessa partizione dovevano essere posizionati sullo stesso nodo. Ciò non è consentito perché l'errore su quel nodo avrebbe un impatto eccessivo sulla partizione. ReplicaExclusionStatic e ReplicaExclusionDynamic sono regole quasi identiche e le differenze sono irrilevanti. Se si nota una sequenza di eliminazione vincolo contenente ReplicaExclusionStatic o ReplicaExclusionDynamic significa che Cluster Resource Manager ritiene che non vi siano nodi sufficienti. Ciò richiede l'uso di posizionamenti non validi che sono disabilitati. Gli altri vincoli nella sequenza indicheranno perché i nodi vengono eliminati nella prima posizione.
* **PlacementConstraint**: se viene visualizzato questo messaggio significa che sono stati eliminati alcuni nodi perché non rispettavano i vincoli di posizionamento del servizio. I vincoli di posizionamento attualmente configurati vengono indicati come parte di questo messaggio. Questo è normale se è stato definito un vincolo di posizionamento. Tuttavia se un vincolo di posizionamento causa l'eliminazione non corretta di troppi nodi, lo si noterà qui.
* **NodeCapacity**: questo vincolo indica che Cluster Resource Manager non è riuscito a inserire le repliche nei nodi indicati perché l'operazione avrebbe fatto superare la capacità del nodo.
* **Affinity**: questo vincolo indica che non è stato possibile inserire la replica nei nodi interessati perché l'operazione avrebbe generato una violazione del vincolo Affinity. Ulteriori informazioni sull'affinità sono riportate in [questo articolo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: questo vincolo elimina i nodi se il posizionamento della replica nei nodi indicati comporterebbe la compressione in un particolare dominio di aggiornamento o di errore. Alcuni esempi che illustrano questo vincolo sono presentati nell'argomento sui [vincoli dei domini di aggiornamento e di errore e il relativo comportamento](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: in genere non si dovrebbe assistere alla rimozione di nodi dalla soluzione da parte di questo vincolo perché per impostazione predefinita funziona come ottimizzazione. Il vincolo di posizione preferita è presente anche durante gli aggiornamenti. Durante l'aggiornamento viene usato per spostare i servizi dove si trovavano quando l'aggiornamento è stato avviato.

## <a name="blocklisting-nodes"></a>Nodi sottoposti a blocklisting
Un altro messaggio di integrità notificato da Cluster Resource Manager si verifica quando i nodi sono sottoposti a blocklisting. È possibile considerare il blocklisting come un vincolo temporaneo che viene applicato automaticamente per l'utente. I nodi vengono sottoposti a blocklisting quando si verificano errori ripetuti durante l'avvio di istanze di quel tipo di servizio. I nodi vengono sottoposti a blocklisting in base al tipo di servizio. Un nodo può essere sottoposto a blocklisting per un tipo di servizio ma non per un altro. 

Il blocklisting avviene spesso in fase di sviluppo: alcuni bug causano l'arresto anomalo dell'host servizio all'avvio. Service Fabric tenta di creare l'host servizio più volte e l'errore continua a verificarsi. Dopo alcuni tentativi, il nodo viene sottoposto a blocklisting e Cluster Resource Manager tenterà di creare il servizio in un'altra posizione. Se l'errore continua a verificarsi in più nodi, è possibile che tutti i nodi validi del cluster vengano bloccati. Il blocklisting può anche rimuovere una tale quantità di nodi che non ne resta una quantità sufficiente per avviare correttamente il servizio per soddisfare la scalabilità desiderata. Verranno visualizzati altri errori o avvisi da Cluster Resource Manager che indicano che il servizio è al di sotto del numero di repliche o di istanze desiderato, nonché messaggi di integrità che indicano qual è l'errore che causa il blocklisting nella prima posizione.

Il blocklisting non è una condizione permanente. Dopo alcuni minuti, il nodo viene rimosso dal blocco e Service Fabric potrebbe attivare nuovamente i servizi su tale nodo. Se i servizi continuano a dare errore, il nodo è nuovamente sottoposto a blocklisting per quel tipo di servizio. 

### <a name="constraint-priorities"></a>Priorità dei vincoli

> [!WARNING]
> Modificare le priorità dei vincoli non è consigliato e può avere effetti negativi significativi nel cluster. Le seguenti informazioni vengono fornite come riferimento delle priorità predefinite dei vincoli e il relativo comportamento. 
>

Dato l'elevato numero di questi vincoli si potrebbe ritenere che i vincoli di dominio di errore siano i più importanti del sistema. Per garantire che non venga violato il vincolo di dominio di errore, si possono violare altri vincoli.

I vincoli possono essere configurati con diversi livelli di priorità. Si tratta di:

   - “hard” (0)
   - “soft” (1)
   - “optimization” (2)
   - “off” (-1). 
   
Per impostazione predefinita, la maggior parte dei vincoli vengono configurati come vincoli "hard".

La modifica della priorità dei vincoli è insolita. Ci sono situazioni in cui è necessario modificare le priorità dei vincoli, in genere per risolvere altri bug o comportamenti che incidono sull'ambiente. In genere la flessibilità dell'infrastruttura di priorità dei vincoli funziona molto bene, ma non si usa molto spesso. La maggior parte delle volte ogni cosa è impostata sulla sua priorità predefinita. 

I livelli di priorità non indicano che uno specifico vincolo _verrà_ violato, né che sarà sempre soddisfatto. Le priorità dei vincoli definiscono un ordine in cui i vincoli vengono applicati. Le priorità di definiscono i compromessi quando non è possibile soddisfare tutti i vincoli. In genere tutti i vincoli possono essere soddisfatti a meno che non si verifichi qualche altra cosa nell'ambiente. Alcuni esempi di scenari in cui è necessario violare i vincoli sono i vincoli in conflitto o un numero elevato di errori simultanei.

In situazioni avanzate è possibile modificare le priorità dei vincoli. Si immagini ad esempio di voler garantire che venga sempre violata l'affinità quando è necessario risolvere problemi di capacità dei nodi. A tale scopo è possibile impostare la priorità del vincolo di affinità su "Soft" (1) e lasciare il vincolo di capacità impostato su "Hard" (0).

I valori di priorità predefiniti per i diversi vincoli sono specificati nella seguente configurazione:

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
Cluster Resource Manager distribuisce i servizi tra i domini di errore e di aggiornamento. Viene modellato come un vincolo all'interno del motore di Cluster Resource Manager. Per altre informazioni sull'uso e sul comportamento specifico, vedere l'articolo sulla [configurazione del cluster](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Cluster Resource Manager potrebbe dover comprimere un paio di repliche in un dominio di aggiornamento per far fronte ad aggiornamenti, errori o altre violazioni di vincoli. La compressione in domini di errore o di aggiornamento si verifica in genere quando sono presenti molti errori o altra varianza nel sistema che impedisce il corretto posizionamento. Se si desidera impedire la compressione anche durante queste situazioni, è possibile usare il [criterio di selezione](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing) `RequireDomainDistribution`. Si noti che questo può influire sulla disponibilità e l'affidabilità del servizio come effetto collaterale e pertanto valutarlo attentamente.

Se l'ambiente è configurato correttamente tutti i vincoli vengono completamente rispettati anche durante gli aggiornamenti. L'elemento fondamentale è che Cluster Resource Manager controlla i vincoli. Quando rileva una violazione la segnala immediatamente e tenta di risolvere il problema.

## <a name="the-preferred-location-constraint"></a>Il vincolo di posizione preferita
Il vincolo PreferredLocation è leggermente diverso, perché ha due diversi usi. Un uso di questo vincolo avviene durante gli aggiornamenti dell'applicazione. Cluster Resource Manager gestisce automaticamente questo vincolo durante gli aggiornamenti. Viene usato per assicurare che quando gli aggiornamenti vengono completati le repliche tornino alle posizioni iniziali. L'altro uso del vincolo PreferredLocation è per il [`PreferredPrimaryDomain` criterio di selezione](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Entrambi sono ottimizzazioni e il vincolo PreferredLocation è l'unico vincolo impostato su "Optimization" per impostazione predefinita.

## <a name="upgrades"></a>Aggiornamenti
Cluster Resource Manager facilita le cose anche durante gli aggiornamenti di applicazioni e cluster, quando ha due compiti:

* assicurarsi che non vengano compromesse le regole del cluster
* agevolare il completamento dell'aggiornamento senza intoppi

### <a name="keep-enforcing-the-rules"></a>Applicazione continua delle regole
La cosa principale da tenere presente è che le regole, vale a dire i rigidi vincoli su aspetti come il posizionamento e le capacità, vengono applicate anche durante gli aggiornamenti. I vincoli di posizionamento assicurano che i carichi di lavoro siano eseguiti solo dove consentito, anche durante gli aggiornamenti. Quando i servizi sono altamente vincolati, gli aggiornamenti possono richiedere più tempo. Quando il servizio o il nodo su che è in esecuzione diventa inattivo per un aggiornamento possono esserci poche opzioni disponibili.

### <a name="smart-replacements"></a>Sostituzioni intelligenti
Quando viene avviato un aggiornamento, Resource Manager crea un'istantanea del layout corrente del cluster. Quando un dominio di aggiornamento viene completato, tenta di riportare i servizi presenti nel dominio di aggiornamento alla disposizione originale. In questo modo durante l'aggiornamento vi sono al massimo due transizioni per un servizio. È presente uno spostamento all'esterno dal nodo interessato e quindi uno all'interno. Riportare il cluster o il servizio come si trovava prima dell'aggiornamento assicura anche che l'aggiornamento non influenzi il layout del cluster. 

### <a name="reduced-churn"></a>Varianza ridotta
Un altro evento che si verifica durante gli aggiornamenti è che Cluster Resource Manager disattiva il bilanciamento. Impedendo il bilanciamento si evitano inutili effetti collaterali dell'aggiornamento, come lo spostamento di servizi in nodi che sono stati svuotati per l'aggiornamento. Se l'aggiornamento in questione è un aggiornamento del cluster, viene disattivato il bilanciamento dell'intero cluster durante l'aggiornamento. Controlli dei vincoli rimangono attivi, viene disattivato solo lo spostamento in base al bilanciamento proattivo delle metriche.

### <a name="buffered-capacity--upgrade"></a>Capacità in buffering e aggiornamento
In genere si vuole che l'aggiornamento sia completato anche se il cluster presenta vincoli o ha quasi raggiunto il massimo livello di utilizzo. Poter gestire la capacità del cluster durante gli aggiornamenti è ancora più importante che durante il normale funzionamento. A seconda del numero di domini di aggiornamento, tra il 5 e il 20 per cento della capacità deve essere migrata mentre l'aggiornamento viene distribuito nel cluster. Questa percentuale di lavoro deve essere trasferita da qualche parte. E qui diventa utile la nozione di [buffer di capacità](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity). Durante il normale funzionamento il buffer di capacità viene rispettato. Cluster Resource Manager può riempire i nodi fino alla capacità totale (consumando il buffer) durante gli aggiornamenti, se necessario.

## <a name="next-steps"></a>Passaggi successivi
* Partire dall'inizio e vedere l' [introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

