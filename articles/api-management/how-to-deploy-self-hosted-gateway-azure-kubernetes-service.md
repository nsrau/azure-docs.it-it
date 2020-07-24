---
title: Distribuire un gateway self-hosted nel servizio Azure Kubernetes | Microsoft Docs
description: Informazioni su come distribuire un componente gateway self-hosted di gestione API di Azure al servizio Azure Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87015222"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Distribuire nel servizio Azure Kubernetes

Questo articolo illustra la procedura per la distribuzione del componente gateway self-hosted di gestione API di Azure al [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/). Per la distribuzione di un gateway self-hosted in un cluster Kubernetes, vedere questo[documento](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Prerequisiti

- [Creare un'istanza di gestione API di Azure](get-started-create-service-instance.md)
- [Creare un cluster Azure Kubernetes](../aks/kubernetes-walkthrough-portal.md)
- Effettuare il [provisioning di una risorsa gateway nell'istanza di gestione API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Distribuire il gateway self-hosted in AKS

1. Selezionare **gateway** da **distribuzione e infrastruttura**.
2. Selezionare la risorsa del gateway self-hosted che si intende distribuire.
3. Selezionare **distribuzione**.
4. Si noti che per l'utente è stato generato automaticamente un nuovo token nella casella di testo **token** usando i valori predefiniti per la **scadenza** e la **chiave privata** . Se lo si desidera, modificare uno o entrambi gli elementi e selezionare **genera** per creare un nuovo token.
5. Assicurarsi che **Kubernetes** sia selezionato in **script di distribuzione**.
6. Per scaricare il file, selezionare **<gateway-name> file con estensione YML** accanto a **Deployment** .
7. Modificare i mapping delle porte e il nome del contenitore nel file yml in base alle esigenze.
8. A seconda dello scenario, potrebbe essere necessario modificare il tipo di [servizio](../aks/concepts-network.md#services). Il valore predefinito è `NodePort`.
9. Selezionare l'icona di **copia** che si trova all'estremità destra della casella di testo **Distribuisci** per salvare il `kubectl` comando negli Appunti.
10. Incollare il comando nella finestra terminale (o comando). Si noti che il comando prevede che il file dell'ambiente scaricato sia presente nella directory corrente.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Eseguire il comando. Il comando indica al cluster AKS di eseguire il contenitore usando l'immagine del gateway self-hosted scaricato da Microsoft Container Registry e di configurare il contenitore per esporre le porte HTTP (8080) e HTTPS (443).
12. Eseguire il comando seguente per verificare che il pod del gateway sia in esecuzione. Si noti che il nome del pod sarà diverso.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Eseguire il comando seguente per verificare che il servizio gateway sia in esecuzione. Si noti che il nome del servizio e gli indirizzi IP saranno diversi.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Tornare alla portale di Azure e verificare che il nodo del gateway appena distribuito stia segnalando lo stato integro.

> [!TIP]
> Usare <code>kubectl logs <gateway-pod-name></code> il comando per visualizzare uno snapshot del log del gateway self-hosted.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md)
* Scopri di più sul [servizio Azure Kubernetes](../aks/intro-kubernetes.md)
* Informazioni [su come configurare e salvare in modo permanente i log nel cloud](how-to-configure-cloud-metrics-logs.md)
* * Informazioni [su come configurare e salvare in locale i log](how-to-configure-local-metrics-logs.md)
