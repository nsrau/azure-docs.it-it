---
title: Concetti - Funzionalità di rete nel servizio Azure Kubernetes
description: Informazioni sulle funzionalità di rete nel servizio Azure Kubernetes, tra cui funzionalità di rete kubenet e Azure CNI, controller di ingresso, bilanciamento del carico e indirizzi IP statici.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 5ce3290f7af32b10e1dfbf9b72686e5d30c885bb
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431326"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes

In un approccio allo sviluppo delle applicazioni come microservizi basati su contenitori, i componenti dell'applicazione devono interagire per elaborare le proprie attività. Kubernetes offre diverse risorse che consentono queste comunicazioni tra le applicazioni. È possibile connettersi alle applicazioni ed esporle internamente o esternamente. Per sviluppare applicazioni a disponibilità elevata, è possibile bilanciare il carico delle applicazioni. Le applicazioni più complesse possono richiedere la configurazione del traffico in ingresso per la terminazione SSL/TLS o il routing di più componenti. Per motivi di sicurezza, potrebbe anche essere necessario limitare il flusso del traffico di rete all'interno o tra i pod e i nodi.

Questo articolo introduce i concetti di base correlati alle funzionalità di rete per le applicazioni nel servizio Kubernetes di Azure:

- [Servizi](#services)
- [Reti virtuali di Azure](#azure-virtual-networks)
- [Controller di ingresso](#ingress-controllers)
- [Criteri di rete](#network-policies)

## <a name="kubernetes-basics"></a>Nozioni di base di Kubernetes

Per consentire l'accesso alle applicazioni o per consentire ai componenti dell'applicazione di comunicare tra loro, Kubernetes offre un livello di astrazione per funzionalità di rete virtuale. I nodi Kubernetes vengono connessi a una rete virtuale e possono fornire la connettività in ingresso e in uscita per i pod. Il componente *kube-proxy* viene eseguito in ogni nodo per fornire queste funzionalità di rete.

In Kubernetes, i *servizi* raggruppano i pod dal punto di vista logico per consentire l'accesso diretto tramite un indirizzo IP o nome DNS e su una porta specifica. È anche possibile distribuire il traffico usando un *bilanciamento del carico*. Si può anche ottenere un routing più complesso del traffico dell'applicazione con i *controller di ingresso*. Sicurezza e il filtro del traffico di rete per POD sono possibili con Kubernetes *i criteri di rete* (in anteprima nel servizio contenitore di AZURE).

Anche la piattaforma Azure contribuisce a semplificare le funzionalità di rete virtuale per i cluster servizio Azure Kubernetes. Quando si crea un bilanciamento del carico di Kubernetes, viene creata e configurata la risorsa di bilanciamento del carico di Azure sottostante. Quando si aprono le porte di rete per i pod, vengono configurate le regole del gruppo di sicurezza di rete di Azure corrispondente. Per il routing delle applicazioni HTTP, Azure può anche configurare il *DNS esterno* quando vengono configurate nuove route in ingresso.

## <a name="services"></a>Servizi

Per semplificare la configurazione di rete per i carichi di lavoro dell'applicazione, Kubernetes usa *servizi* per raggruppare un set di pod in modo logico e fornire la connettività di rete. Sono disponibili i tipi di servizio seguenti:

- **IP del cluster** - Crea un indirizzo IP interno per l'uso all'interno del cluster AKS. Soluzione idonea per le applicazioni solo interne che supportano altri carichi di lavoro all'interno del cluster.

    ![Diagramma che mostra il flusso del traffico IP del cluster in un cluster servizio Azure Kubernetes][aks-clusterip]

- **NodePort** - Crea un mapping delle porte nel nodo sottostante che consente l'accesso diretto all'applicazione con l'indirizzo IP e la porta del nodo.

    ![Diagramma che mostra il flusso del traffico NodePort in un cluster servizio Azure Kubernetes][aks-nodeport]

- **LoadBalancer** - Crea una risorsa di bilanciamento del carico di Azure, configura un indirizzo IP esterno e connette i pod richiesti al pool back-end del bilanciamento del carico. Per consentire al traffico dei clienti di raggiungere l'applicazione, vengono create regole di bilanciamento del carico per le porte desiderate. 

    ![Diagramma che illustra il flusso del traffico di bilanciamento del carico in un cluster servizio Azure Kubernetes][aks-loadbalancer]

    Per un maggiore controllo e il routing del traffico in ingresso, è invece possibile usare un [controller di ingresso](#ingress-controllers).

- **ExternalName** - Crea una voce DNS specifica per semplificare l'accesso dell'applicazione.

L'indirizzo IP per i bilanciamenti del carico e i servizi può essere assegnato in modo dinamico oppure è possibile specificare un indirizzo IP statico esistente da usare. Si possono assegnare sia indirizzi IP statici interni che esterni. Questo indirizzo IP statico esistente è spesso associato a una voce DNS.

Si possono creare sia bilanciamenti del carico *interni* che *esterni*. Ai bilanciamenti del carico interni viene assegnato solo un indirizzo IP privato, in modo che non siano accessibili da Internet.

## <a name="azure-virtual-networks"></a>Reti virtuali di Azure

Nel servizio Azure Kubernetes è possibile distribuire un cluster che usa uno dei due modelli di rete seguenti:

- Funzionalità di rete *kubenet*: le risorse di rete vengono in genere create e configurate quando viene distribuito il cluster del servizio Azure Kubernetes.
- Funzionalità di rete *Azure Container Networking Interface (Azure CNI)* : il cluster del servizio Azure Kubernetes viene connesso alle configurazioni e alle risorse di rete virtuale esistenti.

### <a name="kubenet-basic-networking"></a>Funzionalità di rete kubenet (di base)

L'opzione per le funzionalità di rete *kubenet* è la configurazione predefinita per la creazione del cluster del servizio Azure Kubernetes. Con *kubenet* i nodi ottengono un indirizzo IP dalla subnet della rete virtuale di Azure. I pod ricevono un indirizzo IP da uno spazio di indirizzi diverso dal punto di vista logico nella subnet della rete virtuale di Azure dei nodi. La funzionalità Network Address Translation (NAT) viene quindi configurata in modo che i pod possano raggiungere le risorse nella rete virtuale di Azure. L'indirizzo IP di origine del traffico viene convertito tramite NAT nell'indirizzo IP primario del nodo.

I nodi usano il plug-in [kubenet][kubenet] di Kubernetes. La piattaforma Azure può creare e configurare le reti virtuali automaticamente. In alternativa, è possibile distribuire il cluster del servizio Azure Kubernetes nella subnet di una rete virtuale esistente. Anche in questo caso, solo i nodi ricevono un indirizzo IP instradabile e il numero di POD Usa NAT per comunicare con altre risorse all'esterno del cluster servizio contenitore di AZURE. Questo approccio riduce notevolmente il numero di indirizzi IP che è necessario riservare ai pod nello spazio di indirizzi della rete.

Per altre informazioni, vedere [Configurare funzionalità di rete kubenet per un cluster del servizio Azure Kubernetes][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Funzionalità di rete Azure CNI (avanzata)

Con Azure CNI ogni pod ottiene un indirizzo IP dalla subnet in modo che vi si possa accedere direttamente. Questi indirizzi IP devono essere univoci nello spazio di indirizzi della rete e devono essere pianificati in anticipo. Ogni nodo ha un parametro di configurazione per il numero massimo di pod che supporta. Il numero equivalente di indirizzi IP per nodo viene quindi riservato anticipatamente per tale nodo. Questo approccio richiede una maggiore pianificazione e spesso conduce all'esaurimento degli indirizzi IP o alla necessità di riconfigurare i cluster in una subnet di dimensioni maggiori man mano che aumentano le richieste dell'applicazione.

I nodi usano il plug-in [Azure Container Networking Interface (CNI)][cni-networking] di Kubernetes.

![Diagramma che illustra due nodi ognuno dei quali è connesso da bridge a una singola rete virtuale di Azure][advanced-networking-diagram]

Azure CNI offre le funzionalità seguenti rispetto a kubenet:

- A ogni pod nel cluster viene assegnato un indirizzo IP nella rete virtuale. I pod possono comunicare direttamente con altri pod nel cluster e altri nodi nella rete virtuale.
- I pod in una subnet con endpoint di servizio abilitati possono connettersi in modo sicuro ai servizi di Azure, ad esempio Archiviazione di Azure e il database SQL.
- È possibile creare route definite dall'utente per instradare il traffico dai pod a un'appliance virtuale di rete.

Per altre informazioni, vedere [Configurare Azure CNI in un cluster del servizio Azure Kubernetes][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Controller di ingresso

Quando si crea un servizio di tipo LoadBalancer, viene creata una risorsa di bilanciamento del carico di Azure sottostante. Il bilanciamento del carico viene configurato per distribuire il traffico verso i pod nel servizio su una porta specifica. Il servizio di tipo LoadBalancer opera solo sul livello 4: non è a conoscenza delle applicazioni effettive e non può gestire ulteriori considerazioni sul routing.

I *controller di ingresso* operano sul livello 7 e possono usare regole più intelligenti per distribuire il traffico delle applicazioni. Un uso comune di un controller di ingresso è l'indirizzamento del traffico HTTP verso applicazioni diverse in base all'URL in ingresso.

![Diagramma che mostra il flusso del traffico in ingresso in un cluster del servizio Azure Kubernetes][aks-ingress]

Nel servizio Azure Kubernetes è possibile creare una risorsa di ingresso usando uno strumento come NGINX o la funzionalità di routing delle applicazioni HTTP del servizio Azure Kubernetes. Quando si abilita il routing delle applicazioni HTTP per un cluster servizio Azure Kubernetes, la piattaforma Azure crea il controller di ingresso e un controller *DNS esterno*. Quando vengono create le risorse di ingresso in Kubernetes, i record A DNS necessari vengono creati in una zona DNS specifica del cluster. Per altre informazioni, vedere [Routing di applicazioni HTTP][aks-http-routing].

Un'altra funzionalità comune per il traffico in ingresso è la terminazione SSL/TLS. In applicazioni Web di grandi dimensioni a cui si accede tramite HTTPS, la terminazione TLS può essere gestita dalla risorsa di ingresso invece che all'interno dell'applicazione stessa. Per garantire la generazione e la configurazione automatiche della certificazione TLS, è possibile configurare la risorsa di ingresso per l'uso di provider, ad esempio Let's Encrypt. Per altre informazioni sulla configurazione di un controller di ingresso NGINX con Let's Encrypt, vedere [Traffico in ingresso e TLS][aks-ingress-tls].

È anche possibile configurare il controller di ingresso per mantenere l'IP di origine client nelle richieste per i contenitori nel cluster AKS. Quando una richiesta del client viene instradata a un contenitore nel cluster AKS tramite controller di ingresso, l'ip di origine originale di tale richiesta non sarà disponibile per il contenitore di destinazione. Quando si abilita *mantenimento dell'IP di origine client*, l'indirizzo IP di origine per il client è disponibile nell'intestazione della richiesta sotto *X-Forwarded-For*. Se si usa conservazione dell'IP di origine client nel controller di ingresso, è possibile usare pass-through SSL. Conservazione dell'IP di origine client e pass-through SSL utilizzabile con altri servizi, ad esempio la *LoadBalancer* tipo.

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Un gruppo di sicurezza di rete filtra il traffico per le macchine virtuali, ad esempio i nodi del servizio Azure Kubernetes. Quando si creano servizi, ad esempio LoadBalancer, la piattaforma Azure configura automaticamente le eventuali regole dei gruppi di sicurezza di rete necessarie. Non configurare manualmente le regole dei gruppi di sicurezza di rete per filtrare il traffico per i pod in un cluster servizio Azure Kubernetes. Definire le eventuali porte necessarie e l'inoltro come parte dei manifesti del servizio Kubernetes e delegare alla piattaforma Azure la creazione o l'aggiornamento delle regole appropriate. È anche possibile usare i criteri di rete, come descritto nella sezione successiva, per applicare automaticamente le regole di filtro del traffico al POD.

## <a name="network-policies"></a>Criteri di rete

Per impostazione predefinita, tutti i pod in un cluster del servizio Azure Kubernetes possono inviare e ricevere traffico senza limitazioni. Per garantire maggiore sicurezza, è possibile definire regole per il controllo del flusso del traffico. Le applicazioni back-end vengono spesso esposte solo ai servizi front-end richiesti oppure i componenti di database sono accessibili solo ai livelli applicazione che si connettono a essi.

Criteri di rete sono una funzionalità di Kubernetes disponibile nel servizio contenitore di AZURE che consente di controllare il flusso del traffico tra i POD. È possibile scegliere di consentire o non consentire il traffico in base a impostazioni come la porta del traffico, lo spazio dei nomi o le etichette assegnate. I gruppi di sicurezza di rete sono più adatti per i nodi del servizio Azure Kubernetes piuttosto che per i pod. L'uso di criteri di rete è un metodo nativo del cloud più appropriato per controllare il flusso del traffico. Poiché i pod vengono creati dinamicamente in un cluster del servizio Azure Kubernetes, i criteri di rete necessari possono essere applicati automaticamente.

Per altre informazioni, vedere [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes][use-network-policies].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare le funzionalità di rete del servizio Azure Kubernetes, creare e configurare un cluster del servizio Azure Kubernetes con gli intervalli di indirizzi IP esistenti usando [kubenet][aks-configure-kubenet-networking] o [Azure CNI][aks-configure-advanced-networking].

Per procedure consigliate associati, vedere [procedure consigliate per la connettività di rete e sicurezza nel servizio contenitore di AZURE][operator-best-practices-network].

Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Azure Kubernetes, vedere gli articoli seguenti:

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
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
