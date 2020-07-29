---
title: Creare un bilanciamento del carico interno
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare e usare un servizio di bilanciamento del carico interno per esporre i servizi con il servizio Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: ec8fd1f1b32d5bba6dc4dc756e1f95f4a74f9a96
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285884"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Usare un servizio di bilanciamento del carico interno con il servizio Azure Kubernetes

Per limitare l'accesso alle applicazioni nel servizio Azure Kubernetes, è possibile creare e usare un bilanciamento del carico interno. Un bilanciamento del carico interno rende accessibile un servizio Kubernetes solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster Kubernetes. Questo articolo descrive come usare un servizio di bilanciamento del carico interno con il servizio Azure Kubernetes.

> [!NOTE]
> Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, quando si crea un cluster del servizio Azure Kubernetes viene usato lo SKU Standard.  Quando si crea un servizio con tipo come LoadBalancer, si otterrà lo stesso tipo di LB di quando si esegue il provisioning del cluster. Per altre informazioni, vedere [Confronto tra SKU di Load Balancer][azure-lb-comparison].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.59 o versione successiva. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

L'entità servizio cluster AKS necessita dell'autorizzazione per gestire le risorse di rete se si usa una subnet o un gruppo di risorse esistente. Per informazioni, vedere [usare la rete kubenet con gli intervalli di indirizzi IP in Azure Kubernetes Service (AKS)][use-kubenet] o [configurare la rete di Azure CNI in Azure KUBERNETES Service (AKS)][advanced-networking]. Se si configura il servizio di bilanciamento del carico per usare un [indirizzo IP in un'altra subnet][different-subnet], assicurarsi che l'entità servizio del cluster AKS disponga anche dell'accesso in lettura a tale subnet.

Anziché un'entità servizio, è anche possibile usare l'identità gestita assegnata dal sistema per le autorizzazioni. Per altre informazioni, vedere [Usare le identità gestite](use-managed-identity.md). Per altre informazioni sulle autorizzazioni, vedere [Delegare l'accesso del servizio Azure Kubernetes ad altre risorse di Azure][aks-sp].

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

Distribuire il servizio di bilanciamento del carico interno usando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f internal-lb.yaml
```

Viene creato un servizio di bilanciamento del carico di Azure nel gruppo di risorse nodo e connesso alla stessa rete virtuale del cluster AKS.

Quando si visualizzano i dettagli del servizio, l'indirizzo IP del bilanciamento del carico interno viene visualizzato nella colonna *EXTERNAL-IP*. In questo contesto, l' *esterno* è correlato all'interfaccia esterna del servizio di bilanciamento del carico, non alla ricezione di un indirizzo IP pubblico esterno. La modifica dell'indirizzo IP da *\<pending\>* a un indirizzo IP interno effettivo può richiedere un paio di minuti, come illustrato nell'esempio seguente:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Specificare un indirizzo IP

Se si vuole usare un indirizzo IP specifico con il bilanciamento del carico interno, aggiungere la proprietà *loadBalancerIP* nel manifesto YAML del bilanciamento del carico. In questo scenario, l'indirizzo IP specificato deve trovarsi nella stessa subnet del cluster AKS e non deve essere già assegnato a una risorsa. Ad esempio, non è consigliabile usare un indirizzo IP nell'intervallo definito per la subnet Kubernetes.

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

Quando si distribuisce e si visualizzano i dettagli del servizio, l'indirizzo IP nella colonna *External-IP* riflette l'indirizzo IP specificato:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

Per altre informazioni sulla configurazione del servizio di bilanciamento del carico in un'altra subnet, vedere [specificare una subnet diversa][different-subnet] .

## <a name="use-private-networks"></a>Usare le reti private

Quando si crea il cluster servizio Azure Kubernetes è possibile specificare impostazioni di rete avanzate. Questo approccio consente di distribuire il cluster in una rete virtuale di Azure esistente e nelle subnet. Uno scenario consiste nella distribuzione del cluster servizio Azure Kubernetes in una rete privata connessa all'ambiente locale e nell'esecuzione dei servizi accessibili solo internamente. Per altre informazioni, vedere Configurare le subnet della rete virtuale con [Kubenet][use-kubenet] o [Azure CNI][advanced-networking].

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
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet