---
title: Controllare le distribuzioni di Kubernetes in Azure per l'implementazione di procedure consigliate
description: Informazioni su come controllare l'implementazione delle procedure consigliate nelle distribuzioni nel servizio Azure Kubernetes mediante kube-advisor
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 03c5eb2e32a0a8ec51844511276d9efba5651068
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "65073769"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Verifica delle procedure consigliate di Kubernetes nel cluster

Esistono diverse procedure consigliate da seguire relative alle distribuzioni di Kubernetes per garantire le migliori prestazioni e resilienza per le applicazioni. È possibile usare lo strumento kube-advisor per la ricerca delle distribuzioni che non seguono i suggerimenti.

## <a name="about-kube-advisor"></a>Informazioni su kube-advisor

Lo [strumento kube-advisor][kube-advisor-github] è un singolo contenitore progettato per essere eseguito nel cluster. Esegue una query nel server API Kubernetes per informazioni sulle distribuzioni e restituisce un set di miglioramenti suggeriti.

Lo strumento di kube-advisor può segnalare su richiesta di risorse e i limiti non presente nelle applicazioni PodSpecs per Windows, nonché le applicazioni di Linux, ma solo lo strumento di kube-advisor deve essere pianificato in un pod di Linux. È possibile pianificare un pod per l'esecuzione in un pool di nodi con un sistema operativo specifico utilizzando un [selettore nodo] [ k8s-node-selector] nella configurazione del pod.

> [!NOTE]
> Lo strumento kube-advisor è supportato da Microsoft nel modo più efficiente possibile. I problemi e i suggerimenti devono essere segnalati in GitHub.

## <a name="running-kube-advisor"></a>Esecuzione di kube-advisor

Per eseguire lo strumento in un cluster configurato per il [controllo degli accessi in base al ruolo](azure-ad-integration.md), usare i comandi seguenti. Il primo comando crea un account del servizio Kubernetes. Il secondo comando esegue lo strumento in un pod mediante l'account di tale servizio e configura il pod per l'eliminazione dopo la chiusura. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Se non si usa il controllo degli accessi in base al ruolo, è possibile eseguire il comando come indicato di seguito:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Entro pochi secondi, dovrebbe essere visualizzata una tabella che descrive i possibili miglioramenti per le distribuzioni.

![Output di Kube-advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Controlli eseguiti

Lo strumento convalida diverse procedure consigliate di Kubernetes, ognuna con la procedura di correzione suggerita.

### <a name="resource-requests-and-limits"></a>Richieste di risorse e limiti

Kubernetes supporta la definizione delle [richieste di risorse e dei limiti per le specifiche di pod][kube-cpumem]. La richiesta definisce il valore minimo di CPU e memoria necessario per eseguire il contenitore. Il limite definisce il valore di CPU e memoria massimo che dovrebbe essere consentito.

Per impostazione predefinita, non vengono impostati richieste o limiti nelle specifiche di pod. Questo può causare la sovrapianificazione dei nodi e l'esaurimento dei contenitori. Lo strumento kube-advisor evidenzia i pod senza impostazione delle richieste e dei limiti.

## <a name="cleaning-up"></a>Cleaning up

Se sul cluster è abilitato il controllo degli accessi in base al ruolo, è possibile eseguire la pulizia di `ClusterRoleBinding` dopo aver eseguito lo strumento usando il comando seguente:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Se si esegue lo strumento su un cluster che non è abilitato per il controllo degli accessi in base al ruolo, non è richiesta alcuna pulizia.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere problemi relativi al servizio Azure Kubernetes](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors