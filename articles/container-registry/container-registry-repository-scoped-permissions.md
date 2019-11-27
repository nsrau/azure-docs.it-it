---
title: Autorizzazioni per i repository
description: Creare un token con autorizzazioni con ambito per repository specifici in un registro per eseguire il pull o il push di immagini
ms.topic: article
ms.date: 10/31/2019
ms.openlocfilehash: cf36a49ffd6c04897e6f44b844f0c813d0992b18
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454918"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Autorizzazioni con ambito repository in Azure Container Registry 

Azure Container Registry supporta diverse [Opzioni di autenticazione](container-registry-authentication.md) che usano identità con [accesso basato sui ruoli](container-registry-roles.md) a un intero registro. Tuttavia, per determinati scenari, potrebbe essere necessario fornire l'accesso solo a *repository* specifici in un registro di sistema. 

Questo articolo illustra come creare e usare un token di accesso che dispone delle autorizzazioni per eseguire azioni solo su repository specifici in un registro di sistema. Con un token di accesso, è possibile fornire agli utenti o ai servizi un accesso limitato al tempo ai repository per eseguire il pull o il push di immagini o eseguire altre azioni. 

Vedere [informazioni sulle autorizzazioni con ambito repository](#about-repository-scoped-permissions), più avanti in questo articolo, per informazioni di base sui concetti e gli scenari relativi ai token.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

* Questa funzionalità è disponibile solo in un registro contenitori **Premium** . Per informazioni sui limiti e i livelli di servizio del registro di sistema, vedere [sku container Registry di Azure](container-registry-skus.md).
* Attualmente non è possibile assegnare autorizzazioni con ambito repository a un oggetto Azure Active Directory, ad esempio un'entità servizio o un'identità gestita.

## <a name="prerequisites"></a>prerequisiti

* **Interfaccia** della riga di comando di Azure: questo articolo richiede un'installazione locale dell'interfaccia della riga di comando di Azure (versione 2.0.76 o successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
* **Docker** : per eseguire l'autenticazione con il registro di sistema, è necessaria anche un'installazione locale di Docker. Docker offre istruzioni di installazione per sistemi [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registro contenitori con repository** : se non è presente, creare un registro contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md). 

  A scopo di test, eseguire il [push](container-registry-get-started-docker-cli.md) o l' [importazione](container-registry-import-images.md) di una o più immagini di esempio nel registro di sistema. Gli esempi in questo articolo si riferiscono alle immagini seguenti in due repository: `samples/hello-world:v1` e `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Creare un token di accesso

Creare un token usando il comando [AZ ACR token create][az-acr-token-create] . Quando si crea un token, specificare uno o più repository e azioni associate in ogni repository oppure specificare un mapping dell'ambito esistente con tali impostazioni.

### <a name="create-access-token-and-specify-repositories"></a>Creare il token di accesso e specificare i repository

Nell'esempio seguente viene creato un token di accesso con le autorizzazioni per eseguire `content/write` e `content/read` azioni sul repository di `samples/hello-world` e l'azione `content/read` nel repository di `samples/nginx`. Per impostazione predefinita, il comando genera due password. 

Questo esempio imposta lo stato del token su `enabled` (impostazione predefinita), ma è possibile aggiornare il token in qualsiasi momento e impostare lo stato su `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

L'output Mostra i dettagli sul token, incluse le password generate e la mappa dell'ambito. È consigliabile salvare le password in un luogo sicuro da usare in un secondo momento con `docker login`. Le password non possono essere recuperate nuovamente, ma è possibile generarne di nuove.

L'output Mostra anche che viene creata automaticamente una mappa ambito, denominata `MyToken-scope-map`. È possibile usare la mappa ambito per applicare le stesse azioni del repository ad altri token. In alternativa, aggiornare la mappa dell'ambito in un secondo momento per modificare le autorizzazioni del token.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
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

### <a name="create-a-scope-map-and-associated-token"></a>Creare una mappa ambito e un token associato

In alternativa, specificare una mappa dell'ambito con i repository e le azioni associate durante la creazione di un token. Per creare una mappa dell'ambito, usare il comando [AZ ACR scope-map create][az-acr-scope-map-create] .

Il comando di esempio seguente crea un mapping dell'ambito con le stesse autorizzazioni usate nell'esempio precedente. Consente di `content/write` e `content/read` azioni sul repository di `samples/hello-world` e l'azione `content/read` nel repository `samples/nginx`:

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

L'output è simile al seguente:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Eseguire [AZ ACR token create][az-acr-token-create] per creare un token associato alla mappa dell'ambito *MyScopeMap* . Per impostazione predefinita, il comando genera due password. Questo esempio imposta lo stato del token su `enabled` (impostazione predefinita), ma è possibile aggiornare il token in qualsiasi momento e impostare lo stato su `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

L'output Mostra i dettagli sul token, incluse le password generate e la mappa dell'ambito applicata. È consigliabile salvare le password in un luogo sicuro da usare in un secondo momento con `docker login`. Le password non possono essere recuperate nuovamente, ma è possibile generarne di nuove.

## <a name="generate-passwords-for-token"></a>Genera password per il token

Se le password sono state create al momento della creazione del token, procedere [con l'autenticazione con il registro di sistema](#authenticate-using-token).

Se non si ha una password del token o si vuole generare nuove password, eseguire il comando [AZ ACR token Credential generate][az-acr-token-credential-generate] .

L'esempio seguente genera una nuova password per il token creato con un periodo di scadenza di 30 giorni. Archivia la password nella variabile di ambiente TOKEN_PWD. Questo esempio è formattato per la shell bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Eseguire l'autenticazione tramite token

Eseguire `docker login` per l'autenticazione con il registro di sistema usando le credenziali del token. Immettere il nome del token come nome utente e specificare una delle password. L'esempio seguente è formattato per la shell bash e fornisce i valori usando le variabili di ambiente.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

L'output dovrebbe visualizzare l'autenticazione riuscita:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Verificare l'accesso con ambito

È possibile verificare che il token fornisca autorizzazioni con ambito per i repository nel registro di sistema. In questo esempio i comandi di `docker pull` seguenti vengono completati correttamente per eseguire il pull delle immagini disponibili nei repository `samples/hello-world` e `samples/nginx`:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Poiché il token di esempio consente l'azione `content/write` solo nel repository `samples/hello-world`, `docker push` riesce a tale repository, ma non riesce per `samples/nginx`:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Aggiornare la mappa e il token dell'ambito

Per aggiornare le autorizzazioni del token, aggiornare le autorizzazioni nella mappa dell'ambito associata usando [AZ ACR scope-Map Update][az-acr-scope-map-update]. Ad esempio, per aggiornare *MyScopeMap* per rimuovere l'azione `content/write` nel repository `samples/hello-world`:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Se la mappa dell'ambito è associata a più di un token, il comando Aggiorna l'autorizzazione di tutti i token associati.

Se si vuole aggiornare un token con una mappa di ambito diversa, eseguire [AZ ACR token Update][az-acr-token-update]. Ad esempio:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Dopo l'aggiornamento di un token o di una mappa dell'ambito associata a un token, le modifiche alle autorizzazioni diventano effettive al successivo `docker login` o ad altra autenticazione tramite il token.

Dopo l'aggiornamento di un token, è possibile che si desideri generare nuove password per accedere al registro di sistema. Eseguire [AZ ACR token Credential generate][az-acr-token-credential-generate]. Ad esempio:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>Informazioni sulle autorizzazioni con ambito repository

### <a name="concepts"></a>Concetti

Per configurare le autorizzazioni con ambito repository, è possibile creare un *token di accesso* e una *mappa ambito* associata usando i comandi nell'interfaccia della riga di comando di Azure.

* Un **token di accesso** è una credenziale utilizzata con una password per l'autenticazione con il registro di sistema. Gli elementi associati a ogni token sono le *azioni* consentite con ambito per uno o più repository. È possibile impostare una data di scadenza per ogni token. 

* Le **azioni** in ogni repository specificato includono uno o più dei seguenti elementi.

  |Azione  |DESCRIZIONE  |
  |---------|---------|
  |`content/read`     |  Leggere i dati dal repository. Ad esempio, effettuare il pull di un elemento.  |
  |`metadata/read`    | Leggere i metadati dal repository. Ad esempio, elencare i tag o visualizzare i metadati del manifesto.   |
  |`content/write`     |  Scrivere i dati nel repository. Usare con `content/read` per eseguire il push di un elemento.    |
  |`metadata/write`     |  Scrivere i metadati nel repository. Ad esempio, aggiornare gli attributi del manifesto.  |
  |`content/delete`    | Rimuovere i dati dal repository. Ad esempio, eliminare un repository o un manifesto. |

* Una **mappa dell'ambito** è un oggetto del registro di sistema che raggruppa le autorizzazioni del repository applicate a un token oppure può essere riapplicato ad altri token. Se non si applica una mappa ambito durante la creazione di un token, viene creata automaticamente una mappa ambito per salvare le impostazioni di autorizzazione. 

  Una mappa ambito consente di configurare più utenti con accesso identico a un set di repository. Azure Container Registry fornisce anche mappe dell'ambito definite dal sistema che è possibile applicare quando si creano i token di accesso.

Nell'immagine seguente viene riepilogata la relazione tra token e mappe dell'ambito. 

![Mapping dell'ambito del registro di sistema e token](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Scenari

Gli scenari per l'uso di un token di accesso includono:

* Fornire ai dispositivi di tutti i token singoli per eseguire il pull di un'immagine da un repository
* Fornire un'organizzazione esterna con le autorizzazioni per un repository specifico 
* Limitare l'accesso al repository a gruppi di utenti specifici nell'organizzazione. Ad esempio, fornire l'accesso in lettura e scrittura agli sviluppatori che creano immagini destinate a repository specifici e accesso in lettura ai team distribuiti da tali repository.

### <a name="authentication-using-token"></a>Autenticazione tramite token

Usare un nome di token come nome utente e una delle password associate per l'autenticazione con il registro di destinazione. Il metodo di autenticazione dipende dalle azioni configurate.

### <a name="contentread-or-contentwrite"></a>contenuto/lettura o contenuto/scrittura

Se il token consente solo `content/read` o `content/write` azioni, fornire le credenziali del token in uno dei flussi di autenticazione seguenti:

* Eseguire l'autenticazione con Docker usando `docker login`
* Eseguire l'autenticazione con il registro di sistema usando il comando [AZ ACR login][az-acr-login] nell'interfaccia della riga di comando di Azure

Dopo l'autenticazione, il token consente le azioni configurate nel repository o nei repository con ambito. Se, ad esempio, il token consente l'azione `content/read` su un repository, le operazioni di `docker pull` sono consentite nelle immagini del repository.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>metadati/lettura, metadati/scrittura o contenuto/eliminazione

Se il token consente le azioni `metadata/read`, `metadata/write`o `content/delete` in un repository, le credenziali del token devono essere fornite come parametri con i comandi [AZ ACR repository][az-acr-repository] correlati nell'interfaccia della riga di comando di Azure.

Ad esempio, se `metadata/read` azioni sono consentite in un repository, passare le credenziali del token durante l'esecuzione del comando [AZ ACR repository Show-Tags][az-acr-repository-show-tags] per elencare i tag.

## <a name="next-steps"></a>Passaggi successivi

* Per gestire le mappe di ambito e i token di accesso, usare comandi aggiuntivi nei gruppi di comandi [AZ ACR scope-map][az-acr-scope-map] e [AZ ACR token][az-acr-token] .
* Vedere [Panoramica dell'autenticazione](container-registry-authentication.md) per gli scenari per l'autenticazione con un registro contenitori di Azure con un account amministratore o un'identità Azure Active Directory.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
