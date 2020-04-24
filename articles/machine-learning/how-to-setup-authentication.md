---
title: Configurare l'autenticazione
titleSuffix: Azure Machine Learning
description: Informazioni su come impostare e configurare l'autenticazione per diverse risorse e flussi di lavoro in Azure Machine Learning. Esistono diversi modi per configurare e usare l'autenticazione all'interno del servizio, dalla semplice autenticazione basata sull'interfaccia utente a scopo di sviluppo o test, alla completa Azure Active Directory l'autenticazione basata su entità servizio.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283537"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurare l'autenticazione per risorse e flussi di lavoro Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come configurare e configurare l'autenticazione per diverse risorse e flussi di lavoro in Azure Machine Learning. Esistono diversi modi per eseguire l'autenticazione al servizio, da un'autenticazione semplice basata sull'interfaccia utente a scopo di sviluppo o test fino a completa Azure Active Directory l'autenticazione basata su entità servizio. Questo articolo illustra anche le differenze nel funzionamento dell'autenticazione del servizio Web, nonché come eseguire l'autenticazione all'API REST di Azure Machine Learning.

In questa procedura viene illustrato come eseguire le attività seguenti:

* Usa l'autenticazione interattiva dell'interfaccia utente per test/sviluppo
* Configurare l'autenticazione dell'entità servizio
* Autenticazione nell'area di lavoro
* Ottenere i token di tipo di porta OAuth 2.0 per l'API REST di Azure Machine Learning
* Informazioni sull'autenticazione del servizio Web

Vedere l' [articolo del concetto](concept-enterprise-security.md) per una panoramica generale di sicurezza e autenticazione all'interno Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Creare un' [area di lavoro Azure Machine Learning](how-to-manage-workspace.md).
* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare il Azure Machine Learning SDK o usare una [VM Azure Machine Learning notebook](concept-azure-machine-learning-architecture.md#compute-instance) con l'SDK già installato.

## <a name="interactive-authentication"></a>Autenticazione interattiva

La maggior parte degli esempi nella documentazione per questo servizio usa l'autenticazione interattiva in Jupyter Notebooks come metodo semplice per il test e la dimostrazione. Si tratta di un modo semplice per testare ciò che si sta creando. Sono disponibili due chiamate di funzione che richiederanno automaticamente un flusso di autenticazione basato sull'interfaccia utente.

Quando si `from_config()` chiama la funzione, viene emesso il prompt.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

La `from_config()` funzione Cerca un file JSON contenente le informazioni di connessione dell'area di lavoro. È anche possibile specificare i dettagli della connessione in modo esplicito `Workspace` tramite il costruttore, che richiederà anche l'autenticazione interattiva. Entrambe le chiamate sono equivalenti.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Se si ha accesso a più tenant, potrebbe essere necessario importare la classe e definire in modo esplicito il tenant di destinazione. Quando si chiama il `InteractiveLoginAuthentication` costruttore per verrà richiesto di eseguire l'accesso in modo analogo alle chiamate precedenti.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Sebbene sia utile per i test e l'apprendimento, l'autenticazione interattiva non consente di creare flussi di lavoro automatizzati o senza test. La configurazione dell'autenticazione basata su entità servizio è l'approccio migliore per i processi automatizzati che usano l'SDK.

## <a name="set-up-service-principal-authentication"></a>Configurare l'autenticazione dell'entità servizio

Questo processo è necessario per l'abilitazione dell'autenticazione disaccoppiata da un account di accesso utente specifico che consente di eseguire l'autenticazione al Azure Machine Learning SDK Python nei flussi di lavoro automatizzati. L'autenticazione basata su entità servizio consentirà anche di eseguire [l'autenticazione all'API REST](#azure-machine-learning-rest-api-auth).

Per configurare l'autenticazione basata su entità servizio, creare prima di tutto una registrazione per l'app in Azure Active Directory, quindi concedere l'accesso in base al ruolo dell'app all'area di lavoro ML. Il modo più semplice per completare questa configurazione è tramite il [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) nel portale di Azure. Dopo aver eseguito l'accesso al portale, fare `>_` clic sull'icona in alto a destra nella pagina accanto al nome per aprire la Shell.

Se prima non è stato usato cloud Shell nell'account Azure, sarà necessario creare una risorsa dell'account di archiviazione per archiviare i file scritti. In generale, questo account di archiviazione subirà un costo mensile trascurabile. Installare anche l'estensione Machine Learning se non è stata usata in precedenza con il comando seguente.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Per eseguire la procedura seguente, è necessario essere un amministratore della sottoscrizione.

Eseguire quindi il comando seguente per creare l'entità servizio. Assegnare un nome, in questo caso **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

L'output sarà un file JSON simile al seguente. Prendere nota dei campi `clientId`, `clientSecret`e `tenantId` , in quanto sono necessari per altri passaggi di questo articolo.

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

Eseguire quindi il comando seguente per ottenere i dettagli sull'entità servizio appena creata, usando il `clientId` valore riportato sopra come input per il `--id` parametro.

```azurecli-interactive
az ad sp show --id your-client-id
```

Di seguito è riportato un esempio semplificato dell'output JSON dal comando. Prendere nota del `objectId` campo, in quanto sarà necessario il relativo valore per il passaggio successivo.

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

Usare quindi il comando seguente per assegnare all'entità servizio l'accesso all'area di lavoro di machine learning. Sono necessari il nome dell'area di lavoro e il nome del gruppo di `-w` risorse `-g` rispettivamente per i parametri e. Per il `--user` parametro, usare il `objectId` valore del passaggio precedente. Il `--role` parametro consente di impostare il ruolo di accesso per l'entità servizio e, in generale, si utilizzerà **proprietario** o **collaboratore**. Entrambi hanno accesso in scrittura a risorse esistenti, ad esempio cluster di calcolo e archivi dati, ma solo il **proprietario** può effettuare il provisioning di queste risorse. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Questa chiamata non produce alcun output, ma ora è stata configurata l'autenticazione dell'entità servizio per l'area di lavoro.

## <a name="authenticate-to-your-workspace"></a>Eseguire l'autenticazione nell'area di lavoro

Ora che l'autenticazione dell'entità servizio è abilitata, è possibile eseguire l'autenticazione all'area di lavoro nell'SDK senza accedere fisicamente come utente. Usare il `ServicePrincipalAuthentication` costruttore della classe e usare i valori ottenuti nei passaggi precedenti come parametri. Il `tenant_id` parametro esegue il `tenantId` mapping a da `service_principal_id` sopra, `clientId`ne esegue `service_principal_password` il mapping `clientSecret`a e ne esegue il mapping a.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

La `sp` variabile ora include un oggetto di autenticazione usato direttamente nell'SDK. In generale, è consigliabile archiviare gli ID e i segreti usati in precedenza nelle variabili di ambiente, come illustrato nel codice seguente.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Per i flussi di lavoro automatizzati eseguiti in Python e usano l'SDK principalmente, è possibile usare questo oggetto così com'è nella maggior parte dei casi per l'autenticazione. Il codice seguente esegue l'autenticazione nell'area di lavoro usando l'oggetto di autenticazione appena creato.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Autenticazione API REST di Azure Machine Learning

L'entità servizio creata nei passaggi precedenti può essere usata anche per l'autenticazione all' [API REST](https://docs.microsoft.com/rest/api/azureml/)di Azure Machine Learning. Si usa il [flusso di concessione delle credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory, che consente le chiamate da servizio a servizio per l'autenticazione non disponibile nei flussi di lavoro automatizzati. Gli esempi sono implementati con la [libreria adal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) in Python e node. js, ma è anche possibile usare qualsiasi libreria open source che supporta OpenID Connect 1,0. 

> [!NOTE]
> MSAL. js è una libreria più recente rispetto a ADAL, ma non è possibile eseguire l'autenticazione da servizio a servizio con le credenziali client con MSAL. js, perché si tratta principalmente di una libreria sul lato client destinata all'autenticazione interattiva/dell'interfaccia utente associata a un utente specifico. Si consiglia di usare ADAL come illustrato di seguito per creare flussi di lavoro automatizzati con l'API REST.

### <a name="nodejs"></a>Node.js

Usare la procedura seguente per generare un token di autenticazione con node. js. Nell'ambiente in uso, `npm install adal-node`eseguire. Quindi, usare `tenantId`, `clientId`e `clientSecret` dall'entità servizio creata nei passaggi precedenti come valori per le variabili corrispondenti nello script seguente.

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

La variabile `tokenResponse` è un oggetto che include il token e i metadati associati, ad esempio la data di scadenza. I token sono validi per 1 ora e possono essere aggiornati eseguendo la stessa chiamata per recuperare un nuovo token. Di seguito è riportata una risposta di esempio.

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

Utilizzare la `accessToken` proprietà per recuperare il token di autenticazione. Per esempi su come usare il token per effettuare chiamate API, vedere la [documentazione dell'API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) .

### <a name="python"></a>Python 

Usare la procedura seguente per generare un token di autenticazione con Python. Nell'ambiente in uso, `pip install adal`eseguire. Quindi, usare `tenantId`, `clientId`e `clientSecret` dall'entità servizio creata nei passaggi precedenti come valori per le variabili appropriate nello script seguente.

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

La variabile `token_response` è un dizionario che include il token e i metadati associati, ad esempio la data di scadenza. I token sono validi per 1 ora e possono essere aggiornati eseguendo la stessa chiamata per recuperare un nuovo token. Di seguito è riportata una risposta di esempio.

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

Usare `token_response["accessToken"]` per recuperare il token di autenticazione. Per esempi su come usare il token per effettuare chiamate API, vedere la [documentazione dell'API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) .

## <a name="web-service-authentication"></a>Autenticazione del servizio Web

I servizi Web in Azure Machine Learning usano un modello di autenticazione diverso rispetto a quello descritto in precedenza. Il modo più semplice per eseguire l'autenticazione ai servizi Web distribuiti consiste nell'usare **l'autenticazione basata su chiave**, che genera chiavi di autenticazione del tipo di portatore statiche che non devono essere aggiornate. Se è necessario autenticarsi solo in un servizio Web distribuito, non è necessario configurare l'autenticazione del principio di servizio come illustrato in precedenza.

I servizi Web distribuiti nel servizio Azure Kubernetes hanno l'autenticazione basata su chiavi *abilitata* per impostazione predefinita. I servizi distribuiti nelle istanze di contenitore di Azure hanno l'autenticazione basata su chiavi *disabilitata* per impostazione predefinita, `auth_enabled=True`ma è possibile abilitarla impostando quando si crea il servizio Web ACI. Di seguito è riportato un esempio di creazione di una configurazione di distribuzione ACI con l'autenticazione basata su chiavi abilitata.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

È quindi possibile usare la configurazione di ACI personalizzata nella distribuzione usando `Model` la classe.

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

Per recuperare le chiavi di autenticazione, `aci_service.get_keys()`usare. Per rigenerare una chiave, usare la `regen_key()` funzione e passare **primaria** o **secondaria**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

I servizi Web supportano anche l'autenticazione basata su token, ma solo per le distribuzioni del servizio Kubernetes di Azure. Per ulteriori informazioni sull'autenticazione, [vedere la pagina relativa all'](how-to-consume-web-service.md) utilizzo dei servizi Web.

### <a name="token-based-web-service-authentication"></a>Autenticazione del servizio Web basata su token

Quando si Abilita l'autenticazione basata su token per un servizio Web, gli utenti devono presentare un token Web JSON Azure Machine Learning al servizio Web per accedervi. Il token scade dopo un intervallo di tempo specificato ed è necessario aggiornarlo per continuare a effettuare chiamate.

* Per impostazione predefinita, l'autenticazione del token è **disabilitata** quando si esegue la distribuzione nel servizio Azure Kubernetes.
* L'autenticazione del token **non è supportata** quando si esegue la distribuzione in istanze di contenitore di Azure.

Per controllare l'autenticazione del token, `token_auth_enabled` usare il parametro quando si crea o si aggiorna una distribuzione.

Se è abilitata l'autenticazione basata su token, `get_token` è possibile usare il metodo per recuperare un token JSON Web (JWT) e l'ora di scadenza del token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> È necessario richiedere un nuovo token dopo l' `refresh_by` ora del token. Se è necessario aggiornare i token all'esterno di Python SDK, un'opzione consiste nell'usare l'API REST con l'autenticazione dell'entità servizio per effettuare periodicamente la `service.get_token()` chiamata, come illustrato in precedenza.
>
> Si consiglia di creare l'area di lavoro Azure Machine Learning nella stessa area del cluster del servizio Azure Kubernetes. 
>
> Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro Azure Machine Learning. Se l'area dell'area di lavoro non è disponibile, non sarà possibile recuperare un token per il servizio Web, anche se il cluster si trova in un'area diversa dall'area di lavoro. Il risultato è che Autenticazione di Azure AD non è disponibile fino a quando l'area dell'area di lavoro non sarà nuovamente disponibile. 
>
> Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, più tempo sarà necessario per recuperare un token.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il [training e distribuire un modello di classificazione delle immagini](tutorial-train-models-with-aml.md).
* [Utilizzare un modello di Azure machine learning distribuito come servizio Web](how-to-consume-web-service.md).
