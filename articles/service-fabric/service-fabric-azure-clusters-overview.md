---
title: Creare cluster di Azure Service Fabric su Windows Server e Linux | Documentazione Microsoft
description: I cluster di Service Fabric vengono eseguiti in Windows Server e Linux, per poter distribuire e ospitare le applicazioni di Service Fabric in qualsiasi ambiente che esegue Windows Server o Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: a968b173357bf8bcb83990b891f38306895b4ca8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966572"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Panoramica dei cluster di Service Fabric in Azure
Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una VM che fa parte di un cluster è chiamato nodo del cluster. I cluster possono essere ridimensionati fino a migliaia di nodi. Se si aggiungono nuovi nodi al cluster, Service Fabric ribilancia le repliche e le istanze di partizione del servizio nel numero incrementato di nodi. Le prestazioni complessive dell'applicazione migliorano e la contesa per l'accesso alla memoria si riduce. Se i nodi del cluster non vengono usati in modo efficiente, è possibile ridurre il numero di nodi del cluster. Service Fabric ribilancia di nuovo le repliche e le istanze di partizione nel numero ridotto di nodi per usare al meglio l'hardware in ogni nodo.

Un tipo di nodo definisce le dimensioni, il numero e le proprietà di un set di nodi (macchine virtuali) nel cluster. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. I tipi di nodo vengono usati per definire i ruoli relativi a un set di nodi del cluster, ad esempio "front-end" o "back-end". Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario deve essere costituito da almeno cinque macchine virtuali per i cluster di produzione (o da almeno tre macchine virtuali per i cluster di test). I [servizi di sistema Service Fabric](service-fabric-technical-overview.md#system-services) vengono inseriti nei nodi del tipo di nodo primario. 

## <a name="cluster-components-and-resources"></a>Componenti e risorse dei cluster
Un cluster di Service Fabric in Azure è una risorsa di Azure che usa e interagisce con altre risorse di Azure:
* macchine virtuali e schede di rete virtuale
* set di scalabilità di macchine virtuali
* reti virtuali
* servizi di bilanciamento del carico
* account di archiviazione
* indirizzi IP pubblici

![Cluster di Service Fabric][Image]

### <a name="virtual-machine"></a>Macchina virtuale
Una [macchina virtuale](/azure/virtual-machines/) che fa parte di un cluster è detta nodo anche se, tecnicamente, un nodo del cluster è un processo di runtime di Service Fabric. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le [proprietà di posizionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). In ogni computer o macchina virtuale è disponibile un servizio di avvio automatico, *FabricHost.exe*, che viene eseguito all'avvio e che a sua volta avvia due eseguibili: *Fabric.exe* e *FabricGateway.exe*, che costituiscono il nodo. Una distribuzione di produzione è un nodo per computer fisico o macchina virtuale. Negli scenari di test è possibile ospitare più nodi in un singolo computer o una singola macchina virtuale eseguendo più istanze di *Fabric.exe* e *FabricGateway.exe*.

Ogni macchina virtuale è associata a una scheda di interfaccia di rete virtuale e a ogni scheda di interfaccia di rete è assegnato un indirizzo IP privato.  Una macchina virtuale viene assegnata a una rete virtuale e a un servizio di bilanciamento del carico locale mediante la scheda di interfaccia di rete.

Tutte le macchine virtuali in un cluster vengono collocate in una rete virtuale.  Tutti i nodi dello stesso tipo di nodo/set di scalabilità vengono collocati nella stessa subnet nella rete virtuale.  Questi nodi hanno solo indirizzi IP privati e non sono direttamente raggiungibili all'esterno della rete virtuale.  I client possono accedere ai servizi nei nodi tramite Azure Load Balancer.

### <a name="scale-setnode-type"></a>Set di scalabilità/tipo di nodo
Quando si crea un cluster, occorre definire uno o più tipi di nodo.  I nodi, o macchine virtuali, in un tipo di nodo hanno le stesse dimensioni e caratteristiche, come il numero di CPU, la quantità di memoria, il numero di dischi e l'I/O su disco.  Ad esempio, un tipo di nodo potrebbe essere riservato alle macchine virtuali front-end di piccole dimensioni con porte aperte a Internet, mentre un altro tipo di nodo potrebbe essere dedicato alle macchine virtuali back-end di grandi dimensioni che elaborano i dati. Nei cluster di Azure ogni tipo di nodo è mappato a un [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/).

I set di scalabilità possono essere usati per distribuire e gestire una raccolta di macchine virtuali come un set. Ogni tipo di nodo definito in un cluster di Azure Service Fabric configura un set di scalabilità separato. Viene eseguito il bootstrap del runtime di Service Fabric in ogni macchina virtuale del set di scalabilità usando le estensioni di macchina virtuale di Azure. È possibile aumentare o ridurre in modo indipendente ogni nodo, cambiare lo SKU del sistema operativo in esecuzione in ogni nodo del cluster, avere diversi set di porte aperte e usare metriche per la capacità diverse. Un set di scalabilità ha cinque [domini di aggiornamento](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) e cinque [domini di errore](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) e può contenere fino a 100 macchine virtuali.  Per creare cluster con più di 100 nodi occorre creare più set di scalabilità/tipi di nodo.

> [!IMPORTANT]
> La scelta del numero di tipi di nodo per il cluster e delle proprietà di ogni tipo di nodo (dimensioni, primario, con connessione Internet, numero di VM e così via) è un'attività importante.  Per altre informazioni, vedere [Considerazioni sulla pianificazione della capacità del cluster Service Fabric](service-fabric-cluster-capacity.md).

Per altre informazioni, vedere [Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Le istanze di macchine virtuali vengono aggiunte dietro un servizio [Azure Load Balancer](/azure/load-balancer/load-balancer-overview), che è associato a un [indirizzo IP pubblico](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) e a un'etichetta DNS.  Quando si effettua il provisioning di un cluster con *&lt;clustername&gt;*, il nome DNS, *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com*, è l'etichetta DNS associata al servizio di bilanciamento del carico davanti al set di scalabilità.

Le macchine virtuali in un cluster hanno solo [indirizzi IP privati](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Il traffico di gestione e il traffico dei servizi vengono instradati attraverso il servizio di bilanciamento del carico pubblico.  Il traffico di rete viene instradato a queste macchine tramite regole NAT (i client si connettono a specifici nodi/istanze) o regole di bilanciamento del carico (il traffico viene indirizzato alle macchine virtuali tramite round robin).  A un servizio di bilanciamento del carico è associato un indirizzo IP pubblico con un nome DNS nel formato *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com*.  Un IP pubblico è un'altra risorsa di Azure nel gruppo di risorse.  Se si definiscono più tipi di nodo in un cluster, viene creato un servizio di bilanciamento del carico per ogni tipo di nodo/set di scalabilità. In alternativa è possibile configurare un singolo servizio di bilanciamento del carico per più tipi di nodo.  Il tipo di nodo principale ha l'etichetta DNS *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com*, mentre gli altri tipi di nodo hanno l'etichetta DNS *&lt;clustername&gt;-&lt;nodetype&gt;.&lt;location&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Account di archiviazione
Ogni tipo di nodo del cluster è supportato da un [account di archiviazione di Azure](/azure/storage/common/storage-introduction) e da dischi gestiti.

## <a name="cluster-security"></a>Sicurezza del cluster
Un cluster di Service Fabric è una risorsa di cui si è proprietari.  È responsabilità dell'utente proteggere i cluster per evitare che utenti non autorizzati si connettano a essi. Un cluster sicuro è particolarmente importante quando si eseguono carichi di lavoro nel cluster. 

### <a name="node-to-node-security"></a>Sicurezza da nodo a nodo
La sicurezza da nodo a nodo protegge la comunicazione tra le macchine virtuali o i computer di un cluster. Questo scenario di sicurezza assicura che solo i computer autorizzati a connettersi al cluster possano partecipare all'hosting di applicazioni e servizi nel cluster. Service Fabric usa certificati X.509 per proteggere un cluster e fornire le funzionalità di sicurezza dell'applicazione.  Un certificato cluster è necessario per proteggere il traffico del cluster e fornire l'autenticazione per il cluster e il server.  I certificati autofirmati possono essere usati per i cluster di test, ma per proteggere i cluster di produzione occorre usare un certificato di un'Autorità di certificazione attendibile.

Per altre informazioni, vedere [Sicurezza da nodo a nodo](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Sicurezza da client a nodo
La sicurezza da client a nodo autentica i client e aiuta a proteggere la comunicazione tra un client e i singoli nodi del cluster. Questo tipo di sicurezza aiuta a garantire che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite nel cluster. I client vengono identificati in modo univoco tramite le credenziali di sicurezza del relativo certificato X.509. È possibile usare un numero qualsiasi di certificati client facoltativi per autenticare i client di amministrazione o utente con il cluster.

Oltre ai certificati client, è possibile configurare Azure Active Directory anche per autenticare i client con il cluster.

Per altre informazioni, vedere [Sicurezza da client a nodo](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Il controllo degli accessi in base al ruolo consente di assegnare controlli di accesso dettagliati sulle risorse di Azure.  È possibile assegnare regole di accesso diverse a sottoscrizioni, gruppi di risorse e risorse.  Le regole di controllo degli accessi in base al ruolo vengono ereditate lungo la gerarchia delle risorse, a meno che non ne venga eseguito l'override a un livello inferiore.  È possibile assegnare regole di controllo degli accessi in base al ruolo a qualsiasi utente o gruppo utenti in Azure Active Directory, per consentire agli utenti e ai gruppi designati di modificare il cluster.  Per altre informazioni, vedere [Che cos'è il controllo degli accessi in base al ruolo?](/azure/role-based-access-control/overview)

Service Fabric supporta inoltre il controllo di accesso per limitare l'accesso a determinate operazioni di cluster per gruppi di utenti diversi. In questo modo il cluster è più sicuro. Per i client che si connettono a un cluster sono supportati due tipi di controllo di accesso diversi: il ruolo di amministratore e il ruolo utente.  

Per altre informazioni, vedere [Controllo degli accessi in base al ruolo](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete 
I gruppi di sicurezza di rete (NSG) controllano il traffico in ingresso e in uscita di una subnet, una macchina virtuale o una scheda di interfaccia di rete specifica.  Per impostazione predefinita, quando più macchine virtuali si trovano nella stessa rete virtuale, possono comunicare tra loro attraverso qualsiasi porta.  Se si vogliono limitare le comunicazioni tra le macchine virtuali, è possibile definire gruppi di sicurezza di rete per segmentare la rete o isolare le macchine virtuali l'una dall'altra.  Se un cluster contiene più tipi di nodo, è possibile applicare gruppi di sicurezza di rete alle subnet per impedire alle macchine virtuali che appartengono a tipi di nodo diversi di comunicare tra loro.  

Per altre informazioni, vedere [Gruppi di sicurezza](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Ridimensionamento

Le richieste delle applicazioni cambiano nel tempo. Potrebbe essere necessario aumentare le risorse del cluster per supportare l'aumento del carico di lavoro delle applicazioni o del traffico di rete oppure ridurre le risorse del cluster quando le richieste si riducono. Dopo aver creato un cluster di Service Fabric, è possibile scalare il cluster in orizzontale (modificare il numero di nodi) o in verticale (modificare le risorse dei nodi). È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster. Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

Per altre informazioni, vedere [Ridimensionamento di cluster di Azure Service Fabric](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Aggiornamento
Un cluster di Azure Service Fabric è una risorsa di proprietà dell'utente parzialmente gestita da Microsoft. Microsoft è responsabile dell'applicazione delle patch al sistema operativo sottostante e dell'implementazione degli aggiornamenti del runtime di Service Fabric sul cluster. È possibile configurare il cluster per ricevere gli aggiornamenti automatici del runtime quando Microsoft rilascia una nuova versione oppure selezionare la versione supportata del runtime che si vuole. Oltre agli aggiornamenti del runtime, è possibile aggiornare anche la configurazione del cluster, ad esempio i certificati o le porte delle applicazioni.

Per altre informazioni, vedere [Aggiornamento di un cluster di Azure Service Fabric](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
È possibile creare cluster in macchine virtuali che eseguono questi sistemi operativi:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (supporto anteprima)

> [!NOTE]
> Se si decide di distribuire Service Fabric in Windows Server 1709, si noti che (1) non si tratta di una distribuzione LTSB (Long Term Servicing Branch) e quindi potrebbe essere necessario passare ad altre versioni in futuro, e (2) se la distribuzione include contenitori, i contenitori basati su Windows Server 2016 non funzionano in Windows Server 1709 e viceversa (sarà necessario ricompilarli per distribuirli).
>


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [protezione](service-fabric-cluster-security.md), il [ridimensionamento](service-fabric-cluster-scaling.md) e l'[aggiornamento](service-fabric-cluster-upgrade.md) dei cluster di Azure.

Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG