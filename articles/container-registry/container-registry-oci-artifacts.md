---
title: Spingere e tirare artefatto OCI
description: Push and pull Open Container Initiative (OCI) artifacts using a private container registry in Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371053"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Eseguire il push ed eseguire il pull di un elemento OCI usando un registro contenitori di AzurePush and pull an OCI artifact using an Azure container registry

È possibile usare un registro contenitori di Azure per archiviare e gestire [gli elementi OCI (Open Container Initiative)](container-registry-image-formats.md#oci-artifacts) e le immagini dei contenitori compatibili con Docker e Docker.You can use an Azure container registry to store and manage Open Container Initiative (OCI) artifacts as well as Docker and Docker-compatible container images.

Per illustrare questa funzionalità, in questo articolo viene illustrato come usare lo strumento OCI Registry as Storage (ORAS) per eseguire il push di un elemento di esempio, ovvero un file di testo, in un Registro di sistema del contenitore di Azure.To demonstrate this capability, this article shows how to use the [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) tool to push one sample artifact - a text file - to an Azure container registry. Quindi, estrarre l'elemento dal Registro di sistema. È possibile gestire una varietà di elementi OCI in un registro contenitori di Azure usando diversi strumenti della riga di comando appropriati per ogni elemento.

## <a name="prerequisites"></a>Prerequisiti

* **Registro** di sistema del contenitore di Azure: creare un registro contenitori nella sottoscrizione di Azure.Azure container registry - Create a container registry in your Azure subscription. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
* **Strumento ORAS** - Scaricare e installare una versione CORRENTE di ORAS per il sistema operativo dal [repository GitHub](https://github.com/deislabs/oras/releases). Lo strumento viene rilasciato come`.tar.gz` tarball compresso (file). Estrarre e installare il file utilizzando le procedure standard per il sistema operativo in uso.
* **Entità servizio di Azure Active Directory (facoltativo):** per eseguire l'autenticazione direttamente con ORAS, creare [un'entità servizio](container-registry-auth-service-principal.md) per accedere al Registro di sistema. Assicurarsi che all'entità servizio sia assegnato un ruolo, ad esempio AcrPush, in modo che disponga delle autorizzazioni per eseguire il push e il pull degli elementi.
* Interfaccia della riga di comando di **Azure (facoltativo):** per usare un'identità individuale, è necessaria un'installazione locale dell'interfaccia della riga di comando di Azure.Azure CLI (optional) - To use an individual identity, you need a local installation of the Azure CLI. Si consiglia la versione 2.0.71 o successiva. Eseguire `az --version `per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If](/cli/azure/install-azure-cli)you need to install or upgrade, see Install Azure CLI.
* **Docker (facoltativo):** per utilizzare un'identità individuale, è necessario che Docker sia installato localmente, per eseguire l'autenticazione nel Registro di sistema. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [macOS][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Accedere a un Registro di sistema

In questa sezione vengono illustrati due flussi di lavoro suggeriti per accedere al Registro di sistema, a seconda dell'identità utilizzata. Scegliere il metodo appropriato per l'ambiente.

### <a name="sign-in-with-oras"></a>Accedi con ORAS

Usando [un'entità servizio](container-registry-auth-service-principal.md) con `oras login` diritti push, eseguire il comando per accedere al Registro di sistema usando l'ID e la password dell'applicazione dell'entità servizio. Specificare il nome completo del Registro di sistema (tutto minuscolo), in questo caso *myregistry.azurecr.io*. L'ID applicazione dell'entità servizio `$SP_APP_ID`viene passato nella `$SP_PASSWD`variabile di ambiente e la password nella variabile .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Per leggere la password da `--password-stdin`Stdin, utilizzare .

### <a name="sign-in-with-azure-cli"></a>Accedere tramite l'interfaccia della riga di comando di Azure

[Accedere](/cli/azure/authenticate-azure-cli) all'interfaccia della riga di comando di Azure con l'identità per eseguire il push ed eseguire il pull degli elementi dal Registro di sistema del contenitore.

Quindi, usare il comando dell'interfaccia della riga di comando di Azure [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) per accedere al Registro di sistema. Ad esempio, per eseguire l'autenticazione in un Registro di sistema denominato *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`utilizza il client Docker per impostare `docker.config` un token di Azure Active Directory nel file. Il client Docker deve essere installato e in esecuzione per completare il flusso di autenticazione individuale.

## <a name="push-an-artifact"></a>Spingere un elemento

Creare un file di testo in una directory di lavoro locale con del testo di esempio. Ad esempio, in una shell bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

Utilizzare `oras push` il comando per eseguire il push di questo file di testo nel Registro di sistema. Nell'esempio seguente il file di `samples/artifact` testo di esempio viene eseguito nel repository. Il Registro di sistema viene identificato con il nome completo del Registro di sistema *myregistry.azurecr.io* (tutte minuscole). L'elemento è `1.0`contrassegnato. Per impostazione predefinita, l'elemento ha un tipo non definito, identificato dalla stringa del *tipo di supporto* che segue il nome del file `artifact.txt`. Vedere [Arterie OCI](https://github.com/opencontainers/artifacts) per altri tipi. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

L'output per un push riuscito è simile al seguente:Output for a successful push is similar to the following:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Per gestire gli elementi nel Registro di sistema, `az acr` se si usa l'interfaccia della riga di comando di Azure, eseguire comandi standard per la gestione delle immagini. Ad esempio, ottenere gli attributi dell'elemento utilizzando il comando [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

L'output è simile al seguente:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Tirare un artefatto

Eseguire `oras pull` il comando per estrarre l'elemento dal Registro di sistema.

Rimuovere innanzitutto il file di testo dalla directory di lavoro locale:

```bash
rm artifact.txt
```

Eseguire `oras pull` per estrarre l'elemento e specificare il tipo di supporto utilizzato per eseguire il push dell'elemento:Run to pull the artifact, and specify the media type used to push the artifact:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verificare che il pull sia stato eseguito correttamente:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Rimuovere l'elemento (facoltativo)Remove the artifact (optional)

Per rimuovere l'elemento dal Registro di sistema del contenitore di Azure, usare il comando [az acr repository delete.To][az-acr-repository-delete] remove the artifact from your Azure container registry, use the az acr repository delete command. L'esempio seguente rimuove l'elemento archiviato in questa posizione:The following example removes the artifact you stored there:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sulla [libreria ORAS](https://github.com/deislabs/oras/tree/master/docs), incluso come configurare un manifesto per un elemento
* Visita il repository [OCI Artifacts](https://github.com/opencontainers/artifacts) per informazioni di riferimento sui nuovi tipi di artefatto



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
