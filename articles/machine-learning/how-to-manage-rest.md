---
title: Usare REST per gestire le risorse ML
titleSuffix: Azure Machine Learning
description: Come usare le API REST per creare, eseguire ed eliminare le risorse di Azure ML
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b733fbc44deefe46e3496e288ebad525346ef005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322309"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Creazione, esecuzione ed eliminazione di risorse di Azure ML con REST



Esistono diversi modi per gestire le risorse di Azure ML. È possibile usare il [portale](https://portal.azure.com/), l' [interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true)o [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). In alternativa, è possibile scegliere l'API REST. L'API REST usa verbi HTTP in modo standard per creare, recuperare, aggiornare ed eliminare le risorse. L'API REST funziona con qualsiasi linguaggio o strumento in grado di effettuare richieste HTTP. La struttura semplice di REST lo rende spesso una scelta ottimale negli ambienti di scripting e nell'automazione MLOps. 

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Recuperare un token di autorizzazione
> * Creare una richiesta REST formattata correttamente usando l'autenticazione basata su entità servizio
> * Usare le richieste GET per recuperare informazioni sulle risorse gerarchiche di Azure ML
> * Usare le richieste PUT e POST per creare e modificare le risorse
> * Usare le richieste DELETE per pulire le risorse 
> * Usare l'autorizzazione basata su chiavi per assegnare punteggi ai modelli distribuiti

## <a name="prerequisites"></a>Prerequisiti

- Una **sottoscrizione di Azure** per cui si dispone di diritti amministrativi. Se non si dispone di tale sottoscrizione, provare a usare la [sottoscrizione gratuita o a pagamento personale](https://aka.ms/AMLFree)
- [Area di lavoro di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Le richieste REST amministrative usano l'autenticazione basata su entità servizio. Eseguire la procedura descritta in [configurare l'autenticazione per Azure Machine Learning risorse e flussi di lavoro](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) per creare un'entità servizio nell'area di lavoro
- L'utilità **curl** . Il programma **curl** è disponibile nel [sottosistema Windows per Linux](https://aka.ms/wslinstall/) o qualsiasi distribuzione Unix. In PowerShell, **curl** è un alias per **Invoke-WebRequest** e `curl -d "key=val" -X POST uri` diventa `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Recuperare un token di autenticazione dell'entità servizio

Le richieste REST amministrative vengono autenticate con un flusso implicito OAuth2. Questo flusso di autenticazione usa un token fornito dall'entità servizio della sottoscrizione. Per recuperare questo token, è necessario:

- ID tenant (che identifica l'organizzazione a cui appartiene la sottoscrizione)
- ID client (che verrà associato al token creato)
- Il segreto client (che deve essere salvaguardato)

È necessario avere questi valori dalla risposta alla creazione dell'entità servizio. Il recupero di questi valori viene descritto in [configurare l'autenticazione per Azure Machine Learning risorse e flussi di lavoro](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication). Se si usa la sottoscrizione aziendale, è possibile che non si disponga dell'autorizzazione per creare un'entità servizio. In tal caso, è consigliabile usare una [sottoscrizione personale gratuita o a pagamento](https://aka.ms/AMLFree).

Per recuperare un token:

1. Aprire una finestra del terminale
1. Immettere il codice seguente dalla riga di comando
1. Sostituire i valori di `{your-tenant-id}` , `{your-client-id}` e `{your-client-secret}` . In questo articolo le stringhe racchiuse tra parentesi graffe sono variabili che è necessario sostituire con i valori appropriati.
1. Eseguire il comando

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

La risposta deve fornire un token di accesso valido per un'ora:

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

Prendere nota del token, che verrà usato per autenticare tutte le richieste amministrative successive. Questa operazione viene eseguita impostando un'intestazione di autorizzazione in tutte le richieste:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Si noti che il valore inizia con la stringa "Bearer", incluso uno spazio singolo, prima di aggiungere il token.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Ottenere un elenco di gruppi di risorse associati alla sottoscrizione

Per recuperare l'elenco dei gruppi di risorse associati alla sottoscrizione, eseguire:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

In Azure vengono pubblicate molte API REST. Ogni provider di servizi aggiorna l'API in base alla propria cadenza, ma senza interruzioni dei programmi esistenti. Il provider di servizi utilizza l' `api-version` argomento per garantire la compatibilità. L' `api-version` argomento varia da servizio a servizio. Per il servizio Machine Learning, ad esempio, la versione dell'API corrente è `2019-11-01` . Per gli account di archiviazione, è `2019-06-01` . Per gli insiemi di credenziali delle chiavi, è `2019-09-01` . Tutte le chiamate REST devono impostare l' `api-version` argomento sul valore previsto. È possibile basarsi sulla sintassi e sulla semantica della versione specificata, anche quando l'API continua ad evolversi. Se si invia una richiesta a un provider senza l' `api-version` argomento, la risposta conterrà un elenco leggibile dei valori supportati. 

La chiamata precedente comporterà una risposta JSON compattata nel formato seguente: 

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

Per recuperare il set di aree di lavoro in un gruppo di risorse, eseguire il comando seguente, sostituendo `{your-subscription-id}` , `{your-resource-group}` e `{your-access-token}` : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Anche in questo caso, si riceverà un elenco JSON, che contiene un elenco, ogni elemento di cui viene illustrata un'area di lavoro:

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

Per lavorare con le risorse all'interno di un'area di lavoro, passare dal server **Management.Azure.com** generale a un server API REST specifico per il percorso dell'area di lavoro. Prendere nota del valore della `discoveryUrl` chiave nella risposta JSON precedente. Se si ottiene tale URL, si riceverà una risposta simile alla seguente:

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

Il valore della `api` risposta è l'URL del server che verrà utilizzato per richieste aggiuntive. Per elencare gli esperimenti, ad esempio, inviare il comando seguente. Sostituire `regional-api-server` con il valore della `api` risposta (ad esempio, `centralus.api.azureml.ms` ). Sostituire anche `your-subscription-id` , `your-resource-group` , `your-workspace-name` e `your-access-token` come di consueto:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Analogamente, per recuperare i modelli registrati nell'area di lavoro, inviare:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Si noti che per elencare gli esperimenti il percorso inizia con `history/v1.0` while per elencare i modelli, il percorso inizia con `modelmanagement/v1.0` . L'API REST è divisa in diversi gruppi operativi, ognuno con un percorso distinto. 

|Area|Percorso|
|-|-|
|Artifacts|/rest/api/azureml|
|Archivi dati|/azure/machine-learning/how-to-access-data|
|Ottimizzazione degli iperparametri|iperguida/v 1.0/|
|Modelli|Modelmanagement/v 1.0/|
|Cronologia di esecuzione|esecuzione/v 1.0/e cronologia/v 1.0/|

È possibile esplorare l'API REST usando il modello generale di:

|Componente URL|Esempio|
|-|-|
| https://| |
| regione-API-server/ | centralus.api.azureml.ms/ |
| operazioni-percorso/ | cronologia/v 1.0/ |
| sottoscrizioni/{your-Subscription-ID}/ | sottoscrizioni/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{your-Resource-Group}/ | resourceGroups/MyResourceGroup/ |
| Providers/Operation-provider/ | Providers/Microsoft. MachineLearningServices/ |
| Provider-Resource-Path/ | aree di lavoro/MLWorkspace/area di lavoro/FirstExperiment/esecuzioni/1/ |
| operazioni-endpoint/ | artefatti/metadati/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Creazione e modifica di risorse tramite richieste PUT e POST

Oltre al recupero delle risorse con il verbo GET, l'API REST supporta la creazione di tutte le risorse necessarie per il training, la distribuzione e il monitoraggio delle soluzioni ML. 

Il training e l'esecuzione di modelli ML richiedono risorse di calcolo. È possibile elencare le risorse di calcolo di un'area di lavoro con: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Per creare o sovrascrivere una risorsa di calcolo denominata, si userà una richiesta PUT. Nell'esempio seguente vengono aggiunte le sostituzioni note di `your-subscription-id` ,, `your-resource-group` `your-workspace-name` e `your-access-token` , sostituendo `your-compute-name` e i valori per `location` , `vmSize` , `vmPriority` , `scaleSettings` , `adminUserName` e `adminUserPassword` . Come specificato nella Guida di riferimento a [ambiente di calcolo di Machine Learning-create o Update SDK Reference](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), il comando seguente crea una Standard_D1 dedicata a nodo singolo (una risorsa di calcolo di base della CPU) che verrà ridotta dopo 30 minuti:

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
> Nei terminali di Windows potrebbe essere necessario eseguire l'escape dei simboli con virgolette doppie quando si inviano dati JSON. Ovvero, il testo come `"location"` diventa `\"location\"` . 

Una richiesta con esito positivo riceverà una `201 Created` risposta, ma si noti che questa risposta indica semplicemente che il processo di provisioning è iniziato. È necessario eseguire il polling (o usare il portale) per confermare il corretto completamento.

### <a name="create-an-experimental-run"></a>Creare un'esecuzione sperimentale

Per avviare un'esecuzione all'interno di un esperimento, è necessaria una cartella zip contenente lo script di training e i file correlati e un file JSON di definizione di esecuzione. La cartella zip deve contenere il file di voce Python nella directory radice. Ad esempio, è possibile comprimere un semplice programma Python come il seguente in una cartella denominata **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Salvare il frammento di codice seguente come **definition.js**. Confermare che il valore "script" corrisponda al nome del file Python appena compresso. Verificare che il valore di destinazione corrisponda al nome di una risorsa di calcolo disponibile. 

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

Inserire i file nel server usando il `multipart/form-data` contenuto:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Una richiesta POST eseguita correttamente genererà uno `200 OK` stato, con un corpo della risposta contenente l'identificatore dell'esecuzione creata:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

È possibile monitorare un'esecuzione usando il modello REST-ful che ora dovrebbe essere familiare:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Elimina le risorse che non sono più necessarie

Alcune risorse, ma non tutte, supportano il verbo DELETE. Controllare il [riferimento all'API](https://docs.microsoft.com/rest/api/azureml/) prima di eseguire il commit nell'API REST per i casi d'uso dell'eliminazione. Per eliminare un modello, ad esempio, è possibile usare:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Usare REST per assegnare un punteggio a un modello distribuito

Sebbene sia possibile distribuire un modello in modo che esegua l'autenticazione con un'entità servizio, la maggior parte delle distribuzioni per il client usa l'autenticazione basata su chiavi. È possibile trovare la chiave appropriata nella pagina della distribuzione all'interno della scheda **endpoint** di studio. Lo stesso percorso indicherà l'URI di assegnazione dei punteggi dell'endpoint. Gli input del modello devono essere modellati come matrice JSON denominata `data` :

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Creare un'area di lavoro con REST 

Ogni area di lavoro di Azure ML presenta una dipendenza da quattro altre risorse di Azure: un registro contenitori con l'amministrazione abilitata, un insieme di credenziali delle chiavi, una risorsa Application Insights e un account di archiviazione. Non è possibile creare un'area di lavoro fino a quando non sono presenti risorse. Per informazioni dettagliate sulla creazione di ogni risorsa, vedere il riferimento all'API REST.

Per creare un'area di lavoro, inserire una chiamata simile alla seguente a `management.azure.com` . Sebbene questa chiamata richieda di impostare un numero elevato di variabili, è strutturalmente identica ad altre chiamate illustrate in questo articolo. 

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

Si dovrebbe ricevere una `202 Accepted` risposta e, nelle intestazioni restituite, un `Location` URI. È possibile ottenere questo URI per informazioni sulla distribuzione, incluse informazioni di debug utili se si verifica un problema con una delle risorse dipendenti, ad esempio se si dimentica di abilitare l'accesso amministrativo nel registro contenitori. 

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Spostamento dell'area di lavoro

> [!WARNING]
> Lo spostamento dell’area di lavoro di Azure Machine Learning in una diversa sottoscrizione o della sottoscrizione proprietaria su un nuovo tenant non è supportato in quanto ciò può provocare errori.

### <a name="deleting-the-azure-container-registry"></a>Eliminazione del Registro Azure Container

L'area di lavoro di Azure Machine Learning usa il Registro Azure Container (ACR) per alcune operazioni. Verrà creata automaticamente un'istanza di ACR quando è necessaria per la prima volta.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passaggi successivi

- Esplorare il [riferimento all'API REST AzureML](https://docs.microsoft.com/rest/api/azureml/)completo.
- Informazioni su come usare la finestra di progettazione per [stimare il prezzo delle automobili con la finestra di progettazione](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Esplora [Azure Machine Learning con Jupyter notebook](https://docs.microsoft.com/azure//machine-learning/samples-notebooks).
