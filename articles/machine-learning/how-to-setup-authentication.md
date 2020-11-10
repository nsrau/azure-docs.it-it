---
title: Configurare l'autenticazione
titleSuffix: Azure Machine Learning
description: Informazioni su come impostare e configurare l'autenticazione per diverse risorse e flussi di lavoro in Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperfq2
ms.openlocfilehash: adc0547e36e9cf996a87c2683b4830541b8cd360
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442107"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurare l'autenticazione per le risorse e i flussi di lavoro di Azure Machine Learning


Informazioni su come configurare l'autenticazione per l'area di lavoro Azure Machine Learning. L'autenticazione per l'area di lavoro Azure Machine Learning è basata sulla __Azure Active Directory__ (Azure ad) per la maggior parte delle operazioni. In generale, sono disponibili tre flussi di lavoro di autenticazione che è possibile usare per la connessione all'area di lavoro:

* __Interattivo__ : è possibile usare l'account Azure Active Directory per l'autenticazione diretta o per ottenere un token usato per l'autenticazione. L'autenticazione interattiva viene utilizzata durante la _sperimentazione e lo sviluppo iterativo_. L'autenticazione interattiva consente di controllare l'accesso alle risorse, ad esempio un servizio Web, per ogni singolo utente.

* __Entità servizio__ : è possibile creare un account dell'entità servizio in Azure Active Directory e usarlo per autenticare o ottenere un token. Un'entità servizio viene utilizzata quando è necessario un _processo automatizzato per l'autenticazione_ al servizio senza richiedere l'interazione dell'utente. Ad esempio, uno script di integrazione e distribuzione continua che esegue il training e il test di un modello ogni volta che viene modificato il codice di training.

* __Identità gestita__ : quando si usa Azure Machine Learning SDK _in una macchina virtuale di Azure_ , è possibile usare un'identità gestita per Azure. Questo flusso di lavoro consente alla macchina virtuale di connettersi all'area di lavoro usando l'identità gestita, senza archiviare le credenziali nel codice Python o richiedere all'utente di eseguire l'autenticazione. I cluster di calcolo Azure Machine Learning possono anche essere configurati per usare un'identità gestita per accedere all'area di lavoro durante il _training dei modelli_.

> [!IMPORTANT]
> Indipendentemente dal flusso di lavoro di autenticazione usato, il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) viene usato per definire l'ambito del livello di accesso (autorizzazione) consentito per le risorse. Ad esempio, un processo di amministrazione o automazione potrebbe avere accesso per creare un'istanza di calcolo, ma non utilizzarla, mentre un data scientist può utilizzarla, ma non eliminarla o crearla. Per altre informazioni, vedere [gestire l'accesso all'area di lavoro Azure Machine Learning](how-to-assign-roles.md).

## <a name="prerequisites"></a>Prerequisiti

* Creare un'[area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).
* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare il Azure Machine Learning SDK oppure usare un' [istanza di calcolo Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) con l'SDK già installato.

## <a name="azure-active-directory"></a>Azure Active Directory

Tutti i flussi di lavoro di autenticazione per l'area di lavoro si basano su Azure Active Directory. Se si vuole che gli utenti eseguano l'autenticazione con singoli account, è necessario che dispongano di account nel Azure AD. Se si desidera utilizzare le entità servizio, è necessario che siano presenti nel Azure AD. Anche le identità gestite sono una funzionalità di Azure AD. 

Per ulteriori informazioni su Azure AD, vedere la pagina relativa all' [autenticazione Azure Active Directory](..//active-directory/authentication/overview-authentication.md).

Dopo aver creato gli account di Azure AD, vedere [gestire l'accesso all'area di lavoro Azure Machine Learning](how-to-assign-roles.md) per informazioni su come concedere loro l'accesso all'area di lavoro e ad altre operazioni in Azure Machine Learning.

## <a name="configure-a-service-principal"></a>Configurare un'entità servizio

Per usare un'entità servizio (SP), è necessario innanzitutto creare il servizio SP e concedergli l'accesso all'area di lavoro. Come indicato in precedenza, il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) viene usato per controllare l'accesso, quindi è necessario decidere anche quale accesso concedere a SP.

> [!IMPORTANT]
> Quando si usa un'entità servizio, concedere a tale entità l' __accesso minimo necessario per l'attività__ per cui viene usato. Non è ad esempio possibile concedere a un proprietario dell'entità servizio o a un accesso collaboratore se ne viene usato solo uno per la lettura del token di accesso per una distribuzione Web.
>
> Il motivo per cui si concede l'accesso minimo è che un'entità servizio usa una password per l'autenticazione e la password può essere archiviata come parte di uno script di automazione. Se la password viene persa, l'accesso minimo richiesto per le attività specifiche riduce al minimo l'utilizzo dannoso di SP.

Il modo più semplice per creare un SP e concedere l'accesso all'area di lavoro consiste nell'usare l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest). Per creare un'entità servizio e concedere l'accesso all'area di lavoro, seguire questa procedura:

> [!NOTE]
> Per eseguire tutti questi passaggi, è necessario essere un amministratore della sottoscrizione.

1. Eseguire l'autenticazione alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono l'individuazione di [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e l'immissione di un codice di autorizzazione.

    Se si hanno più sottoscrizioni di Azure, è possibile usare il `az account set -s <subscription name or ID>` comando per impostare la sottoscrizione. Per altre informazioni, vedere [Use multiple Azure subscriptions](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) (Usare più sottoscrizioni di Azure).

    Per altri metodi di autenticazione, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest).

1. Installare l'estensione Azure Machine Learning:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Creare l'entità servizio. Nell'esempio seguente viene creato un SP denominato **ml-auth** :

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    L'output sarà un JSON simile al seguente. Prendere nota dei campi `clientId`, `clientSecret` e `tenantId`, perché saranno necessari per altri passaggi di questo articolo.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Recuperare i dettagli per l'entità servizio usando il `clientId` valore restituito nel passaggio precedente:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Il codice JSON seguente è un esempio semplificato dell'output del comando. Prendere nota del campo `objectId`, in quanto il suo valore sarà necessario per il passaggio successivo.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Consentire a SP di accedere all'area di lavoro Azure Machine Learning. Per i parametri `-w` e `-g` sono necessari rispettivamente il nome dell'area di lavoro e il nome del gruppo di risorse. Per il parametro `--user`, usare il valore `objectId` del passaggio precedente. Il `--role` parametro consente di impostare il ruolo di accesso per l'entità servizio. Nell'esempio seguente, l'SP viene assegnato al ruolo **proprietario** . 

    > [!IMPORTANT]
    > L'accesso del proprietario consente all'entità servizio di eseguire praticamente qualsiasi operazione nell'area di lavoro. Viene usato in questo documento per dimostrare come concedere l'accesso; in un ambiente di produzione Microsoft consiglia di concedere all'entità servizio l'accesso minimo necessario per eseguire il ruolo per il quale si desidera. Per informazioni sulla creazione di un ruolo personalizzato con l'accesso necessario per lo scenario, vedere [gestire l'accesso all'area di lavoro Azure Machine Learning](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Questa chiamata non produce alcun output in caso di esito positivo.

## <a name="configure-a-managed-identity"></a>Configurare un'identità gestita

> [!IMPORTANT]
> L'identità gestita è supportata solo quando si usa Azure Machine Learning SDK da una macchina virtuale di Azure o con un cluster di calcolo Azure Machine Learning. L'uso di un'identità gestita con un cluster di calcolo è attualmente in anteprima.

### <a name="managed-identity-with-a-vm"></a>Identità gestita con una macchina virtuale

1. Abilitare un' [identità gestita assegnata dal sistema per le risorse di Azure nella macchina virtuale](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. Dal [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro, quindi selezionare __controllo di accesso (IAM)__ , __Aggiungi assegnazione ruolo__ e selezionare __macchina virtuale__ dall'elenco __a discesa assegna accesso a__ . Infine, selezionare l'identità della macchina virtuale.

1. Consente di selezionare il ruolo da assegnare a questa identità. Ad esempio, collaboratore o ruolo personalizzato. Per ulteriori informazioni, vedere [controllo dell'accesso alle risorse](how-to-assign-roles.md).

### <a name="managed-identity-with-compute-cluster"></a>Identità gestita con cluster di calcolo

Per altre informazioni, vedere [configurare l'identità gestita per il cluster di calcolo](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Usa autenticazione interattiva

> [!IMPORTANT]
> L'autenticazione interattiva usa il browser e richiede cookie, inclusi i cookie di terze parti. Se i cookie sono stati disabilitati, è possibile che venga visualizzato un errore, ad esempio "non è stato possibile accedere". Questo errore può verificarsi anche se è stata abilitata [l'autenticazione a più fattori di Azure](../active-directory/authentication/concept-mfa-howitworks.md).

Per la maggior parte degli esempi nella documentazione e negli esempi viene usata l'autenticazione interattiva. Ad esempio, quando si usa l'SDK sono presenti due chiamate di funzione che richiederanno automaticamente un flusso di autenticazione basato sull'interfaccia utente:

* La chiamata della funzione `from_config()` genererà il prompt.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    La funzione `from_config()` cerca un file JSON contenente le informazioni di connessione dell'area di lavoro.

* Se si usa il `Workspace` costruttore per fornire informazioni su sottoscrizioni, gruppi di risorse e aree di lavoro, verrà richiesta anche l'autenticazione interattiva.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Se si ha accesso a più tenant, potrebbe essere necessario importare la classe e definire in modo esplicito il tenant di destinazione. Se si chiama il costruttore per `InteractiveLoginAuthentication` verrà richiesto di eseguire l'accesso in modo analogo alle chiamate precedenti.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Quando si usa l'interfaccia della `az login` riga di comando di Azure, il comando viene usato per autenticare la sessione cli. Per altre informazioni, vedere [Introduzione all'interfaccia](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)della riga di comando di Azure.

> [!TIP]
> Se si usa l'SDK da un ambiente in cui in precedenza è stata eseguita l'autenticazione interattiva usando l'interfaccia della riga di comando di Azure, è possibile usare la `AzureCliAuthentication` classe per autenticare l'area di lavoro usando le credenziali memorizzate nella cache dell'interfaccia della riga di comando:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Usare l'autenticazione basata su entità servizio

Per eseguire l'autenticazione all'area di lavoro dall'SDK, usando un'entità servizio, usare il `ServicePrincipalAuthentication` costruttore della classe. Usare i valori ottenuti durante la creazione del provider di servizi come parametri. Il parametro `tenant_id` viene mappato a `tenantId`, `service_principal_id` viene mappato a `clientId` e `service_principal_password` viene mappato a `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

La variabile `sp` ora include un oggetto di autenticazione da usare direttamente nell'SDK. In generale, è consigliabile archiviare gli ID e i segreti usati in precedenza nelle variabili di ambiente, come illustrato nel codice seguente. L'archiviazione nelle variabili di ambiente impedisce che le informazioni vengano accidentalmente archiviate in un repository GitHub.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Per i flussi di lavoro automatizzati eseguiti in Python e che usano principalmente l'SDK, è possibile usare questo oggetto così com'è nella maggior parte dei casi per l'autenticazione. Il codice seguente esegue l'autenticazione nell'area di lavoro usando l'oggetto di autenticazione creato.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Usare un'entità servizio dall'interfaccia della riga di comando di Azure

È possibile usare un'entità servizio per i comandi dell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [accedere usando un'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli?preserve-view=true&view=azure-cli-latest#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Usare un'entità servizio con l'API REST (anteprima)

L'entità servizio può essere usata anche per l'autenticazione all' [API REST](/rest/api/azureml/) di Azure Machine Learning (anteprima). Si usa [il flusso di concessione delle credenziali client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) di Azure Active Directory, che consente di eseguire chiamate da servizio a servizio per l'autenticazione headless nei flussi di lavoro automatizzati. Gli esempi sono implementati con la [libreria ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md) sia in Python sia in Node.js, ma è possibile usare qualsiasi libreria open source che supporta OpenID Connect 1.0.

> [!NOTE]
> MSAL.js è una libreria più recente rispetto ad ADAL, ma con essa non è possibile eseguire l'autenticazione da servizio a servizio con le credenziali client perché si tratta principalmente di una libreria lato client destinata all'autenticazione interattiva/dell'interfaccia utente associata a un utente specifico. È consigliabile usare ADAL come illustrato di seguito per creare flussi di lavoro automatizzati con l'API REST.

#### <a name="nodejs"></a>Node.js

Usare la procedura seguente per generare un token di autenticazione con Node.js. Nell'ambiente corrente, eseguire `npm install adal-node`. Usare quindi i valori `tenantId`, `clientId` e `clientSecret` dall'entità servizio creata nei passaggi precedenti per le variabili corrispondenti nello script seguente.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

La variabile `tokenResponse` è un oggetto che include il token e i metadati associati, ad esempio la data di scadenza. I token sono validi per 1 ora e possono essere aggiornati eseguendo la stessa chiamata per recuperare un nuovo token. Il frammento di codice seguente è una risposta di esempio.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Usare la proprietà `accessToken` per recuperare il token di autenticazione. Per esempi su come usare il token per effettuare chiamate API, vedere la [documentazione sull'API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API).

#### <a name="python"></a>Python

Usare la procedura seguente per generare un token di autenticazione con Python. Nell'ambiente corrente, eseguire `pip install adal`. Usare quindi i valori `tenantId`, `clientId` e `clientSecret` dall'entità servizio creata nei passaggi precedenti per le variabili appropriate nello script seguente.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

La variabile `token_response` è un dizionario che include il token e i metadati associati, ad esempio la data di scadenza. I token sono validi per 1 ora e possono essere aggiornati eseguendo la stessa chiamata per recuperare un nuovo token. Il frammento di codice seguente è una risposta di esempio.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Usare `token_response["accessToken"]` per recuperare il token di autenticazione. Per esempi su come usare il token per effettuare chiamate API, vedere la [documentazione sull'API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API).

#### <a name="java"></a>Java

In Java recuperare il bearer token usando una chiamata REST standard:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

Il codice precedente dovrebbe gestire le eccezioni e i codici di stato diversi da `200 OK` , ma mostra il modello: 

- Usare l'ID client e il segreto per verificare che il programma abbia accesso
- Usare l'ID tenant per specificare dove `login.microsoftonline.com` deve essere cercato
- Usare Azure Resource Manager come origine del token di autorizzazione

## <a name="use-managed-identity-authentication"></a>Usa autenticazione identità gestita

Per eseguire l'autenticazione nell'area di lavoro da una macchina virtuale o da un cluster di calcolo configurato con un'identità gestita, usare la `MsiAuthentication` classe. Nell'esempio seguente viene illustrato come utilizzare questa classe per l'autenticazione in un'area di lavoro:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Passaggi successivi

* [Come usare i segreti nel training](how-to-use-secrets-in-runs.md).
* [Come configurare l'autenticazione per i modelli distribuiti come un servizio Web](how-to-authenticate-web-service.md).
* [Usare un modello di Azure Machine Learning distribuito come servizio Web](how-to-consume-web-service.md).