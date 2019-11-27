---
title: Elemento OCI push e pull
description: Eseguire il push e il pull degli artefatti Open Container Initiative (OCI) usando un registro contenitori privato in Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.openlocfilehash: cb58a7ed51ae15d33ffdbb616c9b32ef03bcbfb7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456249"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Eseguire il push e il pull di un artefatto OCI usando un registro contenitori di Azure

È possibile usare un registro contenitori di Azure per archiviare e gestire gli [artefatti Open Container Initiative (OCI)](container-registry-image-formats.md#oci-artifacts) , nonché le immagini contenitore compatibili con Docker e docker.

Per illustrare questa funzionalità, questo articolo illustra come usare lo strumento [OCI Registry As Storage (Oras)](https://github.com/deislabs/oras) per eseguire il push di un elemento di esempio, un file di testo, in un registro contenitori di Azure. Estrarre quindi l'artefatto dal registro di sistema. È possibile gestire un'ampia gamma di artefatti OCI in un registro contenitori di Azure usando diversi strumenti della riga di comando appropriati per ogni artefatto.

## <a name="prerequisites"></a>prerequisiti

* **Registro Azure Container**: creare un registro di contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
* **Strumento Oras** : scaricare e installare una versione di Oras corrente per il sistema operativo dal [repository GitHub](https://github.com/deislabs/oras/releases). Lo strumento viene rilasciato come archivio compresso (file di`.tar.gz`). Estrarre e installare il file utilizzando le procedure standard per il sistema operativo in uso.
* **Azure Active Directory entità servizio (facoltativo)** : per eseguire l'autenticazione direttamente con Oras, creare un' [entità servizio](container-registry-auth-service-principal.md) per accedere al registro di sistema. Assicurarsi che all'entità servizio venga assegnato un ruolo, ad esempio AcrPush, in modo che disponga delle autorizzazioni per eseguire il push e il pull degli artefatti.
* INTERFACCIA della riga di comando di **Azure (facoltativo)** : per usare una singola identità, è necessaria un'installazione locale dell'interfaccia della riga di comando di Azure. È consigliabile usare la versione 2.0.71 o successiva. Eseguire `az --version `per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* **Docker (facoltativo)** : per usare una singola identità, è necessario che Docker sia installato localmente, per l'autenticazione con il registro di sistema. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [macOS][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Accedere a un registro

Questa sezione illustra due flussi di lavoro consigliati per accedere al registro di sistema, a seconda dell'identità usata. Scegliere il metodo appropriato per l'ambiente in uso.

### <a name="sign-in-with-oras"></a>Accedi con ORAS

Usando un' [entità servizio](container-registry-auth-service-principal.md) con diritti push, eseguire il comando `oras login` per accedere al registro usando l'ID applicazione e la password dell'entità servizio. Specificare il nome completo del registro di sistema (tutto in lettere minuscole), in questo caso *MyRegistry.azurecr.io*. L'ID applicazione dell'entità servizio viene passato nella variabile di ambiente `$SP_APP_ID`e la password nella variabile `$SP_PASSWD`.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Per leggere la password da stdin, usare `--password-stdin`.

### <a name="sign-in-with-azure-cli"></a>Accedere tramite l'interfaccia della riga di comando di Azure

[Accedere](/cli/azure/authenticate-azure-cli) all'interfaccia della riga di comando di Azure con l'identità per eseguire il push e il pull degli artefatti dal registro contenitori.

Usare quindi il comando dell'interfaccia della riga di comando di Azure [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) per accedere al registro di sistema. Ad esempio, per eseguire l'autenticazione a un registro *denominato Registro di sistema:*

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` usa il client Docker per impostare un token di Azure Active Directory nel file `docker.config`. Per completare il singolo flusso di autenticazione, è necessario che il client Docker sia installato e in esecuzione.

## <a name="push-an-artifact"></a>Eseguire il push di un artefatto

Creare un file di testo in una directory di lavoro locale con un testo di esempio. Ad esempio, in una shell bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

Usare il comando `oras push` per eseguire il push di questo file di testo nel registro. L'esempio seguente inserisce il file di testo di esempio nel repository `samples/artifact`. Il registro di sistema viene identificato con il nome completo del registro di sistema *MyRegistry.azurecr.io* (tutto in lettere minuscole). L'artefatto è contrassegnato `1.0`. Per impostazione predefinita, l'elemento ha un tipo non definito identificato dalla stringa del *tipo di supporto* che segue il nome del file `artifact.txt`. Vedere gli [artefatti OCI](https://github.com/opencontainers/artifacts) per altri tipi. 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

L'output per un push riuscito è simile al seguente:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Per gestire gli artefatti nel registro di sistema, se si usa l'interfaccia della riga di comando di Azure, eseguire i comandi di `az acr` standard per gestire le immagini. Ad esempio, ottenere gli attributi dell'artefatto usando il comando [AZ ACR repository Show][az-acr-repository-show] :

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

## <a name="pull-an-artifact"></a>Effettuare il pull di un artefatto

Eseguire il comando `oras pull` per estrarre l'artefatto dal registro di sistema.

Rimuovere prima di tutto il file di testo dalla directory di lavoro locale:

```bash
rm artifact.txt
```

Eseguire `oras pull` per eseguire il pull dell'elemento e specificare il tipo di supporto usato per eseguire il push dell'elemento:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verificare che il pull sia stato eseguito correttamente:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Rimuovere l'artefatto (facoltativo)

Per rimuovere l'artefatto dal registro contenitori di Azure, usare il comando [AZ ACR repository Delete][az-acr-repository-delete] . Nell'esempio seguente viene rimosso l'artefatto archiviato:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [libreria Oras](https://github.com/deislabs/oras/tree/master/docs), inclusa la configurazione di un manifesto per un artefatto
* Visitare il repository di [artefatti OCI](https://github.com/opencontainers/artifacts) per informazioni di riferimento sui nuovi tipi di artefatti



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
