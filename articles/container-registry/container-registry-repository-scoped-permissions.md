---
title: Autorizzazioni per i repository nel Registro di sistema del contenitore di AzurePermissions to repositories in Azure Container Registry
description: Creare un token con autorizzazioni con ambito a repository specifici in un Registro di sistema per eseguire il pull o il push delle immagini o eseguire altre azioniCreate a token with permissions scoped to specific repositories in a registry to pull or push images, or perform other actions
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 9004c45401833d3070266055dd7eb99a2bb43bde
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618838"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Creare un token con autorizzazioni con ambito repositoryCreate a token with repository-scoped permissions

In questo articolo viene descritto come creare token e mapping di ambito per gestire le autorizzazioni con ambito repository nel Registro di sistema del contenitore. Creando token, il proprietario del Registro di sistema può fornire agli utenti o ai servizi un accesso limitato nel tempo ai repository per eseguire il pull o il push delle immagini o eseguire altre azioni. Un token fornisce autorizzazioni più dettagliate rispetto ad altre opzioni di [autenticazione](container-registry-authentication.md)del Registro di sistema, che ambitono le autorizzazioni per un intero Registro di sistema. 

Gli scenari per la creazione di un token includono:Scenarios for creating a token include:

* Consentire ai dispositivi IoT con singoli token di estrarre un'immagine da un repositoryAllow IoT devices with individual tokens to pull an image from a repository
* Fornire a un'organizzazione esterna le autorizzazioni per un repository specifico 
* Limitare l'accesso al repository a diversi gruppi di utenti dell'organizzazione. Ad esempio, fornire l'accesso in lettura e scrittura agli sviluppatori che creano immagini destinate a repository specifici e l'accesso in lettura ai team che distribuiscono da tali repository.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

* Questa funzionalità è disponibile solo in un registro dei contenitori **Premium.This** feature is only available in a Premium container registry. Per informazioni sui limiti e i livelli del servizio Registro di sistema, vedere SKU del [Registro di sistema del contenitore di Azure.For](container-registry-skus.md)information about registry service tiers and limits, see Azure Container Registry SKUs .
* Al momento non è possibile assegnare autorizzazioni con ambito repository a un'identità di Azure Active Directory, ad esempio un'entità servizio o un'identità gestita.
* Non è possibile creare una mappa dell'ambito in un Registro di sistema abilitato per [l'accesso pull anonimo](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Concetti

Per configurare le autorizzazioni con ambito repository, creare un *token* con una *mappa dell'ambito*associata. 

* Un **token** con una password generata consente all'utente di eseguire l'autenticazione con il Registro di sistema. È possibile impostare una data di scadenza per una password di token o disabilitare un token in qualsiasi momento.  

  Dopo l'autenticazione con un token, l'utente o il servizio può eseguire una o più *azioni* nell'ambito di uno o più repository.

  |Azione  |Descrizione  | Esempio |
  |---------|---------|--------|
  |`content/delete`    | Rimuovere i dati dal repository  | Eliminare un repository o un manifestoDelete a repository or a manifest |
  |`content/read`     |  Leggere i dati dal repository |  Tirare un artefatto |
  |`content/write`     |  Scrivere i dati nel repository     | Usare `content/read` con per eseguire il push di un elementoUse with to push an artifact |
  |`metadata/read`    | Leggere i metadati dal repositoryRead metadata from the repository   | Tag o manifesti di elenco |
  |`metadata/write`     |  Scrivere metadati nel repositoryWrite metadata to the repository  | Abilitare o disabilitare le operazioni di lettura, scrittura o eliminazione |

* Una **mappa dell'ambito** raggruppa le autorizzazioni del repository applicate a un token e può essere riapplicata ad altri token. Ogni token è associato a una singola mappa di ambito. 

   Con una mappa di ambito:

    * Configurare più token con autorizzazioni identiche a un set di repositoryConfigure multiple tokens with identical permissions to a set of repositories
    * Aggiornare le autorizzazioni del token quando si aggiungono o rimuovono azioni del repository nella mappa dell'ambito o si applica una mappa dell'ambito diversa 

  Azure Container Registry offre anche diverse mappe di ambito definite dal sistema che è possibile applicare, con autorizzazioni fisse in tutti i repository.

L'immagine seguente mostra la relazione tra token e mappe di ambito. 

![Token del Registro di sistema e mapping di ambitoRegistry tokens and scope maps](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di **Azure:** i comandi dell'interfaccia della riga di comando di Azure per creare e gestire i token sono disponibili nell'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If](/cli/azure/install-azure-cli)you need to install or upgrade, see Install Azure CLI.
* **Docker** - Per eseguire l'autenticazione con il Registro di sistema per eseguire il pull o push delle immagini, è necessaria un'installazione Docker locale. Docker offre istruzioni di installazione per sistemi [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registro contenitore:** se non ne hai uno, crea un registro contenitori Premium nella sottoscrizione di Azure o aggiorna un Registro di sistema esistente. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Crea token - CLICreate token - CLI

### <a name="create-token-and-specify-repositories"></a>Creare token e specificare i repositoryCreate token and specify repositories

Creare un token usando il comando [az acr token create.][az-acr-token-create] Quando si crea un token, è possibile specificare uno o più repository e le azioni associate in ogni repository. Non è necessario che i repository siano ancora nel Registro di sistema. Per creare un token specificando una mappa dell'ambito esistente, vedere la sezione successiva.

Nell'esempio seguente viene creato un token nel Registro di `content/write` `content/read`sistema *Myregistry* con le seguenti autorizzazioni per il `samples/hello-world` repository: e . Per impostazione predefinita, il comando `enabled`imposta lo stato predefinito `disabled` del token su , ma è possibile aggiornare lo stato in qualsiasi momento.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

L'output mostra i dettagli sul token, incluse due password generate. Si consiglia di salvare le password in un luogo sicuro da utilizzare in un secondo momento per l'autenticazione. Le password non possono essere recuperate di nuovo, ma ne possono essere generate di nuove.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

L'output include dettagli sulla mappa dell'ambito creata dal comando. È possibile utilizzare la mappa `MyToken-scope-map`dell'ambito, qui denominata , per applicare le stesse azioni del repository ad altri token. In alternativa, aggiornare la mappa dell'ambito in un secondo momento per modificare le autorizzazioni dei token associati.

### <a name="create-token-and-specify-scope-map"></a>Creare token e specificare la mappa dell'ambitoCreate token and specify scope map

Un modo alternativo per creare un token consiste nel specificare una mappa dell'ambito esistente. Se non si dispone già di una mappa dell'ambito, crearne una specificando i repository e le azioni associate. Specificare quindi la mappa dell'ambito durante la creazione di un token. 

Per creare una mappa dell'ambito, usare il comando [az acr scope-map create.][az-acr-scope-map-create] Il comando seguente crea una mappa dell'ambito con le stesse autorizzazioni nel `samples/hello-world` repository utilizzato in precedenza. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Eseguire [az acr token create][az-acr-token-create] per creare un token, specificando la mappa dell'ambito *MyScopeMap.Run az* acr token create to create a token, specifying the MyScopeMap scope map. Come nell'esempio precedente, il comando imposta `enabled`lo stato predefinito del token su .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

L'output mostra i dettagli sul token, incluse due password generate. Si consiglia di salvare le password in un luogo sicuro da utilizzare in un secondo momento per l'autenticazione. Le password non possono essere recuperate di nuovo, ma ne possono essere generate di nuove.

## <a name="create-token---portal"></a>Crea token - portale

È possibile usare il portale di Azure per creare token e mappe di ambito. Come con `az acr token create` il comando CLI, è possibile applicare una mappa di ambito esistente o creare una mappa di ambito quando si crea un token specificando uno o più repository e azioni associate. Non è necessario che i repository siano ancora nel Registro di sistema. 

Nell'esempio seguente viene creato un token e viene `samples/hello-world` creata `content/write` una `content/read`mappa dell'ambito con le autorizzazioni seguenti per il repository: e .

1. Nel portale passare al Registro di sistema del contenitore.
1. In **Servizi**, selezionare **Token (Anteprima) > Aggiungi**.
  ![Creare token nel portaleCreate token in portal](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Immettere un nome token.
1. In **Mappa ambito**selezionare Crea **nuovo**.
1. Configurare la mappa dell'ambito:
    1. Immettere un nome e una descrizione per la mappa dell'ambito. 
    1. In **Repository**, immettere `samples/hello-world`, e in **Autorizzazioni**, selezionare `content/read` e `content/write`. Quindi selezionare **Aggiungi**.  
    ![Creare una mappa dell'ambito nel portaleCreate scope map in portal](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Dopo aver aggiunto repository e autorizzazioni, selezionare **Aggiungi** per aggiungere la mappa dell'ambito.
1. Accettare lo **stato** predefinito del token **Abilitato,** quindi selezionare **Crea**.

Dopo che il token è stato convalidato e creato, i dettagli del token vengono visualizzati nella schermata **Token.After** the token is validated and created, token details appear in the Tokens screen.

### <a name="add-token-password"></a>Aggiungi password token

Generare una password dopo aver creato un token. Per eseguire l'autenticazione con il Registro di sistema, il token deve essere abilitato e disporre di una password valida.

È possibile generare una o due password e impostare una data di scadenza per ognuna di esse. 

1. Nel portale passare al Registro di sistema del contenitore.
1. In **Servizi**selezionare **Token (anteprima)** e selezionare un token.
1. Nei dettagli del token, selezionare **password1** o **password2**e selezionare l'icona Genera.
1. Nella schermata della password impostare facoltativamente una data di scadenza per la password e selezionare **Genera**.
1. Dopo aver generato una password, copiarla e salvarla in un luogo sicuro. Non è possibile recuperare una password generata dopo aver chiuso la schermata, ma è possibile generarne una nuova.

    ![Creare una password token nel portaleCreate token password in portal](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Eseguire l'autenticazione con tokenAuthenticate with token

Quando un utente o un servizio utilizza un token per l'autenticazione con il Registro di sistema di destinazione, fornisce il nome del token come nome utente e una delle password generate. Il metodo di autenticazione dipende dall'azione o dalle azioni configurate associate al token.

|Azione  |Come eseguire l'autenticazione  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`nell'interfaccia della riga di comando di Azure |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`nell'interfaccia della riga di comando di Azure  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`nell'interfaccia della riga di comando di Azure     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`nell'interfaccia della riga di comando di Azure   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`nell'interfaccia della riga di comando di Azure |

## <a name="examples-use-token"></a>Esempi: usare il tokenExamples: Use token

Gli esempi seguenti usano il token creato in precedenza in questo articolo per eseguire operazioni comuni su un repository: push e pull di immagini, eliminare immagini ed elencare i tag del repository. Il token è stato impostato inizialmente `content/read` con autorizzazioni `samples/hello-world` push (e`content/write` azioni) nel repository.

### <a name="pull-and-tag-test-images"></a>Immagini di test pull e tag

Per gli esempi seguenti, estrarre le `hello-world` immagini e `alpine` da Docker Hub e contrassegnarle per il Registro di sistema e il repository.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Eseguire l'autenticazione tramite tokenAuthenticate using token

Eseguire `docker login` per eseguire l'autenticazione con il Registro di sistema, fornire il nome del token come nome utente e fornire una delle relative password. Lo stato del `Enabled` token deve essere.

L'esempio seguente viene formattato per la shell bash e fornisce i valori utilizzando le variabili di ambiente.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

L'output dovrebbe mostrare la corretta autenticazione:Output should show successful authentication:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Eseguire il push delle immagini nel registro

Dopo l'accesso riuscito, tentare di eseguire il push delle immagini con tag nel Registro di sistema. Poiché il token dispone delle `samples/hello-world` autorizzazioni per eseguire il push delle immagini nel repository, il push seguente ha esito positivo:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Il token non dispone delle `samples/alpine` autorizzazioni per il repository, pertanto il `requested access to the resource is denied`seguente tentativo push ha esito negativo con un errore simile a:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Modificare le autorizzazioni push/pullChange push/pull permissions

Per aggiornare le autorizzazioni di un token, aggiornare le autorizzazioni nella mappa dell'ambito associato. La mappa dell'ambito aggiornata viene applicata immediatamente a tutti i token associati. 

Ad esempio, `MyToken-scope-map` `content/write` aggiornare `content/read` con `samples/alpine` e azioni `content/write` nel repository `samples/hello-world` e rimuovere l'azione nel repository.  

Per usare l'interfaccia della riga di comando di Azure, eseguire az acr scope-map update per aggiornare la mappa dell'ambito:To use the Azure CLI, run [az acr scope-map update][az-acr-scope-map-update] to update the scope map:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Nel portale di Azure:

1. Passare al registro contenitori.
1. In **Servizi**selezionare **Mapping ambito (anteprima)** e selezionare la mappa dell'ambito da aggiornare.
1. In **Repository**, immettere `samples/alpine`, e in **Autorizzazioni**, selezionare `content/read` e `content/write`. Quindi selezionare **Aggiungi**.
1. In **Repository**selezionare `samples/hello-world` e in **Autorizzazioni** `content/write`deselezionare . Quindi selezionare **Salva**.

Dopo aver aggiornato la mappa dell'ambito, il push seguente ha esito positivo:After updating the scope map, the following push succeeds:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Poiché la mappa `content/read` dell'ambito `samples/hello-world` dispone solo dell'autorizzazione per il repository, un tentativo push al `samples/hello-world` repository ora non riesce:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Il pull delle immagini da entrambi i `content/read` repository ha esito positivo, perché la mappa dell'ambito fornisce autorizzazioni su entrambi i repository:Pull images from both repos succeeds, because the scope map provides permissions on both repositories:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Eliminare le immagini

Aggiornare la mappa `content/delete` dell'ambito `alpine` aggiungendo l'azione al repository. Questa azione consente l'eliminazione di immagini nel repository o l'eliminazione dell'intero repository.

Per brevità, viene visualizzato solo il comando [az acr scope-map update][az-acr-scope-map-update] per aggiornare la mappa dell'ambito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Per aggiornare la mappa dell'ambito tramite il portale, vedere la sezione precedente.

Utilizzare il seguente comando [az acr repository delete][az-acr-repository-delete] per eliminare il `samples/alpine` repository. Per eliminare immagini o repository, il token non `docker login`esegue l'autenticazione tramite . Passare invece il nome e la password del token al comando. Nell'esempio seguente vengono utilizzate le variabili di ambiente create in precedenza nell'articolo:The following example uses the environment variables created earlier in the article:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Mostra tag repository 

Aggiornare la mappa `metadata/read` dell'ambito `hello-world` aggiungendo l'azione al repository. Questa azione consente di leggere i dati del manifesto e dei tag nel repository.

Per brevità, viene visualizzato solo il comando [az acr scope-map update][az-acr-scope-map-update] per aggiornare la mappa dell'ambito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Per aggiornare la mappa dell'ambito tramite il portale, vedere la sezione precedente.

Per leggere i `samples/hello-world` metadati nel repository, eseguire il comando [az acr repository show-manifests][az-acr-repository-show-manifests] o [az acr repository show-tags.][az-acr-repository-show-tags] 

Per leggere i metadati, il `docker login`token non esegue l'autenticazione tramite . Passare invece il nome e la password del token a uno dei comandi. Nell'esempio seguente vengono utilizzate le variabili di ambiente create in precedenza nell'articolo:The following example uses the environment variables created earlier in the article:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Output di esempio:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Gestire i token e le mappe dell'ambito

### <a name="list-scope-maps"></a>Elencare le mappe dell'ambito

Utilizzare il comando [az acr scope-map list][az-acr-scope-map-list] o la schermata **Mapping ambiti (anteprima)** nel portale per elencare tutte le mappe di ambito configurate in un Registro di sistema. Ad esempio:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

L'output mostra le mappe di ambito definite e diverse mappe di ambito definite dal sistema che è possibile utilizzare per configurare i token:The output shows the scope maps you defined and several system-defined scope maps you can use to configure tokens:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Mostra dettagli token

Per visualizzare i dettagli di un token, ad esempio le date di scadenza dello stato e della password, eseguire il comando [az acr token show][az-acr-token-show] oppure selezionare il token nella schermata Token **(anteprima)** nel portale. Ad esempio:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Utilizzare il comando [az acr token list][az-acr-token-list] o la schermata **Tokens (Preview)** nel portale per elencare tutti i token configurati in un Registro di sistema. Ad esempio:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Genera password per il token

Se non si dispone di una password token o si desidera generare nuove password, eseguire il comando [az acr token credential generate.][az-acr-token-credential-generate] 

Nell'esempio seguente viene generato un nuovo valore per password1 per il token *MyToken,* con un periodo di scadenza di 30 giorni. Memorizza la password nella `TOKEN_PWD`variabile di ambiente . Questo esempio è formattato per la shell bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Per usare il portale di Azure per generare una password token, vedere la procedura descritta in [Creare token - portale](#create-token---portal) più indietro in questo articolo.

### <a name="update-token-with-new-scope-map"></a>Aggiornare il token con la nuova mappa dell'ambitoUpdate token with new scope map

Se si desidera aggiornare un token con una mappa dell'ambito diversa, eseguire [az acr token update][az-acr-token-update] e specificare la nuova mappa dell'ambito. Ad esempio:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Nel portale, nella schermata **Token (anteprima),** selezionare il token e in **Mappa ambito**selezionare una mappa di ambito diversa.

> [!TIP]
> Dopo aver aggiornato un token con una nuova mappa di ambito, è possibile generare nuove password per i token. Usare il comando [az acr token credential generate][az-acr-token-credential-generate] o rigenerare una password token nel portale di Azure.Use the az acr token credential generate command or regenerate a token password in the Azure portal.

## <a name="disable-or-delete-token"></a>Disabilitare o eliminare il token

Potrebbe essere necessario disabilitare temporaneamente l'utilizzo delle credenziali del token per un utente o un servizio. 

Usando l'interfaccia della riga di comando di Azure, eseguire il comando [az acr token update][az-acr-token-update] per impostare l'opzione `status` su: `disabled`

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Nel portale selezionare il token nella schermata **Token (anteprima)** e selezionare **Disabilitato** in **Stato**.

Per eliminare un token per invalidare in modo permanente l'accesso da parte di chiunque utilizzi le proprie credenziali, eseguire il comando [az acr token delete.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Nel portale selezionare il token nella schermata **Token (anteprima)** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* Per gestire mappe e token di ambito, usare comandi aggiuntivi nei gruppi di comandi [az acr scope-map][az-acr-scope-map] e [az acr token.][az-acr-token]
* Vedere la [panoramica dell'autenticazione](container-registry-authentication.md) per altre opzioni per l'autenticazione con un registro contenitori di Azure, incluso l'uso di un'identità di Azure Active Directory, un'entità servizio o un account amministratore.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
