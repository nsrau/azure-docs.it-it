---
title: Creare un bilanciamento del carico interno nel servizio Kubernetes di Azure (AKS)
description: Informazioni su come creare e usare un servizio di bilanciamento del carico interno per esporre i servizi con Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 042d2ee0f615ce5216fc11152f0f65518ff9bd5c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376380"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Usare un servizio di bilanciamento del carico interno con Azure Kubernetes Service (AKS)

Per limitare l'accesso alle applicazioni nel servizio Kubernetes di Azure (AKS), è possibile creare e usare un bilanciamento del carico interno. Un bilanciamento del carico interno rende accessibile un servizio Kubernetes solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster Kubernetes. Questo articolo descrive come usare un servizio di bilanciamento del carico interno con Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per altre informazioni, vedere [Confronto tra SKU di Load Balancer][azure-lb-comparison]. Servizio Kubernetes di Azure supporta attualmente la SKU *Basic*. Se si vuole usare la SKU *Standard*, è possibile usare [acs-engine][acs-engine] upstream.

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

Dopo la distribuzione con `kubectl apply -f internal-lb.yaml`, un servizio di bilanciamento del carico di Azure viene creato e reso disponibile nella stessa rete virtuale del cluster AKS.

Quando si visualizzano i dettagli del servizio, l'indirizzo IP del bilanciamento del carico interno viene visualizzato nella colonna *EXTERNAL-IP*. Potrebbero essere richiesti un paio di minuti affinché l'indirizzo IP diventi un effettivo indirizzo IP interno dallo stato *\<in sospeso\>*, come mostrato nell'esempio seguente:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Specificare un indirizzo IP

Se si vuole usare un indirizzo IP specifico con il bilanciamento del carico interno, aggiungere la proprietà *loadBalancerIP* nel manifesto YAML del bilanciamento del carico. L'indirizzo IP specificato deve trovarsi nella stessa subnet del cluster del servizio contenitore di Azure e non deve essere già assegnato a una risorsa.

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

Quando si visualizzano i dettagli del servizio, l'indirizzo IP nella colonna *EXTERNAL-IP* riflette l'indirizzo IP specificato:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Usare le reti private

Quando si crea il cluster AKS è possibile specificare impostazioni di rete avanzate. Questo approccio consente di distribuire il cluster in una rete virtuale di Azure esistente e nelle subnet. Uno scenario consiste nella distribuzione del cluster AKS in una rete privata connessa all'ambiente locale e nell'esecuzione dei servizi accessibili solo internamente. Per altre informazioni, vedere l'articolo sulle [configurazioni di rete avanzate in AKS][advanced-networking].

Non sono necessarie modifiche dei passaggi precedenti per distribuire un servizio di bilanciamento del carico interno in un cluster AKS che usa una rete privata. Il servizio di bilanciamento del carico viene creato nello stesso gruppo di risorse del cluster AKS ma è connesso alla rete virtuale e alla subnet private, come illustrato nell'esempio seguente:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Può essere necessario concedere all'entità servizio per il cluster AKS il ruolo *Collaboratore di rete* per il gruppo di risorse in cui vengono distribuite le risorse della rete virtuale di Azure. Visualizzare l'entità servizio con [az aks show][az-aks-show], ad esempio `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Per creare un'assegnazione di ruolo, usare il comando [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Specificare una subnet diversa

Per specificare una subnet per il bilanciamento del carico, aggiungere l'annotazione *azure-load-balancer-internal-subnet* al servizio. La subnet specificata deve essere nella stessa rete virtuale del cluster AKS. Quando viene distribuito, l'indirizzo *EXTERNAL-IP* del servizio di bilanciamento del carico fa parte della subnet specificata.

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
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[acs-engine]: https://github.com/Azure/acs-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-advanced-networking.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus