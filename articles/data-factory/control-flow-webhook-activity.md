---
title: Attività webhook in Azure Data Factory
description: L'attività webhook non continua l'esecuzione della pipeline fino a quando non convalida il set di dati collegato con determinati criteri specificati dall'utente.
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
ms.openlocfilehash: 1ce41a5928d5b8a7c7df439ce5321cd15f0cc1d5
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634981"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Attività webhook in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Un'attività webhook può controllare l'esecuzione delle pipeline tramite il codice personalizzato. Con l'attività webhook, il codice dei clienti può chiamare un endpoint e passargli un URL di callback. L'esecuzione della pipeline attende la chiamata di callback prima di procedere all'attività successiva.

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
**name** | Nome dell'attività del webhook. | string | Sì |
**type** | Deve essere impostato su "webhook". | string | Sì |
**Metodo** | Metodo dell'API REST per l'endpoint di destinazione. | Stringa. Il tipo supportato è "POST". | Sì |
**url** | Endpoint e percorso di destinazione. | Stringa o espressione con il valore **ResultType** di una stringa. | Sì |
**intestazioni** | Intestazioni che vengono inviate alla richiesta. Di seguito è riportato un esempio che imposta il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Stringa o espressione con il valore **ResultType** di una stringa. | Sì. `Content-Type`È necessaria un'intestazione come `"headers":{ "Content-Type":"application/json"}` . |
**body** | Rappresenta il payload inviato all'endpoint. | JSON valido o espressione con valore **RESULTTYPE** JSON. Vedere [schema del payload della richiesta](./control-flow-web-activity.md#request-payload-schema) per lo schema del payload della richiesta. | Sì |
**autenticazione** | Metodo di autenticazione usato per chiamare l'endpoint. I tipi supportati sono "Basic" e "ClientCertificate". Per altre informazioni, vedere [Autenticazione](./control-flow-web-activity.md#authentication). Se l'autenticazione non è necessaria, escludere questa proprietà. | Stringa o espressione con il valore **ResultType** di una stringa. | No |
**timeout** | Tempo di attesa dell'attività affinché venga richiamato il callback specificato da **callBackUri** . Il valore predefinito è 10 minuti ("00:10:00"). I valori hanno il formato TimeSpan *d* . *HH* : *mm* : *SS* . | string | No |
**Segnala stato del callback** | Consente a un utente di segnalare lo stato di errore di un'attività webhook. | Boolean | No |

## <a name="authentication"></a>Authentication

Un'attività webhook supporta i tipi di autenticazione seguenti.

### <a name="none"></a>nessuno

Se l'autenticazione non è necessaria, non includere la proprietà di **autenticazione** .

### <a name="basic"></a>Basic

Specificare il nome utente e la password da usare con l'autenticazione di base.

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

Usare l'identità gestita del data factory per specificare l'URI della risorsa per cui viene richiesto il token di accesso. Per chiamare l'API di gestione delle risorse di Azure, usare `https://management.azure.com/`. Per altre informazioni sul funzionamento delle identità gestite, vedere [Cenni preliminari sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se la data factory è configurata con un repository git, è necessario archiviare le credenziali in Azure Key Vault per usare l'autenticazione di base o del certificato client. Azure Data Factory non archivia le password in git.

## <a name="additional-notes"></a>Note aggiuntive

Data Factory passa la proprietà aggiuntiva **callBackUri** nel corpo inviato all'endpoint dell'URL. Data Factory prevede che questo URI venga richiamato prima del valore di timeout specificato. Se l'URI non viene richiamato, l'attività ha esito negativo con lo stato "timeout".

L'attività del webhook ha esito negativo quando la chiamata all'endpoint personalizzato ha esito negativo. Qualsiasi messaggio di errore può essere aggiunto al corpo di callback e utilizzato in un'attività successiva.

Per ogni chiamata all'API REST, si verifica il timeout del client se l'endpoint non risponde entro un minuto. Questo comportamento è la procedura consigliata standard HTTP. Per risolvere il problema, implementare uno schema 202. Nel caso corrente, l'endpoint restituisce 202 (accettato) e il client esegue il polling.

Il timeout di un minuto per la richiesta non ha nulla a che fare con il timeout dell'attività. Quest'ultimo viene usato per attendere il callback specificato da **callbackUri** .

Il corpo passato all'URI di callback deve essere JSON valido. Impostare l'intestazione `Content-Type` su `application/json`.

Quando si utilizza la proprietà **report status on callback** , è necessario aggiungere il codice seguente al corpo quando si effettua il callback:

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

Vedere le seguenti attività del flusso di controllo supportate da Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Eseguire l'attività di pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere attività di metadati](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)