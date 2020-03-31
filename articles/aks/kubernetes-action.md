---
title: Creare, testare e distribuire contenitori nel servizio Azure Kubernetes usando le azioni GitHubBuild, test, and deploy containers to Azure Kubernetes Service using GitHub Actions
description: Scopri come usare le azioni GitHub per distribuire il contenitore in Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595366"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Azioni per la distribuzione al servizio Kubernetes

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita dello sviluppo software. L'azione [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) Kubernetes facilita le distribuzioni nei cluster di servizi Azure Kubernetes.The Kubernetes action facilitate deployments to Azure Kubernetes Service clusters. L'azione imposta il contesto del cluster AKS di destinazione, che può essere usato da altre azioni, ad esempio [azure/k8s-deploy](https://github.com/Azure/k8s-deploy/tree/master), [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) e così via, oppure esegue comandi kubectl.

Un flusso di lavoro è definito da un file `/.github/workflows/` YAML (.yml) nel percorso nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro destinato ad AKS, il file include tre sezioni:For a workflow targeting AKS, the file has three sections:

|Sezione  |Attività  |
|---------|---------|
|**Autenticazione** | Accedere a un registro contenitori privati (ACR) |
|**Costruire** | Compilare & eseguire il push dell'immagine contenitoreBuild is push the container image  |
|**Distribuire** | 1. Impostare il cluster AKS di destinazione |
| |2. Creare un segreto generico/docker-registry nel cluster Kubernetes  |
||3. Distribuire nel cluster Kubernetes|

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un'entità servizio usando il comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/) [service principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure o selezionando il pulsante **Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Nel comando precedente sostituire i segnaposto con l'ID sottoscrizione e il gruppo di risorse. L'output è le credenziali di assegnazione di ruolo che forniscono l'accesso alla risorsa. Il comando deve restituire un oggetto JSON simile al seguente.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copiare questo oggetto JSON, che è possibile usare per l'autenticazione da GitHub.Copy this JSON object, which you can use to authenticate from GitHub.

## <a name="configure-the-github-secrets"></a>Configurare i segreti di GitHubConfigure the GitHub secrets

Seguire i passaggi per configurare i segreti:

1. In [GitHub](https://github.com/), passare al repository, selezionare **Impostazioni > Segreti > Aggiungere un nuovo segreto**.

    ![chiavi private](media/kubernetes-action/secrets.png)

2. Incollare il contenuto `az cli` del comando precedente come valore della variabile segreta. Ad esempio: `AZURE_CREDENTIALS`.

3. Analogamente, definire i seguenti segreti aggiuntivi per le credenziali del Registro di sistema del contenitore e impostarli nell'azione di accesso Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Vedrai i segreti come mostrato di seguito una volta definito.

    ![kubernetes-segreti](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Creare un'immagine del contenitore e distribuirla al cluster di servizi Azure KubernetesBuild a container image and deploy to Azure Kubernetes Service cluster

La compilazione e il push `Azure/docker-login@v1` delle immagini del contenitore vengono eseguite usando l'azione. Per distribuire un'immagine contenitore in AKS, è necessario utilizzare l'azione. `Azure/k8s-deploy@v1` Questa azione ha cinque parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **namespace** | (Facoltativo) Scegliere lo spazio dei nomi Kubernetes di destinazione. Se lo spazio dei nomi non viene fornito, i comandi verranno eseguiti nello spazio dei nomi predefinito | 
| **Manifesti** |  (Obbligatorio) Percorso dei file manifesto che verranno utilizzati per la distribuzione |
| **Immagini** | (Facoltativo) URL di risorse completo delle immagini da utilizzare per le sostituzioni nei file manifesto |
| **imagepullsecrets** | (Facoltativo) Nome di un segreto docker-registry già impostato all'interno del cluster. Ognuno di questi nomi segreti viene aggiunto nel campo imagePullSecrets per i carichi di lavoro presenti nei file manifesto di input |
| **kubectl-versione** | (Facoltativo) Installa una versione specifica di kubectl binary |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Distribuire nel cluster del servizio Azure KubernetesDeploy to Azure Kubernetes Service cluster

Flusso di lavoro end-to-end per la creazione di immagini del contenitore e la distribuzione in un cluster di servizi Azure Kubernetes.End to end workflow for building container images and deploying to an Azure Kubernetes Service cluster.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare il set di azioni in repository diversi in GitHub, ognuno contenente documentazione ed esempi per usare GitHub per CI/CD e distribuire le app in Azure.You can find our set of Actions in different repositories on GitHub, each each containing documentation and examples to help you use GitHub for CI/CD and deploy your apps to Azure.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-context](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-creare-segreto](https://github.com/Azure/k8s-create-secret)

- [k8s-distribuzione](https://github.com/Azure/k8s-deploy)

- [webapps-container-deploy](https://github.com/Azure/webapps-container-deploy)

- [actions-workflow-samples](https://github.com/Azure/actions-workflow-samples)
