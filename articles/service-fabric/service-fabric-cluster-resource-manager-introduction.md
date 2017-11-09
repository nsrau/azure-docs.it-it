---
title: Introduzione a Cluster Resource Manager di Service Fabric | Microsoft Docs
description: Un'introduzione a Cluster Resource Manager di Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3e8cd4dc8e960e38ba0e4a9a195b2f61d9ec1924
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduzione a Cluster Resource Manager di Service Fabric
Tradizionalmente, la gestione dei sistemi IT o dei Servizi online comportava dedicare alcune macchine virtuali o fisiche a tali sistemi o servizi specifici. I servizi erano progettati come livelli. Era presente un livello "web" e un livello "dati" o "archiviazione". Le applicazioni avevano un livello di messaggistica con un flusso di richieste in entrata e in uscita, nonché un set di computer dedicati alla memorizzazione nella cache. A ogni livello o tipo di carico di lavoro veniva assegnato un computer dedicato. Al database venivano assegnati un paio di computer dedicati, ai server web qualcuno in più. Se un particolare tipo di carico di lavoro causava il surriscaldamento del computer su cui si trovava, si aggiungevano più computer con la medesima configurazione a quel livello. Non tutti i carichi di lavoro possono tuttavia essere scalati orizzontalmente facilmente, in particolare per il livello dati i computer vengono sostituiti con computer di dimensioni più grandi. Semplice. Se si verificava un problema su un computer, la parte dell'applicazione interessata veniva gestita con una capacità inferiore fino a quando il computer non veniva ripristinato. Ancora piuttosto semplice, anche se non necessariamente divertente.

Ora il mondo dell'architettura dei servizi e del software è cambiato. È più comune l'adozione di una progettazione di scalabilità orizzontale da parte delle applicazioni. La creazione di applicazioni con contenitori o microservizi (o entrambi) è comune. Anche se è ancora possibile avere solo pochi computer, questi non eseguono una sola istanza di un carico di lavoro. Possono eseguire più carichi di lavoro diversi contemporaneamente. Ci sono dozzine di tipi di servizi diversi e nessuno usa le risorse di un intero computer. Sono presenti anche centinaia di istanze diverse di questi servizi. Ciascuna istanza denominata dispone di una o più istanze o repliche per la disponibilità elevata. A seconda delle dimensioni e delle misure di questi carichi di lavoro potrebbero essere presenti centinaia o migliaia di computer. 

A un tratto, la gestione dell'ambiente non è più semplice come gestire alcuni computer dedicati a singoli tipi di carichi di lavoro. I server sono virtuali e non sono più riconoscibili per nome: è stato ultimato il passaggio da [animaletti da compagnia a capi di bestiame](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20). Ora, la configurazione riguarda più i servizi che i computer. L'hardware dedicato a una singola istanza di un carico di lavoro è principalmente un ricordo del passato. I servizi stessi sono diventati piccoli sistemi distribuiti che si dividono in più parti più piccole di hardware.

Poiché l'applicazione non è più composta da una serie di monoliti distribuiti su più livelli, ora si devono affrontare molte più combinazioni. Chi decide quali e quanti tipi di carichi di lavoro possono essere eseguiti su quale hardware? Quali carichi di lavoro funzionano correttamente con lo stesso hardware e quali sono in conflitto? Quando un computer smette di funzionare cosa era in funzione in quel momento? Chi è responsabile di assicurarsi che il carico di lavoro venga rimesso in funzione? Si attende che la macchina virtuale ritorni attiva oppure i carichi di lavoro effettuano automaticamente il failover su altri computer e proseguono l'esecuzione? È necessario l'intervento dell'utente? Come si gestiscono gli aggiornamenti in questo ambiente?

Gli sviluppatori e gli operatori che operano nell'ambiente hanno bisogno di aiuto per gestire questa complessità. È chiaro che una campagna di assunzioni per tentare di nascondere questa complessità non è la soluzione giusta.

## <a name="introducing-orchestrators"></a>Introduzione agli agenti di orchestrazione
Un "agente di orchestrazione" è il termine generale con cui ci si riferisce a un componente software che aiuta gli amministratori a gestire questi tipi di ambienti. Gli agenti di orchestrazione sono i componenti che si fanno carico di richieste come "Vorrei avere in esecuzione 5 copie di questo servizio nel mio ambiente". Provano a far corrispondere l'ambiente allo stato desiderato, a prescindere dagli eventi.

Sono gli agenti di orchestrazione e non gli esseri umani che entrano in gioco quando un computer si arresta o un flusso di lavoro si blocca per qualche motivo non previsto. La maggior parte degli agenti di orchestrazione non si limita a gestire gli errori. Gli agenti infatti dispongono di altre funzionalità come la gestione di nuove distribuzioni, degli aggiornamenti e del consumo e la governance di risorse. In sostanza tutti gli agenti di orchestrazione si occupano del mantenimento di uno stato o di una configurazione desiderati dell'ambiente. L'utente deve dire all'agente di orchestrazione che cosa fare e lasciargli fare i lavori pesanti. Aurora su Mesos, Docker Datacenter/Docker Swarm, Kubernetes e Service Fabric sono tutti esempi di agenti di orchestrazione. Questi agenti di orchestrazione vengono continuamente sviluppati per fare fronte alle necessità dei carichi di lavoro reali negli ambienti di produzione. 

## <a name="orchestration-as-a-service"></a>Orchestrazione come servizio
Cluster Resource Manager è il componente di sistema che gestisce l'orchestrazione in Service Fabric. Il lavoro di Cluster Resource Manager è suddiviso in tre parti:

1. Applicazione di regole
2. Ottimizzazione dell'ambiente
3. Contribuire con altri processi

Per conoscere il funzionamento di Cluster Resource Manager, vedere il video seguente di Microsoft Virtual Academy:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Che cosa non è
Nelle applicazioni tradizionali di livello N è sempre presente un [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). In genere si trattava di un servizio di bilanciamento del carico di rete (NLB) o di applicazione (ALB) a seconda del posizionamento nello stack di rete. Alcuni servizi di bilanciamento del carico sono basati su hardware, come BigIP F5, mentre altri sono software, ad Bilanciamento carico di rete di Microsoft. In altri ambienti si userà qualcosa di simile a HAProxy, nginx, Istio o Envoy in questo ruolo. In queste architetture, il compito del servizio di bilanciamento del carico è assicurare che i carichi di lavoro senza stato ricevano sostanzialmente la stessa quantità di lavoro. Le strategie per il bilanciamento del carico sono variate. Alcuni servizi di bilanciamento inviavano ciascuna chiamata diversa a un server diverso. Altri fornivano persistenza o visibilità elevata delle sessioni. I servizi di bilanciamento più avanzati usano stime o rapporti effettivi del carico per reindirizzare le chiamate in base ai costi previsti e al carico corrente del computer.

I servizi di bilanciamento del carico o i router di messaggistica provavano ad assicurare il bilanciamento sostanziale del livello Web/ruolo di lavoro. Le strategie per il bilanciamento del livello dati erano diverse e dipendevano dal meccanismo di archiviazione dati. Il bilanciamento del livello dati era basato sul partizionamento orizzontale dei dati, la memorizzazione nella cache, le visualizzazioni gestite, le stored procedure e altri meccanismi specifici di archiviazione.

Anche se alcune di queste strategie sono interessanti, Cluster Resource Manager di Service Fabric non assomiglia affatto a un servizio di bilanciamento del carico di rete né a una cache. Un servizio di bilanciamento del carico di rete consente di bilanciare i front-end suddividendo il traffico di essi. Cluster Resource Manager di Service Fabric ha un approccio diverso. In breve, Service Fabric sposta i *servizi* dove sono più utili, aspettandosi che il traffico o il carico faranno seguito. Ad esempio, potrebbero spostare i servizi in nodi attualmente non atti poiché i servizi già presenti non sono usati spesso. I nodi possono essere inattivi poiché i servizi presenti sono stati eliminati o spostati. Come ulteriore esempio, Cluster Resource Manager potrebbe anche rimuovere un servizio da un computer per spostarlo, magari perché il computer sta per essere aggiornato o è sovraccaricato da un picco di uso di risorse a causa dei servizi in esecuzione. In alternativa i requisiti di risorse del servizio potrebbero essere aumentati. Di conseguenza non ci sono risorse sufficienti per continuare l'esecuzione su questo computer. 

Poiché Cluster Resource Manager è responsabile dello spostamento dei servizi contiene un set di funzionalità diverso rispetto a quello di un servizio di bilanciamento del carico di rete. Questo avviene perché i bilanciamenti del carico di rete inviano il traffico di rete dove già si trovano i servizi, anche se tale posizione non è ideale per l'esecuzione del servizio stesso. Cluster Resource Manager di Service Fabric impiega strategie essenzialmente diverse per garantire che le risorse del cluster vengano ben usate.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sull'architettura e sul flusso di informazioni in Cluster Resource Manager, vedere [questo articolo](service-fabric-cluster-resource-manager-architecture.md)
- Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni sulle metriche vedere l'articolo [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Per altre informazioni sulla configurazione dei servizi vedere [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager sfrutta le funzionalità di gestione di Service Fabric. Per altre informazioni sull'integrazione, vedere [questo articolo](service-fabric-cluster-resource-manager-management-integration.md)
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
