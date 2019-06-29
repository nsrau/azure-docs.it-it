---
title: Eseguire i servizi di Azure Kubernetes
titleSuffix: Text Analytics - Azure Cognitive Services
description: Distribuire i contenitori di analitica di testo con l'immagine di analisi del sentiment, in servizi di Kubernetes di Azure ed eseguirne il test in un web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 4d5e1da01be531550915a38bed17dd8e57be907a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454950"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Distribuire un contenitore di analisi del Sentiment per i servizi Kubernetes di Azure (AKS)

Informazioni su come distribuire i servizi cognitivi [Analitica testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) contenitore con l'immagine di analisi del Sentiment per i servizi Kubernetes di Azure (AKS). Questa procedura esemplifica la creazione di una risorsa di Analitica di testo, la creazione di un'immagine di analisi del Sentiment associata e la possibilità di provare questa orchestrazione delle due da un browser. Uso di contenitori può essere spostato l'attenzione degli sviluppatori dalla gestione dell'infrastruttura per invece concentrarsi sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede diversi strumenti che devono essere installati ed eseguiti in locale. Non usare Azure Cloud Shell.

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Editor di testo, ad esempio: [Visual Studio Code](https://code.visualstudio.com/download).
* Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Installare il [della riga di comando di Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
* Una risorsa di Azure con il piano tariffario corretto. Non tutti i piani tariffari funzionano con questo contenitore:
    * Risorsa **Analisi del testo** solo con piano tariffario Standard o F0.
    * Risorsa **Servizi cognitivi** con piano tariffario S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>Distribuire un contenitore di Analitica di testo a un Cluster del servizio contenitore di AZURE

1. Aprire la CLI di Azure e accedere ad Azure

    ```azurecli
    az login
    ```

1. Accedere al cluster servizio contenitore di AZURE (sostituire il `your-cluster-name` e `your-resource-group` con i valori appropriati)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Dopo questo comando viene eseguita, segnala un messaggio simile al seguente:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se si hanno più sottoscrizioni disponibili nel proprio account Azure e il `az aks get-credentials` comando restituisce un errore, un problema comune è che si usi la sottoscrizione non corretta. È sufficiente impostare il contesto della sessione di comando di Azure per usare la stessa sottoscrizione che è stato creato con le risorse e ripetere l'operazione.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Aprire l'editor di testo preferito, (in questo esempio viene usato __Visual Studio Code__):

    ```azurecli
    code .
    ```

1. All'interno dell'editor di testo, creare un nuovo file denominato _sentiment.yaml_ e incollarvi il codice YAML seguente:

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
              value: "https://westus2.api.cognitive.microsoft.com/"
            - name: apikey
              value: "16c12e3419f54ba49a3222177cef781d"
     
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
1. Esecuzione di Kubernetes `apply` comando con il _sentiment.yaml_ come destinazione:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Dopo che il comando è applicata correttamente la configurazione della distribuzione, verrà generato un messaggio simile al seguente:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Verificare che è stato distribuito il POD:

    ```console
    kubectl get pods
    ```

    Non verrà generato lo stato di esecuzione del POD:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verificare che il servizio sia disponibile e ottenere l'indirizzo IP:

    ```console
    kubectl get services
    ```

    Non verrà generato lo stato di esecuzione del _sentiment_ di POD:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Passaggi successivi

* Usare altri [contenitori di Servizi cognitivi](../../cognitive-services-container-support.md)
* Usare il [Analitica testo servizio connesso](../vs-text-connected-service.md)