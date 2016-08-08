<properties 
	pageTitle="Nuova versione dello schema 2015-08-01-preview" 
	description="Informazioni su come scrivere la definizione JSON per l'ultima versione di App per la logica" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="stepsic"/>
	
# Nuova versione dello schema 2015-08-01-preview

La nuova versione dello schema e dell'API per App per la logica include una serie di miglioramenti relativi in particolare all'affidabilità e alla semplicità d'uso di App per la logica. Le differenze principali sono quattro:

1. Il tipo di azione **APIApp** è stato aggiornato e sostituito con un nuovo tipo di azione **APIConnection**.
2. **Repeat** è stato rinominato come **Foreach**.
3. L'app per le API **Listener HTTP** non è più necessaria.
4. La chiamata a flussi di lavoro figlio usa un nuovo schema.

## 1\. Passaggio alle connessioni API

Il cambiamento più importante riguarda il fatto che non è più necessario distribuire app per le API nella sottoscrizione di Azure per usare le API. Esistono 2 modi in cui è possibile usare le API:
* API gestite
* API Web personalizzate

Ciascuno di questi metodi viene gestito in modo leggermente diverso perché prevede modelli di gestione e hosting diversi. Un vantaggio di questo modello è che non si è più vincolati a risorse che vengono distribuite nel gruppo di risorse.

### API gestite

Varie API vengono gestite da Microsoft per conto dell'utente, ad esempio Office 365, Salesforce, Twitter, FTP e così via. Alcune di queste API gestite possono essere usate così come sono, ad esempio Bing Translator, mentre altre richiedono una configurazione. Questa configurazione è detta *connessione*.

Quando si usa Office 365, ad esempio, è necessario creare una connessione contenente il token di accesso a Office 365. Il token verrà archiviato in modo sicuro e aggiornato in modo che l'app per la logica possa sempre chiamare l'API di Office 365. In alternativa, per connettersi al server SQL o FTP è necessario creare una connessione che includa la stringa di connessione.

All'interno della definizione queste azioni sono denominate `APIConnection`. Di seguito è riportato un esempio di connessione che chiama Office 365 per inviare un messaggio di posta elettronica:

```
{
    "actions": {
        "Send_Email": {
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
                "method": "post",
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

La parte di input univoca per le connessioni API è l'oggetto `host`, che contiene due parti: `api` e `connection`.

`api` contiene l'URL di runtime della posizione in cui è ospitata l'API gestita. Per visualizzare tutte le API gestite disponibili, è possibile chiamare `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

I **parametri di connessione** dell'API usata possono essere definiti o meno. Se non sono definiti, non è richiesta alcuna **connessione**. Se sono definiti, è necessario creare una connessione. La connessione creata avrà il nome scelto dall'utente, a cui viene fatto riferimento nell'oggetto `connection` all'interno dell'oggetto `host`. Per creare una connessione in un gruppo di risorse, chiamare:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Con il corpo seguente:


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
	"parameterValues" : {
		"accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
	}
  },
  "location" : "{Logic app's location}"
}
```

### Distribuzione di API gestite in un modello di distribuzione di Azure Resource Manager

È possibile creare un'applicazione completa in un modello di Azure Resource Manager, purché non sia necessario l'accesso interattivo. Se è necessario l'accesso, è possibile impostare tutto con il modello di Azure Resource Manager, ma sarà comunque necessario visitare il portale per autorizzare le connessioni.

```
	"resources": [{
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
		}
	}, {
		"type": "Microsoft.Logic/workflows",
		"apiVersion": "2015-08-01-preview",
		"name": "[parameters('logicAppName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[resourceId('Microsoft.Web/connections', 'azureblob')]"
		],
		"properties": {
			"sku": {
				"name": "[parameters('sku')]",
				"plan": {
					"id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
				}
			},
			"definition": {
				"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
				"actions": {
					"Create_file": {
						"type": "apiconnection",
						"inputs": {
							"host": {
								"api": {
									"runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
								},
								"connection": {
									"name": "@parameters('$connections')['azureblob']['connectionId']"
								}
							},
							"method": "post",
							"queries": {
								"folderPath": "[concat('/',parameters('containerName'))]",
								"name": "helloworld.txt"
							},
							"body": "@decodeDataUri('data:,Hello+world!')",
							"path": "/datasets/default/files"
						},
						"conditions": []
					}
				},
				"contentVersion": "1.0.0.0",
				"outputs": {},
				"parameters": {
					"$connections": {
						"defaultValue": {},
						"type": "Object"
					}
				},
				"triggers": {
					"recurrence": {
						"type": "Recurrence",
						"recurrence": {
							"frequency": "Day",
							"interval": 1
						}
					}
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
			}
		}
	}]
```

Come si vede in questo esempio, le connessioni non sono altro che normali risorse presenti nel gruppo di risorse. Fanno riferimento alle API gestite disponibili nella sottoscrizione dell'utente.

### API Web personalizzate

Se si usano API personalizzate, in particolare quelle non gestite da Microsoft, è necessario usare l'azione **HTTP** predefinita per chiamarle. Per un'esperienza ideale, si consiglia di esporre un endpoint swagger per l'API. In questo modo la finestra di progettazione di app per la logica può eseguire il rendering degli input e degli output per l'API. Senza un swagger, la finestra di progettazione può mostrare gli input e gli output solo come oggetti JSON opachi.

Di seguito è riportato un esempio che mostra la nuova proprietà `metadata.apiDefinitionUrl`:
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Se l'API Web è ospitata nel **servizio App**, verrà visualizzata automaticamente nell'elenco di azioni disponibili nella finestra di progettazione. In caso contrario, è necessario incollare direttamente l'URL. Per poter essere usato all'interno della finestra di progettazione di app per la logica, l'endpoint swagger deve essere non autenticato, anche se è possibile proteggere l'API stessa con qualsiasi metodo supportato nel file Swagger.

### Uso delle app per le API già distribuite con 2015-08-01-preview

Se in precedenza è stata distribuita un'app per le API, è possibile chiamarla usando l'azione **HTTP**.

Ad esempio, se si usa Dropbox per elencare i file, la definizione della versione dello schema **2014-12-01-preview** può essere simile a quanto segue:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
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
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

È possibile costruire l'azione HTTP equivalente come illustrato di seguito. La sezione parameters della definizione dell'app per la logica rimane invariata.

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

La tabella seguente illustra le singole proprietà:

| Proprietà dell'azione | Descrizione |
| --------------- | -----------  |
| `type` | `Http` invece di `APIapp` |
| `metadata.apiDefinitionUrl` | Per usare questa azione nella finestra di progettazione di App per la logica, è consigliabile includere l'endpoint dei metadati. L'azione è costruita da: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | L'azione è costruita da: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre `POST` |
| `inputs.body` | Identica ai parametri dell'app per le API | 
| `inputs.authentication` | Identica all'autenticazione dell'app per le API |

Questo approccio dovrebbe funzionare per tutte le azioni delle app per le API. Tuttavia, tenere presente che le app per le API precedenti non sono più supportate ed è necessario passare a una delle due opzioni descritte in precedenza, ovvero usare un'API gestita oppure ospitare un'API Web personalizzata.

## 2\. Repeat rinominato come Foreach

In base ai commenti e suggerimenti ricevuti dai clienti per la versione dello schema precedente, **Repeat** generava confusione e non era chiaramente identificabile come ciclo Foreach. Di conseguenza, è stato rinominato in **Foreach**. Ad esempio:

```
{
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
}
```

Ora viene scritto come segue:

```
{
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
}
```

In precedenza veniva usata la funzione `@repeatItem()` per fare riferimento all'elemento corrente sottoposto a iterazione. La funzione è stata semplificata in `@item()`.

### Riferimento agli output di Foreach
Per maggiore semplicità, gli output delle azioni **Foreach** non vengono racchiusi in un oggetto denominato **repeatItems**. Ciò significa che, mentre gli output dell'azione Repeat precedente erano:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Ora hanno l'aspetto seguente:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Facendo riferimento agli output, per ottenere il corpo dell'azione era necessario eseguire:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Ora, invece, è possibile eseguire:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Con queste modifiche, le funzioni `@repeatItem()`, `@repeatBody()` e `@repeatOutputs()` sono state rimosse.

## 3\. Listener HTTP nativo 
La funzionalità di Listener HTTP ora è incorporata e non è più necessario distribuire un'app per le API Listener HTTP. Per informazioni dettagliate, vedere [App per la logica come endpoint che è possibile chiamare](app-service-logic-http-endpoint.md).

Con queste modifiche, la funzione `@accessKeys()` è stata rimossa e sostituita con la funzione `@listCallbackURL()` allo scopo di ottenere l'endpoint, se necessario. Ora è necessario definire almeno un trigger nell'app per la logica. Per eseguire il comando `/run` sul flusso di lavoro, è necessario avere un trigger `manual`, `apiConnectionWebhook` o `httpWebhook`.

## 4\. Chiamata a flussi di lavoro figlio

In precedenza, per la chiamata a flussi di lavoro figlio era necessario passare al flusso di lavoro, ottenere il token di accesso e quindi incollarlo nella definizione dell'app per la logica che doveva chiamare l'elemento figlio. Con la nuova versione dello schema, il motore di App per la logica genera automaticamente una firma di accesso condiviso in fase di esecuzione per il flusso di lavoro figlio. Ciò significa che non è necessario incollare segreti nella definizione. Di seguito è fornito un esempio:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Un secondo miglioramento è l'accesso completo alla richiesta in ingresso per i flussi di lavoro figlio. Ciò significa che è possibile passare parametri nella sezione *queries* e nell'oggetto *headers* e che è possibile definire completamente l'intero corpo.

Infine, è stato necessario apportare modifiche al flusso di lavoro figlio. Mentre in precedenza era possibile chiamare direttamente un flusso di lavoro figlio, ora occorre definire un endpoint trigger nel flusso di lavoro che l'elemento padre possa chiamare. In genere, questo comporta l'aggiunta di un trigger di tipo **manual** da usare nella definizione dell'elemento padre. Si noti che la proprietà `host` in particolare include un `triggerName`, perché è sempre necessario specificare quale trigger viene richiamato.

## Altre modifiche

### Nuova proprietà queries
Tutti i tipi di azione ora supportano un nuovo input denominato **queries**. Può trattarsi di un oggetto strutturato e non è più necessario assemblare la stringa manualmente.

### Funzione parse() rinominata
In previsione dei nuovi tipi di contenuto che verranno aggiunti a breve, la funzione `parse()` è stata rinominata in `json()`.

## Presto disponibile: API Enterprise Integration
Attualmente non esistono versioni gestite delle API Enterprise Integration, ad esempio AS2, ma saranno presto disponibili, come illustrato nella [guida di orientamento](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Nel frattempo, è possibile usare le API BizTalk distribuite esistenti con l'azione HTTP, come descritto nella sezione "Uso delle app per le API già distribuite" di questo articolo.

<!---HONumber=AcomDC_0727_2016-->