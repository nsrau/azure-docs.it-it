---
title: Disponibilità elevata e ripristino di emergenza in Azure Kubernetes Service (AKS)
description: Informazioni sulle procedure consigliate per un operatore cluster per ottenere il massimo tempo di attività per le applicazioni, offrendo disponibilità elevata e preparazione per il ripristino di emergenza in Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5fdb189fcab3da4dad52642571ac42e669828fe3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971656"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la continuità aziendale e il ripristino di emergenza nel servizio Azure Kubernetes

Il tempo di attività delle applicazioni assume particolare importanza per la gestione dei cluster nel servizio Azure Kubernetes. Il servizio Azure Kubernetes offre disponibilità elevata usando più nodi in un set di disponibilità. Ma questi più nodi non proteggono il sistema da un errore dell'area. Per ottimizzare il tempo di attività, pianificare in anticipo per mantenere la continuità aziendale e prepararsi per il ripristino di emergenza.

Questo articolo è incentrato su come pianificare la continuità aziendale e il ripristino di emergenza in AKS. Si apprenderà come:

> [!div class="checklist"]
> * Pianificare i cluster AKS in più aree.
> * Instradare il traffico tra più cluster usando Gestione traffico di Azure.
> * Usare la replica geografica per i registri di immagini del contenitore.
> * Pianificare lo stato dell'applicazione in più cluster.
> * Replicare lo spazio di archiviazione tra più aree.

## <a name="plan-for-multiregion-deployment"></a>Pianificare la distribuzione in più aree

**Procedura consigliata**: quando si distribuiscono più cluster AKS, scegliere le aree in cui è disponibile AKS e usare le aree abbinate.

Un cluster del servizio Azure Kubernetes viene distribuito in una singola area. Per proteggere il sistema da un errore dell'area, distribuire l'applicazione in più cluster AKS in aree diverse. Quando si pianifica la posizione in cui distribuire il cluster AKS, prendere in considerazione quanto segue:

* [**Disponibilità area AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): scegliere le aree vicine agli utenti. AKS si espande continuamente in nuove aree geografiche.
* [**Aree abbinate di Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): per l'area geografica, scegliere due aree abbinate tra loro. Le aree abbinate aggiornano gli aggiornamenti della piattaforma e assegnano priorità ai tentativi di ripristino laddove necessario.
* **Disponibilità del servizio**: decidere se le aree abbinate devono essere Hot/Hot, Hot/warm o hot/cold. Si desidera eseguire entrambe le aree contemporaneamente, con un'area *pronta* per iniziare a gestire il traffico? Oppure si vuole che un'area abbia tempo per prepararsi a gestire il traffico?

La disponibilità e le aree abbinate dell'area AKS sono una considerazione congiunta. Distribuire i cluster del servizio Azure Kubernetes nelle aree abbinate progettate per gestire congiuntamente il ripristino di emergenza nell'area. Ad esempio, AKS è disponibile negli Stati Uniti orientali e negli Stati Uniti occidentali. Queste aree sono abbinate. Scegliere queste due aree quando si sta creando una strategia AKS BC/DR.

Quando si distribuisce l'applicazione, aggiungere un altro passaggio alla pipeline CI/CD per eseguire la distribuzione in questi cluster AKS multipli. Se non si aggiornano le pipeline di distribuzione, le applicazioni possono essere distribuite solo in una delle aree e nei cluster AKS. Il traffico del cliente indirizzato a un'area secondaria non riceverà gli ultimi aggiornamenti del codice.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usare Gestione traffico di Microsoft Azure per indirizzare il traffico

**Procedura consigliata**: gestione traffico di Azure può indirizzare i clienti al cluster AKS più vicino e all'istanza dell'applicazione. Per ottenere prestazioni e ridondanza ottimali, indirizzare tutto il traffico dell'applicazione attraverso gestione traffico prima di passare al cluster AKS.

Se si dispone di più cluster AKS in aree diverse, usare gestione traffico per controllare il flusso del traffico verso le applicazioni in esecuzione in ogni cluster. [Gestione traffico di Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/) è un servizio di bilanciamento del carico del traffico basato su DNS in grado di distribuire il traffico di rete tra le aree. Usare gestione traffico per indirizzare gli utenti in base al tempo di risposta del cluster o in base all'area geografica.

![AKS con gestione traffico](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

I clienti che dispongono di un singolo cluster AKS si connettono in genere all'indirizzo IP del servizio o al nome DNS di una determinata applicazione. In una distribuzione multicluster i clienti devono connettersi a un nome DNS di gestione traffico che punta ai servizi in ogni cluster AKS. Definire questi servizi usando gli endpoint di gestione traffico. Ogni endpoint è l' *IP del servizio di bilanciamento del carico del servizio*. Usare questa configurazione per indirizzare il traffico di rete dall'endpoint di gestione traffico in un'area all'endpoint in un'area diversa.

![Routing geografico tramite Gestione traffico](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Gestione traffico esegue ricerche DNS e restituisce l'endpoint più appropriato di un utente. I profili annidati possono assegnare priorità a una posizione primaria. Ad esempio, gli utenti devono in genere connettersi alla relativa area geografica più vicina. Se tale area presenta un problema, gestione traffico indirizza gli utenti a un'area secondaria. Questo approccio assicura che i clienti possano connettersi a un'istanza dell'applicazione anche se l'area geografica più vicina non è disponibile.

Per informazioni su come configurare gli endpoint e il routing, vedere [configurare il metodo di routing del traffico geografico tramite Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Routing delle applicazioni di livello 7 con il servizio front door di Azure

Gestione traffico USA il DNS (livello 3) per definire il traffico. Il [servizio front door di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) fornisce un'opzione di routing http/https (livello 7). Funzionalità aggiuntive del servizio front door di Azure includono la terminazione SSL, il dominio personalizzato, web application firewall, la riscrittura URL e l'affinità di sessione. Esaminare le esigenze di traffico dell'applicazione per determinare la soluzione più adatta.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Aree di interconnessione con peering di rete virtuale globale

Se i cluster devono comunicare tra loro, la connessione di entrambe le reti virtuali può essere eseguita tramite il [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Questa tecnologia interconnette le reti virtuali tra loro garantendo una larghezza di banda elevata attraverso la rete backbone di Microsoft, anche in diverse aree geografiche.

Un prerequisito per eseguire il peering delle reti virtuali in cui sono in esecuzione i cluster AKS consiste nell'usare la Load Balancer standard nel cluster AKS, in modo che i servizi kubernetes siano raggiungibili attraverso il peering di rete virtuale.

## <a name="enable-geo-replication-for-container-images"></a>Abilitare la replica geografica per le immagini del contenitore

**Procedura consigliata**: archiviare le immagini del contenitore in Azure container Registry e replicare geograficamente il registro di sistema in ogni area AKS.

Per distribuire ed eseguire le applicazioni nel servizio Azure Kubernetes, è necessario un modo per archiviare ed eseguire il pull delle immagini del contenitore. Container Registry si integra con AKS, in modo da poter archiviare in modo sicuro le immagini del contenitore o i grafici Helm. Container Registry supporta la replica geografica multimaster per replicare automaticamente le immagini in aree di Azure in tutto il mondo. 

Per migliorare le prestazioni e la disponibilità, usare Container Registry la replica geografica per creare un registro in ogni area in cui è presente un cluster AKS. Ogni cluster AKS estrae quindi le immagini del contenitore dal registro contenitori locale nella stessa area:

![Container Registry la replica geografica per le immagini del contenitore](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando si usa Container Registry la replica geografica per eseguire il pull delle immagini dalla stessa area, i risultati sono:

* **Faster**: è possibile estrarre le immagini da connessioni di rete ad alta velocità e a bassa latenza all'interno della stessa area di Azure.
* **Più affidabile**: se un'area non è disponibile, il cluster AKS estrae le immagini da un registro contenitori disponibile.
* **Più economico**: non viene addebitato alcun costo in uscita di rete tra i Data Center.

La replica geografica è una funzionalità dei registri contenitori SKU *Premium* . Per informazioni su come configurare la replica geografica, vedere [container Registry la replica geografica](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Rimuovere lo stato del servizio dai contenitori

**Procedura consigliata**: laddove possibile, non archiviare lo stato del servizio all'interno del contenitore. Usare invece una piattaforma distribuita come servizio (PaaS) di Azure che supporta la replica in più aree.

*Lo stato del servizio* si riferisce ai dati in memoria o su disco necessari per il funzionamento di un servizio. Include le strutture di dati e le variabili membro che vengono lette e scritte dal servizio. A seconda di come viene progettato il servizio, lo stato potrebbe includere anche file o altre risorse archiviate sul disco. Ad esempio, lo stato potrebbe includere i file utilizzati da un database per archiviare i dati e i log delle transazioni.

Lo stato può essere esternalizzato o colocato con il codice che modifica lo stato. In genere si Externalize lo stato usando un database o un altro archivio dati eseguito in computer diversi in rete o che esaurisce il processo nello stesso computer.

I contenitori e i microservizi sono più resilienti quando i processi che vengono eseguiti al loro interno non mantengono lo stato. Poiché le applicazioni contengono quasi sempre uno stato, usare una soluzione PaaS, ad esempio database di Azure per MySQL, database di Azure per PostgreSQL o database SQL di Azure.

Per compilare applicazioni portabili, vedere le linee guida seguenti:

* [Metodologia dell'app a 12 fattori](https://12factor.net/)
* [Eseguire un'applicazione Web in più aree di Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Creare un piano di migrazione di archiviazione

**Procedura consigliata**: se si usa archiviazione di Azure, preparare e testare come eseguire la migrazione dell'archiviazione dall'area primaria all'area di backup.

Le applicazioni potrebbero usare archiviazione di Azure per i propri dati. Poiché le applicazioni vengono distribuite in più cluster AKS in aree diverse, è necessario sincronizzare lo spazio di archiviazione. Ecco due modi comuni per replicare l'archiviazione:

* Replica asincrona basata sull'infrastruttura
* Replica asincrona basata sull'applicazione

### <a name="infrastructure-based-asynchronous-replication"></a>Replica asincrona basata sull'infrastruttura

È possibile che le applicazioni richiedano una risorsa di archiviazione permanente anche dopo l'eliminazione di un pod. In Kubernetes è possibile usare i volumi permanenti per salvare in modo permanente l'archivio dati. I volumi permanenti vengono montati in una macchina virtuale del nodo e quindi esposti ai pod. I volumi permanenti seguono i pod anche se i pod vengono spostati in un altro nodo all'interno dello stesso cluster.

La strategia di replica usata dipende dalla soluzione di archiviazione. Soluzioni di archiviazione comuni come [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/master/disaster-recovery.html)e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) forniscono indicazioni personalizzate sul ripristino di emergenza e la replica.

La strategia tipica consiste nel fornire un punto di archiviazione comune in cui le applicazioni possono scrivere i propri dati. Questi dati vengono poi replicati tra le aree e sono quindi accessibili in locale.

![Replica asincrona basata sull'infrastruttura](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se si usa Managed Disks di Azure, è possibile scegliere la replica e le soluzioni di ripristino di emergenza, ad esempio:

* [Velero in Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replica asincrona basata sull'applicazione

Kubernetes non fornisce attualmente alcuna implementazione nativa per la replica asincrona basata sull'applicazione. Poiché i contenitori e Kubernetes sono a regime di controllo libero, qualsiasi approccio tradizionale di applicazioni o linguaggi dovrebbe funzionare. In genere, le applicazioni stesse replicano le richieste di archiviazione, che vengono quindi scritte nell'archivio dati sottostante di ogni cluster.

![Replica asincrona basata sull'applicazione](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è incentrato sulle considerazioni sulla continuità aziendale e sul ripristino di emergenza per i cluster AKS. Per altre informazioni sulle operazioni del cluster in AKS, vedere questi articoli sulle procedure consigliate:

* [Multi-tenant e isolamento del cluster][aks-best-practices-cluster-isolation]
* [Funzionalità dell'utilità di pianificazione di Kubernetes di base][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
