---
title: Distribuire l'istanza del contenitore tramite l'azione GitHubDeploy container instance by GitHub action
description: Configurare un'azione GitHub che automatizza i passaggi per compilare, eseguire il push e distribuire un'immagine del contenitore nelle istanze del contenitore di AzureConfigure a GitHub action that automates steps to build, push, and deploy a container image to Azure Container Instances
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258040"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Configurare un'azione GitHub per creare un'istanza del contenitoreConfigure a GitHub action to create a container instance

[GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) è una suite di funzionalità in GitHub per automatizzare i flussi di lavoro di sviluppo software nella stessa posizione in cui si archivia il codice e si collabora su richieste pull e problemi.

Usare l'azione Distribuisci nelle [istanze del contenitore](https://github.com/azure/aci-deploy) di Azure GitHub per automatizzare la distribuzione di un contenitore in Istanze del contenitore di Azure.Use the Deploy to Azure Container Instances GitHub action to automate deployment of a container to Azure Container Instances. L'azione consente di impostare le proprietà per un'istanza del contenitore in modo simile a quelle nel comando [az container create.][az-container-create]

Questo articolo illustra come configurare un flusso di lavoro in un repository GitHub che esegue le azioni seguenti:This article shows how to set up a workflow in a GitHub repo that performs the following actions:

* Compilare un'immagine da un Dockerfile
* Eseguire il push dell'immagine in un registro del contenitore di AzurePush the image to an Azure container registry
* Distribuire l'immagine del contenitore in un'istanza del contenitore di AzureDeploy the container image to an Azure container instance

In questo articolo vengono illustrati due modi per impostare il flusso di lavoro:This article shows two ways to set up the workflow:

* Configurare un flusso di lavoro manualmente in un repository GitHub usando l'azione Distribuisci in istanze del contenitore di Azure e altre azioni.  
* Usare `az container app up` il comando nell'estensione [Distribuisci](https://github.com/Azure/deploy-to-azure-cli-extension) in Azure nell'interfaccia della riga di comando di Azure.Use the command in the Deploy to Azure extension in the Azure CLI. Questo comando semplifica la creazione del flusso di lavoro GitHub e dei passaggi di distribuzione.

> [!IMPORTANT]
> L'azione GitHub per le istanze del contenitore di Azure è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* **Account GitHub-** Crea https://github.com un account su se non ne hai già uno.
* Interfaccia della riga di comando di **Azure:** è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per completare i passaggi dell'interfaccia della riga di comando di Azure.Azure CLI - You can use the Azure Cloud Shell or a local installation of the Azure CLI to complete the Azure CLI steps. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli-install]you need to install or upgrade, see Install Azure CLI.
* **Registro** di sistema del contenitore di Azure: se non ne hai uno, crea un registro contenitori di Azure nel livello Basic usando l'interfaccia della riga di comando di [Azure,](../container-registry/container-registry-get-started-azure-cli.md)il portale di [Azure](../container-registry/container-registry-get-started-portal.md)o altri metodi. Prendere nota del gruppo di risorse usato per la distribuzione, che viene usato per il flusso di lavoro GitHub.An not annot of the resource group used for the deployment, which is used for the GitHub workflow.

## <a name="set-up-repo"></a>Impostare il repository

* Per gli esempi in questo articolo, usare GitHub per eseguire il fork del repository seguente:For the examples in this article, use GitHub to fork the following repository:https://github.com/Azure-Samples/acr-build-helloworld-node

  Questo repository contiene un Dockerfile e file di origine per creare un'immagine contenitore di un'app Web di piccole dimensioni.

  ![Screenshot del pulsante Fork (evidenziato) in GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Assicurarsi che Le azioni siano abilitate per il repository. Passare al repository biforcchino e selezionare**Azioni** **impostazioni** > . In **Autorizzazioni azioni**verificare che l'opzione Abilita azioni locali e di terze parti per il **repository** sia selezionata.

## <a name="configure-github-workflow"></a>Configurare il flusso di lavoro di GitHubConfigure GitHub workflow

### <a name="create-service-principal-for-azure-authentication"></a>Creare un'entità servizio per l'autenticazione di AzureCreate service principal for Azure authentication

Nel flusso di lavoro GitHub, è necessario fornire le credenziali di Azure per l'autenticazione nell'interfaccia della riga di comando di Azure.In the GitHub workflow, you need to supply Azure credentials to authenticate to the Azure CLI. Nell'esempio seguente viene creata un'entità servizio con il ruolo Collaboratore nell'ambito del gruppo di risorse per il registro contenitori.

Ottenere innanzitutto l'ID risorsa del gruppo di risorse. Sostituire il nome del gruppo nel seguente comando [az group show:][az-acr-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Usare az ad sp create-for-rbac per creare l'entità [servizio:Use az ad sp create-for-rbac][az-ad-sp-create-for-rbac] to create the service principal:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

L'output è simile a:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Salvare l'output JSON perché viene usato in un passaggio successivo. Inoltre, prendere nota di `clientId`, che è necessario aggiornare l'entità servizio nella sezione successiva.

### <a name="update-service-principal-for-registry-authentication"></a>Aggiornare l'entità servizio per l'autenticazione del Registro di sistemaUpdate service principal for

Aggiornare le credenziali dell'entità servizio di Azure per consentire le autorizzazioni push e pull nel Registro di sistema del contenitore. Questo passaggio consente al flusso di lavoro GitHub di utilizzare l'entità servizio per [l'autenticazione con il Registro di sistema del contenitore.](../container-registry/container-registry-auth-service-principal.md) 

Ottenere l'ID risorsa del Registro di sistema del contenitore. Sostituire il nome del Registro di sistema nel seguente comando [az acr show:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Utilizzare [az role assignment create][az-role-assignment-create] per assegnare il ruolo AcrPush, che offre l'accesso push e pull al Registro di sistema. Sostituire l'ID client dell'entità servizio:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Salvare le credenziali nel repository GitHubSave credentials to GitHub repo

1. Nell'interfaccia utente di GitHub, passare al repository bifork e selezionare**Segreti** **delle impostazioni** > . 

1. Selezionare **Aggiungi un nuovo segreto** per aggiungere i segreti seguenti:Select Add a new secret to add the following secrets:

|Segreto  |valore  |
|---------|---------|
|`AZURE_CREDENTIALS`     | L'intero output JSON dalla creazione dell'entità servizio |
|`REGISTRY_LOGIN_SERVER`   | Il nome del server di accesso del Registro di sistema (tutto in minuscolo). Esempio: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  Dall'output `clientId` JSON dalla creazione dell'entità servizio       |
|`REGISTRY_PASSWORD`     |  Dall'output `clientSecret` JSON dalla creazione dell'entità servizio |
| `RESOURCE_GROUP` | Nome del gruppo di risorse usato per definire l'ambito dell'entità servizio |

### <a name="create-workflow-file"></a>Crea file flusso di lavoro

1. Nell'interfaccia utente di GitHub selezionare **Azioni** > **Nuovo flusso di lavoro**.
1. Selezionare **Imposta un flusso di lavoro manualmente**.
1. In **Modifica nuovo file**incollare il contenuto YAML seguente per sovrascrivere il codice di esempio. Accettare il `main.yml`nome file predefinito o fornire un nome file scelto.
1. Selezionare **Avvia commit**, fornire facoltativamente descrizioni brevi ed estese del commit e selezionare **Commit nuovo file**.

```yml
on: [push]
name: Linux_Container_Workflow

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
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Convalidare il flusso di lavoro

Dopo aver eseguito il commit del file del flusso di lavoro, il flusso di lavoro viene attivato. Per esaminare lo stato di avanzamento del flusso di lavoro, passare a**Flussi di lavoro** **azioni** > . 

![Visualizzare lo stato del flusso di lavoro](./media/container-instances-github-action/github-action-progress.png)

Per informazioni sulla visualizzazione dello stato e dei risultati di ogni passaggio del flusso di lavoro, vedere [Gestione dell'esecuzione](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) di un flusso di lavoro.

Al termine del flusso di lavoro, ottenere informazioni sull'istanza del contenitore denominata *aci-sampleapp* eseguendo il comando [az container show.][az-container-show] Sostituire il nome del gruppo di risorse: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

L'output è simile a:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Dopo aver eseguito il provisioning dell'istanza, passare all'FQDN del contenitore nel browser per visualizzare l'app Web in esecuzione.

![Esecuzione dell'app Web nel browser](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Usare l'estensione Distribuisci in AzureUse Deploy to Azure extension

In alternativa, usare [l'estensione Distribuisci](https://github.com/Azure/deploy-to-azure-cli-extension) in Azure nell'interfaccia della riga di comando di Azure per configurare il flusso di lavoro. Il `az container app up` comando nell'estensione accetta parametri di input per configurare un flusso di lavoro da distribuire alle istanze del contenitore di Azure.The command in the extension takes input parameters from you to set up a workflow to deploy to Azure Container Instances. 

Il flusso di lavoro creato dall'interfaccia della riga di comando di Azure è simile a quello che è possibile [creare manualmente usando GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Prerequisito aggiuntivo

Oltre [ai prerequisiti](#prerequisites) e alla configurazione dei repository per questo scenario, è necessario installare **l'estensione Distribuisci** in Azure per l'interfaccia della riga di comando di Azure.In addition to the prerequisites and [repo setup](#set-up-repo) for this scenario, you need to install the Deploy to Azure extension for the Azure CLI.

Eseguire il comando [az extension add][az-extension-add] per installare l'estensione:

```azurecli
az extension add \
  --name deploy-to-azure
```

Per informazioni sulla ricerca, l'installazione e la gestione delle estensioni, vedere [Usare le estensioni con l'interfaccia della riga](/cli/azure/azure-cli-extensions-overview)di comando di Azure.For information about finding, installing, and managing extensions, see Use extensions with Azure CLI .

### <a name="run-az-container-app-up"></a>Eseguire `az container app up`

Per eseguire il comando [az container app up,][az-container-app-up] specificare almeno:To run the az container app up command, provide at minimum:

* Il nome del registro contenitori di Azure, ad esempio *myregistry*
* L'URL del repository GitHub, ad esempio,`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Comando di esempio:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Avanzamento dei comandi

* Quando richiesto, fornire le credenziali GitHub o fornire un token di [accesso personale (PAT) GitHub](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) con *repository* e ambiti *utente* per l'autenticazione con il Registro di sistema. Se si forniscono le credenziali GitHub, il comando crea automaticamente un PAT.

* Il comando crea segreti di repository per il flusso di lavoro:The command creates repo secrets for the workflow:

  * Credenziali dell'entità servizio per l'interfaccia della riga di comando di AzureService principal credentials for the Azure CLI
  * Credenziali per accedere al Registro di sistema del contenitore di AzureCredentials to access the Azure container registry

* Dopo che il comando esegue il commit del file del flusso di lavoro nel repository, viene attivato il flusso di lavoro. 

L'output è simile a:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Convalidare il flusso di lavoro

Il flusso di lavoro distribuisce un'istanza del contenitore di Azure con il nome di base del repository GitHub, in questo caso *acr-build-helloworld-node*. Nel browser è possibile passare al collegamento fornito per visualizzare l'app Web in esecuzione. Se l'app è in ascolto su una porta diversa da 8080, specificala nell'URL.

Per visualizzare lo stato del flusso di lavoro e i risultati di ogni passaggio nell'interfaccia utente di GitHub, vedere [Gestione di un'esecuzione del flusso di lavoro](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

## <a name="clean-up-resources"></a>Pulire le risorse

Arrestare l'istanza del contenitore con il comando [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Per eliminare il gruppo di risorse e tutte le risorse in esso in esso in esso e seguito il comando [az group delete:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Passaggi successivi

Esplora [GitHub Marketplace](https://github.com/marketplace?type=actions) per altre azioni per automatizzare il flusso di lavoro di sviluppo


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
