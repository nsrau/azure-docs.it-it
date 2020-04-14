---
title: Configurare la rete per i vani di sviluppo di Azure in topologie di rete diverseConfigure networking for Azure Dev Spaces in different network topologies
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Descrive i requisiti di rete per l'esecuzione di Azure Dev Spaces nei servizi di Azure Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, container, CNI, kubenet, SDN, network
ms.openlocfilehash: 3e344576caf276ae7cb5fe00395c84810a4e7d32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262044"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configurare la rete per i vani di sviluppo di Azure in topologie di rete diverseConfigure networking for Azure Dev Spaces in different network topologies

Azure Dev Spaces viene eseguito nei cluster del servizio Azure Kubernetes con la configurazione di rete predefinita. Se si vuole modificare la configurazione di rete del cluster AKS, ad esempio l'inserimento del cluster dietro un firewall, l'utilizzo di gruppi di sicurezza di rete o l'utilizzo di criteri di rete, è necessario incorporare considerazioni aggiuntive per l'esecuzione di spazi di sviluppo di Azure.If you want to change the networking configuration of your AKS cluster, such as putting the cluster behind a firewall, using network security groups, or using network policies, you have to incorporate additional considerations for running Azure Dev Spaces.

![Configurazione della rete virtuale](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configurazioni di rete virtuale o subnetVirtual network or subnet configurations

Il cluster AKS potrebbe avere una configurazione di rete virtuale o subnet diversa per limitare il traffico in ingresso o in uscita per il cluster AKS. Ad esempio, il cluster potrebbe essere protetto da un firewall, ad esempio Firewall di Azure, oppure è possibile usare gruppi di sicurezza di rete o ruoli personalizzati per limitare il traffico di rete. È possibile trovare una configurazione di rete di esempio nel [repository di esempio Azure Dev Spaces in GitHub][sample-repo].

Gli spazi di sviluppo di Azure hanno determinati requisiti per il traffico di rete *in ingresso e in uscita,* nonché per il traffico solo in *ingresso.* Se si usano Azure Dev Spaces in un cluster AKS con una configurazione di rete virtuale o subnet che limita il traffico per il cluster AKS, è necessario seguire solo i requisiti di ingresso e di traffico in ingresso e in uscita seguenti per il corretto funzionamento degli spazi di sviluppo di Azure.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisiti per il traffico di rete in ingresso e in uscita

Azure Dev Spaces needs ingress and egress traffic for following FQDNs:

| Nome di dominio completo                       | Porta       | Uso      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Per eseguire il pull delle immagini docker per gli spazi di sviluppo di AzureTo pull docker images for Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Per eseguire il pull delle immagini per gli spazi di sviluppo di AzureTo pull helm images for Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Per eseguire il pull delle immagini per gli spazi di sviluppo di AzureTo pull helm images for Azure Dev Spaces |
| azds-.azds.io             | HTTPS: 443 | Per comunicare con i servizi back-end di Azure Dev Spaces per il controller Azure Dev Spaces.To communicate with Azure Dev Spaces backend services for the Azure Dev Spaces controller. Il nome FQDN esatto è reperibile in *dataplaneFqdn* in`USERPROFILE\.azds\settings.json` |

Aggiornare la configurazione del firewall o della sicurezza per consentire il traffico di rete da e verso tutti gli FQDN sopra indicati. Ad esempio, se si utilizza un firewall per proteggere la rete, gli FQDN precedenti devono essere aggiunti alla regola dell'applicazione del firewall per consentire il traffico da e verso questi domini.

### <a name="ingress-only-network-traffic-requirements"></a>Requisiti per il traffico di rete solo in ingresso

Azure Dev Spaces offre il routing a livello di spazio dei nomi Kubernetes e l'accesso pubblico ai servizi usando il proprio FQDN. Affinché entrambe le funzionalità funzionino, aggiornare la configurazione del firewall o della rete per consentire l'ingresso pubblico all'indirizzo IP esterno del controller di ingresso Azure Dev Spaces nel cluster. In alternativa, è possibile creare un servizio di [bilanciamento del carico interno][aks-internal-lb] e aggiungere una regola NAT nel firewall per convertire l'indirizzo IP pubblico del firewall nell'indirizzo IP del servizio di bilanciamento del carico interno. È inoltre possibile utilizzare [traefik][traefik-ingress] o [NGINX][nginx-ingress] per creare un controller di ingresso personalizzato.

## <a name="aks-cluster-network-requirements"></a>Requisiti di rete del cluster AKS

AKS consente di utilizzare i criteri di [rete][aks-network-policies] per controllare il traffico in ingresso ed in uscita tra i pod in un cluster, nonché il traffico in uscita da un pod. Gli spazi di sviluppo di Azure hanno determinati requisiti per il traffico di rete *in ingresso e in uscita,* nonché per il traffico solo in *ingresso.* Se si usano Azure Dev Spaces in un cluster AKS con criteri di rete AKS, è necessario seguire solo i requisiti di traffico in ingresso e in ingresso ed in uscita seguenti per il corretto funzionamento degli spazi di sviluppo di Azure.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisiti per il traffico di rete in ingresso e in uscita

Spazi di sviluppo di Azure consente di comunicare direttamente con un pod in uno spazio di sviluppo nel cluster per il debug. Affinché questa funzionalità funzioni, aggiungere un criterio di rete che consenta la comunicazione in ingresso ed in uscita agli indirizzi IP dell'infrastruttura Azure Dev Spaces, che variano in base [all'area.][dev-spaces-ip-auth-range-regions]

### <a name="ingress-only-network-traffic-requirements"></a>Requisiti per il traffico di rete solo in ingresso

Azure Dev Spaces offre il routing tra pod tra gli spazi dei nomi. Ad esempio, gli spazi dei nomi con spazi di sviluppo di Azure abilitati possono avere una relazione padre/figlio, che consente di instradare il traffico di rete tra i pod tra gli spazi dei nomi padre e figlio. Azure Dev Spaces espone anche gli endpoint del servizio usando il proprio FQDN. Per configurare diverse modalità di esposizione dei servizi e il modo in cui influiscono sul routing a livello di spazio dei nomi, vedere [Utilizzo di opzioni endpoint diverse][endpoint-options].

## <a name="using-azure-cni"></a>Uso di Azure CNIUsing Azure CNI

Per impostazione predefinita, i cluster AKS sono configurati per usare [il kubenet][aks-kubenet] per la rete, che funziona con Azure Dev Spaces.By default, AKS clusters are configured to use kubenet for networking, which works with Azure Dev Spaces. È anche possibile configurare il cluster AKS per l'utilizzo di [Azure Container Networking Interface (CNI)][aks-cni]. To use Azure Dev Spaces with Azure CNI on your AKS cluster, allow your virtual network and subnet address spaces up to 10 private IP addresses for pods deployed by Azure Dev Spaces. Ulteriori dettagli sull'abilitazione degli indirizzi IP privati sono disponibili nella documentazione di [AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Utilizzo degli intervalli IP autorizzati del server API

I cluster AKS consentono di configurare una sicurezza aggiuntiva che limita l'ambito dell'indirizzo IP che può interagire con i cluster, ad esempio utilizzando reti virtuali personalizzate o [proteggendo l'accesso al server API utilizzando intervalli IP autorizzati.][aks-ip-auth-ranges] Per usare Azure Dev Spaces quando usi questa sicurezza aggiuntiva durante la [creazione][aks-ip-auth-range-create] del cluster, devi [consentire intervalli aggiuntivi in base all'area][dev-spaces-ip-auth-range-regions]geografica. È inoltre possibile [aggiornare][aks-ip-auth-range-update] un cluster esistente per consentire tali intervalli aggiuntivi. È inoltre necessario consentire l'indirizzo IP di tutti i computer di sviluppo che si connettono al cluster AKS per il debug per connettersi al server API.

## <a name="using-aks-private-clusters"></a>Utilizzo di cluster privati AKSUsing AKS private clusters

Al momento, Azure Dev Spaces non è supportato con [i cluster privati AKS.][aks-private-clusters]

## <a name="using-different-endpoint-options"></a>Utilizzo di diverse opzioni endpoint

Azure Dev Spaces ha la possibilità di esporre gli endpoint per i servizi in esecuzione in AKS. Quando si abilitaAzure Dev Spaces nel cluster, sono disponibili le opzioni seguenti per la configurazione del tipo di endpoint per il cluster:When enabling Azure Dev Spaces on your cluster, you have the following options for configuring the endpoint type for your cluster:

* Un endpoint *pubblico,* che è l'impostazione predefinita, distribuisce un controller in ingresso con un indirizzo IP pubblico. L'indirizzo IP pubblico viene registrato nel DNS del cluster, consentendo l'accesso pubblico ai servizi tramite un URL. È possibile visualizzare `azds list-uris`questo URL utilizzando .
* Un endpoint *privato* distribuisce un controller in ingresso con un indirizzo IP privato. Con un indirizzo IP privato, il servizio di bilanciamento del carico per il cluster è accessibile solo dall'interno della rete virtuale del cluster. L'indirizzo IP privato del servizio di bilanciamento del carico viene registrato nel DNS del cluster in modo che sia possibile accedere ai servizi all'interno della rete virtuale del cluster tramite un URL. È possibile visualizzare `azds list-uris`questo URL utilizzando .
* L'impostazione *di none* per l'opzione dell'endpoint causa la distribuzione di alcun controller in ingresso. Senza la distribuzione di alcun controller in ingresso, le funzionalità di routing di [Azure Dev Spaces][dev-spaces-routing] non funzioneranno. Facoltativamente, è possibile implementare la propria soluzione di controller in ingresso utilizzando [traefik][traefik-ingress] o [NGINX][nginx-ingress], che consentirà alle funzionalità di routing di funzionare di nuovo.

Per configurare l'opzione dell'endpoint, usare *-e* o *--endpoint* quando si abilita Azure Dev Spaces nel cluster. Ad esempio:

> [!NOTE]
> L'opzione endpoint richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Requisiti per i client

Azure Dev Spaces usa strumenti lato client, ad esempio l'estensione DELL'interfaccia della riga di comando di Azure Dev Spaces, l'estensione di codice di Visual Studio e l'estensione di Visual Studio, per comunicare con il cluster AKS per il debug. Per usare gli strumenti sul lato client di Azure Dev Spaces, consentire il traffico dai computer di sviluppo al dominio *azds-\*.azds.io.* Vedere *dataplaneFqdn* in `USERPROFILE\.azds\settings.json` per il nome di dominio completo esatto. Se si utilizzano [intervalli IP autorizzati][auth-range-section]dal server API, è inoltre necessario consentire l'indirizzo IP di tutti i computer di sviluppo che si connettono al cluster AKS per il debug per connettersi al server API.

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
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[team-quickstart]: quickstart-team-development.md