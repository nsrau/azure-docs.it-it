---
title: Usare un indirizzo IP statico con il bilanciamento del carico del servizio contenitore di Azure (AKS)
description: Usare un indirizzo IP statico con il bilanciamento del carico del servizio contenitore di Azure (AKS).
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 945132dd5f7e51f05ceda89a9cb16315aabbda8a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="use-a-static-ip-address-with-the-azure-container-service-aks-load-balancer"></a>Usare un indirizzo IP statico con il bilanciamento del carico del servizio contenitore di Azure (AKS)

In alcuni casi, ad esempio quando viene ricreato il bilanciamento del carico del servizio contenitore di Azure (AKS) o quando vengono ricreati i servizi Kubernetes con un tipo di bilanciamento del carico, l'indirizzo IP pubblico del servizio Kubernetes può cambiare. Questo documento illustra in dettaglio come configurare un indirizzo IP statico per i servizi Kubernetes.

## <a name="create-static-ip-address"></a>Creare un indirizzo IP statico

Creare un indirizzo IP pubblico statico per il servizio Kubernetes. L'indirizzo IP deve essere creato nel gruppo di risorse che è stato creato automaticamente durante la distribuzione del cluster. Per informazioni sui diversi gruppi di risorse del servizio contenitore di Azure e su come identificare il gruppo di risorse creato automaticamente, vedere [Domande frequenti relative al servizio contenitore di Azure (AKS)][aks-faq-resource-group].

Usare il comando [az network public ip create][az-network-public-ip-create] per creare l'indirizzo IP.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGRoup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Prendere nota dell'indirizzo IP.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 Se necessario, l'indirizzo può essere recuperato tramite il comando [az network public-ip list][az-network-public-ip-list].

```console
$ az network public-ip list --resource-group MC_myResourceGRoup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Creare un servizio con indirizzo IP

Dopo che è stato eseguito il provisioning dell'indirizzo IP statico, è possibile creare un servizio Kubernetes con la proprietà `loadBalancerIP` e un valore dell'indirizzo IP statico.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>risoluzione dei problemi

Se l'indirizzo IP statico non è stato creato o se è stato creato nel gruppo di risorse non corretto, la creazione del servizio ha esito negativo. Per risolvere i problemi, restituire gli eventi di creazione del servizio con il comando [kubectl describe][kubectl-describe].

```console
$ kubectl describe service azure-vote-front

Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
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
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/ 

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list