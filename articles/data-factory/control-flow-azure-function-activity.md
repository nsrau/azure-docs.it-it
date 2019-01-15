---
title: Attività della funzione di Azure in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare l'attività della funzione di Azure per eseguire una funzione di Azure in una pipeline di Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: 0fd9b679411dd06336fd65bcb400b152316146fa
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188641"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Attività della funzione di Azure in Azure Data Factory

L'attività della funzione di Azure consente di eseguire le [funzioni di Azure](../azure-functions/functions-overview.md) in una pipeline di Data Factory. Per eseguire una funzione di Azure, è necessario creare una connessione al servizio collegato e un'attività che specifica la funzione di Azure che si intende eseguire.

## <a name="azure-function-linked-service"></a>Servizio collegato della funzione di Azure

Il tipo restituito della funzione di Azure deve essere un `JObject` valido. (Tenere presente che [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *non* è un `JObject`.) Qualsiasi altro tipo restituito diverso da `JObject` non riesce e genera l'errore utente generico *Error calling endpoint* (Errore durante la chiamata all'endpoint).

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
| statico  | Metodo dell'API REST per la chiamata di funzione | Tipi supportati di stringa: "GET", "POST", "PUT"   | Sì |
| intestazione  | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare la lingua e il tipo in una richiesta: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Stringa (o un'espressione con l'elemento resultType della stringa) | No  |
| Corpo  | Corpo inviato insieme alla richiesta al metodo API della funzione  | Stringa (o espressione con l'elemento resultType della stringa) o oggetto.   | Obbligatorio per i metodi POST e PUT |
|   |   |   | |

Vedere lo schema del payload della richiesta nella sezione  [Schema del payload della richiesta](control-flow-web-activity.md#request-payload-schema) .

## <a name="more-info"></a>Altre informazioni

L'attività di Funzione di Azure supporta il **routing**. Ad esempio, se l'app usa il routing seguente: `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>`, `functionName` è `functionName/{value}`, che può essere parametrizzato per fornire il `functionName` desiderato in fase di esecuzione.

L'attività di Funzione di Azure supporta anche le **query**. Una query deve far parte del `functionName`, ad esempio in `HttpTriggerCSharp2?name=hello`, dove il `function name` è `HttpTriggerCSharp2`.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare altre informazioni sulle attività in Data Factory disponibili in [Pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md).
