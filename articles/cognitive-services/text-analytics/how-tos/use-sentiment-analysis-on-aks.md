---
title: Eseguire il servizio Azure Kubernetes-Analisi del testo
titleSuffix: Azure Cognitive Services
description: Distribuire i contenitori di Analisi del testo con l'immagine di analisi del sentimento nel servizio Azure Kubernetes e testarli in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552274"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>Distribuire un contenitore di analisi dei sentimenti nel servizio Azure Kubernetes

Informazioni su come distribuire i servizi cognitivi di Azure [analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) contenitore con l'immagine dell'analisi dei sentimenti per il servizio Azure KUBERNETES (AKS). In questa procedura viene illustrato come creare una risorsa Analisi del testo, come creare un'immagine di analisi del sentimento associata e come eseguire questa orchestrazione dei due da un browser. L'uso di contenitori può spostare l'attenzione dalla gestione dell'infrastruttura al invece di concentrarsi sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede diversi strumenti che devono essere installati ed eseguiti in locale. Non usare Azure Cloud Shell. Sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un editor di testo, ad esempio [Visual Studio Code](https://code.visualstudio.com/download).
* INTERFACCIA della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installata.
* INTERFACCIA della riga di comando di [Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installata.
* Una risorsa di Azure con il piano tariffario corretto. Non tutti i piani tariffari funzionano con questo contenitore:
    * Risorsa **analisi del testo di Azure** con solo piani tariffari F0 o standard.
    * Risorsa **Servizi cognitivi di Azure** con il piano tariffario s0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>Distribuire un contenitore Analisi del testo in un cluster AKS

1. Aprire l'interfaccia della riga di comando di Azure e accedere ad Azure.

    ```azurecli
    az login
    ```

1. Accedere al cluster AKS. Sostituire `your-cluster-name` e`your-resource-group` con i valori appropriati.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Dopo l'esecuzione di questo comando, viene segnalato un messaggio simile al seguente:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se sono disponibili più sottoscrizioni per l'account Azure e il `az aks get-credentials` comando restituisce un errore, un problema comune è che si sta usando una sottoscrizione non corretta. Impostare il contesto della sessione dell'interfaccia della riga di comando di Azure in modo che usi la stessa sottoscrizione con cui sono state create le risorse e riprovare.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Aprire l'editor di testo desiderato. In questo esempio viene usato Visual Studio Code.

    ```azurecli
    code .
    ```

1. Nell'editor di testo creare un nuovo file denominato _sentimenti. YAML_e incollare il file YAML seguente. Assicurarsi di sostituire `billing/value` e `apikey/value` con le proprie informazioni.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Salvare il file e chiudere l'editor di testo.
1. Eseguire il comando `apply` Kubernetes con _sentimento. YAML_ come destinazione:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Quando il comando applica correttamente la configurazione della distribuzione, viene visualizzato un messaggio simile all'output seguente:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Verificare che il POD sia stato distribuito:

    ```console
    kubectl get pods
    ```

    Output per lo stato di esecuzione del Pod:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verificare che il servizio sia disponibile e ottenere l'indirizzo IP.

    ```console
    kubectl get services
    ```

    Output per lo stato di esecuzione _del servizio di_ valutazione nel pod:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Passaggi successivi

* Usare più [contenitori di servizi cognitivi](../../cognitive-services-container-support.md)
* Usare il [servizio analisi del testo connesso](../vs-text-connected-service.md)
