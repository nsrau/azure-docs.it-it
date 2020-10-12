---
title: Distribuire un'istanza del contenitore da un'azione GitHub
description: Configurare un'azione GitHub che consente di automatizzare i passaggi per compilare, inserire e distribuire un'immagine del contenitore in istanze di contenitore di Azure
ms.topic: article
ms.date: 08/20/2020
ms.custom: ''
ms.openlocfilehash: 8da72d3911797e8e3a4551f2af100afb0d7ea0fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88755008"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Configurare un'azione GitHub per creare un'istanza di contenitore

[Azioni di GitHub](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) è una suite di funzionalità di GitHub che consente di automatizzare i flussi di lavoro di sviluppo del software nella stessa posizione in cui si archivia il codice e si collabora alle richieste pull e ai problemi.

Usare l'azione [Distribuisci in istanze di contenitore di Azure](https://github.com/azure/aci-deploy) in GitHub per automatizzare la distribuzione di un singolo contenitore in istanze di contenitore di Azure. L'azione consente di impostare le proprietà per un'istanza del contenitore simile a quelle del comando [AZ container create][az-container-create] .

Questo articolo illustra come configurare un flusso di lavoro in un repository GitHub che esegue le azioni seguenti:

* Compilare un'immagine da un Dockerfile
* Eseguire il push dell'immagine in un registro contenitori di Azure
* Distribuire l'immagine del contenitore in un'istanza di contenitore di Azure

Questo articolo illustra due modi per configurare il flusso di lavoro:

* [Configurare il flusso di lavoro GitHub](#configure-github-workflow) : creare un flusso di lavoro in un repository GitHub usando l'azione Distribuisci in istanze di contenitore di Azure e altre azioni.  
* [Usare l'estensione CLI](#use-deploy-to-azure-extension) : usare il `az container app up` comando nell'estensione [Deploy to Azure](https://github.com/Azure/deploy-to-azure-cli-extension) nell'interfaccia della riga di comando di Azure. Questo comando semplifica la creazione dei passaggi di distribuzione e del flusso di lavoro GitHub.

> [!IMPORTANT]
> L'azione GitHub per istanze di contenitore di Azure è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* **Account github** : https://github.com se non si ha già un account, crearne uno.
* **Interfaccia** della riga di comando di Azure: è possibile usare l'Azure cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per completare i passaggi della CLI di Azure Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].
* **Registro contenitori di Azure** : se non è già stato creato, creare un registro contenitori di Azure nel livello Basic usando l'interfaccia della riga di comando di [Azure](../container-registry/container-registry-get-started-azure-cli.md), [portale di Azure](../container-registry/container-registry-get-started-portal.md)o altri metodi. Prendere nota del gruppo di risorse usato per la distribuzione, che viene usato per il flusso di lavoro GitHub.

## <a name="set-up-repo"></a>Configurare il repository

* Per gli esempi in questo articolo, usare GitHub per creare una copia tramite fork del repository seguente: https://github.com/Azure-Samples/acr-build-helloworld-node

  Questo repository contiene i file di origine e Dockerfile per creare un'immagine del contenitore di un'app Web di piccole dimensioni.

  ![Screenshot del pulsante Fork (evidenziato) in GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Assicurarsi che le azioni siano abilitate per il repository. Passare al repository con fork e selezionare **Impostazioni**  >  **azioni**. In **azioni autorizzazioni**verificare che sia selezionata l'opzione **Abilita azioni locali e di terze parti per questo repository** .

## <a name="configure-github-workflow"></a>Configurare il flusso di lavoro GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Creare un'entità servizio per l'autenticazione di Azure

Nel flusso di lavoro GitHub è necessario specificare le credenziali di Azure per l'autenticazione nell'interfaccia della riga di comando di Azure. L'esempio seguente crea un'entità servizio con il ruolo Collaboratore nell'ambito del gruppo di risorse del registro contenitori.

Per prima cosa, ottenere l'ID risorsa del gruppo di risorse. Sostituire il nome del gruppo con il comando [AZ Group Show][az-group-show] seguente:

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Usare [AZ ad SP create-for-RBAC][az-ad-sp-create-for-rbac] per creare l'entità servizio:

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

Salvare l'output JSON perché verrà usato in un passaggio successivo. Prendere nota anche di `clientId` , che è necessario aggiornare l'entità servizio nella sezione successiva.

### <a name="update-service-principal-for-registry-authentication"></a>Aggiornare l'entità servizio per l'autenticazione del registro di sistema

Aggiornare le credenziali dell'entità servizio di Azure per consentire l'accesso push e pull al registro contenitori. Questo passaggio consente al flusso di lavoro GitHub di usare l'entità servizio per l' [autenticazione con il registro contenitori](../container-registry/container-registry-auth-service-principal.md) e per eseguire il push e il pull di un'immagine docker. 

Ottenere l'ID risorsa del registro contenitori. Sostituire il nome del registro di sistema con il comando [AZ ACR Show][az-acr-show] seguente:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Usare [AZ Role Assignment create][az-role-assignment-create] per assegnare il ruolo AcrPush, che consente l'accesso push e pull al registro di sistema. Sostituire l'ID client dell'entità servizio:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Salva le credenziali nel repository GitHub

1. Nell'interfaccia utente di GitHub passare al repository con fork e selezionare **Impostazioni**  >  **segreti**. 

1. Selezionare **Aggiungi un nuovo segreto** per aggiungere i segreti seguenti:

|Segreto  |Valore  |
|---------|---------|
|`AZURE_CREDENTIALS`     | L'intero output JSON dal passaggio di creazione dell'entità servizio |
|`REGISTRY_LOGIN_SERVER`   | Nome del server di accesso del registro di sistema (tutto in lettere minuscole). Esempio: *MyRegistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  `clientId`Dall'output JSON della creazione dell'entità servizio       |
|`REGISTRY_PASSWORD`     |  `clientSecret`Dall'output JSON della creazione dell'entità servizio |
| `RESOURCE_GROUP` | Nome del gruppo di risorse usato per definire l'ambito dell'entità servizio |

### <a name="create-workflow-file"></a>Crea file del flusso di lavoro

1. Nell'interfaccia utente di GitHub selezionare **azioni**  >  **nuovo flusso di lavoro**.
1. Selezionare **configura un flusso di lavoro**.
1. In **modifica nuovo file**incollare il contenuto YAML seguente per sovrascrivere il codice di esempio. Accettare il nome file predefinito `main.yml` o specificare un nome file scelto.
1. Selezionare **Avvia commit**, facoltativamente fornire descrizioni brevi ed estese del commit e selezionare **commit nuovo file**.

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

### <a name="validate-workflow"></a>Convalida flusso di lavoro

Dopo aver eseguito il commit del file del flusso di lavoro, viene attivato il flusso di lavoro. Per esaminare lo stato del flusso di lavoro, passare a **azioni**  >  **flussi di lavoro**. 

![Visualizza lo stato del flusso di lavoro](./media/container-instances-github-action/github-action-progress.png)

Per informazioni sulla visualizzazione dello stato e dei risultati di ogni passaggio nel flusso di lavoro, vedere [gestione dell'esecuzione di un flusso di lavoro](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) . Se il flusso di lavoro non viene completato, vedere [visualizzazione dei log per diagnosticare gli errori](https://docs.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run#viewing-logs-to-diagnose-failures).

Quando il flusso di lavoro viene completato correttamente, ottenere informazioni sull'istanza del contenitore denominata *ACI-SampleApp* eseguendo il comando [AZ container Show][az-container-show] . Sostituire il nome del gruppo di risorse: 

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

Al termine del provisioning dell'istanza, passare all'FQDN del contenitore nel browser per visualizzare l'app Web in esecuzione.

![Esecuzione dell'app Web nel browser](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Usare l'estensione Distribuisci in Azure

In alternativa, usare l' [estensione Distribuisci in Azure](https://github.com/Azure/deploy-to-azure-cli-extension) nell'interfaccia della riga di comando di Azure per configurare il flusso di lavoro. Il `az container app up` comando nell'estensione accetta parametri di input dall'utente per configurare un flusso di lavoro per la distribuzione in istanze di contenitore di Azure. 

Il flusso di lavoro creato dall'interfaccia della riga di comando di Azure è simile al flusso di lavoro che è possibile [creare manualmente usando GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Prerequisito aggiuntivo

Oltre ai [prerequisiti](#prerequisites) e alla [configurazione del repository](#set-up-repo) per questo scenario, è necessario installare l'  **estensione deploy to Azure** per l'interfaccia della riga di comando di Azure.

Eseguire il comando [AZ Extension Add][az-extension-add] per installare l'estensione:

```azurecli
az extension add \
  --name deploy-to-azure
```

Per informazioni su come trovare, installare e gestire le estensioni, vedere [usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Eseguire `az container app up`

Per eseguire il comando [AZ container app up][az-container-app-up] , specificare come minimo:

* Nome del registro contenitori di Azure, ad esempio, *Registro* di sistema
* URL del repository GitHub, ad esempio `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Comando di esempio:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Stato comando

* Quando richiesto, specificare le credenziali di GitHub o fornire un [token di accesso personale](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) (Pat) GitHub con ambiti di *repository* e *utenti* per l'autenticazione con l'account github. Se si forniscono le credenziali di GitHub, il comando crea automaticamente un PAT. Seguire le istruzioni aggiuntive per configurare il flusso di lavoro.

* Il comando crea i segreti del repository per il flusso di lavoro:

  * Credenziali entità servizio per l'interfaccia della riga di comando di Azure
  * Credenziali per l'accesso al registro contenitori di Azure

* Dopo che il comando ha eseguito il commit del file del flusso di lavoro nel repository, il flusso di lavoro viene attivato. 

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

Per visualizzare lo stato del flusso di lavoro e i risultati di ogni passaggio nell'interfaccia utente di GitHub, vedere [gestione di un'esecuzione del flusso di lavoro](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

### <a name="validate-workflow"></a>Convalida flusso di lavoro

Il flusso di lavoro distribuisce un'istanza di contenitore di Azure con il nome di base del repository GitHub, in questo caso *ACR-Build-HelloWorld-node*. Quando il flusso di lavoro viene completato correttamente, ottenere informazioni sull'istanza del contenitore denominata *ACR-Build-HelloWorld-node* eseguendo il comando [AZ container Show][az-container-show] . Sostituire il nome del gruppo di risorse: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

L'output è simile a:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Al termine del provisioning dell'istanza, passare all'FQDN del contenitore nel browser per visualizzare l'app Web in esecuzione.

## <a name="clean-up-resources"></a>Pulire le risorse

Arrestare l'istanza del contenitore con il comando [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Per eliminare il gruppo di risorse e tutte le risorse, eseguire il comando [AZ Group Delete][az-group-delete] :

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Passaggi successivi

Esplorare il [Marketplace GitHub](https://github.com/marketplace?type=actions) per altre azioni per automatizzare il flusso di lavoro di sviluppo


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
