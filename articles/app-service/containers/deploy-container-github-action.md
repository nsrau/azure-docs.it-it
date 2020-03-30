---
title: CI/CD contenitore personalizzato dalle azioni GitHub
description: Informazioni su come usare le azioni GitHub per distribuire il contenitore Linux personalizzato nel servizio app da una pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246978"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Distribuire un contenitore personalizzato nel servizio app usando le azioni GitHubDeploy a custom container to App Service using GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita dello sviluppo software. Con l'azione del [servizio app di Azure per i contenitori,](https://github.com/Azure/webapps-container-deploy)è possibile automatizzare il flusso di lavoro per distribuire le app come [contenitori personalizzati nel servizio app](https://azure.microsoft.com/services/app-service/containers/) usando le azioni GitHub.With the Azure App Service Action for Containers , you can automate your workflow to deploy apps as custom containers to App Service using GitHub Actions.

> [!IMPORTANT]
> GitHub Actions è attualmente in versione beta. Devi prima [registrarti per unirti all'anteprima](https://github.com/features/actions) usando il tuo account GitHub.
> 

Un flusso di lavoro è definito da un file `/.github/workflows/` YAML (.yml) nel percorso nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro del contenitore del servizio app di Azure, il file include tre sezioni:For an Azure App Service container workflow, the file has three sections:

|Sezione  |Attività  |
|---------|---------|
|**Autenticazione** | 1. Definire un'entità servizio. <br /> 2. Creare un segreto GitHub.2. Create a GitHub secret. |
|**Costruire** | 1. Configurare l'ambiente. <br /> 2. Creare l'immagine contenitore. |
|**Distribuire** | 1. Distribuire l'immagine del contenitore. |

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un'entità servizio usando il comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/) [service principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure o selezionando il pulsante **Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso all'app del servizio app simile a quella riportata di seguito. Copiare questo oggetto JSON per eseguire l'autenticazione da GitHub.Copy this JSON object to authenticate from GitHub.

 ```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> È sempre buona norma concedere un accesso minimo. È possibile limitare l'ambito nel comando dell'interfaccia della riga di comando Az precedente all'app del servizio app specifica e al Registro di sistema del contenitore di Azure in cui vengono inserite le immagini del contenitore.

## <a name="configure-the-github-secret"></a>Configurare il segreto GitHubConfigure the GitHub secret

Nell'esempio seguente vengono utilizzate credenziali a livello di utente, ad esempio l'entità servizio di Azure per la distribuzione. Seguire i passaggi per configurare il segreto:

1. In [GitHub](https://github.com/), sfogliare il repository, selezionare **Impostazioni > segreti > Aggiungi un nuovo segreto**

2. Incollare il contenuto `az cli` del comando seguente come valore della variabile segreta. Ad esempio: `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Ora nel file del flusso `.github/workflows/workflow.yml` di lavoro nel ramo: sostituire il segreto nell'azione di accesso di Azure con il segreto.

4. Analogamente, definire i seguenti segreti aggiuntivi per le credenziali del Registro di sistema del contenitore e impostarli nell'azione di accesso Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Vengono visualizzati i segreti come mostrato di seguito una volta definiti.

    ![segreti dei contenitori](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Creare l'immagine Contenitore

Nell'esempio seguente viene illustrato parte del flusso di lavoro che compila l'immagine docker.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Eseguire la distribuzione in un contenitore del servizio appDeploy to an App Service container

Per distribuire l'immagine in un contenitore `azure/webapps-container-deploy@v1` personalizzato nel servizio app, usare l'azione. Questa azione ha cinque parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **nome app** | (Obbligatorio) Nome dell'app del servizio app | 
| **nome-slot** | (Facoltativo) Immettere uno slot esistente diverso dallo slot di produzione |
| **Immagini** | (Obbligatorio) Specificare il nome completo delle immagini del contenitore. Ad esempio, 'myregistry.azurecr.io/nginx:latest' o 'python:3.7.2-alpine/'. Per un'app multi-contenitore, è possibile fornire più nomi di immagine contenitore (separati da più righe)For a multi-container app, multiple container image names can be provided (multi-line separated) |
| **file di configurazione** | (Facoltativo) Percorso del file Docker-Compose. Deve essere un percorso completo o relativo alla directory di lavoro predefinita. Obbligatorio per le app multi-contenitore. |
| **comando contenitore** | (Facoltativo) Immettere il comando di avvio. Per esempio. dotnet run o dotnet filename.dll |

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app Node.js in un contenitore personalizzato nel servizio app.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare il set di azioni raggruppate in repository diversi in GitHub, ognuno contenente documentazione ed esempi per usare GitHub per CI/CD e distribuire le app in Azure.You can find our set of Actions grouped into different repositories on GitHub, each each containing documentation and examples to help you use GitHub for CI/CD and deploy your apps to Azure.

- [Accesso ad Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [WebApp di Azure per i contenitoriAzure WebApp for containers](https://github.com/Azure/webapps-container-deploy)

- [Accesso/logout Docker](https://github.com/Azure/docker-login)

- [Eventi che attivano flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8 simplementazione](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro CI starter](https://github.com/actions/starter-workflows)

- [Starter workflows to deploy to Azure](https://github.com/Azure/actions-workflow-samples)
