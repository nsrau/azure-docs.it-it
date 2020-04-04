---
title: Disponibilità elevata e ripristino di emergenza nel servizio Azure Kubernetes (AKS)High availability and disaster recovery in Azure Kubernetes Service (AKS)
description: Informazioni sulle procedure consigliate di un operatore del cluster per ottenere il massimo tempo di attività per le applicazioni, garantendo un'elevata disponibilità e la preparazione per il ripristino di emergenza nel servizio Kubernetes di Azure.
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 6aff60cbc4a4cab557e6e202ea1181d5b20bbd20
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655879"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la continuità aziendale e il ripristino di emergenza nel servizio Azure Kubernetes

Il tempo di attività delle applicazioni assume particolare importanza per la gestione dei cluster nel servizio Azure Kubernetes. Per impostazione predefinita, AKS offre disponibilità elevata utilizzando più nodi in un set di scalabilità di macchine [virtuali (VMSS).](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) Ma questi nodi multipli non proteggono il sistema da un errore di area. Per ottimizzare i tempi di attività, pianificare in anticipo per mantenere la continuità aziendale e prepararsi per il ripristino di emergenza.

Questo articolo è incentrato su come pianificare la continuità aziendale e il ripristino di emergenza in AKS. Si apprenderà come:

> [!div class="checklist"]
> * Pianificare i cluster AKS in più aree.
> * Instradare il traffico tra più cluster usando Gestione traffico di Azure.Route traffic across multiple clusters by using Azure Traffic Manager.
> * Utilizzare la replica geografica per i registri delle immagini del contenitore.
> * Pianificare lo stato dell'applicazione in più cluster.
> * Replicare l'archiviazione in più aree.

## <a name="plan-for-multiregion-deployment"></a>Pianificare la distribuzione in più areePlan for multiregion deployment

**Procedura consigliata:** quando si distribuiscono più cluster AKS, scegliere le aree in cui AKS è disponibile e usare aree abbinate.

Un cluster del servizio Azure Kubernetes viene distribuito in una singola area. Per proteggere il sistema da un errore di area, distribuire l'applicazione in più cluster AKS in aree diverse. Quando si pianifica la posizione in cui distribuire il cluster AKS, considerare quanto segue:

* [**Disponibilità area AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Scegliere le aree vicine agli utenti. AKS si espande continuamente in nuove regioni.
* [**Aree associate ad Azure:**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)per l'area geografica, scegliere due aree abbinate tra loro. Le aree associate coordinano gli aggiornamenti della piattaforma e assegnano priorità alle attività di ripristino ove necessario.
* **Disponibilità del servizio**: Decidere se le aree abbinate devono essere calde/calde, calde o calde/fredde. Si desidera eseguire entrambe le aree contemporaneamente, con un'area *pronta* per iniziare a gestire il traffico? O vuoi che una regione abbia il tempo di prepararti a servire il traffico?

La disponibilità dell'area AKS e le aree abbinate sono una considerazione congiunta. Distribuire i cluster del servizio Azure Kubernetes nelle aree abbinate progettate per gestire congiuntamente il ripristino di emergenza nell'area. Ad esempio, il servizio Azure Kubernetes è disponibile negli Stati Uniti orientali e negli Stati Uniti occidentali. Queste aree sono accoppiate. Scegliere queste due aree quando si crea una strategia AKS BC/DR.

Quando si distribuisce l'applicazione, aggiungere un altro passaggio alla pipeline CI/CD per la distribuzione in questi più cluster AKS. Se non si aggiornano le pipeline di distribuzione, le applicazioni potrebbero essere distribuite in una sola delle aree e dei cluster AKS. Il traffico dei clienti indirizzato a un'area secondaria non riceverà gli aggiornamenti del codice più recenti.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usare Gestione traffico di Microsoft Azure per indirizzare il traffico

**Procedura consigliata:** Gestione traffico di Azure può indirizzare i clienti all'istanza dell'applicazione e del cluster AKS più vicini. Per ottenere prestazioni e ridondanza ottimali, indirizzare tutto il traffico delle applicazioni tramite Gestione traffico prima che venga indirizzato al cluster AKS.

Se si dispone di più cluster AKS in aree diverse, usare Gestione traffico per controllare il flusso del traffico verso le applicazioni in esecuzione in ogni cluster. [Gestione traffico di Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/) è un servizio di bilanciamento del carico del traffico basato su DNS in grado di distribuire il traffico di rete tra le aree. Usare Gestione traffico per instradare gli utenti in base al tempo di risposta del cluster o in base all'area geografica.

![AKS con Gestione traffico](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

I clienti che dispongono di un singolo cluster AKS in genere si connettono all'indirizzo IP del servizio o al nome DNS di una determinata applicazione. In una distribuzione multicluster, i clienti devono connettersi a un nome DNS di Gestione traffico che punta ai servizi in ogni cluster AKS. Definire questi servizi usando gli endpoint di Gestione traffico. Ogni endpoint è l'IP del servizio di *bilanciamento del carico*del servizio. Usare questa configurazione per indirizzare il traffico di rete dall'endpoint di Gestione traffico in un'area all'endpoint in un'area diversa.

![Routing geografico tramite Gestione traffico](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Gestione traffico esegue ricerche DNS e restituisce l'endpoint più appropriato di un utente. I profili nidificati possono assegnare una priorità a una posizione primaria. Ad esempio, gli utenti dovrebbero in genere connettersi all'area geografica più vicina. Se l'area presenta un problema, Gestione traffico indirizza invece gli utenti a un'area secondaria. Questo approccio garantisce che i clienti possano connettersi a un'istanza dell'applicazione anche se l'area geografica più vicina non è disponibile.

Per informazioni su come configurare gli endpoint e il routing, vedere Configurare il metodo di [routing del traffico geografico tramite Gestione traffico.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Routing delle applicazioni di livello 7 con Azure Front Door Service

Gestione traffico usa DNS (livello 3) per modellare il traffico. [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) offre un'opzione di routing HTTP/HTTPS (livello 7). Le funzionalità aggiuntive di Azure Front Door Service includono la terminazione TLS, il dominio personalizzato, il firewall dell'applicazione Web, la riscrittura url e l'affinità di sessione. Esaminare le esigenze di traffico dell'applicazione per determinare la soluzione più adatta.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Interconnettere le aree con il peering globale della rete virtuale

Se i cluster devono comunicare tra loro, è possibile connettere tra loro entrambe le reti virtuali tramite il peering di [reti virtuali.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Questa tecnologia interconnette le reti virtuali tra loro fornendo un'elevata larghezza di banda nella rete backbone di Microsoft, anche in diverse aree geografiche.

Un prerequisito per peering le reti virtuali in cui sono in esecuzione i cluster AKS consiste nell'utilizzare il servizio di bilanciamento del carico standard nel cluster AKS, in modo che i servizi Kubernetes siano raggiungibili attraverso la rete virtuale peering.

## <a name="enable-geo-replication-for-container-images"></a>Abilitare la replica geografica per le immagini del contenitore

**Procedura consigliata:** archiviare le immagini del contenitore nel Registro di sistema del contenitore di Azure e replicare geograficamente il Registro di sistema in ogni area AKS.

Per distribuire ed eseguire le applicazioni nel servizio Azure Kubernetes, è necessario un modo per archiviare ed eseguire il pull delle immagini del contenitore. Container Registry si integra con AKS, in modo da poter archiviare in modo sicuro le immagini del contenitore o i grafici Helm. Il Registro di sistema del contenitore supporta la replica geografica multimaster per replicare automaticamente le immagini nelle aree di Azure in tutto il mondo. 

Per migliorare le prestazioni e la disponibilità, usare la replica geografica del Registro di sistema del contenitore per creare un Registro di sistema in ogni area in cui si dispone di un cluster AKS. Ogni cluster AKS estrae quindi le immagini del contenitore dal Registro di sistema del contenitore locale nella stessa area:Each AKS cluster then pulls container images from the local container registry in the same region:

![Replica geografica del Registro di sistema del contenitore per le immagini del contenitoreContainer Registry geo-replication for container images](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando si utilizza la replica geografica del Registro di sistema del contenitore per estrarre le immagini dalla stessa area, i risultati sono:When you use Container Registry geo-replication to pull images from the same region, the results are:

* **Più veloce:** si estraggono immagini da connessioni di rete ad alta velocità e a bassa latenza all'interno della stessa area di Azure.Faster : You pull images from high-speed, low-latency network connections within the same Azure region.
* **Più affidabile**: se un'area non è disponibile, il cluster AKS estrae le immagini da un registro contenitori disponibile.
* **Più economico**: Non c'è nessun costo in uscita di rete tra i data center.

La replica geografica è una funzionalità dei registri dei contenitori SKU Premium.Geo-replication is a feature of *Premium* SKU container registries. Per informazioni su come configurare la replica geografica, vedere Replica geografica del [Registro di sistema del contenitore](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Rimuovere lo stato del servizio dai contenitori

**Procedura consigliata:** se possibile, non archiviare lo stato del servizio all'interno del contenitore. Usare invece una piattaforma Azure come servizio (PaaS) che supporta la replica in più aree.

*Lo stato del servizio* si riferisce ai dati in memoria o su disco necessari per il funzionamento di un servizio. Include le strutture di dati e le variabili membro che vengono lette e scritte dal servizio. A seconda dell'architettura del servizio, lo stato potrebbe includere anche file o altre risorse archiviate nel disco. Ad esempio, lo stato potrebbe includere i file utilizzati da un database per archiviare i dati e i log delle transazioni.

Lo stato può essere esternalizzato o collocato con il codice che modifica lo stato. In genere, lo stato di esternalizzazione viene esternalizzato utilizzando un database o un altro archivio dati che viene eseguito su computer diversi in rete o che esaurisce il processo sullo stesso computer.

I contenitori e i microservizi sono più resilienti quando i processi eseguiti al loro interno non mantengono lo stato. Poiché le applicazioni contengono quasi sempre uno stato, usare una soluzione PaaS, ad esempio Database di Azure per MySQL, Database di Azure per PostgreSQL o Database SQL di Azure.Because applications almost always contain some state, use a PaaS solution such as Azure Database for MySQL, Azure Database for PostgreSQL, or Azure SQL Database.

Per creare applicazioni portatili, vedere le linee guida seguenti:To build portable applications, see the following guidelines:

* [La metodologia dell'app a 12 fattori](https://12factor.net/)
* [Eseguire un'applicazione Web in più aree di Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Creare un piano di migrazione di archiviazione

**Procedura consigliata:** se si usa Archiviazione di Azure, preparare e testare come eseguire la migrazione dell'archiviazione dall'area primaria all'area di backup.

Le applicazioni potrebbero usare Archiviazione di Azure per i dati. Poiché le applicazioni sono distribuite su più cluster AKS in aree diverse, è necessario mantenere sincronizzata l'archiviazione. Ecco due modi comuni per replicare l'archiviazione:Here are two common ways to replicate storage:

* Replica asincrona basata sull'infrastruttura
* Replica asincrona basata sull'applicazione

### <a name="infrastructure-based-asynchronous-replication"></a>Replica asincrona basata sull'infrastruttura

Le applicazioni potrebbero richiedere spazio di archiviazione permanente anche dopo l'eliminazione di un contenitore. In Kubernetes è possibile utilizzare volumi persistenti per rendere persistenti l'archiviazione dei dati. I volumi persistenti vengono montati in una macchina virtuale del nodo e quindi esposti ai pod. I volumi persistenti seguono i pod anche se i pod vengono spostati in un nodo diverso all'interno dello stesso cluster.

La strategia di replica utilizzata dipende dalla soluzione di archiviazione. Le soluzioni di storage comuni come [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) forniscono indicazioni personalizzate sul ripristino di emergenza e la replica.

La strategia tipica consiste nel fornire un punto di archiviazione comune in cui le applicazioni possono scrivere i dati. Questi dati vengono poi replicati tra le aree e sono quindi accessibili in locale.

![Replica asincrona basata sull'infrastruttura](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se si usano dischi gestiti di Azure, è possibile scegliere soluzioni di replica e ripristino di emergenza come queste:If you use Azure Managed Disks, you can choose replication and DR solutions such as these:

* [Velero su Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replica asincrona basata sull'applicazione

Kubernetes attualmente non fornisce alcuna implementazione nativa per la replica asincrona basata su applicazioni. Poiché i contenitori e le Kubernetes sono loosely coupled, qualsiasi approccio tradizionale alle applicazioni o al linguaggio dovrebbe funzionare. In genere, le applicazioni stesse replicano le richieste di archiviazione, che vengono quindi scritte nell'archiviazione dei dati sottostante di ogni cluster.

![Replica asincrona basata sull'applicazione](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è incentrato sulla continuità aziendale e considerazioni sul ripristino di emergenza per i cluster AKS. Per altre informazioni sulle operazioni cluster in AKS, vedere questi articoli sulle procedure consigliate:For more information about cluster operations in AKS, see these articles about best practices:

* [Multitenancy e isolamento del cluster][aks-best-practices-cluster-isolation]
* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
