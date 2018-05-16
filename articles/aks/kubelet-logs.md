---
title: Ottenere i log di kubelet da Azure Kubernetes Service (AKS)
description: Ottenere i log di kubelet dai nodi del cluster Azure Kubernetes Service (AKS)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0467be7e91fdbf4685fc41a375ea86a503e26009
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Ottenere i log di kubelet dai nodi del cluster Azure Kubernetes Service (AKS)

A volte potrebbe essere necessario recuperare i log di kubelet da un nodo di Azure Kubernetes Service (AKS) per operazioni di risoluzione dei problemi. Questo documento illustra un modo per eseguire il pull di questi log.

## <a name="create-an-ssh-connection"></a>Creare una connessione SSH

Per prima cosa, creare una connessione SSH con il nodo da cui occorre eseguire il pull dei log di kubelet. Questa operazione è descritta in dettaglio nel documento [Accesso SSH in nodi del cluster Azure Kubernetes Service (AKS)][aks-ssh].

## <a name="get-kubelet-logs"></a>Ottenere i log di kubelet

Dopo la connessione al nodo, eseguire il comando seguente per eseguire il pull dei log di kubelet.

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

Output di esempio:

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md