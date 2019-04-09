---
title: Esercitazione - Automatizzare la compilazione di immagini dei contenitori - Attività di Registro Azure Container
description: Questa esercitazione illustra come configurare un'attività di Registro Azure Container per attivare automaticamente compilazioni delle immagini dei contenitori nel cloud quando si esegue il commit di codice sorgente in un repository Git.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 5aa637938433eb1f906f0a4d81038cec0d6c6dcc
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893011"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Esercitazione: Automatizzare la compilazione di immagini dei contenitori nel cloud quando si esegue il commit di codice sorgente

Oltre a un'[attività rapida](container-registry-tutorial-quick-task.md), ACR Tasks supporta la compilazione automatica di immagini dei contenitori Docker con l'*attività di compilazione*. In questa esercitazione si usa l'interfaccia della riga di comando di Azure per creare un'attività che attiva automaticamente compilazioni delle immagini nel cloud quando si esegue il commit di codice sorgente in un repository Git.

In questa esercitazione, la seconda della serie, vengono illustrate le seguenti attività:

> [!div class="checklist"]
> * Crea un'attività
> * Testare l'attività
> * Visualizzare lo stato dell'attività
> * Attivare l'attività con un commit di codice

Questa esercitazione presuppone che siano già state completate le procedure dell'[esercitazione precedente](container-registry-tutorial-quick-task.md). Se non è già stato fatto, prima di procedere eseguire i passaggi descritti nella sezione [Prerequisiti](container-registry-tutorial-quick-task.md#prerequisites) dell'esercitazione precedente.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario che sia installata l'interfaccia della riga di comando di Azure versione **2.0.46** o successiva ed eseguire l'accesso con [az login][az-login]. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="prerequisites"></a>Prerequisiti

### <a name="get-sample-code"></a>Ottenere il codice di esempio

Questa esercitazione presuppone che siano già state completate le procedure dell'[esercitazione precedente](container-registry-tutorial-quick-task.md) e che siano state eseguite la copia tramite fork e la clonazione del repository di esempio. Se non è già stato fatto, prima di procedere eseguire i passaggi descritti nella sezione [Prerequisiti](container-registry-tutorial-quick-task.md#prerequisites) dell'esercitazione precedente.

### <a name="container-registry"></a>Registro contenitori

Per completare questa esercitazione è necessario che la sottoscrizione di Azure includa un registro contenitori di Azure. Se occorre un registro, vedere l'[esercitazione precedente](container-registry-tutorial-quick-task.md), o la [Guida introduttiva: Create a Registro Container using the Azure CLI](container-registry-get-started-azure-cli.md) (Creare un registro contenitori con l'interfaccia della riga di comando di Azure).

## <a name="overview-of-acr-tasks"></a>Panoramica di ACR Tasks

Un'attività definisce le proprietà di una compilazione automatica, come la posizione del codice sorgente dell'immagine del contenitore e l'evento che attiva la compilazione. Quando si verifica un evento definito nell'attività, ad esempio un commit in un repository Git, ACR Tasks avvia la compilazione di un'immagine del contenitore nel cloud. Per impostazione predefinita, esegue quindi il push dell'immagine compilata correttamente nel registro contenitori di Azure specificato nell'attività.

ACR Tasks attualmente supporta i trigger seguenti:

* Commit in un repository Git
* Aggiornamento dell'immagine di base

In questa esercitazione l'attività del Registro Azure Container crea ed esegue il push di una singola immagine del contenitore specificata in un Dockerfile. Le funzionalità di Attività del Registro Azure Container consentono anche di eseguire [attività in più passi](container-registry-tasks-multi-step.md), usando un file YAML per definire i passaggi per creare, eseguire il push e facoltativamente testare più contenitori.

## <a name="create-a-build-task"></a>Creare un'attività di compilazione

In questa sezione si crea prima di tutto un token di accesso personale GitHub da usare con ACR Tasks. Si crea quindi un'attività che attiva una compilazione quando viene eseguito il commit di codice nel fork del repository.

### <a name="create-a-github-personal-access-token"></a>Creare un token di accesso personale GitHub

Per attivare una compilazione in caso di commit in un repository Git, ACR Tasks necessita di un token di accesso personale per accedere al repository. Per generare un token di accesso personale in GitHub, seguire questa procedura:

1. Passare alla pagina per la creazione di token di accesso personali in GitHub all'indirizzo https://github.com/settings/tokens/new
1. Immettere una breve **descrizione** del token, ad esempio "ACR Tasks Demo".
1. In **repo** abilitare **repo:status** e **public_repo**.

   ![Screenshot della pagina per la generazione di token di accesso personali in GitHub][build-task-01-new-token]

1. Selezionare il pulsante **Generate token** (Genera token). Potrebbe essere richiesto di confermare la password.
1. Copiare e salvare il token generato in una **posizione sicura**. Questo token verrà usato per definire un'attività nella sezione seguente.

   ![Screenshot del token di accesso personale generato in GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Creare l'attività di compilazione

Dopo aver completato i passaggi necessari per consentire ad ACR Tasks di leggere lo stato del commit e creare webhook in un repository, è possibile creare un'attività che attiva la compilazione di un'immagine del contenitore in caso di commit nel repository.

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
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Se in precedenza sono state create attività durante l'anteprima con il comando `az acr build-task`, tali attività devono essere ricreate con il comando [az acr task][az-acr-task].

L'attività specifica che ogni volta che verrà eseguito il commit di codice nel ramo *principale* del repository specificato da `--context`, ACR Tasks compilerà l'immagine del contenitore dal codice in tale ramo. Per creare l'immagine viene usato il Dockerfile specificato da `--file` presente nella radice del repository. L'argomento `--image` specifica un valore `{{.Run.ID}}` con parametri per la parte della versione del tag dell'immagine, affinché l'immagine compilata sia correlata a una compilazione specifica e contrassegnata con un tag univoco.

L'output di un comando [az acr task create][az-acr-task-create] riuscito è simile al seguente:

```console
$ az acr task create \
>     --registry $ACR_NAME \
>     --name taskhelloworld \
>     --image helloworld:{{.Run.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
>     --branch master \
>     --file Dockerfile \
>     --git-access-token $GIT_PAT
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
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
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
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

## <a name="test-the-build-task"></a>Testare l'attività di compilazione

È ora disponibile un'attività che definisce la compilazione. Per testare la pipeline di compilazione, attivare manualmente una compilazione eseguendo il comando [az acr task run][az-acr-task-run]:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Quando viene eseguito dall'utente, il comando `az acr task run` per impostazione predefinita trasmette l'output di log alla console.

```console
$ az acr task run --registry $ACR_NAME --name taskhelloworld

2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>Attivare una compilazione con un commit

Dopo aver testato l'attività eseguendola manualmente, attivarla automaticamente con una modifica del codice sorgente.

Per prima cosa, verificare di trovarsi nella directory contenente il clone locale del [repository][sample-repo]:

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

Dopo aver eseguito il push di un commit nel repository, il webhook creato da ACR Tasks viene attivato e avvia una compilazione in Registro Azure Container. Visualizzare i log dell'attività attualmente in esecuzione per verificarne e monitorarne lo stato di avanzamento:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

L'output è simile al seguente e mostra l'attività attualmente in esecuzione o l'ultima attività eseguita:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>Elencare le compilazioni

Per visualizzare un elenco delle esecuzioni delle attività che ACR Tasks ha completato per il registro, eseguire il comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

L'output del comando dovrebbe essere simile al seguente e mostra le esecuzioni eseguite da ACR Tasks, con "Git Commit" nella colonna TRIGGER per l'attività più recente:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare un'attività per attivare automaticamente compilazioni delle immagini dei contenitori in Azure quando si esegue il commit di codice sorgente in un repository Git. Passare all'esercitazione successiva per informazioni su come creare attività che attivano compilazioni quando viene aggiornata l'immagine di base di un'immagine del contenitore.

> [!div class="nextstepaction"]
> [Automatizzare la compilazione in caso di aggiornamento dell'immagine di base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr
[az-acr-task-create]: /cli/azure/acr
[az-acr-task-run]: /cli/azure/acr
[az-acr-task-list-runs]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
