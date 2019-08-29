---
title: Attività della funzione di Azure in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare l'attività della funzione di Azure per eseguire una funzione di Azure in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 292fe858b85faef69b9df2dbdf54e7061ed56fa2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142500"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Attività della funzione di Azure in Azure Data Factory

L'attività della funzione di Azure consente di eseguire le [funzioni di Azure](../azure-functions/functions-overview.md) in una pipeline di Data Factory. Per eseguire una funzione di Azure, è necessario creare una connessione al servizio collegato e un'attività che specifica la funzione di Azure che si intende eseguire.

Per un'introduzione di otto minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Servizio collegato della funzione di Azure

Il tipo restituito della funzione di Azure deve essere un `JObject` valido. (Tenere presente che [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *non* è un `JObject`.) Qualsiasi tipo restituito diverso da `JObject` ha esito negativo e genera il contenuto della risposta di errore dell'utente *non è un JObject valido*.

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| --- | --- | --- |
| type   | La proprietà type deve essere impostata su: **AzureFunction** | sì |
| function app url | URL dell'app per le funzioni di Azure. Il formato è `https://<accountname>.azurewebsites.net`. Questo URL è il valore presente nella sezione **URL** quando si visualizza l'app per le funzioni nel portale di Azure  | sì |
| function key | Chiave di accesso per la funzione di Azure. Fare clic sulla sezione **Gestisci** per la funzione corrispondente e copiare il **tasto funzione** o la **chiave host**. Per altre informazioni, vedere: [Trigger e associazioni HTTP di Funzioni di Azure](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | sì |
|   |   |   |

## <a name="azure-function-activity"></a>Attività della funzione di Azure

| **Proprietà**  | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| --- | --- | --- | --- |
| name  | Nome dell'attività nella pipeline  | String | sì |
| type  | Il tipo di attività è 'AzureFunctionActivity' | String | sì |
| linked service | Servizio collegato della funzione di Azure collegata per l'app per le funzioni di Azure corrispondente  | Riferimento del servizio collegato | sì |
| function name  | Nome della funzione nell'app per le funzioni di Azure chiamata dall'attività | String | sì |
| statico  | Metodo dell'API REST per la chiamata di funzione | Tipi supportati di stringa: "GET", "POST", "PUT"   | sì |
| intestazione  | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare la lingua e il tipo in una richiesta: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Stringa (o un'espressione con l'elemento resultType della stringa) | No |
| body  | Corpo inviato insieme alla richiesta al metodo API della funzione  | Stringa (o espressione con l'elemento resultType della stringa) o oggetto.   | Obbligatorio per i metodi POST e PUT |
|   |   |   | |

Vedere lo schema del payload della richiesta nella sezione  [Schema del payload della richiesta](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Routing e query

L'attività di Funzione di Azure supporta il **routing**. Ad esempio, se la funzione di Azure ha l' `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`endpoint, il `functionName` da usare nell'attività funzione di Azure è `<functionName>/<value>`. È possibile parametrizzare questa funzione per fornire l' `functionName` oggetto desiderato in fase di esecuzione.

L'attività di Funzione di Azure supporta anche le **query**. È necessario includere una query come parte di `functionName`. Ad esempio, quando il nome della funzione `HttpTriggerCSharp` è e la query che si desidera includere è `name=hello`, è possibile costruire `functionName` nell'attività funzione di Azure come `HttpTriggerCSharp?name=hello`. Questa funzione può essere parametrizzata in modo che il valore possa essere determinato in fase di esecuzione.

## <a name="timeout-and-long-running-functions"></a>Funzioni di timeout e a esecuzione prolungata

Si verifica il timeout di funzioni di Azure dopo 230 secondi `functionTimeout` indipendentemente dall'impostazione configurata nelle impostazioni. Per altre informazioni, vedere [questo articolo](../azure-functions/functions-versions.md#timeout). Per ovviare a questo comportamento, seguire un modello asincrono o usare Durable Functions. Il vantaggio di Durable Functions è che offrono un proprio meccanismo di rilevamento dello stato, quindi non sarà necessario implementarne uno personalizzato.

Altre informazioni su Durable Functions in [questo articolo](../azure-functions/durable/durable-functions-overview.md). È possibile configurare un'attività funzione di Azure per chiamare la funzione durevole, che restituirà una risposta con un URI diverso, ad esempio [questo esempio](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Poiché `statusQueryGetUri` restituisce lo stato HTTP 202 mentre la funzione è in esecuzione, è possibile eseguire il polling dello stato della funzione utilizzando un'attività Web. È sufficiente configurare un'attività Web con il `url` campo impostato su `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Quando la funzione durevole viene completata, l'output della funzione sarà l'output dell'attività Web.


## <a name="sample"></a>Esempio

È possibile trovare un esempio di Data Factory che usa una funzione di Azure per estrarre il contenuto di un file tar [qui](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Passaggi successivi

Visualizzare altre informazioni sulle attività in Data Factory disponibili in [Pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md).
