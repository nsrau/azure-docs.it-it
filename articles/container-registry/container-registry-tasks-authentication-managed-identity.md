---
title: Usare un'identità gestita con le attività del Registro di sistema di contenitore di Azure
description: Fornire un accesso di attività di registro contenitori di Azure alle risorse di Azure inclusi altri registri contenitori privati tramite l'assegnazione di un'identità gestita per le risorse di Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148035"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Usare Azure managed identity nelle attività di registro contenitori di AZURE 

Usare un [identità per le risorse di Azure gestito](../active-directory/managed-identities-azure-resources/overview.md) eseguire l'autenticazione dalle attività di registro contenitori di AZURE a un'istanza di registro contenitori di Azure o altre risorse di Azure, senza dover fornire o gestire le credenziali nel codice. Ad esempio, usare un'identità gestita per eseguire il pull o push di immagini del contenitore a un altro registro di sistema come un passaggio di un'attività.

In questo articolo altre informazioni sulle identità gestita e su come:

> [!div class="checklist"]
> * Abilitare un'identità assegnata dal sistema o un'identità assegnata dall'utente in un'attività di registro contenitori di AZURE
> * Concedere all'identità l'accesso alle risorse di Azure, ad esempio altri registri contenitori di Azure
> * Usare l'identità gestita per accedere alle risorse da un'attività 

Per creare le risorse di Azure, in questo articolo richiede l'esecuzione del comando di Azure versione 2.0.66 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="why-use-a-managed-identity"></a>Perché usare un'identità gestita?

Un'identità gestita per le risorse di Azure fornisce ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile configurare [alcune risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), incluse le attività di registro contenitori di AZURE, con un'identità gestita. Quindi, usare l'identità per accedere ad altre risorse di Azure, senza passare credenziali nel codice o negli script.

Esistono due tipi di identità gestite:

* *Le identità assegnate dall'utente*, che è possibile assegnare a più risorse e sono conservate per il tempo desiderato. Le identità assegnate dall'utente sono attualmente in anteprima.

* Oggetto *gestite dal sistema di identità*, che è univoco per una risorsa specifica, ad esempio un'attività di registro contenitori di AZURE e ha una durata per la durata di tale risorsa.

Dopo aver configurato una risorsa di Azure con un'identità gestita, concedere all'identità l'accesso a un'altra risorsa, analogamente a qualsiasi entità di sicurezza. Ad esempio, assegnare un'identità gestita un ruolo con pull, push e pull o altre autorizzazioni a un registro contenitori privato in Azure. (Per un elenco completo dei ruoli di registro, vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md).) È possibile assegnare a un'identità l'accesso a una o più risorse.

## <a name="create-container-registries"></a>Creare registri contenitori

Per questa esercitazione sono necessari tre registri per contenitori:

* Usare il registro prima per creare ed eseguire attività di registro contenitori di AZURE. In questo articolo, questo registro di sistema di origine è denominato *myregistry*. 
* I registri i secondo e terzi sono i registri di destinazione per la prima attività di esempio per push di un'immagine che viene compilata. In questo articolo, vengono denominati i registri di destinazione *customregistry1* e *customregistry2*.

Sostituire con i propri nomi di registro di sistema nei passaggi successivi.

Se non si dispone già di registri contenitori di Azure necessari, vedere [Guida introduttiva: Creare un registro contenitori privato usando l'interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md). Non è necessario eseguire il push di immagini nel Registro di sistema ancora.

## <a name="example-task-with-a-system-assigned-identity"></a>Esempio: Attività con un'identità assegnata dal sistema

In questo esempio viene illustrato come creare un [attività in più passaggi](container-registry-tasks-multi-step.md) con un'identità assegnata dal sistema. L'attività crea un'immagine e quindi Usa l'identità per l'autenticazione con due registri di destinazione per il push dell'immagine.

I passaggi per questo esempio di attività sono definiti in un [file YAML](container-registry-tasks-reference-yaml.md) denominata `testtask.yaml`. Il file si trova nella directory multipleRegistries del [acr-attività](https://github.com/Azure-Samples/acr-tasks) repository di esempi. Il file viene riprodotto di seguito:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Crea attività con identità assegnata dal sistema

Creare l'attività *multiplo reg* eseguendo il codice seguente [az acr attività creare] [ az-acr-task-create] comando. Il contesto di attività è la cartella multipleRegistries del repository di esempi e il comando fa riferimento al file `testtask.yaml` nel repository. Il `--assign-identity` parametro non prevede alcun valore aggiuntivo crea un'identità assegnata dal sistema per l'attività. Questa attività viene configurata in modo che è necessario attivarlo manualmente, ma è possibile configurarlo per l'esecuzione quando viene effettuato il push di commit al repository o viene effettuata una richiesta pull. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

Nell'output del comando, il `identity` sezione mostra un'identità del tipo `SystemAssigned` è impostato nell'attività. Il `principalId` è l'ID dell'entità servizio dell'identità:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Usare la [show attività di az acr] [ az-acr-task-show] comando per archiviare il `principalId` in una variabile, da usare nei comandi successivi:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Concedere le autorizzazioni di push di identità per due registri contenitori di destinazione

In questa sezione, concedere le autorizzazioni di identità assegnata dal sistema per eseguire il push di registri di due destinazione, denominati *customregistry1* e *customregistry2*.

Utilizzare innanzitutto le [show di az acr] [ az-acr-show] comando per ottenere l'ID di risorsa di ogni registro di sistema e archiviare gli ID nelle variabili:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Usare la [creazione dell'assegnazione di ruolo az] [ az-role-assignment-create] comando per assegnare l'identità di `acrpush` ruolo ogni registro di sistema. Questo ruolo dispone delle autorizzazioni per le immagini di pull e push in un registro contenitori.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Aggiungere le credenziali del Registro di sistema di destinazione attività

A questo punto usare il [aggiungere le credenziali di attività di az acr] [ az-acr-task-credential-add] comando per aggiungere le credenziali dell'identità per l'attività in modo che l'autenticazione con entrambi i registri di destinazione.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Eseguire manualmente l'attività

Usare la [dell'esecuzione dell'attività az acr] [ az-acr-task-run] comando per attivare manualmente l'attività. Il `--set` parametro viene usato per passare i nomi dei server di accesso dei registri due destinazione come valori per le variabili di attività `REGISTRY1` e `REGISTRY2`.

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

L'output è simile a:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Esempio: Attività con un'identità assegnata dall'utente

In questo esempio si crea un'identità assegnata dall'utente con autorizzazioni di lettura di segreti da Azure key vault. Questa identità è assegnare a un'attività a più passaggi che legge la chiave privata, compila un'immagine e accede alla CLI di Azure per leggere il tag di immagine.

### <a name="create-a-key-vault-and-store-a-secret"></a>Creare un insieme di credenziali delle chiavi e archiviare un segreto

In primo luogo, se è necessario, creare un gruppo di risorse denominato *myResourceGroup* nel *eastus* percorso con il codice seguente [Crea gruppo di az] [ az-group-create]comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Usare la [az keyvault create] [ az-keyvault-create] comando per creare un insieme di credenziali delle chiavi. Assicurarsi di specificare un nome univoco dell'insieme di credenziali chiave. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store un segreto di esempio nell'insieme di credenziali chiave usando il [set di az keyvault secret] [ az-keyvault-secret-set] comando:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Ad esempio, è possibile archiviare le credenziali per l'autenticazione con un registro Docker privato in modo che è possibile estrarre un'immagine privata.

### <a name="create-an-identity"></a>Creare un'identità

Creare un'identità denominata *myACRTasksId* nella sottoscrizione usando la [creare identità az] [ az-identity-create] comando. È possibile usare stesso gruppo di risorse usato in precedenza per creare un registro contenitori o insieme di credenziali delle chiavi o uno diverso.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Per configurare l'identità nei passaggi seguenti, usare il comando [az identity show][az-identity-show] per archiviare l'ID risorsa dell'identità e l'ID dell'entità servizio nelle variabili.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Identità di concedere l'accesso a Key Vault per la lettura di segreti

Eseguire il comando seguente [az keyvault set-policy] [ az-keyvault-set-policy] comando per impostare un criterio di accesso nell'insieme di credenziali chiave. Nell'esempio seguente consente l'identità assegnata dall'utente ottenere i segreti dall'insieme di credenziali chiave. Questo accesso è necessaria in un secondo momento per eseguire correttamente un'attività in più passaggi.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Concedere l'identità dell'accesso in lettura al gruppo di risorse del Registro di sistema

Eseguire il comando seguente [creazione dell'assegnazione di ruolo az] [ az-role-assignment-create] comando per assegnare l'identità di un ruolo di lettura, in questo caso al gruppo di risorse contenente il Registro di sistema di origine. Questo ruolo è necessaria in un secondo momento per eseguire correttamente un'attività in più passaggi.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Crea attività con identità assegnata dall'utente

A questo punto creare un [attività in più passaggi](container-registry-tasks-multi-step.md) e assegnare l'identità assegnata dall'utente. Per questo esempio di attività, creare un [file YAML](container-registry-tasks-reference-yaml.md) denominata `managed-identities.yaml` in una directory di lavoro locale e incollare il contenuto seguente. Assicurarsi di sostituire il nome dell'insieme di credenziali chiave nel file con il nome dell'insieme di credenziali delle chiavi

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

Questa attività esegue le operazioni seguenti:

* Verifica che possa accedere il segreto nell'insieme di credenziali delle chiavi. Questo passaggio è a scopo dimostrativo. In uno scenario reale, potrebbe essere necessario un passaggio di attività per ottenere le credenziali per accedere a un repository dell'Hub Docker privato.
* Crea e inserisce il `mywebsite` immagine nel Registro di sistema di origine.
* I log nella CLI di Azure all'elenco di `my-website` tag del Registro di sistema di origine immagine.

Creare un'attività denominata *msitask* e passare l'ID risorsa dell'identità assegnata dall'utente creato in precedenza. Questo esempio di attività viene creato dal `managed-identities.yaml` file salvato nella directory di lavoro locale, pertanto è necessario attivarlo manualmente.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

Nell'output del comando, il `identity` sezione mostra un'identità del tipo `UserAssigned` è impostato nell'attività. Il `principalId` è l'ID dell'entità servizio dell'identità:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Eseguire manualmente l'attività

Usare la [dell'esecuzione dell'attività az acr] [ az-acr-task-run] comando per attivare manualmente l'attività. Il `--set` parametro viene usato per passare il nome del Registro di sistema di origine per l'attività:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Output mostra il segreto viene risolto, l'immagine è stata creata ed eseguito il push e l'attività registra in Powershell con l'identità da cui leggere il tag di immagine nel Registro di sistema di origine:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Passaggi successivi

In questo articolo, sono state illustrate usando identità gestite con le attività del Registro di sistema di contenitore di Azure e su come:

> [!div class="checklist"]
> * Abilitare un'identità assegnata dal sistema o assegnata dall'utente in un'attività di registro contenitori di AZURE
> * Concedere all'identità l'accesso alle risorse di Azure, ad esempio altri registri contenitori di Azure
> * Usare l'identità gestita per accedere alle risorse da un'attività  

* Altre informazioni sulle [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
