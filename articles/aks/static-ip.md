---
title: Usare un indirizzo IP statico e un'etichetta DNS con il servizio di bilanciamento del carico del servizio Azure Kubernetes (AKS)
description: Informazioni su come creare e usare un indirizzo IP statico con il bilanciamento del carico del servizio Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 6c219976db21fb05ea1ad313b4effdf95906f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047962"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usare un indirizzo IP pubblico statico e un'etichetta DNS con il servizio di bilanciamento del carico del servizio Azure Kubernetes (AKS)Use a static public IP address and DNS label with the Azure Kubernetes Service (AKS) load balancer

Per impostazione predefinita, l'indirizzo IP pubblico assegnato a una risorsa di bilanciamento del carico creata da un cluster servizio Azure Kubernetes vale solo per la durata di tale risorsa. Se si elimina il servizio Kubernetes, vengono eliminati anche il bilanciamento del carico e l'indirizzo IP associati. Se si vuole assegnare un indirizzo IP specifico o mantenere un indirizzo IP per i servizi Kubernetes ridistribuiti, è possibile creare e usare un indirizzo IP pubblico statico.

Questo articolo illustra come creare un indirizzo IP pubblico statico e assegnarlo al servizio Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È inoltre necessaria l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Questo articolo illustra l'uso di un IP SKU Standard con un servizio di bilanciamento del carico SKU *Standard.This* article covers using a *Standard* SKU IP with a Standard SKU load balancer. Per altre informazioni, vedere Tipi di indirizzi IP e metodi di allocazione in Azure.For more information, see [IP address types and allocation methods in Azure.][ip-sku]

## <a name="create-a-static-ip-address"></a>Creare un indirizzo IP statico

Creare un indirizzo IP pubblico statico con il comando [az network public ip create.][az-network-public-ip-create] Di seguito viene creata una risorsa IP statica *denominata myAKSPublicIP* nel gruppo di risorse *myResourceGroup:The* following creates a static IP resource named myAKSPublicIP in the myResourceGroup resource group:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Se si usa un servizio di bilanciamento del carico SKU *di base* nel cluster AKS, usare *Basic* per il parametro *sku* quando si definisce un indirizzo IP pubblico. Solo gli indirizzi IP SKU *di base* funzionano con il bilanciamento del carico SKU di base e solo gli INDIRIZZI IP SKU Standard funzionano con i servizi di bilanciamento del carico SKU *Standard.Only* Basic SKU IPs work with the *Basic* SKU load balancer and only *Standard* SKU IPs work with Standard SKU load balancers. 

Viene visualizzato l'indirizzo IP, come illustrato nel seguente output di esempio condensato:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

È possibile ottenere l'indirizzo IP pubblico in un secondo momento usando il comando [az network public-ip list][az-network-public-ip-list]. Specificare il nome del gruppo di risorse del nodo e l'indirizzo IP creato e quindi eseguire una query per *ipAddress* come illustrato nell'esempio seguente:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Creare un servizio usando l'indirizzo IP statico

Prima di creare un servizio, verificare che l'entità servizio usata dal cluster AKS abbia delegato le autorizzazioni per l'altro gruppo di risorse. Ad esempio:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

In alternativa, è possibile usare l'identità gestita assegnata dal sistema per le autorizzazioni anziché l'entità servizio. Per ulteriori informazioni, vedere [Utilizzare le identità gestite](use-managed-identity.md).

Per creare un servizio *LoadBalancer* con l'indirizzo IP pubblico statico, aggiungere la `loadBalancerIP` proprietà e il valore dell'indirizzo IP pubblico statico al manifesto YAML. Creare un file denominato `load-balancer-service.yaml` e copiarlo nel codice YAML seguente. Aggiungere il proprio indirizzo IP pubblico creato nel passaggio precedente. Nell'esempio seguente viene inoltre impostata l'annotazione sul gruppo di risorse denominato *myResourceGroup*. Specificare il nome del gruppo di risorse.

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

## <a name="apply-a-dns-label-to-the-service"></a>Applicare un'etichetta DNS al servizio

Se il servizio usa un indirizzo IP pubblico statico o `service.beta.kubernetes.io/azure-dns-label-name` dinamico, è possibile usare l'annotazione del servizio per impostare un'etichetta DNS pubblica. In questo modo viene pubblicato un nome di dominio completo per il servizio usando i server DNS pubblici e il dominio di primo livello di Azure.This publishs a fully qualified domain name for your service using Azure's public DNS servers and top-level domain. Il valore dell'annotazione deve essere univoco all'interno della posizione di Azure, pertanto è consigliabile usare un'etichetta sufficientemente qualificata.   

Azure aggiungerà automaticamente una subnet `<location>.cloudapp.azure.com` predefinita, ad esempio (dove percorso è l'area selezionata), al nome specificato per creare il nome DNS completo. Ad esempio:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Per pubblicare il servizio nel proprio dominio, vedere DNS di [Azure][azure-dns-zone] e il progetto [dns esterno.][external-dns]

## <a name="troubleshoot"></a>Risolvere i problemi

Se l'indirizzo IP statico definito nella proprietà *loadBalancerIP* del manifesto del servizio Kubernetes non esiste o non è stato creato nel gruppo di risorse del nodo e non sono state configurate altre deleghe, la creazione del servizio di bilanciamento del carico non riesce. Per risolvere il problema, rivedere gli eventi di creazione del servizio con il comando [kubectl describe][kubectl-describe]. Specificare il nome del servizio indicato nel manifesto YAML, come illustrato nell'esempio seguente:

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
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

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
