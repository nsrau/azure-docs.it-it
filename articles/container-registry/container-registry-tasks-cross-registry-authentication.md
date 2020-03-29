---
title: Autenticazione tra registro dall'attività ACRCross-registry authentication from ACR task
description: Configurare un'attività del Registro di sistema del contenitore di Azure per accedere a un altro registro di contenitori di Azure privato usando un'identità gestita per le risorse di AzureConfigure an Azure Container Registry Task (ACR Task) to access another private Azure container registry by using a managed identity for Azure resources
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842495"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticazione tra registro in un'attività ACR usando un'identità gestita da AzureCross-registry authentication in an ACR task using an Azure-managed identity 

In [un'attività ACR](container-registry-tasks-overview.md)è possibile [abilitare un'identità gestita per](container-registry-tasks-authentication-managed-identity.md)le risorse di Azure. L'attività può usare l'identità per accedere ad altre risorse di Azure, senza dover fornire o gestire le credenziali. 

In questo articolo viene illustrato come abilitare un'identità gestita in un'attività per eseguire il pull di un'immagine da un Registro di sistema diverso da quello usato per eseguire l'attività.

Per creare le risorse di Azure, questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.68 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli]you need to install or upgrade, see Install Azure CLI.

## <a name="scenario-overview"></a>Panoramica dello scenario

L'attività di esempio estrae un'immagine di base da un altro registro del contenitore di Azure per compilare ed eseguire il push di un'immagine dell'applicazione. Per eseguire il pull dell'immagine di base, configurare l'attività con un'identità gestita e assegnarle le autorizzazioni appropriate. 

In questo esempio viene illustrata la procedura di utilizzo di un'identità gestita assegnata dall'utente o dal sistema. La scelta dell'identità dipende dalle esigenze dell'organizzazione.

In uno scenario reale, un'organizzazione potrebbe gestire un set di immagini di base utilizzate da tutti i team di sviluppo per compilare le proprie applicazioni. Queste immagini di base vengono archiviate in un registro aziendale, con ogni team di sviluppo che ha solo diritti pull. 

## <a name="prerequisites"></a>Prerequisiti

Per questo articolo sono necessari due registri dei contenitori di Azure:For this article, you need two Azure container registries:

* Utilizzare il primo Registro di sistema per creare ed eseguire attività ACR. In questo articolo, questo Registro di sistema è denominato *myregistry*. 
* Il secondo Registro di sistema ospita un'immagine di base utilizzata per l'attività per compilare un'immagine. In questo articolo, il secondo Registro di sistema è denominato *mybaseregistry*. 

Sostituire con i propri nomi del Registro di sistema nei passaggi successivi.

Se non si dispone già dei registri dei contenitori di Azure necessari, vedere [Guida introduttiva: Creare un registro dei contenitori privati usando l'interfaccia della riga di comando](container-registry-get-started-azure-cli.md)di Azure.If you don't already have the needed Azure container registries, see Quickstart: Create a private container registry using the Azure CLI . Non è ancora necessario eseguire il push delle immagini nel Registro di sistema.

## <a name="prepare-base-registry"></a>Preparare il Registro di sistema di basePrepare base registry

Creare innanzitutto una directory di lavoro e quindi un file denominato Dockerfile con il contenuto seguente. Questo semplice esempio crea un'immagine di base Node.js da un'immagine pubblica in Docker Hub.This simple example builds a Node.js base image from a public image in Docker Hub.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
Nella directory corrente, eseguire il comando [az acr build][az-acr-build] per compilare ed eseguire il push dell'immagine di base nel Registro di sistema di base. In pratica, un altro team o processo nell'organizzazione potrebbe gestire il registro di base.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Definire i passaggi delle attività nel file YAMLDefine task steps in YAML file

I passaggi per questa [attività a più passaggi](container-registry-tasks-multi-step.md) sono definiti in un file [YAML](container-registry-tasks-reference-yaml.md). Creare un `helloworldtask.yaml` file denominato nella directory di lavoro locale e incollare il contenuto seguente. Aggiornare il `REGISTRY_NAME` valore di nel passaggio di compilazione con il nome del server del Registro di sistema di base.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

L'istruzione di `Dockerfile-app` compilazione usa il file nel repository [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) per compilare un'immagine. I `--build-arg` riferimenti del Registro di sistema di base per estrarre l'immagine di base. Se compilata correttamente, l'immagine viene inserita nel Registro di sistema utilizzato per eseguire l'attività.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Opzione 1: Creare un'attività con l'identità assegnata dall'utenteOption 1: Create task with user-assigned identity

I passaggi descritti in questa sezione consentono di creare un'attività e abilitare un'identità assegnata dall'utente. Se invece si desidera abilitare un'identità assegnata dal sistema, vedere [Opzione 2: Creare un'attività con identità assegnata dal sistema](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Creare un'attività

Creare l'attività *helloworldtask* eseguendo il comando [az acr task create][az-acr-task-create] seguente. L'attività viene eseguita senza un contesto `helloworldtask.yaml` del codice sorgente e il comando fa riferimento al file nella directory di lavoro. Il `--assign-identity` parametro passa l'ID risorsa dell'identità assegnata dall'utente. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opzione 2: Creare un'attività con un'identità assegnata dal sistemaOption 2: Create task with system-assigned identity

I passaggi in questa sezione creano un'attività e abilitano un'identità assegnata dal sistema. Se invece si desidera abilitare un'identità assegnata dall'utente, vedere [Opzione 1: Creare un'attività con identità assegnata dall'utente](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Creare un'attività

Creare l'attività *helloworldtask* eseguendo il comando [az acr task create][az-acr-task-create] seguente. L'attività viene eseguita senza un contesto `helloworldtask.yaml` del codice sorgente e il comando fa riferimento al file nella directory di lavoro. Il `--assign-identity` parametro senza valore abilita l'identità assegnata dal sistema nell'attività. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Concedere autorizzazioni di pull dell'identità al Registro di sistema di base

In questa sezione assegnare all'identità gestita le autorizzazioni per il pull dal Registro di sistema di base, *mybaseregistry*.

Utilizzare il comando [az acr show][az-acr-show] per ottenere l'ID risorsa del Registro di sistema di base e archiviarlo in una variabile:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Utilizzare il comando [az role assignment][az-role-assignment-create] `acrpull` create per assegnare all'identità il ruolo al Registro di sistema di base. Questo ruolo dispone solo delle autorizzazioni per estrarre le immagini dal Registro di sistema.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Aggiungere le credenziali del Registro di sistema di destinazione all'attivitàAdd target registry credentials to task

Ora utilizzare il comando [az acr credenziale add][az-acr-task-credential-add] per consentire all'attività di eseguire l'autenticazione con il Registro di sistema di base utilizzando le credenziali dell'identità. Eseguire il comando corrispondente al tipo di identità gestita abilitato nell'attività. Se è stata abilitata un'identità assegnata dall'utente, passare `--use-identity` con l'ID client dell'identità. Se è stata abilitata un'identità assegnata dal sistema, passare `--use-identity [system]`.

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Eseguire manualmente l'attività

Per verificare che l'attività in cui è stata abilitata un'identità gestita venga eseguita correttamente, attivare manualmente l'attività con il comando [az acr task run.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Se l'attività viene eseguita correttamente, l'output è simile al:If the task runs successfully, output is similar to:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Eseguire il comando [az acr repository show-tags][az-acr-repository-show-tags] per verificare che l'immagine sia stata compilata ed inviata correttamente a *myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Output di esempio:

```console
cf10
```

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni [sull'abilitazione di un'identità gestita in un'attività ACR](container-registry-tasks-authentication-managed-identity.md).
* Vedere le informazioni di riferimento su [Attività ACR YAML](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
