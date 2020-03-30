---
title: Ripristino di emergenza di Azure Service FabricAzure Service Fabric disaster recovery
description: Azure Service Fabric offre funzionalità per gestire le emergenze. L'articolo descrive i tipi di emergenze che si possono verificare e come affrontarli.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b29985d40ae3a1bf582099e998e000fed83460f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371648"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Ripristino di emergenza in Azure Service Fabric
Una parte fondamentale della fornitura di disponibilità elevata consiste nel garantire che i servizi possano sopravvivere a tutti i diversi tipi di errori. Ciò è particolarmente importante per gli errori non pianificati e esterni al controllo dell'utente. 

In questo articolo vengono descritte alcune modalità di errore comuni che potrebbero essere disastri se non modellati e gestiti correttamente. Vengono inoltre illustrate le attenuazioni e le azioni da intraprendere in caso di emergenza. L'obiettivo è limitare o eliminare il rischio di tempi di inattività o perdita di dati quando si verificano errori, pianificati o meno.

## <a name="avoiding-disaster"></a>Evitare situazioni di emergenza
L'obiettivo principale di Azure Service Fabric consiste nell'aiutarti a modellare sia l'ambiente che i servizi in modo che i tipi di errore comuni non siano disastri. 

In generale, esistono due tipi di scenari di emergenza/errore:
- Errori hardware e software
- Errori operativi

### <a name="hardware-and-software-faults"></a>Errori hardware e software
Gli errori hardware e software sono imprevedibili. Il modo più semplice per sopravvivere agli errori consiste nell'eseguire più copie del servizio oltre i limiti di errore hardware o software. 

Ad esempio, se il servizio è in esecuzione su un solo computer, l'errore di tale computer è un'emergenza per tale servizio. Il modo semplice per evitare questa emergenza consiste nel garantire che il servizio sia in esecuzione su più computer. I test sono inoltre necessari per garantire che l'errore di un computer non interrompa il servizio in esecuzione. La pianificazione della capacità garantisce che un'istanza sostitutiva possa essere creata altrove e che la riduzione della capacità non sovraccarichi i servizi rimanenti. 

Lo stesso criterio funziona indipendentemente dall'errore che si sta tentando di evitare. Ad esempio, se si è preoccupati per l'errore di una rete SAN, si esegue attraverso più RETI SAN. Se si teme la perdita di un rack di server, si può eseguire il servizio su più rack. Se si è preoccupati per la perdita dei data center, il servizio deve essere eseguito in più aree di Azure, in più zone di disponibilità di Azure o nei propri data center. 

Quando un servizio è espandito su più istanze fisiche (computer, rack, data center, aree), si è ancora soggetti ad alcuni tipi di errori simultanei. Tuttavia, gli errori singoli e anche multipli di un determinato tipo (ad esempio, una singola macchina virtuale o un collegamento di rete non riesce) vengono gestiti automaticamente e quindi non sono più un "disastro". 

Service Fabric fornisce meccanismi per espandere il cluster e gli handle per riportare i nodi e i servizi con errori. Service Fabric consente inoltre l'esecuzione di molte istanze dei servizi per evitare che errori non pianificati si trasformino in errori reali.

Ci potrebbero essere motivi per cui l'esecuzione di una distribuzione sufficientemente grande da estendersi su errori non è fattibile. Ad esempio, potrebbe richiedere più risorse hardware di quelle che si è disposti a pagare in relazione alla possibilità di errore. Quando si ha a che fare con applicazioni distribuite, ulteriori hop di comunicazione o costi di replica dello stato su distanze geografiche potrebbero causare latenza inaccettabile. La posizione di questa linea di confine varia per ogni applicazione. 

Per gli errori software in particolare, l'errore potrebbe essere nel servizio che si sta tentando di scalare. In questo caso, più copie non impediscono l'emergenza, perché la condizione di errore è correlata tra tutte le istanze.

### <a name="operational-faults"></a>Errori operativi
Anche se il servizio è esteso in tutto il mondo con molte ridondanze, può comunque essere soggetto a eventi di emergenza, Ad esempio, qualcuno potrebbe accidentalmente riconfigurare il nome DNS per il servizio o eliminarlo definitivamente. 

Si supponga, ad esempio, di disporre di un servizio di Service Fabric con stato e che un utente lo abbia eliminato inavvertitamente. A meno che non ci sia qualche altra mitigazione, quel servizio e tutto lo stato che aveva ora sono andati. Questi tipi di emergenze operative ("errori") richiedono mitigazioni e procedure per il ripristino diverse rispetto ai normali errori imprevisti. 

I modi migliori per evitare questi tipi di errori operativi sono:
- Limitare l'accesso operativo all'ambiente.
- Controllare rigorosamente le operazioni pericolose.
- Imporre l'automazione, impedire modifiche manuali o fuori banda e convalidare modifiche specifiche rispetto all'ambiente prima di metterle in atto.
- Assicurarsi che le operazioni distruttive siano "morbide". Le operazioni soft non hanno effetto immediato o possono essere annullate entro un intervallo di tempo.

Service Fabric fornisce meccanismi per prevenire errori operativi, ad esempio fornire il controllo degli accessi [in base](service-fabric-cluster-security-roles.md) al ruolo per le operazioni del cluster. La maggior parte di questi errori operativi richiede tuttavia attività organizzative e altri sistemi. Service Fabric fornisce meccanismi per superare gli errori operativi, in particolare il backup e il ripristino per i [servizi con stato.](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

## <a name="managing-failures"></a>Gestione degli errori
L'obiettivo di Service Fabric è la gestione automatica degli errori. Ma per gestire alcuni tipi di errori, i servizi devono avere codice aggiuntivo. Altri tipi di errori _non_ devono essere risolti automaticamente per motivi di sicurezza e continuità aziendale. 

### <a name="handling-single-failures"></a>Gestione di errori singoli
I computer singoli possono essere soggetti a errori per ogni genere di motivo. A volte si tratta di cause hardware, come alimentatori e guasti hardware di rete. Altri errori sono nel software. Questi includono gli errori del sistema operativo e del servizio stesso. Service Fabric rileva automaticamente questi tipi di errori, inclusi i casi in cui la macchina viene isolata da altri computer a causa di problemi di rete.

Indipendentemente dal tipo di servizio, l'esecuzione di una sola istanza provoca tempi di inattività del servizio se per qualche motivo l'unica copia del codice genera un errore. 

Per gestire qualsiasi singolo errore, la cosa più semplice da fare è assicurarsi che i servizi vengano eseguiti su più di un nodo per impostazione predefinita. Per i servizi senza `InstanceCount` stato, assicurarsi che sia maggiore di 1.For stateless services, make sure that is greater than 1. Per i servizi con stato, `TargetReplicaSetSize` `MinReplicaSetSize` la raccomandazione minima è che e sono entrambi impostati su 3.For stateful services, the minimum recommendation is that and are both set to 3. L'esecuzione di più copie del codice del servizio assicura al servizio la capacità di gestire qualsiasi errore automaticamente. 

### <a name="handling-coordinated-failures"></a>Gestione di errori coordinati
Gli errori coordinati in un cluster possono essere dovuti a errori e modifiche dell'infrastruttura pianificati o non pianificati o a modifiche software pianificate. Service Fabric modella le zone dell'infrastruttura in cui si verificano errori coordinati come *domini di errore.* Le aree in cui si verificheranno modifiche software coordinate vengono modellate come domini di *aggiornamento.* Per ulteriori informazioni sui domini di errore, sui domini di aggiornamento e sulla topologia del cluster, vedere [Descrivere un cluster di Service Fabric tramite Gestione risorse cluster.](service-fabric-cluster-resource-manager-cluster-description.md)

Per impostazione predefinita, Service Fabric considera i domini di errore e di aggiornamento durante la pianificazione in cui devono essere eseguiti i servizi. Per impostazione predefinita, Service Fabric tenta di garantire che i servizi vengano eseguiti in diversi domini di errore e di aggiornamento in modo che, in caso di modifiche pianificate o non pianificate, i servizi rimangano disponibili. 

Si supponga, ad esempio, che il guasto di una fonte di alimentazione provochi un guasto simultaneo di tutte le macchine su un rack. Con più copie del servizio in esecuzione, la perdita di molti computer in errore del dominio di errore si trasforma in un unico esempio di un singolo errore per un servizio. Questo è il motivo per cui la gestione dei domini di errore e aggiornamento è fondamentale per garantire un'elevata disponibilità dei servizi. 

Quando si esegue Service Fabric in Azure, i domini di errore e i domini di aggiornamento vengono gestiti automaticamente. In altri ambienti, potrebbero non esserlo. Se si creano i propri cluster in locale, assicurarsi di mappare e pianificare correttamente il layout del dominio di errore.

I domini di aggiornamento sono utili per la modellazione delle aree in cui il software verrà aggiornato contemporaneamente. Per questo motivo, i domini di aggiornamento spesso definiscono i limiti in cui il software viene eseguito durante gli aggiornamenti pianificati. Gli aggiornamenti sia di Service Fabric che dei servizi seguono lo stesso modello. Per altre informazioni sugli aggiornamenti in sequenza, sui domini di aggiornamento e sul modello di integrità di Service Fabric che consente di impedire che le modifiche indesiderate influiscano sul cluster e sul servizio, vedere:For more information on rolling upgrades, upgrade domains, and the Service Fabric health model that helps prevent unintended changes from affecting the cluster and your service, see:

 - [Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)
 - [Esercitazione sull'aggiornamento delle applicazioni](service-fabric-application-upgrade-tutorial.md)
 - [Modello di integrità di Service FabricService Fabric health model](service-fabric-health-introduction.md)

È possibile visualizzare il layout del cluster usando la mappa del cluster fornita in [Service Fabric Explorer:](service-fabric-visualizing-your-cluster.md)

<center>

![Nodi distribuiti nei domini di errore in Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Le aree di modellazione degli errori, gli aggiornamenti in sequenza, l'esecuzione di molte istanze del codice e dello stato del servizio, le regole di posizionamento per garantire che i servizi vengano eseguiti nei domini di errore e di aggiornamento e il monitoraggio dell'integrità incorporato sono solo *alcune* delle funzionalità fornite da Service Fabric per impedire che i normali problemi operativi e gli errori si trasformino in situazioni di errore. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Gestione di errori hardware o software simultanei
Abbiamo parlato di singoli fallimenti. Come si può vedere, sono facili da gestire sia per i servizi senza stato che per i servizi con stato semplicemente mantenendo più copie del codice (e dello stato) in esecuzione nei domini di errore e di aggiornamento. 

I servizi possono essere interessati anche da più errori casuali simultanei. Questi sono più propensi a portare a tempi di inattività o un disastro reale.


#### <a name="stateless-services"></a>Servizi apolidi
Il numero di istanze per un servizio senza stato indica il numero desiderato di istanze che devono essere in esecuzione. Quando una o tutte le istanze hanno esito negativo, Service Fabric risponde creando automaticamente istanze di sostituzione in altri nodi. Service Fabric continua a creare sostituzioni fino a quando il servizio non torna al numero di istanze desiderato.

Si supponga, ad esempio, che `InstanceCount` il servizio senza stato abbia un valore pari a -1. Questo valore indica che un'istanza deve essere in esecuzione su ogni nodo del cluster. Se alcune di queste istanze hanno esito negativo, Service Fabric rileverà che il servizio non è nello stato desiderato e tenterà di creare le istanze nei nodi in cui mancano.

#### <a name="stateful-services"></a>Servizi statali
Esistono due tipi di servizi con stato:
- Stato con stato persistente.
- Stato con stato non persistente. (Lo stato è memorizzato in memoria.)

Il ripristino da un errore di un servizio con stato dipende dal tipo di servizio con stato, dal numero di repliche di cui disponeva e dal numero di repliche non riuscite.

In un servizio con stato, i dati in ingresso vengono replicati tra le repliche (il database primario e gli eventuali server secondari attivi). Se la maggior parte delle repliche riceve i dati, i dati vengono considerati *quorum* di commit. (Per cinque repliche, tre saranno un quorum.) Ciò significa che in qualsiasi momento, ci sarà almeno un quorum di repliche con i dati più recenti. Se le repliche hanno esito negativo (ad esempio due su cinque), è possibile utilizzare il valore del quorum per calcolare se è possibile eseguire il ripristino. Poiché le altre tre repliche su cinque sono ancora in attività, è garantito che almeno una replica dirà dati completi.

Quando un quorum di repliche ha esito negativo, la partizione viene dichiarata in uno stato di perdita del *quorum.* Si supponga che una partizione disponga di cinque repliche, il che significa che è garantito che almeno tre dispongano di dati completi. Se un quorum (tre su cinque) di repliche ha esito negativo, Service Fabric non è in grado di determinare se le repliche rimanenti (due su cinque) dispongono di dati sufficienti per ripristinare la partizione. Nei casi in cui Service Fabric rileva la perdita del quorum, il comportamento predefinito consiste nell'impedire scritture aggiuntive nella partizione, dichiarare la perdita del quorum e attendere il ripristino di un quorum delle repliche.

Per determinare se si è verificata un'emergenza per un servizio con stato e quindi la sua gestione segue tre fasi:Determining whether a disaster occurred for a stateful service and then managing it follows three stages:

1. Determinare se si è verificata o meno la perdita del quorum.
   
   La perdita del quorum viene dichiarata quando la maggior parte delle repliche di un servizio con stato è inattiva contemporaneamente.
2. Determinare se la perdita del quorum è permanente o meno.
   
   Nella maggior parte dei casi gli errori sono temporanei. I processi vengono riavviati, i nodi riavviati, le macchine virtuali riavviate e le partizioni di rete vengono richiamate. A volte, però, i fallimenti sono permanenti. Il fatto che gli errori siano permanenti o meno dipende dal fatto che il servizio con stato mantenga lo stato o che lo mantenga solo in memoria: 
   
   - Per i servizi senza stato persistente, un errore di uno o più quorum di repliche determina _immediatamente_ una perdita del quorum permanente. Quando Service Fabric rileva la perdita del quorum in un servizio non persistente con stato, passa immediatamente alla fase 3 dichiarando una (potenziale) perdita di dati. Procedere alla perdita di dati ha senso perché Service Fabric sa che non ha senso aspettare che le repliche tornino. Anche se vengono ripristinati, i dati andranno persi a causa della natura non persistente del servizio.
   - Per i servizi persistenti con stato, un errore di un quorum o più repliche fa sì che Service Fabric attenda il ritorno delle repliche e il ripristino del quorum. Si verifica di conseguenza un'interruzione del servizio per tutte le _operazioni di scrittura_ alla partizione interessata (o "set di repliche") del servizio. Tuttavia, le letture potrebbero essere ancora possibili con garanzie di coerenza ridotte. Il periodo di tempo predefinito che Service Fabric attende il ripristino del quorum è *infinito,* poiché il procedimento è un evento di perdita di dati (potenziale) e comporta altri rischi. Ciò significa che Service Fabric non procedere al passaggio successivo a meno che un amministratore non eseri per dichiarare la perdita di dati.
3. Determinare se i dati vengono persi e il ripristino dai backup.

   Se la perdita del quorum è stata dichiarata (automaticamente o tramite un'azione amministrativa), Service Fabric e i servizi passano a determinare se i dati sono stati effettivamente persi. A questo punto, Service Fabric sa anche che le altre repliche non stanno tornando. Si arriva così alla decisione di smettere di attendere che la perdita del quorum si risolva da sola. La migliore linea di azione per il servizio è in genere bloccare la situazione e attendere un intervento specifico da parte dell'amministratore.
   
   Quando Service Fabric `OnDataLossAsync` chiama il metodo, è sempre a causa di _una sospetta_ perdita di dati. Service Fabric assicura che la chiamata venga inviata alla _migliore_ replica rimanente, vale a dire quella che ha fatto i maggiori progressi. 
   
   Il motivo per cui diciamo sempre _una perdita_ di dati sospetta è che è possibile che la replica rimanente abbia lo stesso stato del database primario quando il quorum è stato perso. Senza quello stato con cui fare il confronto, tuttavia, non esiste un modo utile che dica a Service Fabric o agli operatori l'effettivo stato.     
   
   Cosa fa quindi una tipica implementazione del metodo `OnDataLossAsync`?
   1. L'implementazione `OnDataLossAsync` registra che è stata attivata e genera tutti gli avvisi amministrativi necessari.
   1. In genere, l'implementazione viene sospesa e attende ulteriori decisioni e azioni manuali da intraprendere. Questo perché anche se i backup sono disponibili, potrebbe essere necessario prepararli. 
   
      Ad esempio, se due servizi diversi coordinano le informazioni, potrebbe essere necessario modificare tali backup per garantire che, dopo il ripristino, le informazioni importanti per i due servizi siano coerenti. 
   1. Spesso c'è qualche altro telemetria o scarico dal servizio. Questi metadati potrebbero essere contenuti in altri servizi o nei log. Queste informazioni possono essere utilizzate in base alle esigenze per determinare se sono state ricevute ed elaborate chiamate nel database primario che non erano presenti nel backup o replicate in questa particolare replica. È possibile che sia necessario riprodurre o aggiungere queste chiamate al backup prima che sia possibile eseguire il ripristino.  
   1. L'implementazione confronta lo stato della replica rimanente con quello contenuto in tutti i backup disponibili. Se si usano raccolte affidabili di Service Fabric, sono disponibili [strumenti e processi](service-fabric-reliable-services-backup-restore.md) per eseguire questa operazione. L'obiettivo è verificare se lo stato all'interno della replica è sufficiente e per vedere cosa potrebbe mancare il backup.
   1. Al termine del confronto e dopo il completamento del ripristino (se necessario), il codice del servizio deve restituire **true** se sono state apportate modifiche allo stato. Se la replica ha determinato che si tratta della migliore copia disponibile dello stato e non ha apportato modifiche, il codice restituisce **false**. 
   
      Il valore **true** indica che tutte le _altre_ repliche rimanenti potrebbero ora essere incoerenti con questa. Le repliche rimanenti verranno eliminate e ricreate da questa replica. Il valore **false** indica che non sono state apportate modifiche allo stato, pertanto le altre repliche possono mantenere ciò che hanno. 

È di fondamentale importanza che gli autori di servizi pratichino potenziali scenari di perdita di dati e di errore prima che i servizi vengano distribuiti nell'ambiente di produzione. Per proteggersi dalla possibilità di perdita di dati, è importante eseguire periodicamente [il backup](service-fabric-reliable-services-backup-restore.md) dello stato di uno dei servizi con stato in un archivio con ridondanza geografica. 

È inoltre necessario assicurarsi di avere la possibilità di ripristinare lo stato. Poiché i backup di molti servizi diversi vengono eseguiti in momenti diversi, è necessario assicurarsi che dopo un ripristino, i servizi abbiano una visione coerente l'uno dell'altro. 

Si consideri, ad esempio, una situazione in cui un servizio genera un numero e lo archivia, quindi lo invia a un altro servizio che lo archivia. Dopo un ripristino, è possibile scoprire che il secondo servizio ha il numero, ma il primo no, perché il backup non include tale operazione.

Se si scopre che le repliche rimanenti sono insufficienti per continuare in uno scenario di perdita di dati e non è possibile ricostruire lo stato del servizio dalla telemetria o dallo scarico, la frequenza dei backup determina il miglior obiettivo del punto di ripristino (RPO) possibile. Service Fabric fornisce molti strumenti per testare vari scenari di errore, tra cui quorum permanente e perdita di dati che richiede il ripristino da un backup. Questi scenari sono inclusi come parte degli strumenti di testability in Service Fabric, gestiti dal servizio di analisi degli errori. Per ulteriori informazioni su tali strumenti e modelli, vedere [Introduzione al servizio di analisi degli errori](service-fabric-testability-overview.md). 

> [!NOTE]
> I servizi di sistema possono anche subire la perdita del quorum. L'impatto è specifico del servizio in questione. Ad esempio, la perdita del quorum nel servizio di denominazione influisce sulla risoluzione dei nomi, mentre la perdita del quorum nel servizio Gestione failover blocca la creazione e i failover di nuovi servizi. 
> 
> I servizi di sistema di Service Fabric seguono lo stesso modello dei servizi per la gestione dello stato, ma non è consigliabile provare a spostarli fuori dalla perdita del quorum in caso di potenziale perdita di dati. Al contrario, si consiglia di [cercare supporto](service-fabric-support.md) per trovare una soluzione che è mirata alla vostra situazione. In genere è preferibile attendere semplicemente fino a quando le repliche inbasso restituiscono.
>

#### <a name="troubleshooting-quorum-loss"></a>Risoluzione dei problemi relativi alla perdita del quorumTroubleshooting

Le repliche potrebbero essere inattivo in modo intermittente a causa di un errore temporaneo. Attendere qualche tempo mentre Service Fabric tenta di visualizzarli. Se le repliche sono state inattivo per più di una durata prevista, seguire queste azioni di risoluzione dei problemi:If replicas have been down for more than an expected duration, follow these troubleshooting actions:
- Le repliche potrebbero bloccarsi. Controllare i report di integrità a livello di replica e i log dell'applicazione. Raccogliere i dump di arresto anomalo del sistema ed eseguire le azioni necessarie per il ripristino.
- Il processo di replica potrebbe non rispondere. Esaminare i log dell'applicazione per verificarlo. Raccogliere i dump di processo e quindi arrestare il processo che non risponde. Service Fabric creerà un processo di sostituzione e tenterà di riportare la replica.
- I nodi che ospitano le repliche potrebbero essere inattivi. Riavviare la macchina virtuale sottostante per visualizzare i nodi.

In alcuni stati, potrebbe non essere possibile ripristinare le repliche. Ad esempio, le unità hanno avuto esito negativo o i computer fisicamente non rispondono. In questi casi, Service Fabric deve essere detto di non attendere il ripristino della replica.

*Non* utilizzare questi metodi se una potenziale perdita di dati non è accettabile per portare il servizio online. In tal caso, tutti gli sforzi dovrebbero essere fatti per il recupero di macchine fisiche.

Le azioni seguenti potrebbero causare la perdita di dati. Controlla prima di seguirli.
   
> [!NOTE]
> Non è _mai_ sicuro usare questi metodi se non in modo mirato contro partizioni specifiche. 
>

- Utilizzare `Repair-ServiceFabricPartition -PartitionId` l'API o `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` . Questa API consente di specificare l'ID della partizione per uscire dalla perdita del quorum e in una potenziale perdita di dati.
- Se il cluster rileva errori frequenti che causano la perdita di quorum dei servizi e la potenziale perdita di _dati è accettabile,_ specificare un valore [QuorumLossWaitDuration](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) appropriato può consentire al servizio di eseguire automaticamente il ripristino del servizio. Service Fabric attenderà `QuorumLossWaitDuration` il valore fornito (il valore predefinito è infinito) prima di eseguire il ripristino. Questo metodo *non* è consigliato perché può causare perdite impreviste di dati.

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilità del cluster di Service Fabric
In generale, il cluster Service Fabric è un ambiente altamente distribuito senza singoli punti di errore. Un errore di un nodo non causerà problemi di disponibilità o affidabilità per il cluster, soprattutto perché i servizi di sistema di Service Fabric seguono le stesse linee guida fornite in precedenza. Ovvero, vengono sempre eseguiti con tre o più repliche per impostazione predefinita e i servizi di sistema che sono senza stato eseguiti su tutti i nodi. 

I livelli di rete e di rilevamento degli errori di Service Fabric sono completamente distribuiti. La maggior parte dei servizi di sistema può essere ricreata dai metadati del cluster o sa come risincronizzare il proprio stato da altre posizioni. La disponibilità del cluster può essere compromessa se i servizi di sistema si trovano in situazioni di quorum-loss come quelle descritte in precedenza. In questi casi, potrebbe non essere possibile eseguire determinate operazioni nel cluster, ad esempio l'avvio di un aggiornamento o la distribuzione di nuovi servizi, ma il cluster stesso è ancora attivo. 

I servizi in un cluster in esecuzione continueranno a funzionare in queste condizioni, a meno che non richiedano scritture nei servizi di sistema per continuare a funzionare. Ad esempio, se Gestione failover è in perdita di quorum, tutti i servizi continueranno a essere eseguiti. Tuttavia, tutti i servizi che non riescono non saranno in grado di riavviarsi automaticamente, perché ciò richiede il coinvolgimento di Gestione failover. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Errori di un data center o di un'area di AzureFailures of a datacenter or an Azure region
In rari casi, un datacenter fisico può diventare temporaneamente non disponibile a fronte di una perdita di alimentazione o connettività di rete. In questi casi i cluster e i servizi di Service Fabric presenti nel data center o nell'area di Azure non saranno disponibili. _I dati saranno tuttavia preservati_. 

Per i cluster in esecuzione in Azure, è possibile visualizzare gli aggiornamenti sulle interruzioni del servizio nella [pagina Stato di Azure][azure-status-dashboard]. Nell'evento altamente improbabile che un datacenter fisico sia parzialmente o completamente eliminato, tutti i cluster di Service Fabric ospitati in tale cluster o i servizi al loro interno potrebbero andare persi. Questa perdita include qualsiasi stato di cui non è stato eseguito il backup all'esterno di tale data center o area.

Esistono due diverse strategie per superare il fallimento permanente o sostenuto di un singolo datacenter o area: 

- Eseguire cluster di Service Fabric separati in più aree di questo tipo e usare un meccanismo per il failover e il failback tra questi ambienti. Questo tipo di modello attivo/attivo o attivo/passivo a più cluster richiede codice operativo e di gestione aggiuntivo. Questo modello richiede anche il coordinamento dei backup dai servizi in un data center o in un'area in modo che siano disponibili in altri data center o aree in caso di errore. 
- Eseguire un singolo cluster di Service Fabric che si estende su più data center o più aree. La configurazione minima supportata per questa strategia è tre data center o aree. Il numero consigliato di aree o data center è cinque. 
  
  Questo modello richiede una topologia cluster più complessa. Tuttavia, il vantaggio è che l'errore di un datacenter o di un'area viene convertito da un'emergenza in un normale errore. Questi errori possono essere gestiti dai meccanismi usati per i cluster in una singola area. I domini di errore, i domini di aggiornamento e le regole di posizionamento di Service Fabric assicurano che i carichi di lavoro vengano distribuiti in modo che tollerino gli errori normali. 
  
  Per ulteriori informazioni sui criteri che consentono di gestire i servizi in questo tipo di cluster, vedere [Criteri di posizionamento per i servizi Service Fabric](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Errori casuali che causano errori del clusterRandom failures that lead to cluster failures
Service Fabric ha il concetto di nodi di *seeding.* Si tratta di nodi che gestiscono la disponibilità del cluster sottostante. 

I nodi di seeding contribuiscono a garantire che il cluster rimanga attivo stabilendo lease con altri nodi e fungendo da spareggi durante determinati tipi di errori. Se errori casuali rimuovono la maggior parte dei nodi di seeding nel cluster e non vengono riportati rapidamente, il cluster viene arrestato automaticamente. Il cluster ha quindi esito negativo. 

In Azure Service Fabric Resource Provider gestisce le configurazioni cluster di Service Fabric.In Azure, Service Fabric Resource Provider manages Service Fabric cluster configurations. Per impostazione predefinita, il provider di risorse distribuisce i nodi di cui è stato eseguito il seedinazione tra i domini di errore e di aggiornamento per il *tipo di nodo primario.* Se il tipo di nodo primario è contrassegnato come Durabilità Silver o Gold, quando si rimuove un nodo di seeding (ridimensionando il tipo di nodo primario o rimuovendolo manualmente), il cluster tenterà di promuovere un altro nodo non semio dalla capacità disponibile del tipo di nodo primario. Questo tentativo avrà esito negativo se si dispone di una capacità disponibile inferiore a quella del cluster richiesto per il tipo di nodo primario.

In entrambi i cluster di Service Fabric autonomi e in Azure, il tipo di nodo primario è quello che esegue i semi. Quando si definisce un tipo di nodo primario, Service Fabric sfruterai automaticamente del numero di nodi forniti creando fino a nove nodi di cui è stato eseguito il seeding e sette repliche di ogni servizio di sistema. Se un set di errori casuali elimina la maggior parte di tali repliche contemporaneamente, i servizi di sistema inseriranno la perdita del quorum. Se la maggior parte dei nodi di inizializzazione viene persa, il cluster si arresta subito dopo.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come simulare vari errori utilizzando il [framework di verificabilità](service-fabric-testability-overview.md).
- Lettura di altre risorse sul ripristino di emergenza e sulla disponibilità elevata. Microsoft ha pubblicato molti documenti su questi argomenti. Sebbene alcune di queste risorse facciano riferimento a tecniche specifiche per l'uso in altri prodotti, contengono molte procedure consigliate generali che è possibile applicare nel contesto di Service Fabric:Although some of these resources refer to specific techniques for use in other products, they contain many general best practices that you can apply in the Service Fabric context:
  - [Elenco di controllo per la disponibilitàAvailability checklist](/azure/architecture/checklist/resiliency-per-service)
  - [Esercitazione nel ripristino di emergenza](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure][dr-ha-guide]
- Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
