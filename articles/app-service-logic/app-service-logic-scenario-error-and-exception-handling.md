<properties 
    pageTitle="Registrazione e gestione degli errori in App per la logica | Microsoft Azure" 
    description="Visualizzare un caso d'uso reale di registrazione e gestione degli errori con App per la logica" 
    keywords=""
    services="logic-apps" 
    authors="hedidin" 
    manager="" 
    editor="" 
    documentationCenter=""/>
    
<tags 
    ms.service="logic-apps" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2016" 
    ms.author="b-hoedid"/>
    
# Registrazione e gestione degli errori in App per la logica 

Questo articolo descrive in dettaglio come è possibile estendere un'app per la logica per supportare al meglio la gestione delle eccezioni. Si tratta di un caso d'uso reale e risponde alla domanda **App per la logica supporta la gestione di errori ed eccezioni?**

>[AZURE.NOTE] La versione corrente di App per la logica offre un modello standard per le risposte alle azioni. Include le risposte per gli errori e la convalida interna restituite da un'app per le API.

## Panoramica del caso d'uso e dello scenario

Di seguito è descritto il caso d'uso di questo articolo.

> Una nota organizzazione sanitaria ha richiesto la creazione di una soluzione di Azure per realizzare un portale per i pazienti con Dynamics CRM Online (CRMOL). Attualmente usano Salesforce ed quindi necessario inviare i record degli appuntamenti tra il portale dei pazienti CRMOL e Salesforce. È stato richiesto l'uso dello standard [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) per tutti i pazienti.

> Ecco i due requisiti principali che è stato necessario includere:
> -  Registrazione dei record inviati dal portale CRMOL
> -  È necessario un modo per visualizzare gli eventuali errori che si verificano all'interno del flusso di lavoro.


## Come è stato risolto il problema

>[AZURE.TIP] È possibile guardare un video di alto livello disponibile in[Integration User Group](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integration User Group")

Per i record di log e di errore si è deciso di usare **[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB")** come repository. In DocumentDB i record sono definiti documenti. Poiché App per la logica include un modello standard per tutte le risposte, non è necessario creare uno schema personalizzato. È stato possibile creare un'app per le API per inserimenti e query, sia per i record di errore che di log. È stato anche possibile definire uno schema per ognuno all'interno dell'app per le API.

Un altro requisito consiste nel ripulire i record dopo una certa data. DocumentDB include una proprietà [Time-To-Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time-To-Live") (ttl), che consente di impostare un valore **Time-to-Live** per ogni record o in un'intera raccolta. Questo evita di dover eliminare manualmente i record in DocumentDB.

### Approccio adottato

Il primo passaggio consiste nel creare l'app per la logica e caricarla nella finestra di progettazione. In questo esempio vengono usate app per la logica padre-figlio. Si supponga di aver già creato l'elemento padre. Si procederà alla creazione di un'app per la logica figlio.

Poiché si prevede di registrare il record proveniente da CRMOL, si inizierà dal principio. È necessario usare un trigger di richiesta, perché l'app per la logica padre attiverà questo elemento figlio.

> [AZURE.IMPORTANT] Per completare questa esercitazione, è necessario creare un database DocumentDB e due raccolte per registrazione ed errori.

### Trigger dell'app per la logica 

**Viene usato un trigger di richiesta come illustrato di seguito**

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### Passi 

Si inizierà con la registrazione. È necessario registrare l'origine (richiesta) del record del paziente dal portale CRMOL.

1. Prima di tutto occorre **ottenere un record di nuovo appuntamento** da CRMOL. Il trigger proveniente da CRM fornirà **PatentId CRM**, **tipo di record**, **record nuovo o aggiornato** (valore booleano new o update) e **SalesforceId**. SalesforceId può essere Null, perché viene usato solo per un aggiornamento. Per ottenere il record CRM si userà PatientID CRM e il tipo di record.
1. È quindi necessario aggiungere l'operazione **InsertLogEntry** dell'app per le API DocumentDB, come illustrato nella figura seguente:


#### Inserire una visualizzazione di progettazione per le voci di log

![Inserire una voce di log](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### Inserire una visualizzazione di progettazione per le voci di errore
![Inserire una voce di log](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### Condizione: verificare la presenza di operazioni di creazione di record non riuscite 

![Condizione](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## Codice sorgente dell'app per la logica 

>[AZURE.NOTE]  Quelli riportati di seguito sono solo esempi. Considerato che l'esercitazione è basata su un'implementazione effettiva attualmente in produzione, il valore di un **Nodo di origine** potrebbe non mostrare le proprietà correlate alla pianificazione di un appuntamento.

### Registrazione
L'esempio di codice dell'app per la logica seguente illustra come gestire la registrazione

#### Inserire una voce di log
Questo è il codice sorgente dell'app per la logica per l'inserimento di una voce di log

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### Richiesta di log

Questo è il messaggio di richiesta di log registrato nell'app per le API

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
	    "date": "Fri, 10 Jun 2016 22:31:56 GMT",
	    "operation": "New Patient",
	    "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "providerId": "",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}"
    	}
    }
    
```


#### Risposta di log

Questo è il messaggio di risposta di log dall'app per le API

``` json
{
    "statusCode": 200,
    "headers": {
	    "Pragma": "no-cache",
	    "Cache-Control": "no-cache",
	    "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
	    "Server": "Microsoft-IIS/8.0",
	    "X-AspNet-Version": "4.0.30319",
	    "X-Powered-By": "ASP.NET",
	    "Content-Length": "964",
	    "Content-Type": "application/json; charset=utf-8",
	    "Expires": "-1"
    },
    "body": {
	    "ttl": 2592000,
	    "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
	    "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
	    "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
	    "_ts": 1465597936,
	    "_etag": ""0400fc2f-0000-0000-0000-575b3ff00000"",
	    "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "timestamp": "2016-06-10T22:31:56Z",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}",
	    "operation": "New Patient",
	    "salesforceId": "",
	    "expired": false
    }
}
    
```

Si esamineranno ora i passi per la gestione degli errori:

----------    
    
### Gestione degli errori

L'esempio di codice dell'app per la logica seguente illustra come implementare la gestione degli errori.

#### Creare record di errore

Questo è il codice sorgente dell'app per la logica per la creazione di un record di errore.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter": 
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}  	       
```

#### Inserire l'errore in DocumentDB: richiesta

``` json
  
{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MasterID_mp__c":"","C_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","ONY_ID__c":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "statusCode": "400"
    }
}
```

#### Inserire l'errore in DocumentDB: risposta


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": ""0c00eaac-0000-0000-0000-575b3fdc0000"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c":"DO - Degree level is DO","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MterID_mp__c":"","Medicis_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","XXXXXXX":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### Risposta di errore di Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}
    
```

### Restituzione della risposta all'app per la logica padre

Dopo aver creato la risposta, è possibile restituirla all'app per la logica padre.

#### Restituire la risposta di esito positivo all'app per la logica padre

``` json
"SuccessResponse": {
    "runAfter": 
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "	Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### Restituire la risposta di errore all'app per la logica padre

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}
  
```

----------
  
## Portale e repository di DocumentDB

In questa soluzione sono state aggiunte funzionalità con [DocumentDB](https://azure.microsoft.com/services/documentdb)

### Portale di gestione degli errori

Per visualizzare gli errori, è possibile creare un'app Web MVC per visualizzare i record di errore da DocumentDB. Per la versione corrente sono incluse le operazioni **Elenco**, **Dettagli**, **Modifica** ed **Elimina**.

> [AZURE.NOTE] Operazione di modifica: DocumentDB sostituisce l'intero documento. I record visualizzati nelle viste di elenco e dettagli sono solo esempi. Non si tratta di record effettivi di appuntamenti dei pazienti
        
Ecco alcuni esempi di dettagli dell'app MVC usando l'approccio precedente.

#### Elenco di gestione degli errori

![Elenco errori](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)
        
#### Visualizzazione dei dettagli di gestione degli errori

![Dettagli errore](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### Portale di gestione dei log

Per visualizzare i log, è stata creata anche un'app Web MVC. Ecco alcuni esempi di dettagli dell'app MVC usando l'approccio precedente.

#### Visualizzazione di dettagli dei log di esempio

![Visualizzare i dettagli dei log](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)
    
### Dettagli dell'app per le API

#### API di gestione delle eccezioni di app per la logica

L'app per le API di gestione delle eccezioni di app per la logica offre le funzionalità seguenti.

Sono presenti due controller
- ErrorController inserisce un record di errore (documento) in una raccolta di DocumentDB
- LogController inserisce un record di log (documento) in una raccolta di DocumentDB

> [AZURE.TIP] Entrambi i controller usano operazioni `async Task<dynamic>`. In questo modo le operazioni possono essere risolte in fase di esecuzione, per poter creare lo schema di DocumentDB nel corpo dell'operazione.

Ogni documento in DocumentDB deve avere un ID univoco. Viene usato `PatientId` e viene aggiunto un timestamp che sarà convertito in un valore di timestamp Unix (double). Viene troncato per rimuovere il valore frazionario.

È possibile visualizzare il codice sorgente dell'API controller di errore [da GitHub qui] (https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs)

L'API viene chiamata da un'app per la logica usando la sintassi seguente:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

L'espressione nell'esempio di codice precedente ricerca lo stato *Failed* di **Create\_NewPatientRecord**

## Riepilogo 

- È possibile implementare facilmente la registrazione e la gestione degli errori in un'app per la logica.
- È possibile sfruttare DocumentDB come repository per i record di log e di errore (documenti).
- È possibile usare MVC per creare un portale per la visualizzazione dei record di log e di errore.

### Codice sorgente
Il codice sorgente dell'app per le API di gestione delle eccezioni di app per la logica è disponibile in questo [Repository GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API di gestione delle eccezioni dell'app per la logica").


## Passaggi successivi 
- [Esempi e scenari comuni di app per la logica](app-service-logic-examples-and-scenarios.md)
- [Monitorare le app per la logica](app-service-logic-monitor-your-logic-apps.md)
- [Creare un modello di distribuzione di app per la logica](app-service-logic-create-deploy-template.md)

<!---HONumber=AcomDC_0803_2016-->