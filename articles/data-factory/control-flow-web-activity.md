---
title: Attività Web in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare l'attività Web, una delle attività del flusso di controllo supportate da Data Factory, per richiamare un endpoint REST da una pipeline.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 73770e559af8a999c17fff5ea1aa6ee53ac17e83
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141594"
---
# <a name="web-activity-in-azure-data-factory"></a>Attività Web in Azure Data Factory
L'attività Web può essere usata per chiamare un endpoint REST personalizzato da una pipeline di Data Factory. È possibile passare set di dati e servizi collegati in modo che l'attività possa usarli e accedervi.

> [!NOTE]
> L'attività Web può chiamare solo URL esposti pubblicamente. Non è supportata per gli URL ospitati in una rete virtuale privata.

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

Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività Web | String | Sì
type | Deve essere impostato su **WebActivity**. | String | Yes
statico | Metodo API REST per l'endpoint di destinazione. | Stringa. <br/><br/>Tipi supportati: "GET", "POST", "PUT" | Sì
url | Endpoint e percorso di destinazione | Stringa (o espressione con l'elemento resultType della stringa). L'attività raggiungerà il timeout a 1 minuto con un errore se non riceve una risposta dall'endpoint. | Sì
headers | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì, l'intestazione Content-type è obbligatoria. `"headers":{ "Content-Type":"application/json"}`
body | Rappresenta il payload inviato all'endpoint.  | Stringa (o espressione con l'elemento resultType della stringa). <br/><br/>Vedere lo schema del payload della richiesta nella sezione [Schema del payload della richiesta](#request-payload-schema). | Obbligatoria per i metodi POST e PUT.
autenticazione | Metodo di autenticazione usato per chiamare l'endpoint. I tipi supportati sono "Basic" o "ClientCertificate". Per altre informazioni, vedere la sezione [Autenticazione](#authentication). Se l'autenticazione non è necessaria, escludere questa proprietà. | Stringa (o un'espressione con l'elemento resultType della stringa) | No
datasets | Elenco di set di dati passato all'endpoint. | Matrice di riferimenti a set di dati. Può essere una matrice vuota. | Yes
linkedServices | Elenco dei servizi collegati passato all'endpoint. | Matrice di riferimenti a servizi collegati. Può essere una matrice vuota. | Sì

> [!NOTE]
> Gli endpoint REST che l'attività Web richiama devono restituire una risposta di tipo JSON. L'attività raggiungerà il timeout a 1 minuto con un errore se non riceve una risposta dall'endpoint.

La tabella seguente indica i requisiti per il contenuto JSON:

| Tipo di valore | Corpo della richiesta | Corpo della risposta |
|---|---|---|
|Oggetto JSON | Supportato | Supportato |
|Matrice JSON | Supportato <br/>Al momento, le matrici JSON non funzionano per via di un bug. È in corso una correzione. | File modifiche disco non supportato |
| Valore JSON | Supportato | File modifiche disco non supportato |
| Tipo non JSON | File modifiche disco non supportato | File modifiche disco non supportato |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>Nessuna
Se l'autenticazione non è necessaria, non includere la proprietà "authentication".

### <a name="basic"></a>Basic
Specificare il nome utente e la password da usare per l'autenticazione di base.

```json
"authentication":{
   "type":"Basic",
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

### <a name="managed-identity"></a>Identità gestita

Specificare l'URI di risorsa per cui verrà richiesto il token di accesso usando l'identità gestita per la data factory. Per chiamare l'API di gestione delle risorse di Azure, usare `https://management.azure.com/`. Per altre informazioni sul funzionamento delle identità gestite, consultare la [pagina di panoramica sulle identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
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
