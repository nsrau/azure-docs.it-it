---
title: Configurare l'autenticazione
titleSuffix: Azure Machine Learning
description: "Informazioni su come impostare e configurare l'autenticazione per diverse risorse e flussi di lavoro in Azure Machine Learning. Esistono diversi modi per configurare e usare l'autenticazione all'interno del servizio: dalla semplice autenticazione basata sull'interfaccia utente a scopo di sviluppo o test, all'autenticazione Azure Active Directory basata su entità servizio completa."
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 6b2cfa85ea412a5ef8bda47a7ff6e99970ba6b0e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611841"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurare l'autenticazione per le risorse e i flussi di lavoro di Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo contiene informazioni su come impostare e configurare l'autenticazione per diverse risorse e flussi di lavoro in Azure Machine Learning. Esistono diversi modi per eseguire l'autenticazione del servizio: dalla semplice autenticazione basata sull'interfaccia utente a scopo di sviluppo o test, all'autenticazione Azure Active Directory basata su entità servizio completa. Questo articolo illustra anche le differenze di funzionamento dell'autenticazione del servizio Web, nonché come eseguire l'autenticazione all'API REST di Azure Machine Learning.

Questo articolo illustra come eseguire le attività seguenti:

* Usare l'autenticazione interattiva dell'interfaccia utente a scopo di test/sviluppo
* Usare l'autenticazione basata sull'entità servizio
* Eseguire l'autenticazione nell'area di lavoro
* Ottenere i token di tipo di bearer OAuth 2.0 per l'API REST di Azure Machine Learning
* Comprendere l'autenticazione del servizio Web

Per una panoramica generale sulla sicurezza e l'autenticazione in Azure Machine Learning, vedere l'articolo [relativo al concetto](concept-enterprise-security.md).

## <a name="prerequisites"></a>Prerequisiti

* Creare un'[area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).
* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare l'SDK Azure Machine Learning o usare una [macchina virtuale con notebook Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) con l'SDK già installato.

## <a name="interactive-authentication"></a>Autenticazione interattiva

La maggior parte degli esempi nella documentazione per questo servizio usa l'autenticazione interattiva in notebook Jupyter come metodo semplice per il test e la dimostrazione. Si tratta di un modo comodo per testare ciò che si sta creando. Sono disponibili due chiamate di funzione che richiederanno automaticamente un flusso di autenticazione basato sull'interfaccia utente.

La chiamata della funzione `from_config()` genererà il prompt.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

La funzione `from_config()` cerca un file JSON contenente le informazioni di connessione dell'area di lavoro. È anche possibile specificare i dettagli della connessione in modo esplicito tramite il costruttore `Workspace`, che richiederà anche l'autenticazione interattiva. Entrambe le chiamate sono equivalenti.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Se si ha accesso a più tenant, potrebbe essere necessario importare la classe e definire in modo esplicito il tenant di destinazione. Se si chiama il costruttore per `InteractiveLoginAuthentication` verrà richiesto di eseguire l'accesso in modo analogo alle chiamate precedenti.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Sebbene sia utile a scopo di test e apprendimento, l'autenticazione interattiva non consente di creare flussi di lavoro automatizzati o headless. La configurazione dell'autenticazione basata su entità servizio è l'approccio migliore per i processi automatizzati che usano l'SDK.

## <a name="set-up-service-principal-authentication"></a>Usare l'autenticazione basata sull'entità servizio

Questo processo è necessario per abilitare l'autenticazione disaccoppiata da un accesso utente specifico che consente di eseguire l'autenticazione a Python SDK di Azure Machine Learning nei flussi di lavoro automatizzati. L'autenticazione basata su entità servizio consentirà anche l'[autenticazione all'API REST](#azure-machine-learning-rest-api-auth).

Per configurare l'autenticazione basata su entità servizio, creare prima di tutto una registrazione per l'app in Azure Active Directory, quindi concedere all'app l'accesso all'area di lavoro ML in base al ruolo. Il modo più semplice per completare questa configurazione è tramite [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) nel portale di Azure. Dopo aver eseguito l'accesso al portale, fare clic sull'icona `>_` nella parte superiore destra della pagina, accanto al nome per aprire la shell.

Se la shell cloud non è mai stata usata nell'account Azure, sarà necessario creare una risorsa dell'account di archiviazione per archiviare i file che vengono scritti. In generale, questo account di archiviazione sarà soggetto a un costo mensile irrisorio. Installare con il comando seguente anche l'estensione Machine Learning se non è stata usata in precedenza.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Per eseguire la procedura seguente, è necessario essere un amministratore della sottoscrizione.

Eseguire quindi il comando seguente per creare l'entità servizio. Assegnare un nome, in questo caso **ml-auth**.

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

Eseguire quindi il comando seguente per ottenere i dettagli sull'entità servizio appena creata, usando il valore `clientId` riportato sopra come input per il parametro `--id`.

```azurecli-interactive
az ad sp show --id your-client-id
```

L'esempio seguente illustra in maniera semplificata l'output JSON ottenuto dal comando. Prendere nota del campo `objectId`, in quanto il suo valore sarà necessario per il passaggio successivo.

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

Usare quindi il comando seguente per assegnare all'entità servizio l'accesso all'area di lavoro di apprendimento automatico. Per i parametri `-w` e `-g` sono necessari rispettivamente il nome dell'area di lavoro e il nome del gruppo di risorse. Per il parametro `--user`, usare il valore `objectId` del passaggio precedente. Il parametro `--role` consente di impostare il ruolo di accesso per l'entità servizio e, in generale, si userà **proprietario** o **collaboratore**. Entrambi hanno accesso in scrittura a risorse esistenti, ad esempio cluster di elaborazione e archivi dati, ma solo il parametro **proprietario** può effettuare il provisioning di queste risorse.

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Questa chiamata non produce alcun output, ma ora è stata configurata l'autenticazione dell'entità servizio per l'area di lavoro.

## <a name="authenticate-to-your-workspace"></a>Eseguire l'autenticazione all'area di lavoro

Ora che l'autenticazione dell'entità servizio è abilitata, è possibile eseguire l'autenticazione all'area di lavoro nell'SDK senza accedere fisicamente come utente. Usare il costruttore di classe `ServicePrincipalAuthentication` e usare come parametri i valori ottenuti nei passaggi precedenti. Il parametro `tenant_id` viene mappato a `tenantId`, `service_principal_id` viene mappato a `clientId` e `service_principal_password` viene mappato a `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

La variabile `sp` ora include un oggetto di autenticazione da usare direttamente nell'SDK. In generale, è consigliabile archiviare gli ID e i segreti usati in precedenza nelle variabili di ambiente, come illustrato nel codice seguente.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Per i flussi di lavoro automatizzati eseguiti in Python e che usano principalmente l'SDK, è possibile usare questo oggetto così com'è nella maggior parte dei casi per l'autenticazione. Il codice seguente esegue l'autenticazione nell'area di lavoro usando l'oggetto di autenticazione appena creato.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Autenticazione dell'API REST di Azure Machine Learning

L'entità servizio creata nei passaggi precedenti può essere usata anche per l'autenticazione dell'[API REST](https://docs.microsoft.com/rest/api/azureml/) di Azure Machine Learning. Si usa [il flusso di concessione delle credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) di Azure Active Directory, che consente di eseguire chiamate da servizio a servizio per l'autenticazione headless nei flussi di lavoro automatizzati. Gli esempi sono implementati con la [libreria ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) sia in Python sia in Node.js, ma è possibile usare qualsiasi libreria open source che supporta OpenID Connect 1.0.

> [!NOTE]
> MSAL.js è una libreria più recente rispetto ad ADAL, ma con essa non è possibile eseguire l'autenticazione da servizio a servizio con le credenziali client perché si tratta principalmente di una libreria lato client destinata all'autenticazione interattiva/dell'interfaccia utente associata a un utente specifico. È consigliabile usare ADAL come illustrato di seguito per creare flussi di lavoro automatizzati con l'API REST.

### <a name="nodejs"></a>Node.js

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

La variabile `tokenResponse` è un oggetto che include il token e i metadati associati, ad esempio la data di scadenza. I token sono validi per 1 ora e possono essere aggiornati eseguendo la stessa chiamata per recuperare un nuovo token. Quanto segue è una risposta di esempio.

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

### <a name="python"></a>Python

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

La variabile `token_response` è un dizionario che include il token e i metadati associati, ad esempio la data di scadenza. I token sono validi per 1 ora e possono essere aggiornati eseguendo la stessa chiamata per recuperare un nuovo token. Quanto segue è una risposta di esempio.

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

## <a name="web-service-authentication"></a>Autenticazione del servizio Web

I servizi Web in Azure Machine Learning usano un modello di autenticazione diverso rispetto a quello descritto in precedenza. Il modo più semplice per eseguire l'autenticazione ai servizi Web distribuiti consiste nell'usare l'**autenticazione basata su chiave**, che genera chiavi di autenticazione di tipo bearer statiche che non devono essere aggiornate. Se è necessario eseguire l'autenticazione solo in un servizio Web distribuito, non occorre configurare l'autenticazione del principio di servizio come illustrato in precedenza.

Nei servizi Web distribuiti nel servizio Azure Kubernetes, l'autenticazione basata su chiavi è *abilitata* per impostazione predefinita. Nei servizi distribuiti di Istanze di Azure Container, l'autenticazione basata su chiave è *disabilitata* per impostazione predefinita, ma può essere abilitata impostando `auth_enabled=True` durante la creazione del servizio Web ACI. Il seguente è un esempio di creazione di una configurazione di distribuzione di Istanze di Azure Container con l'autenticazione basata su chiave abilitata.

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

Per recuperare il token di autenticazione usare `aci_service.get_keys()`. Per rigenerare una chiave, usare la funzione `regen_key()` e passare **Primaria** o **Secondaria**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

I servizi Web supportano anche l'autenticazione basata su token, ma solo per le distribuzioni del servizio Azure Kubernetes. Per ulteriori informazioni sull'autenticazione, vedere la [procedura](how-to-consume-web-service.md) sull'uso di servizi Web.

### <a name="token-based-web-service-authentication"></a>Autenticazione del servizio Web tramite token

Quando si abilita l'autenticazione tramite token per un servizio Web, gli utenti devono presentare un token JSON Web di Azure Machine Learning al servizio Web per accedervi. Il token scade dopo un periodo di tempo specificato e deve essere aggiornato per continuare a eseguire chiamate.

* L'autenticazione tramite token è **disabilitata per impostazione predefinita** quando si esegue la distribuzione al servizio Azure Kubernetes.
* L'autenticazione tramite token **non è supportata** quando si esegue la distribuzione a Istanze di Azure Container.

Per controllare l'autenticazione tramite token, usare il parametro `token_auth_enabled` quando si crea o si aggiorna una distribuzione.

Se l'autenticazione tramite token è abilitata, è possibile usare il metodo `get_token` per recuperare un token JSON Web (JWT) e la data di scadenza del token:

```python
token, refresh_by = service.get_token()
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

* [Eseguire il training di un modello di classificazione delle immagini](tutorial-train-models-with-aml.md).
* [Usare un modello di Azure Machine Learning distribuito come servizio Web](how-to-consume-web-service.md).
