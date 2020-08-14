---
title: Configurare la rete per Azure Dev Spaces in topologie di rete diverse
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Descrive i requisiti di rete per l'esecuzione di Azure Dev Spaces nei servizi Kubernetes di Azure
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, CNI, kubenet, SDN, rete
ms.openlocfilehash: 0d9ebbec3e3c07a466acb58e88b67e6a32a20edb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214167"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configurare la rete per Azure Dev Spaces in topologie di rete diverse

Azure Dev Spaces viene eseguito nei cluster di Azure Kubernetes Service (AKS) con la configurazione di rete predefinita. Se si vuole modificare la configurazione di rete del cluster AKS, ad esempio inserendo il cluster dietro un firewall, usando gruppi di sicurezza di rete o usando i criteri di rete, è necessario incorporare ulteriori considerazioni per l'esecuzione di Azure Dev Spaces.

![Configurazione della rete virtuale](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configurazioni della rete virtuale o della subnet

Il cluster AKS può avere una configurazione di rete virtuale o subnet diversa per limitare il traffico in ingresso o in uscita per il cluster AKS. Ad esempio, il cluster potrebbe trovarsi dietro un firewall, ad esempio un firewall di Azure, oppure è possibile usare gruppi di sicurezza di rete o ruoli personalizzati per limitare il traffico di rete. È possibile trovare una configurazione di rete di esempio nel [repository di esempio Azure Dev spaces su GitHub][sample-repo].

Azure Dev Spaces prevede determinati requisiti per il traffico di rete in *ingresso e in uscita* , oltre che per il traffico in *ingresso* . Se si usa Azure Dev Spaces in un cluster AKS con una configurazione della rete virtuale o della subnet che limita il traffico per il cluster AKS, è necessario rispettare i requisiti di traffico in ingresso e in entrata e in uscita per consentire il corretto funzionamento di Azure Dev Spaces.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisiti del traffico di rete in ingresso e in uscita

Azure Dev Spaces richiede il traffico in ingresso e in uscita per i nomi di dominio completi seguenti:

| Nome di dominio completo                       | Porta       | Uso      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Per eseguire il pull delle immagini Docker per Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Per eseguire il pull delle immagini Helm per Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Per eseguire il pull delle immagini Helm per Azure Dev Spaces |

Aggiornare la configurazione del firewall o della sicurezza per consentire il traffico di rete da e verso tutti i nomi di dominio completi e i [servizi di infrastruttura Azure Dev Spaces][service-tags]. Ad esempio, se si usa un firewall per proteggere la rete, è necessario aggiungere i nomi di dominio completi sopra indicati alla regola dell'applicazione del firewall e anche il tag del servizio di Azure Dev Spaces deve essere [aggiunto al firewall][firewall-service-tags]. Entrambi gli aggiornamenti al firewall sono necessari per consentire il traffico da e verso questi domini.

### <a name="ingress-only-network-traffic-requirements"></a>Requisiti solo per il traffico di rete in ingresso

Azure Dev Spaces fornisce il routing a livello di spazio dei nomi Kubernetes, nonché l'accesso pubblico ai servizi usando il proprio nome di dominio completo. Per il corretto funzionamento di entrambe le funzionalità, aggiornare il firewall o la configurazione di rete per consentire il traffico in ingresso pubblico nell'indirizzo IP esterno del controller di ingresso del Azure Dev Spaces nel cluster. In alternativa, è possibile creare un servizio di [bilanciamento del carico interno][aks-internal-lb] e aggiungere una regola NAT nel firewall per tradurre l'IP pubblico del firewall nell'IP del servizio di bilanciamento del carico interno. Per creare un controller di ingresso personalizzato, è anche possibile usare [traefik][traefik-ingress] o [nginx][nginx-ingress] .

## <a name="aks-cluster-network-requirements"></a>Requisiti di rete del cluster AKS

AKS consente di usare i [criteri di rete][aks-network-policies] per controllare il traffico in ingresso e in uscita tra i pod in un cluster e il traffico in uscita da un pod. Azure Dev Spaces prevede determinati requisiti per il traffico di rete in *ingresso e in uscita* , oltre che per il traffico in *ingresso* . Se si usa Azure Dev Spaces in un cluster del servizio contenitore di sistema con i criteri di rete AKS, è necessario seguire i requisiti di traffico in ingresso e in ingresso e in uscita per consentire il corretto funzionamento di Azure Dev Spaces.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisiti del traffico di rete in ingresso e in uscita

Azure Dev Spaces consente di comunicare direttamente con un pod in uno spazio di sviluppo nel cluster per eseguire il debug. Per il funzionamento di questa funzionalità, aggiungere un criterio di rete che consenta la comunicazione in ingresso e in uscita agli indirizzi IP dell'infrastruttura di Azure Dev Spaces, che [variano in base all'area][service-tags].

### <a name="ingress-only-network-traffic-requirements"></a>Requisiti solo per il traffico di rete in ingresso

Azure Dev Spaces fornisce il routing tra i pod tra gli spazi dei nomi. Ad esempio, gli spazi dei nomi con Azure Dev Spaces abilitato possono avere una relazione padre/figlio, che consente il routing del traffico di rete tra i pod tra gli spazi dei nomi padre e figlio. Azure Dev Spaces espone anche gli endpoint di servizio con il proprio FQDN. Per configurare diverse modalità di esposizione dei servizi e il modo in cui influisca sul routing a livello di spazio dei nomi, vedere [utilizzo di diverse opzioni di endpoint][endpoint-options].

## <a name="using-azure-cni"></a>Uso di Azure CNI

Per impostazione predefinita, i cluster AKS sono configurati per l'uso di [kubenet][aks-kubenet] per la rete, che funziona con Azure Dev Spaces. È anche possibile configurare il cluster AKS per l'uso dell' [interfaccia di rete di contenitori di Azure (CNI)][aks-cni]. Per usare Azure Dev Spaces con Azure CNI nel cluster AKS, consentire agli spazi degli indirizzi della rete virtuale e della subnet fino a 10 indirizzi IP privati per i pod distribuiti da Azure Dev Spaces. Altre informazioni su come consentire gli indirizzi IP privati sono disponibili nella [documentazione di Azure CNI di AKS][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Uso degli intervalli IP autorizzati del server API

I cluster AKS consentono di configurare una sicurezza aggiuntiva che limita quale indirizzo IP può interagire con i cluster, ad esempio usando reti virtuali personalizzate o [proteggendo l'accesso al server API usando intervalli di indirizzi IP autorizzati][aks-ip-auth-ranges]. Per usare Azure Dev Spaces quando si usa questa sicurezza aggiuntiva durante la [creazione][aks-ip-auth-range-create] del cluster, è necessario [consentire intervalli aggiuntivi in base all'area geografica][service-tags]. È anche possibile [aggiornare][aks-ip-auth-range-update] un cluster esistente per consentire tali intervalli aggiuntivi. È anche necessario consentire l'indirizzo IP di tutti i computer di sviluppo che si connettono al cluster AKS per eseguire il debug per la connessione al server API.

## <a name="using-aks-private-clusters"></a>Uso di cluster privati AKS

A questo punto, Azure Dev Spaces non è supportato con i [cluster privati AKS][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Utilizzo di opzioni endpoint diverse

Azure Dev Spaces dispone dell'opzione per esporre gli endpoint per i servizi in esecuzione su AKS. Quando si Abilita Azure Dev Spaces nel cluster, sono disponibili le opzioni seguenti per la configurazione del tipo di endpoint per il cluster:

* Un endpoint *pubblico* , che è l'impostazione predefinita, distribuisce un controller di ingresso con un indirizzo IP pubblico. L'indirizzo IP pubblico viene registrato nel DNS del cluster, consentendo l'accesso pubblico ai servizi tramite un URL. È possibile visualizzare questo URL utilizzando `azds list-uris` .
* Un endpoint *privato* distribuisce un controller di ingresso con un indirizzo IP privato. Con un indirizzo IP privato, il servizio di bilanciamento del carico per il cluster è accessibile solo dall'interno della rete virtuale del cluster. L'indirizzo IP privato del servizio di bilanciamento del carico è registrato nel DNS del cluster, in modo che sia possibile accedere ai servizi all'interno della rete virtuale del cluster usando un URL. È possibile visualizzare questo URL utilizzando `azds list-uris` .
* Se si imposta *None* per l'opzione endpoint, non verrà distribuito alcun controller di ingresso. Senza il controller di ingresso distribuito, le [funzionalità di routing Azure Dev Spaces][dev-spaces-routing] non funzioneranno. Facoltativamente, è possibile implementare la propria soluzione del controller di ingresso usando [traefik][traefik-ingress] o [nginx][nginx-ingress], che consente di riutilizzare le funzionalità di routing.

Per configurare l'opzione endpoint, usare *-e* o *--endpoint* quando si Abilita Azure Dev Spaces nel cluster. Ad esempio:

> [!NOTE]
> Per l'opzione endpoint è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Requisiti per i client

Azure Dev Spaces usa gli strumenti lato client, ad esempio l'estensione dell'interfaccia della riga di comando Azure Dev Spaces, l'estensione Visual Studio Code e l'estensione di Visual Studio per comunicare con il cluster AKS per il debug. Per usare gli strumenti Azure Dev Spaces lato client, consentire il traffico dai computer di sviluppo all' [infrastruttura Azure Dev Spaces][dev-spaces-allow-infrastructure]. Se si usano gli [intervalli IP autorizzati del server API][auth-range-section], è anche necessario consentire l'indirizzo IP di tutti i computer di sviluppo che si connettono al cluster AKS per eseguire il debug per la connessione al server API.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags