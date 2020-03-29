---
title: Sentiment Analysis Kubernetes config e distribuire i passaggi
titleSuffix: Azure Cognitive Services
description: Sentiment Analysis Kubernetes config e distribuire i passaggi
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 2a99f85cf861c0c36ffac136cdf1f792b40719b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262270"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Distribuire il contenitore di analisi del sentiment in un cluster AKSDeploy the Sentiment Analysis container to an AKS cluster

1. Aprire l'interfaccia della riga di comando di Azure e accedere ad Azure.Open the Azure CLI, and sign in to Azure.

    ```azurecli
    az login
    ```

1. Accedere al cluster AKS. Sostituire `your-cluster-name` `your-resource-group` e con i valori appropriati.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Dopo l'esecuzione di questo comando, viene segnalato un messaggio simile al seguente:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se nell'account Azure sono disponibili più `az aks get-credentials` sottoscrizioni e il comando restituisce un errore, un problema comune è che si usa la sottoscrizione errata. Impostare il contesto della sessione dell'interfaccia della riga di comando di Azure in modo che utilizzi la stessa sottoscrizione con cui sono state create le risorse e riprovare.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Aprire l'editor di testo preferito. In questo esempio viene utilizzato Visual Studio Code.This example uses Visual Studio Code.

    ```console
    code .
    ```

1. All'interno dell'editor di testo, creare un nuovo file denominato *sentiment.yaml*e incollarvi il seguente YAML. Assicurati di `billing/value` sostituire `apikey/value` e con le tue informazioni.

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
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
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
1. Eseguire il comando Kubernetes `apply` con il file *sentiment.yaml* come destinazione:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Dopo che il comando applica correttamente la configurazione di distribuzione, viene visualizzato un messaggio simile al seguente output:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Verificare che il contenitore sia stato distribuito:

    ```console
    kubectl get pods
    ```

    L'output per lo stato di esecuzione del contenitore:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verificare che il servizio sia disponibile e ottenere l'indirizzo IP.

    ```console
    kubectl get services
    ```

    Output per lo stato di esecuzione del servizio *sentiment* nel pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
