---
title: Esercitazione - Attività del contenitore in più passaggi - Attività del Registro Azure Container
description: Questa esercitazione illustra come configurare un'attività del Registro Azure Container per attivare automaticamente un flusso di lavoro in più passaggi per la compilazione, l'esecuzione e il push delle immagini dei contenitori nel cloud quando si esegue il commit di codice sorgente in un repository Git.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 913f535cb7fa07832a272c1cb9d02ab3e885f52c
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743575"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Esercitazione: Eseguire un flusso di lavoro dei contenitori in più passaggi nel cloud quando si esegue il commit di codice sorgente

Oltre alle [attività rapide](container-registry-tutorial-quick-task.md), Attività del Registro Azure Container supporta flussi di lavoro in più passaggi e basati su più contenitori che si possono attivare automaticamente quando si esegue il commit di codice sorgente in un repository Git. 

Questa esercitazione descrive come usare i file YAML di esempio per definire attività in più passaggi per la compilazione, l'esecuzione e il push di una o più immagini del contenitore in un registro quando si esegue il commit di codice sorgente. Per creare un'attività che consente di automatizzare solo la compilazione di una singola immagine con il commit di codice, consultare [Esercitazione: Automatizzare la compilazione di immagini dei contenitori nel cloud quando si esegue il commit di codice sorgente](container-registry-tutorial-build-task.md). Per una panoramica di Attività del Registro Azure Container, consultare [Automatizzare l'applicazione di patch dei sistemi operativi e del framework con Attività del Registro Azure Container](container-registry-tasks-overview.md),

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Definire un'attività in più passaggi usando un file YAML
> * Crea un'attività
> * Aggiungere facoltativamente credenziali all'attività per abilitare l'accesso a un altro registro
> * Testare l'attività
> * Visualizzare lo stato dell'attività
> * Attivare l'attività con un commit di codice

Questa esercitazione presuppone che siano già state completate le procedure dell'[esercitazione precedente](container-registry-tutorial-quick-task.md). Se non è già stato fatto, prima di procedere eseguire i passaggi descritti nella sezione [Prerequisiti](container-registry-tutorial-quick-task.md#prerequisites) dell'esercitazione precedente.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario che sia installata la versione **2.0.62** o successiva e che sia stato eseguito l'accesso con [az login][az-login]. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Creare un'attività in più passaggi

Dopo aver completato i passaggi necessari per consentire ad Attività del Registro Azure Container di leggere lo stato del commit e creare webhook in un repository, creare un'attività in più passaggi che attiva la compilazione, l'esecuzione e il push di un'immagine del contenitore.

### <a name="yaml-file"></a>File YAML

I passaggi per un'attività in più passaggi si definiscono in un [file YAML](container-registry-tasks-reference-yaml.md). Il primo esempio di attività in più passaggi per questa esercitazione è definito nel file `taskmulti.yaml`, disponibile nella radice del repository GitHub clonato:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Le operazioni di un'attività in più passaggi sono le seguenti:

1. Esegue un passaggio `build` per compilare un'immagine dal Dockerfile nella directory di lavoro. L'immagine è indirizzata verso `Run.Registry`, il registro in cui si esegue l'attività, quindi viene contrassegnata con un ID di esecuzione di Attività del Registro Azure Container univoco. 
1. Esegue un passaggio `cmd` per eseguire l'immagine in un contenitore temporaneo. In questo esempio si avvia un contenitore in background con esecuzione prolungata e se ne ottiene l'ID, quindi viene arrestato. In uno scenario reale, è possibile includere passaggi per testare il contenitore in esecuzione per garantire che sia eseguito correttamente.
1. In un passaggio `push`, si esegue il push dell'immagine compilata nel registro di esecuzione.

### <a name="task-command"></a>Comando dell'attività

Per prima cosa, popolare queste variabili di ambiente della shell con i valori appropriati per l'ambiente in uso. Questo passaggio non è obbligatorio, ma semplifica in parte l'esecuzione dei comandi su più righe dell'interfaccia della riga di comando di Azure. Se non si popolano queste variabili di ambiente, sarà necessario sostituire manualmente ogni valore in ogni occorrenza nei comandi di esempio.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Creare quindi l'attività eseguendo questo comando [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

L'attività specifica che ogni volta che verrà eseguito il commit di codice nel ramo *principale* del repository specificato da `--context`, Attività del Registro Azure Container eseguirà l'attività in più passaggi dal codice in tale ramo. Il file YAML specificato da `--file` nella radice del repository definisce i passaggi. 

L'output di un comando [az acr task create][az-acr-task-create] riuscito è simile al seguente:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Testare il flusso di lavoro in più passaggi

Per testare l'attività in più passaggi, attivarla manualmente eseguendo il comando [az acr task run][az-acr-task-run]:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Quando viene eseguito dall'utente, il comando `az acr task run` per impostazione predefinita trasmette l'output di log alla console. L'output mostra lo stato di avanzamento dell'esecuzione di ogni passaggio dell'attività. L'output seguente è ridotto per mostrare i passaggi principali.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Attivare una compilazione con un commit

Dopo aver testato l'attività eseguendola manualmente, attivarla automaticamente con una modifica del codice sorgente.

Prima di tutto, verificare di trovarsi nella directory contenente il clone locale del [repository][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Eseguire quindi questi comandi per eseguire la creazione, il commit e il push di un nuovo file nel fork del repository in GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Quando si esegue il comando `git push`, potrebbe essere richiesto di specificare le credenziali di GitHub. Immettere il nome utente GitHub e il token di accesso personale creato in precedenza per la password.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Dopo aver eseguito il push di un commit nel repository, il webhook creato da Attività del Registro Azure Container viene attivato e avvia un'attività in Registro Azure Container. Visualizzare i log dell'attività attualmente in esecuzione per verificarne e monitorarne lo stato di avanzamento:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

L'output è simile al seguente e mostra l'attività attualmente in esecuzione o l'ultima attività eseguita:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Elencare le compilazioni

Per visualizzare un elenco delle esecuzioni di attività completate da Attività del Registro Azure Container, eseguire il comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

L'output del comando dovrebbe essere simile al seguente e mostra le esecuzioni eseguite da ACR Tasks, con "Git Commit" nella colonna TRIGGER per l'attività più recente:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Creare un'attività in più passaggi per più registri

Attività del Registro Azure Container, per impostazione predefinita, dispone delle autorizzazioni per il push o il pull delle immagini dal registro in cui viene eseguita l'attività. Potrebbe essere necessario eseguire un'attività in più passaggi che ha come destinazione uno o più registri aggiuntivi, oltre al registro di esecuzione. Ad esempio, potrebbe essere necessario compilare le immagini in un registro e memorizzarle con tag diversi in un altro, a cui si accede tramite un sistema di produzione. Questo esempio illustra come creare un'attività di questo tipo e fornire le credenziali per un altro registro.

Se non si dispone già di un secondo registro, crearne uno per questo esempio. Se occorre un registro, vedere l'[esercitazione precedente](container-registry-tutorial-quick-task.md), o la [Guida introduttiva: Create a Registro Container using the Azure CLI](container-registry-get-started-azure-cli.md) (Creare un registro contenitori con l'interfaccia della riga di comando di Azure).

Per creare l'attività è necessario il nome del server di accesso del registro, che è nel formato *mycontainerregistrydate.azurecr.io* (tutto minuscolo). In questo esempio si usa il secondo registro per memorizzare le immagini contrassegnate per data di compilazione.

### <a name="yaml-file"></a>File YAML

Il secondo esempio di attività in più passaggi per questa esercitazione è definito nel file `taskmulti-multiregistry.yaml`, disponibile nella radice del repository GitHub clonato:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Le operazioni di un'attività in più passaggi sono le seguenti:

1. Esegue due passaggi `build` per compilare immagini dal Dockerfile nella directory di lavoro:
    * La prima è indirizzata verso `Run.Registry`, il registro in cui si esegue l'attività, quindi viene contrassegnata con l'ID di esecuzione di Attività del Registro Azure Container univoco. 
    * Il secondo è indirizzato verso il registro identificato con il valore `regDate`, impostato quando si crea l'attività (o fornito tramite un file `values.yaml` passato in `az acr task create`). Questa immagine viene contrassegnata con la data di esecuzione.
1. Esegue un passaggio `cmd` per eseguire uno dei contenitori compilati. In questo esempio si avvia un contenitore in background con esecuzione prolungata e se ne ottiene l'ID, quindi viene arrestato. In uno scenario reale, è possibile testare un contenitore in esecuzione per garantire che sia eseguito correttamente.
1. In un passaggio `push`, effettua il push delle immagini compilate: la prima per l'esecuzione del registro, la seconda per il registro identificato da `regDate`.

### <a name="task-command"></a>Comando dell'attività

Usando le variabili di ambiente shell definite in precedenza, creare l'attività eseguendo il comando [az acr task create][az-acr-task-create] seguente. Sostituire *mycontainerregistrydate* con il nome del proprio registro.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Aggiungere le credenziali dell'attività

Per eseguire il push delle immagini nel registro identificato dal valore `regDate`, usare il comando [az acr task credential add][az-acr-task-credential-add] per aggiungere le credenziali di accesso a quel registro nell'attività.

Per questo esempio, è consigliabile creare un'[entità servizio](container-registry-auth-service-principal.md) con accesso al registro con ambito del ruolo *AcrPush*. Per creare l'entità servizio, consultare questo [script dell'interfaccia della riga di comando di Azure](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Passare l'ID applicazione e la password dell'entità servizio nel seguente comando `az acr task credential add`:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

L'interfaccia della riga di comando restituisce il nome del server di accesso del registro aggiunto.

### <a name="test-the-multi-step-workflow"></a>Testare il flusso di lavoro in più passaggi

Come nell'esempio precedente, per testare l'attività in più passaggi, attivarla manualmente eseguendo il comando [az acr task run][az-acr-task-run]. Per attivare l'attività con un commit nel repository Git, vedere la sezione [Attivare una compilazione con un commit](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Quando viene eseguito dall'utente, il comando `az acr task run` per impostazione predefinita trasmette l'output di log alla console. Come in precedenza, l'output mostra lo stato di avanzamento dell'esecuzione di ogni passaggio dell'attività. L'output è ridotto in modo da mostrare i passaggi principali.

Output:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare attività in più passaggi e basate su più contenitori che si attivano automaticamente quando si eseguire il commit di codice sorgente in un repository Git. Per le funzionalità avanzate delle attività in più passaggi, tra cui l'esecuzione parallela e dipendente del passaggio, consultare il [Riferimento YAML per Attività del Registro Azure Container](container-registry-tasks-reference-yaml.md). Passare all'esercitazione successiva per informazioni su come creare attività che attivano compilazioni quando viene aggiornata l'immagine di base di un'immagine del contenitore.

> [!div class="nextstepaction"]
> [Automatizzare la compilazione in caso di aggiornamento dell'immagine di base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
