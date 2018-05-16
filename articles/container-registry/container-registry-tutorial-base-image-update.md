---
title: Esercitazione - Automatizzare la compilazione di immagini dei contenitori in caso di aggiornamento dell'immagine di base con ACR Build
description: Questa esercitazione illustra come configurare un'attività di compilazione per attivare automaticamente compilazioni delle immagini dei contenitori nel cloud quando viene aggiornata un'immagine di base.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 976f61d99b88d241b39bfec9d95e16de272d9c14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>Esercitazione: Automatizzare la compilazione di immagini in caso di aggiornamento dell'immagine di base con ACR Build

ACR Build supporta l'esecuzione automatica della compilazione quando viene aggiornata l'immagine di base di un contenitore, ad esempio per applicare patch al sistema operativo o al framework applicazioni in una delle immagini di base. Questa esercitazione illustra come creare in ACR Build un'attività di compilazione che attiva una compilazione nel cloud in seguito al push nel registro dell'immagine di base di un contenitore.

In questa esercitazione, che è l'ultima della serie, si apprenderà come:

> [!div class="checklist"]
> * Compilare l'immagine di base
> * Creare un'attività di compilazione dell'immagine dell'applicazione
> * Aggiornare l'immagine di base per attivare una compilazione dell'immagine dell'applicazione
> * Visualizzare la compilazione attivata
> * Verificare l'immagine dell'applicazione aggiornata

> [!IMPORTANT]
> ACR Build è attualmente in anteprima ed è supportato dai registri contenitori di Azure solo nelle aree **Stati Uniti orientali** (eastus) ed **Europa occidentale** (westeurope). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario che sia installata l'interfaccia della riga di comando di Azure versione **2.0.32** o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli].

## <a name="prerequisites"></a>Prerequisiti

### <a name="complete-previous-tutorials"></a>Completare le esercitazioni precedenti

Questa esercitazione presuppone che siano già state completate le procedure delle prime due esercitazioni della serie, in cui viene illustrato come:

* Creare un'istanza di Registro contenitori di Azure
* Creare una copia tramite fork del repository di esempio
* Clonare il repository di esempio
* Creare un token di accesso personale GitHub

Se non è già stato fatto, prima di procedere completare le prime due esercitazioni:

[Compilare immagini dei contenitori nel cloud con ACR Build](container-registry-tutorial-quick-build.md)

[Automatizzare la compilazione di immagini dei contenitori con ACR Build](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>Configurare l'ambiente

Popolare queste variabili di ambiente della shell con i valori appropriati per l'ambiente in uso. Questa operazione non è obbligatoria, ma semplifica in parte l'esecuzione dei comandi su più righe dell'interfaccia della riga di comando di Azure. Se non si popolano queste variabili, sarà necessario sostituire manualmente ogni valore in ogni occorrenza nei comandi di esempio.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Immagini di base

I documenti Dockerfile che definiscono la maggior parte delle immagini dei contenitori specificano un'immagine padre su cui sono basate, spesso denominata *immagine di base*. Le immagini di base contengono in genere il sistema operativo, ad esempio [Alpine Linux][base-alpine] o [Windows Server Nano][base-windows], su cui vengono applicati i restanti livelli del contenitore. Questi possono includere anche framework applicazioni come [Node.js][base-node] o [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Aggiornamenti dell'immagine di base

Un'immagine di base viene spesso aggiornata dal gestore delle immagini per includere nuove funzionalità o nuovi miglioramenti del sistema operativo o del framework. Un'altra comune causa di aggiornamento dell'immagine di base è costituita dalle patch di sicurezza.

Quando viene aggiornata un'immagine di base, diventa necessario ricompilare tutte le immagini dei contenitori nel registro che sono basate su di essa, per includere le nuove funzionalità e i nuovi aggiornamenti. ACR Build include la possibilità di compilare automaticamente le immagini quando viene aggiornata l'immagine di base di un contenitore.

### <a name="base-image-update-scenario"></a>Scenario di aggiornamento dell'immagine di base

Questa esercitazione illustra uno scenario di aggiornamento dell'immagine di base. L'[esempio di codice][code-sample] include due Dockerfile: l'immagine di un'applicazione e un'immagine specificata come relativa base. Nelle sezioni seguenti si crea un'attività di ACR Build che attiva automaticamente una compilazione dell'immagine dell'applicazione quando viene eseguito il push di una nuova versione dell'immagine di base nel registro contenitori.

[Dockerfile-app][dockerfile-app]: applicazione Web Node.js di piccole dimensioni che esegue il rendering di una pagina Web statica in cui viene visualizzata la versione di Node.js su cui è basata. La stringa della versione è simulata, perché mostra il contenuto della variabile di ambiente `NODE_VERSION` definita nell'immagine di base.

[Dockerfile-base][dockerfile-base]: immagine specificata da `Dockerfile-app` come base. È a propria volta basata su un'immagine [Node][base-node] e include la variabile di ambiente `NODE_VERSION`.

Nelle sezioni seguenti si crea un'attività di compilazione, si aggiorna il valore di `NODE_VERSION` nel documento Dockerfile dell'immagine di base e quindi si usa ACR Build per compilare l'immagine di base. Quando ACR Build esegue il push della nuova immagine di base nel registro, attiva automaticamente una compilazione dell'immagine dell'applicazione. Facoltativamente, si esegue l'immagine del contenitore dell'applicazione in locale per verificare le diverse stringhe della versione nelle immagini compilate.

## <a name="build-base-image"></a>Compilare l'immagine di base

Per iniziare, compilare l'immagine di base con una *compilazione rapida* di ACR Build. Come illustrato nella [prima esercitazione](container-registry-tutorial-quick-build.md) della serie, in questo modo non solo viene compilata l'immagine, ma ne viene anche eseguito il push nel registro contenitori, se la compilazione viene completata correttamente.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>Creare l'attività di compilazione

Creare quindi un'attività di compilazione con [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

L'attività di compilazione, simile a quella creata nell'[esercitazione precedente](container-registry-tutorial-build-task.md), indica ad ACR Build di attivare una compilazione dell'immagine quando viene eseguito il push di commit nel repository specificato da `--context`.

La differenza risiede nel comportamento, perché viene attivata una compilazione dell'immagine anche quando viene aggiornata la relativa *immagine di base*. Il documento Dockerfile specificato dall'argomento `--file`, [Dockerfile-app][dockerfile-app], supporta la specifica di un'immagine nello stesso registro della base:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Quando si esegue un'attività di compilazione, ACR Build rileva le dipendenze di un'immagine. Se l'immagine di base specificata nell'istruzione `FROM` risiede nello stesso registro, viene aggiunto un hook per garantire la ricompilazione dell'immagine ogni volta che viene aggiornata la relativa base.

> [!NOTE]
> Durante l'anteprima, ACR Build supporta l'attivazione di una compilazione dell'immagine derivata solo quando sia l'immagine di base che quella che vi fa riferimento risiedono nello stesso registro contenitori di Azure.

## <a name="build-application-container"></a>Compilare il contenitore dell'applicazione

Per consentire ad ACR Build di determinare le dipendenze di un'immagine del contenitore, tra cui l'immagine di base, e tenerne traccia, è prima di tutto **necessario** attivare l'attività di compilazione **almeno una volta**.

Usare [az acr build-task run][az-acr-build-task-run] per attivare manualmente l'attività di compilazione e compilare l'immagine dell'applicazione:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Al termine della compilazione, se si intende completare il passaggio facoltativo seguente prendere nota del valore di **Build ID** (ad esempio, "eastus6").

### <a name="optional-run-application-container-locally"></a>Facoltativo: eseguire il contenitore dell'applicazione in locale

Se si lavora in locale (non in Cloud Shell) ed è installato Docker, eseguire il contenitore per verificare il rendering dell'applicazione in un Web browser prima di ricompilare l'immagine di base. Se si usa Cloud Shell, ignorare questa sezione. Cloud Shell non supporta `az acr login` né `docker run`.

Per prima cosa, accedere al registro contenitori con [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Eseguire quindi il contenitore in locale con `docker run`. Sostituire **\<build-id\>** con il valore di "Build ID" riportato nell'output del passaggio precedente (ad esempio, "eastus5").

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Passare a http://localhost:8080 nel browser. Nella pagina Web verrà visualizzato il rendering del numero di versione di Node.js, simile allo screenshot seguente. In un passaggio successivo si modificherà la versione aggiungendo una "a" alla stringa della versione.

![Screenshot del rendering dell'applicazione di esempio nel browser][base-update-01]

## <a name="list-builds"></a>Elencare le compilazioni

Elencare quindi le compilazioni completate da ACR Build per il registro:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Se è stata completata l'esercitazione precedente e non è stato eliminato il registro, l'output visualizzato dovrebbe essere simile al seguente. Prendere nota del numero di compilazioni e del valore di BUILD ID più recente per poter confrontare l'output dopo l'aggiornamento dell'immagine di base nella sezione successiva.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
eastus6     buildhelloworld  Linux       Succeeded  Manual        2018-04-22T00:03:46Z  00:00:40
eastus5                                  Succeeded  Manual        2018-04-22T00:01:45Z  00:00:25
eastus4     buildhelloworld  Linux       Succeeded  Git Commit    2018-04-21T23:52:33Z  00:00:30
eastus3     buildhelloworld  Linux       Succeeded  Manual        2018-04-21T23:50:10Z  00:00:35
eastus2     buildhelloworld  Linux       Succeeded  Manual        2018-04-21T23:46:15Z  00:00:55
eastus1                                  Succeeded  Manual        2018-04-21T23:24:05Z  00:00:35
```

## <a name="update-base-image"></a>Aggiornare l'immagine di base

In questa sezione si simula l'applicazione di una patch del framework nell'immagine di base. Modificare **Dockerfile-base** e aggiungere una "a" dopo il numero di versione definito in `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

Eseguire una compilazione rapida in ACR Build per compilare l'immagine di base modificata. Prendere nota del valore di **Build ID** nell'output.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Al termine della compilazione e del push della nuova immagine di base nel registro, ACR Build attiva una compilazione dell'immagine dell'applicazione. Prima che l'attività di ACR Build creata in precedenza attivi la compilazione dell'immagine dell'applicazione potrebbero trascorrere alcuni istanti, perché deve rilevare l'immagine di base appena completata e sottoposta a push.

## <a name="list-builds"></a>Elencare le compilazioni

Dopo aver aggiornato l'immagine di base, visualizzare di nuovo l'elenco delle compilazioni per confrontarlo con l'elenco precedente. Se inizialmente l'output non presenta differenze, eseguire periodicamente il comando finché la nuova compilazione non risulta inclusa nell'elenco.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

L'output è simile al seguente. Il valore di TRIGGER per l'ultima compilazione eseguita sarà "Image Update", che indica che l'attività di compilazione è stata avviata dalla compilazione rapida dell'immagine di base.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ----------    --------------------  ----------
eastus8     buildhelloworld  Linux       Succeeded  Image Update  2018-04-22T00:09:24Z  00:00:50
eastus7                                  Succeeded  Manual        2018-04-22T00:08:49Z  00:00:40
eastus6     buildhelloworld  Linux       Succeeded  Image Update  2018-04-20T00:15:30Z  00:00:43
eastus5     buildhelloworld  Linux       Succeeded  Manual        2018-04-20T00:10:05Z  00:00:45
eastus4     buildhelloworld  Linux       Succeeded  Git Commit    2018-04-19T23:40:38Z  00:00:40
eastus3     buildhelloworld  Linux       Succeeded  Manual        2018-04-19T23:36:37Z  00:00:40
eastus2     buildhelloworld  Linux       Succeeded  Manual        2018-04-19T23:35:27Z  00:00:40
eastus1                                  Succeeded  Manual        2018-04-19T22:51:13Z  00:00:30
```

Se si vuole eseguire il passaggio facoltativo seguente che prevede l'esecuzione del contenitore appena compilato per visualizzare il numero di versione aggiornato, prendere nota del valore di **BUILD ID** della compilazione con trigger "Image Update" (nell'output precedente, "eastus6").

### <a name="optional-run-newly-built-image"></a>Facoltativo: eseguire l'immagine appena compilata

Se si lavora in locale (non in Cloud Shell) ed è installato Docker, al termine della compilazione eseguire la nuova immagine dell'applicazione. Sostituire `<build-id>` con il valore di BUILD ID ottenuto nel passaggio precedente. Se si usa Cloud Shell, ignorare questa sezione. Cloud Shell non supporta `docker run`.

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Passare a http://localhost:8081 nel browser. Nella pagina Web verrà visualizzato il numero di versione di Node.js aggiornato con "a":

![Screenshot del rendering dell'applicazione di esempio nel browser][base-update-02]

È importante notare che si è aggiornata l'immagine di **base** con un nuovo numero di versione, ma questo viene visualizzato nell'ultima immagine compilata dell'**applicazione**. ACR Build ha rilevato la modifica apportata all'immagine di base e ha ricompilato automaticamente l'immagine dell'applicazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create in questa serie di esercitazioni, inclusi il registro contenitori, l'istanza del contenitore, l'insieme di credenziali delle chiavi e l'entità servizio, eseguire questi comandi:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare un'attività di compilazione per attivare automaticamente compilazioni delle immagini dei contenitori in seguito all'aggiornamento dell'immagine di base. Passare ora alle informazioni sull'autenticazione per il registro contenitori.

> [!div class="nextstepaction"]
> [Autenticazione in Registro contenitori di Azure](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png