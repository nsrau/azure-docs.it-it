---
title: Attività della funzione di Azure in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare l'attività della funzione di Azure per eseguire una funzione di Azure in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 82786b8f01ce409179f4ddd37127679f9357cd0e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727039"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Attività della funzione di Azure in Azure Data Factory

L'attività della funzione di Azure consente di eseguire le [funzioni di Azure](../azure-functions/functions-overview.md) in una pipeline di Data Factory. Per eseguire una funzione di Azure, è necessario creare una connessione al servizio collegato e un'attività che specifica la funzione di Azure che si intende eseguire.

Per un'introduzione di otto minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Servizio collegato della funzione di Azure

Il tipo restituito della funzione di Azure deve essere un `JObject` valido. (Tenere presente che [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *non* è un `JObject`.) Diverso da un tipo restituito `JObject` ha esito negativo e viene generato l'errore utente *contenuto della risposta non è valido JObject*.

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| --- | --- | --- |
| type   | La proprietà type deve essere impostata su: **AzureFunction** | Sì |
| function app url | URL dell'app per le funzioni di Azure. Il formato è `https://<accountname>.azurewebsites.net`. Questo URL è il valore presente nella sezione **URL** quando si visualizza l'app per le funzioni nel portale di Azure  | Sì |
| function key | Chiave di accesso per la funzione di Azure. Fare clic sulla sezione **Gestisci** per la funzione corrispondente e copiare il **tasto funzione** o la **chiave host**. Per altre informazioni, vedere: [Trigger e associazioni HTTP di Funzioni di Azure](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | Sì |
|   |   |   |

## <a name="azure-function-activity"></a>Attività della funzione di Azure

| **Proprietà**  | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| --- | --- | --- | --- |
| name  | Nome dell'attività nella pipeline  | string | Sì |
| type  | Il tipo di attività è 'AzureFunctionActivity' | string | Sì |
| linked service | Servizio collegato della funzione di Azure collegata per l'app per le funzioni di Azure corrispondente  | Riferimento del servizio collegato | Sì |
| function name  | Nome della funzione nell'app per le funzioni di Azure chiamata dall'attività | string | Sì |
| method  | Metodo dell'API REST per la chiamata di funzione | Tipi supportati di stringa: "GET", "POST", "PUT"   | Sì |
| intestazione  | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare la lingua e il tipo in una richiesta: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Stringa (o un'espressione con l'elemento resultType della stringa) | No  |
| Corpo  | Corpo inviato insieme alla richiesta al metodo API della funzione  | Stringa (o espressione con l'elemento resultType della stringa) o oggetto.   | Obbligatorio per i metodi POST e PUT |
|   |   |   | |

Vedere lo schema del payload della richiesta nella sezione  [Schema del payload della richiesta](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Routing e le query

L'attività di Funzione di Azure supporta il **routing**. Ad esempio, se la funzione di Azure è l'endpoint `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, il `functionName` da utilizzare nell'attività di funzione di Azure è `<functionName>/<value>`. È possibile parametrizzare questa funzione per fornire il valore desiderato `functionName` in fase di esecuzione.

L'attività di Funzione di Azure supporta anche le **query**. Deve essere incluso come parte di una query di `functionName`. Ad esempio, quando è il nome della funzione `HttpTriggerCSharp` e la query che si desidera includere `name=hello`, quindi è possibile costruire il `functionName` nell'attività di funzione di Azure come `HttpTriggerCSharp?name=hello`. Questa funzione può essere parametrizzata in modo che il valore può essere determinato in fase di esecuzione.

## <a name="timeout-and-long-running-functions"></a>Timeout e funzioni con esecuzione prolungata

Azure funzioni timeout dopo 230 secondi indipendentemente il `functionTimeout` configurate nelle impostazioni dell'impostazione. Per altre informazioni, vedere [questo articolo](../azure-functions/functions-versions.md#timeout). Per risolvere questo problema, seguire un modello asincrono o usare funzioni permanenti. Il vantaggio di funzioni permanenti è che offrono un proprio meccanismo di monitoraggio dello stato, in modo da non dover implementare soluzioni personalizzate.

Altre informazioni su funzioni permanenti [questo articolo](../azure-functions/durable/durable-functions-overview.md). È possibile configurare un'attività di funzione di Azure per chiamare la funzione durevole, che restituirà una risposta con un URI diverso, ad esempio [in questo esempio](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Poiché `statusQueryGetUri` restituisce HTTP 202 stato mentre la funzione è in esecuzione, è possibile eseguire il polling lo stato della funzione usando un'attività Web. È sufficiente configurare un'attività Web con il `url` campo impostato su `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Al termine, la funzione permanente l'output della funzione sarà l'output dell'attività Web.


## <a name="next-steps"></a>Passaggi successivi

Visualizzare altre informazioni sulle attività in Data Factory disponibili in [Pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md).
