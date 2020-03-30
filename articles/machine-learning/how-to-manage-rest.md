---
title: Usare REST per gestire le risorse di MLUse REST to manage ML resources
titleSuffix: Azure Machine Learning
description: Come usare le API REST per creare, eseguire ed eliminare risorse di Azure MLHow to use REST APIs to create, run, and delete Azure ML resources
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580629"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Creare, eseguire ed eliminare risorse di Azure ML usando RESTCreate, run, and delete Azure ML resources using REST

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Esistono diversi modi per gestire le risorse di Azure ML. È possibile utilizzare [portal](https://portal.azure.com/), [command-line interface](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)o [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). In alternativa, è possibile scegliere l'API REST. L'API REST usa i verbi HTTP in modo standard per creare, recuperare, aggiornare ed eliminare risorse. L'API REST funziona con qualsiasi linguaggio o strumento in grado di effettuare richieste HTTP. La struttura lineare di REST spesso lo rende una buona scelta negli ambienti di scripting e per l'automazione di MLOps. 

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Recuperare un token di autorizzazioneRetrieve an authorization token
> * Creare una richiesta REST formattata correttamente usando l'autenticazione dell'entità servizioCreate a properly-formatted REST request using service principal authentication
> * Usare le richieste GET per recuperare informazioni sulle risorse gerarchiche di Azure MLUse GET requests to retrieve information about Azure ML's hierarchical resources
> * Usare le richieste PUT e POST per creare e modificare le risorseUse PUT and POST requests to create and modify resources
> * Usare le richieste DELETE per pulire le risorseUse DELETE requests to clean up resources 
> * Usare l'autorizzazione basata su chiave per assegnare un punteggio ai modelli distribuitiUse key-based authorization to score deployed models

## <a name="prerequisites"></a>Prerequisiti

- Una **sottoscrizione di Azure** per la quale si dispone di diritti amministrativi. Se non si dispone di un abbonamento di questo tipo, provare [l'abbonamento personale gratuito o a pagamento](https://aka.ms/AMLFree)
- Area di lavoro di [Azure Machine LearningAn Azure Machine Learning Workspace](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Le richieste REST amministrative usano l'autenticazione dell'entità servizio. Seguire i passaggi descritti in [Configurare l'autenticazione per](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) le risorse e i flussi di lavoro di Azure Machine Learning per creare un'entità servizio nell'area di lavoroFollow the steps in Set up authentication for Azure Machine Learning resources and workflows to create a service principal in your workspace
- L'utilità **curl.** Il programma **di ricciolo** è disponibile nel [sottosistema Windows per Linux](https://aka.ms/wslinstall/) o qualsiasi distribuzione UNIX. In PowerShell, **curl** è un alias `curl -d "key=val" -X POST uri` per `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` **Invoke-WebRequest** e diventa . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Recuperare un token di autenticazione dell'entità servizioRetrieve a service principal authentication token

Le richieste REST amministrative vengono autenticate con un flusso implicito OAuth2.Administrative REST requests are authenticated with an OAuth2 implicit flow. Questo flusso di autenticazione usa un token fornito dall'entità servizio della sottoscrizione. Per recuperare questo token, è necessario:To retrieve this token, you'll need:

- ID tenant (identificante dell'organizzazione a cui appartiene la sottoscrizione)
- L'ID client (che verrà associato al token creato)
- Il tuo segreto cliente (che dovresti salvaguardare)

È necessario disporre di questi valori dalla risposta alla creazione dell'entità servizio come descritto in [Configurare l'autenticazione per](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)le risorse e i flussi di lavoro di Azure Machine Learning. Se si usa la sottoscrizione aziendale, è possibile che non si disponga dell'autorizzazione per creare un'entità servizio. In tal caso, è necessario utilizzare un [abbonamento personale gratuito o a pagamento](https://aka.ms/AMLFree).

Per recuperare un token:

1. Aprire una finestra del terminale
1. Immettere il codice seguente nella riga di comando
1. Sostituire i propri `{your-tenant-id}` `{your-client-id}`valori `{your-client-secret}`con , , e . In questo articolo, le stringhe racchiuse tra parentesi graffe sono variabili che dovrai sostituire con i tuoi valori appropriati.
1. Eseguire il comando

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

La risposta deve fornire un token di accesso valido per un'ora:The response should provide an access token good for one hour:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Prendere nota del token, poiché verrà utilizzato per autenticare tutte le richieste amministrative successive. A tale scopo, impostare un'intestazione Authorization in tutte le richieste:You'll do so by setting an Authorization header in all requests:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Si noti che il valore inizia con la stringa "Bearer" che include un singolo spazio prima di aggiungere il token.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Ottenere un elenco dei gruppi di risorse associati alla sottoscrizioneGet a list of resource groups associated with your subscription

Per recuperare l'elenco dei gruppi di risorse associati alla sottoscrizione, eseguire:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

In Azure, vengono pubblicate molte API REST. Ogni provider di servizi aggiorna l'API in base alla propria cadenza, ma lo fa senza interrompere i programmi esistenti. Il provider di `api-version` servizi utilizza l'argomento per garantire la compatibilità. L'argomento `api-version` varia da servizio a servizio. Per il servizio Machine Learning, ad esempio, la versione corrente dell'API è `2019-11-01`. Per gli account di `2019-06-01`archiviazione, è . Per gli insiemi `2019-09-01`di credenziali delle chiavi, è . Tutte le chiamate `api-version` REST devono impostare l'argomento sul valore previsto. È possibile fare affidamento sulla sintassi e la semantica della versione specificata anche se l'API continua a evolversi. Se si invia una richiesta `api-version` a un provider senza l'argomento, la risposta conterrà un elenco leggibile di valori supportati. 

La chiamata precedente darà come risultato una risposta JSON compattata nel formato: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Eseguire il drill-down nelle aree di lavoro e nelle relative risorse

Per recuperare il set di aree di lavoro in un `{your-subscription-id}`gruppo `{your-resource-group}`di `{your-access-token}`risorse, eseguire le operazioni seguenti, sostituendo , e : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Anche in questo caso si riceverà un elenco JSON, questa volta contenente un elenco, ogni elemento di cui dettagli un'area di lavoro:Anche in questo caso si riceverà un elenco JSON, questa volta contenente un elenco, ogni elemento di cui vengono descritti in dettaglio un'area di lavoro:Again you'll receive a JSON list, this time

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Per usare le risorse all'interno di un'area di lavoro, è necessario passare dal server **di management.azure.com** generale a un server API REST specifico per la posizione dell'area di lavoro. Prendere nota `discoveryUrl` del valore della chiave nella risposta JSON precedente. Se ottieni quell'URL, riceverai una risposta simile al:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Il valore `api` della risposta è l'URL del server che verrà utilizzato per le richieste aggiuntive. Per elencare gli esperimenti, ad esempio, inviare il comando seguente. Sostituire `regional-api-server` con il `api` valore della risposta `centralus.api.azureml.ms`(ad esempio, ). Sostituire `your-subscription-id`anche `your-resource-group` `your-workspace-name`, `your-access-token` , e come di consueto:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Analogamente, per recuperare i modelli registrati nell'area di lavoro, inviare:Similarly, to retrieve registered models in your workspace, send:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Si noti che per elencare gli esperimenti il percorso inizia con `history/v1.0` per elencare i modelli, il percorso inizia con `modelmanagement/v1.0`. L'API REST è suddivisa in diversi gruppi operativi, ognuno con un percorso distinto. I documenti di riferimento API nei collegamenti seguenti elencano le operazioni, i parametri e i codici di risposta per le varie operazioni.

|Area|Path|Riferimento|
|-|-|-|
|Elementi|artefatto/v2.0/|[Guida di riferimento all'API REST](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Archivi dati|datastore/v1.0/|[Guida di riferimento all'API REST](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Ottimizzazione degli iperparametri|hyperdrive/v1.0/|[Guida di riferimento all'API REST](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modelli|modelmanagement/v1.0/|[Guida di riferimento all'API REST](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Cronologia di esecuzione|esecuzione/v1.0/ e cronologia/v1.0/|[Guida di riferimento all'API REST](https://docs.microsoft.com/rest/api/azureml/runs)|

È possibile esplorare l'API REST usando il modello generale di:You can explore the REST API using the general pattern of:

|Componente URL|Esempio|
|-|-|
| https://| |
| regional-api-server/ | centralus.api.azureml.ms/ |
| operazione-percorso/ | history/v1.0/ |
| abbonamenti/ID-sottoscrizione// | abbonamenti/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/-your-resource-group// | resourceGroups/GruppoRisorse/ |
| fornitori/fornitori di operazioni/ | provider/Microsoft.MachineLearningServices/ |
| provider-percorso-risorsa/ | workspaces/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operazioni-endpoint/ | artefatti/metadati/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Creare e modificare risorse utilizzando le richieste PUT e POST

Oltre al recupero delle risorse con il verbo GET, l'API REST supporta la creazione di tutte le risorse necessarie per eseguire il training, distribuire e monitorare le soluzioni ML. 

La formazione e l'esecuzione di modelli ML richiedono risorse di calcolo. È possibile elencare le risorse di calcolo di un'area di lavoro con:You can list the compute resources of a workspace with: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Per creare o sovrascrivere una risorsa di calcolo denominata, si userà una richiesta PUT. Di `your-subscription-id`seguito, oltre alle ormai note sostituzioni di , `your-resource-group`, `your-workspace-name`e `your-access-token`, `vmPriority`sostituire `scaleSettings` `your-compute-name` `adminUserName`e `adminUserPassword`valori per `location`, `vmSize`, , , e . Come specificato nel riferimento in [Machine Learning Compute - Create or Update SDK Reference](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), il comando seguente crea un Standard_D1 dedicato a nodo singolo (una risorsa di calcolo cpu di base) che verrà ridimensionata dopo 30 minuti:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> Nei terminali windows potrebbe essere necessario eseguire l'escape dei simboli delle virgolette doppie durante l'invio di dati JSON. Ovvero, il testo `"location"` `\"location\"`diventa . 

Una richiesta riuscita `201 Created` otterrà una risposta, ma si noti che questa risposta significa semplicemente che il processo di provisioning è iniziato. Dovrai eseguire il polling (o utilizzare il portale) per confermarne il completamento.

### <a name="create-an-experimental-run"></a>Creare un'esecuzione sperimentaleCreate an experimental run

Per avviare un'esecuzione all'interno di un esperimento, è necessaria una cartella zip contenente lo script di training e i file correlati e un file JSON di definizione dell'esecuzione. La cartella zip deve avere il file di voce Python nella directory radice. Ad esempio, comprimere un programma Python banale come il seguente in una cartella denominata **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Salvare il frammento successivo come **definition.json**. Verificare che il valore "Script" corrisponda al nome del file Python appena compresso. Verificare che il valore "Target" corrisponda al nome di una risorsa di calcolo disponibile. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Pubblicare questi file `multipart/form-data` nel server utilizzando il contenuto:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Una richiesta POST riuscita genererà uno `200 OK` stato, con un corpo di risposta contenente l'identificatore dell'esecuzione creata:A successful POST request will generate a status, with a response body containing the identifier of the created run:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

È possibile monitorare un'esecuzione usando il modello di tipo REST che dovrebbe essere familiare:You can monitor a run using the REST-ful pattern that should now be familiar:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Eliminare le risorse non più necessarie

Alcune risorse, ma non tutte, supportano il verbo DELETE. Controllare la [Guida di riferimento all'API](https://docs.microsoft.com/rest/api/azureml/) prima di eseguire il commit nell'API REST per l'eliminazione dei casi d'uso. Per eliminare un modello, ad esempio, è possibile utilizzare:To delete a model, for instance, you can use:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Usare REST per assegnare un punteggio a un modello distribuitoUse REST to score a deployed model

Sebbene sia possibile distribuire un modello in modo che esegua l'autenticazione con un'entità servizio, la maggior parte delle distribuzioni con connessione client usa l'autenticazione basata su chiave. È possibile trovare la chiave appropriata nella pagina della distribuzione all'interno della scheda Endpoint di Studio.You can find the appropriate key in your deployment's page within the **Endpoints** tab of Studio. Nella stessa posizione verrà visualizzato l'URI di punteggio dell'endpoint. Gli input del modello devono essere modellati `data`come una matrice JSON denominata:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Creare un'area di lavoro usando RESTCreate a workspace using REST 

Ogni area di lavoro di Azure ML ha una dipendenza da altre quattro risorse di Azure: un registro contenitori con l'amministrazione abilitata, un insieme di credenziali delle chiavi, una risorsa di Application Insights e un account di archiviazione. Non è possibile creare un'area di lavoro finché queste risorse non esistono. Consultare le informazioni di riferimento sull'API REST per informazioni dettagliate sulla creazione di ogni risorsa di questo tipo.

Per creare un'area di lavoro, `management.azure.com`inserire una chiamata simile alla seguente a . Anche se questa chiamata richiede di impostare un numero elevato di variabili, è strutturalmente identico ad altre chiamate discusse in questo articolo. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Si dovrebbe `202 Accepted` ricevere una risposta e, nelle `Location` intestazioni restituite, un URI. È possibile ottenere questo URI per informazioni sulla distribuzione, incluse utili informazioni di debug se si verifica un problema con una delle risorse dipendenti (ad esempio, se si è dimenticato di abilitare l'accesso di amministratore nel Registro di sistema del contenitore). 

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Spostamento dell'area di lavoro

> [!WARNING]
> Lo spostamento dell'area di lavoro di Azure Machine Learning in una sottoscrizione diversa o lo spostamento della sottoscrizione proprietaria in un nuovo tenant non sono supportati. Questa operazione può causare errori.

### <a name="deleting-the-azure-container-registry"></a>Eliminazione del Registro di sistema del contenitore di AzureDeleting the Azure Container Registry

L'area di lavoro di Azure Machine Learning usa il Registro di sistema del contenitore di Azure (ACR) per alcune operazioni. Verrà creata automaticamente un'istanza ACR quando ne ha bisogno per la prima volta.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passaggi successivi

- Esplorare il riferimento completo all'API REST di AzureML.Explore the complete [AzureML REST API reference](https://docs.microsoft.com/rest/api/azureml/).
- Scopri come utilizzare Studio & Designer per [prevedere il prezzo dell'automobile con il progettista (anteprima).](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)
- Esplora [Azure Machine Learning con blocchi appunti Jupyter.](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)
