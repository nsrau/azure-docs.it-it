---
title: Usare un indirizzo IP statico con il bilanciamento del carico del servizio Azure Kubernetes
description: Informazioni su come creare e usare un indirizzo IP statico con il bilanciamento del carico del servizio Azure Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/06/2019
ms.author: mlearned
ms.openlocfilehash: 8457f1c0c5b6107c4b44f6f00236a33f7c67452a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325433"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usare un indirizzo IP pubblico statico con il bilanciamento del carico del servizio Azure Kubernetes

Per impostazione predefinita, l'indirizzo IP pubblico assegnato a una risorsa di bilanciamento del carico creata da un cluster servizio Azure Kubernetes vale solo per la durata di tale risorsa. Se si elimina il servizio Kubernetes, vengono eliminati anche il bilanciamento del carico e l'indirizzo IP associati. Se si vuole assegnare un indirizzo IP specifico o mantenere un indirizzo IP per i servizi Kubernetes ridistribuiti, è possibile creare e usare un indirizzo IP pubblico statico.

Questo articolo illustra come creare un indirizzo IP pubblico statico e assegnarlo al servizio Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. If you need an AKS cluster, see the AKS quickstart [using the Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

You also need the Azure CLI version 2.0.59 or later installed and configured. Eseguire  `az --version` per trovare la versione. If you need to install or upgrade, see [Install Azure CLI][install-azure-cli].

This article covers using a *Standard* SKU IP with a *Standard* SKU load balancer. For more information, see [IP address types and allocation methods in Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Creare un indirizzo IP statico

Create a static public IP address with the [az network public ip create][az-network-public-ip-create] command. The following creates a static IP resource named *myAKSPublicIP* in the *myResourceGroup* resource group:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> If you are using a *Basic* SKU load balancer in your AKS cluster, use *Basic* for the *sku* parameter when defining a public IP. Only *Basic* SKU IPs work with the *Basic* SKU load balancer and only *Standard* SKU IPs work with *Standard* SKU load balancers. 

The IP address is displayed, as shown in the following condensed example output:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

You can later get the public IP address using the [az network public-ip list][az-network-public-ip-list] command. Specificare il nome del gruppo di risorse del nodo e l'indirizzo IP creato e quindi eseguire una query per *ipAddress* come illustrato nell'esempio seguente:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Creare un servizio usando l'indirizzo IP statico

Before creating a service, ensure the service principal used by the AKS cluster has delegated permissions to the other resource group. ad esempio:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

To create a *LoadBalancer* service with the static public IP address, add the `loadBalancerIP` property and the value of the static public IP address to the YAML manifest. Creare un file denominato `load-balancer-service.yaml` e copiarlo nel codice YAML seguente. Aggiungere il proprio indirizzo IP pubblico creato nel passaggio precedente. The following example also sets the annotation to the resource group named *myResourceGroup*. Provide your own resource group name.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Creare il servizio e la distribuzione con il comando `kubectl apply`.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="troubleshoot"></a>Risolvere problemi

If the static IP address defined in the *loadBalancerIP* property of the Kubernetes service manifest does not exist, or has not been created in the node resource group and no additional delegations configured, the load balancer service creation fails. To troubleshoot, review the service creation events with the [kubectl describe][kubectl-describe] command. Specificare il nome del servizio indicato nel manifesto YAML, come illustrato nell'esempio seguente:

```console
kubectl describe service azure-load-balancer
```

Vengono visualizzate le informazioni sulla risorsa del servizio Kubernetes. Gli *Eventi* alla fine dell'output di esempio seguente indicano che l'*indirizzo IP fornito dall'utente non è stato trovato*. In questi scenari, verificare che sia stato creato l'indirizzo IP pubblico statico nel gruppo di risorse del nodo e che l'indirizzo IP specificato nel manifesto del servizio Kubernetes sia corretto.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Passaggi successivi

For additional control over the network traffic to your applications, you may want to instead [create an ingress controller][aks-ingress-basic]. You can also [create an ingress controller with a static public IP address][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
