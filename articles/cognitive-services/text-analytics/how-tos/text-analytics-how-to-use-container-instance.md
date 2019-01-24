---
title: Eseguire il servizio Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Distribuire il contenitore di rilevamento della lingua, con un esempio di esecuzione, nel servizio Azure Kubernetes e testarlo in un Web browser.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 01/11/2019
ms.author: diberry
ms.openlocfilehash: 513067f09d8cac64ca747ff217c84667c5469d82
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248214"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Distribuire il contenitore di rilevamento della lingua nel servizio Azure Kubernetes

Informazioni su come distribuire il contenitore di rilevamento della lingua. Questa procedura illustra come creare i contenitori Docker locali, eseguire il push dei contenitori in un registro contenitori privato, eseguire il contenitore in un cluster Kubernetes e testarlo in un Web browser. 

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede diversi strumenti che devono essere installati ed eseguiti in locale. Non usare Azure Cloud Shell. 

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Git](https://git-scm.com/downloads) per il sistema operativo, in modo che sia possibile clonare l'[esempio](https://github.com/Azure-Samples/cognitive-services-containers-samples) usato in questa procedura. 
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
* [Motore Docker](https://www.docker.com/products/docker-engine) e verificare che l'interfaccia della riga di comando di Docker funzioni in una finestra della console.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe). 
* Una risorsa di Azure con il piano tariffario corretto. Non tutti i piani tariffari funzionano con questo contenitore:
    * Risorsa **Analisi del testo** solo con piano tariffario Standard o F0.
    * Risorsa **Servizi cognitivi** con piano tariffario S0.

## <a name="running-the-sample"></a>Esecuzione dell'esempio

Questa procedura carica ed esegue l'esempio di contenitore di Servizi cognitivi per il rilevamento della lingua. L'esempio include due contenitori, uno per l'applicazione client e uno per il contenitore di Servizi cognitivi. È necessario eseguire il push di entrambe le immagini nel proprio Registro Azure Container. Una volta che si trovano nel proprio registro, creare un servizio Azure Kubernetes per accedere a queste immagini ed eseguire i contenitori. Quando i contenitori sono in esecuzione, usare l'interfaccia della riga di comando di **kubectl** per osservare le prestazioni dei contenitori. Accedere all'applicazione client con una richiesta HTTP e visualizzare i risultati. 



## <a name="the-sample-containers"></a>I contenitori di esempio

L'esempio include due immagini del contenitore, una delle quali per il sito Web front-end. La seconda immagine è il contenitore di rilevamento della lingua che restituisce la lingua rilevata (impostazioni cultura) del testo. Al termine, entrambi i contenitori saranno accessibili da un indirizzo IP esterno. 

### <a name="the-language-frontend-container"></a>Il contenitore della lingua front-end

Questo sito Web è equivalente alla propria applicazione lato client che effettua le richieste dell'endpoint di rilevamento lingua. Al termine della procedura, si ottiene la lingua rilevata di una stringa di caratteri accedendo al contenitore del sito Web in un browser con `http://<external-IP>/<text-to-analyze>`. Un esempio di questo URL è `http://132.12.23.255/helloworld!`. Il risultato nel browser è `English`.

### <a name="the-language-container"></a>Il contenitore della lingua

Il contenitore di rilevamento della lingua, in questa procedura specifica, è accessibile a tutte le richieste esterne. Il contenitore non è stato modificato, in modo che sia disponibile l'API Rilevamento lingua standard di Servizi cognitivi specifica del contenitore. 

Per questo contenitore, tale API è una richiesta POST per il rilevamento della lingua. Come per tutti i contenitori di Servizi cognitivi, altre informazioni sul contenitore sono disponibili nelle relative informazioni Swagger ospitate, `http://<external-IP>:5000/swagger/index.html`. 

La porta 5000 è la porta predefinita usata con i contenitori di Servizi cognitivi. 

## <a name="create-azure-container-registry-service"></a>Creare un'istanza del servizio Registro Azure Container

Per distribuire il contenitore nel servizio Azure Kubernetes, le immagini del contenitore devono essere accessibili. Creare il proprio servizio Registro Azure Container per ospitare le immagini. 

1. Accedere all'interfaccia della riga di comando di Azure 

    ```azurecli
    az login
    ```

1. Creare un gruppo di risorse denominato `cogserv-container-rg` per contenere tutte le risorse create in questa procedura.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Creare quindi il Registro Azure Container con il formato del proprio nome e quindi `registry`, ad esempio `pattyregistry`. Non usare trattini o caratteri di sottolineatura nel nome.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Salvare i risultati per ottenere la proprietà **loginServer**. Questa farà parte dell'indirizzo del contenitore ospitato, usato successivamente nel file `language.yml`.

    ```console
    >az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Accedere al registro contenitori. È necessario accedere prima di eseguire il push di immagini nel registro.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Ottenere l'immagine Docker del sito Web 

1. Il codice di esempio usato in questa procedura è nel repository di esempi di contenitori di Servizi cognitivi. Clonare il repository per avere una copia locale dell'esempio.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Dopo aver aggiunto il repository nel computer locale, trovare il sito Web nella directory [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService). Questo sito Web funge da applicazione client che chiama l'API di rilevamento lingua ospitata nel contenitore di rilevamento della lingua.  

1. Compilare l'immagine Docker per questo sito Web. Verificare che la console sia nella directory [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) in cui si trova il documento Dockerfile quando si esegue il comando seguente:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 . 
    ```

    Per tenere traccia della versione del registro contenitori, aggiungere il tag con un formato di versione, ad esempio `v1`. 

1. Eseguire il push dell'immagine nel registro contenitori. L'operazione potrebbe richiedere alcuni minuti. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Se si verifica un errore `unauthorized: authentication required`, accedere con il comando `az acr login --name <your-container-registry-name>`. 

    Al termine del processo, il risultato dovrebbe essere simile al seguente:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Ottenere l'immagine Docker di rilevamento lingua 

1. Eseguire il pull dell'ultima versione dell'immagine Docker nel computer locale. L'operazione potrebbe richiedere alcuni minuti. Se è disponibile una versione più recente di questo contenitore, modificare il valore da `1.1.006770001-amd64-preview` alla versione più recente. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Assegnare all'immagine il tag del registro contenitori. Trovare l'ultima versione e sostituire la versione `1.1.006770001-amd64-preview` se si ha una versione più recente. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Eseguire il push dell'immagine nel registro contenitori. L'operazione potrebbe richiedere alcuni minuti. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Ottenere le credenziali del registro contenitori

I passaggi seguenti servono per ottenere le informazioni necessarie per la connessione del registro contenitori al servizio Azure Kubernetes creato più avanti in questa procedura.

1. Creare un'entità servizio.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Salvare il valore `appId` dei risultati per il parametro assignee nel passaggio 3, `<appId>`. Salvare il `password` per il parametro del segreto client della sezione successiva `<client-secret>`.

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Ottenere l'ID del registro contenitori.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Salvare l'output per il valore del parametro di ambito, `<acrId>`, nel passaggio successivo. L'aspetto sarà simile al seguente:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Salvare il valore completo per il passaggio 3 di questa sezione. 

1. Per concedere l'accesso corretto al cluster AKS per l'uso delle immagini archiviate nel registro contenitori, creare un'assegnazione di ruolo. Sostituire <appId> e <acrId> con i valori raccolti nei due passaggi precedenti.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Creare il servizio Azure Kubernetes

1. Creare il cluster del servizio Azure Kubernetes. Tutti i valori dei parametri provengono dalle sezioni precedenti, ad eccezione del parametro del nome. Scegliere un nome che indichi chi ha creato il servizio e lo scopo, ad esempio `patty-kube`. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Questo passaggio potrebbe richiedere alcuni minuti. Il risultato è: 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Il servizio viene creato, ma non ha ancora il contenitore del sito Web o il contenitore di rilevamento della lingua.  

1. Ottenere le credenziali del cluster Kubernetes. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Caricare la definizione di orchestrazione nel servizio Kubernetes

Questa sezione usa l'interfaccia della riga di comando di **kubectl** per comunicare con il servizio Azure Kubernetes. 

1. Prima di caricare la definizione di orchestrazione, controllare che **kubectl** abbia accesso ai nodi.

    ```console
    kubectl get nodes
    ```

    La risposta ha un aspetto simile al seguente:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Copiare il file seguente e denominarlo `language.yml`. Il file ha una sezione `service` e una sezione `deployment` per ognuno dei due tipi di contenitore, il contenitore del sito Web `language-frontend` e il contenitore di rilevamento `language`. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Modificare le righe nella sezione deployment del contenitore language-frontend in `language.yml` sulla base della tabella seguente per aggiungere il nome della propria immagine del registro contenitori, il segreto client e le impostazioni di analisi del testo.

    Impostazioni di distribuzione del contenitore language-frontend|Scopo|
    |--|--|
    |Riga 32<br> Proprietà `image`|Percorso dell'immagine di language-frontend nel registro contenitori<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Riga 44<br> Proprietà `name`|Segreto del registro contenitori per l'immagine, definito `<client-secret>` in una sezione precedente.|

1. Modificare le righe nella sezione deployment del contenitore language in `language.yml` sulla base della tabella seguente per aggiungere il nome della propria immagine del registro contenitori, il segreto client e le impostazioni di analisi del testo.

    |Impostazioni di distribuzione del contenitore language|Scopo|
    |--|--|
    |Riga 78<br> Proprietà `image`|Percorso dell'immagine di language nel registro contenitori<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Riga 95<br> Proprietà `name`|Segreto del registro contenitori per l'immagine, definito `<client-secret>` in una sezione precedente.|
    |Riga 91<br> Proprietà `apiKey`|Chiave della risorsa di analisi del testo|
    |Riga 92<br> Proprietà `billing`|Endpoint di fatturazione per la risorsa di analisi del testo.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

    Poiché **chiave API** ed **endpoint di fatturazione** vengono impostati nell'ambito della definizione di orchestrazione Kubernetes, il contenitore del sito Web non ha bisogno di conoscere questi valori o passarli come parte della richiesta. Il contenitore del sito Web fa riferimento al contenitore di rilevamento della lingua in base al nome dell'agente di orchestrazione `language`. 

1. Caricare il file di definizione dell'orchestrazione per questo esempio dalla cartella in cui è stato creato e salvato il file `language.yml`. 

    ```console
    kubectl apply -f language.yml
    ```

    La risposta è:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Ottenere gli indirizzi IP esterni dei contenitori

Per i due contenitori, verificare che i servizi `language-frontend` e `language` siano in esecuzione e ottenere l'indirizzo IP esterno. 

```console
kubectl get all
```

```console
> kubectl get all
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Se il valore `EXTERNAL-IP` per il servizio viene visualizzato come in sospeso, eseguire nuovamente il comando finché l'indirizzo IP non viene visualizzato prima di procedere al passaggio successivo. 

## <a name="test-the-language-detection-container"></a>Testare il contenitore di rilevamento della lingua

Aprire un browser e passare all'indirizzo IP esterno del contenitore `language` nella sezione precedente: `http://<external-ip>:5000/swagger/index.html`. È possibile usare la funzionalità `Try it` dell'API e testare l'endpoint di rilevamento della lingua. 

![Visualizzare la documentazione Swagger del contenitore](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testare il contenitore dell'applicazione client

Modificare l'URL nel browser impostando l'indirizzo IP esterno del contenitore `language-frontend` nel formato seguente: `http://<external-ip>/helloworld`. Il testo `helloworld` nelle impostazioni cultura inglese viene stimato come `English`.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il cluster non è più necessario, eliminare il gruppo di risorse di Azure. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informazioni correlate

* [kubectl per gli utenti di Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Passaggi successivi 

> [!div class="nextstepaction"]
> [Usare il servizio connesso di Analisi del testo](../vs-text-connected-service.md)


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->