---
title: Domande frequenti su Microsoft Azure Service Fabric | Microsoft Docs
description: Domande frequenti su Service Fabric e relative risposte
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: a9b7490fd51a2a39e6438856041fb25110ddde69
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="commonly-asked-service-fabric-questions"></a>Domande frequenti su Service Fabric

Esistono molte domande frequenti sulle caratteristiche e sulle modalità di uso di Service Fabric. In questo documento vengono illustrate molte di queste domande comuni e le relative risposte.

## <a name="cluster-setup-and-management"></a>Configurazione e gestione del cluster

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>È possibile creare un cluster che si estenda a più aree di Azure o ai miei data center?

Sì. 

La tecnologia di clustering principale di Service Fabric può essere usata per unire macchine in esecuzione in tutto il mondo, purché dispongano di connettività di rete l'una con l'altra. La compilazione e l'esecuzione di questo tipo di cluster possono essere tuttavia complicate.

Se si è interessati a questo scenario, invitiamo a contattarci tramite [Service Fabric Github Issues List](https://github.com/azure/service-fabric-issues) o tramite il supporto tecnico per ottenere altro materiale sussidiario. Il team di Service Fabric sta lavorando per fornire ulteriori informazioni, materiale sussidiario e consigli per questo scenario. 

Alcuni aspetti da considerare: 

1. La risorsa cluster di Service Fabric in Azure è ora divisa in aree, così come i set di scalabilità di macchine virtuali su cui viene creato il cluster. Ciò significa che in caso di malfunzionamento di un'area si potrebbe perdere la possibilità di gestire il cluster tramite Azure Resource Manager o il Portale di Azure. Questa situazione può verificarsi anche se il cluster resta in esecuzione ed è possibile interagire direttamente con esso. Azure inoltre attualmente non offre la possibilità di disporre di una singola rete virtuale che può essere usata tra le aree. Ciò significa che un cluster con più aree in Azure richiede [indirizzi IP pubblici per ogni macchina virtuale nei set di scalabilità di macchine virtuali di Microsoft Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) o [gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Queste opzioni di rete hanno effetti diversi sui costi, sulle prestazioni e su alcuni livelli di progettazione di applicazioni. È richiesta un'attenta analisi e una pianificazione prima di affrontare un ambiente di questo tipo.
2. La manutenzione, gestione e monitoraggio di tali computer possono diventare complicati, soprattutto quando estesi su _tipi_ di ambienti, ad esempio tra provider di cloud diversi o tra risorse locali e Azure. È necessario determinare accuratamente gli aggiornamenti, il monitoraggio, la gestione e la diagnostica per il cluster e le applicazioni prima di eseguire i carichi di lavoro di produzione in tale ambiente. Se si ha già grande esperienza di risoluzione di questi problemi in Azure o nei propri Data center, è possibile che si possano applicare queste stesse soluzioni durante la compilazione o l'esecuzione del cluster di Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>I nodi di Service Fabric ricevono automaticamente aggiornamenti del sistema operativo?

Non ancora, ma si tratta di una richiesta comune che Azure intende implementare in futuro.

Nel frattempo, abbiamo [fornito un'applicazione](service-fabric-patch-orchestration-application.md) attraverso la quale i sistemi operativi sottostanti ai nodi di Service Fabric ricevono patch e sono aggiornati.

Il problema con gli aggiornamenti del sistema operativo è che solitamente richiedono un riavvio della macchina, causando così una perdita temporanea di disponibilità. Di per sé non sarebbe un problema, poiché Service Fabric reindirizzerà automaticamente verso altri nodi il traffico di tali servizi. Tuttavia, se gli aggiornamenti del sistema operativo non vengono coordinati all'interno del cluster, esiste il rischio di interruzione simultanea per parecchi nodi. Tali riavvii simultanei possono causare la perdita totale di disponibilità per un servizio o, perlomeno, per una partizione specifica (per un servizio con stato).

In futuro Microsoft intende supportare un criterio di aggiornamento del sistema operativo completamente automatico e coordinato tra i vari domini di aggiornamento, per garantire la disponibilità nonostante i riavvii e altri guasti o errori imprevisti.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>È possibile usare set di scalabilità di macchine virtuali di grandi dimensioni nel cluster di Service Fabric? 

**Risposta breve**: no. 

**Risposta lunga**: anche se con i set di scalabilità di macchine virtuali di grandi dimensioni è possibile arrivare fino a 1000 istanze di VM, per farlo è necessario usare i gruppi di posizionamento. I domini di errore e i domini di aggiornamento sono coerenti solo in un gruppo di posizionamento. Service Fabric usa i domini di errore e i domini di aggiornamento per prendere decisioni relative al posizionamento delle repliche del servizio/istanze del servizio. Poiché i domini di errore e i domini di aggiornamento sono confrontabili solo in un gruppo di posizionamento, Service Fabric non può usarli. Se, ad esempio, VM1 nel gruppo di posizionamento 1 ha una topologia di dominio di errore 0 e VM9 nel gruppo di posizionamento 2 ha una topologia di domini di errore 4, non significa che VM1 e VM2 siano in due diversi rack hardware, quindi Service Fabric in questo caso non può usare i valori dei domini di errore per prendere decisioni relative al posizionamento.

I set di scalabilità di macchine virtuali di grandi dimensioni attualmente presentano altri problemi, ad esempio la mancanza di supporto per il bilanciamento del carico di livello 4. Per altre informazioni, vedere i [dettagli sui set di scalabilità di grandi dimensioni](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Qual è la dimensione minima di un cluster di Service Fabric? Perché non può essere di dimensioni minori?

La dimensione minima supportata per un cluster di Service Fabric che esegue carichi di lavoro di produzione è di cinque nodi. Per scenari di sviluppo e test, sono supportati cluster a tre nodi.

Queste dimensioni minime esistono poiché il cluster di Service Fabric esegue un set di servizi di sistema con stato, inclusi il servizio di denominazione e la gestione del failover. Questi servizi, che tengono traccia di quali sono stati distribuiti nel cluster e dove sono attualmente ospitati, dipendono da una coerenza elevata. Tale coerenza elevata, a sua volta, dipende dalla possibilità di acquisire un *quorum* per qualsiasi aggiornamento specifico per lo stato di tali servizi, dove per quorum si intende una stretta maggioranza delle repliche (N/2 + 1) per un determinato servizio.

Detto questo, esaminiamo alcune possibili configurazioni di cluster:

**Un nodo**: questa opzione non offre una disponibilità elevata, in quanto perdere quel singolo nodo per qualsivoglia motivo significa perdere l'intero cluster.

**Due nodi**: un quorum per un servizio distribuito tra due nodi (N = 2) è 2 (2/2 + 1 = 2). Se viene persa una singola replica, è impossibile creare un quorum. Dato che per aggiornare un servizio è necessario portare temporaneamente offline una replica, questa non è una configurazione utile.

**Tre nodi**: con tre nodi (N = 3), è comunque necessario creare un quorum di duo nodi (3/2 + 1 = 2). Ciò significa che è possibile perdere un singolo nodo e mantenere comunque il quorum.

La configurazione del cluster a tre nodi è supportata per gli scenari di sviluppo e test perché è possibile eseguire aggiornamenti in sicurezza e i guasti ai nodi individuali non rappresentano un rischio elevato, purché che non si verifichino contemporaneamente. Per i carichi di lavoro di produzione, è necessario disporre di una maggiore resilienza a tali guasti simultanei, pertanto sono richiesti cinque nodi.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>È possibile disattivare il cluster di notte o nei fine settimana per ridurre i costi?

Generalmente, no. Service Fabric archivia lo stato su dischi locali e temporanei, vale a dire che se la macchina virtuale viene spostata in un host diverso, i dati non vengono spostati con essa. In condizioni normali, ciò non rappresenta un problema, in quanto il nuovo nodo viene aggiornato dagli altri nodi. Tuttavia, se si arrestano tutti i nodi per riavviarli in un secondo momento, è molto probabile che la maggior parte di essi verrà avviata su nuovi host, rendendo impossibile il ripristino dle sistema.

Se si desidera creare cluster per testare l'applicazione prima di distribuirla, è consigliabile crearli in maniera dinamica come parte della [pipeline integrazione costante/distribuzione costante](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Come aggiorno il sistema operativo? Ad esempio come passo da Windows Server 2012 a Windows Server 2016?

Mentre Microsoft sviluppa un'esperienza migliorata oggi l'utente è responsabile dell'aggiornamento. È necessario aggiornare l'immagine del sistema operativo nelle macchine virtuali del cluster per una macchina virtuale per volta. 

## <a name="container-support"></a>Supporto dei contenitori

### <a name="why-are-my-containers-that-are-deployed-to-sf-unable-to-resolve-dns-addresses"></a>Perché i contenitori distribuiti in Service Fabric non riescono a risolvere gli indirizzi DNS?

Il problema è stato segnalato nei cluster versione 5.6.204.9494 

**Mitigazione**: seguire [questo documento](service-fabric-dnsservice.md) per abilitare il servizio DNS di Service Fabric nel cluster.

**Correzione**: eseguire l'aggiornamento a una versione del cluster supportata successiva a 5.6.204.9494, se disponibile. Se il cluster è impostato per gli aggiornamenti automatici, verrà aggiornato automaticamente alla versione in cui questo problema è stato risolto.

  
## <a name="application-design"></a>Progettazione di applicazioni

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Qual è il modo migliore per eseguire query sui dati in partizioni di una raccolta Reliable Collections?

Le raccolte Reliable Collections sono in genere [partizionate](service-fabric-concepts-partitioning.md) per abilitare l'aumento delle istanze per maggiori prestazioni e velocità effettiva. Ciò significa che lo stato di un determinato servizio può essere distribuito in decine o centinaia di macchine. Per eseguire operazioni su quel set di dati completo, sono disponibili varie opzioni:

- Creare un servizio che esegua una query di tutte le partizioni di un altro servizio per estrarre i dati richiesti.
- Creare un servizio che possa ricevere dati da tutte le partizioni di un altro servizio.
- Inviare periodicamente dati da ogni servizio in un archivio esterno. Questo approccio è appropriato solamente se si eseguono query che non fanno parte della logica di business principale.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Qual è il modo migliore per eseguire query sui dati nei vari attori?

Gli attori sono progettati per essere unità indipendenti di stato e calcolo, pertanto è consigliabile non eseguire query generali dello stato degli attori in fase di esecuzione. Se è necessario eseguire query nel set completo di stati degli attori, è consigliabile piuttosto:

- Sostituire i servizi di attori con servizi Reliable con stato, come il numero delle richieste di rete per raccogliere tutti i dati, dal numero di attori al numero di partizioni nel servizio.
- Progettare gli attori per l'invio periodico del relativo stato a un'archiviazione esterna per facilitare le query. Come in precedenza, questo approccio è ideale solo se le query in esecuzione non sono richieste per il comportamento di runtime.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quanti dai è possibile archiviare in una raccolta Reliable Collections?

In genere i servizi Reliable sono partizionati. Pertanto, la quantità di dati archiviabili è limitata solo dal numero di macchine presenti nel cluster e dalla quantità di memoria disponibile su tali macchine.

Ad esempio, si supponga di avere una raccolta Reliable Collections in un servizio con 100 partizioni e 3 replica, con archiviati oggetti di dimensioni pari a 1 KB di media. Si supponga quindi di disporre di un cluster di 10 macchine con 16 GB di memoria per macchina. Per ragioni di semplicità e cautela, si supponga che il sistema operativo, i servizi di sistema, il runtime di Service Fabric e i servizi consumino 6 GB di quella memoria, lasciandone così 10 disponibili per ogni macchina o 100 per il cluster.

Tenendo presente che ciascun oggetto deve essere archiviato tre volte (una primaria e due repliche), si avrà una memoria sufficiente per circa 35 milioni di oggetti nella raccolta, con funzionamento a piena capacità. Tuttavia, si consiglia di essere preparati in caso di perdita simultanea di un dominio di errore e di un dominio di aggiornamento, che rappresentano circa 1/3 di capacità e ne ridurrebbero così il numero a circa 23 milioni.

Si noti che questo calcolo presuppone inoltre:

- Che la distribuzione dei dati nelle partizioni sia approssimativamente uniforme o che si indichino le metriche di caricamento a Cluster Resource Manager. Per impostazione predefinita, Service Fabric bilancia il carico in base al numero di repliche. Nell'esempio precedente, vengono inserite 10 repliche primarie e 20 repliche secondarie su ciascun nodo nel cluster. Quanto descritto in precedenza è ideale per carichi distribuiti in modo uniforme tra le partizioni. Se i carichi non sono uniformi, è necessario segnalarli in modo che Resource Manager possa raggruppare più repliche di piccole dimensioni e consentire alle repliche di dimensioni maggiori di utilizzare più memoria su un singolo nodo.

- Che il servizio Reliable in questione sia l'unico ad archiviare stato nel cluster. Poiché è possibile distribuire più servizi in un cluster, è necessario tenere conto delle risorse di esecuzione e gestione dello stato richieste da ciascun servizio.

- Che il cluster stesso non si stia ampliando o riducendo. Se si aggiungono più macchine, Service Fabric bilancerà nuovamente le repliche per sfruttare la capacità aggiuntiva fino a quando il numero di macchine sarà superiore al numero di partizioni nel servizio, poiché una replica singola non può estendersi alle macchine. Al contrario, se si riduce la dimensione del cluster rimuovendo le macchine, le repliche saranno raggruppate più strettamente e avranno una minore capacità totale.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quanti dati è possibile archiviare in un attore?

Come per i servizi Reliable, la quantità di dati archiviabile in un servizio attore è limitata solo dallo spazio totale su disco e della memoria disponibile tra i nodi del cluster. Tuttavia, i singoli attori sono più efficaci quando vengono usati per racchiudere una piccola quantità di logica di business di stato e associata. Come regola generale, un singolo attore dovrebbe avere uno stato misurabile in kilobyte.

## <a name="other-questions"></a>Altre domande

### <a name="how-does-service-fabric-relate-to-containers"></a>In che modo Service Fabric è correlato ai contenitori?

I contenitori offrono un modo semplice per racchiudere i servizi e le relative dipendenze in modo da risultare eseguibili con coerenza in tutti gli ambiente e usabili in modo isolato su una singola macchina. Service Fabric offre un modo per distribuire e gestire servizi, tra cui [quelli inclusi in un contenitore](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Si prevede di rendere disponibile Service Fabric in open source?

Microsoft prevede di rendere disponibili in open source i framework di Reliable Services e Reliable Actors in GitHub e accetterà i contributi della community per questi progetti. Seguire il [blog di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) per le informazioni più recenti.

Non si prevede attualmente di rendere disponibile in open source il runtime di Service Fabric.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui concetti chiave e sulle procedure consigliate di Service Fabric](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
