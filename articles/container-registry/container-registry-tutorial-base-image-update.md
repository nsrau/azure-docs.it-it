---
title: Esercitazione - Automatizzare la compilazione di immagini dei contenitori in caso di aggiornamento dell'immagine - Attività di Registro Azure Container
description: Questa esercitazione illustra come configurare un'attività di Registro Azure Container per attivare automaticamente le compilazioni delle immagini dei contenitori nel cloud quando viene aggiornata un'immagine di base.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 781e64c57c53412e999de98b937d568097b2825e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55204117"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Esercitazione: Automatizzare la compilazione di immagini dei contenitori quando viene aggiornata un'immagine in Registro Azure Container 

ACR Tasks supporta l'esecuzione automatica della compilazione quando viene aggiornata l'immagine di base di un contenitore, ad esempio per applicare patch al sistema operativo o al framework applicazioni in una delle immagini di base. Questa esercitazione illustra come creare un'attività in ACR Tasks che attiva una compilazione nel cloud in seguito al push nel registro dell'immagine di base di un contenitore.

In questa esercitazione, che è l'ultima della serie, si apprenderà come:

> [!div class="checklist"]
> * Compilare l'immagine di base
> * Creare un'attività di compilazione dell'immagine dell'applicazione
> * Aggiornare l'immagine di base per attivare un'attività dell'immagine dell'applicazione
> * Visualizzare l'attività attivata
> * Verificare l'immagine dell'applicazione aggiornata

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario che sia installata l'interfaccia della riga di comando di Azure versione **2.0.46** o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="prerequisites"></a>Prerequisiti

### <a name="complete-the-previous-tutorials"></a>Completare le esercitazioni precedenti

Questa esercitazione presuppone che siano già state completate le procedure delle prime due esercitazioni della serie, in cui viene illustrato come:

* Creare un'istanza di Registro Azure Container
* Creare una copia tramite fork del repository di esempio
* Clonare il repository di esempio
* Creare un token di accesso personale GitHub

Se non è già stato fatto, prima di procedere completare le prime due esercitazioni:

[Compilare immagini dei contenitori nel cloud con ACR Tasks](container-registry-tutorial-quick-task.md)

[Automatizzare la compilazione di immagini dei contenitori con ACR Tasks](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Configurare l'ambiente

Popolare queste variabili di ambiente della shell con i valori appropriati per l'ambiente in uso. Questo passaggio non è obbligatorio, ma semplifica in parte l'esecuzione dei comandi su più righe dell'interfaccia della riga di comando di Azure. Se non si popolano queste variabili di ambiente, sarà necessario sostituire manualmente ogni valore in ogni occorrenza nei comandi di esempio.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Immagini di base

I documenti Dockerfile che definiscono la maggior parte delle immagini dei contenitori specificano un'immagine padre su cui sono basate, spesso denominata *immagine di base*. Le immagini di base contengono in genere il sistema operativo, ad esempio [Alpine Linux][base-alpine] o [Windows Server Nano][base-windows], su cui vengono applicati i restanti livelli del contenitore. Questi possono includere anche framework applicazioni come [Node.js][base-node] o [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Aggiornamenti dell'immagine di base

Un'immagine di base viene spesso aggiornata dal gestore delle immagini per includere nuove funzionalità o nuovi miglioramenti del sistema operativo o del framework. Un'altra comune causa di aggiornamento dell'immagine di base è costituita dalle patch di sicurezza.

Quando viene aggiornata un'immagine di base, diventa necessario ricompilare tutte le immagini dei contenitori nel registro che sono basate su di essa, per includere le nuove funzionalità e i nuovi aggiornamenti. ACR Tasks include la possibilità di compilare automaticamente le immagini quando viene aggiornata l'immagine di base di un contenitore.

### <a name="base-image-update-scenario"></a>Scenario di aggiornamento dell'immagine di base

Questa esercitazione illustra uno scenario di aggiornamento dell'immagine di base. L'[esempio di codice][code-sample] include due Dockerfile: l'immagine di un'applicazione e un'immagine specificata come relativa base. Nelle sezioni seguenti si crea un'attività di ACR Tasks che attiva automaticamente una compilazione dell'immagine dell'applicazione quando viene eseguito il push di una nuova versione dell'immagine di base nel registro contenitori.

[Dockerfile-app][dockerfile-app]: applicazione Web Node.js di piccole dimensioni che esegue il rendering di una pagina Web statica in cui viene visualizzata la versione di Node.js su cui è basata. La stringa della versione è simulata, perché mostra il contenuto della variabile di ambiente `NODE_VERSION` definita nell'immagine di base.

[Dockerfile-base][dockerfile-base]: immagine specificata da `Dockerfile-app` come base. È a propria volta basata su un'immagine [Node][base-node] e include la variabile di ambiente `NODE_VERSION`.

Nelle sezioni seguenti si crea un'attività, si aggiorna il valore di `NODE_VERSION` nel documento Dockerfile dell'immagine di base e quindi si usa ACR Tasks per compilare l'immagine di base. Quando l'attività di ACR Tasks esegue il push della nuova immagine di base nel registro, attiva automaticamente una compilazione dell'immagine dell'applicazione. Facoltativamente, si esegue l'immagine del contenitore dell'applicazione in locale per verificare le diverse stringhe della versione nelle immagini compilate.

## <a name="build-the-base-image"></a>Compilare l'immagine di base

Per iniziare, compilare l'immagine di base con un'*attività rapida* di ACR Tasks. Come illustrato nella [prima esercitazione](container-registry-tutorial-quick-task.md) della serie, questo processo non solo compila l'immagine, ma ne esegue anche il push nel registro contenitori, se la compilazione viene completata correttamente.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Crea un'attività

Creare quindi un'attività con [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Se in precedenza sono state create attività durante l'anteprima con il comando `az acr build-task`, tali attività devono essere ricreate con il comando [az acr task][az-acr-task].

Questa attività, simile a quella rapida creata nell'[esercitazione precedente](container-registry-tutorial-build-task.md), indica ad ACR Tasks di attivare una compilazione dell'immagine quando viene eseguito il push di commit nel repository specificato da `--context`.

La differenza risiede nel comportamento, perché viene attivata una compilazione dell'immagine anche quando viene aggiornata la relativa *immagine di base*. Il documento Dockerfile specificato dall'argomento `--file`, [Dockerfile-app][dockerfile-app], supporta la specifica di un'immagine nello stesso registro della base:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Quando si esegue un'attività, ACR Tasks rileva le dipendenze di un'immagine. Se l'immagine di base specificata nell'istruzione `FROM` risiede nello stesso registro o in un repository dell'hub Docker pubblico, viene aggiunto un hook per garantire la ricompilazione dell'immagine ogni volta che viene aggiornata la relativa base.

## <a name="build-the-application-container"></a>Compilare il contenitore dell'applicazione

Per consentire ad ACR Tasks di determinare le dipendenze di un'immagine del contenitore, tra cui l'immagine di base, e tenerne traccia, è prima di tutto **necessario** attivare l'attività **almeno una volta**.

Usare [az acr task run][az-acr-task-run] per attivare manualmente l'attività e compilare l'immagine dell'applicazione:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Al termine dell'attività, se si intende completare il passaggio facoltativo seguente prendere nota del valore di **ID esecuzione** (ad esempio, "da6").

### <a name="optional-run-application-container-locally"></a>Facoltativo: eseguire il contenitore dell'applicazione in locale

Se si lavora in locale (non in Cloud Shell) ed è installato Docker, eseguire il contenitore per verificare il rendering dell'applicazione in un Web browser prima di ricompilare l'immagine di base. Se si usa Cloud Shell, ignorare questa sezione. Cloud Shell non supporta `az acr login` né `docker run`.

Per prima cosa, accedere al registro contenitori con [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Eseguire quindi il contenitore in locale con `docker run`. Sostituire **\<run-id\>** con il valore di "ID esecuzione" riportato nell'output del passaggio precedente (ad esempio, "da6").

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Passare a http://localhost:8080 nel browser. Nella pagina Web verrà visualizzato il rendering del numero di versione di Node.js, simile allo screenshot seguente. In un passaggio successivo si modificherà la versione aggiungendo una "a" alla stringa della versione.

![Screenshot del rendering dell'applicazione di esempio nel browser][base-update-01]

## <a name="list-the-builds"></a>Elencare le compilazioni

Elencare quindi le esecuzioni delle attività che ACR Tasks ha completato per il registro usando il comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Se è stata completata l'esercitazione precedente e non è stato eliminato il registro, l'output visualizzato dovrebbe essere simile al seguente. Prendere nota del numero di esecuzioni delle attività e del valore di ID esecuzione più recente per poter confrontare l'output dopo l'aggiornamento dell'immagine di base nella sezione successiva.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Aggiornare l'immagine di base

In questa sezione si simula l'applicazione di una patch del framework nell'immagine di base. Modificare **Dockerfile-base** e aggiungere una "a" dopo il numero di versione definito in `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Eseguire un'attività rapida per compilare l'immagine di base modificata. Prendere nota del valore di **ID esecuzione** nell'output.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Al termine della compilazione e del push della nuova immagine di base nel registro, ACR Tasks attiva una compilazione dell'immagine dell'applicazione. Prima che l'attività creata in precedenza attivi la compilazione dell'immagine dell'applicazione potrebbero trascorrere alcuni istanti, perché deve essere rilevata l'immagine di base appena compilata e sottoposta a push.

## <a name="list-updated-build"></a>Elencare la compilazione aggiornata

Dopo aver aggiornato l'immagine di base, visualizzare di nuovo l'elenco delle esecuzioni delle attività per confrontarlo con l'elenco precedente. Se inizialmente l'output non presenta differenze, eseguire periodicamente il comando finché la nuova esecuzione dell'attività non risulta inclusa nell'elenco.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

L'output è simile al seguente. Il valore di TRIGGER per l'ultima compilazione eseguita sarà "Image Update", che indica che l'attività è stata avviata dall'attività rapida dell'immagine di base.

```console
$ az acr task list-builds --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Se si vuole eseguire il passaggio facoltativo seguente che prevede l'esecuzione del contenitore appena compilato per visualizzare il numero di versione aggiornato, prendere nota del valore di **ID esecuzione** della compilazione con trigger "Image Update" (nell'output precedente, "da8").

### <a name="optional-run-newly-built-image"></a>Facoltativo: eseguire l'immagine appena compilata

Se si lavora in locale (non in Cloud Shell) ed è installato Docker, al termine della compilazione eseguire la nuova immagine dell'applicazione. Sostituire `<run-id>` con il valore di ID esecuzione ottenuto nel passaggio precedente. Se si usa Cloud Shell, ignorare questa sezione. Cloud Shell non supporta `docker run`.

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Passare a http://localhost:8081 nel browser. Nella pagina Web verrà visualizzato il numero di versione di Node.js aggiornato con "a":

![Screenshot del rendering dell'applicazione di esempio nel browser][base-update-02]

È importante notare che si è aggiornata l'immagine di **base** con un nuovo numero di versione, ma questo viene visualizzato nell'ultima immagine compilata dell'**applicazione**. ACR Tasks ha rilevato la modifica apportata all'immagine di base e ha ricompilato automaticamente l'immagine dell'applicazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create in questa serie di esercitazioni, inclusi il registro contenitori, l'istanza del contenitore, l'insieme di credenziali delle chiavi e l'entità servizio, eseguire questi comandi:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare un'attività per attivare automaticamente compilazioni delle immagini dei contenitori in seguito all'aggiornamento dell'immagine di base. Passare ora alle informazioni sull'autenticazione per il registro contenitori.

> [!div class="nextstepaction"]
> [Autenticazione in Registro Azure Container](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr
[az-acr-task-run]: /cli/azure/acr#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr#az-acr-task

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
