---
title: Esercitazione - Automatizzare la compilazione di immagini dei contenitori con ACR Build
description: Questa esercitazione illustra come configurare un'attività di compilazione per attivare automaticamente compilazioni delle immagini dei contenitori nel cloud quando si esegue il commit di codice sorgente in un repository Git.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 71ea0f489df6969f0916ac14d187e10a90a520cd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722712"
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Esercitazione: Automatizzare la compilazione di immagini dei contenitori con ACR Build

Oltre alla [compilazione rapida](container-registry-tutorial-quick-build.md), ACR Build supporta la compilazione automatica di immagini dei contenitori Docker con l'*attività di compilazione*. In questa esercitazione si usa l'interfaccia della riga di comando di Azure per creare un'attività di compilazione che attiva automaticamente compilazioni delle immagini nel cloud quando si esegue il commit di codice sorgente in un repository Git.

In questa esercitazione, la seconda della serie, vengono illustrate le seguenti attività:

> [!div class="checklist"]
> * Creare un'attività di compilazione
> * Testare l'attività di compilazione
> * Visualizzare lo stato delle compilazioni
> * Attivare l'attività di compilazione con un commit di codice

Questa esercitazione presuppone che siano già state completate le procedure dell'[esercitazione precedente](container-registry-tutorial-quick-build.md). Se non è già stato fatto, prima di procedere eseguire i passaggi descritti nella sezione [Prerequisiti](container-registry-tutorial-quick-build.md#prerequisites) dell'esercitazione precedente.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario che sia installata l'interfaccia della riga di comando di Azure versione **2.0.32** o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="prerequisites"></a>prerequisiti

### <a name="get-sample-code"></a>Ottenere il codice di esempio

Questa esercitazione presuppone che siano già state completate le procedure dell'[esercitazione precedente](container-registry-tutorial-quick-build.md) e che siano state eseguite la copia tramite fork e la clonazione del repository di esempio. Se non è già stato fatto, prima di procedere eseguire i passaggi descritti nella sezione [Prerequisiti](container-registry-tutorial-quick-build.md#prerequisites) dell'esercitazione precedente.

### <a name="container-registry"></a>Registro contenitori

Per completare questa esercitazione è necessario che la sottoscrizione di Azure includa un registro contenitori di Azure. Se è necessario un registro, vedere l'[esercitazione precedente](container-registry-tutorial-quick-build.md) oppure [Guida introduttiva: Creare un registro contenitori con l'interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).

## <a name="build-task"></a>Attività di compilazione

Un'attività di compilazione definisce le proprietà di una compilazione automatica, come la posizione del codice sorgente dell'immagine del contenitore e l'evento che attiva la compilazione. Quando si verifica un evento definito nell'attività di compilazione, ad esempio un commit in un repository Git, ACR Build avvia la compilazione di un'immagine del contenitore nel cloud. Per impostazione predefinita, esegue quindi il push dell'immagine compilata correttamente nel registro contenitori di Azure specificato nell'attività.

Per l'attività di compilazione, ACR Build supporta attualmente i trigger seguenti:

* Commit in un repository Git
* Aggiornamento dell'immagine di base

## <a name="create-a-build-task"></a>Creare un'attività di compilazione

In questa sezione si crea prima di tutto un token di accesso personale GitHub da usare con ACR Build. Si crea quindi un'attività di compilazione che attiva una compilazione quando viene eseguito il commit di codice nel fork del repository.

### <a name="create-a-github-personal-access-token"></a>Creare un token di accesso personale GitHub

Per attivare una compilazione in caso di commit in un repository Git, ACR Build necessita di un token di accesso personale per accedere al repository. Per generare un token di accesso personale in GitHub, seguire questa procedura:

1. Passare alla pagina per la creazione di token di accesso personali in GitHub all'indirizzo https://github.com/settings/tokens/new
1. Immettere una breve **descrizione** del token, ad esempio "ACR Build Task Demo".
1. In **repo** abilitare **repo:status** e **public_repo**.

   ![Screenshot della pagina per la generazione di token di accesso personali in GitHub][build-task-01-new-token]

1. Selezionare il pulsante **Generate token** (Genera token). Potrebbe essere richiesto di confermare la password.
1. Copiare e salvare il token generato in una **posizione sicura**. Questo token verrà usato per definire un'attività di compilazione nella sezione seguente.

   ![Screenshot del token di accesso personale generato in GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Creare l'attività di compilazione

Dopo aver completato i passaggi necessari per consentire ad ACR Build di leggere lo stato del commit e creare webhook in un repository, è possibile creare un'attività di compilazione che attiva la compilazione di un'immagine del contenitore in caso di commit nel repository.

Per prima cosa, popolare queste variabili di ambiente della shell con i valori appropriati per l'ambiente in uso. Questa operazione non è obbligatoria, ma semplifica in parte l'esecuzione dei comandi su più righe dell'interfaccia della riga di comando di Azure. Se non si popolano queste variabili, sarà necessario sostituire manualmente ogni valore in ogni occorrenza nei comandi di esempio.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

Creare quindi l'attività di compilazione eseguendo questo comando [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

L'attività di compilazione specifica che ogni volta che verrà eseguito il commit di codice nel ramo *principale* del repository specificato da `--context`, ACR Build compilerà l'immagine del contenitore dal codice in tale ramo. L'argomento `--image` specifica un valore `{{.Build.ID}}` con parametri per la parte della versione del tag dell'immagine, affinché l'immagine compilata sia correlata a una compilazione specifica e contrassegnata con un tag univoco.

L'output di un comando [az acr build-task create][az-acr-build-task-create] riuscito è simile al seguente:

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>Testare l'attività di compilazione

È ora disponibile un'attività di compilazione che definisce la compilazione. Per testare tale definizione, attivare manualmente una compilazione eseguendo il comando [az acr build-task run][az-acr-build-task-run]:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Quando viene eseguito dall'utente, il comando `az acr build-task run` per impostazione predefinita trasmette l'output di log alla console. L'output seguente mostra che la compilazione **aa2** è stata inserita in coda e compilata.

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>Visualizzare lo stato delle compilazioni

In alcuni casi può essere utile visualizzare lo stato di una compilazione in corso che non è stata attivata manualmente, ad esempio durante la risoluzione dei problemi relativi alle compilazioni attivate da commit del codice sorgente. In questa sezione si attiva una compilazione manuale eliminando però il comportamento predefinito in base al quale il log della compilazione viene trasmesso alla console. Si usa quindi il comando `az acr build-task logs` per monitorare la compilazione in corso.

Per prima cosa, attivare manualmente una compilazione come è stato fatto in precedenza, specificando però l'argomento `--no-logs` per eliminare la registrazione nella console:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

Usare quindi il comando `az build-task logs` per visualizzare il log della compilazione attualmente in esecuzione:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Il log della compilazione attualmente in esecuzione viene trasmesso alla console e dovrebbe essere simile all'output seguente (qui troncato):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>Attivare una compilazione con un commit

Dopo aver testato l'attività di compilazione eseguendola manualmente, attivarla automaticamente con una modifica del codice sorgente.

Per prima cosa, verificare di trovarsi nella directory contenente il clone locale del [repository][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Eseguire quindi questi comandi per eseguire la creazione, il commit e il push di un nuovo file nel fork del repository in GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Quando si esegue il comando `git push`, potrebbe essere richiesto di specificare le credenziali di GitHub. Immettere il nome utente GitHub e il token di accesso personale creato in precedenza per la password.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Dopo aver eseguito il push di un commit nel repository, il webhook creato da ACR Build viene attivato e avvia una compilazione in Registro contenitori di Azure. Visualizzare i log della compilazione attualmente in esecuzione per verificarne e monitorarne lo stato di avanzamento:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

L'output è simile al seguente e mostra la compilazione attualmente in esecuzione o l'ultima compilazione eseguita:

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>Elencare le compilazioni

Per visualizzare un elenco delle compilazioni completate da ACR Build per il registro, eseguire il comando [az acr build-task list-builds][az-acr-build-task-list-builds]:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

L'output del comando dovrebbe essere simile al seguente e mostra le compilazioni eseguite da ACR Build, con "Git Commit" nella colonna TRIGGER per la compilazione più recente:

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare un'attività di compilazione per attivare automaticamente compilazioni delle immagini dei contenitori in Azure quando si esegue il commit di codice sorgente in un repository Git. Passare all'esercitazione successiva per informazioni su come creare attività di compilazione che attivano compilazioni quando viene aggiornata l'immagine di base di un'immagine del contenitore.

> [!div class="nextstepaction"]
> [Automatizzare la compilazione in caso di aggiornamento dell'immagine di base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
