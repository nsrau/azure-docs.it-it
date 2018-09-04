---
title: 'Aggiornamenti dello schema in anteprima del 1° agosto 2015: App per la logica di Azure | Microsoft Docs'
description: Anteprima del 1° agosto 2015 della versione aggiornata dello schema per le definizioni di App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: stepsic-microsoft-com
ms.author: stepsic
ms.reviewer: klam, estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: dd05543c2a727f010432ecb54c2dc3e77a245de4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122778"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aggiornamenti dello schema per App per la logica di Azure: anteprima del 1° agosto 2015

Questo schema e la versione dell'API per App per la logica di Azure includono importanti miglioramenti che rendono le app per la logica più affidabili e più facili da usare:

* Il tipo di azione **APIApp** è ora denominato [**APIConnection**](#api-connections).
* L'azione **Repeat** è ora denominata [ **Foreach**](#foreach).
* L'[app per le API **Listener HTTP**](#http-listener) non è più necessaria.
* La chiamata a flussi di lavoro figlio usa un [nuovo schema](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Passare alle connessioni API

Il cambiamento più importante riguarda il fatto che non è più necessario distribuire app per le API nella sottoscrizione di Azure per poter usare le API. Ecco in che modo è possibile usare le API:

* API gestite
* API Web personalizzate

Ciascun metodo viene gestito in modo leggermente diverso perché prevede modelli di gestione e hosting diversi. Un vantaggio di questo modello è che non si è più vincolati a risorse che vengono distribuite nel gruppo di risorse di Azure. 

### <a name="managed-apis"></a>API gestite

Microsoft gestisce alcune API per conto dell'utente, ad esempio Office 365, Salesforce, Twitter e FTP. È possibile usare alcune API gestite così come sono, ad esempio Bing Translate, mentre altre richiedono una configurazione, denominata anche *connection*.

Quando si usa Office 365, ad esempio, è necessario creare una connessione che include il token di accesso a Office 365. Il token è archiviato in modo sicuro e aggiornato in modo che l'app per la logica possa sempre chiamare l'API di Office 365. Per connettersi al server SQL o FTP è necessario creare una connessione che includa la stringa di connessione. 

In questa definizione, queste azioni sono denominate `APIConnection`. Di seguito è riportato un esempio di connessione che chiama Office 365 per inviare un messaggio di posta elettronica:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

L'oggetto `host` è la parte di input univoca per le connessioni API e contiene queste parti: `api` e `connection`. L'oggetto `api` specifica l'URL di runtime per la posizione in cui è ospitata l'API gestita. Per visualizzare tutte le API gestite disponibili, è possibile chiamare `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Quando si usa un'API, questa può presentare o meno *parametri di connessione* definiti. Se l'API non definisce questi parametri, non è richiesta alcuna connessione. Se l'API definisce questi parametri, è necessario creare una connessione con un nome specificato.  
Si fa quindi riferimento al nome nell'oggetto `connection` all'interno dell'oggetto `host`. Per creare una connessione in un gruppo di risorse, chiamare questo metodo:

```
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Con il corpo seguente:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Distribuire API gestite in un modello di Azure Resource Manager

È possibile creare un'app completa in un modello di Azure Resource Manager, purché non sia necessario l'accesso interattivo.
Se è necessario l'accesso, è possibile impostare tutto con il modello di Azure Resource Manager, ma sarà comunque necessario visitare il portale di Azure per autorizzare le connessioni. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

Come si vede in questo esempio, le connessioni non sono altro che risorse presenti nel gruppo di risorse. Fanno riferimento alle API gestite disponibili nella sottoscrizione dell'utente.

### <a name="your-custom-web-apis"></a>API Web personalizzate

Se si usano API personalizzate, non quelle gestite da Microsoft, usare l'azione **HTTP** predefinita per chiamarle. Per un'esperienza ideale, si consiglia di esporre un endpoint swagger per l'API. Questo endpoint consente alla finestra di progettazione di app per la logica di eseguire il rendering degli input e degli output per l'API. Senza Swagger, la finestra di progettazione può mostrare gli input e gli output solo come oggetti JSON opachi.

Di seguito è riportato un esempio che mostra la nuova proprietà `metadata.apiDefinitionUrl` :

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Se l'API Web è ospitata in Servizio app di Azure, verrà visualizzata automaticamente nell'elenco di azioni disponibili nella finestra di progettazione. In caso contrario, è necessario incollare direttamente l'URL. Per poter essere usato nella finestra di progettazione di app per la logica, l'endpoint Swagger deve essere non autenticato, anche se è possibile proteggere l'API stessa con qualsiasi metodo supportato da Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Chiamare le app per le API già distribuite con 2015-08-01-preview

Se in precedenza è stata distribuita un'app per le API, è possibile chiamare l'app usando l'azione **HTTP**.
Ad esempio, se si usa Dropbox per elencare i file, la definizione della versione dello schema **2014-12-01-preview** può essere simile a quanto segue:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

A questo punto è possibile costruire l'azione HTTP equivalente come nell'esempio seguente, mentre la sezione parameters della definizione dell'app per la logica rimane invariata:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

La tabella seguente illustra le singole proprietà:

| Proprietà dell'azione | DESCRIZIONE |
| --- | --- |
| `type` | `Http` anziché `APIapp` |
| `metadata.apiDefinitionUrl` | Per usare questa azione nella finestra di progettazione di app per la logica, includere l'endpoint dei metadati, costituito da: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Costituito da: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre `POST` |
| `inputs.body` | Identica ai parametri dell'app per le API |
| `inputs.authentication` | Identica all'autenticazione dell'app per le API |

Questo approccio dovrebbe funzionare per tutte le azioni delle app per le API. Tenere tuttavia presente che queste app per le api precedenti non sono più supportate. È quindi consigliabile passare a una delle altre due opzioni precedenti, un'API gestita o l'hosting dell'API Web personalizzata.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>"Repeat" rinominato come "foreach"

In base ai commenti e suggerimenti ricevuti dai clienti per la versione dello schema precedente, il nome dell'azione **Repeat** generava confusione e non era chiaramente identificabile come ciclo **Foreach**. L'azione `repeat` è stata rinominata `foreach`. In precedenza questa azione sarebbe stata scritta come nell'esempio seguente:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Ora si scrive questa versione:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

Inoltre, la funzione `repeatItem()`, che referenziava all'elemento che il ciclo sta elaborando durante l'iterazione corrente, è stata ora rinominata `item()`. 

### <a name="reference-outputs-from-foreach"></a>Fare riferimento agli output da "foreach"

Per maggiore semplicità, non viene più eseguito il wrapping degli output dalle azioni `foreach` in un oggetto denominato `repeatItems`. Inoltre, con queste modifiche, le funzioni `repeatItem()`, `repeatBody()` e `repeatOutputs()` sono state rimosse.

Riprendendo l'esempio `repeat` precedente, si ottengono questi output:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

A questo punto vengono visualizzati i seguenti output:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

In precedenza per ottenere `body` dall'azione quando si faceva riferimento a questi output, era necessario eseguire:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Ora è possibile usare questa versione:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Listener HTTP nativo

Le funzionalità del listener HTTP ora sono predefinite. Non è quindi più necessario distribuire un'app per le API del listener HTTP. Per informazioni dettagliate, vedere [App per la logica come endpoint che è possibile chiamare](../logic-apps/logic-apps-http-endpoint.md). 

Con queste modifiche, è stata rimossa la funzione `@accessKeys()`, che è stata sostituita con la funzione `@listCallbackURL()` per ottenere l'endpoint, se necessario. Ora è anche necessario definire almeno un trigger nell'app per la logica. Per eseguire il comando `/run` sul flusso di lavoro, è necessario avere un trigger `manual`, `apiConnectionWebhook` o `httpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Chiamare flussi di lavoro figlio

In precedenza, per la chiamata a flussi di lavoro figlio era necessario passare al flusso di lavoro, ottenere il token di accesso e incollare il token nella definizione dell'app per la logica in cui si vuole chiamare tale flusso di lavoro figlio. Con il nuovo schema, il motore di app per la logica genera automaticamente una firma di accesso condiviso in fase di esecuzione per il flusso di lavoro figlio, quindi non è necessario incollare segreti nella definizione. Di seguito è fornito un esempio: 

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Un secondo miglioramento è l'accesso completo alla richiesta in ingresso per i flussi di lavoro figlio. Ciò significa che è possibile passare parametri nella sezione *queries* e nell'oggetto *headers* e che è possibile definire completamente l'intero corpo.

Infine, è stato necessario apportare modifiche al flusso di lavoro figlio. Mentre in precedenza era possibile chiamare direttamente un flusso di lavoro figlio, ora occorre definire un endpoint trigger nel flusso di lavoro che l'elemento padre possa chiamare. In genere, si aggiungerà un trigger con il tipo `manual` da usare nella definizione dell'elemento padre. Si noti che la proprietà `host` in particolare include un `triggerName`, perché è sempre necessario specificare quale trigger viene richiamato.

## <a name="other-changes"></a>Altre modifiche

### <a name="new-queries-property"></a>Nuova proprietà "queries"

Tutti i tipi di azione ora supportano un nuovo input denominato `queries`. Questo input può essere un oggetto strutturato e non è più necessario assemblare la stringa manualmente.

### <a name="renamed-parse-function-to-json"></a>Funzione "parse()" rinominata come "json()"

Verranno aggiunti a breve altri tipi di contenuti, quindi la funzione `parse()` è stata rinominata `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Presto disponibile: API Enterprise Integration

Non sono ancora disponibili versioni gestite delle API Enterprise Integration, ad esempio AS2. Per il momento è possibile usare le API di BizTalk distribuite tramite l'azione HTTP. Per informazioni dettagliate, vedere "Using your already deployed API apps" (Uso delle app per le API già distribuite) nella [guida di orientamento all'integrazione](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
