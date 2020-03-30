---
title: Distribuire un gateway di Gestione API di Azure self-hosted a Kubernetes Documenti Microsoft
description: Informazioni su come distribuire un gateway di Gestione API di Azure self-hosted a Kubernetes
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513835"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Distribuire un gateway di Gestione API di Azure self-hosted a KubernetesDeploy a self-hosted Azure API Management gateway to Kubernetes

Questo articolo illustra i passaggi per la distribuzione del gateway di Gestione API di Azure self-hosted in un cluster Kubernetes.This article provides the steps for deploying self-hosted Azure API Management gateway into a Kubernetes Cluster.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello sviluppatore è limitato a una singola distribuzione di gateway self-hosted.


## <a name="prerequisites"></a>Prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza](get-started-create-service-instance.md) di Gestione API di AzureComplete the following quickstart: Create an Azure API Management instance
- Creare un cluster Kubernetes.Create a Kubernetes cluster. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) è una buona opzione per scopi di sviluppo e valutazione. Per i carichi di lavoro di produzione, è possibile usare [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) o un cluster Kubernetes in un cloud esterno.
- [Effettuare il provisioning di una risorsa gateway nell'istanza](api-management-howto-provision-self-hosted-gateway.md)di Gestione API.

## <a name="deploy-the-gateway-to-kubernetes"></a>Distribuire il gateway a Kubernetes

1. Selezionare **Gateway** in **Impostazioni**.
2. Selezionare la risorsa gateway che si desidera distribuire.
3. Selezionare **Distribuzione**.
4. Si noti che un nuovo token nella casella di testo **Token** è stato generato automaticamente utilizzando i valori predefiniti **Di scadenza** e **Chiave segreta.** Regolare uno o entrambi, se lo si desidera, e selezionare **Genera** per creare un nuovo token.
5. Assicurarsi che **Kubernetes** sia selezionato in **Script di distribuzione**.
6. Selezionare<collegamento al **file nome-gateway>.yml** accanto a **Distribuzione** per scaricare il file.
7. Modificare i mapping delle porte e il nome del contenitore nel file yml in base alle esigenze.
8. Selezionare l'icona di **copia** all'estremità destra `kubectl` della casella di testo **Distribuisci** per salvare il comando negli Appunti. 
9. Incollare il comando nella finestra del terminale (o del comando). Si noti che il comando prevede che il file di ambiente scaricato sia presente nella directory corrente.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Eseguire il comando. Il comando indica al cluster Kubernetes di eseguire il contenitore, utilizzando l'immagine del gateway self-hosted scaricata dal Registro di sistema del contenitore Microsoft, e di configurare il contenitore per esporre le porte HTTP (8080) e HTTPS (443).
11. Eseguire il comando seguente per verificare che il pod del gateway sia in esecuzione. Si noti che il nome del contenitore sarà diverso. 
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
13. Tornare al portale di Azure e verificare che il nodo del gateway appena distribuito stia segnalando lo stato integro.

![stato del gateway](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Utilizzare <code>kubectl logs <gateway-pod-name></code> il comando per visualizzare uno snapshot del log del gateway indipendente.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere Panoramica del gateway self-hosted di Gestione API di AzureTo learn more about the self-hosted gateway, see [Azure API Management self-hosted gateway overview](self-hosted-gateway-overview.md)
* Altre informazioni sul [servizio Azure KubernetesLearn more about Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)


