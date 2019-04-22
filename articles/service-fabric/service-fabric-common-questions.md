---
title: Domande frequenti su Microsoft Azure Service Fabric | Microsoft Docs
description: Domande frequenti su Service Fabric e relative risposte
services: service-fabric
documentationcenter: .net
author: chackdan
manager: chackdan
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: 0bd8a7d403ad1fe0f7abb15356cc9c90ed6b3f02
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359415"
---
# <a name="commonly-asked-service-fabric-questions"></a>Domande frequenti su Service Fabric

Esistono molte domande frequenti sulle caratteristiche e sulle modalità di uso di Service Fabric. In questo documento vengono illustrate molte di queste domande comuni e le relative risposte.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Configurazione e gestione del cluster

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Come si esegue il roll back del certificato del cluster di Service Fabric?

Il rollback di un aggiornamento dell'applicazione richiede il rilevamento degli errori di integrità prima che il quorum del cluster di Service Fabric esegua il commit della modifica; solo a questo punto sarà possibile eseguire il roll forward delle modifiche. Potrebbe essere necessario fare ripristinare il cluster da un tecnico dell'escalation tramite il servizio clienti se è stata introdotta una modifica di interruzione del certificato non monitorata.  [Aggiornamento dell'applicazione di Service Fabric](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) applica i [Parametri di aggiornamento di un'applicazione](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master) e promette un aggiornamento con tempi di inattività pari a zero.  Dopo l'aggiornamento monitorato dell'applicazione consigliato, l'avanzamento automatico tramite domini di aggiornamento è basato sul superamento di controlli di integrità, eseguendo il rollback automaticamente se l'aggiornamento di un servizio predefinito non riesce.
 
Se il cluster sfrutta ancora la proprietà di identificazione personale del certificato classica nel modello di gestione risorse, si consiglia di [modificare il cluster dall'identificazione personale del certificato a un nome comune](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn), per sfruttare le moderne funzionalità di gestione dei segreti.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>È possibile creare un cluster che si estenda a più aree di Azure o ai miei data center?

Sì. 

La tecnologia di clustering principale di Service Fabric può essere usata per unire macchine in esecuzione in tutto il mondo, purché dispongano di connettività di rete l'una con l'altra. La compilazione e l'esecuzione di questo tipo di cluster possono essere tuttavia complicate.

Se si è interessati a questo scenario, è consigliabile contattare Microsoft tramite la pagina di [elenco di problemi di Service Fabric su GitHub](https://github.com/azure/service-fabric-issues) oppure tramite il rappresentante del supporto tecnico per ottenere informazioni aggiuntive. Il team di Service Fabric sta lavorando per fornire ulteriori informazioni, materiale sussidiario e consigli per questo scenario. 

Alcuni aspetti da considerare: 

1. La risorsa cluster di Service Fabric in Azure è ora divisa in aree, così come i set di scalabilità di macchine virtuali su cui viene creato il cluster. Ciò significa che in caso di malfunzionamento di un'area si potrebbe perdere la possibilità di gestire il cluster tramite Azure Resource Manager o il portale di Azure. Questa situazione può verificarsi anche se il cluster resta in esecuzione ed è possibile interagire direttamente con esso. Azure inoltre attualmente non offre la possibilità di disporre di una singola rete virtuale che può essere usata tra le aree. Ciò significa che un cluster con più aree in Azure richiede [indirizzi IP pubblici per ogni macchina virtuale nei set di scalabilità di macchine virtuali di Microsoft Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) o [gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Queste opzioni di rete hanno effetti diversi sui costi, sulle prestazioni e su alcuni livelli di progettazione di applicazioni. È richiesta un'attenta analisi e una pianificazione prima di affrontare un ambiente di questo tipo.
2. La manutenzione, gestione e monitoraggio di tali computer possono diventare complicati, soprattutto quando estesi su _tipi_ di ambienti, ad esempio tra provider di cloud diversi o tra risorse locali e Azure. È necessario determinare accuratamente gli aggiornamenti, il monitoraggio, la gestione e la diagnostica per il cluster e le applicazioni prima di eseguire i carichi di lavoro di produzione in tale ambiente. Se si ha già esperienza di risoluzione di questi problemi in Azure o nei propri data center, è possibile che si possano applicare queste stesse soluzioni durante la compilazione o l'esecuzione del cluster di Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>I nodi di Service Fabric ricevono automaticamente aggiornamenti del sistema operativo?

È possibile usare [Virtual Machine Scale Set Automatic OS Image Update](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) (Aggiornamento automatico dell'immagine del sistema operativo con i set di scalabilità di macchine virtuali di Azure) oggi disponibile a livello generale.

Per i cluster NON eseguiti in Azure è stata [fornita un'applicazione](service-fabric-patch-orchestration-application.md) attraverso la quale i sistemi operativi sottostanti ai nodi di Service Fabric ricevono patch.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>È possibile usare set di scalabilità di macchine virtuali di grandi dimensioni nel cluster di Service Fabric? 

**Risposta breve**: no. 

**Risposta lunga**: anche se con i set di scalabilità di macchine virtuali di grandi dimensioni è possibile arrivare fino a 1000 istanze di VM, per farlo è necessario usare i gruppi di posizionamento. I domini di errore e i domini di aggiornamento sono coerenti solo in un gruppo di posizionamento. Service Fabric usa i domini di errore e i domini di aggiornamento per prendere decisioni relative al posizionamento delle repliche del servizio/istanze del servizio. Poiché i domini di errore e i domini di aggiornamento sono confrontabili solo in un gruppo di posizionamento, Service Fabric non può usarli. Se, ad esempio, VM1 nel gruppo di posizionamento 1 ha una topologia di domini di errore 0 e VM9 nel gruppo di posizionamento 2 ha una topologia di domini di errore 4, non significa che VM1 e VM2 siano in due diversi rack hardware, quindi Service Fabric in questo caso non può usare i valori dei domini di errore per prendere decisioni relative al posizionamento.

I set di scalabilità di macchine virtuali di grandi dimensioni attualmente presentano altri problemi, ad esempio la mancanza di supporto per il bilanciamento del carico di livello 4. Per altre informazioni, vedere i [dettagli sui set di scalabilità di grandi dimensioni](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Qual è la dimensione minima di un cluster di Service Fabric? Perché non può essere di dimensioni minori?

La dimensione minima supportata per un cluster di Service Fabric che esegue carichi di lavoro di produzione è di cinque nodi. Per gli scenari di sviluppo, sono supportati cluster a un nodo (ottimizzati per un'esperienza di sviluppo rapido in Visual Studio) e cluster a cinque nodi.

Un cluster di produzione deve avere almeno cinque nodi per i tre motivi seguenti:
1. Anche quando nessun servizio utente è in esecuzione, un cluster di Service Fabric esegue un set di servizi di sistema con stato, inclusi il servizio di denominazione e il servizio di gestione del failover. Questi servizi di sistema sono essenziali per garantire l'operatività del cluster.
2. Viene sempre configurata una sola replica di un servizio per ogni nodo, in modo che la dimensione del cluster costituisca il limite massimo per il numero di repliche che può avere un servizio, ovvero una partizione.
3. Poiché l'aggiornamento di un cluster comporta l'arresto di almeno un nodo, è necessario avere almeno un nodo di riserva. Un cluster di produzione deve quindi avere almeno due nodi *in aggiunta* al numero minimo. Il numero minimo è definito dalla dimensione del quorum di un servizio di sistema, come illustrato di seguito.  

L'obiettivo è che il cluster sia disponibile in caso di errore simultaneo di due nodi. Affinché un cluster di Service Fabric sia disponibile, è necessario che lo siano anche i servizi di sistema. I servizi di sistema con stato, ad esempio il servizio di denominazione e il servizio di gestione del failover, che tengono traccia dei servizi distribuiti nel cluster e della posizione in cui sono attualmente ospitati, dipendono da una coerenza elevata. Tale coerenza elevata, a sua volta, dipende dalla possibilità di acquisire un *quorum* per qualsiasi aggiornamento specifico per lo stato di tali servizi, dove per quorum si intende una stretta maggioranza delle repliche (N/2 + 1) per un determinato servizio. Pertanto, per garantire resilienza in caso di perdita simultanea di due nodi, ovvero perdita simultanea di due repliche di un servizio di sistema, è necessario avere ClusterSize - QuorumSize >= 2, il che richiede una dimensione minima di cinque nodi. Per una dimostrazione di questo concetto, tenere presente che il cluster ha N nodi e che sono presenti N repliche di un servizio di sistema: una in ogni nodo. La dimensione del quorum per un servizio di sistema è (N/2 + 1). La disuguaglianza precedente sarà simile a N - (N/2 + 1) >= 2. È necessario considerare due casi: quando N è pari e quando N è dispari. Se N è pari, ad esempio N = 2\*m, dove m >= 1, la disuguaglianza sarà 2\*m - (2\*m/2 + 1) >= 2 o m >= 3. Il valore minimo per N è 6, che si ottiene quando m = 3. Se invece N è dispari, ovvero N = 2\*m + 1, dove m >= 1, la disuguaglianza sarà 2\*m + 1 - ((2\*m + 1)/2 + 1) >= 2 o 2\*m + 1 - (m + 1) >= 2 o m >= 2. Il valore minimo per N è 5, che si ottiene quando m = 2. Pertanto, fra tutti i valori di N che soddisfano la disuguaglianza ClusterSize - QuorumSize >= 2, il valore minimo è 5.

Si noti che nel ragionamento precedente si è presupposto che ogni nodo abbia una replica di un servizio di sistema e pertanto la dimensione del quorum viene calcolata in base al numero di nodi nel cluster. Se tuttavia si modifica *TargetReplicaSetSize*, è possibile rendere la dimensione del quorum minore di (N/2+1). Sembrerebbe quindi possibile avere un cluster inferiore a 5 nodi e avere comunque 2 nodi in più rispetto alla dimensione del quorum. In un cluster a 4 nodi, ad esempio, se si imposta TargetReplicaSetSize su 3, la dimensione del quorum in base a TargetReplicaSetSize è (3/2 + 1) o 2 e pertanto la disuguaglianza è CluserSize - QuorumSize = 4-2 > = 2. Non è tuttavia possibile garantire che il servizio di sistema abbia una dimensione uguale o superiore al quorum se si perde una coppia di nodi nello stesso momento. Se ad esempio i due nodi persi ospitavano due repliche, il servizio di sistema subirà una perdita a livello di quorum (poiché rimarrà una sola replica) e non risulterà più disponibile.

Detto questo, esaminiamo alcune possibili configurazioni di cluster:

**Un nodo**: questa opzione non offre una disponibilità elevata, in quanto perdere quel singolo nodo per qualsivoglia motivo significa perdere l'intero cluster.

**Due nodi**: un quorum per un servizio distribuito tra due nodi (N = 2) è 2 (2/2 + 1 = 2). Se viene persa una singola replica, è impossibile creare un quorum. Dato che per aggiornare un servizio è necessario portare temporaneamente offline una replica, questa non è una configurazione utile.

**Tre nodi**: con tre nodi (N = 3), è comunque necessario creare un quorum di duo nodi (3/2 + 1 = 2). È pertanto possibile perdere un singolo nodo e mantenere comunque il quorum, ma un errore simultaneo di due nodi determinerà la perdita di quorum dei servizi di sistema e renderà il cluster non disponibile.

**Quattro nodi**: con quattro nodi (N = 4), è necessario creare un quorum di tre nodi (4/2 + 1 = 3). È pertanto possibile perdere un singolo nodo e mantenere comunque il quorum, ma un errore simultaneo di due nodi determinerà la perdita di quorum dei servizi di sistema e renderà il cluster non disponibile.

**Cinque nodi**: con cinque nodi (N = 5), è comunque necessario creare un quorum di tre nodi (5/2 + 1 = 3). Ciò significa che è possibile perdere due nodi nello stesso momento e mantenere comunque il quorum per i servizi di sistema.

Per i carichi di lavoro di produzione, è necessario garantire resilienza in caso di errori simultanei di almeno due nodi (ad esempio, un errore per un aggiornamento del cluster e uno per altri motivi) e sono pertanto necessari cinque nodi.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>È possibile disattivare il cluster di notte o nei fine settimana per ridurre i costi?

Generalmente, no. Service Fabric archivia lo stato su dischi locali e temporanei, vale a dire che se la macchina virtuale viene spostata in un host diverso, i dati non vengono spostati con essa. In condizioni normali, ciò non rappresenta un problema, in quanto il nuovo nodo viene aggiornato dagli altri nodi. Tuttavia, se si arrestano tutti i nodi per riavviarli in un secondo momento, è molto probabile che la maggior parte di essi verrà avviata su nuovi host, rendendo impossibile il ripristino dle sistema.

Se si desidera creare cluster per testare l'applicazione prima di distribuirla, è consigliabile crearli in maniera dinamica come parte della [pipeline integrazione costante/distribuzione costante](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Come aggiorno il sistema operativo? Ad esempio come passo da Windows Server 2012 a Windows Server 2016?

Mentre Microsoft sviluppa un'esperienza migliorata oggi l'utente è responsabile dell'aggiornamento. È necessario aggiornare l'immagine del sistema operativo nelle macchine virtuali del cluster per una macchina virtuale per volta. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>È possibile crittografare i dischi dati collegati in un tipo di nodo del cluster (set di scalabilità di macchine virtuali)?
Sì.  Per altre informazioni, vedere l'articolo [Creare un cluster di Service Fabric con dischi dati collegati](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) e gli articoli relativi a come [crittografare i dischi (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) e [crittografare i dischi (CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>È possibile usare macchine virtuali con priorità bassa in un tipo di nodo del cluster (set di scalabilità di macchine virtuali)?
No. Le macchine virtuali con priorità bassa non sono supportate. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Quali sono le directory e i processi da escludere quando si esegue un programma antivirus nel cluster?

| **Directory escluse dall'antivirus** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (dalla configurazione del cluster) |
| FabricLogRoot (dalla configurazione del cluster) |

| **Processi esclusi dall'antivirus** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>In che modo è possibile autenticare l'applicazione con Key Vault per ottenere i segreti?
Di seguito sono riportati i mezzi che permettono all'applicazione di ottenere le credenziali per l'autenticazione a Key Vault:

R. Durante il processo di compilazione/compressione delle applicazioni, è possibile estrarre un certificato nel pacchetto di dati della tua app di Service Fabric e utilizzare questa opzione per l'autenticazione in Key Vault.
B. Per gli host di identità del servizio gestito abilitata del set di scalabilità di macchine virtuali, è possibile sviluppare un semplice PowerShell SetupEntryPoint per l'app di Service Fabric per ottenere [un token di accesso dall'endpoint MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)e quindi [recuperare i segreti da Key Vault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Progettazione di applicazioni

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Qual è il modo migliore per eseguire query sui dati in partizioni di una raccolta Reliable Collections?

Le raccolte Reliable Collections sono in genere [partizionate](service-fabric-concepts-partitioning.md) per abilitare l'aumento delle istanze per maggiori prestazioni e velocità effettiva. Ciò significa che lo stato di un determinato servizio può essere distribuito in decine o centinaia di macchine. Per eseguire operazioni su quel set di dati completo, sono disponibili varie opzioni:

- Creare un servizio che esegua una query di tutte le partizioni di un altro servizio per estrarre i dati richiesti.
- Creare un servizio che possa ricevere dati da tutte le partizioni di un altro servizio.
- Inviare periodicamente dati da ogni servizio in un archivio esterno. Questo approccio è appropriato solamente se si eseguono query che non fanno parte della logica di business principale.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Qual è il modo migliore per eseguire query sui dati nei vari attori?

Gli attori sono progettati per essere unità indipendenti di stato e calcolo, pertanto è consigliabile non eseguire query generali dello stato degli attori in fase di esecuzione. Se è necessario eseguire query nel set completo di stati degli attori, è consigliabile piuttosto:

- Sostituire i servizi di attori con servizi Reliable con stato, in modo che il numero delle richieste di rete raccolga tutti i dati dal numero di attori al numero di partizioni nel servizio.
- Progettare gli attori per l'invio periodico del relativo stato a un'archiviazione esterna per facilitare le query. Come in precedenza, questo approccio è ideale solo se le query in esecuzione non sono richieste per il comportamento di runtime.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quanti dai è possibile archiviare in una raccolta Reliable Collections?

In genere i servizi Reliable sono partizionati. Pertanto, la quantità di dati archiviabili è limitata solo dal numero di macchine presenti nel cluster e dalla quantità di memoria disponibile su tali macchine.

Ad esempio, si supponga di avere una raccolta Reliable Collections in un servizio con 100 partizioni e 3 repliche, con archiviati oggetti di dimensioni pari a 1 KB di media. Si supponga quindi di disporre di un cluster di 10 macchine con 16 GB di memoria per macchina. Per ragioni di semplicità e cautela, si supponga che il sistema operativo, i servizi di sistema, il runtime di Service Fabric e i servizi consumino 6 GB di quella memoria, lasciando così 10 GB disponibili per ogni macchina o 100 GB per il cluster.

Tenendo presente che ciascun oggetto deve essere archiviato tre volte (una primaria e due repliche), si avrà una memoria sufficiente per circa 35 milioni di oggetti nella raccolta, con funzionamento a piena capacità. Tuttavia, si consiglia di essere preparati in caso di perdita simultanea di un dominio di errore e di un dominio di aggiornamento, che rappresentano circa 1/3 di capacità e ne ridurrebbero così il numero a circa 23 milioni.

Si noti che questo calcolo presuppone inoltre:

- Che la distribuzione dei dati nelle partizioni sia approssimativamente uniforme o che si indichino le metriche di caricamento a Cluster Resource Manager. Per impostazione predefinita, Service Fabric bilancia il carico in base al numero di repliche. Nell'esempio precedente, vengono inserite 10 repliche primarie e 20 repliche secondarie su ciascun nodo nel cluster. Quanto descritto in precedenza è ideale per carichi distribuiti in modo uniforme tra le partizioni. Se i carichi non sono uniformi, è necessario segnalarli in modo che Resource Manager possa raggruppare più repliche di piccole dimensioni e consentire alle repliche di dimensioni maggiori di utilizzare più memoria su un singolo nodo.

- Che il servizio Reliable in questione sia l'unico ad archiviare stato nel cluster. Poiché è possibile distribuire più servizi in un cluster, è necessario tenere conto delle risorse di esecuzione e gestione dello stato richieste da ciascun servizio.

- Che il cluster stesso non si stia ampliando o riducendo. Se si aggiungono più macchine, Service Fabric bilancerà nuovamente le repliche per sfruttare la capacità aggiuntiva fino a quando il numero di macchine sarà superiore al numero di partizioni nel servizio, poiché una replica singola non può estendersi alle macchine. Al contrario, se si riduce la dimensione del cluster rimuovendo le macchine, le repliche sono raggruppate più strettamente e hanno una minore capacità totale.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quanti dati è possibile archiviare in un attore?

Come per i servizi Reliable, la quantità di dati archiviabile in un servizio attore è limitata solo dallo spazio totale su disco e della memoria disponibile tra i nodi del cluster. Tuttavia, i singoli attori sono più efficaci quando vengono usati per racchiudere una piccola quantità di logica di business di stato e associata. Come regola generale, un singolo attore dovrebbe avere uno stato misurabile in kilobyte.

## <a name="other-questions"></a>Altre domande

### <a name="how-does-service-fabric-relate-to-containers"></a>In che modo Service Fabric è correlato ai contenitori?

I contenitori offrono un modo semplice per racchiudere i servizi e le relative dipendenze in modo da risultare eseguibili con coerenza in tutti gli ambiente e usabili in modo isolato su una singola macchina. Service Fabric offre un modo per distribuire e gestire servizi, tra cui [quelli inclusi in un contenitore](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Si prevede di rendere disponibile Service Fabric in open source?

Sono disponibili in open source parti di Service Fabric ([framework Reliable Services](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [framework Reliable Actors](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [librerie di integrazione di ASP.NET Core](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer) e [interfaccia della riga di comando di Service Fabric](https://github.com/Azure/service-fabric-cli)) su GitHub e si accettano i contributi della community per questi progetti. 

Microsoft ha [recentemente annunciato](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) l'intenzione di rendere disponibile in open source il runtime di Service Fabric. Attualmente il [repository di Service Fabric](https://github.com/Microsoft/service-fabric/) è disponibile su GitHub con la build di Linux e gli strumenti di test, che consentono di clonare il repository, compilare Service Fabric per Linux, eseguire test di base, segnalare problemi e inviare richieste pull. Stiamo lavorando anche per completare la migrazione dell'ambiente di compilazione di Windows, oltre a ottenere un ambiente CI completo.

Seguire il [blog di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) per le informazioni più recenti.

## <a name="next-steps"></a>Passaggi successivi

Scopri [principali concetti di Service Fabric](service-fabric-technical-overview.md) e [procedure consigliate](service-fabric-best-practices-overview.md) ghiaccio concepts](service-fabric-technical-overview.md) Fabric e [procedure consigliate](service-fabric-best-practices-overview.md)
