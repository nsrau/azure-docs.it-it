---
title: Indirizzo IP statico per il traffico in uscita nel servizio Azure Kubernetes
description: Informazioni su come creare e usare un indirizzo IP pubblico statico per il traffico in uscita in un cluster del servizio Azure Kubernetes
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 094a696a12025dcfd575ce3f035b12b4a04aba10
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615576"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Usare un indirizzo IP statico per il traffico in uscita nel servizio Azure Kubernetes

Per impostazione predefinita, l'indirizzo IP in uscita da un cluster del servizio Azure Kubernetes viene assegnato in modo casuale. Questa configurazione non è ideale ad esempio quando è necessario identificare un indirizzo IP per l'accesso a servizi esterni. Al contrario, potrebbe essere necessario assegnare un indirizzo IP statico che può essere inserito nell'elenco elementi consentiti per l'accesso al servizio.

Questo articolo illustra come creare e usare un indirizzo IP pubblico statico per il traffico in uscita in un cluster di AKS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster del servizio contenitore di AZURE, vedere la Guida introduttiva AKS [tramite la CLI di Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Anche necessario la CLI di Azure versione 2.0.59 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare CLI Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Panoramica sul traffico in uscita

Il traffico in uscita da un cluster AKS segue [convenzioni di Azure Load Balancer][outbound-connections]. Prima che venga creato il primo servizio Kubernetes di tipo `LoadBalancer`, i nodi dell'agente in un cluster di servizio Azure Kubernetes non appartengono ad alcun pool di Azure Load Balancer. In questa configurazione i nodi sono privi di indirizzo IP pubblico a livello di istanza. Azure trasla il flusso in uscita a un indirizzo IP pubblico di origine che non è configurabile o deterministico.

Dopo che è stato creato un servizio Kubernetes di tipo `LoadBalancer`, i nodi dell'agente vengono aggiunti a un pool di Azure Load Balancer. Azure trasla il flusso in uscita al primo indirizzo IP pubblico che è stato configurato nel bilanciamento del carico. Questo indirizzo IP pubblico è valido solo per la durata di tale risorsa. Se si elimina il servizio di Kubernetes di tipo LoadBalancer, vengono eliminati anche il bilanciamento del carico e l'indirizzo IP associati. Se si vuole assegnare un indirizzo IP specifico o mantenere un indirizzo IP per i servizi Kubernetes ridistribuiti, è possibile creare e usare un indirizzo IP pubblico statico.

## <a name="create-a-static-public-ip"></a>Creare un IP pubblico statico

Quando si crea un indirizzo IP pubblico statico per l'utilizzo con servizio Azure Kubernetes, è necessario creare la risorsa indirizzo IP nel gruppo di risorse del **nodo**. Ottenere il nome del gruppo di risorse con il [show di az aks][az-aks-show] comandi e aggiungere il `--query nodeResourceGroup` parametro di query. L'esempio seguente ottiene il gruppo di risorse del nodo per il nome del cluster servizio Azure Kubernetes *myservizio Azure KubernetesCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

A questo punto creare un indirizzo IP pubblico statico con il [az network public ip create][az-network-public-ip-create] comando. Specificare il nome del gruppo di risorse del nodo ottenuto nel comando precedente e quindi un nome per la risorsa indirizzo IP, ad esempio *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Viene visualizzato l'indirizzo IP, come illustrato nell'esempio sintetico di output seguente:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

È possibile ottenere in un secondo momento l'indirizzo IP pubblico usando il [elenco di az network public-ip][az-network-public-ip-list] comando. Specificare il nome del gruppo di risorse del nodo e quindi eseguire una query per *ipAddress* come illustrato nell'esempio seguente:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Creare un servizio con l'indirizzo IP statico

Per creare un servizio con l'indirizzo IP pubblico statico, aggiungere la proprietà `loadBalancerIP` e il valore dell'indirizzo IP pubblico statico al manifesto YAML. Creare un file denominato `egress-service.yaml` e copiarlo nel codice YAML seguente. Aggiungere il proprio indirizzo IP pubblico creato nel passaggio precedente.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Creare il servizio e la distribuzione con il comando `kubectl apply`.

```console
kubectl apply -f egress-service.yaml
```

Questo servizio configura un nuovo indirizzo IP front-end in Azure Load Balancer. Se non sono configurati altri indirizzi IP, ora **tutto** il traffico in uscita dovrebbe usare questo indirizzo. Quando sono configurati più indirizzi in Azure Load Balancer, il flusso in uscita usa il primo indirizzo IP di questo bilanciamento del carico.

## <a name="verify-egress-address"></a>Verificare l'indirizzo in uscita

Per verificare che si stia usando l'indirizzo IP pubblico statico, è possibile usare un servizio di ricerca DNS, ad esempio `checkip.dyndns.org`.

Avviare e connettersi a un pod *Debian* di base:

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Per accedere a un sito web dall'interno del contenitore, usare `apt-get` per installare `curl` nel contenitore.

```console
apt-get update && apt-get install curl -y
```

Viene usato curl per accedere al sito *checkip.dyndns.org*. Viene visualizzato l'indirizzo IP di uscita, come illustrato nell'esempio di output seguente. Questo indirizzo IP corrisponde all'indirizzo IP pubblico statico creato e definito per il servizio loadBalancer:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Passaggi successivi

Per evitare di mantenere più indirizzi IP pubblici in Azure Load Balancer, è possibile usare un controller di ingresso. I controller di ingresso forniscono vantaggi aggiuntivi come la terminazione SSL/TLS, il supporto per la riscrittura degli URI e la crittografia SSL/TLS a monte. Per altre informazioni, vedere [creare un controller di ingresso di base nel servizio contenitore di AZURE][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli