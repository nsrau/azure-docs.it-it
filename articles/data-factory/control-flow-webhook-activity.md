---
title: Attività Webhook in Azure Data Factory
description: L'attività webhook non continua l'esecuzione della pipeline finché non convalida il set di dati associato con determinati criteri specificati dall'utente.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417879"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Attività Webhook in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Un'attività webhook può controllare l'esecuzione di pipeline tramite il codice personalizzato. Con l'attività webhook, il codice dei clienti può chiamare un endpoint e passargli un URL di callback. L'esecuzione della pipeline attende la chiamata di callback prima di passare all'attività successiva.

## <a name="syntax"></a>Sintassi

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
**name** | Nome dell'attività webhook. | string | Sì |
**type** | Deve essere impostato su "WebHook". | string | Sì |
**Metodo** | Metodo dell'API REST per l'endpoint di destinazione. | Stringa. Il tipo supportato è "POST". | Sì |
**url** | Endpoint e percorso di destinazione. | Una stringa o un'espressione con il valore **resultType** di una stringa. | Sì |
**Intestazioni** | Intestazioni che vengono inviate alla richiesta. Di seguito è riportato un esempio che `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`imposta la lingua e la digitazione in una richiesta: . | Una stringa o un'espressione con il valore **resultType** di una stringa. | Sì. È `Content-Type` necessaria `"headers":{ "Content-Type":"application/json"}` un'intestazione come.A header like is required. |
**body** | Rappresenta il payload inviato all'endpoint. | JSON valido o un'espressione con il valore **resultType** di JSON. Vedere Schema di [payload](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) della richiesta per lo schema del payload della richiesta. | Sì |
**Autenticazione** | Metodo di autenticazione utilizzato per chiamare l'endpoint. I tipi supportati sono "Basic" e "ClientCertificate". Per altre informazioni, vedere [Autenticazione](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication). Se l'autenticazione non è necessaria, escludere questa proprietà. | Una stringa o un'espressione con il valore **resultType** di una stringa. | No |
**Timeout** | Il tempo di attesa dell'attività per richiamare il callback specificato da **callBackUri.** Il valore predefinito è 10 minuti ("00:10:00"). I valori hanno il formato TimeSpan *d*. *hh*:*mm*:*ss*. | string | No |
**Segnala stato sulla richiamata** | Consente a un utente di segnalare lo stato di errore di un'attività webhook. | Boolean | No |

## <a name="authentication"></a>Authentication

Un'attività webhook supporta i tipi di autenticazione seguenti.

### <a name="none"></a>nessuno

Se l'autenticazione non è necessaria, non includere la proprietà **di autenticazione.**

### <a name="basic"></a>Basic

Specificare il nome utente e la password da utilizzare con l'autenticazione di base.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificato client

Specificare il contenuto con codifica Base64 di un file PFX e una password.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identità gestita

Usare l'identità gestita della data factory per specificare l'URI della risorsa per cui viene richiesto il token di accesso. Per chiamare l'API di gestione delle risorse di Azure, usare `https://management.azure.com/`. Per altre informazioni sul funzionamento delle identità gestite, vedere Panoramica delle identità gestite per le risorse di Azure.For more information about how managed [identities work, see the managed identities for Azure resources overview](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se la data factory è configurata con un repository Git, è necessario archiviare le credenziali nell'insieme di credenziali delle chiavi di Azure per usare l'autenticazione di base o del certificato client. Azure Data Factory doesn't store passwords in Git.

## <a name="additional-notes"></a>Note aggiuntive

Data Factory passa la proprietà aggiuntiva **callBackUri** nel corpo inviato all'endpoint URL. Data Factory prevede che questo URI venga richiamato prima del valore di timeout specificato. Se l'URI non viene richiamato, l'attività ha esito negativo con lo stato "TimedOut".

L'attività webhook ha esito negativo quando la chiamata all'endpoint personalizzato ha esito negativo. Qualsiasi messaggio di errore può essere aggiunto al corpo del callback e utilizzato in un'attività successiva.

Per ogni chiamata all'API REST, il client scade se l'endpoint non risponde entro un minuto. Questo comportamento è la procedura consigliata HTTP standard. Per risolvere questo problema, implementare un modello 202. Nel caso corrente, l'endpoint restituisce 202 (Accettato) e il client esegue il polling.

Il timeout di un minuto nella richiesta non ha nulla a che fare con il timeout dell'attività. Quest'ultimo viene utilizzato per attendere il callback specificato da **callbackUri**.

Il corpo passato all'URI di callback deve essere JSON valido. Impostare l'intestazione `Content-Type` su `application/json`.

Quando si utilizza lo stato Report nella proprietà **di callback,** è necessario aggiungere il codice seguente al corpo quando si effettua il callback:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Passaggi successivi

Vedere le attività del flusso di controllo seguenti supportate da Data Factory:See the following control flow activities supported by Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Eseguire l'attività di pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere attività di metadati](control-flow-get-metadata-activity.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
