---
title: Analisi del sentiment la configurazione di Kubernetes e i passaggi di distribuzione
titleSuffix: Azure Cognitive Services
description: Analisi del sentiment la configurazione di Kubernetes e i passaggi di distribuzione
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 5d16500b45cbc6190186835d0c793a48fd121bd0
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051876"
---
## <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Distribuire il contenitore Analisi del sentiment in un cluster AKS

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

1. Nell'editor di testo creare un nuovo file denominato *sentimenti. YAML*e incollare il file YAML seguente. Assicurarsi di sostituire `billing/value` e `apikey/value` con le proprie informazioni.

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
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
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
1. Eseguire il comando `apply` Kubernetes con il file *sentimento. YAML* come destinazione:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Quando il comando applica correttamente la configurazione della distribuzione, viene visualizzato un messaggio simile all'output seguente:

    ```console
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Verificare che il POD sia stato distribuito:

    ```console
    kubectl get pods
    ```

    Output per lo stato di esecuzione del Pod:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verificare che il servizio sia disponibile e ottenere l'indirizzo IP.

    ```console
    kubectl get services
    ```

    Output per lo stato di esecuzione del servizio di valutazione nel pod:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
