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
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 490ec2933130f15d707a06206e452f47554acdeb


---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduzione a Cluster Resource Manager di Service Fabric
Tradizionalmente, la gestione dei sistemi IT o di un gruppo di servizi comportava dedicare alcune macchine virtuali o fisiche a tali sistemi o servizi specifici. La maggiore parte dei servizi principali erano suddivisi in un livello "web" e un livello "dati" o "archiviazione", magari con alcuni altri componenti specializzati come una cache. Altri tipi di applicazioni avrebbero avuto un livello "messaggistica" che gestiva le richieste in entrata e uscita connesso a un livello "lavoro" per le attività di analisi o trasformazione necessarie per la messaggistica. A ogni tipo di carico di lavoro veniva assegnato un computer dedicato. Al database venivano assegnati un paio di computer dedicati, ai server web qualcuno in più. Se un particolare tipo di carico di lavoro provocava il sovraccarico del computer che lo ospitava, si aggiungevano altri computer configurati per quel particolare tipo di carico di lavoro oppure si sostituivano alcuni computer con equivalenti più potenti. Semplice. Se si verificava un problema su un computer, la parte dell'applicazione interessata veniva gestita con una capacità inferiore fino a quando il computer non veniva ripristinato. Ancora piuttosto semplice, anche se non necessariamente divertente.

Supponiamo ora che a un certo punto si presentasse la necessità di aumentare la capacità del proprio sistema adottando contenitori e/o microservizi. Improvvisamente ci si ritrova con decine, centinaia o addirittura migliaia di computer, decine di diversi tipi di servizi, magari centinaia di diverse istanze di tali servizi, ognuna con una o più istanze o repliche per disponibilità elevata.

A un tratto, la gestione dell'ambiente non è più semplice come gestire alcuni computer dedicati a singoli tipi di carichi di lavoro. I server sono virtuali e non sono più riconoscibili per nome ( *è stato* ultimato il passaggio da [animaletti da compagnia a capi di bestiame](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) ). Ora, la configurazione riguarda più i servizi che i computer. L'hardware dedicato è un ricordo del passato e servizi sono diventati piccoli sistemi distribuiti, che si dividono in più parti più piccole di hardware.

La conseguenza di quella che era un'applicazione a più livelli monolitica in servizi separati in esecuzione su hardware è che ora si devono affrontare molte più combinazioni. Chi decide quali e quanti tipi di carichi di lavoro possono essere eseguiti su quale hardware? Quali carichi di lavoro funzionano correttamente con lo stesso hardware e quali sono in conflitto? Quando un computer smette di funzionare, cosa era in funzione in quel momento? Chi è responsabile di assicurarsi che il carico di lavoro venga rimesso in funzione? Si attende che la macchina virtuale ritorni attiva oppure i carichi di lavoro effettuano automaticamente il failover su altri computer e proseguono l'esecuzione? È necessario l'intervento dell'utente? Come si gestiscono gli aggiornamenti in ambienti di questo tipo?

Gli sviluppatori e gli operatori di questo tipo di mondo avranno bisogno di aiuto per gestire questa complessità, ma è chiaro che una campagna di assunzioni e il tentativo di documentare su carta questa complessità non sia la soluzione giusta.

Cosa fare?

## <a name="introducing-orchestrators"></a>Introduzione agli agenti di orchestrazione
Un "agente di orchestrazione" è il termine generale con cui ci si riferisce a un componente software che aiuta gli amministratori a gestire questi tipi di ambienti. Gli agenti di orchestrazione sono i componenti che si fanno carico di richieste come "Vorrei avere in esecuzione 5 copie di questo servizio nel mio ambiente", le realizzano e provano quindi a mantenerle efficaci.

Sono gli agenti di orchestrazione, e non gli esseri umani, che entrano in gioco quando un computer si arresta o un flusso di lavoro si blocca per qualche motivo non previsto. La maggior parte degli agenti di orchestrazione si occupa di ben più che più risolvere gli errori, ad esempio contribuiscono ad attivare le nuove distribuzioni, gestiscono gli aggiornamenti e il consumo di risorse, ma servono fondamentalmente a mantenere nello stato desiderato la configurazione nell'ambiente. L'utente deve dire all'agente di orchestrazione che cosa fare e lasciargli fare i lavori pesanti. Chronos o Marathon su Mesosphere, Fleet, Swarm, Kubernetes e Service Fabric sono tutti esempi di orchestrazione o contengono agenti di orchestrazione. Ne vengono creati sempre di nuovi per fare fronte alle crescenti e mutevoli complessità legate alla gestione delle distribuzioni del mondo reale in diversi tipi di ambienti.

## <a name="orchestration-as-a-service"></a>Orchestrazione come servizio
Il compito di orchestrazione in un cluster di Service Fabric viene gestito principalmente da Cluster Resource Manager. Cluster Resource Manager di Service Fabric è uno dei servizi di sistema di Service Fabric e viene avviato automaticamente all'interno di ogni cluster.  In genere, il lavoro di Cluster Resource Manager è suddiviso in tre parti:

1. Applicazione di regole
2. Ottimizzazione dell'ambiente
3. Supporto di altri processi

### <a name="what-it-isnt"></a>Che cosa non è
Nelle app Web tradizionali a più livelli era sempre presente un qualche tipo di "bilanciamento del carico", in genere definito come un servizio di bilanciamento del carico di rete o bilanciamento del carico dell'applicazione, a seconda di dove risiedeva nello stack di rete. Alcuni servizi di bilanciamento del carico sono basati su hardware, come BigIP F5, mentre altri sono software, ad esempio Bilanciamento carico di rete di Microsoft. In altri ambienti verrà visualizzato qualcosa di simile a HAProxy in questo ruolo. In queste architetture, il servizio di bilanciamento del carico assicurava che tutti i computer front-end senza stato o tutti i computer del cluster ricevessero approssimativamente la stessa quantità di lavoro. Le strategie per realizzare questo compito erano diverse, comprendevano l'invio di ogni chiamata a un server diverso, l'aggiunta o la persistenza delle sessioni fino alla stima e all'allocazione della chiamata sulla base del suo costo previsto e del carico di lavoro corrente sul computer.

Si noti che questo era, nel migliore dei casi, il meccanismo per garantire che il livello Web rimanesse approssimativamente bilanciato. Le strategie per garantire il bilanciamento del livello dati erano completamente diverse e dipendevano dal meccanismo di archiviazione dei dati, che in genere ruotava intorno al partizionamento dei dati, alla memorizzazione nella cache, alle visualizzazioni gestite dal database, alle procedure memorizzate, e così via.

Anche se alcune di queste strategie sono interessanti, Cluster Resource Manager di Service Fabric non assomiglia affatto a un servizio di bilanciamento del carico di rete né a una cache. Mentre un servizio di bilanciamento del carico di rete assicura che i front-end siano bilanciati spostando il traffico dove sono in esecuzione i servizi, Cluster Resource Manager di Service Fabric adotta un approccio completamente diverso spostando sostanzialmente i *servizi* dove ha più senso, anche dal punto di vista del traffico e del carico. Può trattarsi, ad esempio, di nodi attualmente inattivi in quanto i servizi presenti al loro interno non stanno eseguendo tante operazioni o nodi sono stati eliminati o spostati in altre aree. Come altro esempio, Cluster Resource Manager può anche spostare i servizi da una macchina che sta per essere aggiornata o che è sovraccarica a causa di un picco nell'uso dei servizi in esecuzione su di essa. Poiché Cluster Resource Manager è responsabile dello spostamento dei servizi (non recapitare il traffico di rete nel punto in cui si trovano già i servizi), contiene un set di funzionalità significativamente diverso rispetto a quello di un servizio di bilanciamento del carico di rete e impiega strategie essenzialmente diverse per garantire che le risorse hardware del cluster vengano ben usate.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sull'architettura e sul flusso di informazioni in Cluster Resource Manager, vedere [questo articolo ](service-fabric-cluster-resource-manager-architecture.md)
* Cluster Resource Manager dispone di molte opzioni per la descrizione del cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili per Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
* Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)
* Cluster Resource Manager sfrutta le funzionalità di gestione di Service Fabric. Per altre informazioni sull'integrazione, vedere [questo articolo](service-fabric-cluster-resource-manager-management-integration.md)
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)




<!--HONumber=Nov16_HO3-->


