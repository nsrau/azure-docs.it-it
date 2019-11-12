---
title: Autenticazione tra più registri da Azure Container Registry attività
description: Configurare un'attività Container Registry di Azure (attività ACR) per accedere a un altro registro contenitori di Azure privato usando un'identità gestita per le risorse di Azure
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: f2ffb42ce109f5e6f7186461f931b7f8da57ff32
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931506"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticazione tra più registri in un'attività ACR usando un'identità gestita da Azure 

In un' [attività ACR](container-registry-tasks-overview.md)è possibile [abilitare un'identità gestita per le risorse di Azure](container-registry-tasks-authentication-managed-identity.md). L'attività può usare l'identità per accedere ad altre risorse di Azure, senza dover fornire o gestire le credenziali. 

Questo articolo illustra come abilitare un'identità gestita in un'attività che estrae un'immagine da un registro diverso da quello usato per eseguire l'attività.

Per creare le risorse di Azure, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.68 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="scenario-overview"></a>Panoramica dello scenario

L'attività di esempio esegue il pull di un'immagine di base da un altro registro contenitori di Azure per compilare ed eseguire il push di un'immagine dell'applicazione. Per eseguire il pull dell'immagine di base, è necessario configurare l'attività con un'identità gestita e assegnarvi le autorizzazioni appropriate. 

Questo esempio illustra i passaggi che usano un'identità gestita assegnata dall'utente o assegnata dal sistema. La scelta dell'identità dipende dalle esigenze della propria organizzazione.

In uno scenario reale, un'organizzazione potrebbe mantenere un set di immagini di base usate da tutti i team di sviluppo per compilare le proprie applicazioni. Queste immagini di base vengono archiviate in un registro aziendale e ogni team di sviluppo dispone solo di diritti di pull. 

## <a name="prerequisites"></a>prerequisiti

Per questo articolo sono necessari due registri contenitori di Azure:

* Usare il primo registro per creare ed eseguire attività ACR. In questo articolo il registro di sistema è denominato *Registro*di sistema. 
* Il secondo registro di sistema ospita un'immagine di base usata per l'attività per la compilazione di un'immagine. In questo articolo, il secondo registro di sistema è denominato *mybaseregistry*. 

Sostituire con i nomi dei propri registri nei passaggi successivi.

Se non si hanno già i registri contenitori di Azure necessari, vedere [Guida introduttiva: creare un registro contenitori privato usando l'interfaccia della](container-registry-get-started-azure-cli.md)riga di comando di Azure. Non è ancora necessario eseguire il push delle immagini nel registro.

## <a name="prepare-base-registry"></a>Preparare il registro di sistema di base

Per prima cosa, creare una directory di lavoro e quindi creare un file denominato Dockerfile con il contenuto seguente. Questo semplice esempio crea un'immagine di base node. js da un'immagine pubblica nell'hub docker.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
Nella directory corrente eseguire il comando [AZ ACR Build][az-acr-build] per compilare ed effettuare il push dell'immagine di base nel registro di sistema di base. In pratica, un altro team o processo nell'organizzazione potrebbe gestire il registro di sistema di base.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Definire i passaggi dell'attività nel file YAML

I passaggi per questo esempio di attività in più [passaggi](container-registry-tasks-multi-step.md) sono definiti in un [file YAML](container-registry-tasks-reference-yaml.md). Creare un file denominato `helloworldtask.yaml` nella directory di lavoro locale e incollare il contenuto seguente. Aggiornare il valore di `REGISTRY_NAME` nell'istruzione di compilazione con il nome del server del registro di sistema di base.

```yml
version: v1.0.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Il passaggio di compilazione usa il file di `Dockerfile-app` nel repository [Azure-Samples/ACR-Build-HelloWorld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) per creare un'immagine. Il `--build-arg` fa riferimento al registro di sistema di base per eseguire il pull dell'immagine di base. Al termine della compilazione, viene effettuato il push dell'immagine nel registro di sistema usato per eseguire l'attività.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Opzione 1: creare un'attività con identità assegnata dall'utente

Nei passaggi di questa sezione viene creata un'attività e viene abilitata un'identità assegnata dall'utente. Se invece si desidera abilitare un'identità assegnata dal sistema, vedere l' [opzione 2: creare un'attività con l'identità assegnata dal sistema](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Creare un'attività

Creare l'attività *helloworldtask* eseguendo il comando [AZ ACR task create][az-acr-task-create] seguente. L'attività viene eseguita senza un contesto del codice sorgente e il comando fa riferimento al file `helloworldtask.yaml` nella directory di lavoro. Il parametro `--assign-identity` passa l'ID risorsa dell'identità assegnata dall'utente. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opzione 2: creare un'attività con identità assegnata dal sistema

Nei passaggi di questa sezione viene creata un'attività e viene abilitata un'identità assegnata dal sistema. Se invece si desidera abilitare un'identità assegnata dall'utente, vedere l' [opzione 1: creare un'attività con l'identità assegnata dall'utente](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Creare un'attività

Creare l'attività *helloworldtask* eseguendo il comando [AZ ACR task create][az-acr-task-create] seguente. L'attività viene eseguita senza un contesto del codice sorgente e il comando fa riferimento al file `helloworldtask.yaml` nella directory di lavoro. Il parametro `--assign-identity` senza valore consente di abilitare l'identità assegnata dal sistema nell'attività. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Assegnare le autorizzazioni di pull all'identità al registro di sistema di base

In questa sezione, concedere le autorizzazioni di identità gestite per eseguire il pull dal registro di sistema di base, *mybaseregistry*.

Usare il comando [AZ ACR Show][az-acr-show] per ottenere l'ID risorsa del registro di sistema di base e archiviarlo in una variabile:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Usare il comando [AZ Role Assignment create][az-role-assignment-create] per assegnare l'identità del `acrpull` ruolo al registro di sistema di base. Questo ruolo ha le autorizzazioni solo per eseguire il pull delle immagini dal registro di sistema.

```azurecli
az role assignment create --assignee $principalID --scope $baseregID --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Aggiungere le credenziali del registro di sistema di destinazione all'attività

A questo punto usare il comando [AZ ACR Task Credential Add][az-acr-task-credential-add] per aggiungere le credenziali dell'identità all'attività in modo da poter eseguire l'autenticazione con il registro di sistema di base. Eseguire il comando corrispondente al tipo di identità gestita abilitata nell'attività. Se è stata abilitata un'identità assegnata dall'utente, passare `--use-identity` con l'ID client dell'identità. Se è stata abilitata un'identità assegnata dal sistema, passare `--use-identity [system]`.

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

Per verificare che l'attività in cui è stata abilitata un'identità gestita venga eseguita correttamente, attivare manualmente l'attività con il comando [AZ ACR task run][az-acr-task-run] . 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Se l'attività viene eseguita correttamente, l'output è simile al seguente:

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

Eseguire il comando [AZ ACR repository Show-Tags][az-acr-repository-show-tags] per verificare che l'immagine compilata ed è stata inserita correttamente nel *Registro di sistema*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Output di esempio:

```console
cf10
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull' [Abilitazione di un'identità gestita in un'attività ACR](container-registry-tasks-authentication-managed-identity.md).
* Vedere le informazioni di riferimento su YAML per le [attività ACR](container-registry-tasks-reference-yaml.md)

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
