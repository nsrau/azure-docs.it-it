---
title: Creare un bilanciamento del carico interno
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare e usare un servizio di bilanciamento del carico interno per esporre i servizi con il servizio Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 4decd66a558b031f1aaaf9c64556dae545ed05d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668419"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Usare un servizio di bilanciamento del carico interno con il servizio Azure Kubernetes

Per limitare l'accesso alle applicazioni nel servizio Azure Kubernetes, è possibile creare e usare un bilanciamento del carico interno. Un bilanciamento del carico interno rende accessibile un servizio Kubernetes solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster Kubernetes. Questo articolo descrive come usare un servizio di bilanciamento del carico interno con il servizio Azure Kubernetes.

> [!NOTE]
> Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, lo SKU Standard viene usato quando si crea un cluster AKS.  Quando si crea un servizio con tipo LoadBalancer, si otterrà lo stesso tipo LB di quando si esegue il provisioning del cluster. Per altre informazioni, vedere [Confronto tra SKU di Load Balancer][azure-lb-comparison].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È inoltre necessaria l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

L'entità servizio cluster AKS deve disporre dell'autorizzazione per gestire le risorse di rete se si usa una subnet o un gruppo di risorse esistente. In generale, assegnare il ruolo *Collaboratore di rete* all'entità servizio nelle risorse delegate. Per altre informazioni sulle autorizzazioni, vedere [Delegare l'accesso AKS ad altre risorse][aks-sp]di Azure.For more information on permissions, see Delegate AKS access to other Azure resources.

## <a name="create-an-internal-load-balancer"></a>Creare un bilanciamento del carico interno

Per creare un bilanciamento del carico interno, creare un manifesto del servizio denominato `internal-lb.yaml` con il tipo di servizio *LoadBalancer* e l'annotazione *azure-load-balancer-internal* come illustrato nell'esempio seguente:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Distribuire il servizio di bilanciamento del carico interno usando l'applicazione kubectl e specificare il nome del manifesto YAML:Deploy the internal load balancer using the [kubectl apply][kubectl-apply] and specify the name of your YAML manifest:

```console
kubectl apply -f internal-lb.yaml
```

Un servizio di bilanciamento del carico di Azure viene creato nel gruppo di risorse del nodo e connesso alla stessa rete virtuale del cluster AKS.

Quando si visualizzano i dettagli del servizio, l'indirizzo IP del bilanciamento del carico interno viene visualizzato nella colonna *EXTERNAL-IP*. In questo contesto, *External* è in relazione all'interfaccia esterna del servizio di bilanciamento del carico, non che riceva un indirizzo IP esterno pubblico. La modifica dell'indirizzo IP da * \<in sospeso\> * a un indirizzo IP interno effettivo, come illustrato nell'esempio seguente, potrebbe essere necessario un o due minuti:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Specificare un indirizzo IP

Se si vuole usare un indirizzo IP specifico con il bilanciamento del carico interno, aggiungere la proprietà *loadBalancerIP* nel manifesto YAML del bilanciamento del carico. L'indirizzo IP specificato deve trovarsi nella stessa subnet del cluster del servizio Azure Container e non deve essere già assegnato a una risorsa.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Quando viene distribuito e si visualizzano i dettagli del servizio, l'indirizzo IP nella colonna *EXTERNAL-IP* riflette l'indirizzo IP specificato:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Usare le reti private

Quando si crea il cluster servizio Azure Kubernetes è possibile specificare impostazioni di rete avanzate. Questo approccio consente di distribuire il cluster in una rete virtuale di Azure esistente e nelle subnet. Uno scenario consiste nella distribuzione del cluster servizio Azure Kubernetes in una rete privata connessa all'ambiente locale e nell'esecuzione dei servizi accessibili solo internamente. Per altre informazioni, vedere configurare subnet di rete virtuale personalizzate con [Kubenet][use-kubenet] o [Azure CNI.][advanced-networking]

Non sono necessarie modifiche dei passaggi precedenti per distribuire un servizio di bilanciamento del carico interno in un cluster servizio Azure Kubernetes che usa una rete privata. Il servizio di bilanciamento del carico viene creato nello stesso gruppo di risorse del cluster servizio Azure Kubernetes ma è connesso alla rete virtuale e alla subnet private, come illustrato nell'esempio seguente:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Può essere necessario concedere all'entità servizio per il cluster servizio Azure Kubernetes il ruolo *Collaboratore di rete* per il gruppo di risorse in cui vengono distribuite le risorse della rete virtuale di Azure. Visualizzare l'entità servizio con [az servizio Azure Kubernetes show][az-aks-show], ad esempio `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Per creare un'assegnazione di ruolo, usare il comando [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Specificare una subnet diversa

Per specificare una subnet per il bilanciamento del carico, aggiungere l'annotazione *azure-load-balancer-internal-subnet* al servizio. La subnet specificata deve essere nella stessa rete virtuale del cluster servizio Azure Kubernetes. Quando viene distribuito, l'indirizzo *EXTERNAL-IP* del servizio di bilanciamento del carico fa parte della subnet specificata.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Eliminare il servizio di bilanciamento del carico

Quando vengono eliminati tutti i servizi che usano il servizio di bilanciamento del carico interno, viene eliminato anche tale servizio.

È anche possibile eliminare direttamente un servizio come per qualsiasi risorsa di Kubernetes, ad esempio `kubectl delete service internal-app`, operazione che comporta anche l'eliminazione del bilanciamento del carico sottostante.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi Kubernetes sono disponibili nella [documentazione relativa ai servizi Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
