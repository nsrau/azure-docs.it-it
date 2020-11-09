---
title: Autorizzazioni per i repository in Registro Azure Container
description: Creare un token con autorizzazioni con ambito in repository specifici in un registro di sistema Premium per eseguire il pull o il push di immagini oppure eseguire altre azioni
ms.topic: article
ms.date: 05/27/2020
ms.openlocfilehash: b65b1bf69337cb172a17043490a5d13c7bd7afc2
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381236"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Creare un token con autorizzazioni con ambito repository

Questo articolo descrive come creare token e mappe dell'ambito per gestire le autorizzazioni con ambito repository nel registro contenitori. Con la creazione dei token, il proprietario di un registro può offrire a utenti o servizi un accesso con ambito a tempo limitato ai repository per effettuare il pull o il push di immagini o eseguire altre azioni. Un token offre autorizzazioni più specifiche rispetto ad altre [opzioni di autenticazione](container-registry-authentication.md) del registro, che definiscono come ambito delle autorizzazioni un intero registro. 

Gli scenari per la creazione di un token includono:

* Consentire ai dispositivi IoT con singoli token di effettuare il pull di un'immagine da un repository
* Rendere disponibili per un'organizzazione esterna le autorizzazioni per un repository specifico 
* Limitare l'accesso al repository a diversi gruppi di utenti dell'organizzazione. Ad esempio, si può concedere l'accesso in lettura e scrittura agli sviluppatori che creano immagini destinate a repository specifici e l'accesso in lettura ai team che effettuano distribuzioni da tali repository.

Questa funzionalità è disponibile nel livello di servizio **Premium** del registro contenitori. Per informazioni sui livelli di servizio e sui limiti del registro contenitori, vedere [Livelli di servizio del Registro Azure Container](container-registry-skus.md).

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

* Attualmente non è possibile assegnare autorizzazioni con ambito repository a un'identità di Azure Active Directory, ad esempio un'entità servizio o un'identità gestita.
* Non è possibile creare un mapping di ambito in un registro abilitato per l'[accesso pull anonimo](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Concetti

Per configurare le autorizzazioni con ambito repository, è necessario creare un *token* con un *mapping di ambito* associato. 

* Un **token** insieme a una password generata consente all'utente di eseguire l'autenticazione con il registro. È possibile impostare una data di scadenza per una password del token o disabilitare un token in qualsiasi momento.  

  Dopo l'autenticazione con un token, l'utente o il servizio può eseguire una o più *azioni* nell'ambito di uno o più repository.

  |Azione  |Descrizione  | Esempio |
  |---------|---------|--------|
  |`content/delete`    | Rimuovere i dati dal repository  | Eliminare un repository o un manifesto |
  |`content/read`     |  Leggere i dati dal repository |  Eseguire il pull di un artefatto |
  |`content/write`     |  Scrivere dati nel repository     | Usare con `content/read` per eseguire il push di un artefatto |
  |`metadata/read`    | Leggere i metadati dal repository   | Elencare tag o manifesti |
  |`metadata/write`     |  Scrivere i metadati nel repository  | Abilitare o disabilitare le operazioni di lettura, scrittura o eliminazione |

* Un **mapping di ambito** raggruppa le autorizzazioni del repository che si applicano a un token e possono essere riapplicate ad altri token. Ogni token è associato a un singolo mapping di ambito. 

   Con un mapping di ambito:

    * Configurare più token con autorizzazioni identiche per un set di repository
    * Aggiornare le autorizzazioni del token quando si aggiungono o rimuovono le azioni del repository nel mapping di ambito o si applica un mapping di ambito diverso 

  Azure Container Registry fornisce inoltre diverse mappe dell'ambito definite dal sistema che è possibile applicare quando si creano i token. Le autorizzazioni delle mappe dell'ambito definite dal sistema si applicano a tutti i repository nel registro.

L'immagine seguente illustra la relazione tra token e mapping di ambito. 

![Token del registro e mapping di ambito](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Prerequisiti

* **Interfaccia della riga di comando di Azure** : i comandi dell'interfaccia della riga di comando di Azure per creare e gestire i token sono disponibili nella versione 2.0.76 o successiva dell'interfaccia. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* **Docker** : per eseguire l'autenticazione con il registro per effettuare il pull o il push di immagini, è necessaria un'installazione locale di Docker. Docker offre istruzioni di installazione per sistemi [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registro contenitori** : se non è già presente, creare un registro contenitori Premium nella sottoscrizione di Azure o aggiornare un registro esistente. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Creare il token - interfaccia della riga di comando

### <a name="create-token-and-specify-repositories"></a>Creare il token e specificare i repository

Creare un token usando il comando [az acr token create][az-acr-token-create]. Quando si crea un token, è possibile specificare uno o più repository e le azioni associate in ogni repository. Non è necessario che i repository siano già nel registro. Per creare un token specificando una mappa dell'ambito esistente, vedere la [sezione successiva](#create-token-and-specify-scope-map).

Nell'esempio seguente viene creato un token nel registro *myregistry* con le seguenti autorizzazioni per il repository `samples/hello-world`: `content/write` e `content/read`. Per impostazione predefinita, il comando imposta lo stato del token predefinito su `enabled`, ma è possibile aggiornare lo stato impostandolo su `disabled` in qualsiasi momento.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

L'output Mostra i dettagli sul token. Per impostazione predefinita, vengono generate due password. È consigliabile salvare le password in un luogo sicuro per usarle in un secondo momento per l'autenticazione. Le password non possono essere nuovamente recuperate, ma è possibile generarne di nuove.

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

> [!NOTE]
> Se si desidera rigenerare le password del token e impostare i periodi di scadenza delle password, vedere [rigenerare le password del token](#regenerate-token-passwords) più avanti in questo articolo.

L'output include i dettagli sul mapping di ambito creato dal comando. È possibile usare il mapping di ambito, qui denominato `MyToken-scope-map`, per applicare le stesse azioni del repository ad altri token. Oppure aggiornare il mapping di ambito in seguito per modificare le autorizzazioni dei token associati.

### <a name="create-token-and-specify-scope-map"></a>Creare il token e specificare il mapping di ambito

Un metodo alternativo per creare un token è specificare un mapping di ambito esistente. Se non si ha un mapping di ambito, per prima cosa crearne uno specificando i repository e le azioni associate. Quindi, specificare il mapping di ambito quano si crea un token. 

Per creare un mapping di ambito, usare il comando [az acr scope-map create][az-acr-scope-map-create]. Il comando seguente crea un mapping di ambito con le stesse autorizzazioni per il repository `samples/hello-world` usato in precedenza. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Eseguire [az acr token create][az-acr-token-create] per creare un token, specificando il mapping di ambito *MyScopeMap*. Come nell'esempio precedente, il comando imposta lo stato predefinito del token su `enabled`.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

L'output Mostra i dettagli sul token. Per impostazione predefinita, vengono generate due password. È consigliabile salvare le password in un luogo sicuro per usarle in un secondo momento per l'autenticazione. Le password non possono essere nuovamente recuperate, ma è possibile generarne di nuove.

> [!NOTE]
> Se si desidera rigenerare le password del token e impostare i periodi di scadenza delle password, vedere [rigenerare le password del token](#regenerate-token-passwords) più avanti in questo articolo.

## <a name="create-token---portal"></a>Creare il token - portale

È possibile usare il portale di Azure per creare token e mapping di ambito. Come per il comando `az acr token create` dell'interfaccia della riga di comando, è possibile applicare un mapping di ambito esistente o creare un mapping di ambito quando si crea un token specificando uno o più repository e le azioni associate. Non è necessario che i repository siano già nel registro. 

Nell'esempio seguente vengono creati un token e un mapping di ambito con le seguenti autorizzazioni per il repository `samples/hello-world`: `content/write` e `content/read`.

1. Passare al registro contenitori nel portale.
1. In **autorizzazioni repository** selezionare **token (anteprima) > + Aggiungi**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Creare il token nel portale":::
1. Immettere un nome di token.
1. In **Mapping di ambito** selezionare **Crea nuovo**.
1. Configurare il mapping di ambito:
    1. Immettere un nome e una descrizione per il mapping di ambito. 
    1. In **Repository** immettere `samples/hello-world` e in **Autorizzazioni** selezionare `content/read` e `content/write`. Quindi selezionare **+Aggiungi**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Creare un mapping di ambito nel portale":::

    1. Dopo l'aggiunta di repository e autorizzazioni, selezionare **Aggiungi** per aggiungere il mapping di ambito.
1. Accettare lo **stato** predefinito del token **Abilitato** e quindi selezionare **Crea**.

Dopo che il token è stato convalidato e creato, i dettagli del token vengono visualizzati nella schermata **Token**.

### <a name="add-token-password"></a>Aggiungere la password del token

Per usare un token creato nel portale, è necessario generare una password. È possibile generare una o due password e impostare una data di scadenza per ciascuna di esse. 

1. Passare al registro contenitori nel portale.
1. In **autorizzazioni repository** selezionare **token (anteprima)** e selezionare un token.
1. Nei dettagli del token selezionare **password1** o **password2** e selezionare l'icona Genera.
1. Nella schermata della password, se necessario impostare una data di scadenza per la password e selezionare **Genera**. È consigliabile impostare una data di scadenza.
1. Dopo aver generato una password, copiarla e salvarla in una posizione sicura. Non è possibile recuperare una password generata dopo aver chiuso la schermata, ma è possibile generarne una nuova.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Creare la password del token nel portale":::

## <a name="authenticate-with-token"></a>Eseguire l'autenticazione con un token

Quando un utente o un servizio usa un token per l'autenticazione con il registro di destinazione, specifica il nome del token come nome utente e una delle password generate. 

Il metodo di autenticazione dipende dall'azione o dalle azioni configurate associate al token.

|Azione  |Come eseguire l'autenticazione  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` nell'interfaccia della riga di comando di Azure<br/><br/>Esempio: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` nell'interfaccia della riga di comando di Azure<br/><br/>Esempio: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` nell'interfaccia della riga di comando di Azure     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` nell'interfaccia della riga di comando di Azure   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` nell'interfaccia della riga di comando di Azure |

## <a name="examples-use-token"></a>Esempi: Usare il token

Gli esempi seguenti usano il token creato in precedenza in questo articolo per eseguire operazioni comuni su un repository: push e pull di immagini, eliminazione di immagini ed elenco dei tag del repository. Il token è stato configurato inizialmente con le autorizzazioni push (azioni `content/write` e `content/read`) per il repository `samples/hello-world`.

### <a name="pull-and-tag-test-images"></a>Eseguire il pull e contrassegnare le immagini di test

Per gli esempi seguenti, eseguire il pull delle immagini `hello-world` e `alpine` da Docker Hub e contrassegnarle per il registro e il repository.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag alpine myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Eseguire l'autenticazione con il token

Eseguire `docker login` o `az acr login` per eseguire l'autenticazione con il registro di sistema per eseguire il push o il pull delle immagini. Specificare il nome del token come nome utente e specificare una delle password. Lo stato del token deve essere `Enabled`.

L'esempio seguente è formattato per la shell Bash e specifica i valori usando le variabili di ambiente.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

L'output dovrebbe indicare che l'autenticazione è riuscita:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Eseguire il push delle immagini nel registro

Dopo aver eseguito l'accesso, provare a eseguire il push delle immagini contrassegnate nel registro. Poiché il token ha le autorizzazioni per eseguire il push delle immagini nel repository `samples/hello-world`, il push seguente ha esito positivo:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Il token non ha le autorizzazioni per il repository `samples/alpine`, quindi il tentativo di push seguente ha esito negativo con un errore simile a `requested access to the resource is denied`:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="update-token-permissions"></a>Aggiornare le autorizzazioni del token

Per aggiornare le autorizzazioni di un token, aggiornare le autorizzazioni nel mapping di ambito associato. Il mapping di ambito aggiornato viene applicato immediatamente a tutti i token associati. 

Ad esempio, aggiornare `MyToken-scope-map` con le azioni `content/write` e `content/read` per il repository `samples/alpine` e rimuovere l'azione `content/write` per il repository `samples/hello-world`.  

Per usare l'interfaccia della riga di comando di Azure, eseguire [az acr scope-map update][az-acr-scope-map-update] per aggiornare il mapping di ambito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Nel portale di Azure:

1. Passare al registro contenitori.
1. In **autorizzazioni repository** selezionare **mappe ambito (anteprima)** e selezionare la mappa dell'ambito da aggiornare.
1. In **Repository** immettere `samples/alpine` e in **Autorizzazioni** selezionare `content/read` e `content/write`. Quindi selezionare **+Aggiungi**.
1. In **Repository** selezionare `samples/hello-world` e in **Autorizzazioni** deselezionare `content/write`. Selezionare quindi **Salva**.

Dopo aver aggiornato il mapping di ambito, il push seguente viene eseguito correttamente:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Poiché il mapping di ambito ha solo l'autorizzazione `content/read` per il repository `samples/hello-world`, un tentativo di push al repository `samples/hello-world` ora non riesce:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Il pull di immagini da entrambi i repository ha esito positivo, perché il mapping di ambito concede autorizzazioni `content/read` per entrambi i repository:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Eliminare le immagini

Aggiornare il mapping di ambito aggiungendo l'azione `content/delete` al repository `alpine`. Questa azione consente di eliminare immagini nel repository o l'intero repository.

Per brevità, viene visualizzato solo il comando [az acr scope-map update][az-acr-scope-map-update] per l'aggiornamento del mapping di ambito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Per aggiornare la mappa dell'ambito usando il portale, vedere la [sezione precedente](#update-token-permissions).

Usare il seguente comando [az acr repository delete][az-acr-repository-delete] per eliminare il repository `samples/alpine`. Per eliminare immagini o repository, passare il nome e la password del token al comando. L'esempio seguente usa le variabili di ambiente create in precedenza nell'articolo:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Visualizzare i tag del repository 

Aggiornare il mapping di ambito aggiungendo l'azione `metadata/read` al repository `hello-world`. Questa azione consente di leggere i dati del manifesto e dei tag nel repository.

Per brevità, viene visualizzato solo il comando [az acr scope-map update][az-acr-scope-map-update] per l'aggiornamento del mapping di ambito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Per aggiornare la mappa dell'ambito usando il portale, vedere la [sezione precedente](#update-token-permissions).

Per leggere i metadati nel repository `samples/hello-world`, eseguire il comando [az acr repository show-manifests][az-acr-repository-show-manifests] o [az acr repository show-tags][az-acr-repository-show-tags]. 

Per leggere i metadati, passare il nome e la password del token a uno dei comandi. L'esempio seguente usa le variabili di ambiente create in precedenza nell'articolo:

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

## <a name="manage-tokens-and-scope-maps"></a>Gestire i token e i mapping di ambito

### <a name="list-scope-maps"></a>Elenco dei mapping di ambito

Per elencare tutti i mapping di ambito configurati in un registro, usare il comando [az acr scope-map list][az-acr-scope-map-list] o la schermata **Mapping di ambito (anteprima** ) nel portale. Ad esempio:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

L'output è costituito dalle tre mappe dell'ambito definite dal sistema e da altre mappe dell'ambito generate dall'utente. I token possono essere configurati con una qualsiasi di queste mappe dell'ambito.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Visualizzare i dettagli del token

Per visualizzare i dettagli di un token, ad esempio lo stato e le date di scadenza delle password, eseguire il comando [az acr token show][az-acr-token-show] oppure selezionare il token nella schermata **Token (anteprima)** nel portale. Ad esempio:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Per elencare tutti i token configurati in un registro, usare il comando [az acr token list][az-acr-token-list] o la schermata **Token (anteprima)** nel portale. Ad esempio:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Rigenera password token

Se non è stata generata una password per il token o si vogliono generare nuove password, eseguire il comando [AZ ACR token Credential generate][az-acr-token-credential-generate] . 

Nell'esempio seguente viene generato un nuovo valore per password1 per il token *MyToken* , con un periodo di scadenza di 30 giorni. La password viene archiviata nella variabile di ambiente `TOKEN_PWD`. Questo esempio è formattato per la shell Bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Per usare il portale di Azure per generare una password del token, vedere i passaggi in [Creare il token - portale](#create-token---portal) più indietro in questo articolo.

### <a name="update-token-with-new-scope-map"></a>Aggiornare il token con un nuovo mapping di ambito

Se si vuole aggiornare un token con un altro mapping di ambito, eseguire [az acr token update][az-acr-token-update] e specificare il nuovo mapping di ambito. Ad esempio:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Nella schermata **Token (anteprima)** del portale selezionare il token e quindi un altro mapping di ambito in **Mapping di ambito**.

> [!TIP]
> Dopo l'aggiornamento di un token con un nuovo mapping di ambito, potrebbe essere necessario generare nuove password per il token. Usare il comando [az acr token credential generate][az-acr-token-credential-generate] o rigenerare una password del token nel portale di Azure.

## <a name="disable-or-delete-token"></a>Disabilitare o eliminare il token

Potrebbe essere necessario disabilitare temporaneamente l'uso delle credenziali del token per un utente o un servizio. 

Usando l'interfaccia della riga di comando di Azure, eseguire il comando [az acr token update][az-acr-token-update] per impostare `status` su `disabled`:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Nel portale selezionare il token nella schermata **Token (anteprima)** e selezionare **Disabilitato** in **Stato**.

Per eliminare un token e invalidare in modo permanente l'accesso da parte di chiunque usi le relative credenziali, eseguire il comando [az acr token delete][az-acr-token-delete]. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Nel portale selezionare il token nella schermata **Token (anteprima)** e selezionare **Rimuovi**.

## <a name="next-steps"></a>Passaggi successivi

* Per gestire i mapping di ambito e i token, usare i comandi aggiuntivi dei gruppi [az acr scope-map][az-acr-scope-map] e [az acr token][az-acr-token].
* Vedere la [panoramica sull'autenticazione](container-registry-authentication.md) per altre opzioni per l'autenticazione con un registro contenitori di Azure, tra cui l'uso di un'identità di Azure Active Directory, un'entità servizio o un account amministratore.


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
