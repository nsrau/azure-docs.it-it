---
title: Consente di compilare, testare e distribuire i contenitori nel servizio Azure Kubernetes usando le azioni di GitHub
description: Informazioni su come usare le azioni di GitHub per distribuire il contenitore in Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: a0f64b0d19dd3f65d883237e9ead2c9f1303adaf
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95794779"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Azioni di GitHub per la distribuzione nel servizio Kubernetes

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. È possibile usare più azioni Kubernetes per la distribuzione in contenitori da Azure Container Registry al servizio Kubernetes di Azure con azioni di GitHub. 

## <a name="prerequisites"></a>Prerequisiti 

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un account GitHub. Se non è disponibile, iscriversi per riceverne uno [gratuito](https://github.com/join).  
- Un cluster Kubernetes funzionante
    - [Esercitazione: preparare un'applicazione per il servizio Azure Kubernetes](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro che ha come destinazione AKS, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | Accedere a un registro contenitori privato (ACR) |
|**Build** | Compilare & eseguire il push dell'immagine del contenitore  |
|**Distribuzione** | 1. impostare il cluster AKS di destinazione |
| |2. creare un segreto del registro di sistema generico/Docker nel cluster Kubernetes  |
||3. eseguire la distribuzione nel cluster Kubernetes|

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) nell'[interfaccia della riga di comando di Azure](/cli/azure/). È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Nel comando precedente sostituire i segnaposto con l'ID sottoscrizione e il gruppo di risorse. L'output è costituito dalle credenziali di assegnazione di ruolo che forniscono l'accesso alla risorsa. Il comando deve restituire un oggetto JSON simile al seguente.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copiare questo oggetto JSON, che potrà essere usato per eseguire l'autenticazione da GitHub.

## <a name="configure-the-github-secrets"></a>Configurare i segreti GitHub

Seguire i passaggi per configurare i segreti:

1. In [GitHub](https://github.com/)passare al repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**.

    ![Screenshot mostra il collegamento Aggiungi un nuovo segreto per un repository.](media/kubernetes-action/secrets.png)

2. Incollare il contenuto del comando precedente `az cli` come valore della variabile segreta. Ad esempio: `AZURE_CREDENTIALS`.

3. Analogamente, definire i seguenti segreti aggiuntivi per le credenziali del registro contenitori e impostarli in azione Docker login. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. I segreti vengono visualizzati come mostrato di seguito, una volta definiti.

    ![Screenshot mostra i segreti esistenti per un repository.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Compilare un'immagine del contenitore e distribuirla nel cluster del servizio Kubernetes di Azure

La compilazione e il push delle immagini del contenitore vengono eseguiti usando l' `Azure/docker-login@v1` azione. 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Eseguire la distribuzione nel cluster del servizio Kubernetes di Azure

Per distribuire un'immagine del contenitore in AKS, sarà necessario usare l' `Azure/k8s-deploy@v1` azione. Questa azione ha cinque parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **namespace** | Opzionale Scegliere lo spazio dei nomi Kubernetes di destinazione. Se lo spazio dei nomi non viene specificato, i comandi vengono eseguiti nello spazio dei nomi predefinito | 
| **manifesti** |  Necessaria Percorso dei file manifesto che verrà usato per la distribuzione |
| **images** | Opzionale URL completo della risorsa delle immagini da usare per le sostituzioni nei file manifesto |
| **imagepullsecrets** | Opzionale Nome di un segreto del registro Docker già configurato all'interno del cluster. Ognuno di questi nomi di segreto viene aggiunto nel campo imagePullSecrets per i carichi di lavoro trovati nei file manifesto di input |
| **kubectl-versione** | Opzionale Installa una versione specifica di kubectl Binary |


Prima di poter eseguire la distribuzione in AKS, è necessario impostare lo spazio dei nomi Kubernetes di destinazione e creare un segreto di pull immagine. Vedere eseguire [il pull delle immagini da un registro contenitori di Azure a un cluster Kubernetes](../container-registry/container-registry-auth-kubernetes.md)per altre informazioni sul funzionamento del pull delle immagini. 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


Completare la distribuzione con l' `k8s-deploy` azione. Sostituire le variabili di ambiente con i valori per l'applicazione. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Pulizia delle risorse

Quando il cluster Kubernetes, il registro contenitori e il repository non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse e il repository GitHub. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sul servizio Azure Kubernetes](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Altre azioni di GitHub Kubernetes

* [Programma di installazione dello strumento Kubectl](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): installa una versione specifica di Kubectl nel Runner.
* [Kubernetes set context](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): impostare il contesto del cluster Kubernetes di destinazione che verrà usato da altre azioni o eseguire qualsiasi comando kubectl.
* [Contesto del set AKS](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): impostare il contesto del cluster del servizio Kubernetes di Azure di destinazione.
* [Kubernetes create Secret](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): crea un segreto generico o un segreto del registro Docker nel cluster Kubernetes.
* [Kubernetes deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): consente di preparare e distribuire manifesti nei cluster Kubernetes.
* [Installazione Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): installare una versione specifica di Helm Binary nel Runner.
* [Kubernetes Bake](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): consente di preparare il file manifesto da usare per le distribuzioni usando helm2, kustomize o kompose.
* [Kubernetes pelucchi](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): convalida/inpelucchi i file manifesto.
