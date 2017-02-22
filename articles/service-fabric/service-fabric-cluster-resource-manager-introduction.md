---
title: Introduzione a Cluster Resource Manager di Service Fabric | Microsoft Docs
description: Un&quot;introduzione a Cluster Resource Manager di Service Fabric.
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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: ec470466f006265af5c4ccfddeeba975e6e648b5


---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduzione a Cluster Resource Manager di Service Fabric
Tradizionalmente, la gestione dei sistemi IT o di un gruppo di servizi comportava dedicare alcune macchine virtuali o fisiche a tali sistemi o servizi specifici. La maggiore parte dei servizi principali erano suddivisi in un livello "web" e un livello "dati" o "archiviazione", magari con alcuni altri componenti specializzati come una cache. Altri tipi di applicazioni disponevano invece di un livello di messaggistica con richieste in entrata e in uscita. Questo livello si connetteva a un livello di lavoro per eventuali analisi o trasformazioni necessarie come parte della messaggistica. A ogni tipo di carico di lavoro veniva assegnato un computer dedicato. Al database venivano assegnati un paio di computer dedicati, ai server web qualcuno in più. Se un particolare tipo di carico di lavoro causava il surriscaldamento del computer su cui si trovava, si aggiungevano più computer con la medesima configurazione. Generalmente, però, si sostituivano alcuni computer con altri di dimensioni maggiori. Semplice. Se si verificava un problema su un computer, la parte dell'applicazione interessata veniva gestita con una capacità inferiore fino a quando il computer non veniva ripristinato. Ancora piuttosto semplice, anche se non necessariamente divertente.

Supponiamo ora che a un certo punto si presentasse la necessità di aumentare la capacità del proprio sistema adottando contenitori e/o microservizi. Improvvisamente ci si ritrova con decine, centinaia o migliaia di computer. Ci sono decine di tipi di servizi diversi e nessuno usa le risorse di un intero computer. Sono presenti anche centinaia di istanze diverse di questi servizi. Ciascuna istanza denominata dispone di una o più istanze o repliche per la disponibilità elevata.

A un tratto, la gestione dell'ambiente non è più semplice come gestire alcuni computer dedicati a singoli tipi di carichi di lavoro. I server sono virtuali e non sono più riconoscibili per nome ( *è stato* ultimato il passaggio da [animaletti da compagnia a capi di bestiame](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) ). Ora, la configurazione riguarda più i servizi che i computer. L'hardware dedicato è un ricordo del passato e i servizi stessi sono diventati piccoli sistemi distribuiti, che si dividono in più parti più piccole di hardware.

La conseguenza di quella che era un'applicazione a più livelli monolitica in servizi separati in esecuzione su hardware è che ora si devono affrontare molte più combinazioni. Chi decide quali e quanti tipi di carichi di lavoro possono essere eseguiti su quale hardware? Quali carichi di lavoro funzionano correttamente con lo stesso hardware e quali sono in conflitto? Quando un computer smette di funzionare, cosa era in funzione in quel momento? Chi è responsabile di assicurarsi che il carico di lavoro venga rimesso in funzione? Si attende che la macchina virtuale ritorni attiva oppure i carichi di lavoro effettuano automaticamente il failover su altri computer e proseguono l'esecuzione? È necessario l'intervento dell'utente? Come si gestiscono gli aggiornamenti in questo ambiente?

Gli sviluppatori e gli operatori che operano nell'ambiente hanno bisogno di aiuto per gestire questa complessità. È chiaro che una campagna di assunzioni per tentare di nascondere questa complessità non è la soluzione giusta.

Cosa fare?

## <a name="introducing-orchestrators"></a>Introduzione agli agenti di orchestrazione
Un "agente di orchestrazione" è il termine generale con cui ci si riferisce a un componente software che aiuta gli amministratori a gestire questi tipi di ambienti. Gli agenti di orchestrazione sono i componenti che si fanno carico di richieste come "Vorrei avere in esecuzione&5; copie di questo servizio nel mio ambiente". Provano a far corrispondere l'ambiente allo stato desiderato, a prescindere dagli eventi.

Sono gli agenti di orchestrazione, e non gli esseri umani, che entrano in gioco quando un computer si arresta o un flusso di lavoro si blocca per qualche motivo non previsto. La maggior parte degli agenti non si limita a gestire gli errori. Gli agenti, infatti, dispongono di altre funzionalità, come la gestione di nuove distribuzioni, degli aggiornamenti e del consumo di risorse. In sostanza, tutti gli agenti di orchestrazione si occupano del mantenimento di uno stato o di una configurazione desiderati dell'ambiente. L'utente deve dire all'agente di orchestrazione che cosa fare e lasciargli fare i lavori pesanti. Chronos o Marathon su Mesosphere, Fleet, Docker Datacenter/Docker Swarm, Kubernetes e Service Fabric sono tutti esempi di orchestrazione o contengono agenti di orchestrazione. Ne vengono creati sempre di nuovi per fare fronte alle crescenti e mutevoli complessità legate alla gestione delle distribuzioni del mondo reale in diversi tipi di ambienti.

## <a name="orchestration-as-a-service"></a>Orchestrazione come servizio
Il compito di orchestrazione in un cluster di Service Fabric viene gestito principalmente da Cluster Resource Manager. Cluster Resource Manager di Service Fabric è uno dei servizi di sistema di Service Fabric e viene avviato automaticamente all'interno di ogni cluster. In genere, il lavoro di Cluster Resource Manager è suddiviso in tre parti:

1. Applicazione di regole
2. Ottimizzazione dell'ambiente
3. Contribuire con altri processi

Per conoscere il funzionamento di Cluster Resource Manager, vedere il video seguente di Microsoft Virtual Academy:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Che cosa non è
Nelle applicazioni tradizionali di livello N si faceva sempre cenno a un "servizio di bilanciamento del carico". In genere si trattava di un servizio di bilanciamento del carico di rete (NLB) o di applicazione (ALB) a seconda del posizionamento nello stack di rete. Alcuni servizi di bilanciamento del carico sono basati su hardware, come BigIP F5, mentre altri sono software, ad Bilanciamento carico di rete di Microsoft. In altri ambienti si userà qualcosa di simile a HAProxy o nginx a questo scopo. In queste architetture, il compito del servizio di bilanciamento del carico è assicurare che i carichi di lavoro senza stato ricevano sostanzialmente la stessa quantità di lavoro. Le strategie di bilanciamento del carico erano varie. Alcuni servizi di bilanciamento inviavano ciascuna chiamata diversa a un server diverso. Altri fornivano persistenza o visibilità elevata delle sessioni. I servizi di bilanciamento più avanzati usavano stime o rapporti effettivi per reindirizzare le chiamate in base ai costi previsti e al carico corrente del computer.

I servizi di bilanciamento del carico o i router di messaggistica provavano ad assicurare il bilanciamento sostanziale del livello Web/ruolo di lavoro. Le strategie per garantire il bilanciamento del livello dati erano diverse e dipendevano dal meccanismo di archiviazione dei dati, che in genere ruotava intorno al partizionamento dei dati, alla memorizzazione nella cache, alle visualizzazioni gestite, alle procedure memorizzate e ad altri meccanismi di archiviazione specifici.

Anche se alcune di queste strategie sono interessanti, Cluster Resource Manager di Service Fabric non assomiglia affatto a un servizio di bilanciamento del carico di rete né a una cache. Un servizio di bilanciamento del carico di rete garantisce che i front-end siano bilanciati, spostando il traffico dove sono in esecuzione i servizi. Cluster Resource Manager di Service Fabric ha un approccio diverso. In breve, Service Fabric sposta i *servizi* dove sono più utili, aspettandosi che il traffico o il carico faranno seguito. Ad esempio, potrebbero spostare i servizi in nodi attualmente non atti poiché i servizi già presenti non sono usati spesso. I nodi possono essere inattivi poiché i servizi presenti sono stati eliminati o spostati. Come ulteriore esempio, Cluster Resource Manager potrebbe anche rimuovere un servizio da un computer per spostarlo, magari perché il computer sta per essere aggiornato o è sovraccaricato da un picco di uso di risorse a causa dei servizi in esecuzione.

Poiché Cluster Resource Manager è responsabile dello spostamento dei servizi (non recapitare il traffico di rete nel punto in cui si trovano già i servizi), contiene un set di funzionalità diverso rispetto a quello di un servizio di bilanciamento del carico di rete. Si vedrà in dettaglio come questo impieghi infatti strategie essenzialmente diverse per garantire che le risorse hardware del cluster vengano ben usate.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sull'architettura e sul flusso di informazioni in Cluster Resource Manager, vedere [questo articolo](service-fabric-cluster-resource-manager-architecture.md)
* Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili per Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
* Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)
* Cluster Resource Manager sfrutta le funzionalità di gestione di Service Fabric. Per altre informazioni sull'integrazione, vedere [questo articolo](service-fabric-cluster-resource-manager-management-integration.md)
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)



<!--HONumber=Feb17_HO3-->


