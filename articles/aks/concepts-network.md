---
title: Concetti - Funzionalità di rete nel servizio Kubernetes di Azure (AKS)
description: Informazioni sulle funzionalità di rete nel servizio Kubernetes di Azure (AKS), tra cui funzionalità di rete di base e avanzate, controller di ingresso, bilanciamento del carico e indirizzi IP statici.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 62ba98f221041d5bbf9bb095a02d052218eb0fd0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380886"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Concetti relativi alla rete per le applicazioni nel servizio Kubernetes di Azure (AKS)

In un approccio allo sviluppo delle applicazioni come microservizi basati su contenitori, i componenti dell'applicazione devono interagire per elaborare le proprie attività. Kubernetes offre diverse risorse che consentono queste comunicazioni tra le applicazioni. È possibile connettersi alle applicazioni ed esporle internamente o esternamente. Per sviluppare applicazioni a disponibilità elevata, è possibile bilanciare il carico delle applicazioni. Le applicazioni più complesse possono richiedere la configurazione del traffico in ingresso per la terminazione SSL/TLS o il routing di più componenti. Per motivi di sicurezza, potrebbe anche essere necessario limitare il flusso del traffico di rete all'interno o tra i pod e i nodi.

Questo articolo introduce i concetti di base correlati alle funzionalità di rete per le applicazioni nel servizio Kubernetes di Azure:

- [Servizi](#services)
- [Reti virtuali di Azure](#azure-virtual-networks)
- [Controller di ingresso](#ingress-controllers)
- [Criteri di rete](#network-policies)

## <a name="kubernetes-basics"></a>Nozioni di base di Kubernetes

Per consentire l'accesso alle applicazioni o per consentire ai componenti dell'applicazione di comunicare tra loro, Kubernetes offre un livello di astrazione per funzionalità di rete virtuale. I nodi Kubernetes vengono connessi a una rete virtuale e possono fornire la connettività in ingresso e in uscita per i pod. Il componente *kube-proxy* viene eseguito in ogni nodo per fornire queste funzionalità di rete.

In Kubernetes, i *servizi* raggruppano i pod dal punto di vista logico per consentire l'accesso diretto tramite un indirizzo IP o nome DNS e su una porta specifica. È anche possibile distribuire il traffico usando un *bilanciamento del carico*. Si può anche ottenere un routing più complesso del traffico dell'applicazione con i *controller di ingresso*. La sicurezza e il filtraggio del traffico di rete per i pod sono possibili con i *criteri di rete* di Kubernetes.

Anche la piattaforma Azure contribuisce a semplificare le funzionalità di rete virtuale per i cluster AKS. Quando si crea un bilanciamento del carico di Kubernetes, viene creata e configurata la risorsa di bilanciamento del carico di Azure sottostante. Quando si aprono le porte di rete per i pod, vengono configurate le regole del gruppo di sicurezza di rete di Azure corrispondente. Per il routing delle applicazioni HTTP, Azure può anche configurare il *DNS esterno* quando vengono configurate nuove route in ingresso.

## <a name="services"></a>Servizi

Per semplificare la configurazione di rete per i carichi di lavoro dell'applicazione, Kubernetes usa *servizi* per raggruppare un set di pod in modo logico e fornire la connettività di rete. Sono disponibili i tipi di servizio seguenti:

- **IP del cluster** - Crea un indirizzo IP interno per l'uso all'interno del cluster AKS. Soluzione idonea per le applicazioni solo interne che supportano altri carichi di lavoro all'interno del cluster.

    ![Diagramma che mostra il flusso del traffico IP del cluster in un cluster AKS][aks-clusterip]

- **NodePort** - Crea un mapping delle porte nel nodo sottostante che consente l'accesso diretto all'applicazione con l'indirizzo IP e la porta del nodo.

    ![Diagramma che mostra il flusso del traffico NodePort in un cluster AKS][aks-nodeport]

- **LoadBalancer** - Crea una risorsa di bilanciamento del carico di Azure, configura un indirizzo IP esterno e connette i pod richiesti al pool back-end del bilanciamento del carico. Per consentire al traffico dei clienti di raggiungere l'applicazione, vengono create regole di bilanciamento del carico per le porte desiderate. 

    ![Diagramma che illustra il flusso del traffico di bilanciamento del carico in un cluster AKS][aks-loadbalancer]

    Per un maggiore controllo e il routing del traffico in ingresso, è invece possibile usare un [controller di ingresso](#ingress-controllers).

- **ExternalName** - Crea una voce DNS specifica per semplificare l'accesso dell'applicazione.

L'indirizzo IP per i bilanciamenti del carico e i servizi può essere assegnato in modo dinamico oppure è possibile specificare un indirizzo IP statico esistente da usare. Si possono assegnare sia indirizzi IP statici interni che esterni. Questo indirizzo IP statico esistente è spesso associato a una voce DNS.

Si possono creare sia bilanciamenti del carico *interni* che *esterni*. Ai bilanciamenti del carico interni viene assegnato solo un indirizzo IP privato, in modo che non siano accessibili da Internet.

## <a name="azure-virtual-networks"></a>Reti virtuali di Azure

Nel servizio Kubernetes di Azure è possibile distribuire un cluster che usa uno dei due modelli di rete seguenti:

- Funzionalità di rete di *base* - Le risorse di rete vengono create e configurate quando viene distribuito il cluster AKS.
- Funzionalità di rete *avanzate* - Il cluster AKS viene connesso alle risorse di rete virtuale e alle configurazioni esistenti.

### <a name="basic-networking"></a>Rete di base

L'opzione per le funzionalità di rete di *base* è la configurazione predefinita per la creazione del cluster AKS. La piattaforma Azure gestisce la configurazione di rete del cluster e i pod. Le funzionalità di rete di base sono appropriate per le distribuzioni che non richiedono la configurazione di una rete virtuale personalizzata. Con le funzionalità di rete di base non è possibile definire la configurazione di rete, ad esempio i nomi delle subnet o gli intervalli di indirizzi IP assegnati al cluster AKS.

I nodi in un cluster AKS configurato per le funzionalità di rete di base usano il plug-in Kubernetes [kubenet][kubenet].

Le funzionalità di rete di base sono le seguenti:

- Esporre un servizio Kubernetes esternamente o internamente tramite Azure Load Balancer.
- I pod possono accedere alle risorse su Internet pubblico.

### <a name="advanced-networking"></a>Rete avanzata

Con le funzionalità di rete *avanzate* i pod vengono posizionati in una rete virtuale di Azure configurata dall'utente. Questa rete virtuale offre connettività automatica ad altre risorse di Azure e l'integrazione con un'ampia gamma di funzionalità. Le funzionalità di rete avanzate sono appropriate per le distribuzioni che richiedono configurazioni di rete virtuale specifiche, ad esempio per usare una subnet e la connettività esistenti. Con le funzionalità di rete avanzate è possibile definire questi nomi di subnet e gli intervalli di indirizzi IP.

I nodi in un cluster AKS configurato per le funzionalità di rete avanzate usano il plug-in Kubernetes [Azure Container Networking Interface (CNI)][cni-networking].

![Diagramma che illustra due nodi ognuno dei quali è connesso da bridge a una singola rete virtuale di Azure][advanced-networking-diagram]

Le funzionalità di rete avanzate aggiuntive rispetto alle funzionalità di rete di base sono le seguenti:

- Distribuire il cluster AKS in una rete virtuale di Azure esistente o creare una nuova rete virtuale e una subnet per il cluster.
- A ogni pod nel cluster viene assegnato un indirizzo IP nella rete virtuale. I pod possono comunicare direttamente con altri pod nel cluster e altri nodi nella rete virtuale.
- Un pod può connettersi ad altri servizi in una rete virtuale con peering, nonché a reti locali tramite ExpressRoute e connessioni VPN da sito a sito. I pod sono raggiungibili anche da locale.
- I pod in una subnet con endpoint di servizio abilitati possono connettersi in modo sicuro ai servizi di Azure, ad esempio Archiviazione di Azure e il database SQL.
- È possibile creare route definite dall'utente per instradare il traffico dai pod a un'appliance virtuale di rete.

Per altre informazioni, vedere [Configurare funzionalità di rete avanzate nel servizio Kubernetes di Azure (AKS)][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Controller di ingresso

Quando si crea un servizio di tipo LoadBalancer, viene creata una risorsa di bilanciamento del carico di Azure sottostante. Il bilanciamento del carico viene configurato per distribuire il traffico verso i pod nel servizio su una porta specifica. Il servizio di tipo LoadBalancer opera solo sul livello 4: non è a conoscenza delle applicazioni effettive e non può gestire ulteriori considerazioni sul routing.

I *controller di ingresso* operano sul livello 7 e possono usare regole più intelligenti per distribuire il traffico delle applicazioni. Un uso comune di un controller di ingresso è l'indirizzamento del traffico HTTP verso applicazioni diverse in base all'URL in ingresso.

![Diagramma che mostra il flusso del traffico in ingresso in un cluster AKS][aks-ingress]

Nel servizio Kubernetes di Azure è possibile creare una risorsa di ingresso usando uno strumento come NGINX o la funzionalità di routing delle applicazioni HTTP AKS. Quando si abilita il routing delle applicazioni HTTP per un cluster AKS, la piattaforma Azure crea il controller di ingresso e un controller *DNS esterno*. Quando vengono create le risorse di ingresso in Kubernetes, i record A DNS necessari vengono creati in una zona DNS specifica del cluster. Per altre informazioni, vedere [Routing di applicazioni HTTP][aks-http-routing].

Un'altra funzionalità comune per il traffico in ingresso è la terminazione SSL/TLS. In applicazioni Web di grandi dimensioni a cui si accede tramite HTTPS, la terminazione TLS può essere gestita dalla risorsa di ingresso invece che all'interno dell'applicazione stessa. Per garantire la generazione e la configurazione automatiche della certificazione TLS, è possibile configurare la risorsa di ingresso per l'uso di provider, ad esempio Let's Encrypt. Per altre informazioni sulla configurazione di un controller di ingresso NGINX con Let's Encrypt, vedere [Traffico in ingresso e TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Un gruppo di sicurezza di rete filtra il traffico per le macchine virtuali, ad esempio i nodi del servizio Kubernetes di Azure. Quando si creano servizi, ad esempio LoadBalancer, la piattaforma Azure configura automaticamente le eventuali regole dei gruppi di sicurezza di rete necessarie. Non configurare manualmente le regole dei gruppi di sicurezza di rete per filtrare il traffico per i pod in un cluster AKS. Definire le eventuali porte necessarie e l'inoltro come parte dei manifesti del servizio Kubernetes e delegare alla piattaforma Azure la creazione o l'aggiornamento delle regole appropriate.

Esistono regole dei gruppi di sicurezza di rete predefinite per il traffico, ad esempio SSH. Queste regole predefinite sono progettate per la gestione del cluster e la risoluzione dei problemi di accesso. L'eliminazione di queste regole predefinite può causare problemi con la gestione di AKS e non consente di raggiungere l'obiettivo del livello di servizio (SLO).

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni introduttive per le funzionalità di rete del servizio Kubernetes di Azure, vedere [Configurare funzionalità di rete avanzate nel servizio Kubernetes di Azure (AKS)][aks-configure-advanced-networking].

Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Kubernetes di Azure, vedere gli articoli seguenti:

- [Kubernetes / Cluster AKS e carichi di lavoro][aks-concepts-clusters-workloads]
- [Kubernetes / Accesso e identità per AKS][aks-concepts-identity]
- [Kubernetes / Sicurezza di AKS][aks-concepts-security]
- [Kubernetes / Archiviazione in AKS][aks-concepts-storage]
- [Kubernetes / Ridimensionamento in AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-advanced-networking]: configure-advanced-networking.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md