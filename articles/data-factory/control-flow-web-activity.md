---
title: "Attività Web in Azure Data Factory | Microsoft Docs"
description: "Informazioni su come usare l'attività Web, una delle attività del flusso di controllo supportate da Data Factory, per richiamare un endpoint REST da una pipeline."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: shlo
ms.openlocfilehash: b5661dea3a63f6e7e5b67261bc9704061ae9c5b6
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2017
---
# <a name="web-activity-in-azure-data-factory"></a>Attività Web in Azure Data Factory
L'attività Web può essere usata per chiamare un endpoint REST personalizzato da una pipeline di Data Factory. È possibile passare set di dati e servizi collegati in modo che l'attività possa usarli e accedervi. 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Sintassi

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
name | Nome dell'attività Web | String | Sì
type | Deve essere impostato su **WebActivity**. | String | Sì
statico | Metodo API REST per l'endpoint di destinazione. | Stringa. <br/><br/>Tipi supportati: "GET", "POST", "PUT" | Sì
URL | Endpoint e percorso di destinazione | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì
headers | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì, l'intestazione Content-type è obbligatoria. `"headers":{ "Content-Type":"application/json"}`
body | Rappresenta il payload inviato all'endpoint. Obbligatoria per i metodi POST e PUT.  | Stringa (o espressione con l'elemento resultType della stringa). <br/><br/>Vedere lo schema del payload della richiesta nella sezione [Schema del payload della richiesta](#request-payload-schema). | No
authentication | Metodo di autenticazione usato per chiamare l'endpoint. I tipi supportati sono "Basic" o "ClientCertificate". Per altre informazioni, vedere la sezione [Autenticazione](#authentication). Se l'autenticazione non è necessaria, escludere questa proprietà. | Stringa (o un'espressione con l'elemento resultType della stringa) | No
set di dati | Elenco di set di dati passato all'endpoint. | Matrice di riferimenti a set di dati. Può essere una matrice vuota. | Sì
linkedServices | Elenco dei servizi collegati passato all'endpoint. | Matrice di riferimenti a servizi collegati. Può essere una matrice vuota. | Sì

> [!NOTE]
> Gli endpoint REST che l'attività Web richiama devono restituire una risposta di tipo JSON.

## <a name="authentication"></a>Autenticazione

### <a name="none"></a>Nessuno
Se l'autenticazione non è necessaria, non includere la proprietà "authentication".

### <a name="basic"></a>Basic
Specificare il nome utente e la password da usare per l'autenticazione di base. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificato client
Specificare il contenuto con codifica Base64 di un file PFX e la password. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Schema del payload della richiesta
Quando si usa il metodo POST o PUT, la proprietà body rappresenta il payload che viene inviato all'endpoint. È possibile passare i servizi collegati e i set di dati come parte del payload. Di seguito è riportato lo schema per il payload: 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>Esempio
In questo esempio, l'attività Web della pipeline chiama un endpoint REST. Passa all'endpoint un servizio collegato SQL di Azure e un set di dati SQL di Azure. L'endpoint REST usa la stringa di connessione SQL di Azure per connettersi al server SQL di Azure e restituisce il nome dell'istanza del server SQL. 

### <a name="pipeline-definition"></a>Definizione della pipeline

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Valori dei parametri della pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Codice endpoint del servizio Web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
