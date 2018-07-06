---
title: Creare un servizio di bilanciamento del carico interno di Azure Kubernetes Service (AKS)
description: Usare un servizio di bilanciamento del carico interno con Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7606ce574c7ff94caef3ffa89320d682b22d8502
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097921"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Usare un servizio di bilanciamento del carico interno con Azure Kubernetes Service (AKS)

Il bilanciamento del carico interno rende accessibile un servizio Kubernetes alle applicazioni in esecuzione nella stessa rete virtuale del cluster Kubernetes. Questo documento illustra come creare un servizio di bilanciamento del carico interno con Azure Kubernetes Service (AKS). Azure Load Balancer è disponibile in due SKU: Basic e Standard, Azure Kubernetes Service (AKS) usa lo SKU Basic.

## <a name="create-internal-load-balancer"></a>Creare un servizio di bilanciamento del carico interno

Per creare un servizio di bilanciamento del carico interno, generare un manifesto del servizio specificando il tipo di servizio `LoadBalancer` e l'annotazione `azure-load-balancer-internal`, come illustrato nell'esempio seguente.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Una volta distribuito, un servizio di bilanciamento del carico di Azure viene creato e reso disponibile nella stessa rete virtuale del cluster del servizio contenitore di Azure.

![Immagine del servizio di bilanciamento del carico interno del servizio contenitore di Azure](media/internal-lb/internal-lb.png)

Quando si recuperano i dettagli del servizio, l'indirizzo IP nella colonna `EXTERNAL-IP` corrisponde all'indirizzo IP del servizio di bilanciamento del carico interno.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Specificare un indirizzo IP

Se si vuole usare un indirizzo IP specifico con il servizio di bilanciamento del carico interno, aggiungere la proprietà `loadBalancerIP` alle specifiche del servizio di bilanciamento del carico. L'indirizzo IP specificato deve trovarsi nella stessa subnet del cluster del servizio contenitore di Azure e non deve essere già assegnato a una risorsa.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Quando si recuperano i dettagli del servizio, l'indirizzo IP nella colonna `EXTERNAL-IP` deve riflettere l'indirizzo IP specificato.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>Eliminare il servizio di bilanciamento del carico

Quando sono stati eliminati tutti i servizi che usano il servizio di bilanciamento del carico interno, viene eliminato anche tale servizio.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi Kubernetes sono disponibili nella [documentazione relativa ai servizi Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
