---
title: Implementare CI/CD per Analisi di flusso di Azure usando le API REST
description: Informazioni su come implementare una pipeline di integrazione e distribuzione continua per Analisi di flusso di Azure usando le API REST.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: f22a4c4d0159efa2ac42d15ff6afdadc2d6755d0
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215049"
---
# <a name="implement-cicd-for-stream-analytics-using-apis"></a>Implementare CI/CD per Analisi di flusso usando le API

È possibile abilitare l'integrazione e la distribuzione continua per i processi di Analisi di flusso di Azure usando le API REST. Questo articolo fornisce esempi sulle API da usare e come usarle. Le API REST non sono supportate in Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Chiamare le API da ambienti diversi

Le API REST possono essere chiamate sia da Linux che da Windows. I comandi seguenti dimostrano la sintassi corretta per la chiamata API. L'uso specifico delle API verrà descritto nelle sezioni successive di questo articolo.

### <a name="linux"></a>Linux

Per Linux è possibile usare i comandi `Curl` o `Wget`:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a> Windows

Per Windows usare Powershell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url>-Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Creare un processo ASA in Edge 
 
Per creare un processo di Analisi di flusso, chiamare il metodo PUT usando l'API di Analisi di flusso.

|Metodo|URL richiesta|
|------|-----------|
|PUT|https://management.azure.com/subscriptions/{**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview|
 
Esempio di comando con **curl**:

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Esempio di corpo della richiesta in JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Per altre informazioni, vedere la [documentazione delle API](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Pubblicare il pacchetto Edge 
 
Per pubblicare un processo di Analisi di flusso in IoT Edge, chiamare il metodo POST usando l'API di pubblicazione di pacchetti Edge.

|Metodo|URL richiesta|
|------|-----------|
|POST|https://management.azure.com/subscriptions/{**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview|

Questa operazione asincrona restituisce uno stato di 202 finché il processo non è stato pubblicato correttamente. L'intestazione della risposta di posizione contiene l'URI usato per ottenere lo stato del processo. Mentre il processo è in esecuzione, una chiamata all'URI nell'intestazione della posizione restituisce uno stato di 202. Al termine del processo, l'URI nell'intestazione della posizione restituisce uno stato di 200. 

Esempio di una chiamata di pubblicazione del pacchetto Edge con **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Dopo aver effettuato la chiamata POST, è prevedibile una risposta con un corpo vuoto. Cercare l'URL che si trova nell'intestazione della risposta e registralo per un ulteriore uso.
 
Esempio di URL dell'intestazione della risposta:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Attendere uno o due minuti prima di eseguire questo comando per effettuare una chiamata API con l'URL trovato nell'intestazione della risposta. Riprovare il comando se non si ottiene una risposta 200.
 
Esempio di chiamata API con URL restituito con **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

La risposta include le informazioni che è necessario aggiungere allo script di distribuzione di Edge. Gli esempi seguenti mostrano quali informazioni è necessario raccogliere e dove aggiungerle nel manifesto della distribuzione.
 
Esempio di corpo della risposta dopo la pubblicazione:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}",”PublishTimeStamp”:”{publishtimestamp}”}}}}" 
  status : "Succeeded" 
} 
```

Esempio di manifesto della distribuzione: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Dopo la configurazione del manifesto della distribuzione, vedere [Distribuire i moduli di Azure IoT Edge con l'interfaccia della riga di comando di Azure](../iot-edge/how-to-deploy-modules-cli.md) per la distribuzione.


## <a name="next-steps"></a>Passaggi successivi 
 
* [Analisi di flusso di Azure in IoT Edge](stream-analytics-edge.md)
* [Esercitazione su Analisi di flusso di Azure in IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Sviluppare processi Edge di Analisi di flusso usando gli strumenti di Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
