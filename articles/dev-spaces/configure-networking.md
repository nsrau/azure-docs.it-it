---
title: Configurare la rete per Azure Dev Spaces in topologie di rete diverse
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Descrive i requisiti di rete per l'esecuzione di Azure Dev Spaces nei servizi Kubernetes di Azure
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, CNI, kubenet, SDN, rete
ms.openlocfilehash: 9e32e3b65451dceefaeeaf7faed7c8337797e0b8
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044984"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configurare la rete per Azure Dev Spaces in topologie di rete diverse

Azure Dev Spaces viene eseguito nei cluster di Azure Kubernetes Service (AKS) con la configurazione di rete predefinita. Se si vuole modificare la configurazione di rete del cluster AKS, ad esempio inserendo il cluster dietro un firewall, usando gruppi di sicurezza di rete o usando i criteri di rete, è necessario incorporare ulteriori considerazioni per l'esecuzione di Azure Dev Spaces.

![Configurazione della rete virtuale](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configurazioni della rete virtuale o della subnet

Il cluster AKS può avere una configurazione di rete virtuale o subnet diversa per limitare il traffico in ingresso o in uscita per il cluster AKS. Ad esempio, il cluster potrebbe trovarsi dietro un firewall, ad esempio un firewall di Azure, oppure è possibile usare gruppi di sicurezza di rete o ruoli personalizzati per limitare il traffico di rete.

Azure Dev Spaces prevede determinati requisiti per il traffico di rete in *ingresso e in uscita* , oltre che per il traffico in *ingresso* . Se si usa Azure Dev Spaces in un cluster AKS con una configurazione di rete virtuale o subnet che limita il traffico per il cluster AKS, è necessario rispettare i requisiti di traffico in ingresso e in entrata e in uscita per Azure Dev Spaces funzione corretta.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisiti del traffico di rete in ingresso e in uscita

Azure Dev Spaces richiede il traffico in ingresso e in uscita per i nomi di dominio completi seguenti:

| Nome di dominio completo                       | Porta       | Uso      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Per eseguire il pull delle immagini Docker per Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Per eseguire il pull delle immagini Helm per Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Per eseguire il pull delle immagini Helm per Azure Dev Spaces |
| azds-*. azds. io             | HTTPS: 443 | Per comunicare con Azure Dev Spaces servizi back-end per il controller Azure Dev Spaces. Il nome FQDN esatto è reperibile in *dataplaneFqdn* in `USERPROFILE\.azds\settings.json` |

Aggiornare la configurazione del firewall o della sicurezza per consentire il traffico di rete da e verso i nomi di dominio completi precedenti. Se ad esempio si usa un firewall per proteggere la rete, è necessario aggiungere i nomi di dominio completi sopra indicati alla regola dell'applicazione del firewall per consentire il traffico da e verso questi domini.

### <a name="ingress-only-network-traffic-requirements"></a>Requisiti solo per il traffico di rete in ingresso

Azure Dev Spaces fornisce il routing a livello di spazio dei nomi Kubernetes, nonché l'accesso pubblico ai servizi usando il proprio nome di dominio completo. Per il corretto funzionamento di entrambe le funzionalità, aggiornare il firewall o la configurazione di rete per consentire il traffico in ingresso pubblico nell'indirizzo IP esterno del controller di ingresso del Azure Dev Spaces nel cluster. In alternativa, è possibile creare un servizio di [bilanciamento del carico interno][aks-internal-lb] e aggiungere una regola NAT nel firewall per tradurre l'IP pubblico del firewall nell'IP del servizio di bilanciamento del carico interno. Per creare un controller di ingresso personalizzato, è anche possibile usare [traefik][traefik-ingress] o [nginx][nginx-ingress] .

## <a name="aks-cluster-network-requirements"></a>Requisiti di rete del cluster AKS

AKS consente di usare i [criteri di rete][aks-network-policies] per controllare il traffico in ingresso e in uscita tra i pod in un cluster e il traffico in uscita da un pod. Azure Dev Spaces prevede determinati requisiti per il traffico di rete in *ingresso e in uscita* , oltre che per il traffico in *ingresso* . Se si usa Azure Dev Spaces in un cluster del servizio contenitore di sistema con i criteri di rete AKS, è necessario seguire i requisiti di traffico in ingresso e in ingresso e in uscita per consentire il corretto funzionamento di Azure Dev Spaces.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisiti del traffico di rete in ingresso e in uscita

Azure Dev Spaces consente di comunicare direttamente con un pod in uno spazio di sviluppo nel cluster per eseguire il debug. Per il funzionamento di questa funzionalità, aggiungere un criterio di rete che consenta la comunicazione in ingresso e in uscita agli indirizzi IP dell'infrastruttura di Azure Dev Spaces, che [variano in base all'area][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Requisiti solo per il traffico di rete in ingresso

Azure Dev Spaces fornisce il routing tra i pod tra gli spazi dei nomi. Ad esempio, gli spazi dei nomi con Azure Dev Spaces abilitato possono avere una relazione padre/figlio, che consente il routing del traffico di rete tra i pod tra gli spazi dei nomi padre e figlio. Per il corretto funzionamento di questa funzionalità, aggiungere un criterio di rete che consenta il traffico tra gli spazi dei nomi in cui viene indirizzato il traffico di rete, ad esempio gli spazi dei nomi padre/figlio. Inoltre, se il controller di ingresso viene distribuito nello spazio dei nomi *azds* , il controller di ingresso deve comunicare con i pod instrumentati dallo spazio di sviluppo di Azure in uno spazio dei nomi diverso. Affinché il controller di ingresso funzioni correttamente, il traffico di rete deve essere consentito dallo spazio dei nomi *azds* allo spazio dei nomi in cui sono in esecuzione i pod instrumentati.

## <a name="using-azure-cni"></a>Uso di Azure CNI

Per impostazione predefinita, i cluster AKS sono configurati per l'uso di [kubenet][aks-kubenet] per la rete, che funziona con Azure Dev Spaces. È anche possibile configurare il cluster AKS per l'uso dell' [interfaccia di rete di contenitori di Azure (CNI)][aks-cni]. Per usare Azure Dev Spaces con Azure CNI nel cluster AKS, consentire agli spazi degli indirizzi della rete virtuale e della subnet fino a 10 indirizzi IP privati per i pod distribuiti da Azure Dev Spaces. Altre informazioni su come consentire gli indirizzi IP privati sono disponibili nella [documentazione di Azure CNI di AKS][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Uso degli intervalli IP autorizzati del server API

I cluster AKS consentono di configurare una sicurezza aggiuntiva che limita quale indirizzo IP può interagire con i cluster, ad esempio usando reti virtuali personalizzate o [proteggendo l'accesso al server API usando intervalli di indirizzi IP autorizzati][aks-ip-auth-ranges]. Per usare Azure Dev Spaces quando si usa questa sicurezza aggiuntiva durante la [creazione][aks-ip-auth-range-create] del cluster, è necessario [consentire intervalli aggiuntivi in base all'area geografica][dev-spaces-ip-auth-range-regions]. È anche possibile [aggiornare][aks-ip-auth-range-update] un cluster esistente per consentire tali intervalli aggiuntivi. È anche necessario consentire l'indirizzo IP di tutti i computer di sviluppo che si connettono al cluster AKS per eseguire il debug per la connessione al server API.

## <a name="using-aks-private-clusters"></a>Uso di cluster privati AKS

A questo punto, Azure Dev Spaces non è supportato con i [cluster privati AKS][aks-private-clusters].

## <a name="client-requirements"></a>Requisiti per i client

Azure Dev Spaces usa gli strumenti lato client, ad esempio l'estensione dell'interfaccia della riga di comando Azure Dev Spaces, l'estensione Visual Studio Code e l'estensione di Visual Studio per comunicare con il cluster AKS per il debug. Per usare gli strumenti Azure Dev Spaces lato client, consentire il traffico dai computer di sviluppo al dominio *azds-\*. azds.io* . Vedere *dataplaneFqdn* in `USERPROFILE\.azds\settings.json` per il nome FQDN esatto. Se si usano gli [intervalli IP autorizzati del server API][auth-range-section], è anche necessario consentire l'indirizzo IP di tutti i computer di sviluppo che si connettono al cluster AKS per eseguire il debug per la connessione al server API.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Sviluppo in team in Azure Dev Spaces][team-quickstart]

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
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md