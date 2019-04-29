---
title: Usare un indirizzo IP statico con il bilanciamento del carico del servizio Azure Kubernetes
description: Informazioni su come creare e usare un indirizzo IP statico con il bilanciamento del carico del servizio Azure Kubernetes.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: d2e4314948eeda0c82c004414f894dafc4d4cff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031648"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usare un indirizzo IP pubblico statico con il bilanciamento del carico del servizio Azure Kubernetes

Per impostazione predefinita, l'indirizzo IP pubblico assegnato a una risorsa di bilanciamento del carico creata da un cluster servizio Azure Kubernetes vale solo per la durata di tale risorsa. Se si elimina il servizio Kubernetes, vengono eliminati anche il bilanciamento del carico e l'indirizzo IP associati. Se si vuole assegnare un indirizzo IP specifico o mantenere un indirizzo IP per i servizi Kubernetes ridistribuiti, è possibile creare e usare un indirizzo IP pubblico statico.

Questo articolo illustra come creare un indirizzo IP pubblico statico e assegnarlo al servizio Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Anche necessario la CLI di Azure versione 2.0.59 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Attualmente solo *SKU Basic IP*è supportata. Lavoro è in corso per supportare le *IP Standard* SKU di risorsa. Per altre informazioni, vedere [metodi di allocazione in Azure e i tipi di indirizzi IP][ip-sku].

## <a name="create-a-static-ip-address"></a>Creare un indirizzo IP statico

Quando si crea un indirizzo IP pubblico statico per l'uso con servizio Azure Kubernetes, è necessario creare la risorsa indirizzo IP nel gruppo di risorse del **nodo**. Se si desidera separare le risorse, vedere la sezione seguente per [usare un indirizzo IP statico di fuori del gruppo di risorse nodo](#use-a-static-ip-address-outside-of-the-node-resource-group).

Innanzitutto, ottenere il nome di gruppo di risorse di nodo con il [show di az aks] [ az-aks-show] comando e aggiungere il `--query nodeResourceGroup` parametro di query. L'esempio seguente ottiene il gruppo di risorse del nodo per il nome del cluster servizio Azure Kubernetes *myservizio Azure KubernetesCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_chinaeast
```

Quindi usare il comando [az network public-ip create][az-network-public-ip-create] per creare un indirizzo IP pubblico statico. Specificare il nome del gruppo di risorse del nodo ottenuto nel comando precedente e quindi un nome per la risorsa indirizzo IP, ad esempio *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
    --name myAKSPublicIP \
    --allocation-method static
```

Viene visualizzato l'indirizzo IP, come illustrato nell'output di esempio sintetico seguente:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_chinaeast/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

È possibile ottenere l'indirizzo IP pubblico in un secondo momento usando il comando [az network public-ip list][az-network-public-ip-list]. Specificare il nome del gruppo di risorse del nodo e l'indirizzo IP creato e quindi eseguire una query per *ipAddress* come illustrato nell'esempio seguente:

```azurecli
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_chinaeast --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Creare un servizio usando l'indirizzo IP statico

Per creare un servizio con l'indirizzo IP pubblico statico, aggiungere la proprietà `loadBalancerIP` e il valore dell'indirizzo IP pubblico statico al manifesto YAML. Creare un file denominato `load-balancer-service.yaml` e copiarlo nel codice YAML seguente. Aggiungere il proprio indirizzo IP pubblico creato nel passaggio precedente.

```yaml
apiVersion: v1
kind: Service
metadata:
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

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Usare un indirizzo IP statico all'esterno del gruppo di risorse del nodo

Con Kubernetes 1.10 o versione successiva è possibile usare un indirizzo IP statico che viene creato all'esterno del gruppo di risorse del nodo. L'entità servizio usata dal cluster servizio Azure Kubernetes deve avere autorizzazioni delegate per l'accesso all'altro gruppo di risorse, come illustrato nell'esempio seguente:

```azurecli
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Per usare un indirizzo IP esterno al gruppo di risorse del nodo, aggiungere un'annotazione alla definizione del servizio. L'esempio seguente imposta l'annotazione sul gruppo di risorse denominato *myResourceGroup*. Specificare il nome del gruppo di risorse:

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

## <a name="troubleshoot"></a>Risolvere problemi

Se l'indirizzo IP statico definito nel *loadBalancerIP* proprietà del manifesto del servizio Kubernetes non esiste o non è stato creato il gruppo di risorse di nodi e nessuna delega aggiuntiva configurata, il servizio di bilanciamento del carico creazione ha esito negativo. Per risolvere il problema, rivedere gli eventi di creazione del servizio con il comando [kubectl describe][kubectl-describe]. Specificare il nome del servizio indicato nel manifesto YAML, come illustrato nell'esempio seguente:

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

Per un ulteriore controllo sul traffico di rete nelle applicazioni è possibile invece [creare un controller di ingresso][aks-ingress-basic]. È anche possibile [creare un controller di ingresso con un indirizzo IP pubblico statico][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create
[az-network-public-ip-list]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-list
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku