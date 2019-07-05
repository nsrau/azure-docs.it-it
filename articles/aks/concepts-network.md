---
title: Concetti - Funzionalità di rete nel servizio Azure Kubernetes
description: Informazioni sulle funzionalità di rete nel servizio Azure Kubernetes, tra cui funzionalità di rete kubenet e Azure CNI, controller di ingresso, bilanciamento del carico e indirizzi IP statici.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: afb7acda67eb5818ace8169dc4e98fb86bdbeaa7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442010"
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

Usano nodi di [kubenet][kubenet] plug-in Kubernetes. La piattaforma Azure può creare e configurare le reti virtuali automaticamente. In alternativa, è possibile distribuire il cluster del servizio Azure Kubernetes nella subnet di una rete virtuale esistente. Anche in questo caso, solo i nodi ricevono un indirizzo IP instradabile e il numero di POD Usa NAT per comunicare con altre risorse all'esterno del cluster servizio contenitore di AZURE. Questo approccio riduce notevolmente il numero di indirizzi IP che è necessario riservare ai pod nello spazio di indirizzi della rete.

Per altre informazioni, vedere [configurazione di rete per un cluster AKS kubenet][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Funzionalità di rete Azure CNI (avanzata)

Con Azure CNI ogni pod ottiene un indirizzo IP dalla subnet in modo che vi si possa accedere direttamente. Questi indirizzi IP devono essere univoci nello spazio di indirizzi della rete e devono essere pianificati in anticipo. Ogni nodo ha un parametro di configurazione per il numero massimo di pod che supporta. Il numero equivalente di indirizzi IP per nodo viene quindi riservato anticipatamente per tale nodo. Questo approccio richiede più pianificazione, come in caso contrario, può portare a esaurimento di indirizzi IP o la necessità di ricompilare i cluster in una subnet più grande man mano che aumentano le esigenze dell'applicazione.

Usano nodi di [interfaccia di rete contenitore di Azure (CNI)][cni-networking] plug-in Kubernetes.

![Diagramma che illustra due nodi ognuno dei quali è connesso da bridge a una singola rete virtuale di Azure][advanced-networking-diagram]

Per altre informazioni, vedere [configurare Azure CNI per un cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Confrontare i modelli di rete

Kubenet sia CNI Azure forniscono la connettività di rete per i cluster di AKS. Tuttavia, esistono vantaggi e svantaggi di ognuno. A livello generale, si applicano le considerazioni seguenti:

* **kubenet**
    * Consente di conservare spazio di indirizzi IP.
    * Usa bilanciamento del carico interno o esterno di Kubernetes per raggiungere i POD dall'esterno del cluster.
    * Manualmente, è necessario gestire e mantenere la route definita dall'utente (Udr).
    * Numero massimo di 400 nodi al cluster.
* **Azure CNI**
    * I POD per usufruire di connettività di rete virtuale completo e possono essere raggiunto direttamente dall'esterno del cluster.
    * Richiede più spazio di indirizzi IP.

Le seguenti differenze di comportamento esistono tra kubenet e CNI Azure:

| Funzionalità                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Distribuire cluster nella rete virtuale nuova o esistente                                            | Supportati: route definite dall'utente applicati manualmente | Supportato |
| Connettività POD pod                                                                         | Supportato | Supportato |
| Connettività delle VM di POD; Macchina virtuale nella stessa rete virtuale                                          | Può essere usata se avviata dal pod | Funziona in entrambe le direzioni |
| Connettività delle VM di POD; Macchina virtuale nella rete virtuale con peering                                            | Può essere usata se avviata dal pod | Funziona in entrambe le direzioni |
| Accesso in locale mediante Express Route o VPN                                                | Può essere usata se avviata dal pod | Funziona in entrambe le direzioni |
| Accesso alle risorse protette tramite endpoint di servizio                                             | Supportato | Supportato |
| Esporre i servizi Kubernetes usando un controller di servizio, il Gateway dell'App o in ingresso del servizio di bilanciamento carico | Supportato | Supportato |
| DNS di Azure e le zone Private predefiniti                                                          | Supportato | Supportato |

### <a name="support-scope-between-network-models"></a>Ambito del supporto tra i modelli di rete

Indipendentemente dal modello di rete che è usare, kubenet sia CNI Azure può essere distribuito in uno dei modi seguenti:

* La piattaforma Azure può automaticamente creare e configurare le risorse di rete virtuale quando si crea un cluster AKS.
* Manualmente, è possibile creare e configurare le risorse di rete virtuale e collegare a tali risorse quando si crea il cluster AKS.

Anche se le funzionalità, ad esempio gli endpoint di servizio o route definite dall'utente sono supportate con kubenet sia CNI di Azure, il [criteri di supporto per AKS][support-policies] definiscono quali modifiche è possibile apportare. Ad esempio:

* Se si creano manualmente le risorse di rete virtuale per un cluster AKS, sono supportati durante la configurazione delle route definite dall'utente o gli endpoint di servizio personalizzati.
* Se la piattaforma Azure crea automaticamente le risorse di rete virtuale per il cluster AKS, non è supportata per modificare manualmente le risorse gestite al servizio contenitore di AZURE per configurare route definite dall'utente o gli endpoint di servizio personalizzati.

## <a name="ingress-controllers"></a>Controller di ingresso

Quando si crea un servizio di tipo LoadBalancer, viene creata una risorsa di bilanciamento del carico di Azure sottostante. Il bilanciamento del carico viene configurato per distribuire il traffico verso i pod nel servizio su una porta specifica. Il servizio di tipo LoadBalancer opera solo sul livello 4: non è a conoscenza delle applicazioni effettive e non può gestire ulteriori considerazioni sul routing.

I *controller di ingresso* operano sul livello 7 e possono usare regole più intelligenti per distribuire il traffico delle applicazioni. Un uso comune di un controller di ingresso è l'indirizzamento del traffico HTTP verso applicazioni diverse in base all'URL in ingresso.

![Diagramma che mostra il flusso del traffico in ingresso in un cluster del servizio Azure Kubernetes][aks-ingress]

Nel servizio Azure Kubernetes è possibile creare una risorsa di ingresso usando uno strumento come NGINX o la funzionalità di routing delle applicazioni HTTP del servizio Azure Kubernetes. Quando si abilita il routing delle applicazioni HTTP per un cluster servizio Azure Kubernetes, la piattaforma Azure crea il controller di ingresso e un controller *DNS esterno*. Quando vengono create le risorse di ingresso in Kubernetes, i record A DNS necessari vengono creati in una zona DNS specifica del cluster. Per altre informazioni, vedere [distribuire routing HTTP dell'applicazione][aks-http-routing].

Un'altra funzionalità comune per il traffico in ingresso è la terminazione SSL/TLS. In applicazioni Web di grandi dimensioni a cui si accede tramite HTTPS, la terminazione TLS può essere gestita dalla risorsa di ingresso invece che all'interno dell'applicazione stessa. Per garantire la generazione e la configurazione automatiche della certificazione TLS, è possibile configurare la risorsa di ingresso per l'uso di provider, ad esempio Let's Encrypt. Per altre informazioni sulla configurazione di un controller di Ingress NGINX con Let's Encrypt, vedere [traffico in ingresso e TLS][aks-ingress-tls].

È anche possibile configurare il controller di ingresso per mantenere l'IP di origine client nelle richieste per i contenitori nel cluster AKS. Quando una richiesta del client viene instradata a un contenitore nel cluster AKS tramite controller di ingresso, l'ip di origine originale di tale richiesta non sarà disponibile per il contenitore di destinazione. Quando si abilita *mantenimento dell'IP di origine client*, l'indirizzo IP di origine per il client è disponibile nell'intestazione della richiesta sotto *X-Forwarded-For*. Se si usa conservazione dell'IP di origine client nel controller di ingresso, è possibile usare pass-through SSL. Conservazione dell'IP di origine client e pass-through SSL utilizzabile con altri servizi, ad esempio la *LoadBalancer* tipo.

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Un gruppo di sicurezza di rete filtra il traffico per le macchine virtuali, ad esempio i nodi del servizio Azure Kubernetes. Quando si creano servizi, ad esempio LoadBalancer, la piattaforma Azure configura automaticamente le eventuali regole dei gruppi di sicurezza di rete necessarie. Non configurare manualmente le regole dei gruppi di sicurezza di rete per filtrare il traffico per i pod in un cluster servizio Azure Kubernetes. Definire le eventuali porte necessarie e l'inoltro come parte dei manifesti del servizio Kubernetes e delegare alla piattaforma Azure la creazione o l'aggiornamento delle regole appropriate. È anche possibile usare i criteri di rete, come descritto nella sezione successiva, per applicare automaticamente le regole di filtro del traffico al POD.

## <a name="network-policies"></a>Criteri di rete

Per impostazione predefinita, tutti i pod in un cluster del servizio Azure Kubernetes possono inviare e ricevere traffico senza limitazioni. Per garantire maggiore sicurezza, è possibile definire regole per il controllo del flusso del traffico. Le applicazioni back-end vengono spesso esposte solo ai servizi front-end richiesti oppure i componenti di database sono accessibili solo ai livelli applicazione che si connettono a essi.

Criteri di rete sono una funzionalità di Kubernetes disponibile nel servizio contenitore di AZURE che consente di controllare il flusso del traffico tra i POD. È possibile scegliere di consentire o non consentire il traffico in base a impostazioni come la porta del traffico, lo spazio dei nomi o le etichette assegnate. I gruppi di sicurezza di rete sono più adatti per i nodi del servizio Azure Kubernetes piuttosto che per i pod. L'uso di criteri di rete è un metodo nativo del cloud più appropriato per controllare il flusso del traffico. Poiché i pod vengono creati dinamicamente in un cluster del servizio Azure Kubernetes, i criteri di rete necessari possono essere applicati automaticamente.

Per altre informazioni, vedere [proteggere il traffico tra i POD usando i criteri di rete in Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Passaggi successivi

Introduzione a servizio contenitore di AZURE di rete, creare e configurare un cluster AKS con i propri intervalli di indirizzi IP usando [kubenet][aks-configure-kubenet-networking] or [Azure CNI][aks-configure-advanced-networking].

Per procedure consigliate associati, vedere [procedure consigliate per la connettività di rete e sicurezza nel servizio contenitore di AZURE][operator-best-practices-network].

Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes / cluster servizio contenitore di AZURE e i carichi di lavoro][aks-concepts-clusters-workloads]
- [Kubernetes / AKS di accesso e identità][aks-concepts-identity]
- [Kubernetes / sicurezza AKS][aks-concepts-security]
- [Kubernetes / archiviazione servizio contenitore di AZURE][aks-concepts-storage]
- [Kubernetes / scalabilità AKS][aks-concepts-scale]

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
