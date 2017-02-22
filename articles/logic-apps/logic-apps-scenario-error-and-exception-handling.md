---
title: Registrazione e gestione degli errori in App per la logica | Documentazione Microsoft
description: Visualizzare un caso d&quot;uso reale di registrazione e gestione degli errori con App per la logica
keywords: 
services: logic-apps
author: hedidin
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: fa65d18391aae1cc36f5e8ea54c195ed06b24c00


---
# <a name="logging-and-error-handling-in-logic-apps"></a>Registrazione e gestione degli errori in App per la logica
Questo articolo descrive come è possibile estendere un'app per la logica per supportare al meglio la gestione delle eccezioni. Si tratta di un caso d'uso reale e risponde alla domanda "App per la logica supporta la gestione di errori ed eccezioni?".

> [!NOTE]
> Lo schema corrente delle app per la logica offre un modello standard per le risposte alle azioni,
> che include le risposte per gli errori e la convalida interna restituite da un'app per le API.
> 
> 

## <a name="overview-of-the-use-case-and-scenario"></a>Panoramica del caso d'uso e dello scenario
Di seguito è descritto il caso d'uso di questo articolo.
Una nota organizzazione sanitaria ha richiesto lo sviluppo di una soluzione di Azure per creare un portale per i pazienti con Microsoft Dynamics CRM Online, con invio dei record degli appuntamenti tra il portale per i pazienti Dynamics CRM Online e Salesforce.  È stato richiesto di usare lo standard [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) per tutti i record dei pazienti.

Il progetto prevedeva due requisiti principali:  

* Un metodo per registrare i record inviati dal portale Dynamics CRM Online
* Un modo per visualizzare gli eventuali errori verificatisi nel flusso di lavoro

## <a name="how-we-solved-the-problem"></a>Come è stato risolto il problema
> [!TIP]
> Un video generale del progetto è disponibile in [Integration User Group](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integration User Group").
> 
> 

Come repository per i record di log e di errore è stato scelto [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB") , in cui i record sono definiti documenti. Dato che App per la logica include un modello standard per tutte le risposte, non è stato necessario creare uno schema personalizzato. È stato possibile creare un'app per le API per l'**inserimento** e la **query** per i record di errore e di log. È stato anche possibile definire uno schema per ognuno all'interno dell'app per le API.  

Un altro requisito era ripulire i record dopo una certa data. DocumentDB include una proprietà denominata [Durata (TTL)](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Durata (TTL)") che ha consentito di impostare un valore di **Durata (TTL)** per ogni record o raccolta. Questo ha evitato di dover eliminare manualmente i record in DocumentDB.

### <a name="creation-of-the-logic-app"></a>Creazione dell'app per la logica
Il primo passaggio consiste nel creare l'app per la logica e caricarla nella finestra di progettazione. In questo esempio vengono usate app per la logica padre-figlio. Partendo dal presupposto di aver già creato l'elemento padre, si procederà alla creazione di un'app per la logica figlio.

Dato che si prevede di registrare il record proveniente da Dynamics CRM Online, si inizierà dal principio. È necessario usare un trigger di richiesta, perché l'app per la logica padre attiva questo elemento figlio.

> [!IMPORTANT]
> Per completare questa esercitazione, è necessario creare un database DocumentDB e due raccolte, per la registrazione e gli errori.
> 
> 

### <a name="logic-app-trigger"></a>Trigger dell'app per la logica
Viene usato un trigger di richiesta come illustrato nell'esempio seguente.

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


### <a name="steps"></a>Passi
È necessario registrare l'origine (richiesta) del record del paziente dal portale Dynamics CRM Online.

1. È necessario ottenere un nuovo record di appuntamento da Dynamics CRM Online.
    Il trigger proveniente da CRM fornisce il **PatientID CRM**, il **tipo di record**, un **valore booleano new o update** che indica un record nuovo o aggiornato e **SalesforceId**. **SalesforceId** può essere Null perché viene usato solo per un aggiornamento.
    Per ottenere il record CRM si userà il **PatientID** CRM e il **tipo di record**.
2. È quindi necessario aggiungere l'operazione **InsertLogEntry** dell'app per le API DocumentDB, come illustrato nelle figure seguenti.

#### <a name="insert-log-entry-designer-view"></a>Visualizzazione della finestra di progettazione per l'inserimento di voci di log
![Inserire una voce di log](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

#### <a name="insert-error-entry-designer-view"></a>Visualizzazione della finestra di progettazione per l'inserimento di voci di errore
![Inserire una voce di log](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

#### <a name="check-for-create-record-failure"></a>Verifica della presenza di errori nella creazione di record
![Condizione](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Codice sorgente dell'app per la logica
> [!NOTE]
> Quelli riportati di seguito sono solo esempi. Dato che l'esercitazione è basata su un'implementazione attualmente in produzione, il valore di un **nodo di origine** potrebbe non mostrare le proprietà correlate alla pianificazione di un appuntamento.
> 
> 

### <a name="logging"></a>Registrazione
L'esempio di codice dell'app per la logica seguente illustra come gestire la registrazione.

#### <a name="log-entry"></a>Voce di log
Questo è il codice sorgente dell'app per la logica per l'inserimento di una voce di log.

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

#### <a name="log-request"></a>Richiesta di log
Questo è il messaggio di richiesta di log inviato all'app per le API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Risposta di log
Questo è il messaggio di risposta di log proveniente dall'app per le API.

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
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Verranno ora esaminati i passaggi della gestione degli errori.

### <a name="error-handling"></a>Gestione degli errori
L'esempio di codice di App per la logica seguente illustra come è possibile implementare la gestione degli errori.

#### <a name="create-error-record"></a>Creare record di errore
Questo è il codice sorgente di App per la logica per creare un record di errore.

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

#### <a name="insert-error-into-documentdb--request"></a>Inserire l'errore in DocumentDB: richiesta
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
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-documentdb--response"></a>Inserire l'errore in DocumentDB: risposta
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
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Risposta di errore di Salesforce
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

### <a name="returning-the-response-back-to-the-parent-logic-app"></a>Restituzione della risposta all'app per la logica padre
Dopo aver creato la risposta, è possibile restituirla all'app per la logica padre.

#### <a name="return-success-response-to-the-parent-logic-app"></a>Restituire la risposta di esito positivo all'app per la logica padre
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
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-the-parent-logic-app"></a>Restituire la risposta di errore all'app per la logica padre
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


## <a name="documentdb-repository-and-portal"></a>Portale e repository di DocumentDB
In questa soluzione sono state aggiunte funzionalità con [DocumentDB](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Portale di gestione degli errori
Per visualizzare gli errori, è possibile creare un'app Web MVC per visualizzare i record di errore da DocumentDB. Nella versione corrente sono incluse operazioni di tipo **Elenco**, **Dettagli**, **Modifica** ed **Elimina**.

> [!NOTE]
> Con l'operazione di modifica, DocumentDB sostituisce l'intero documento.
> I record contenuti nelle visualizzazioni **elenco** e **dettagli** sono solo esempi. Non si tratta di record effettivi di appuntamenti dei pazienti.
> 
> 

Di seguito sono riportati esempi dei dettagli dell'app MVC creati con l'approccio precedentemente descritto.

#### <a name="error-management-list"></a>Elenco di gestione degli errori
![Elenco errori](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Visualizzazione dei dettagli di gestione degli errori
![Dettagli errore](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portale di gestione dei log
È stata creata un'app Web MVC anche per visualizzare i log.  Di seguito sono riportati esempi dei dettagli dell'app MVC creati con l'approccio precedentemente descritto.

#### <a name="sample-log-detail-view"></a>Visualizzazione di dettagli dei log di esempio
![Visualizzare i dettagli dei log](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Dettagli dell'app per le API
#### <a name="logic-apps-exception-management-api"></a>API di gestione delle eccezioni di app per la logica
L'app per le API di gestione delle eccezioni di app per la logica offre le funzionalità seguenti.

Sono presenti due controller:

* **ErrorController** inserisce un record di errore (documento) in una raccolta di DocumentDB.
* **LogController** inserisce un record di log (documento) in una raccolta di DocumentDB.

> [!TIP]
> Entrambi i controller usano operazioni `async Task<dynamic>`. In questo modo le operazioni possono essere risolte in fase di esecuzione, per poter creare lo schema di DocumentDB nel corpo dell'operazione.
> 
> 

Ogni documento in DocumentDB deve avere un ID univoco. Viene usato `PatientId` e viene aggiunto un timestamp che sarà convertito in un valore di timestamp Unix (double). Viene troncato per rimuovere il valore frazionario.

È possibile visualizzare il codice sorgente dell'API del controller degli errori [da GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

L'API viene chiamata da un'app per la logica usando la sintassi seguente.

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

L'espressione nell'esempio di codice precedente ricerca lo stato **Failed** di *Create_NewPatientRecord*.

## <a name="summary"></a>Riepilogo
* È possibile implementare facilmente la registrazione e la gestione degli errori in un'app per la logica.
* È possibile usare DocumentDB come repository per i record di log e di errore (documenti).
* È possibile usare MVC per creare un portale per la visualizzazione dei record di log e di errore.

### <a name="source-code"></a>Codice sorgente
Il codice sorgente dell'applicazione per le API di gestione delle eccezioni di app per la logica è disponibile in questo [repository GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API di gestione delle eccezioni dell'app per la logica").

## <a name="next-steps"></a>Passaggi successivi
* [Esempi e scenari comuni di app per la logica](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Creare un modello di distribuzione di app per la logica](../logic-apps/logic-apps-create-deploy-template.md)




<!--HONumber=Jan17_HO3-->


