---
title: Esecuzione rapida dell'attività con modello
description: Accodare un'attività ACR eseguita per compilare un'immagine usando un modello di Azure Resource Manager
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 7ad40d2e925d5e1443af9bce4115d45b0e8c06e1
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927769"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Eseguire attività ACR usando modelli di Gestione risorse

[Attività del Registro Azure Container](container-registry-tasks-overview.md) è un gruppo di funzionalità incluse in Registro Azure Container che consente di gestire e modificare le immagini del contenitore per tutto il ciclo di vita del contenitore. 

Questo articolo illustra Azure Resource Manager esempi di modelli per accodare un'esecuzione di attività rapida, simile a una che è possibile creare manualmente usando il comando [AZ ACR Build][az-acr-build] .

Un modello di Gestione risorse per accodare un'esecuzione di attività è utile negli scenari di automazione ed `az acr build`estende la funzionalità di. Ad esempio:

* Usare un modello per creare un registro contenitori e accodare immediatamente un'esecuzione di attività per compilare ed eseguire il push di un'immagine del contenitore
* Creare o abilitare risorse aggiuntive che è possibile usare in una rapida esecuzione di attività, ad esempio un'identità gestita per le risorse di Azure

## <a name="limitations"></a>Limitazioni

* È necessario specificare un contesto remoto, ad esempio un repository GitHub, come [percorso di origine](container-registry-tasks-overview.md#context-locations) per l'esecuzione dell'attività. Non è possibile usare un contesto di origine locale.
* Per le esecuzioni di attività usando un'identità gestita, è consentita solo un'identità gestita *assegnata dall'utente* .

## <a name="prerequisites"></a>Prerequisiti

* **Account github** : https://github.com se non si ha già un account, crearne uno. 
* **Repository di esempio fork** : per gli esempi di attività illustrati di seguito, usare l'interfaccia utente di GitHub per creare una copia tramite https://github.com/Azure-Samples/acr-build-helloworld-nodefork del repository di esempio seguente nell'account github:. Questo repository contiene Dockerfile di esempio e codice sorgente per compilare piccole immagini del contenitore.

## <a name="example-create-registry-and-queue-task-run"></a>Esempio: creare un'attività Esegui registro di sistema e Accodamento

Questo esempio usa un [modello di esempio](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) per creare un registro contenitori e accodare un'esecuzione di attività che compila ed esegue il push di un'immagine. 

### <a name="template-parameters"></a>Parametri di modelli

Per questo esempio, specificare i valori per i parametri di modello seguenti:

|Parametro  |valore  |
|---------|---------|
|registryName     |Nome univoco del registro di sistema creato         |
|repository     |Repository di destinazione per l'attività di compilazione        |
|taskRunName     |Nome dell'esecuzione dell'attività, che specifica il tag di immagine |
|sourceLocation     |Contesto remoto per l'attività di compilazione, ad esempio https://github.com/Azure-Samples/acr-build-helloworld-node. Il Dockerfile nella radice del repository compila un'immagine del contenitore per una piccola app Web Node. js. Se lo si desidera, utilizzare il fork del repository come contesto di compilazione.         |

### <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello con il comando [AZ Deployment Group create][az-deployment-group-create] . Questo esempio crea ed esegue il push dell'immagine *HelloWorld-node: TestRun* in un registro denominato *mycontainerregistry*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git
 ```

Il comando precedente passa i parametri nella riga di comando. Se lo si desidera, passarli in un [file di parametri](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificare la distribuzione

Al termine della distribuzione, verificare che l'immagine venga compilata eseguendo [AZ ACR repository Show-Tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Output:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Visualizza log di esecuzione

Per visualizzare i dettagli relativi all'esecuzione dell'attività, visualizzare il log di esecuzione.

Per prima cosa, ottenere l'ID esecuzione con [AZ ACR Task List-runs][az-acr-task-list-runs]
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

L'output è simile a:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Eseguire il comando [AZ ACR Task logs][az-acr-task-logs] per visualizzare i log di esecuzione dell'attività per l'ID esecuzione, in questo caso *CA1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

L'output Mostra il log di esecuzione dell'attività.

È anche possibile visualizzare il log di esecuzione dell'attività nella portale di Azure. 

1. Passare al registro contenitori
2. In **Servizi**selezionare**esecuzioni** **attività** > .
3. Selezionare l'ID esecuzione, in questo caso *CA1*. 

Il portale Mostra il log di esecuzione dell'attività.

## <a name="example-task-run-with-managed-identity"></a>Esempio: esecuzione di attività con identità gestita

Usare un [modello di esempio](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) per accodare un'esecuzione di attività che Abilita un'identità gestita assegnata dall'utente. Durante l'esecuzione dell'attività, l'identità viene autenticata per eseguire il pull di un'immagine da un altro registro contenitori di Azure. 

Questo scenario è simile all' [autenticazione tra più registri in un'attività ACR usando un'identità gestita da Azure](container-registry-tasks-cross-registry-authentication.md). Un'organizzazione, ad esempio, potrebbe mantenere un registro centralizzato con immagini di base accessibili da più team di sviluppo.

### <a name="prepare-base-registry"></a>Preparare il registro di sistema di base

A scopo dimostrativo, creare un registro contenitori separato come registro di base ed eseguire il push di un'immagine di base node. js pull dall'hub docker.

1. Creare un secondo registro contenitori, ad esempio *mybaseregistry*, per archiviare le immagini di base.
1. Eseguire il `node:9-alpine` pull dell'immagine dall'hub Docker, contrassegnarla per il registro di sistema di base ed eseguirne il push nel registro di sistema di base:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Crea nuovo Dockerfile

Creare un Dockerfile che esegue il pull dell'immagine di base dal registro di sistema di base. Eseguire la procedura seguente nel fork locale del repository GitHub, ad esempio `https://github.com/myGitHubID/acr-build-helloworld-node.git`.

1. Nell'interfaccia utente di GitHub selezionare **Crea nuovo file**.
1. Denominare il file *Dockerfile-test* e incollare il contenuto seguente. Sostituire il nome del registro di sistema per *mybaseregistry*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Selezionare Esegui **commit nuovo file**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Assegnare le autorizzazioni di pull all'identità al registro di sistema di base

Assegnare le autorizzazioni di identità gestite per eseguire il pull dal registro di sistema di base, *mybaseregistry*.

Usare il comando [AZ ACR Show][az-acr-show] per ottenere l'ID risorsa del registro di sistema di base e archiviarlo in una variabile:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Usare il comando [AZ Role Assignment create][az-role-assignment-create] per assegnare l'identità del ruolo Acrpull al registro di sistema di base. Questo ruolo ha le autorizzazioni solo per eseguire il pull delle immagini dal registro di sistema.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Parametri di modelli

Per questo esempio, specificare i valori per i parametri di modello seguenti:

|Parametro  |valore  |
|---------|---------|
|registryName     |Nome del registro in cui viene compilata l'immagine  |
|repository     |Repository di destinazione per l'attività di compilazione        |
|taskRunName     |Nome dell'esecuzione dell'attività, che specifica il tag di immagine |
|userAssignedIdentity |ID risorsa dell'identità assegnata dall'utente abilitata nell'attività|
|customRegistryIdentity | ID client dell'identità assegnata dall'utente abilitato nell'attività, usato per l'autenticazione con il registro di sistema personalizzato |
|customRegistry |Nome del server di accesso del registro di sistema personalizzato a cui si accede nell'attività, ad esempio *mybaseregistry.azurecr.io*|
|sourceLocation     |Contesto remoto per l'attività di compilazione, ad esempio * https://github.com/\<your-GitHub-ID\>/acr-build-helloworld-node.* |
|dockerFilePath | Percorso di Dockerfile nel contesto remoto, utilizzato per compilare l'immagine. |

### <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello con il comando [AZ Deployment Group create][az-deployment-group-create] . Questo esempio crea ed esegue il push dell'immagine *HelloWorld-node: TestRun* in un registro denominato *mycontainerregistry*. Viene eseguito il pull dell'immagine di base da *mybaseregistry.azurecr.io*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

Il comando precedente passa i parametri nella riga di comando. Se lo si desidera, passarli in un [file di parametri](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificare la distribuzione

Al termine della distribuzione, verificare che l'immagine venga compilata eseguendo [AZ ACR repository Show-Tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Output:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Visualizza log di esecuzione

Per visualizzare il log di esecuzione, vedere i passaggi nella [sezione precedente](#view-run-log).

## <a name="next-steps"></a>Passaggi successivi

 * Vedere altri esempi di modelli nel [repository di GitHub ACR](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Per informazioni dettagliate sulle proprietà dei modelli, vedere il riferimento al modello per le [esecuzioni](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) e le [attività](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)delle attività.


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
