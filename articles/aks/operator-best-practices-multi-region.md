---
title: Elevata disponibilità e ripristino di emergenza in Azure Kubernetes Service (AKS)
description: Informazioni su procedure consigliate dell'operatore di un cluster per ottenere il massimo tempo di attività per le applicazioni, garantendo una disponibilità elevata e la preparazione per il ripristino di emergenza in Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 4afc1231e6c9fa49c04c7bf6dfe26ee5eb87cc31
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475160"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la continuità aziendale e il ripristino di emergenza nel servizio Azure Kubernetes

Il tempo di attività delle applicazioni assume particolare importanza per la gestione dei cluster nel servizio Azure Kubernetes. Il servizio Azure Kubernetes offre disponibilità elevata usando più nodi in un set di disponibilità. Ma questi più nodi non proteggere il sistema da un errore di area. Per ottimizzare il tempo di attività, pianificare in anticipo mantenere la continuità aziendale e preparare il ripristino di emergenza.

Questo articolo è incentrato sulla pianificazione per la continuità aziendale e ripristino di emergenza nel servizio contenitore di AZURE. Si apprenderà come:

> [!div class="checklist"]
> * Pianificare cluster servizio contenitore di AZURE in più aree.
> * Instradare il traffico tra più cluster usando Gestione traffico di Azure.
> * Usare la replica geografica per i registri di immagini contenitore.
> * Pianificare lo stato dell'applicazione in più cluster.
> * Eseguire la replica di archiviazione tra più aree.

## <a name="plan-for-multiregion-deployment"></a>Pianificare la distribuzione multiarea

**Procedura consigliata**: Quando si distribuiscono più cluster servizio contenitore di AZURE, scegliere le aree in cui servizio contenitore di AZURE è disponibile e usare le aree abbinate.

Un cluster del servizio Azure Kubernetes viene distribuito in una singola area. Per proteggere il sistema da errori di area, distribuire l'applicazione in più cluster servizio contenitore di AZURE tra aree diverse. Quando si pianifica la posizione in cui distribuire il cluster AKS, prendere in considerazione:

* [**Disponibilità delle aree del servizio contenitore di AZURE**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Scegliere le aree vicine ai propri utenti. Servizio contenitore di AZURE espande continuamente in nuove aree geografiche.
* [**Aree abbinate di Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Per la propria area geografica, scegliere due aree abbinate tra loro. Le aree abbinate coordinare gli aggiornamenti della piattaforma e stabilire le priorità le attività di ripristino dove necessario.
* **Disponibilità del servizio**: Decidere se devono essere di aree abbinate a caldo/hot, hot/warm o a freddo/caldo. Si desidera eseguire entrambe le aree allo stesso tempo, con un'area *pronti* per iniziare a gestire il traffico? Oppure si preferisce un'area a abbiano il tempo di prepararsi gestire il traffico?

Disponibilità delle aree del servizio contenitore di AZURE e le aree abbinate vengono presi in considerazione comune. Distribuire i cluster del servizio Azure Kubernetes nelle aree abbinate progettate per gestire congiuntamente il ripristino di emergenza nell'area. Ad esempio, servizio contenitore di AZURE è disponibile nelle aree Stati Uniti orientali e Stati Uniti occidentali. Queste aree sono abbinate. Scegliere queste due aree quando si crea una strategia di BC AKS e ripristino di emergenza.

Quando si distribuisce l'applicazione, aggiungere un altro passaggio per la pipeline di integrazione continua/recapito Continuo per distribuire questi più cluster AKS. Se non si aggiornano le pipeline di distribuzione, le applicazioni potrebbero essere distribuite in uno solo delle aree e i cluster AKS. Il traffico dei clienti che viene indirizzato a un'area secondaria non riceverà gli aggiornamenti più recenti del codice.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usare Gestione traffico di Microsoft Azure per indirizzare il traffico

**Procedura consigliata**: Gestione traffico di Azure può indirizzare clienti alla propria istanza cluster e dell'applicazione di servizio contenitore di AZURE più vicina. Per prestazioni ottimali e la ridondanza, indirizzare tutto il traffico dell'applicazione tramite Gestione traffico prima dell'aggiunta al cluster AKS.

Se si dispone di più cluster servizio contenitore di AZURE in aree diverse, usare Gestione traffico per controllare il flusso del traffico per applicazioni eseguite in ogni cluster. [Gestione traffico di Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/) è un servizio di bilanciamento del carico del traffico basato su DNS in grado di distribuire il traffico di rete tra le aree. Usare Gestione traffico per instradare gli utenti in base al tempo di risposta del cluster o basato su Geografia.

![Servizio contenitore di AZURE con gestione traffico](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

I clienti che hanno in genere un singolo cluster servizio contenitore di AZURE connettersi al nome di indirizzo IP o DNS del servizio di una determinata applicazione. In una distribuzione multicluster, i clienti devono connettersi a un nome DNS di gestione traffico che punta ai servizi in ogni cluster AKS. Definire questi servizi con endpoint di gestione traffico. Ogni endpoint è il *indirizzo IP del servizio di bilanciamento del carico del servizio*. Usare questa configurazione per indirizzare il traffico di rete dall'endpoint di gestione traffico in un'unica area per l'endpoint in un'area diversa.

![Geografico di routing tramite Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Gestione traffico consente di eseguire ricerche DNS e restituisce l'endpoint più appropriato un utente. Profili annidati possono stabilire le priorità di una posizione primaria. Ad esempio, gli utenti devono connettersi a livello generale rispettiva area geografica più vicina. Se tale area presenta un problema, gestione traffico indirizza invece gli utenti in un'area secondaria. Questo approccio assicura che i clienti possono connettersi a un'istanza dell'applicazione anche se non è disponibile l'area geografica più vicina.

Per informazioni su come configurare gli endpoint e routing, vedere [configurare il metodo di routing del traffico geografico tramite Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Routing di livello 7 dell'applicazione con il servizio di ingresso principale di Azure

Gestione traffico Usa DNS (livello 3) per il traffico di forma. [Il servizio di ingresso principale Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) offre un'opzione di routing HTTP/HTTPS (livello 7). Funzionalità aggiuntive porta d'ingresso del servizio di Azure includono SSL terminazione, il dominio personalizzato, web application firewall, di riscrittura dell'URL e l'affinità di sessione. Esaminare le esigenze di traffico dell'applicazione per determinare la soluzione più adatta.

## <a name="enable-geo-replication-for-container-images"></a>Abilitare la replica geografica per le immagini del contenitore

**Procedura consigliata**: Store le immagini del contenitore in Registro contenitori di Azure e la replica geografica il Registro di sistema per ogni area del servizio contenitore di AZURE.

Per distribuire ed eseguire le applicazioni nel servizio Azure Kubernetes, è necessario un modo per archiviare ed eseguire il pull delle immagini del contenitore. Registro contenitori si integra con servizio contenitore di AZURE, è possibile archiviare in modo sicuro le immagini del contenitore o i grafici Helm. Registro contenitori di supporta la replica geografica multimaster per replicare automaticamente le immagini per le aree di Azure in tutto il mondo. 

Per migliorare le prestazioni e disponibilità, usare la replica geografica del registro contenitori per creare un registro di sistema in ogni area in cui si dispone di un cluster AKS. Ogni cluster AKS quindi effettua il pull di immagini del contenitore dal registro contenitori locale nella stessa area:

![Contenitore del Registro di sistema-replica geografica per le immagini del contenitore](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando si usa la replica geografica del registro contenitori di immagini pull dalla stessa area, i risultati sono:

* **Più velocemente**: È eseguire il pull di immagini da connessioni di rete ad alta velocità, bassa latenza nella stessa area di Azure.
* **Più affidabile**: Se non è disponibile un'area, il cluster AKS effettua il pull di immagini da un registro contenitori disponibili.
* **Più economica**: Non vengono applicati addebiti in uscita di rete tra i Data center.

La replica geografica è una funzionalità del *Premium* registri contenitori SKU. Per informazioni su come configurare la replica geografica, vedere [replica geografica del registro contenitori](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Rimuovere lo stato del servizio dai contenitori

**Procedura consigliata**: Dove possibile, non archiviare lo stato del servizio all'interno del contenitore. Usare invece una piattaforma di Azure come servizio (PaaS) che supporta la replica multiarea.

*Stato del servizio* si intendono i dati in memoria o su disco che un servizio necessita per funzionare. Include le strutture di dati e le variabili membro che vengono lette e scritte dal servizio. A seconda del modo in cui è progettato il servizio, lo stato può includere anche i file o altre risorse che sono archiviati sul disco. Ad esempio, lo stato potrebbe includere i file di che un database viene utilizzato per archiviare i dati e log delle transazioni.

Lo stato può essere esternalizzato o condivideranno il percorso con il codice che modifica lo stato. In genere, si Esternalizza stato usando un database o un altro archivio dati che viene eseguito in computer diversi in rete o che viene eseguito fuori del processo nello stesso computer.

I contenitori e microservizi sono più resilienti quando i processi che vengono eseguiti all'interno di essi non mantengono lo stato. Poiché le applicazioni contengono quasi sempre di uno stato, usare una soluzione PaaS, ad esempio Database di Azure per MySQL, Database di Azure per PostgreSQL, o un Database SQL di Azure.

Per compilare applicazioni portabili, vedere le linee guida seguenti:

* [La metodologia di app a 12 fattori](https://12factor.net/)
* [Eseguire un'applicazione web in più aree di Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Creare un piano di migrazione di archiviazione

**Procedura consigliata**: Se si Usa archiviazione di Azure, preparare e testare la procedura eseguire la migrazione dell'archiviazione dall'area primaria all'area di backup.

Le applicazioni potrebbero usare archiviazione di Azure per i propri dati. Poiché le applicazioni vengono distribuite tra più cluster servizio contenitore di AZURE in aree diverse, è necessario mantenere lo spazio di archiviazione sincronizzata. Di seguito sono due modi comuni per la replica di archiviazione:

* Replica asincrona basata sull'infrastruttura
* Replica asincrona basata sull'applicazione

### <a name="infrastructure-based-asynchronous-replication"></a>Replica asincrona basata sull'infrastruttura

Le applicazioni potrebbero richiedere un archivio permanente anche dopo l'eliminazione di un pod. In Kubernetes, è possibile usare volumi permanenti per rendere persistenti di archiviazione dei dati. Volumi permanenti sono montati in una macchina virtuale del nodo e quindi esposti per il numero di POD. Volumi permanenti seguono POD, anche se il numero di POD viene spostati in un nodo diverso all'interno dello stesso cluster.

La strategia di replica utilizzata varia a seconda della soluzione di archiviazione. Soluzioni di archiviazione comuni, ad esempio [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [torre](https://rook.io/docs/rook/master/disaster-recovery.html), e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) fornire le proprie linee guida sul ripristino di emergenza e replica.

La tipica strategia consiste nel fornire una temporizzazione comune di archiviazione in cui le applicazioni possono scrivere i propri dati. Questi dati vengono poi replicati tra le aree e sono quindi accessibili in locale.

![Replica asincrona basata sull'infrastruttura](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se si usa Azure Managed Disks, è possibile scegliere la replica e le soluzioni di ripristino di emergenza come i seguenti:

* [Velero in Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replica asincrona basata sull'applicazione

Kubernetes non offre attualmente alcuna implementazione nativa per la replica asincrona basata sull'applicazione. Poiché i contenitori e Kubernetes sono a regime, qualsiasi applicazione o un linguaggio all'approccio tradizionale dovrebbe funzionare. In genere, le applicazioni stesse replicano le richieste di archiviazione, che vengono quindi scritti in un archivio dati sottostante di ogni cluster.

![Replica asincrona basata sull'applicazione](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è incentrato sulla continuità aziendale e considerazioni sul ripristino di emergenza per i cluster AKS. Per altre informazioni sulle operazioni del cluster nel servizio contenitore di AZURE, vedere questi articoli sulle procedure consigliate:

* [Isolamento multi-tenancy e cluster][aks-best-practices-cluster-isolation]
* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
