---
title: Concetti - Funzionalità di rete nel servizio Azure Kubernetes
description: Informazioni sulle funzionalità di rete nel servizio Azure Kubernetes, tra cui funzionalità di rete kubenet e Azure CNI, controller di ingresso, bilanciamento del carico e indirizzi IP statici.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 429205d1df91b5a63679d1189903e5340ab837f8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913890"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes

In un approccio allo sviluppo delle applicazioni come microservizi basati su contenitori, i componenti dell'applicazione devono interagire per elaborare le proprie attività. Kubernetes offre diverse risorse che consentono queste comunicazioni tra le applicazioni. È possibile connettersi alle applicazioni ed esporle internamente o esternamente. Per sviluppare applicazioni a disponibilità elevata, è possibile bilanciare il carico delle applicazioni. Le applicazioni più complesse possono richiedere la configurazione del traffico in ingresso per la terminazione SSL/TLS o il routing di più componenti. Per motivi di sicurezza, potrebbe anche essere necessario limitare il flusso del traffico di rete all'interno o tra i pod e i nodi.

Questo articolo introduce i concetti di base correlati alle funzionalità di rete per le applicazioni nel servizio Azure Kubernetes:

- [Servizi](#services)
- [Reti virtuali di Azure](#azure-virtual-networks)
- [Controller di ingresso](#ingress-controllers)
- [Criteri di rete](#network-policies)

## <a name="kubernetes-basics"></a>Nozioni di base di Kubernetes

Per consentire l'accesso alle applicazioni o per consentire ai componenti dell'applicazione di comunicare tra loro, Kubernetes offre un livello di astrazione per funzionalità di rete virtuale. I nodi Kubernetes vengono connessi a una rete virtuale e possono fornire la connettività in ingresso e in uscita per i pod. Il componente *kube-proxy* viene eseguito in ogni nodo per fornire queste funzionalità di rete.

In Kubernetes, i *servizi* raggruppano i pod dal punto di vista logico per consentire l'accesso diretto tramite un indirizzo IP o nome DNS e su una porta specifica. È anche possibile distribuire il traffico usando un *bilanciamento del carico*. Si può anche ottenere un routing più complesso del traffico dell'applicazione con i *controller di ingresso*. La sicurezza e il filtraggio del traffico di rete per i pod sono possibili con i *criteri di rete* di Kubernetes.

Anche la piattaforma Azure contribuisce a semplificare le funzionalità di rete virtuale per i cluster servizio Azure Kubernetes. Quando si crea un bilanciamento del carico di Kubernetes, viene creata e configurata la risorsa di bilanciamento del carico di Azure sottostante. Quando si aprono le porte di rete per i pod, vengono configurate le regole del gruppo di sicurezza di rete di Azure corrispondente. Per il routing delle applicazioni HTTP, Azure può anche configurare il *DNS esterno* quando vengono configurate nuove route in ingresso.

## <a name="services"></a>Services

Per semplificare la configurazione di rete per i carichi di lavoro dell'applicazione, Kubernetes usa *servizi* per raggruppare un set di pod in modo logico e fornire la connettività di rete. Sono disponibili i tipi di servizio seguenti:

- **IP del cluster** - Crea un indirizzo IP interno per l'uso all'interno del cluster del servizio Azure Kubernetes. Soluzione idonea per le applicazioni solo interne che supportano altri carichi di lavoro all'interno del cluster.

    ![Diagramma che mostra il flusso del traffico IP del cluster in un cluster servizio Azure Kubernetes][aks-clusterip]

- **NodePort** - Crea un mapping delle porte nel nodo sottostante che consente l'accesso diretto all'applicazione con l'indirizzo IP e la porta del nodo.

    ![Diagramma che mostra il flusso del traffico NodePort in un cluster servizio Azure Kubernetes][aks-nodeport]

- **LoadBalancer** - Crea una risorsa di bilanciamento del carico di Azure, configura un indirizzo IP esterno e connette i pod richiesti al pool back-end del bilanciamento del carico. Per consentire al traffico dei clienti di raggiungere l'applicazione, vengono create regole di bilanciamento del carico per le porte desiderate. 

    ![Diagramma che illustra il flusso del traffico di bilanciamento del carico in un cluster servizio Azure Kubernetes][aks-loadbalancer]

    Per un maggiore controllo e il routing del traffico in ingresso, è invece possibile usare un [controller di ingresso](#ingress-controllers).

- **ExternalName** - Crea una voce DNS specifica per semplificare l'accesso dell'applicazione.

L'indirizzo IP per i bilanciamenti del carico e i servizi può essere assegnato in modo dinamico oppure è possibile specificare un indirizzo IP statico esistente da usare. Si possono assegnare sia indirizzi IP statici interni che esterni. Questo indirizzo IP statico esistente è spesso associato a una voce DNS.

Si possono creare sia bilanciamenti del carico *interni* che *esterni*. Al servizio di bilanciamento del carico interno viene assegnato solo un indirizzo IP privato, pertanto non è possibile accedervi da Internet.

## <a name="azure-virtual-networks"></a>Reti virtuali di Azure

Nel servizio Azure Kubernetes è possibile distribuire un cluster che usa uno dei due modelli di rete seguenti:

- Funzionalità di rete *kubenet*: le risorse di rete vengono in genere create e configurate quando viene distribuito il cluster del servizio Azure Kubernetes.
- Funzionalità di rete *Azure Container Networking Interface (Azure CNI)* : il cluster del servizio Azure Kubernetes viene connesso alle configurazioni e alle risorse di rete virtuale esistenti.

### <a name="kubenet-basic-networking"></a>Funzionalità di rete kubenet (di base)

L'opzione per le funzionalità di rete *kubenet* è la configurazione predefinita per la creazione del cluster del servizio Azure Kubernetes. Con *kubenet* i nodi ottengono un indirizzo IP dalla subnet della rete virtuale di Azure. I pod ricevono un indirizzo IP da uno spazio di indirizzi diverso dal punto di vista logico nella subnet della rete virtuale di Azure dei nodi. La funzionalità Network Address Translation (NAT) viene quindi configurata in modo che i pod possano raggiungere le risorse nella rete virtuale di Azure. L'indirizzo IP di origine del traffico viene convertito tramite NAT nell'indirizzo IP primario del nodo.

I nodi usano il plug-in [kubenet][kubenet] Kubernetes. La piattaforma Azure può creare e configurare le reti virtuali automaticamente. In alternativa, è possibile distribuire il cluster del servizio Azure Kubernetes nella subnet di una rete virtuale esistente. Anche in questo caso, solo i nodi ricevono un indirizzo IP instradabile e i pod usano NAT per comunicare con altre risorse all'esterno del cluster AKS. Questo approccio riduce notevolmente il numero di indirizzi IP che è necessario riservare ai pod nello spazio degli indirizzi della rete.

Per altre informazioni, vedere [configurare la rete kubenet per un cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Funzionalità di rete Azure CNI (avanzata)

Con Azure CNI ogni pod ottiene un indirizzo IP dalla subnet in modo che vi si possa accedere direttamente. Questi indirizzi IP devono essere univoci nello spazio degli indirizzi della rete e devono essere pianificati in anticipo. Ogni nodo ha un parametro di configurazione per il numero massimo di pod che supporta. Il numero equivalente di indirizzi IP per nodo viene quindi riservato anticipatamente per tale nodo. Questo approccio richiede una pianificazione più approfondita, in quanto può compromettere l'esaurimento degli indirizzi IP o la necessità di ricompilare i cluster in una subnet più ampia in caso di aumento delle richieste dell'applicazione.

I nodi usano il plug-in Kubernetes [(CNI) di Azure container Network Interface][cni-networking] .

![Diagramma che illustra due nodi ognuno dei quali è connesso da bridge a una singola rete virtuale di Azure][advanced-networking-diagram]

Per altre informazioni, vedere [configurare Azure CNI per un cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Confrontare i modelli di rete

Sia kubenet che Azure CNI forniscono la connettività di rete per i cluster AKS. Esistono tuttavia vantaggi e svantaggi. A un livello elevato, si applicano le considerazioni seguenti:

* **kubenet**
    * Conserva lo spazio degli indirizzi IP.
    * Usa il servizio di bilanciamento del carico interno o esterno di Kubernetes per raggiungere i pod dall'esterno del cluster.
    * È necessario gestire e gestire manualmente le route definite dall'utente (UDR).
    * Massimo 400 nodi per cluster.
* **Azure CNI**
    * I pod ottengono la connettività di rete virtuale completa e possono essere raggiunti direttamente tramite il proprio indirizzo IP privato dalle reti connesse.
    * Richiede più spazio di indirizzi IP.

Tra kubenet e Azure CNI sono presenti le differenze di comportamento seguenti:

| Funzionalità                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Distribuire il cluster in una rete virtuale nuova o esistente                                            | Supportato-UdR applicato manualmente | Supportato |
| Connettività Pod-Pod                                                                         | Supportato | Supportato |
| Connettività Pod-VM; VM nella stessa rete virtuale                                          | Funziona quando avviato da Pod | Funziona in entrambi i modi |
| Connettività Pod-VM; VM in una rete virtuale con peering                                            | Funziona quando avviato da Pod | Funziona in entrambi i modi |
| Accesso locale tramite VPN o Express Route                                                | Funziona quando avviato da Pod | Funziona in entrambi i modi |
| Accesso alle risorse protette dagli endpoint del servizio                                             | Supportato | Supportato |
| Esporre i servizi Kubernetes usando un servizio di bilanciamento del carico, un gateway app o un controller di ingresso | Supportato | Supportato |
| DNS di Azure e zone private predefinite                                                          | Supportato | Supportato |

### <a name="support-scope-between-network-models"></a>Ambito di supporto tra i modelli di rete

Indipendentemente dal modello di rete usato, sia kubenet che Azure CNI possono essere distribuiti in uno dei modi seguenti:

* La piattaforma Azure può creare e configurare automaticamente le risorse della rete virtuale quando si crea un cluster AKS.
* Quando si crea il cluster AKS, è possibile creare e configurare manualmente le risorse della rete virtuale e connetterle a tali risorse.

Sebbene le funzionalità come gli endpoint di servizio o UdR siano supportate sia con kubenet che con Azure CNI, i [criteri di supporto per AKS][support-policies] definiscono le modifiche che è possibile apportare. ad esempio:

* Se si creano manualmente le risorse della rete virtuale per un cluster AKS, si è supportati quando si configurano gli endpoint di servizio o UdR personalizzati.
* Se la piattaforma Azure crea automaticamente le risorse di rete virtuale per il cluster del servizio contenitore di Azure, non è supportata la modifica manuale delle risorse gestite da AKS per configurare UdR o endpoint di servizio personalizzati.

## <a name="ingress-controllers"></a>Controller di ingresso

Quando si crea un servizio di tipo LoadBalancer, viene creata una risorsa di bilanciamento del carico di Azure sottostante. Il bilanciamento del carico viene configurato per distribuire il traffico verso i pod nel servizio su una porta specifica. Il servizio di tipo LoadBalancer opera solo sul livello 4: non è a conoscenza delle applicazioni effettive e non può gestire ulteriori considerazioni sul routing.

I *controller di ingresso* operano sul livello 7 e possono usare regole più intelligenti per distribuire il traffico delle applicazioni. Un uso comune di un controller di ingresso è l'indirizzamento del traffico HTTP verso applicazioni diverse in base all'URL in ingresso.

![Diagramma che mostra il flusso del traffico in ingresso in un cluster del servizio Azure Kubernetes][aks-ingress]

Nel servizio Azure Kubernetes è possibile creare una risorsa di ingresso usando uno strumento come NGINX o la funzionalità di routing delle applicazioni HTTP del servizio Azure Kubernetes. Quando si abilita il routing delle applicazioni HTTP per un cluster servizio Azure Kubernetes, la piattaforma Azure crea il controller di ingresso e un controller *DNS esterno*. Quando vengono create le risorse di ingresso in Kubernetes, i record A DNS necessari vengono creati in una zona DNS specifica del cluster. Per altre informazioni, vedere [deploy http Application routing][aks-http-routing].

Un'altra funzionalità comune per il traffico in ingresso è la terminazione SSL/TLS. In applicazioni Web di grandi dimensioni a cui si accede tramite HTTPS, la terminazione TLS può essere gestita dalla risorsa di ingresso invece che all'interno dell'applicazione stessa. Per garantire la generazione e la configurazione automatiche della certificazione TLS, è possibile configurare la risorsa di ingresso per l'uso di provider, ad esempio Let's Encrypt. Per altre informazioni sulla configurazione di un controller di ingresso NGINX con la crittografia, vedere [ingresso e TLS][aks-ingress-tls].

È anche possibile configurare il controller di ingresso per mantenere l'IP di origine del client nelle richieste ai contenitori nel cluster AKS. Quando la richiesta di un client viene indirizzata a un contenitore nel cluster AKS tramite il controller di ingresso, l'indirizzo IP di origine della richiesta non sarà disponibile per il contenitore di destinazione. Quando si Abilita la *conservazione dell'indirizzo IP di origine client*, l'indirizzo IP di origine per il client è disponibile nell'intestazione della richiesta in *X-inoltred-for*. Se si usa la conservazione dell'indirizzo IP di origine client sul controller di ingresso, non è possibile usare il pass-through SSL. La conservazione IP di origine client e il pass-through SSL possono essere usati con altri servizi, ad esempio il tipo *LoadBalancer* .

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Un gruppo di sicurezza di rete filtra il traffico per le macchine virtuali, ad esempio i nodi del servizio Azure Kubernetes. Quando si creano servizi, ad esempio LoadBalancer, la piattaforma Azure configura automaticamente le eventuali regole dei gruppi di sicurezza di rete necessarie. Non configurare manualmente le regole dei gruppi di sicurezza di rete per filtrare il traffico per i pod in un cluster servizio Azure Kubernetes. Definire le eventuali porte necessarie e l'inoltro come parte dei manifesti del servizio Kubernetes e delegare alla piattaforma Azure la creazione o l'aggiornamento delle regole appropriate. È anche possibile usare i criteri di rete, come illustrato nella sezione successiva, per applicare automaticamente le regole di filtro del traffico ai pod.

## <a name="network-policies"></a>Criteri di rete

Per impostazione predefinita, tutti i pod in un cluster del servizio Azure Kubernetes possono inviare e ricevere traffico senza limitazioni. Per garantire maggiore sicurezza, è possibile definire regole per il controllo del flusso del traffico. Le applicazioni back-end vengono spesso esposte solo ai servizi front-end richiesti oppure i componenti di database sono accessibili solo ai livelli applicazione che si connettono a essi.

Criteri di rete è una funzionalità di Kubernetes disponibile in AKS che consente di controllare il flusso del traffico tra i pod. È possibile scegliere di consentire o non consentire il traffico in base a impostazioni come la porta del traffico, lo spazio dei nomi o le etichette assegnate. I gruppi di sicurezza di rete sono più adatti per i nodi del servizio Azure Kubernetes piuttosto che per i pod. L'uso di criteri di rete è un metodo nativo del cloud più appropriato per controllare il flusso del traffico. Poiché i pod vengono creati dinamicamente in un cluster del servizio Azure Kubernetes, i criteri di rete necessari possono essere applicati automaticamente.

Per altre informazioni, vedere [proteggere il traffico tra i pod usando i criteri di rete in Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare la rete AKS, creare e configurare un cluster del servizio contenitore di Azure con gli intervalli di indirizzi IP usando [kubenet][aks-configure-kubenet-networking] o [Azure CNI][aks-configure-advanced-networking].

Per le procedure consigliate associate, vedere procedure consigliate [per la connettività di rete e la sicurezza in AKS][operator-best-practices-network].

Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Cluster e carichi di lavoro Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identità e accesso Kubernetes/AKS][aks-concepts-identity]
- [Sicurezza di Kubernetes/AKS][aks-concepts-security]
- [Archiviazione Kubernetes/AKS][aks-concepts-storage]
- [Scala Kubernetes/AKS][aks-concepts-scale]

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
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
