---
title: Distribuire un gateway di gestione API di Azure self-hosted in Kubernetes | Microsoft Docs
description: Informazioni su come distribuire un gateway di gestione API di Azure self-hosted in Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513835"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Distribuire un gateway di gestione API di Azure self-hosted in Kubernetes

Questo articolo illustra la procedura per la distribuzione del gateway di gestione API di Azure self-hosted in un cluster Kubernetes.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello Developer è limitato a una singola distribuzione del gateway self-hosted.


## <a name="prerequisites"></a>Prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
- Crea un cluster Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) è una soluzione efficace per scopi di sviluppo e valutazione. Per i carichi di lavoro di produzione, è possibile usare il [servizio Kubernetes di Azure](https://azure.microsoft.com/services/kubernetes-service/) o un cluster Kubernetes in un cloud esterno.
- Effettuare il [provisioning di una risorsa gateway nell'istanza di gestione API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Distribuire il gateway in Kubernetes

1. Selezionare **gateway** in **Impostazioni**.
2. Selezionare la risorsa del gateway che si intende distribuire.
3. Selezionare **distribuzione**.
4. Si noti che per l'utente è stato generato automaticamente un nuovo token nella casella di testo **token** usando i valori predefiniti per la **scadenza** e la **chiave privata** . Se lo si desidera, modificare uno o entrambi gli elementi e selezionare **genera** per creare un nuovo token.
5. Assicurarsi che **Kubernetes** sia selezionato in **script di distribuzione**.
6. Per scaricare il file, selezionare **< gateway-name > file con estensione YML** accanto a **Deployment** .
7. Modificare i mapping delle porte e il nome del contenitore nel file yml in base alle esigenze.
8. Selezionare l'icona di **copia** che si trova all'estremità destra della casella di testo **Distribuisci** per salvare il `kubectl` comando negli Appunti. 
9. Incollare il comando nella finestra terminale (o comando). Si noti che il comando prevede che il file dell'ambiente scaricato sia presente nella directory corrente.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Eseguire il comando. Il comando indica al cluster Kubernetes di eseguire il contenitore, usando l'immagine del gateway self-hosted scaricata da Microsoft Container Registry e di configurare il contenitore per esporre le porte HTTP (8080) e HTTPS (443).
11. Eseguire il comando seguente per verificare che il pod del gateway sia in esecuzione. Si noti che il nome del pod sarà diverso. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Eseguire il comando seguente per verificare che il servizio gateway sia in esecuzione. Si noti che il nome del servizio sarà diverso. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Tornare alla portale di Azure e verificare che il nodo del gateway appena distribuito stia segnalando lo stato integro.

![stato del gateway](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Usare <code>kubectl logs <gateway-pod-name></code> comando per visualizzare uno snapshot del log del gateway self-hosted.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md)
* Scopri di più sul [servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)


