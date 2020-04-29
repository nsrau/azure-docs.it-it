---
title: Autorizzazioni per i repository in Azure Container Registry
description: Creare un token con autorizzazioni con ambito per repository specifici in un registro di sistema per eseguire il pull o il push di immagini oppure eseguire altre azioni
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 9004c45401833d3070266055dd7eb99a2bb43bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618838"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Creare un token con autorizzazioni con ambito repository

Questo articolo descrive come creare token e mappe dell'ambito per gestire le autorizzazioni con ambito repository nel registro contenitori. Grazie alla creazione di token, un proprietario del registro di sistema può fornire agli utenti o ai servizi un accesso limitato a un determinato periodo di tempo ai repository per eseguire il pull o il push di immagini o eseguire altre azioni. Un token fornisce autorizzazioni più specifiche rispetto ad altre [Opzioni di autenticazione](container-registry-authentication.md)del registro di sistema, che hanno come ambito le autorizzazioni per un intero registro. 

Gli scenari per la creazione di un token includono:

* Consenti ai dispositivi Internet con i singoli token di effettuare il pull di un'immagine da un repository
* Fornire un'organizzazione esterna con le autorizzazioni per un repository specifico 
* Limitare l'accesso al repository a diversi gruppi di utenti dell'organizzazione. Ad esempio, fornire l'accesso in lettura e scrittura agli sviluppatori che creano immagini destinate a repository specifici e accesso in lettura ai team distribuiti da tali repository.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

* Questa funzionalità è disponibile solo in un registro contenitori **Premium** . Per informazioni sui limiti e i livelli di servizio del registro di sistema, vedere [sku container Registry di Azure](container-registry-skus.md).
* Attualmente non è possibile assegnare autorizzazioni con ambito repository a un'identità di Azure Active Directory, ad esempio un'entità servizio o un'identità gestita.
* Non è possibile creare una mappa ambito in un registro abilitato per [l'accesso pull anonimo](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Concetti

Per configurare le autorizzazioni con ambito repository, è necessario creare un *token* con una *mappa ambito*associata. 

* Un **token** insieme a una password generata consente all'utente di eseguire l'autenticazione con il registro di sistema. È possibile impostare una data di scadenza per una password di token o disabilitare un token in qualsiasi momento.  

  Dopo l'autenticazione con un token, l'utente o il servizio può eseguire una o più *azioni* nell'ambito di uno o più repository.

  |Azione  |Descrizione  | Esempio |
  |---------|---------|--------|
  |`content/delete`    | Rimuovere i dati dal repository  | Eliminare un repository o un manifesto |
  |`content/read`     |  Leggere i dati dal repository |  Effettuare il pull di un artefatto |
  |`content/write`     |  Scrivere i dati nel repository     | Usare con `content/read` per eseguire il push di un artefatto |
  |`metadata/read`    | Leggere i metadati dal repository   | Elencare tag o manifesti |
  |`metadata/write`     |  Scrivere metadati nel repository  | Abilitare o disabilitare le operazioni di lettura, scrittura o eliminazione |

* Una **mappa ambito** raggruppa le autorizzazioni del repository che si applicano a un token e può essere riapplicata ad altri token. Ogni token è associato a una singola mappa dell'ambito. 

   Con una mappa ambito:

    * Configurare più token con autorizzazioni identiche per un set di repository
    * Aggiornare le autorizzazioni del token quando si aggiungono o rimuovono le azioni del repository nella mappa dell'ambito o si applica una mappa di ambito diversa 

  Azure Container Registry fornisce anche diverse mappe di ambito definite dal sistema che è possibile applicare, con autorizzazioni fisse in tutti i repository.

Nell'immagine seguente viene illustrata la relazione tra token e mappe dell'ambito. 

![Token del registro di sistema e mappe dell'ambito](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Prerequisiti

* **Interfaccia** della riga di comando di Azure: i comandi di Azure per creare e gestire i token sono disponibili nell'interfaccia della riga di comando di Azure versione 2.0.76 o successiva Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* **Docker** : per eseguire l'autenticazione con il registro di sistema per eseguire il pull o il push di immagini, è necessaria un'installazione locale di Docker. Docker offre istruzioni di installazione per sistemi [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registro contenitori** : se non è già presente, creare un registro contenitori Premium nella sottoscrizione di Azure o aggiornare un registro di sistema esistente. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Crea token-CLI

### <a name="create-token-and-specify-repositories"></a>Creare il token e specificare i repository

Creare un token usando il comando [AZ ACR token create][az-acr-token-create] . Quando si crea un token, è possibile specificare uno o più repository e le azioni associate in ogni repository. Non è necessario che i repository siano ancora presenti nel registro di sistema. Per creare un token specificando una mappa dell'ambito esistente, vedere la sezione successiva.

Nell'esempio seguente viene creato un token nel *Registro* di sistema Registry con le autorizzazioni seguenti per `samples/hello-world` il repository `content/write` : `content/read`e. Per impostazione predefinita, il comando imposta lo stato del token `enabled`predefinito su, ma è possibile aggiornare lo `disabled` stato in qualsiasi momento.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

L'output Mostra i dettagli sul token, incluse due password generate. È consigliabile salvare le password in un luogo sicuro da usare in un secondo momento per l'autenticazione. Le password non possono essere recuperate nuovamente, ma è possibile generarne di nuove.

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

L'output include i dettagli sull'ambito Mappa del comando creato. È possibile usare la mappa ambito, denominata `MyToken-scope-map`qui, per applicare le stesse azioni del repository ad altri token. In alternativa, aggiornare la mappa dell'ambito in un secondo momento per modificare le autorizzazioni dei token associati.

### <a name="create-token-and-specify-scope-map"></a>Crea token e specifica mappa ambito

Un metodo alternativo per creare un token consiste nel specificare una mappa dell'ambito esistente. Se non si dispone già di una mappa ambito, crearne prima una specificando i repository e le azioni associate. Specificare quindi la mappa dell'ambito durante la creazione di un token. 

Per creare una mappa dell'ambito, usare il comando [AZ ACR scope-map create][az-acr-scope-map-create] . Il comando seguente crea un mapping dell'ambito con le stesse autorizzazioni nel `samples/hello-world` repository usato in precedenza. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Eseguire [AZ ACR token create][az-acr-token-create] per creare un token, specificando la mappa dell'ambito *MyScopeMap* . Come nell'esempio precedente, il comando imposta lo stato del token predefinito su `enabled`.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

L'output Mostra i dettagli sul token, incluse due password generate. È consigliabile salvare le password in un luogo sicuro da usare in un secondo momento per l'autenticazione. Le password non possono essere recuperate nuovamente, ma è possibile generarne di nuove.

## <a name="create-token---portal"></a>Crea token-portale

È possibile utilizzare il portale di Azure per creare token e mappe dell'ambito. Analogamente al `az acr token create` comando CLI, è possibile applicare una mappa dell'ambito esistente o creare una mappa dell'ambito quando si crea un token specificando uno o più repository e le azioni associate. Non è necessario che i repository siano ancora presenti nel registro di sistema. 

Nell'esempio seguente viene creato un token e viene creata una mappa dell'ambito con le autorizzazioni seguenti `samples/hello-world` per il `content/write` repository `content/read`: e.

1. Nel portale passare al registro contenitori.
1. In **Servizi**selezionare **token (anteprima) > + Aggiungi**.
  ![Crea token nel portale](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Immettere un nome di token.
1. In **mapping ambito**selezionare **Crea nuovo**.
1. Configurare la mappa ambito:
    1. Immettere un nome e una descrizione per la mappa ambito. 
    1. In **repository**immettere `samples/hello-world`e in **autorizzazioni**selezionare `content/read` e `content/write`. Quindi selezionare **+ Aggiungi**.  
    ![Creare una mappa dell'ambito nel portale](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Dopo l'aggiunta di repository e autorizzazioni, selezionare **Aggiungi** per aggiungere la mappa dell'ambito.
1. Accettare lo **stato** del token predefinito **abilitato** e quindi selezionare **Crea**.

Dopo che il token è stato convalidato e creato, i dettagli del token vengono visualizzati nella schermata **token** .

### <a name="add-token-password"></a>Aggiungi password token

Generare una password dopo aver creato un token. Per eseguire l'autenticazione con il registro di sistema, il token deve essere abilitato e deve avere una password valida.

È possibile generare una o due password e impostare una data di scadenza per ciascuna di esse. 

1. Nel portale passare al registro contenitori.
1. In **Servizi**selezionare **token (anteprima)** e selezionare un token.
1. Nei dettagli del token selezionare **Password1** o **password2**e selezionare l'icona Genera.
1. Nella schermata password, facoltativamente, impostare una data di scadenza per la password e selezionare **genera**.
1. Dopo aver generato una password, copiarla e salvarla in una posizione sicura. Non è possibile recuperare una password generata dopo aver chiuso la schermata, ma è possibile generarne una nuova.

    ![Creazione della password del token nel portale](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Eseguire l'autenticazione con il token

Quando un utente o un servizio utilizza un token per l'autenticazione con il registro di sistema di destinazione, fornisce il nome del token come nome utente e una delle password generate. Il metodo di autenticazione dipende dall'azione o dalle azioni configurate associate al token.

|Action  |Come eseguire l'autenticazione  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`nell'interfaccia della riga di comando di Azure |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`nell'interfaccia della riga di comando di Azure  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`nell'interfaccia della riga di comando di Azure     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`nell'interfaccia della riga di comando di Azure   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`nell'interfaccia della riga di comando di Azure |

## <a name="examples-use-token"></a>Esempi: usare il token

Gli esempi seguenti usano il token creato in precedenza in questo articolo per eseguire operazioni comuni su un repository: immagini push e pull, Elimina immagini ed elenca i tag del repository. Il token è stato configurato inizialmente con le autorizzazioni push`content/write` ( `content/read` e le azioni) `samples/hello-world` nel repository.

### <a name="pull-and-tag-test-images"></a>Immagini di test pull e tag

Per gli esempi seguenti, estrarre le `hello-world` immagini `alpine` e dall'hub Docker e contrassegnarle per il registro e il repository.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Eseguire l'autenticazione tramite token

Eseguire `docker login` per eseguire l'autenticazione con il registro di sistema, specificare il nome del token come nome utente e specificare una delle password. Il token deve avere lo `Enabled` stato.

L'esempio seguente è formattato per la shell bash e fornisce i valori usando le variabili di ambiente.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

L'output dovrebbe visualizzare l'autenticazione riuscita:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Eseguire il push delle immagini nel registro

Dopo aver eseguito l'accesso, provare a eseguire il push delle immagini con tag nel registro di sistema. Poiché il token ha le autorizzazioni per eseguire il push `samples/hello-world` delle immagini nel repository, il push seguente ha esito positivo:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Il token non ha le autorizzazioni per `samples/alpine` il repository, quindi il tentativo di push seguente ha esito negativo `requested access to the resource is denied`e restituisce un errore simile al seguente:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Modificare le autorizzazioni push/pull

Per aggiornare le autorizzazioni di un token, aggiornare le autorizzazioni nella mappa dell'ambito associata. La mappa di ambito aggiornata viene applicata immediatamente a tutti i token associati. 

Ad esempio, aggiornare `MyToken-scope-map` con `content/write` le `content/read` azioni e nel `samples/alpine` repository e rimuovere l' `content/write` azione nel `samples/hello-world` repository.  

Per usare l'interfaccia della riga di comando di Azure, eseguire [AZ ACR scope-Map Update][az-acr-scope-map-update] per aggiornare la mappa dell'ambito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Nel portale di Azure:

1. Passare al registro contenitori.
1. In **Servizi**selezionare **mappe ambito (anteprima)** e selezionare la mappa ambito da aggiornare.
1. In **repository**immettere `samples/alpine`e in **autorizzazioni**selezionare `content/read` e `content/write`. Quindi selezionare **+ Aggiungi**.
1. In **repository**selezionare e in `samples/hello-world` **autorizzazioni**deselezionare `content/write`. Selezionare quindi **Salva**.

Dopo aver aggiornato la mappa dell'ambito, il push seguente viene eseguito correttamente:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Poiché la mappa dell'ambito ha solo `content/read` l'autorizzazione per `samples/hello-world` il repository, un tentativo push del `samples/hello-world` repository ha ora esito negativo:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Il pull di immagini da entrambi i repository ha esito positivo, `content/read` perché la mappa ambito fornisce le autorizzazioni per entrambi i repository:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Eliminare le immagini

Aggiornare la mappa dell'ambito aggiungendo l' `content/delete` azione al `alpine` repository. Questa azione consente l'eliminazione di immagini nel repository o l'eliminazione dell'intero repository.

Per brevità, viene visualizzato solo il comando [AZ ACR scope-Map Update][az-acr-scope-map-update] per aggiornare la mappa dell'ambito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Per aggiornare la mappa dell'ambito usando il portale, vedere la sezione precedente.

Usare il comando [AZ ACR repository Delete][az-acr-repository-delete] seguente per eliminare il `samples/alpine` repository. Per eliminare immagini o repository, il token non esegue l'autenticazione tramite `docker login`. Al contrario, passare il nome e la password del token al comando. L'esempio seguente usa le variabili di ambiente create in precedenza nell'articolo:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Mostra tag del repository 

Aggiornare la mappa dell'ambito aggiungendo l' `metadata/read` azione al `hello-world` repository. Questa azione consente di leggere i dati del manifesto e dei tag nel repository.

Per brevità, viene visualizzato solo il comando [AZ ACR scope-Map Update][az-acr-scope-map-update] per aggiornare la mappa dell'ambito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Per aggiornare la mappa dell'ambito usando il portale, vedere la sezione precedente.

Per leggere i `samples/hello-world` metadati nel repository, eseguire il comando [AZ ACR repository Show-manifests][az-acr-repository-show-manifests] o [AZ ACR repository Show-Tags][az-acr-repository-show-tags] . 

Per leggere i metadati, il token non esegue l' `docker login`autenticazione tramite. Al contrario, passare il nome e la password del token a uno dei comandi. L'esempio seguente usa le variabili di ambiente create in precedenza nell'articolo:

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

### <a name="list-scope-maps"></a>Elenca mappe ambito

Per elencare tutte le mappe dell'ambito configurate in un registro di sistema, usare il comando [AZ ACR scope-map list][az-acr-scope-map-list] o la schermata **scope Maps (Preview)** nel portale. Ad esempio:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

L'output Mostra le mappe dell'ambito definite e diverse mappe dell'ambito definite dal sistema che è possibile usare per configurare i token:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Mostra dettagli token

Per visualizzare i dettagli di un token, ad esempio il relativo stato e le date di scadenza della password, eseguire il comando [AZ ACR token Show][az-acr-token-show] oppure selezionare il token nella schermata **Tokens (anteprima)** nel portale. Ad esempio:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Usare il comando [AZ ACR token list][az-acr-token-list] o la schermata **Tokens (anteprima)** nel portale per elencare tutti i token configurati in un registro. Ad esempio:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Genera password per il token

Se non si ha una password del token o si vuole generare nuove password, eseguire il comando [AZ ACR token Credential generate][az-acr-token-credential-generate] . 

Nell'esempio seguente viene generato un nuovo valore per Password1 per *il token token,* con un periodo di scadenza di 30 giorni. Archivia la password nella variabile `TOKEN_PWD`di ambiente. Questo esempio è formattato per la shell bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Per usare la portale di Azure per generare una password del token, vedere la procedura descritta in [creare token-portale](#create-token---portal) più indietro in questo articolo.

### <a name="update-token-with-new-scope-map"></a>Aggiorna token con nuova mappa ambito

Se si vuole aggiornare un token con una mappa di ambito diversa, eseguire [AZ ACR token Update][az-acr-token-update] e specificare la nuova mappa dell'ambito. Ad esempio:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Nella schermata **token (anteprima)** del portale selezionare il token e in **mapping ambito**Selezionare un'altra mappa dell'ambito.

> [!TIP]
> Dopo l'aggiornamento di un token con una nuova mappa ambito, potrebbe essere necessario generare nuove password per i token. Usare il comando [AZ ACR token Credential generate][az-acr-token-credential-generate] o rigenerare una password del token nel portale di Azure.

## <a name="disable-or-delete-token"></a>Disabilitare o eliminare il token

Potrebbe essere necessario disabilitare temporaneamente l'uso delle credenziali del token per un utente o un servizio. 

Usando l'interfaccia della riga di comando di Azure, eseguire il comando [AZ ACR token Update][az-acr-token-update] per impostare `status` su `disabled`:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Nel portale selezionare il token nella schermata **Tokens (anteprima)** e selezionare **disabled** in **status**.

Per eliminare un token per invalidare l'accesso in modo permanente da chiunque usi le credenziali, eseguire il comando [AZ ACR token Delete][az-acr-token-delete] . 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Nel portale selezionare il token nella schermata **Tokens (anteprima)** e selezionare **Ignora**.

## <a name="next-steps"></a>Passaggi successivi

* Per gestire le mappe e i token dell'ambito, usare comandi aggiuntivi nei gruppi di comandi [AZ ACR scope-map][az-acr-scope-map] e [AZ ACR token][az-acr-token] .
* Vedere [Panoramica dell'autenticazione](container-registry-authentication.md) per altre opzioni per l'autenticazione con un registro contenitori di Azure, tra cui l'uso di un'identità di Azure Active Directory, un'entità servizio o un account amministratore.


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
