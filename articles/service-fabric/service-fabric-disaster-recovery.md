---
title: Ripristino di emergenza di Azure Service Fabric
description: Azure Service Fabric offre funzionalità per gestire le emergenze. L'articolo descrive i tipi di emergenze che si possono verificare e come affrontarli.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9c258d8d0a7aa26c96ab4f64017770ebdd153e60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257506"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Ripristino di emergenza in Azure Service Fabric
Una parte essenziale della disponibilità elevata è la garanzia che i servizi possano sopravvivere a tutti i diversi tipi di errori. Questa operazione è particolarmente importante per gli errori non pianificati e all'esterno del controllo. 

Questo articolo descrive alcune modalità di errore comuni che potrebbero essere emergenze se non sono modellate e gestite correttamente. Vengono inoltre illustrate le mitigazioni e le azioni da intraprendere in caso di emergenza. L'obiettivo consiste nel limitare o eliminare il rischio di tempi di inattività o perdita di dati in caso di errori, pianificati o in altro modo.

## <a name="avoiding-disaster"></a>Evitare situazioni di emergenza
L'obiettivo principale di Azure Service Fabric è aiutare a modellare l'ambiente e i servizi in modo che i tipi di errore comuni non siano calamità. 

In generale, esistono due tipi di scenari di emergenza/errore:
- Errori hardware e software
- Errori operativi

### <a name="hardware-and-software-faults"></a>Errori hardware e software
Gli errori hardware e software sono imprevedibili. Il modo più semplice per sopravvivere agli errori è l'esecuzione di più copie del servizio attraverso i limiti di errore hardware o software. 

Se, ad esempio, il servizio è in esecuzione in un solo computer, l'errore di un computer è un'emergenza per quel servizio. Il modo più semplice per evitare questa situazione di emergenza è garantire che il servizio sia in esecuzione su più computer. Il test è necessario anche per assicurarsi che l'errore di un computer non interrompa il servizio in esecuzione. La pianificazione della capacità garantisce che sia possibile creare un'istanza di sostituzione altrove e che la riduzione della capacità non sovraccarichi i servizi rimanenti. 

Lo stesso criterio funziona indipendentemente dall'errore che si sta tentando di evitare. Se, ad esempio, si è preoccupati dell'errore di una rete SAN, è possibile eseguire tra più reti SAN. Se si teme la perdita di un rack di server, si può eseguire il servizio su più rack. Se si è preoccupati della perdita dei Data Center, il servizio deve essere eseguito in più aree di Azure, in più zone di disponibilità di Azure o nei propri Data Center. 

Quando un servizio viene esteso tra più istanze fisiche (computer, rack, Data Center, aree), si è ancora soggetti ad alcuni tipi di errori simultanei. Tuttavia, uno o più errori di un determinato tipo, ad esempio una singola macchina virtuale o un collegamento di rete, vengono gestiti automaticamente e pertanto non sono più una "emergenza". 

Service Fabric fornisce i meccanismi per espandere il cluster e gestire nuovamente i nodi e i servizi non riusciti. Service Fabric consente inoltre l'esecuzione di molte istanze dei servizi per evitare errori non pianificati che si verificano in caso di calamità reali.

Potrebbero verificarsi motivi per cui l'esecuzione di una distribuzione sufficientemente grande per gli errori di estensione non è fattibile. Ad esempio, potrebbero essere necessarie più risorse hardware rispetto a quelle di cui si è disposti a pagare rispetto alla probabilità di errore. Quando si gestiscono applicazioni distribuite, gli hop di comunicazione aggiuntivi o i costi di replica dello stato tra le distanze geografiche possono causare una latenza inaccettabile. La posizione di questa linea di confine varia per ogni applicazione. 

Per gli errori software in particolare, l'errore potrebbe trovarsi nel servizio che si sta tentando di ridimensionare. In questo caso, un numero maggiore di copie non impedisce l'emergenza, perché la condizione di errore è correlata tra tutte le istanze.

### <a name="operational-faults"></a>Errori operativi
Anche se il servizio è esteso in tutto il mondo con molte ridondanze, può comunque essere soggetto a eventi di emergenza, Ad esempio, è possibile che qualcuno riconfigura accidentalmente il nome DNS per il servizio o lo elimini in modo corretto. 

Si supponga, ad esempio, di disporre di un servizio di Service Fabric con stato e che un utente lo abbia eliminato inavvertitamente. A meno che non esistano altre misure di mitigazione, il servizio e tutto lo stato in cui si trovavano sono diventati obsoleti. Questi tipi di emergenze operative ("errori") richiedono mitigazioni e procedure per il ripristino diverse rispetto ai normali errori imprevisti. 

Il modo migliore per evitare questi tipi di errori operativi è:
- Limitare l'accesso operativo all'ambiente.
- Controllare in modo rigoroso le operazioni pericolose.
- Imporre l'automazione, impedire modifiche manuali o fuori banda e convalidare modifiche specifiche nell'ambiente prima di applicarle.
- Assicurarsi che le operazioni distruttive siano "soft". Le operazioni Soft non hanno effetto immediato o possono essere annullate in un intervallo di tempo.

Service Fabric fornisce meccanismi per evitare errori operativi, ad esempio per fornire il controllo degli accessi in [base al ruolo](service-fabric-cluster-security-roles.md) per le operazioni del cluster. La maggior parte di questi errori operativi richiede tuttavia attività organizzative e altri sistemi. Service Fabric fornisce meccanismi per la sopravvivenza degli errori operativi, in particolare di [backup e ripristino per i servizi con stato](service-fabric-backuprestoreservice-quickstart-azurecluster.md).

## <a name="managing-failures"></a>Gestione degli errori
L'obiettivo di Service Fabric è la gestione automatica degli errori. Tuttavia, per gestire alcuni tipi di errori, i servizi devono disporre di codice aggiuntivo. Altri tipi di errori _non_ devono essere risolti automaticamente per motivi di sicurezza e continuità aziendale. 

### <a name="handling-single-failures"></a>Gestione di errori singoli
I computer singoli possono essere soggetti a errori per ogni genere di motivo. Talvolta si tratta di cause hardware, ad esempio alimentatori e guasti hardware di rete. Altri errori sono nel software. Sono inclusi gli errori del sistema operativo e del servizio stesso. Service Fabric rileva automaticamente questi tipi di errori, inclusi i casi in cui il computer viene isolato da altri computer a causa di problemi di rete.

Indipendentemente dal tipo di servizio, l'esecuzione di una sola istanza provoca tempi di inattività del servizio se per qualche motivo l'unica copia del codice genera un errore. 

Per gestire qualsiasi singolo errore, la cosa più semplice da fare è assicurarsi che i servizi vengano eseguiti su più di un nodo per impostazione predefinita. Per i servizi senza stato, verificare che `InstanceCount` sia maggiore di 1. Per i servizi con stato, la raccomandazione minima è che `TargetReplicaSetSize` e `MinReplicaSetSize` sono entrambi impostati su 3. L'esecuzione di più copie del codice del servizio assicura al servizio la capacità di gestire qualsiasi errore automaticamente. 

### <a name="handling-coordinated-failures"></a>Gestione di errori coordinati
Gli errori coordinati in un cluster possono essere causati da errori e modifiche dell'infrastruttura pianificata o non pianificata o da modifiche software pianificate. Service Fabric modella le zone di infrastruttura che sperimentano errori coordinati come *domini di errore*. Le aree in cui si verificheranno modifiche del software coordinate vengono modellate come *domini di aggiornamento*. Per ulteriori informazioni sui domini di errore, i domini di aggiornamento e la topologia del cluster, vedere [descrivere un cluster di Service Fabric tramite Gestione risorse cluster](service-fabric-cluster-resource-manager-cluster-description.md).

Per impostazione predefinita, Service Fabric considera i domini di errore e di aggiornamento quando si pianifica la posizione in cui devono essere eseguiti i servizi. Per impostazione predefinita, Service Fabric prova a garantire che i servizi vengano eseguiti in diversi domini di errore e di aggiornamento in modo che, se si verificano modifiche pianificate o non pianificate, i servizi rimangano disponibili. 

Si immagini, ad esempio, che l'errore di una fonte di alimentazione provochi un errore simultaneo di tutte le macchine in un rack. Con più copie del servizio in esecuzione, la perdita di molti computer nell'errore del dominio di errore si riassume solo un altro esempio di un singolo errore per un servizio. Questo è il motivo per cui la gestione di domini di errore e di aggiornamento è essenziale per garantire la disponibilità elevata dei servizi. 

Quando si esegue Service Fabric in Azure, i domini di errore e i domini di aggiornamento vengono gestiti automaticamente. In altri ambienti potrebbero non essere. Se si stanno creando cluster personalizzati in locale, assicurarsi di eseguire il mapping e pianificare correttamente il layout del dominio di errore.

I domini di aggiornamento sono utili per le aree di modellazione in cui il software verrà aggiornato nello stesso momento. Per questo motivo, i domini di aggiornamento spesso definiscono anche i limiti in cui il software viene disattivato durante gli aggiornamenti pianificati. Gli aggiornamenti sia di Service Fabric che dei servizi seguono lo stesso modello. Per ulteriori informazioni sugli aggiornamenti in sequenza, i domini di aggiornamento e il modello di integrità di Service Fabric che consente di impedire modifiche impreviste che influiscono sul cluster e sul servizio, vedere:

 - [Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)
 - [Esercitazione sull'aggiornamento dell'applicazione](service-fabric-application-upgrade-tutorial.md)
 - [Modello di integrità Service Fabric](service-fabric-health-introduction.md)

È possibile visualizzare il layout del cluster usando la mappa del cluster disponibile in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Nodi distribuiti nei domini di errore in Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modellazione di aree di errore, aggiornamento in sequenza, esecuzione di molte istanze del codice e dello stato del servizio, regole di posizionamento per garantire che i servizi vengano eseguiti nei domini di errore e di aggiornamento e il monitoraggio dell'integrità predefinito sono solo *alcune* delle funzionalità che Service Fabric fornisce per evitare che i normali problemi operativi e gli errori si trasformano in situazioni di emergenza. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Gestione di errori hardware o software simultanei
Abbiamo parlato di singoli errori. Come si può vedere, sono facili da gestire per i servizi con e senza stato, semplicemente mantenendo più copie del codice (e dello stato) in esecuzione nei domini di errore e di aggiornamento. 

I servizi possono essere interessati anche da più errori casuali simultanei. È più probabile che si verifichino tempi di inattività o una vera emergenza.


#### <a name="stateless-services"></a>Servizi senza stato
Il numero di istanze di un servizio senza stato indica il numero desiderato di istanze che devono essere in esecuzione. Quando una o tutte le istanze di hanno esito negativo, Service Fabric risponde creando automaticamente istanze di sostituzione in altri nodi. Service Fabric continua a creare sostituzioni fino a quando il servizio non torna al numero di istanze desiderato.

Si supponga, ad esempio, che il servizio senza stato abbia un `InstanceCount` valore pari a-1. Questo valore indica che un'istanza deve essere in esecuzione in ogni nodo del cluster. Se alcune di queste istanze hanno esito negativo, Service Fabric rileverà che il servizio non si trova nello stato desiderato e tenterà di creare le istanze nei nodi in cui sono mancanti.

#### <a name="stateful-services"></a>Servizi con stato
Esistono due tipi di servizi con stato:
- Con stato permanente con stato permanente.
- Con stato non permanente. (Lo stato viene archiviato in memoria).

Il ripristino dall'errore di un servizio con stato dipende dal tipo di servizio con stato, dal numero di repliche del servizio e dal numero di repliche non riuscite.

In un servizio con stato, i dati in ingresso vengono replicati tra le repliche (la replica primaria e le repliche secondarie attive). Se la maggior parte delle repliche riceve i dati, i dati vengono considerati di cui è stato eseguito il commit del *quorum* . Per cinque repliche, tre saranno un quorum. Ciò significa che in qualsiasi momento sarà presente almeno un quorum di repliche con i dati più recenti. Se le repliche hanno esito negativo (ad indicare due di cinque), è possibile usare il valore del quorum per calcolare se è possibile eseguire il ripristino. Poiché le restanti tre di cinque repliche sono ancora in esecuzione, è garantito che almeno una replica avrà dati completi.

Quando un quorum di repliche ha esito negativo, la partizione viene dichiarata in uno stato di *perdita del quorum* . Supponiamo che una partizione includa cinque repliche, il che significa che per almeno tre è garantita la presenza di dati completi. Se un quorum (tre cinque) di repliche ha esito negativo, Service Fabric non è in grado di determinare se le repliche rimanenti (due cinque) hanno dati sufficienti per ripristinare la partizione. Nei casi in cui Service Fabric rileva la perdita del quorum, il comportamento predefinito è quello di impedire scritture aggiuntive nella partizione, dichiarare la perdita del quorum e attendere il ripristino di un quorum di repliche.

Determinare se si è verificata un'emergenza per un servizio con stato e quindi gestirla dopo tre fasi:

1. Determinare se si è verificata una perdita del quorum.
   
   La perdita del quorum viene dichiarata quando la maggior parte delle repliche di un servizio con stato è inattiva allo stesso tempo.
2. Determinare se la perdita del quorum è permanente o meno.
   
   Nella maggior parte dei casi gli errori sono temporanei. I processi vengono riavviati, i nodi vengono riavviati, le macchine virtuali vengono riavviate e le partizioni di rete si risanano. In alcuni casi, tuttavia, gli errori sono permanenti. Il fatto che gli errori siano permanenti o non dipenda dal fatto che il servizio con stato mantenga lo stato o se lo mantiene solo in memoria: 
   
   - Per i servizi senza stato persistente, un errore di uno o più quorum di repliche determina _immediatamente_ una perdita del quorum permanente. Quando Service Fabric rileva la perdita del quorum in un servizio non persistente con stato, passa immediatamente alla fase 3 dichiarando una (potenziale) perdita di dati. La perdita di dati è sensata perché Service Fabric sa che non ci sono punti in attesa di tornare alle repliche. Anche se si ripristinano, i dati andranno perduti a causa della natura non permanente del servizio.
   - Per i servizi permanenti con stato, un errore di un quorum o di più repliche fa in modo che Service Fabric attenda che le repliche tornino e ripristinino il quorum. Si verifica di conseguenza un'interruzione del servizio per tutte le _operazioni di scrittura_ alla partizione interessata (o "set di repliche") del servizio. Tuttavia, le letture potrebbero ancora essere possibili con garanzie di coerenza ridotte. La quantità di tempo predefinita che Service Fabric attende il ripristino del quorum è *infinita*, perché la continuazione è un evento di perdita di dati (potenziale) e comporta altri rischi. Ciò significa che Service Fabric non procederà al passaggio successivo, a meno che un amministratore non intervenga per dichiarare la perdita di dati.
3. Determinare se i dati vengono persi e il ripristino dai backup.

   Se la perdita del quorum è stata dichiarata (automaticamente o tramite un'azione amministrativa), Service Fabric e i servizi passano a determinare se i dati sono stati effettivamente persi. A questo punto, Service Fabric sa anche che le altre repliche non vengono restituite. Si arriva così alla decisione di smettere di attendere che la perdita del quorum si risolva da sola. La migliore linea di azione per il servizio è in genere bloccare la situazione e attendere un intervento specifico da parte dell'amministratore.
   
   Quando Service Fabric chiama il `OnDataLossAsync` metodo, è sempre a causa di una perdita di dati _sospetta_ . Service Fabric assicura che la chiamata venga inviata alla _migliore_ replica rimanente, vale a dire quella che ha fatto i maggiori progressi. 
   
   Il motivo per cui si dice sempre che la perdita di dati _sospetta_ è che è possibile che la replica rimanente abbia lo stesso stato del primario quando il quorum è andato perso. Senza quello stato con cui fare il confronto, tuttavia, non esiste un modo utile che dica a Service Fabric o agli operatori l'effettivo stato.     
   
   Cosa fa quindi una tipica implementazione del metodo `OnDataLossAsync`?
   1. Log di implementazione che `OnDataLossAsync` sono stati attivati e vengono attivati tutti gli avvisi amministrativi necessari.
   1. In genere, l'implementazione viene sospesa e resta in attesa di prendere decisioni e azioni manuali. Ciò è dovuto al fatto che, anche se sono disponibili backup, potrebbe essere necessario prepararli. 
   
      Se, ad esempio, due servizi diversi coordinano le informazioni, è possibile che sia necessario modificare tali backup per assicurarsi che, dopo l'esecuzione del ripristino, siano coerenti le informazioni a cui questi due servizi sono interessati. 
   1. Spesso sono presenti altri dati di telemetria o esaurimento del servizio. Questi metadati potrebbero essere contenuti in altri servizi o nei log. Queste informazioni possono essere utilizzate in base alle esigenze per determinare se sono state ricevute ed elaborate chiamate nel database primario che non erano presenti nel backup o replicate in questa particolare replica. È possibile che tali chiamate debbano essere riprodotte o aggiunte al backup prima che sia possibile eseguire il ripristino.  
   1. L'implementazione confronta lo stato della replica rimanente con quello contenuto in tutti i backup disponibili. Se si usano Service Fabric raccolte Reliable Collections, sono disponibili [strumenti e processi](service-fabric-reliable-services-backup-restore.md) . L'obiettivo consiste nel verificare se lo stato all'interno della replica è sufficiente e per vedere quali potrebbero mancare i backup.
   1. Al termine del confronto, dopo che il ripristino è stato completato (se necessario), il codice del servizio deve restituire **true** se sono state apportate modifiche allo stato. Se la replica ha determinato che si tratta della migliore copia disponibile dello stato e non ha apportato modifiche, il codice restituisce **false**. 
   
      Il valore **true** indica che qualsiasi _altra_ replica rimanente potrebbe ora essere incoerente con questa. Le repliche rimanenti verranno eliminate e ricreate da questa replica. Il valore **false** indica che non sono state apportate modifiche allo stato, quindi le altre repliche possono conservarne gli elementi. 

È fondamentale che gli autori del servizio esercitino potenziali scenari di perdita di dati e di errore prima che i servizi vengano distribuiti nell'ambiente di produzione. Per evitare la possibilità di perdita di dati, è importante [eseguire periodicamente il backup dello stato](service-fabric-reliable-services-backup-restore.md) dei servizi con stato in un archivio con ridondanza geografica. 

È inoltre necessario assicurarsi di avere la possibilità di ripristinare lo stato. Poiché i backup di molti servizi diversi vengono eseguiti in momenti diversi, è necessario verificare che dopo un ripristino i servizi abbiano una visualizzazione coerente. 

Si consideri, ad esempio, una situazione in cui un servizio genera un numero e lo archivia, quindi lo invia a un altro servizio che lo archivia anch ' esso. Dopo un ripristino, si potrebbe scoprire che il secondo servizio ha il numero ma il primo non lo è, perché il backup non include l'operazione.

Se si scopre che le repliche rimanenti non sono sufficienti per continuare in uno scenario di perdita dei dati e non è possibile ricostruire lo stato del servizio dalla telemetria o dallo scarico, la frequenza dei backup determina il migliore obiettivo del punto di ripristino (RPO). In Service Fabric sono disponibili molti strumenti per testare diversi scenari di errore, tra cui il quorum permanente e la perdita di dati che richiedono il ripristino da un backup. Questi scenari sono inclusi come parte degli strumenti di testabilità in Service Fabric, gestiti dal servizio di analisi degli errori. Per ulteriori informazioni su questi strumenti e modelli, vedere [Introduzione al servizio di analisi degli errori](service-fabric-testability-overview.md). 

> [!NOTE]
> I servizi di sistema possono inoltre subire una perdita di quorum. L'effetto è specifico per il servizio in questione. Ad esempio, la perdita del quorum nel servizio di denominazione influiscono sulla risoluzione dei nomi, mentre la perdita del quorum nel servizio Gestione failover blocca la creazione e il failover di nuovi servizi. 
> 
> I servizi di sistema Service Fabric seguono lo stesso modello dei servizi per la gestione dello stato, ma non è consigliabile provare a spostarli fuori dalla perdita del quorum e in una potenziale perdita di dati. È invece consigliabile  [richiedere supporto](service-fabric-support.md) per trovare una soluzione destinata alla propria situazione. In genere è preferibile attendere semplicemente il ritorno delle repliche.
>

#### <a name="troubleshooting-quorum-loss"></a>Risoluzione dei problemi relativi alla perdita del quorum

Le repliche potrebbero essere inattive a intermittenza a causa di un errore temporaneo. Attendere un po' di tempo mentre Service Fabric tenta di riportarli. Se le repliche sono inattive per un periodo di tempo superiore a quello previsto, attenersi alle seguenti azioni di risoluzione dei problemi:
- Le repliche potrebbero essere arrestate in modo anomalo. Controllare i report sull'integrità a livello di replica e i log dell'applicazione. Raccogliere i dump di arresto anomalo del sistema ed eseguire le azioni necessarie per il ripristino.
- Potrebbe non essere possibile rispondere al processo di replica. Esaminare i registri applicazioni per verificarlo. Raccogliere i dump del processo, quindi arrestare il processo che non risponde. Service Fabric creerà un processo di sostituzione e tenterà di ripristinare la replica.
- I nodi che ospitano le repliche potrebbero essere inattivi. Riavviare la macchina virtuale sottostante per riportare i nodi.

In alcuni casi potrebbe non essere possibile recuperare le repliche. Ad esempio, le unità non sono riuscite o i computer non rispondono fisicamente. In questi casi, è necessario che Service Fabric non attendano il ripristino della replica.

Non usare questi metodi se è possibile che la perdita di dati *non* sia accettabile per portare il servizio online. In tal caso, è necessario effettuare tutte le attività per il ripristino di computer fisici.

Le azioni seguenti potrebbero causare la perdita di dati. Verificare prima di procedere.
   
> [!NOTE]
> Non è _mai_ sicuro usare questi metodi, tranne che in modo mirato, su partizioni specifiche. 
>

- Usare l' `Repair-ServiceFabricPartition -PartitionId` `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API o. Questa API consente di specificare l'ID della partizione per spostarsi fuori dalla perdita del quorum e in una potenziale perdita di dati.
- Se nel cluster si verificano errori frequenti che fanno sì che i servizi entrano in uno stato di perdita del quorum e la potenziale _perdita di dati è accettabile, è_possibile specificare un valore [QuorumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) appropriato per consentire il ripristino automatico del servizio. Service Fabric attenderà il valore specificato `QuorumLossWaitDuration` (il valore predefinito è infinito) prima di eseguire il ripristino. Questo metodo *non* è consigliato perché può causare perdite di dati impreviste.

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilità del cluster di Service Fabric
In generale, il cluster Service Fabric è un ambiente altamente distribuito senza singoli punti di errore. Un errore di un nodo non provocherà problemi di disponibilità o affidabilità per il cluster, principalmente perché i servizi di sistema Service Fabric seguono le stesse linee guida fornite in precedenza. Ovvero, vengono sempre eseguiti con tre o più repliche per impostazione predefinita e i servizi di sistema senza stato vengono eseguiti in tutti i nodi. 

I livelli di rete e di rilevamento degli errori di Service Fabric sono completamente distribuiti. La maggior parte dei servizi di sistema può essere ricreata dai metadati del cluster o sa come risincronizzare il proprio stato da altre posizioni. La disponibilità del cluster può essere compromessa se i servizi di sistema entrano in situazioni di perdita del quorum come quelle descritte in precedenza. In questi casi, potrebbe non essere possibile eseguire determinate operazioni nel cluster, ad esempio l'avvio di un aggiornamento o la distribuzione di nuovi servizi, ma il cluster stesso è ancora attivo. 

I servizi in un cluster in esecuzione continueranno a funzionare in queste Condizioni a meno che non richiedano scritture nei servizi di sistema per continuare a funzionare. Se ad esempio Gestione failover è in perdita del quorum, tutti i servizi continueranno a essere eseguiti. Tuttavia, tutti i servizi che non riescono a riavviarsi automaticamente, perché questa operazione richiede il coinvolgimento dei Gestione failover. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Errori di un Data Center o di un'area di Azure
In rari casi, un data center fisico può diventare temporaneamente non disponibile dalla perdita di energia o della connettività di rete. In questi casi i cluster e i servizi di Service Fabric presenti nel data center o nell'area di Azure non saranno disponibili. _I dati saranno tuttavia preservati_. 

Per i cluster in esecuzione in Azure, è possibile visualizzare gli aggiornamenti sulle interruzioni del servizio nella [pagina Stato di Azure][azure-status-dashboard]. Nell'evento estremamente improbabile che un data center fisico sia parzialmente o completamente distrutto, eventuali cluster Service Fabric ospitati o i servizi al loro interno potrebbero andare persi. Questa perdita include uno stato di cui non è stato eseguito il backup all'esterno del Data Center o dell'area geografica.

Ci sono due strategie diverse per sopravvivere all'errore permanente o prolungato di un singolo Data Center o area: 

- Eseguire cluster di Service Fabric separati in più aree di questo tipo e usare un meccanismo per il failover e il failback tra questi ambienti. Questo tipo di modello a più cluster attivo/attivo o attivo/passivo richiede codice di gestione e di operazioni aggiuntivo. Questo modello richiede anche il coordinamento dei backup dai servizi in un Data Center o in un'area, in modo che siano disponibili in altri Data Center o aree in cui uno non riesce. 
- Eseguire un singolo cluster di Service Fabric che si estende su più data center o più aree. La configurazione minima supportata per questa strategia è di tre Data Center o aree geografiche. Il numero consigliato di aree o data center è cinque. 
  
  Questo modello richiede una topologia di cluster più complessa. Tuttavia, il vantaggio è che l'errore di un Data Center o di un'area viene convertito da un'emergenza in un errore normale. Questi errori possono essere gestiti dai meccanismi usati per i cluster in una singola area. I domini di errore, i domini di aggiornamento e le regole di posizionamento Service Fabric garantiscono che i carichi di lavoro vengano distribuiti in modo da tollerare gli errori normali. 
  
  Per ulteriori informazioni sui criteri che consentono di utilizzare i servizi in questo tipo di cluster, vedere [criteri di posizionamento per i servizi Service Fabric](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Errori casuali che portano a errori del cluster
Service Fabric ha il concetto di *nodi di inizializzazione*. Si tratta di nodi che gestiscono la disponibilità del cluster sottostante. 

I nodi di inizializzazione consentono di garantire che il cluster rimanga attivo stabilendo lease con altri nodi e fungendo da tiebreakers durante determinati tipi di errori. Se gli errori casuali rimuovono la maggior parte dei nodi di inizializzazione nel cluster e non vengono restituiti rapidamente, il cluster si arresta automaticamente. Quindi il cluster ha esito negativo. 

In Azure Service Fabric provider di risorse gestisce Service Fabric configurazioni del cluster. Per impostazione predefinita, il provider di risorse distribuisce i nodi di inizializzazione nei domini di errore e di aggiornamento per il *tipo di nodo primario*. Se il tipo di nodo primario è contrassegnato come durabilità Silver o Gold, quando si rimuove un nodo di inizializzazione (ridimensionando il tipo di nodo primario o rimuovendo manualmente), il cluster tenterà di innalzare di livello un altro nodo non di inizializzazione dalla capacità disponibile del tipo di nodo primario. Questo tentativo avrà esito negativo se la capacità disponibile è inferiore a quella del livello di affidabilità del cluster per il tipo di nodo primario.

In entrambi i cluster Service Fabric autonomo e Azure, il tipo di nodo primario è quello che esegue i seed. Quando si definisce un tipo di nodo primario, Service Fabric utilizzerà automaticamente il numero di nodi forniti creando fino a nove nodi di inizializzazione e sette repliche di ogni servizio di sistema. Se un set di errori casuali estrae la maggior parte delle repliche simultaneamente, i servizi di sistema immetteranno la perdita del quorum. Se la maggior parte dei nodi di inizializzazione viene persa, il cluster si arresta subito dopo.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come simulare diversi errori usando il [Framework di testabilità](service-fabric-testability-overview.md).
- Lettura di altre risorse sul ripristino di emergenza e sulla disponibilità elevata. Microsoft ha pubblicato molti documenti su questi argomenti. Sebbene alcune di queste risorse facciano riferimento a tecniche specifiche per l'utilizzo in altri prodotti, contengono numerose procedure consigliate generali che è possibile applicare nel contesto Service Fabric:
  - [Elenco di controllo della disponibilità](/azure/architecture/checklist/resiliency-per-service)
  - [Esercitazione nel ripristino di emergenza](../azure-sql/database/disaster-recovery-drills.md)
  - [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure][dr-ha-guide]
- Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
