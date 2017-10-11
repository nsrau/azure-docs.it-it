---
title: Ripristino di emergenza di Azure Service Fabric | Documentazione Microsoft
description: "Azure Service Fabric offre le funzionalità necessarie per affrontare tutti i tipi di emergenze. L'articolo descrive i tipi di emergenze che si possono verificare e come affrontarli."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 5346e331d76149ac3aed7aaf11eb3171e0ac5cfc
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Ripristino di emergenza in Azure Service Fabric
Una parte fondamentale della distribuzione a disponibilità elevata consiste nel garantire la resistenza dei servizi a tutti i tipi di errori. Ciò è particolarmente importante per gli errori imprevisti e incontrollabili. Questo articolo descrive alcune modalità di errore comuni che possono generare situazioni di emergenza se non organizzate e gestite correttamente. Illustra anche le mitigazioni e le azioni da intraprendere in presenza di un'emergenza. L'obiettivo consiste nel limitare o eliminare il rischio di tempi di inattività o di perdita di dati quando si verificano errori, pianificati o imprevisti.

## <a name="avoiding-disaster"></a>Evitare situazioni di emergenza
L'obiettivo principale di Service Fabric è di aiutare a organizzare sia l'ambiente che i servizi in modo che gli errori comuni non generino situazioni di emergenza. 

Esistono, in generale, due tipi di scenari di emergenza/errore:

1. Errori hardware o software
2. Errori operativi

### <a name="hardware-and-software-faults"></a>Errori hardware e software
Gli errori hardware e software sono imprevedibili. Il modo più semplice per resistere a questi errori è eseguire più copie del servizio estese oltre i limiti dell'errore hardware o software. Se ad esempio il servizio è in esecuzione solo in un computer specifico, l'errore di quel computer determina una situazione di emergenza per il servizio. Questo tipo di emergenza può essere evitato semplicemente eseguendo il servizio in più computer. Anche i test sono necessari per garantire che l'errore in un unico computer non interrompa l'esecuzione del servizio. La pianificazione della capacità offre la possibilità di creare un'istanza sostitutiva in un'altra posizione ed evita che la riduzione della capacità sovraccarichi i servizi restanti. Lo stesso criterio funziona indipendentemente dall'errore che si sta tentando di evitare. Ad esempio, se si teme il guasto di una rete SAN, si può eseguire i servizi in più reti SAN. Se si teme la perdita di un rack di server, si può eseguire il servizio su più rack. Se si teme la perdita di dati nei data center, è consigliabile eseguire il servizio in più data center o aree di Azure. 

Quando si esegue il servizio con questo tipo di modalità estesa, il rischio che si verifichino alcuni tipi di errori simultanei è ancora presente, tuttavia i singoli errori e anche quelli multipli di un determinato tipo (ad esempio un errore in una VM o un collegamento di rete) vengono gestiti automaticamente, di conseguenza non generano più una situazione di emergenza. Service Fabric prevede molti meccanismi per espandere il cluster e far tornare disponibili i nodi e i servizi. Service Fabric consente anche di eseguire molte istanze dei servizi per evitare che questi tipi di errori imprevisti si trasformino in vere e proprie emergenze.

Esistono alcuni motivi per cui l'esecuzione di una distribuzione sufficientemente grande da coprire gli errori potrebbe non essere fattibile. Potrebbe, ad esempio, richiedere l'implementazione di risorse hardware aggiuntive il cui costo sarebbe sproporzionato rispetto alla possibilità che si verifichi un errore. Quando si lavora con le applicazioni distribuite, è possibile che gli hop di comunicazione o i costi di replica di stato aggiuntivi su distanze geografiche causino una latenza inaccettabile. La posizione di questa linea di confine varia per ogni applicazione. Per gli errori software, in particolare, l'errore potrebbe risiedere nel servizio che si sta tentando di scalare. La creazione di più copie, in questo caso, non previene la situazione di emergenza perché la condizione di errore è correlata a tutte le istanze.

### <a name="operational-faults"></a>Errori operativi
Anche se il servizio è esteso in tutto il mondo con molte ridondanze, può comunque essere soggetto a eventi di emergenza, ad esempio se qualcuno riconfigura accidentalmente il nome DNS per il servizio o lo elimina definitivamente. Si supponga, ad esempio, di disporre di un servizio di Service Fabric con stato e che un utente lo abbia eliminato inavvertitamente. A meno che non esistano altre mitigazioni, il servizio e il relativo stato sono ormai eliminati. Questi tipi di emergenze operative ("errori") richiedono mitigazioni e procedure per il ripristino diverse rispetto ai normali errori imprevisti. 

I modi migliori per evitare questi tipi di errori operativi consistono nel
1. limitare l'accesso operativo all'ambiente
2. controllare rigorosamente le operazioni pericolose
3. favorire l'automazione, impedire le modifiche manuali o fuori banda e convalidare le modifiche specifiche nell'ambiente effettivo prima di applicarle
4. assicurarsi che le operazioni distruttive siano "soft". Le operazioni soft non hanno effetto immediato o possono essere annullate entro un intervallo di tempo.

Per evitare gli errori operativi, Service Fabric offre alcuni meccanismi come il controllo degli accessi [in base al ruolo](service-fabric-cluster-security-roles.md) per le operazioni nel cluster. La maggior parte di questi errori operativi richiede tuttavia attività organizzative e altri sistemi. Service Fabric offre alcuni meccanismi per resistere agli errori operativi, i più noti dei quali sono il backup e il ripristino per i servizi con stato.

## <a name="managing-failures"></a>Gestione degli errori
Lo scopo di Service Fabric è quasi sempre la gestione automatica degli errori. Per gestire alcuni tipi di errori, tuttavia, i servizi devono essere dotati di codice aggiuntivo. Altri tipi di errori _non_ devono essere risolti automaticamente per motivi di continuità e sicurezza aziendali. 

### <a name="handling-single-failures"></a>Gestione di errori singoli
I computer singoli possono essere soggetti a errori per ogni genere di motivo. Alcuni di questi sono causati dall'hardware, ad esempio i guasti agli alimentatori e all'hardware di rete. Altri errori sono nel software. Questi errori includono quelli del sistema operativo effettivo e del servizio stesso. Service Fabric rileva automaticamente questi tipi di errori, inclusi i casi in cui il computer viene isolato dagli altri computer a causa di problemi di rete.

Indipendentemente dal tipo di servizio, l'esecuzione di una sola istanza provoca tempi di inattività del servizio se per qualche motivo l'unica copia del codice genera un errore. 

Per gestire qualsiasi errore singolo, la soluzione più semplice è assicurarsi di eseguire i servizi in più nodi come impostazione predefinita. Per i servizi senza stato questo risultato si ottiene con un `InstanceCount` maggiore di 1. Per i servizi con stato il requisito minimo è sempre un `TargetReplicaSetSize` e un `MinReplicaSetSize` di almeno 3. L'esecuzione di più copie del codice del servizio assicura al servizio la capacità di gestire qualsiasi errore automaticamente. 

### <a name="handling-coordinated-failures"></a>Gestione di errori coordinati
Gli errori coordinati possono verificarsi in un cluster a causa di modifiche ed errori nell'infrastruttura pianificati o imprevisti di o modifiche software pianificate. Service Fabric crea le zone di infrastruttura che sperimentano errori coordinati come domini di errore. Le aree che sperimentano le modifiche software coordinate vengono modellate come domini di aggiornamento. Altre informazioni sui domini di errore e di aggiornamento sono disponibili in [questo documento](service-fabric-cluster-resource-manager-cluster-description.md) che descrive la definizione e la topologia del cluster.

Per impostazione predefinita, Service Fabric considera i domini di errore e di aggiornamento quando si pianificano le posizioni di esecuzione dei servizi. Per impostazione predefinita, Service Fabric tenta di assicurarsi che i servizi vengano eseguiti in diversi domini di errore e di aggiornamento in modo che, se si verificano modifiche pianificate o impreviste, i servizi rimangano disponibili. 

Si supponga, ad esempio, che un guasto all'alimentazione determini un errore in un rack di computer. Se sono in esecuzione più copie del servizio, la perdita di molti computer per l'errore del dominio di errore si trasforma in un altro semplice esempio di errore singolo per un determinato servizio. Questo perché la gestione dei domini di errore è essenziale per garantire la disponibilità elevata dei servizi. Quando si esegue Azure Service Fabric, i domini di errore vengono gestiti automaticamente. In altri ambienti potrebbero non esserlo. Se si creano cluster in locale, assicurarsi di eseguire il mapping e di pianificare il layout del dominio di errore correttamente.

I domini di aggiornamento sono utili per la creazione di aree in cui il software viene aggiornato nello stesso momento. Per questo motivo, i domini di aggiornamento spesso definiscono anche i limiti in cui software viene disattivato durante gli aggiornamenti pianificati. Gli aggiornamenti sia di Service Fabric che dei servizi seguono lo stesso modello. Per altre informazioni sulla distribuzione degli aggiornamenti, i domini di aggiornamento e il modello di integrità di Service Fabric che aiuta a impedire che eventuali modifiche accidentali abbiano ripercussioni sul cluster e sul servizio, vedere questi documenti:

 - [Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)
 - [Esercitazione sull'aggiornamento di un'applicazione ](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric Health Model](service-fabric-health-introduction.md) (Modello di integrità di Service Fabric)

È possibile visualizzare il layout del cluster usando la mappa del cluster disponibile in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Nodi distribuiti nei domini di errore in Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> La creazione di aree di errore, la distribuzione di aggiornamenti, l'esecuzione di molte istanze del codice e dello stato del servizio, le regole di posizionamento che garantiscono l'esecuzione dei servizi nei domini di errore e di aggiornamento e il monitoraggio dell'integrità incorporato sono solo **alcune** delle funzionalità che Service Fabric offre per evitare che normali errori e problemi operativi si trasformino in situazioni di emergenza. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Gestione di errori hardware o software simultanei
Si è parlato finora degli errori singoli. Come si può vedere, questi errori possono essere facilmente gestiti sia per i servizi senza stato che per quelli con stato semplicemente mantenendo più copie del codice (e dello stato) in esecuzione in domini di errore e di aggiornamento. I servizi possono essere interessati anche da più errori casuali simultanei. Questi errori hanno più probabilità di generare un'emergenza effettiva.


### <a name="random-failures-leading-to-service-failures"></a>Errori casuali che portano a errori del servizio
Si supponga che il servizio abbia un `InstanceCount` di 5 e che diversi nodi in cui sono in esecuzione tali istanze generino tutti un errore nello stesso momento. Service Fabric risponde creando automaticamente istanze sostitutive in altri nodi. Continuerà a creare sostituzioni fino a quando il servizio non avrà raggiunto di nuovo il numero di istanze desiderato. Si supponga come altro esempio che sia presente un servizio senza stato con un `InstanceCount` di -1, vale a dire che il servizio viene eseguito in tutti i nodi validi del cluster. In alcune di queste istanze si verifica un errore. In questo caso Service Fabric nota che il servizio non è nello stato desiderato e tenta di creare le istanze nei nodi in cui mancano. 

Per i servizi con stato la situazione varia a seconda che il servizio abbia uno stato persistente o meno. Dipende inoltre dal numero di repliche del servizio e dal numero di quelle che hanno generato un errore. La determinazione di una situazione di emergenza per un servizio con stato e la relativa gestione seguono tre fasi:

1. Determinazione di una eventuale perdita del quorum
 - Si ha una perdita del quorum ogni volta che la maggioranza delle repliche di un servizio con stato è inattiva nello stesso momento, inclusa la replica primaria.
2. Determinazione se la perdita del quorum è permanente o meno
 - Nella maggior parte dei casi gli errori sono temporanei. I processi, i nodi e le VM vengono riavviati e le partizioni di rete vengono corrette. In alcuni casi invece gli errori sono permanenti. 
    - Per i servizi senza stato persistente, un errore di uno o più quorum di repliche determina _immediatamente_ una perdita del quorum permanente. Quando Service Fabric rileva la perdita del quorum in un servizio non persistente con stato, passa immediatamente alla fase 3 dichiarando una (potenziale) perdita di dati. La dichiarazione di una perdita di dati è motivata dal fatto che Service Fabric sa che non ha senso attendere che le repliche tornino disponibili, perché sarebbero comunque vuote.
    - Per i servizi permanenti con stato, un errore di uno o più quorum di repliche fa sì che Service Fabric inizi ad attendere che le repliche tornino disponibili e il quorum venga ripristinato. Si verifica di conseguenza un'interruzione del servizio per tutte le _operazioni di scrittura_ alla partizione interessata (o "set di repliche") del servizio. Le operazioni di lettura restano invece ancora possibili con una minore garanzia di coerenza. La quantità di tempo predefinita in cui Service Fabric attende il ripristino del quorum è infinita, poiché il passaggio successivo è un evento di perdita di dati (potenziale) e comporta altri rischi. È possibile ignorare il valore predefinito di `QuorumLossWaitDuration` ma non è consigliato. In questa fase tutti gli sforzi dovrebbero invece essere rivolti a ripristinare le repliche interrotte. Questa operazione comporta il ripristino dei nodi interrotti e la garanzia che possano montare nuovamente le unità in cui hanno archiviato lo stato persistente locale. Se la perdita del quorum è causata da un errore di processo, Service Fabric tenta automaticamente di ricreare i processi e di riavviare le repliche in essi contenute. Se l'operazione non riesce, Service Fabric segnala errori di integrità. Se questi errori possono essere risolti, le repliche tornano in genere disponibili. In alcuni casi invece non possono tornare disponibili, ad esempio perché tutte le unità hanno generato errori o perché i computer sono rimasti in qualche modo fisicamente danneggiati. In questi casi ci si trova in presenza di un evento di perdita del quorum permanente. Per indicare a Service Fabric di smettere di attendere che le repliche tornino disponibili, un amministratore di cluster deve determinare quali partizioni di quali servizi sono interessate e chiamare l'API `Repair-ServiceFabricPartition -PartitionId` o ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)`.  Questa API consente di specificare l'ID della partizione da spostare fuori dalla perdita del quorum e inserirla nella perdita di dati potenziale.

> [!NOTE]
> Non è _mai_ sicuro usare questa API diversamente dal modo per cui è stata studiata quando si ha a che fare con partizioni specifiche. 
>

3. Determinazione se vi è stata una effettiva perdita di dati e ripristino da backup
  - Quando Service Fabric chiama il metodo `OnDataLossAsync`, è sempre a causa di una _sospetta_ perdita di dati. Service Fabric assicura che la chiamata venga inviata alla _migliore_ replica rimanente, vale a dire quella che ha fatto i maggiori progressi. Il motivo per cui si parla di _sospetta_ perdita di dati è che è possibile che la replica rimanente abbia in realtà lo stesso stato della replica primaria quando si è interrotta l'esecuzione. Senza quello stato con cui fare il confronto, tuttavia, non esiste un modo utile che dica a Service Fabric o agli operatori l'effettivo stato. A questo punto Service Fabric sa che anche le altre repliche non torneranno disponibili. Si arriva così alla decisione di smettere di attendere che la perdita del quorum si risolva da sola. La migliore linea di azione per il servizio è in genere bloccare la situazione e attendere un intervento specifico da parte dell'amministratore. Cosa fa quindi una tipica implementazione del metodo `OnDataLossAsync`?
  - Registra prima di tutto che è stato generato un evento `OnDataLossAsync` e genera tutti i necessari avvisi amministrativi.
   - A questo punto di solito sospende l'esecuzione e attende ulteriori decisioni e interventi manuali. Questo perché, anche se sono disponibili backup, può essere necessario prepararli. Se, ad esempio, due servizi diversi coordinano le informazioni, i backup potrebbero dover essere modificati per garantire che dopo il ripristino le informazioni di cui si occupano i due servizi siano coerenti. 
  - Sono spesso presenti anche alcuni dati di telemetria e di scarico dal servizio. Questi metadati possono essere contenuti in altri servizi o in registri. Queste informazioni possono essere usate per determinare se sono state ricevute ed elaborate chiamate nella replica primaria che non erano presenti nel backup o replicate in questa replica specifica. Queste chiamate devono essere riprodotte o aggiunte al backup prima che sia fattibile il ripristino.  
   - Confronta lo stato della replica rimanente con quello contenuto in tutti i backup disponibili. Se si usano le raccolte affidabili di Service Fabric, sono disponibili strumenti e processi per questa operazione, descritti in [questo articolo](service-fabric-reliable-services-backup-restore.md). L'obiettivo consiste nel vedere se lo stato all'interno della replica è sufficiente o anche cosa potrebbe mancare nel backup.
  - Dopo che è stato eseguito il confronto ed è stato completato l'eventuale ripristino necessario, il codice del servizio dovrebbe restituire true se sono state apportate modifiche allo stato. Se la replica ha determinato che era la migliore copia disponibile dello stato e che non state apportate modifiche, restituisce false. True indica che qualsiasi _altra_ replica rimanente potrebbe ora essere incoerente con questa. Le repliche rimanenti verranno eliminate e ricreate da questa replica. False indica che non sono state apportate modifiche allo stato, pertanto le altre repliche possono conservare le informazioni che includono. 

È estremamente importante che gli autori del servizio applichino gli scenari di errore e di potenziale perdita di dati prima che i servizi vengano distribuiti nell'ambiente di produzione. Per evitare la possibilità di perdere i dati, è importante [eseguire periodicamente il backup dello stato](service-fabric-reliable-services-backup-restore.md) di tutti i servizi con stato in un archivio con ridondanza geografica. È inoltre necessario assicurarsi di poterlo ripristinare. Poiché i backup di numerosi servizi diversi vengono eseguiti in momenti diversi, è necessario assicurarsi che dopo un ripristino i servizi abbiano una visione coerente tra loro. Si consideri, ad esempio, una situazione in cui un servizio genera un numero e lo archivia, quindi lo invia a un altro servizio che lo archivia a sua volta. Dopo un ripristino, potrebbe accadere che il secondo servizio abbia conservato il numero mentre il primo servizio non lo conservi più perché il relativo backup non includeva tale operazione.

Se si scopre che le repliche rimanenti non sono sufficienti per continuare in uno scenario di perdita di dati e non è possibile ricostruire lo stato del servizio dai dati di telemetria o di scarico, la frequenza dei backup determina il migliore obiettivo del punto di ripristino (RPO) possibile. Service Fabric include molti strumenti per testare vari scenari di errore, tra cui il quorum permanente e la perdita di dati che richiede il ripristino da un backup. Questi scenari sono inclusi nell'ambito degli strumenti di testabilità di Service Fabric, gestiti dal servizio di analisi degli errori. Altre informazioni su questi strumenti e criteri sono disponibili [qui](service-fabric-testability-overview.md). 

> [!NOTE]
> Anche i servizi di sistema possono subire la perdita del quorum, con un impatto commisurato al servizio in questione. La perdita del quorum nel servizio di denominazione, ad esempio, incide sulla risoluzione dei nomi, mentre la perdita del quorum nel servizio di gestione failover blocca la creazione e i failover di nuovi servizi. Mentre i servizi di sistema di Service Fabric seguono lo stesso criterio dei servizi per la gestione dello stato, non è consigliabile tentare di spostarli dalla perdita del quorum alla potenziale perdita di dati. È invece consigliabile [cercare supporto](service-fabric-support.md) per determinare una soluzione che sia mirata alla situazione specifica.  È di solito preferibile attendere semplicemente che le repliche tornino disponibili.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilità del cluster di Service Fabric
In linea generale, il cluster di Service Fabric è un ambiente altamente distribuito senza singoli punti di errore. Un errore di un qualsiasi nodo non comporta problemi di disponibilità o di affidabilità per il cluster, principalmente perché i servizi di sistema di Service Fabric seguono le stesse linee guida indicate in precedenza: vengono sempre eseguiti con tre o più repliche per impostazione predefinita e i servizi di sistema che sono senza stato vengono eseguiti in tutti i nodi. I livelli di rete e di rilevamento degli errori di Service Fabric sono completamente distribuiti. La maggior parte dei servizi di sistema può essere ricreata dai metadati del cluster o sa come risincronizzare il proprio stato da altre posizioni. La disponibilità del cluster può venire compromessa se i servizi di sistema incorrono in situazioni di perdita del quorum come quelle descritte sopra. In questi casi potrebbe non essere possibile eseguire determinate operazioni in un cluster, come avviare un aggiornamento o distribuire nuovi servizi, ma il cluster resterebbe attivo. I servizi attivi già in esecuzione rimarranno in esecuzione in queste condizioni a meno che non richiedano operazioni di scrittura per i servizi di sistema per continuare a funzionare. Se ad esempio Gestione failover è incluso nella perdita del quorum, tutti i servizi continueranno l'esecuzione, ma quelli che genereranno un errore non saranno in grado di riavviarsi automaticamente, poiché il riavvio prevede il coinvolgimento del servizio Gestione failover. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Errori di un data center o un'area di Azure
In casi rari è possibile che un data center fisico diventi temporaneamente non disponibile a causa di un'interruzione dell'alimentazione o della perdita della connettività di rete. In questi casi i cluster e i servizi di Service Fabric presenti nel data center o nell'area di Azure non saranno disponibili. _I dati saranno tuttavia preservati_. Per i cluster in esecuzione in Azure, è possibile visualizzare gli aggiornamenti sulle interruzioni del servizio nella [pagina Stato di Azure][azure-status-dashboard]. Nell'improbabile caso in cui un data center fisico venga danneggiato parzialmente o completamente, tutti i cluster di Service Fabric in esso ospitati o i servizi presenti nei cluster potrebbero andare persi. Sono inclusi tutti gli stati di cui non è stato eseguito il backup al di fuori del data center o dell'area.

Esistono due diverse strategie per superare un errore permanente o prolungato di un singolo data center o di un'area. 

1. Eseguire cluster di Service Fabric distinti in più aree e usare un qualche sistema per il failover e il failback tra questi ambienti. Questo tipo di modello multi-cluster attivo-attivo o attivo-passivo richiede codice di gestione e operativo aggiuntivo. Richiede anche il coordinamento di backup dai servizi in un unico data center o un'unica area in modo che siano disponibili in altri data center o altre aree quando uno di loro genera un errore. 
2. Eseguire un singolo cluster di Service Fabric che si estende su più data center o più aree. La configurazione minima supportata per questo modello è tre data center o aree. Il numero consigliato di aree o data center è cinque. Questo modello richiede una topologia di cluster più complessa. Il vantaggio che offre, tuttavia, è che l'errore di un data center o di un'area viene convertito da emergenza in errore normale. Questi errori possono essere gestiti dai meccanismi usati per i cluster in una singola area. I domini di errore, i domini di aggiornamento e le regole di posizionamento di Service Fabric assicurano che i carichi di lavoro vengano distribuiti in modo da essere in grado di tollerare gli errori normali. Per altre informazioni sui criteri che aiutano a far funzionare i servizi in questo tipo di cluster, leggere l'articolo relativo ai [criteri di posizionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-leading-to-cluster-failures"></a>Errori casuali che generano errori del cluster
In Service Fabric è presente il concetto di nodi di inizializzazione. Si tratta di nodi che gestiscono la disponibilità del cluster sottostante. Questi nodi aiutano a garantire che il cluster rimanga attivo stabilendo lease con altri nodi e servendo da tiebreaker durante determinati tipi di errori di rete. Se si verificano errori casuali che rimuovono la maggior parte dei nodi di inizializzazione nel cluster e i nodi non vengono ripristinati, il cluster si arresta automaticamente. In Azure i nodi di inizializzazione vengono gestiti automaticamente: vengono distribuiti nei domini di errore e di aggiornamento disponibili e, se dal cluster viene rimosso un singolo nodo di inizializzazione, ne verrà creato un altro al suo posto. 

Sia nei cluster di Service Fabric autonomi che in Azure il "tipo di nodo primario" è quello che esegue il seeding. Quando si definisce un tipo di nodo primario, Service Fabric sfrutta automaticamente il numero di nodi forniti creando fino a 9 nodi di inizializzazione e 9 repliche di ognuno dei servizi di sistema. Se un set di errori casuali interrompe l'esecuzione contemporaneamente della maggior parte delle repliche dei servizi di sistema, i servizi del sistema saranno coinvolti nella perdita del quorum, come descritto sopra. Se la maggior parte dei nodi di inizializzazione viene persa, il cluster si arresta subito dopo.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come simulare diversi errori con il [framework di verificabilità](service-fabric-testability-overview.md)
- Lettura di altre risorse sul ripristino di emergenza e sulla disponibilità elevata. Microsoft ha pubblicato molti documenti su questi argomenti. Sebbene alcuni di questi documenti si riferiscano a tecniche specifiche da applicare in altri prodotti, contengono anche molte procedure consigliate generali valide anche per Service Fabric:
  - [Elenco di controllo della disponibilità](../best-practices-availability-checklist.md)
  - [Esercitazione nel ripristino di emergenza](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure][dr-ha-guide]
- Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png

