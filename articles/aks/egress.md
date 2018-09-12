---
title: Configurare l'elenco elementi consentiti per il traffico in uscita da un cluster Azure Kubernetes Service (AKS)
description: Configurare l'elenco elementi consentiti per il traffico in uscita da un cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: e2793a72fcbc20b79bdd564e331426fedf1ae34b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347801"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Uscita da Azure Kubernetes Service (AKS)

Per impostazione predefinita, l'indirizzo in uscita da un cluster Azure Kubernetes Service (AKS) viene assegnato in modo casuale. Questa configurazione non è ideale quando è necessario identificare un indirizzo IP per l'accesso a servizi esterni. Questo documento illustra in dettaglio come creare e gestire un indirizzo IP in uscita assegnato in modo statico in un cluster AKS.

## <a name="egress-overview"></a>Panoramica del flusso in uscita

Il traffico in uscita da un cluster AKS segue le convenzioni di Azure Load Balancer, documentate [qui][outbound-connections]. Prima che venga creato il primo servizio Kubernetes di tipo `LoadBalancer`, i nodi dell'agente non appartengono ad alcun pool di Azure Load Balancer. In questa configurazione i nodi sono privi di indirizzo IP pubblico a livello di istanza. Azure trasla il flusso in uscita a un indirizzo IP pubblico di origine che non è configurabile o deterministico.

Dopo che è stato creato un servizio Kubernetes di tipo `LoadBalancer`, i nodi dell'agente vengono aggiunti a un pool di Azure Load Balancer. Azure trasla il flusso in uscita al primo indirizzo IP pubblico che è stato configurato nel bilanciamento del carico.

## <a name="create-a-static-public-ip"></a>Creare un IP pubblico statico

Per evitare che siano usati indirizzi IP casuali, creare un indirizzo IP statico e assicurarsi che il bilanciamento del carico usi questo indirizzo. L'indirizzo IP deve essere creato nel gruppo di risorse del **nodo** AKS.

Ottenere il nome del gruppo di risorse con il comando [az resource show][az-resource-show]. Aggiornare il nome del gruppo di risorse e il nome del cluster affinché corrispondano al proprio ambiente.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Quindi usare il comando [az network public-ip create][public-ip-create] per creare un indirizzo IP pubblico statico. Aggiornare il nome del gruppo di risorse in modo che corrisponda al nome ottenuto nell'ultimo passaggio.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Creare un servizio con l'indirizzo IP statico

Ora che si dispone di un indirizzo IP, creare un servizio Kubernetes con il tipo `LoadBalancer` e assegnare l'indirizzo IP al servizio.

Creare un file denominato `egress-service.yaml` e copiarlo nel codice YAML seguente. Aggiornare l'indirizzo IP in modo corrispondente al proprio ambiente.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

Creare il servizio e la distribuzione con il comando `kubectl apply`.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

Creando questo servizio viene configurato un nuovo indirizzo IP front-end in Azure Load Balancer. Se non sono configurati altri indirizzi IP, ora **tutto** il traffico in uscita dovrebbe usare questo indirizzo. Quando sono configurati più indirizzi in Azure Load Balancer, il flusso in uscita usa il primo indirizzo IP di questo bilanciamento del carico.

## <a name="verify-egress-address"></a>Verificare l'indirizzo in uscita

Per verificare che l'indirizzo IP pubblico sia in uso, utilizzare un servizio come `checkip.dyndns.org`.

Avviare e connettersi a un pod:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

Se necessario, installare curl nel contenitore:

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org`, che restituisce l'indirizzo IP in uscita:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Si noterà che l'indirizzo IP corrisponde all'indirizzo IP statico associato al bilanciamento del carico di Azure.

## <a name="ingress-controller"></a>Controller di ingresso

Per evitare di mantenere più indirizzi IP pubblici in Azure Load Balancer, provare a usare un controller di ingresso. I controller di ingresso forniscono vantaggi come il bilanciamento del carico, la terminazione SSL/TLS, il supporto per la riscrittura degli URI e la crittografia SSL/TLS a monte. Per altre informazioni sui controller di ingresso in AKS, vedere la guida sulla [configurazione del controller di ingresso NGINX in un cluster AKS][ingress-aks-cluster].

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul software illustrato in questo documento.

- [Interfaccia della riga di comando di Helm][helm-cli-install]
- [Controller di ingresso NGINX][nginx-ingress]
- [Connessioni in uscita di Azure Load Balancer][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
