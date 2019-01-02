---
title: Procedure consigliate per l'operatore - Disponibilità elevata e ripristino di emergenza nel servizio Azure Kubernetes
description: Informazioni sulle procedure consigliate che consentono all'operatore del cluster di ottenere il massimo tempo di attività delle applicazioni per offrire disponibilità elevata e prepararsi al ripristino di emergenza nel servizio Azure Kubernetes
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 622cdd36a1ecf582c4cdb883b12753ee2a75d50e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855001"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la continuità aziendale e il ripristino di emergenza nel servizio Azure Kubernetes

Il tempo di attività delle applicazioni assume particolare importanza per la gestione dei cluster nel servizio Azure Kubernetes. Il servizio Azure Kubernetes offre disponibilità elevata usando più nodi in un set di disponibilità. Questi nodi non offrono protezione in caso di errore completo dell'area. Per ottimizzare il tempo di attività, è necessario implementare alcune funzionalità di continuità aziendale e ripristino di emergenza.

Questo articolo sulle procedure consigliate contiene considerazioni utili per pianificare la continuità aziendale e il ripristino di emergenza nel servizio Azure Kubernetes. Si apprenderà come:

> [!div class="checklist"]
* [Eseguire la pianificazione per i cluster del servizio Azure Kubernetes in più aree](#region-planning)
* [Indirizzare il traffico tra più cluster con Gestione traffico di Microsoft Azure](#ingress-traffic)
* [Usare la replica geografica per i registri di immagini del contenitore](#container-registry)
* [Eseguire la pianificazione dello stato dell'applicazione in più cluster](#managing-application-state)
* [Replicare l'archiviazione tra più aree](#storage)

## <a name="plan-for-multi-region-deployment"></a>Eseguire la pianificazione per la distribuzione in più aree

**Indicazioni sulle procedure consigliate**: quando si distribuiscono più cluster del servizio Azure Kubernetes, scegliere le aree in cui tale servizio è disponibile e usare le aree abbinate.

Un cluster del servizio Azure Kubernetes viene distribuito in una singola area. Per proteggersi da errori di area, distribuire l'applicazione in più cluster del servizio Azure Kubernetes in aree diverse. Quando si pianificano le aree in cui distribuire il cluster del servizio Azure Kubernetes, si applicano le considerazioni seguenti:

* [Disponibilità a livello di area del servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * Scegliere le aree vicine ai propri utenti. Il servizio Azure Kubernetes è in continua espansione in nuove aree.
* [Aree abbinate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * Per la propria area geografica, scegliere due aree abbinate tra loro. In queste aree vengono coordinati gli aggiornamenti della piattaforma e gli interventi di ripristino vengono classificati in ordine di priorità, se necessario.
* Livello di disponibilità dei servizi (accesso molto frequente/molto frequente, accesso molto frequente/frequente, accesso molto frequente/sporadico)
  * Determinare se si vogliono eseguire entrambe le aree contemporaneamente, con un'area *pronta* a iniziare a gestire il traffico o un'area che necessita di più tempo per prepararsi alla gestione del traffico.

La disponibilità a livello di area del servizio Azure Kubernetes e le aree abbinate devono essere considerate insieme. Distribuire i cluster del servizio Azure Kubernetes nelle aree abbinate progettate per gestire congiuntamente il ripristino di emergenza nell'area. Ad esempio, il servizio Azure Kubernetes è disponibile negli *Stati Uniti orientali* e negli *Stati Uniti occidentali*. Queste aree sono anche abbinate. È consigliabile usare queste due aree nella creazione di una strategia di continuità aziendale e ripristino di emergenza del servizio Azure Kubernetes.

Quando si distribuisce l'applicazione, è necessario inserire anche un altro passaggio nella pipeline di CI/CD per eseguire la distribuzione a più cluster del servizio Azure Kubernetes. Se non si aggiornano le pipeline di distribuzione, le distribuzioni di applicazioni possono avvenire solo in una delle aree e in uno dei cluster del servizio Azure Kubernetes. Il traffico dei clienti che viene indirizzato a un'area secondaria non riceverà gli aggiornamenti più recenti del codice.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usare Gestione traffico di Microsoft Azure per indirizzare il traffico

**Indicazioni sulle procedure consigliate**: Gestione traffico di Microsoft Azure può indirizzare i clienti ai cluster e all'istanza dell'applicazione del servizio Azure Kubernetes più vicini. Per prestazioni e ridondanza ottimali, indirizzare tutto il traffico dell'applicazione tramite Gestione traffico prima di passare al cluster del servizio Azure Kubernetes.

Con più cluster del servizio Azure Kubernetes in aree diverse, è necessario controllare come il traffico viene indirizzato alle applicazioni eseguite in ogni cluster. [Gestione traffico di Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/) è un servizio di bilanciamento del carico del traffico basato su DNS in grado di distribuire il traffico di rete tra le aree. È possibile indirizzare gli utenti in base al tempo di risposta del cluster o in base all'area geografica.

![Servizio Azure Kubernetes con Gestione traffico di Microsoft Azure](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Con un singolo cluster del servizio Azure Kubernetes, i clienti in genere si connettono all'*IP del servizio* o al nome DNS di una determinata applicazione. In una distribuzione a più cluster, i clienti devono connettersi a un nome DNS di Gestione traffico che punta ai servizi in ogni cluster del servizio Azure Kubernetes. Questi servizi vengono definiti tramite gli endpoint di Gestione traffico. Ogni endpoint è l'*IP del servizio di bilanciamento del carico*. Questa configurazione consente di indirizzare il traffico di rete dall'endpoint di Gestione traffico in un'area all'endpoint in un'altra area.

![Routing geografico con Gestione traffico di Microsoft Azure](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Gestione traffico consente di eseguire le ricerche DNS e restituire l'endpoint più appropriato per un utente. Si possono usare profili annidati; la priorità viene assegnata per una località primaria. Ad esempio, un utente deve connettersi principalmente all'area geografica più vicina. Se tale area presenta un problema, Gestione traffico lo indirizza in un'area secondaria. Con questo approccio i clienti possono sempre connettersi a un'istanza dell'applicazione, anche se l'area geografica più vicina non è disponibile.

Per istruzioni su come configurare questi endpoint e il routing, vedere [Configurare il metodo di routing del traffico geografico tramite Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door"></a>Routing dell'applicazione di livello 7 con Frontdoor di Azure

Gestione traffico di Microsoft Azure usa DNS (livello 3) per gestire il traffico. Il servizio [Frontdoor di Azure (anteprima)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) offre un'opzione di routing HTTP/HTTPS (livello 7). Le funzionalità aggiuntive del servizio Frontdoor includono terminazione SSL, dominio personalizzato, web application firewall, riscrittura URL e affinità di sessione.

Esaminare le esigenze di traffico dell'applicazione per determinare la soluzione più adatta.

## <a name="enable-geo-replication-for-container-images"></a>Abilitare la replica geografica per le immagini del contenitore

**Indicazioni sulle procedure consigliate**: archiviare le immagini del contenitore nel Registro Azure Container ed eseguire la replica geografica del registro in ogni regione del servizio Azure Kubernetes.

Per distribuire ed eseguire le applicazioni nel servizio Azure Kubernetes, è necessario un modo per archiviare ed eseguire il pull delle immagini del contenitore. Il Registro Azure Container può integrarsi con il servizio Azure Kubernetes per archiviare in modo sicuro le immagini del contenitore o i grafici Helm. Il Registro Azure Container supporta la replica geografica multimaster per replicare automaticamente le immagini nelle aree di Azure in tutto il mondo. Per migliorare prestazioni e disponibilità, usare la replica geografica del Registro Azure Container per creare un registro in ogni area in cui è presente un cluster del servizio Azure Kubernetes. Ogni cluster del servizio Azure Kubernetes esegue quindi il pull delle immagini del contenitore dal Registro Azure Container locale nella stessa area:

![Replica geografica del Registro Azure Container per le immagini del contenitore](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Tra i vantaggi dell'uso della replica geografica del Registro Azure Container vi sono i seguenti:

* **Il pull delle immagini dalla stessa area è più veloce.** Il pull delle immagini viene eseguito da connessioni di rete ad alta velocità e a bassa latenza all'interno della stessa area di Azure.
* **Il pull delle immagini dalla stessa area è più affidabile.** Se un'area non è disponibile, il cluster del servizio Azure Kubernetes esegue il pull dell'immagine da un Registro Azure Container diverso che rimane disponibile.
* **Il pull delle immagini dalla stessa area è più economico.** Non vengono applicati addebiti in uscita di rete tra i Data center.

La replica geografica è una funzionalità disponibile solo per le istanze di Registro Azure Container SKU *Premium*. Per istruzioni su come configurare la replica, vedere [Replica geografica nel Registro Azure Container](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>Rimuovere lo stato del servizio dai contenitori

**Indicazioni sulle procedure consigliate**: laddove possibile, non archiviare lo stato del servizio all'interno del contenitore. Usare invece i servizi PaaS di Azure che supportano la replica di tipo multiarea.

Lo stato del servizio si riferisce ai dati in memoria o su disco di cui un servizio necessita per funzionare. Include le strutture di dati e le variabili membro che vengono lette e scritte dal servizio. A seconda di come è progettato il servizio, lo stato può anche includere file o altre risorse archiviati su disco. I file ad esempio che un database userebbe per archiviare log delle transazioni e dati.

Lo stato può essere esternalizzato oppure condividere la posizione con il codice che lo modifica. L'esternalizzazione dello stato viene in genere eseguita usando un database o un altro archivio dati eseguito in altri computer sulla rete o all'esterno del processo nello stesso computer.

Contenitori e microservizi sono più resilienti quando i processi che vengono eseguiti al loro interno non mantengono lo stato. Poiché le applicazioni contengono quasi sempre uno stato, usare una soluzione PaaS, ad esempio Database di Azure per MySQL/Postgres o SQL di Azure.

Per informazioni dettagliate su come compilare applicazioni maggiormente portabili, vedere le linee guida seguenti:

* [The Twelve-Factor App Methodology](https://12factor.net/) (Metodologia dell'app a dodici fattori).
* [Eseguire un'applicazione Web in più aree di Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Creare un piano di migrazione di archiviazione

**Indicazioni sulle procedure consigliate**: se si usa Archiviazione di Azure, preparare e testare la procedura di migrazione dell'archiviazione dall'area primaria all'area di backup.

Le applicazioni possono usare Archiviazione di Azure per i propri dati. Poiché le applicazioni vengono distribuite tra più cluster del servizio Azure Kubernetes in aree diverse, è necessario mantenere sincronizzato lo spazio di archiviazione. Due modi comuni per replicare l'archiviazione includono gli approcci seguenti:

* Replica asincrona basata sull'applicazione
* Replica asincrona basata sull'infrastruttura

### <a name="infrastructure-based-asynchronous-replication"></a>Replica asincrona basata sull'infrastruttura

Le applicazioni possono richiedere un'archiviazione persistente anche dopo l'eliminazione di un pod. In Kubernetes è possibile usare volumi permanenti per rendere persistente l'archiviazione dei dati. Questi volumi permanenti sono montati in una macchina virtuale del nodo e quindi esposti ai pod. I volumi permanenti seguono i pod, anche se questi ultimi vengono spostati su un nodo diverso all'interno dello stesso cluster.

A seconda dell'uso delle soluzioni di archiviazione, le strategie di replica possono essere diverse. Soluzioni di archiviazione comuni come [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/master/disaster-recovery.html) e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) hanno le proprie indicazioni.

L'approccio centrale è un punto di archiviazione comune dove le applicazioni scrivono i propri dati. Questi dati vengono poi replicati tra le aree e sono quindi accessibili in locale.

![Replica asincrona basata sull'infrastruttura](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se si usa Azure Managed Disks, le soluzioni di replica e ripristino di emergenza includono l'uso di uno degli approcci seguenti:

* [Ark in Azure](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replica asincrona basata sull'applicazione

Non esiste attualmente alcuna implementazione nativa di Kubernetes per la replica asincrona basata sull'applicazione. Dato il regime di controllo libero di contenitori e Kubernetes, qualsiasi approccio tradizionale al linguaggio o all'applicazione dovrebbe funzionare. L'approccio centrale prevede che siano le stesse applicazioni a eseguire la replica delle richieste di archiviazione, che vengono quindi scritte nell'archiviazione dati sottostante di ogni cluster.

![Replica asincrona basata sull'applicazione](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato alcune considerazioni in materia di continuità aziendale e ripristino di emergenza nei cluster del servizio Azure Kubernetes. Per altre informazioni sulle operazioni cluster in AKS, vedere le procedure consigliate seguenti:

* [Isolamento cluster e multi-tenant][aks-best-practices-cluster-isolation]
* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
