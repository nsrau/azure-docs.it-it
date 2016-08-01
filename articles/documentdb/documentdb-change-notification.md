<properties 
    pageTitle="Notifiche delle modifiche DocumentDB usando le app per la logica | Microsoft Azure" 
    description="." 
    keywords="notifica delle modifiche"
    services="documentdb" 
    authors="hedidin" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.date="06/14/2016" 
    ms.author="b-hoedid"/>

# Notifiche per le risorse di DocumentDB nuove o modificate usando le app per la logica

Questo articolo ha origine da una domanda inviata in uno dei forum della community di Azure DocumentDB. La domanda è **DocumentDB supporta le notifiche per le risorse modificate**?

Ho collaborato con BizTalk Server per molti anni e questo è uno scenario molto comune quando si usa il [WCF LOB Adapter](https://msdn.microsoft.com/library/bb798128.aspx). Per questa ragione ho deciso di provare a duplicare questa funzionalità in DocumentDB per i documenti nuovi e/o modificati.

Questo articolo offre una panoramica dei componenti della soluzione di notifica delle modifiche che include un [trigger](documentdb-programming.md#trigger) e un'[app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md). Frammenti di codice importanti sono disponibili inline, mentre l'intera soluzione è disponibile su [GitHub](https://github.com/HEDIDIN/DocDbNotifications).

## Caso d'uso

Di seguito è descritto il caso d'uso di questo articolo.

DocumentDB è il repository per i documenti di Health Level Seven International (HL7) Fast Healthcare Interoperability Resources (FHIR). Si supponga che il database di DocumentDB associato all'API e all'app per la logica costituisca un server di HL7 FHIR. Una struttura sanitaria memorizza i dati dei pazienti nel database "Patients" di DocumentDB. Il database dei pazienti include diverse raccolte: Clinical, Identification e così via. Le informazioni sui pazienti rientrano nella categoria Identification. Esiste una raccolta denominata "Patient".

Il reparto di cardiologia tiene traccia dei dati personali relativi a salute ed esercizio. La ricerca dei record paziente nuovi o modificati può richiedere molto tempo. Viene richiesto al reparto IT se è possibile ricevere una notifica per i record paziente nuovi o modificati.

Il reparto IT risponde che l'operazione è molto semplice. Comunica inoltre che è possibile inviare i documenti all'[archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/) in modo che il reparto di cardiologia possa accedervi facilmente.

## Come è stato risolto il problema dal reparto IT

Per creare l'applicazione, il reparto IT ha deciso di definire prima un modello. Il vantaggio della notazione BPMN (Business Process Model and Notation) è rappresentato dalla sua facilità d'uso sia da parte di specialisti che di non specialisti. L'intero processo di notifica è considerato un processo aziendale.

## Panoramica del processo di notifica

1. Si inizia con un'app per la logica che include un trigger timer. Per impostazione predefinita, il trigger viene eseguito ogni ora.
2. Successivamente viene eseguito un POST HTTP nell'app per la logica.
3. L'app per la logica esegue tutte le operazioni.

![panoramica](./media/documentdb-change-notification/high-level-view.png)

### Operazioni eseguite dall'app per la logica
Nella figura seguente è possibile osservare i diversi passaggi del flusso di lavoro dell'app per la logica.

![Processo di logica principale](./media/documentdb-change-notification/main-logic-app-process.png)

Attenersi alla procedura seguente:

1. È necessario ottenere il valore DateTime UTC da un'app per le API. Il valore predefinito è un'ora prima.

2. Il valore DateTime UTC viene convertito in un formato Timestamp Unix. Questo è il formato predefinito per i timestamp in DocumentDB.

3. Viene eseguito il POST del valore in un'app per le API che esegue una query DocumentDB. Il valore viene usato in una query.

    ```SQL
     	SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```

    > [AZURE.NOTE] \_ts rappresenta i metadati TimeStamp per tutte le risorse di DocumentDB.

4. Se vengono trovati documenti, il corpo della risposta viene inviato all'archiviazione BLOB di Azure.

    > [AZURE.NOTE] L'archiviazione BLOB richiede un account di archiviazione di Azure. Eseguire il provisioning di un account di archiviazione BLOB di Azure e aggiungere un nuovo BLOB denominato patients. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md) e [Introduzione all'archiviazione BLOB di Azure ](../storage/storage-dotnet-how-to-use-blobs.md).

5. Viene infine inviato un messaggio che comunica al destinatario il numero di documenti trovati. Se non vengono trovati documenti, il corpo del messaggio sarà "0 Documents Found".

Dopo aver illustrato le operazioni eseguite dal flusso di lavoro, viene ora descritta la modalità di implementazione.

### Iniziare con l'app per la logica principale

Se non si ha familiarità con le app per la logica, le app sono disponibili in [Azure Marketplace](https://portal.azure.com/). Vedere anche [Cosa sono le app per la logica?](../app-service-logic/app-service-logic-what-are-logic-apps.md)

Quando si crea una nuova app per la logica, viene richiesto come si desidera iniziare (**How would you like to start?**)

Quando si fa clic all'interno della casella di testo, sono disponibili diversi eventi. Per questa app per la logica, selezionare **Manual - When an HTTP request is received** come illustrato di seguito.

![Avvio](./media/documentdb-change-notification/starting-off.png)

### Visualizzazione di progettazione dell'app per la logica completata
Si osservi ora la visualizzazione di progettazione completata per l'app per la logica denominata DocDB.

![Flusso di lavoro dell'app per la logica](./media/documentdb-change-notification/workflow-expanded.png)

Quando si modificano le azioni nella finestra di progettazione dell'app per la logica, è possibile selezionare gli **output** della richiesta HTTP o dell'azione precedente come illustrato nell'azione sendMail riportata di seguito.

![Scegliere gli output](./media/documentdb-change-notification/choose-outputs.png)

Prima di ogni azione del flusso di lavoro, è possibile scegliere **Add an action** o **Add a condition** come illustrato nella figura seguente.

![Prendere una decisione](./media/documentdb-change-notification/add-action-or-condition.png)

Se si seleziona **Add a condition**, viene visualizzato un modulo per l'immissione della logica, come illustrato nella figura seguente. Si tratta in sostanza di una regola di business. Se si fa clic all'interno di un campo, è possibile selezionare i parametri dell'azione precedente. È anche possibile immettere direttamente i valori.

![Aggiungere una condizione](./media/documentdb-change-notification/condition1.png)

> [AZURE.NOTE] È inoltre possibile immettere tutti gli elementi nella visualizzazione Codice.

Si osservi l'app per la logica completata nella visualizzazione Codice.

```JSON
   
   	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }
	
```

Se non si ha familiarità con ciò che rappresentano le diverse sezioni del codice, vedere la documentazione relativa al [linguaggio di definizione del flusso di lavoro delle app per la logica](http://aka.ms/logicappsdocs).

Per questo flusso di lavoro si usa un [trigger webhook HTTP](https://sendgrid.com/blog/whats-webhook/). Se si esamina il codice riportato sopra, si noteranno parametri simili all'esempio seguente.

```C#

    =@{triggerBody()['Subject']}

```

`triggerBody()` rappresenta i parametri inclusi nel corpo di un POST REST nell'API REST dell'app per la logica. `()['Subject']` rappresenta il campo. Tutti questi parametri costituiscono il corpo in formato JSON.

> [AZURE.NOTE] Usando un webhook, è possibile accedere all'intestazione e al corpo della richiesta del trigger. In questa applicazione si desidera accedere al corpo.

Come accennato in precedenza, è possibile usare la finestra di progettazione per assegnare i parametri. In alternativa, l'operazione può essere eseguita nella visualizzazione Codice. Se si esegue l'operazione nella visualizzazione Codice, si definiscono le proprietà che richiedono un valore, come illustrato nell'esempio di codice seguente.

```JSON

	"triggers": {
		"manual": {
		    "inputs": {
			"schema": {
			    "properties": {
			"Subject": {
			    "type" : "String"	

			}
			},
			    "required": [
			"Subject"
			     ],
			    "type": "object"
			}
		    },
		    "type": "Manual"
		}
	    }
```

Si sta creando uno schema JSON che verrà passato dal corpo del POST HTTP. Per attivare il trigger, è necessario un URL callback. La generazione dell'URL è descritta più avanti nell'esercitazione.

## Azioni
Di seguito sono descritte le operazioni eseguite da ogni azione dell'app per la logica.

### GetUTCDate

**Visualizzazione di progettazione**

![](./media/documentdb-change-notification/getutcdate.png)

**Visualizzazione Codice**

```JSON

	"GetUtcDate": {
		    "conditions": [],
		    "inputs": {
			"method": "get",
			"queries": {
			    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
			},
			"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
		    },
		    "metadata": {
			"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
		    },
		    "type": "Http"
		},

```

Questa azione HTTP esegue un'operazione GET. Chiama il metodo GetUtcDate dell'app per le API. L'URI usa la proprietà 'GetUtcDate\_HoursBack' passata nel corpo del trigger. Il valore 'GetUtcDate\_HoursBack' è impostato nella prima app per la logica. Più avanti nell'esercitazione verranno fornite altre informazioni sull'app per la logica Trigger.

Questa azione chiama l'app per le API per restituire il valore di stringa della data UTC.

#### Operazioni

**Richiesta**

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
	    "method": "get",
	    "queries": {
		  "hoursBack": "24"
	    }
	}

```

**Risposta**

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		  "pragma": "no-cache",
		  "cache-Control": "no-cache",
		  "date": "Fri, 26 Feb 2016 15:47:33 GMT",
		  "server": "Microsoft-IIS/8.0",
		  "x-AspNet-Version": "4.0.30319",
		  "x-Powered-By": "ASP.NET"
	    },
	    "body": "Fri, 15 Jan 2016 23:47:33 GMT"
	}

```

Il passaggio successivo consiste nel convertire il valore DateTime UTC in TimeStamp Unix, che è un tipo double .NET.

### Conversione

##### Visualizzazione di progettazione

![Conversione](./media/documentdb-change-notification/conversion.png)

##### Visualizzazione Codice

```JSON

	"Conversion": {
	    "conditions": [
		{
		    "dependsOn": "GetUtcDate"
		}
	    ],
	    "inputs": {
		"method": "post",
		"queries": {
		    "currentDateTime": "@{body('GetUtcDate')}"
		},
		"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
	    },
	    "metadata": {
		"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
	    },
	    "type": "Http"
	},

```

Viene passato il valore restituito da GetUTCDate. Poiché è presente una condizione dependsOn, l'azione GetUTCDate deve essere completata correttamente. In caso contrario, l'azione viene ignorata.

Questa azione chiama l'app per le API per gestire la conversione.

#### Operazioni

##### Richiesta

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
	    "method": "post",
	    "queries": {
		"currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
	    }
	}   
```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		  "pragma": "no-cache",
		  "cache-Control": "no-cache",
		  "date": "Fri, 26 Feb 2016 15:47:33 GMT",
		  "server": "Microsoft-IIS/8.0",
		  "x-AspNet-Version": "4.0.30319",
		  "x-Powered-By": "ASP.NET"
	    },
	    "body": 1452901653
	}
```

Nell'azione successiva, verrà eseguita un'operazione POST nell'app per le API.

### GetDocuments 

##### Visualizzazione di progettazione

![Acquisizione documenti](./media/documentdb-change-notification/getdocuments.png)

##### Visualizzazione Codice

```JSON

	"GetDocuments": {
	    "conditions": [
		{
		    "dependsOn": "Conversion"
		}
	    ],
	    "inputs": {
		"method": "post",
		"queries": {
		    "unixTimeStamp": "@{body('Conversion')}"
		},
		"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
	    },
	    "metadata": {
		"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
	    },
	    "type": "Http"
	},

```

Per l'azione GetDocuments si passa il corpo della risposta dell'azione di conversione. Si tratta di un parametro dell'URI:

 
```C#

	unixTimeStamp=@{body('Conversion')}

```

L'azione QueryDocuments esegue un'operazione HTTP POST nell'app per le API.

Il metodo chiamato è **QueryForNewPatientDocuments**.

#### Operazioni

##### Richiesta

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
	    "method": "post",
	    "queries": {
		"unixTimeStamp": "1452901653"
	    }
	}
```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"pragma": "no-cache",
		"cache-Control": "no-cache",
		"date": "Fri, 26 Feb 2016 15:47:35 GMT",
		"server": "Microsoft-IIS/8.0",
		"x-AspNet-Version": "4.0.30319",
		"x-Powered-By": "ASP.NET"
	    },
	    "body": [
		{
		    "id": "xcda",
		    "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
		    "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
		    "_ts": 1454874620,
		    "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
		    "resourceType": "Patient",
		    "text": {
			"status": "generated",
			"div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
		    },
		    "identifier": [
			{
			    "use": "usual",
			    "type": {
				"coding": [
				    {
					"system": "http://hl7.org/fhir/v2/0203",
					"code": "MR"
				    }
				]
			    },
			    "system": "urn:oid:2.16.840.1.113883.19.5",
			    "value": "12345"
			}
		    ],
		    "active": true,
		    "name": [
			{
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

L'azione successiva consiste nel salvare i documenti nell'[archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/).

> [AZURE.NOTE] L'archiviazione BLOB richiede un account di archiviazione di Azure. Eseguire il provisioning di un account di archiviazione BLOB di Azure e aggiungere un nuovo BLOB denominato patients. Per altre informazioni, vedere [Introduzione all'archiviazione BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md).

### Create File

##### Visualizzazione di progettazione

![Create File](./media/documentdb-change-notification/createfile.png)

##### Visualizzazione Codice

```JSON

	{
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

Il codice viene generato dall'azione nella finestra di progettazione. Non è necessario modificare il codice.

Se non si ha familiarità con l'API BLOB di Azure, vedere l'articolo relativo all'[introduzione all'API di archiviazione BLOB di Azure](../connectors/connectors-create-api-azureblobstorage.md).

#### Operazioni

##### Richiesta

```JSON

	"host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"pragma": "no-cache",
		"x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
		"cache-Control": "no-cache",
		"date": "Fri, 26 Feb 2016 15:47:36 GMT",
		"set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
		"server": "Microsoft-HTTPAPI/2.0",
		"x-AspNet-Version": "4.0.30319",
		"x-Powered-By": "ASP.NET"
	    },
	    "body": {
		"Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
		"Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"LastModified": "2016-02-26T15:47:36.215Z",
		"Size": 65647,
		"MediaType": "application/octet-stream",
		"IsFolder": false,
		"ETag": ""c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ"",
		"FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
	    }
	}
```

L'ultimo passaggio consiste nell'inviare una notifica di posta elettronica

### sendEmail

##### Visualizzazione di progettazione

![Invia messaggio di posta elettronica](./media/documentdb-change-notification/sendemail.png)

##### Visualizzazione Codice

```JSON


	"sendMail": {
	    "conditions": [
		{
		    "dependsOn": "GetDocuments"
		}
	    ],
	    "inputs": {
		"body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
		"headers": {
		    "Content-type": "application/x-www-form-urlencoded"
		},
		"method": "POST",
		"uri": "https://api.sendgrid.com/api/mail.send.json"
	    },
	    "type": "Http"
	}
```

In questa azione si invia una notifica di posta elettronica. Si usa [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29).

Il codice è stato generato usando un modello per l'app per la logica e SendGrid che si trova nel [repository Github 101-logic-app-sendgrid](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid).
 
L'operazione HTTP è un POST.

I parametri di autorizzazione sono nelle proprietà del trigger

```JSON

	},
		"sendgridPassword": {
			 "type": "SecureString"
		 },
		 "sendgridUsername": {
			"type": "String"
		 }

		In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
		},
		"toAddress": {
		    "defaultValue": "XXXX@XXXX.com",
		    "type": "String"
		},
		"fromAddress": {
		    "defaultValue": "XXX@msn.com",
		    "type": "String"
		},
		"emailBody": {
		    "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
		    "type": "String"
		},

```

emailBody esegue la concatenazione del numero di documenti restituiti dalla query, che può essere "0" o più, con i record trovati. I parametri rimanenti vengono impostati in base ai parametri di Trigger.

Questa azione dipende dall'azione **GetDocuments**.

#### Operazioni

##### Richiesta
```JSON

	{
	    "uri": "https://api.sendgrid.com/api/mail.send.json",
	    "method": "POST",
	    "headers": {
		"Content-type": "application/x-www-form-urlencoded"
	    },
	    "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
	}

```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"connection": "keep-alive",
		"x-Frame-Options": "DENY,DENY",
		"access-Control-Allow-Origin": "https://sendgrid.com",
		"date": "Fri, 26 Feb 2016 15:47:35 GMT",
		"server": "nginx"
	    },
	    "body": {
		"message": "success"
	    }
	}
```

Infine si desidera essere in grado di visualizzare i risultati dall'app per la logica nel portale di Azure. A tale scopo, si aggiunge un parametro alla sezione degli output.


```JSON

	"outputs": {
		"Results": {
		    "type": "String",
		    "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
		}

```

Viene restituito lo stesso valore che viene inviato nel corpo del messaggio di posta elettronica. La figura seguente mostra un esempio in cui sono stati trovati 29 record.

![Risultati](./media/documentdb-change-notification/logic-app-run.png)

## Metrica
È possibile configurare il monitoraggio dell'app per la logica principale nel portale. In questo modo è possibile visualizzare la latenza di esecuzione e altri eventi, come illustrato nella figura riportata di seguito.

![](./media/documentdb-change-notification/metrics.png)

## DocDb Trigger

Questa app per la logica è il trigger che avvia il flusso di lavoro nell'app per la logica principale.

La figura seguente mostra la visualizzazione di progettazione.

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

	{
	    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	    "actions": {
		"Http": {
		    "conditions": [],
		    "inputs": {
			"body": {
			    "EmailTo": "XXXXXX@XXXXX.net",
			    "GetUtcDate_HoursBack": "24",
			    "Subject": "New Patients",
			    "sendgridPassword": "********",
			    "sendgridUsername": "azureuser@azure.com"
			},
			"method": "POST",
			"uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
		    },
		    "type": "Http"
		}
	    },
	    "contentVersion": "1.0.0.0",
	    "outputs": {
		"Results": {
		    "type": "String",
		    "value": "@{body('Http')['status']}"
		}
	    },
	    "parameters": {},
	    "triggers": {
		"recurrence": {
		    "recurrence": {
			"frequency": "Hour",
			"interval": 24
		    },
		    "type": "Recurrence"
		}
	    }
	}

```

Il trigger è impostato per una ricorrenza di 24 ore. L'azione è un POST HTTP che usa l'URL callback per l'app per la logica principale. Il corpo contiene i parametri specificati nello schema JSON.

#### Operazioni

##### Richiesta

```JSON

	{
	    "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
	    "method": "POST",
	    "body": {
		"EmailTo": "XXXXXX@XXXXX.net",
		"GetUtcDate_HoursBack": "24",
		"Subject": "New Patients",
		"sendgridPassword": "********",
		"sendgridUsername": "azureuser@azure.com"
	    }
	}

```

##### Response

```JSON

	{
	    "statusCode": 202,
	    "headers": {
		"pragma": "no-cache",
		"x-ms-ratelimit-remaining-workflow-writes": "7486",
		"x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
		"x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
		"cache-Control": "no-cache",
		"date": "Thu, 25 Feb 2016 21:01:06 GMT"
	    }
	}
```

Si osservi ora l'app per le API.

## DocDBNotificationApi

Sebbene esistano diverse operazioni nell'app, ne verranno usate solo tre.

* GetUtcDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### Operazioni DocDBNotificationApi
Si esamini la documentazione di Swagger

> [AZURE.NOTE] Per consentire di chiamare le operazioni esternamente, è necessario aggiungere un valore di origine CORS consentito "*" (senza virgolette) nelle impostazioni dell'app per le API come illustrato nella figura seguente.

![Configurazione Cors](./media/documentdb-change-notification/cors.png)

#### GetUtcDate

![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### ConvertToTimeStamp

![Ottenere la data UTC](./media/documentdb-change-notification/converion-swagger.png)

#### QueryForNewPatientDocuments

![Query](./media/documentdb-change-notification/patientswagger.png)

Si esamini ora il codice di questa operazione.

#### GetUtcDate

```C#

    /// <summary>
	/// Gets the current UTC Date value
	/// </summary>
	/// <returns></returns>
	[H ttpGet]
	[Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
	[SwaggerOperation("GetUtcDate")]
	[SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
	[SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
	public string GetUtcDate(
	   [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
	{


	    return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
	}
```

Questa operazione restituisce semplicemente il valore DateTime UTC corrente meno il valore HoursBack.

#### ConvertToTimeStamp

``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

Questa operazione converte la risposta dell'operazione GetUtcDate in un valore double.

#### QueryForNewPatientDocuments

```C#

	    /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
	}

```

Questa operazione usa [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) per creare una query del documento.

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

La risposta dell'operazione ConvertToTimeStamp (unixTimeStamp) viene passata. L'operazione restituisce un elenco di documenti, `IList<Document>`.

In precedenza è stato citato CallbackURL. Per avviare il flusso di lavoro nell'app per la logica principale, è necessario effettuare una chiamata usando CallbackURL.

## CallbackURL

Per iniziare, sarà necessario il token di Azure AD. Ottenere questo token può risultare difficile. Stavo cercando un metodo semplice e Jeff Hollan, che è un Program Manager di app per la logica di Azure, mi ha consigliato di usare [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) in PowerShell. È possibile installarlo seguendo le indicazioni fornite.

Le operazioni che si desidera usare sono Login e Call ARM API.
 
Login: usare le stesse credenziali per l'accesso al portale di Azure.

L'operazione Call ARM API genererà il CallBackURL.

In PowerShell, è possibile eseguire la chiamata come indicato di seguito:

```powershell

	ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

Il risultato dovrebbe avere l'aspetto seguente:

```powershell

	https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

È possibile usare uno strumento come [postman](http://www.getpostman.com/) per testare l'app per la logica principale come illustrato nella figura seguente.

![Postman](./media/documentdb-change-notification/newpostman.png)

Nella tabella seguente sono elencati i parametri di Trigger che costituiscono il corpo dell'app per la logica DocDB Trigger.

Parametro | Descrizione 
--- | --- 
GetUtcDate\_HoursBack | Consente di impostare il numero di ore per la data di inizio della ricerca
sendgridUsername | Consente di impostare il numero di ore per la data di inizio della ricerca
sendgridPassword | Il nome utente per la posta elettronica di Send Grid
EmailTo | L'indirizzo di posta elettronica che riceverà la notifica di posta elettronica
Oggetto | L'oggetto per il messaggio di posta elettronica

## Visualizzazione dei dati dei pazienti nel servizio BLOB di Azure

Accedere al proprio account di archiviazione di Azure e selezionare BLOB in Servizi, come illustrato nella figura seguente.

![Account di archiviazione](./media/documentdb-change-notification/docdbstorageaccount.png)

Sarà possibile visualizzare le informazioni del file BLOB del paziente, come illustrato di seguito.

![Servizio BLOB](./media/documentdb-change-notification/blobservice.png)


## Riepilogo

In questa procedura dettagliata, è stato appreso quanto segue:

* È possibile implementare le notifiche in DocumentDB.
* Usando le app per la logica è possibile automatizzare il processo.
* Usando le app per la logica è possibile ridurre il tempo necessario per distribuire un'applicazione.
* Usando HTTP è possibile usare facilmente un'app per le API in un'app per la logica.
* È possibile creare facilmente un CallBackURL che sostituisce il listener HTTP.
* È possibile creare facilmente flussi di lavoro personalizzati con la progettazione delle app per la logica.

È fondamentale pianificare con anticipo e creare un modello del flusso di lavoro.

## Passaggi successivi
Scaricare e usare il codice delle app per la logica disponibile in [Github](https://github.com/HEDIDIN/DocDbNotifications). È consigliabile compilare l'applicazione e inviare le modifiche nel repository.

Per altre informazioni su DocumentDB, visitare il [percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/documentdb/).

<!---HONumber=AcomDC_0720_2016-->