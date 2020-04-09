---
title: Key Phrase Extraction Kubernetes config e distribuire i passaggi
titleSuffix: Azure Cognitive Services
description: Key Phrase Extraction Kubernetes config e distribuire i passaggi
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6ef7efe3d48fd20c5141803430260a80395faa82
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877830"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Distribuire il contenitore Estrazione di frasi chiave in un cluster AKSDeploy the Key Phrase Extraction container to an AKS cluster

1. Aprire l'interfaccia della riga di comando di Azure e accedere ad Azure.Open the Azure CLI, and sign in to Azure.

    ```azurecli
    az login
    ```

1. Accedere al cluster AKS. Sostituire `your-cluster-name` `your-resource-group` e con i valori appropriati.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Dopo l'esecuzione di questo comando, viene segnalato un messaggio simile al seguente:

    ```output
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

1. All'interno dell'editor di testo, creare un nuovo file denominato *keyphrase.yaml*e incollarvi il seguente YAML. Assicurati di `billing/value` sostituire `apikey/value` e con le tue informazioni.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Salvare il file e chiudere l'editor di testo.
1. Eseguire il comando Kubernetes `apply` con il file *keyphrase.yaml* come destinazione:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Dopo che il comando applica correttamente la configurazione di distribuzione, viene visualizzato un messaggio simile al seguente output:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Verificare che il contenitore sia stato distribuito:

    ```console
    kubectl get pods
    ```

    L'output per lo stato di esecuzione del contenitore:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verificare che il servizio sia disponibile e ottenere l'indirizzo IP.

    ```console
    kubectl get services
    ```

    Output per lo stato di esecuzione del servizio *keyphrase* nel pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
