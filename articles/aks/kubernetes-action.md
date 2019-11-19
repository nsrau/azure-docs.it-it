---
title: Consente di compilare, testare e distribuire i contenitori nel servizio Azure Kubernetes usando le azioni di GitHub
description: Informazioni su come usare le azioni di GitHub per distribuire il contenitore in Kubernetes
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: cc2d6df952b2e0aa9b9f4d4e1dcb4859a5bb3790
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130539"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Azioni di GitHub per la distribuzione nel servizio Kubernetes

[Azioni di GitHub](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. L'azione Kubernetes [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) facilita le distribuzioni nei cluster di servizi Kubernetes di Azure. L'azione imposta il contesto del cluster AKS di destinazione, che può essere usato da altre azioni come [Azure/K8S-deploy](https://github.com/Azure/k8s-deploy/tree/master), [Azure/K8S-create-Secret](https://github.com/Azure/k8s-create-secret/tree/master) e così via oppure eseguire qualsiasi comando kubectl.

> [!IMPORTANT]
> Le azioni di GitHub sono attualmente in versione beta. È prima di tutto necessario [iscriversi per partecipare all'anteprima](https://github.com/features/actions) usando il proprio account github.
> 

Un flusso di lavoro viene definito da un file YAML (. yml) nel percorso `/.github/workflows/` nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro che ha come destinazione AKS, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**Autenticazione** | Accedere a un registro contenitori privato (ACR) |
|**Build** | Compilare & eseguire il push dell'immagine del contenitore  |
|**Distribuzione** | 1. impostare il cluster AKS di destinazione |
| |2. creare un segreto del registro di sistema generico/Docker nel cluster Kubernetes  |
||3. eseguire la distribuzione nel cluster Kubernetes|

## <a name="create-a-service-principal"></a>Creare un’entità servizio

È possibile creare un' [entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando il comando [AZ ad SP create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/). È possibile eseguire questo comando usando [Azure cloud Shell](https://shell.azure.com/) nel portale di Azure o selezionando il pulsante **prova** .

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
Copiare questo oggetto JSON, che è possibile usare per eseguire l'autenticazione da GitHub.

## <a name="configure-the-github-secrets"></a>Configurare i segreti GitHub

Seguire i passaggi per configurare i segreti:

1. In [GitHub](https://github.com/)passare al repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**.

    ![chiavi private](media/kubernetes-action/secrets.png)

2. Incollare il contenuto del `az cli` comando precedente come valore della variabile segreta. Ad esempio: `AZURE_CREDENTIALS`.

3. Analogamente, definire i seguenti segreti aggiuntivi per le credenziali del registro contenitori e impostarli in azione Docker login. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. I segreti vengono visualizzati come mostrato di seguito, una volta definiti.

    ![kubernetes-segreti](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Compilare un'immagine del contenitore e distribuirla nel cluster del servizio Kubernetes di Azure

La compilazione e il push delle immagini del contenitore vengono eseguiti usando `Azure/docker-login@v1` azione. Per distribuire un'immagine del contenitore in AKS, sarà necessario usare l'azione `Azure/k8s-deploy@v1`. Questa azione ha cinque parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **namespace** | Opzionale Scegliere lo spazio dei nomi Kubernetes di destinazione. Se lo spazio dei nomi non viene specificato, i comandi vengono eseguiti nello spazio dei nomi predefinito | 
| **manifesti** |  Necessaria Percorso dei file manifesto che verrà usato per la distribuzione |
| **images** | Opzionale URL completo della risorsa delle immagini da usare per le sostituzioni nei file manifesto |
| **imagepullsecrets** | Opzionale Nome di un segreto del registro Docker già configurato all'interno del cluster. Ognuno di questi nomi di segreto viene aggiunto nel campo imagePullSecrets per i carichi di lavoro trovati nei file manifesto di input |
| **kubectl-versione** | Opzionale Installa una versione specifica di kubectl Binary |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Eseguire la distribuzione nel cluster del servizio Kubernetes di Azure

Flusso di lavoro end-to-end per la creazione di immagini del contenitore e la distribuzione in un cluster del servizio Azure Kubernetes.

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

È possibile trovare il set di azioni in repository diversi su GitHub, ognuno dei quali contiene documentazione ed esempi che consentono di usare GitHub per CI/CD e distribuire le app in Azure.

- [installazione di kubectl](https://github.com/Azure/setup-kubectl)

- [K8S-set-context](https://github.com/Azure/k8s-set-context)

- [AKS-set-context](https://github.com/Azure/aks-set-context)

- [K8S-creazione-segreto](https://github.com/Azure/k8s-create-secret)

- [K8S-distribuzione](https://github.com/Azure/k8s-deploy)

- [webapps-contenitore-distribuzione](https://github.com/Azure/webapps-container-deploy)

- [azioni-flusso di lavoro-esempi](https://github.com/Azure/actions-workflow-samples)
