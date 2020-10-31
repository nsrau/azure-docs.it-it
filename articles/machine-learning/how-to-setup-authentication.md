---
title: Configurare l'autenticazione
titleSuffix: Azure Machine Learning
description: "Informazioni su come impostare e configurare l'autenticazione per diverse risorse e flussi di lavoro in Azure Machine Learning. Esistono diversi modi per configurare e usare l'autenticazione all'interno del servizio: dalla semplice autenticazione basata sull'interfaccia utente a scopo di sviluppo o test, all'autenticazione Azure Active Directory basata su entità servizio completa."
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli
ms.openlocfilehash: a23f44e60bd68e51c26cc6a0bbf3e85e64914135
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125768"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurare l'autenticazione per le risorse e i flussi di lavoro di Azure Machine Learning


Informazioni su come eseguire l'autenticazione per l'area di lavoro di Azure Machine Learning e per i modelli distribuiti come servizi Web.

In generale, esistono due tipi di autenticazione che è possibile usare con Azure Machine Learning:

* __Interattivo__ : è possibile usare l'account Azure Active Directory per l'autenticazione diretta o per ottenere un token usato per l'autenticazione. L'autenticazione interattiva viene utilizzata durante la sperimentazione e lo sviluppo iterativo. O in cui si vuole controllare l'accesso alle risorse, ad esempio un servizio Web, per ogni singolo utente.
* __Entità servizio__ : è possibile creare un account dell'entità servizio in Azure Active Directory e usarlo per autenticare o ottenere un token. Un'entità servizio viene utilizzata quando è necessario un processo automatizzato per l'autenticazione al servizio senza richiedere l'interazione dell'utente. Ad esempio, uno script di integrazione e distribuzione continua che esegue il training e il test di un modello ogni volta che viene modificato il codice di training. È anche possibile usare un'entità servizio per recuperare un token per l'autenticazione a un servizio Web, se non si vuole che l'utente finale del servizio esegua l'autenticazione. O in cui l'autenticazione dell'utente finale non viene eseguita direttamente usando Azure Active Directory.

Indipendentemente dal tipo di autenticazione usato, il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) viene usato per definire l'ambito del livello di accesso consentito alle risorse. Ad esempio, un account utilizzato per ottenere il token di accesso per un modello distribuito richiede solo l'accesso in lettura all'area di lavoro. Per ulteriori informazioni sul controllo degli accessi in base al ruolo di Azure, vedere [Manage Access to Azure Machine Learning Workspace](how-to-assign-roles.md)

## <a name="prerequisites"></a>Prerequisiti

* Creare un'[area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).
* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare l'SDK Azure Machine Learning o usare una [macchina virtuale con notebook Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) con l'SDK già installato.

## <a name="interactive-authentication"></a>Autenticazione interattiva

> [!IMPORTANT]
> L'autenticazione interattiva usa il browser e richiede cookie, inclusi i cookie di terze parti. Se i cookie sono stati disabilitati, è possibile che venga visualizzato un errore, ad esempio "non è stato possibile accedere". Questo errore può verificarsi anche se è stata abilitata [l'autenticazione a più fattori di Azure](/azure/active-directory/authentication/concept-mfa-howitworks).

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

## <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per utilizzare l'autenticazione dell'entità servizio (SP), è necessario innanzitutto creare il servizio SP e concedergli l'accesso all'area di lavoro. Come indicato in precedenza, il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) viene usato per controllare l'accesso, quindi è necessario decidere anche quale accesso concedere a SP.

> [!IMPORTANT]
> Quando si usa un'entità servizio, concedere a tale entità l' __accesso minimo necessario per l'attività__ per cui viene usato. Non è ad esempio possibile concedere a un proprietario dell'entità servizio o a un accesso collaboratore se ne viene usato solo uno per la lettura del token di accesso per una distribuzione Web.
>
> Il motivo per cui si concede l'accesso minimo è che un'entità servizio usa una password per l'autenticazione e la password può essere archiviata come parte di uno script di automazione. Se la password viene persa, l'accesso minimo richiesto per le attività specifiche riduce al minimo l'utilizzo dannoso di SP.

Il modo più semplice per creare un SP e concedere l'accesso all'area di lavoro consiste nell'usare l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Per creare un'entità servizio e concedere l'accesso all'area di lavoro, seguire questa procedura:

> [!NOTE]
> Per eseguire tutti questi passaggi, è necessario essere un amministratore della sottoscrizione.

1. Eseguire l'autenticazione alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono l'individuazione di [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e l'immissione di un codice di autorizzazione.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    Per altri metodi di autenticazione, vedere [Accedere con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

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
    > L'accesso del proprietario consente all'entità servizio di eseguire praticamente qualsiasi operazione nell'area di lavoro. Viene usato in questo documento per dimostrare come concedere l'accesso; in un ambiente di produzione Microsoft consiglia di concedere all'entità servizio l'accesso minimo necessario per eseguire il ruolo per il quale si desidera. Per altre informazioni, vedere [gestire l'accesso all'area di lavoro Azure Machine Learning](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Questa chiamata non produce alcun output in caso di esito positivo.

### <a name="use-a-service-principal-from-the-sdk"></a>Usare un'entità servizio dall'SDK

Per eseguire l'autenticazione nell'area di lavoro dall'SDK, usando l'entità servizio, usare il `ServicePrincipalAuthentication` costruttore della classe. Usare i valori ottenuti durante la creazione del provider di servizi come parametri. Il parametro `tenant_id` viene mappato a `tenantId`, `service_principal_id` viene mappato a `clientId` e `service_principal_password` viene mappato a `clientSecret`.

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

È possibile usare un'entità servizio per i comandi dell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [accedere usando un'entità servizio](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest&preserve-view=true#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Usare un'entità servizio con l'API REST (anteprima)

L'entità servizio può essere usata anche per l'autenticazione all' [API REST](https://docs.microsoft.com/rest/api/azureml/) di Azure Machine Learning (anteprima). Si usa [il flusso di concessione delle credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) di Azure Active Directory, che consente di eseguire chiamate da servizio a servizio per l'autenticazione headless nei flussi di lavoro automatizzati. Gli esempi sono implementati con la [libreria ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) sia in Python sia in Node.js, ma è possibile usare qualsiasi libreria open source che supporta OpenID Connect 1.0.

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

## <a name="web-service-authentication"></a>Autenticazione del servizio Web

Le distribuzioni di modelli create da Azure Machine Learning forniscono due metodi di autenticazione:

* **basato su chiavi** : una chiave statica viene usata per l'autenticazione nel servizio Web.
* **basato su token** : è necessario ottenere un token temporaneo dall'area di lavoro e usarlo per l'autenticazione al servizio Web. Questo token scade dopo un periodo di tempo e deve essere aggiornato per continuare a usare il servizio Web.

    > [!NOTE]
    > L'autenticazione basata su token è disponibile solo quando si esegue la distribuzione nel servizio Azure Kubernetes.

### <a name="key-based-web-service-authentication"></a>Autenticazione del servizio Web basata su chiave

I servizi Web distribuiti in Azure Kubernetes Service (AKS) hanno l'autenticazione basata su chiavi *abilitata* per impostazione predefinita. Per i servizi distribuiti da istanze di contenitore di Azure (ACI) l'autenticazione basata su chiavi è *disabilitata* per impostazione predefinita, ma è possibile abilitarla impostando `auth_enabled=True` quando si crea il servizio Web ACI. Il codice seguente è un esempio di creazione di una configurazione di distribuzione ACI con l'autenticazione basata su chiavi abilitata.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

È quindi possibile usare la configurazione personalizzata di Istanze di Azure Container nella distribuzione usando la classe `Model`.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Per recuperare il token di autenticazione usare `aci_service.get_keys()`. Per rigenerare una chiave, usare la funzione `regen_key()` e passare **Primaria** o **Secondaria** .

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Per ulteriori informazioni sull'autenticazione a un modello distribuito, vedere [creare un client per un modello distribuito come servizio Web](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Autenticazione del servizio Web tramite token

Quando si abilita l'autenticazione tramite token per un servizio Web, gli utenti devono presentare un token JSON Web di Azure Machine Learning al servizio Web per accedervi. Il token scade dopo un periodo di tempo specificato e deve essere aggiornato per continuare a eseguire chiamate.

* L'autenticazione tramite token è **disabilitata per impostazione predefinita** quando si esegue la distribuzione al servizio Azure Kubernetes.
* L'autenticazione tramite token **non è supportata** quando si esegue la distribuzione a Istanze di Azure Container.
* **Non è possibile usare l'autenticazione basata su token allo stesso tempo dell'autenticazione basata su chiavi** .

Per controllare l'autenticazione del token, usare il `token_auth_enabled` parametro quando si crea o si aggiorna una distribuzione:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Se l'autenticazione tramite token è abilitata, è possibile usare il metodo `get_token` per recuperare un token JSON Web (JWT) e la data di scadenza del token:

> [!TIP]
> Se si utilizza un'entità servizio per ottenere il token e si desidera che disponga dell'accesso minimo richiesto per recuperare un token, assegnarlo al ruolo **Reader** per l'area di lavoro.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Al termine della durata del token `refresh_by`, sarà necessario richiedere un nuovo token. Se è necessario aggiornare i token al di fuori di Python SDK, un'opzione consiste nell'usare l'API REST con l'autenticazione dell'entità servizio per effettuare periodicamente la chiamata `service.get_token()`, come illustrato in precedenza.
>
> È consigliabile creare l'area di lavoro Azure Machine Learning nella stessa area del cluster del servizio Azure Kubernetes.
>
> Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro Azure Machine Learning. Se tale area non è disponibile, non sarà possibile recuperare un token per il servizio Web, anche se il cluster si trova in un'area diversa dall'area di lavoro. Il risultato è che Autenticazione di Azure AD non è disponibile fino a quando l'area dell'area di lavoro non sarà nuovamente disponibile.
>
> Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, più tempo sarà necessario per recuperare un token.

## <a name="next-steps"></a>Passaggi successivi

* [Come usare i segreti nel training](how-to-use-secrets-in-runs.md).
* [Eseguire il training di un modello di classificazione delle immagini](tutorial-train-models-with-aml.md).
* [Usare un modello di Azure Machine Learning distribuito come servizio Web](how-to-consume-web-service.md).
