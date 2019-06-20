---
title: Procedure consigliate per l'operatore - Connettività di rete nel servizio Azure Kubernetes
description: Informazioni sulle procedure consigliate per l'operatore del cluster per la connettività e le risorse di rete virtuale nel servizio Azure Kubernetes
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: iainfou
ms.openlocfilehash: 2bdc18ba4dc77178d5fcc5d2ba6d89aa109d923c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "65192229"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la sicurezza e la connettività di rete nel servizio Azure Kubernetes

Quando si creano e si gestiscono cluster nel servizio Azure Kubernetes, viene fornita la connettività di rete per nodi e applicazioni. Queste risorse di rete includono gli intervalli di indirizzi IP, i servizi di bilanciamento del carico e i controller in ingresso. Per garantire un'elevata qualità del servizio per le applicazioni, è necessario pianificare e configurare queste risorse.

Questo articolo sulle procedure consigliate è incentrato sulla sicurezza e la connettività di rete per gli operatori del cluster. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Confrontare le modalità di rete kubenet e Azure CNI nel servizio Azure Kubernetes
> * Pianificare la connettività e gli indirizzi IP necessari
> * Distribuire il traffico usando servizi di bilanciamento del carico, controller di ingresso o web application firewall (WAF)
> * Connettersi in modo sicuro ai nodi del cluster

## <a name="choose-the-appropriate-network-model"></a>Scegliere il modello di rete appropriato

**Suggerimento per la procedura consigliata**: per consentire l'integrazione con le reti locali o con le reti virtuali esistenti, usare le funzionalità di rete Azure CNI nel servizio Azure Kubernetes. Questo modello di rete consente inoltre una maggiore separazione delle risorse e dei controlli in un ambiente aziendale.

Le reti virtuali forniscono la connettività di base per consentire ai clienti e ai nodi del servizio Azure Kubernetes di accedere alle applicazioni. Esistono due diversi modi per distribuire i cluster del servizio Azure Kubernetes nelle reti virtuali:

* **Funzionalità di rete kubenet**: Azure gestisce le risorse di rete virtuale durante la distribuzione del cluster e usa il plug-in [kubenet][kubenet] di Kubernetes.
* **Funzionalità di rete Azure CNI**: la distribuzione avviene in una rete virtuale esistente e viene usato il plug-in [Azure Container Networking Interface (CNI)][cni-networking] di Kubernetes. Ai pod vengono assegnati IP singoli che possono essere instradati ad altri servizi di rete o a risorse locali.

Container Networking Interface (CNI) è un protocollo indipendente dal fornitore che consente al runtime del contenitore di indirizzare le richieste a un provider di rete. Azure CNI assegna gli indirizzi IP ai pod e ai nodi e offre funzionalità di gestione degli indirizzi IP (IPAM) durante la connessione a reti virtuali Azure esistenti. A ogni risorsa di nodi e pod viene assegnato un indirizzo IP nella rete virtuale di Azure e non è necessario alcun routing aggiuntivo per comunicare con altri servizi o risorse.

![Diagramma che illustra due nodi ognuno dei quali è connesso da bridge a una singola rete virtuale di Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

Per la maggior parte delle distribuzioni di produzione, è consigliabile usare le funzionalità di rete Azure CNI. Questo modello di rete consente di separare il controllo e la gestione delle risorse. Dal punto di vista della sicurezza, è spesso preferibile che siano team diversi a gestire e proteggere tali risorse. Le funzionalità di rete Azure CNI consentono di connettersi a risorse Azure esistenti, a risorse locali o ad altri servizi direttamente tramite gli indirizzi IP assegnati a ogni pod.

Quando si usano le funzionalità di rete Azure CNI, la risorsa di rete virtuale si trova in un gruppo di risorse separato rispetto al cluster del servizio Azure Kubernetes. Delegare le autorizzazioni per consentire all'entità servizio del servizio Azure Kubernetes di accedere e gestire queste risorse. L'entità servizio usata dal cluster servizio Azure Kubernetes deve avere almeno autorizzazioni di [Collaboratore di rete](../role-based-access-control/built-in-roles.md#network-contributor) per la subnet all'interno della rete virtuale. Se si vuole definire un [ruolo personalizzato](../role-based-access-control/custom-roles.md) invece di usare il ruolo predefinito Collaboratore di rete, sono necessarie le autorizzazioni seguenti:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Per altre informazioni sulla delega dell'entità servizio del servizio Azure Kubernetes, vedere [Delegare l'accesso ad altre risorse di Azure][sp-delegation].

Dal momento che a ogni nodo e a ogni pod è assegnato un indirizzo IP, pianificare gli intervalli di indirizzi per le subnet del servizio Azure Kubernetes. Le dimensioni della subnet devono essere tali da fornire gli indirizzi IP per tutti i nodi, pod e risorse di rete che vengono distribuiti. Ogni cluster del servizio Azure Kubernetes deve essere inserito nella relativa subnet. Per consentire la connettività a reti locali o associate in Azure, non usare intervalli di indirizzi IP che si sovrappongono alle risorse di rete esistenti. Sono previsti limiti predefiniti per il numero di pod utilizzabili con ogni nodo sia con le funzionalità di rete kubenet che con quelle Azure CNI. Per gestire eventi di aumento o aggiornamenti del cluster, sono necessari anche indirizzi IP aggiuntivi disponibili per l'uso nella subnet assegnata. Questo spazio indirizzi aggiuntivo è particolarmente importante se si usano contenitori di Windows Server (attualmente in anteprima nel servizio contenitore di AZURE), come i pool di nodi, richiedono un aggiornamento per applicare le patch di sicurezza più recenti. Per altre informazioni sui nodi di Windows Server, vedere [esegue l'aggiornamento di un pool di nodi in AKS][nodepool-upgrade].

Per calcolare l'indirizzo IP richiesto, vedere [Configure Azure CNI networking in Azure Kubernetes Service (AKS)][advanced-networking] (Configurare le funzionalità di rete Azure CNI nel servizio Azure Kubernetes).

### <a name="kubenet-networking"></a>Funzionalità di rete kubenet

Anche se con kubenet non è necessario configurare le reti virtuali prima della distribuzione del cluster, questo metodo presenta alcuni svantaggi:

* Nodi e pod vengono inseriti in subnet IP diverse. Per instradare il traffico tra pod e nodi, si usano il routing definito dall'utente e l'inoltro IP. Questo routing aggiuntivo può comportare una riduzione delle prestazioni di rete.
* Le connessioni alle reti locali esistenti o le operazioni di peering con altre reti virtuali di Azure possono risultare complesse.

Kubenet è adatto per carichi di lavoro di sviluppo o di test di dimensioni ridotte, in quanto non è necessario creare la rete virtuale e le subnet separatamente rispetto al cluster del servizio Azure Kubernetes. Anche i siti Web semplici con traffico ridotto o il trasferimento in modalità lift-and-shift dei carichi di lavoro nei contenitori possono sfruttare la semplicità dei cluster del servizio Azure Kubernetes distribuiti con le funzionalità di rete kubenet. Per la maggior parte delle distribuzioni di produzione, è consigliabile effettuare la pianificazione e usare le funzionalità di rete Azure CNI. È anche possibile [configurare gli intervalli di indirizzi IP e le reti virtuali usando kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuire il traffico in ingresso

**Suggerimento per la procedura consigliata**: per distribuire il traffico HTTP o HTTPS alle applicazioni, usare controller e risorse in ingresso. I controller in ingresso offrono funzionalità aggiuntive rispetto a un normale servizio di bilanciamento del carico di Azure e possono essere gestiti come risorse native di Kubernetes.

Un servizio di bilanciamento del carico di Azure può distribuire il traffico dei clienti alle applicazioni del cluster del servizio Azure Kubernetes, ma è limitato a quanto riconosce di tale traffico. Una risorsa di bilanciamento del carico funziona al livello 4 e distribuisce il traffico in base al protocollo o alle porte. La maggior parte delle applicazioni Web che usano HTTP o HTTPS deve usare controller e risorse in ingresso Kubernetes, che funzionano al livello 7. I controller e le risorse in ingresso possono distribuire il traffico in base all'URL dell'applicazione e gestire la terminazione TLS/SSL. Questa funzionalità consente anche di ridurre il numero di indirizzi IP che vengono esposti e di cui si esegue il mapping. Con un servizio di bilanciamento del carico, ogni applicazione necessita in genere di un indirizzo IP pubblico che è stato assegnato e di cui viene eseguito il mapping al servizio nel cluster del servizio Azure Kubernetes. Con una risorsa in ingresso, un singolo indirizzo IP può distribuire il traffico a più applicazioni.

![Diagramma che mostra il flusso del traffico in ingresso in un cluster del servizio Azure Kubernetes](media/operator-best-practices-network/aks-ingress.png)

 Esistono due componenti per l'ingresso:

 * Una *risorsa* in ingresso e
 * Un *controller* in ingresso

La risorsa in ingresso è un manifesto YAML di `kind: Ingress` che definisce l'host, i certificati e le regole per instradare il traffico ai servizi che vengono eseguiti nel cluster del servizio Azure Kubernetes. L'esempio di manifesto YAML seguente consente di distribuire il traffico per *myapp.com* a uno dei due servizi, ovvero *blog* o *storeservice*. Il cliente viene indirizzato all'uno o all'altro servizio in base all'URL a cui accede.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Un controller di ingresso è un daemon che viene eseguito in un nodo del servizio Azure Kubernetes e controlla le richieste in arrivo. Il traffico viene quindi distribuito in base alle regole definite nella risorsa in ingresso. Il controller in ingresso più comune è basato su [NGINX]. Il servizio Azure Kubernetes non limita l'uso a uno specifico controller, quindi è possibile usare altri controller come [Contour][contour], [HAProxy][haproxy] o [Traefik][traefik].

Controller di ingresso deve essere pianificato in un nodo Linux. I nodi di Windows Server (attualmente in anteprima nel servizio contenitore di AZURE) non devono eseguire il controller di ingresso. Usare un selettore di nodo nel manifesto YAML o distribuzione grafico Helm per indicare che la risorsa deve essere eseguito in un nodo basato su Linux. Per altre informazioni, vedere [usare i selettori di nodo di controllo in cui i POD vengono programmati in AKS][concepts-node-selectors].

Esistono molti scenari per l'ingresso, tra cui le guide pratiche seguenti:

* [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
* [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
* [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
* Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS [con un indirizzo IP pubblico dinamico][aks-ingress-tls] o [con un indirizzo IP pubblico statico][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Proteggere il traffico con un web application firewall (WAF)

**Suggerimento per la procedura consigliata**: per analizzare il traffico in entrata alla ricerca di potenziali attacchi, usare un web application firewall (WAF) come [WAF Barracuda per Azure][barracuda-waf] o il gateway applicazione di Azure. Queste risorse di rete più avanzate possono anche instradare il traffico oltre le sole connessioni HTTP e HTTPS o la terminazione SSL di base.

Un controller in ingresso che distribuisce il traffico ai servizi e alle applicazioni è in genere una risorsa Kubernetes nel cluster del servizio Azure Kubernetes. Il controller viene eseguito come daemon in un nodo del servizio Azure Kubernetes e consuma alcune delle risorse del nodo come CPU, memoria e larghezza di banda di rete. In ambienti più grandi spesso si preferisce scaricare parte di questo routing del traffico o di questa terminazione TLS a una risorsa di rete all'esterno del cluster del servizio Azure Kubernetes. Si intende anche analizzare il traffico in entrata alla ricerca di potenziali attacchi.

![Un web application firewall (WAF), come il gateway applicazione di Azure, può proteggere e distribuire il traffico per il cluster del servizio Azure Kubernetes.](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Un web application firewall (WAF) garantisce un ulteriore livello di sicurezza grazie al filtro del traffico in entrata. OWASP (Open Web Application Security Project) fornisce un insieme di regole da seguire in caso di attacchi come il Cross Site Scripting o la cookie poisoning. [Gateway applicazione di Azure] [ app-gateway] (attualmente in anteprima nel servizio contenitore di AZURE) è un WAF in grado di integrarsi con i cluster servizio contenitore di AZURE per fornire queste funzionalità di sicurezza, prima che il traffico raggiunge le applicazioni e cluster AKS. Anche altre soluzioni di terze parti svolgono queste funzioni ed è quindi possibile continuare a sfruttare gli investimenti esistenti o le competenze già acquisite per un determinato prodotto.

Le risorse in ingresso e il servizio di bilanciamento del carico continuano a essere eseguiti nel cluster del servizio Azure Kubernetes per perfezionare ulteriormente la distribuzione del traffico. Il gateway applicazione può essere gestito centralmente come controller in ingresso con una definizione delle risorse. Per iniziare, [creare un controller in ingresso del gateway applicazione][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Controllare il flusso del traffico con criteri di rete

**Suggerimento per la procedura consigliata**: usare criteri di rete per consentire o meno il traffico verso i pod. Per impostazione predefinita, è consentito tutto il traffico tra i pod in un cluster. Per garantire maggiore sicurezza, definire regole che limitino la comunicazione dei pod.

I criteri di rete sono una funzionalità Kubernetes che consente di controllare il flusso del traffico tra pod. È possibile scegliere di consentire o non consentire il traffico in base a impostazioni come la porta del traffico, lo spazio dei nomi o le etichette assegnate. L'uso di criteri di rete rappresenta un metodo nativo del cloud per controllare il flusso del traffico. Poiché i pod vengono creati dinamicamente in un cluster del servizio Azure Kubernetes, i criteri di rete necessari possono essere applicati automaticamente. Usare tali criteri, anziché gruppi di sicurezza di rete di Azure, per controllare il traffico da pod a pod.

Per usare criteri di rete, è necessario che la funzionalità sia abilitata quando si crea un cluster del servizio Azure Kubernetes. Non è possibile abilitare criteri di rete in un cluster esistente di tale servizio. Pianificare in anticipo per essere certi di abilitare i criteri di rete nei cluster e poterli quindi usare in base alle esigenze. Criteri di rete devono essere utilizzato solo per i nodi basati su Linux e i POD nel servizio contenitore di AZURE.

I criteri di rete vengono creati come una risorsa Kubernetes con un manifesto YAML. I criteri vengono applicati ai pod definiti, quindi le regole di ingresso o uscita definiscono la modalità di flusso del traffico. L'esempio seguente applica i criteri di rete ai pod a cui è stata applicata l'etichetta *app: backend*. La regola di ingresso quindi consente solo il traffico proveniente da pod con l'etichetta *app: frontend*:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Per iniziare a usare i criteri, vedere [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Connettersi in modo sicuro ai nodi tramite un bastion host

**Suggerimento per la procedura consigliata**: non esporre la connettività remota ai nodi del servizio Azure Kubernetes. Creare un bastion host, o una jump box, in una rete virtuale di gestione. Usare il bastion host per instradare in modo sicuro il traffico nel cluster del servizio Azure Kubernetes alle attività di gestione remota.

La maggior parte delle operazioni nel servizio Azure Kubernetes può essere completata usando gli strumenti di gestione di Azure o tramite il server dell'API Kubernetes. I nodi del servizio Azure Kubernetes non sono collegati a Internet pubblico e sono disponibili solo in una rete privata. Per connettersi ai nodi ed eseguire la manutenzione o la risoluzione dei problemi, instradare le connessioni usando un bastion host o una jump box. Questo host dovrebbe trovarsi in una rete virtuale di gestione separata che viene associata in modo sicuro alla rete virtuale del cluster del servizio Azure Kubernetes.

![Connettersi ai nodi del servizio Azure Kubernetes usando un bastion host o una jump box](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Anche la rete di gestione per il bastion host dovrebbe essere protetta. Usare un servizio [Azure ExpressRoute][expressroute] o un [gateway VPN][vpn-gateway] per connettersi a una rete locale e controllare l'accesso con i gruppi di sicurezza di rete.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è stato incentrato sulla sicurezza e la connettività di rete. Per altre informazioni sulle nozioni di base della rete in Kubernetes, vedere [Concetti relativi alla rete per le applicazioni nel servizio Kubernetes di Azure (AKS)][aks-concepts-network].

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool