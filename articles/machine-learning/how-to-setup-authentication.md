---
title: Configurare l'autenticazione
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare e configurare l'autenticazione per varie risorse e flussi di lavoro in Azure Machine Learning.Learn how to set up and configure authentication for various resources and workflows in Azure Machine Learning. Esistono diversi modi per configurare e usare l'autenticazione all'interno del servizio, che vanno dalla semplice autenticazione basata sull'interfaccia utente per scopi di sviluppo o test, all'autenticazione dell'entità servizio di Azure Active Directory completa.
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
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurare l'autenticazione per le risorse e i flussi di lavoro di Azure Machine LearningSet up authentication for Azure Machine Learning resources and workflows
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this article, you learn how to set up and configure authentication for various resources and workflows in Azure Machine Learning. Esistono diversi modi per eseguire l'autenticazione al servizio, che vanno da semplice autenticazione basata sull'interfaccia utente per scopi di sviluppo o test all'autenticazione completa dell'entità servizio di Azure Active Directory.There are multiple ways to authenticate to the service, ranging from simple UI-based auth for development or testing purposes to full Azure Active Directory service principal authentication. Questo articolo illustra anche le differenze nel funzionamento dell'autenticazione del servizio Web, nonché come eseguire l'autenticazione nell'API REST di Azure Machine Learning.This article also explains the differences in how web-service authentication works, as well as how to authenticate to the Azure Machine Learning REST API.

In questa procedura viene illustrato come eseguire le attività seguenti:This how-to shows you how to do the following tasks:

* Usare l'autenticazione interattiva dell'interfaccia utente per il test/sviluppoUse interactive UI authentication for testing/development
* Configurare l'autenticazione dell'entità servizioSet up service principal authentication
* Autenticazione nell'area di lavoro
* Ottenere token di tipo bearer OAuth2.0 per l'API REST di Azure Machine LearningGet OAuth2.0 bearer-type tokens for Azure Machine Learning REST API
* Comprendere l'autenticazione del servizio WebUnderstand web-service authentication

Vedere [l'articolo sul concetto](concept-enterprise-security.md) per una panoramica generale della sicurezza e dell'autenticazione in Azure Machine Learning.See the concept article for a general overview of security and authentication within Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Creare [un'area](how-to-manage-workspace.md)di lavoro di Azure Machine Learning.
* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare Azure Machine Learning SDK oppure usare una [macchina virtuale del blocco appunti](concept-azure-machine-learning-architecture.md#compute-instance) di Azure Machine Learning con l'SDK già installato.

## <a name="interactive-authentication"></a>Autenticazione interattiva

La maggior parte degli esempi nella documentazione di questo servizio usa l'autenticazione interattiva nei blocchi appunti di Jupyter come metodo semplice per il test e la dimostrazione. Questo è un modo leggero per testare ciò che si sta costruendo. Sono disponibili due chiamate di funzione che richiedono automaticamente un flusso di autenticazione basato sull'interfaccia utente.

La `from_config()` chiamata alla funzione emetterà il prompt.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

La `from_config()` funzione cerca un file JSON contenente le informazioni sulla connessione all'area di lavoro. È inoltre possibile specificare i `Workspace` dettagli della connessione in modo esplicito utilizzando il costruttore, che richiederà anche l'autenticazione interattiva. Entrambe le chiamate sono equivalenti.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Se si ha accesso a più tenant, potrebbe essere necessario importare la classe e definire in modo esplicito il tenant di destinazione. La chiamata `InteractiveLoginAuthentication` al costruttore per richiederà inoltre di accedere in modo simile alle chiamate precedenti.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Sebbene sia utile per il test e l'apprendimento, l'autenticazione interattiva non ti aiuterà a creare flussi di lavoro automatizzati o headless. La configurazione dell'autenticazione dell'entità servizio è l'approccio migliore per i processi automatizzati che utilizzano l'SDK.

## <a name="set-up-service-principal-authentication"></a>Configurare l'autenticazione dell'entità servizioSet up service principal authentication

Questo processo è necessario per abilitare l'autenticazione disaccoppiata da un account di accesso utente specifico, che consente di eseguire l'autenticazione all'SDK di Azure Machine Learning Python nei flussi di lavoro automatizzati. L'autenticazione dell'entità servizio consentirà inoltre di [eseguire l'autenticazione nell'API REST.](#azure-machine-learning-rest-api-auth)

Per configurare l'autenticazione dell'entità servizio, è innanzitutto necessario creare una registrazione dell'app in Azure Active Directory e quindi concedere all'app l'accesso basato sui ruoli all'area di lavoro di ML. Il modo più semplice per completare questa configurazione è tramite Azure Cloud Shell nel portale di Azure.The easiest way to complete this setup is through the [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in the Azure portal. Dopo aver effettuato l'accesso `>_` al portale, fare clic sull'icona in alto a destra della pagina accanto al proprio nome per aprire la shell.

Se non è stata usata la shell cloud in precedenza nell'account Azure, sarà necessario creare una risorsa dell'account di archiviazione per archiviare i file scritti. In generale, questo account di archiviazione comporta un costo mensile trascurabile. Inoltre, installare l'estensione di Machine Learning se non è stata usata in precedenza con il comando seguente.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Per eseguire la procedura seguente, è necessario essere un amministratore della sottoscrizione.

Eseguire quindi il comando seguente per creare l'entità servizio. Dagli un nome, in questo caso **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

L'output sarà un JSON simile al seguente. Prendere nota `clientId` `clientSecret`dei `tenantId` campi , e , in quanto sono necessari per altri passaggi descritti in questo articolo.

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

Eseguire quindi il comando seguente per ottenere i dettagli sull'entità servizio appena creata, usando il valore dall'alto `clientId` come input per il `--id` parametro.

```azurecli-interactive
az ad sp show --id your-client-id
```

Di seguito è riportato un esempio semplificato dell'output JSON dal comando. Prendere nota `objectId` del campo, in quanto è necessario il suo valore per il passaggio successivo.

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

Usare quindi il comando seguente per assegnare l'accesso dell'entità servizio all'area di lavoro di Machine Learning.Next, use the following command to assign your service principal access to your machine learning workspace. Sarà necessario il nome dell'area di `-w` lavoro `-g` e il nome del gruppo di risorse per i parametri e, rispettivamente. Per `--user` il parametro, utilizzare il `objectId` valore del passaggio precedente. Il `--role` parametro consente di impostare il ruolo di accesso per l'entità servizio e in generale si utilizzerà **proprietario** o **collaboratore**. Entrambi dispongono dell'accesso in scrittura alle risorse esistenti, ad esempio i cluster di calcolo e gli archivi dati, ma solo il **proprietario** può eseguire il provisioning di queste risorse. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Questa chiamata non produce alcun output, ma è ora impostata l'autenticazione dell'entità servizio per l'area di lavoro.

## <a name="authenticate-to-your-workspace"></a>Eseguire l'autenticazione nell'area di lavoro

Ora che l'autenticazione dell'entità servizio è abilitata, è possibile eseguire l'autenticazione nell'area di lavoro nell'SDK senza accedere fisicamente come utente. Utilizzare `ServicePrincipalAuthentication` il costruttore della classe e utilizzare i valori ottenuti dai passaggi precedenti come parametri. Il `tenant_id` parametro `tenantId` viene `service_principal_id` mappato `clientId`dall'alto, mappato a e `service_principal_password` viene eseguito il mapping a `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

La `sp` variabile contiene ora un oggetto di autenticazione utilizzato direttamente nell'SDK. In generale, è consigliabile archiviare gli ID/segreti usati in precedenza nelle variabili di ambiente, come illustrato nel codice seguente.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Per i flussi di lavoro automatizzati eseguiti in Python e che usano principalmente l'SDK, è possibile usare questo oggetto così com'è nella maggior parte dei casi per l'autenticazione. Il codice seguente esegue l'autenticazione nell'area di lavoro usando l'oggetto auth appena creato.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Autenticazione dell'API REST di Azure Machine LearningAzure Machine Learning REST API auth

L'entità servizio creata nei passaggi precedenti può essere usata anche per l'autenticazione [nell'API REST](https://docs.microsoft.com/rest/api/azureml/)di Azure Machine Learning. Utilizzare il flusso delle [credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)di Azure Active Directory , che consentono le chiamate da servizio a servizio per l'autenticazione headless nei flussi di lavoro automatizzati. Gli esempi vengono implementati con la [libreria ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) in Python e Node.js, ma è anche possibile usare qualsiasi libreria open source che supporta OpenID Connect 1.0. 

> [!NOTE]
> MSAL.js è una libreria più recente di ADAL, ma non è possibile eseguire l'autenticazione da servizio a servizio utilizzando le credenziali client con MSAL.js, poiché si tratta principalmente di una libreria lato client destinata all'autenticazione interattiva/UI associata a un utente specifico. È consigliabile usare ADAL come illustrato di seguito per creare flussi di lavoro automatizzati con l'API REST.

### <a name="nodejs"></a>Node.js

Utilizzare la procedura seguente per generare un token di autenticazione usando Node.js.Use the following steps to generate an auth token using Node.js. Nell'ambiente eseguire `npm install adal-node`. Quindi, utilizzare `tenantId` `clientId`, `clientSecret` e dall'entità servizio creata nei passaggi precedenti come valori per le variabili corrispondenti nello script seguente.

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

La `tokenResponse` variabile è un oggetto che include il token e i metadati associati, ad esempio l'ora di scadenza. I token sono validi per 1 ora e possono essere aggiornati eseguendo nuovamente la stessa chiamata per recuperare un nuovo token. Di seguito è riportata una risposta di esempio.

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

Utilizzare `accessToken` la proprietà per recuperare il token di autenticazione. Vedere la [documentazione dell'API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) per esempi su come usare il token per effettuare chiamate API.

### <a name="python"></a>Python 

Utilizzare la procedura seguente per generare un token di autenticazione usando Python.Use the following steps to generate an auth token using Python. Nell'ambiente eseguire `pip install adal`. Utilizzare quindi `tenantId`, `clientId`e `clientSecret` dall'entità servizio creata nei passaggi precedenti come valori per le variabili appropriate nello script seguente.

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

La `token_response` variabile è un dizionario che include il token e i metadati associati, ad esempio l'ora di scadenza. I token sono validi per 1 ora e possono essere aggiornati eseguendo nuovamente la stessa chiamata per recuperare un nuovo token. Di seguito è riportata una risposta di esempio.

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

Utilizzare `token_response["accessToken"]` per recuperare il token di autenticazione. Vedere la [documentazione dell'API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) per esempi su come usare il token per effettuare chiamate API.

## <a name="web-service-authentication"></a>Autenticazione del servizio Web

I servizi Web in Azure Machine Learning usano un modello di autenticazione diverso da quello descritto in precedenza. Il modo più semplice per eseguire l'autenticazione ai servizi Web distribuiti consiste nell'utilizzare **l'autenticazione basata su chiave**, che genera chiavi di autenticazione statiche di tipo bearer che non è necessario aggiornare. Se è necessario eseguire l'autenticazione solo a un servizio Web distribuito, non è necessario impostare l'autenticazione principale del servizio come illustrato in precedenza.

I servizi Web distribuiti nel servizio Azure Kubernetes hanno l'autenticazione basata su chiave *abilitata* per impostazione predefinita. Le istanze del contenitore di Azure sono disabilitate per `auth_enabled=True`impostazione predefinita l'autenticazione *basata* su chiave, ma è possibile abilitarla impostandola durante la creazione del servizio Web ACI. Di seguito è riportato un esempio di creazione di una configurazione di distribuzione ACI con l'autenticazione basata su chiave abilitata.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

È quindi possibile utilizzare la configurazione ACI personalizzata nella distribuzione usando la `Model` classe .

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

Per recuperare le chiavi `aci_service.get_keys()`di autenticazione, utilizzare . Per rigenerare una `regen_key()` chiave, utilizzare la funzione e passare **Primaria** o **Secondaria**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

I servizi Web supportano anche l'autenticazione basata su token, ma solo per le distribuzioni del servizio Azure Kubernetes.Web-services also support token-based authentication, but only for Azure Kubernetes Service deployments. Per [how-to](how-to-consume-web-service.md) ulteriori informazioni sull'autenticazione, vedere la procedura per l'utilizzo dei servizi Web.

### <a name="token-based-web-service-authentication"></a>Autenticazione del servizio Web basata su tokenToken-based web-service authentication

Quando si abilita l'autenticazione token per un servizio Web, gli utenti devono presentare un token Web JSON di Azure Machine Learning al servizio Web per accedervi. Il token scade dopo un intervallo di tempo specificato e deve essere aggiornato per continuare a effettuare chiamate.

* L'autenticazione basata su token è **disabilitata per impostazione predefinita** quando si distribuisce nel servizio Azure Kubernetes.Token authentication is disabled by default when you deploy to Azure Kubernetes Service.
* L'autenticazione basata su token **non è supportata** quando si distribuisce in istanze del contenitore di Azure.Token authentication isn't supported when you deploy to Azure Container Instances.

Per controllare l'autenticazione del token, usare il `token_auth_enabled` parametro quando si crea o si aggiorna una distribuzione.

Se l'autenticazione token è `get_token` abilitata, è possibile usare il metodo per recuperare un token JSON Web (JWT) e l'ora di scadenza del token:If token authentication is enabled, you can use the method to retrieve a JSON Web Token (JWT) and that token's expiration time:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Dovrai richiedere un nuovo token dopo il `refresh_by` tempo del token. Se è necessario aggiornare i token all'esterno di Python SDK, un'opzione consiste nell'usare l'API REST con l'autenticazione dell'entità servizio per effettuare periodicamente la `service.get_token()` chiamata, come illustrato in precedenza.
>
> È consigliabile creare l'area di lavoro di Azure Machine Learning nella stessa area del cluster del servizio Azure Kubernetes.We strongly recommend that you create your Azure Machine Learning workspace in the same region as your Azure Kubernetes Service cluster. 
>
> Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro di Azure Machine Learning.To authenticate with a token, the web service will make a call to the region in which your Azure Machine Learning workspace is created. Se l'area di lavoro non è disponibile, non sarà possibile recuperare un token per il servizio Web, anche se il cluster si trova in un'area diversa dall'area di lavoro. Il risultato è che l'autenticazione di Azure AD non è disponibile finché l'area dell'area di lavoro non è di nuovo disponibile. 
>
> Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, maggiore sarà il tempo necessario per recuperare un token.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il training e distribuire un modello di [classificazione delle immagini.](tutorial-train-models-with-aml.md)
* [Usare un modello di Azure Machine Learning distribuito come servizio Web.](how-to-consume-web-service.md)
