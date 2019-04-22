---
title: Creare azioni o flussi di lavoro basati su eventi - App per la logica di Azure | Microsoft Docs
description: Automatizzare azioni o flussi di lavoro basati su eventi usando webhook e App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895580"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Creare azioni o flussi di lavoro basati su eventi usando webhook e App per la logica di Azure

Con l'azione e il trigger webhook è possibile attivare, sospendere e riprendere flussi per eseguire queste attività:

* Eseguire l'attivazione da un [Hub eventi di Azure](https://github.com/logicappsio/EventHubAPI) alla ricezione di un elemento
* Attendere l'approvazione prima di continuare un flusso di lavoro

Altre informazioni su [come creare API personalizzate che supportano un webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Usare il trigger webhook

Un [*trigger*](../connectors/apis-list.md) è un evento che avvia un flusso di lavoro nell'app per la logica. Il trigger webhook è basato su eventi, che non variano a polling per nuovi elementi. Quando si salva l'app per la logica con un trigger di webhook o quando si modifica l'app per la logica da disabilitato ad abilitato, il trigger webhook *sottoscrive* per il servizio specificato o l'endpoint registrando un *URL callback* con tale servizio o dell'endpoint. Il trigger Usa quindi tale URL per eseguire l'app per la logica in base alle esigenze. Ad esempio la [trigger di richiesta](connectors-native-reqres.md), app per la logica viene attivata immediatamente quando si verifica l'evento previsto. Il trigger *Annulla la sottoscrizione* se si rimuove il trigger e salvare l'app per la logica, o quando si modifica l'app per la logica da abilitato a disabilitato.

Ecco un esempio che mostra come configurare un trigger HTTP in Progettazione app per la logica. I passaggi presuppongono che un'API sia già stata distribuita o che si stia accedendo all'API che segue il [modello di "subscribe" e "unsubscribe" del webhook nelle app per la logica](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). 

**Per aggiungere il trigger webhook**

1. Aggiungere il trigger **HTTP Webhook** come primo passaggio in un'app per la logica.
2. Specificare i parametri per le chiamate "subscribe" e "unsubscribe" del webhook.

   Questo passaggio segue lo stesso modello del formato dell'[azione HTTP](connectors-native-http.md).

     ![Trigger HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Aggiungere almeno un'azione.
4. Fare clic su **Salva** per pubblicare l'app per la logica. Questo passaggio chiama l'endpoint "subscribe" con l'URL di callback necessario per attivare questa app per la logica.
5. Ogni volta che il servizio esegue un `HTTP POST` all'URL di callback, viene attivata l'app per la logica, inclusi i dati passati nella richiesta.

## <a name="use-the-webhook-action"></a>Usare l'azione webhook

Un' [ *azione* ](../connectors/apis-list.md) è un'operazione definita e l'esecuzione da parte del flusso di lavoro dell'app per la logica. Quando un'app per la logica viene eseguita un'azione webhook, tale azione *sottoscrive* per il servizio specificato o dell'endpoint tramite la registrazione di un *URL di callback* con tale servizio o dell'endpoint. Quindi l'azione di webhook attende che l'URL prima i curricula di app per la logica che esegue chiamate al servizio. Annulla la sottoscrizione dell'app per la logica del servizio o un endpoint in questi casi: 

* Quando l'azione webhook è stata completata correttamente
* Se l'esecuzione dell'app per la logica è stata annullata durante l'attesa di una risposta
* Prima la logica app verifica il timeout

Ad esempio, il [ **inviare posta elettronica di approvazione** ](connectors-create-api-office365-outlook.md) azione è riportato un esempio di azione webhook che segue questo modello. È possibile estendere questo modello in qualsiasi servizio tramite l'azione webhook. 

Ecco un esempio che mostra come configurare un azione webhook in Progettazione app per la logica. I passaggi presuppongono che un'API sia già stata distribuita o che si stia accedendo a un'API che segue il [modello di "subscribe" e "unsubscribe" del webhook usato nelle app per la logica](../logic-apps/logic-apps-create-api-app.md#webhook-actions). 

**Per aggiungere un'azione webhook**

1. Scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

2. Nella casella di ricerca digitare "webhook" per trovare l'azione **HTTP Webhook**.

    ![Selezionare l'azione di query](./media/connectors-native-webhook/using-action-1.png)

3. Specificare i parametri per le chiamate "subscribe" e "unsubscribe" del webhook

   Questo passaggio segue lo stesso modello del formato dell'[azione HTTP](connectors-native-http.md).

     ![Completare l'azione di query](./media/connectors-native-webhook/using-action-2.png)
   
   In fase di esecuzione, l'app per la logica chiama l'endpoint "subscribe" dopo il raggiungimento di tale passaggio.

4. Fare clic su **Salva** per pubblicare l'app per la logica.

## <a name="technical-details"></a>Dettagli tecnici

Di seguito altre informazioni sui trigger e sulle azioni che supporta webhook.

## <a name="webhook-triggers"></a>Trigger webhook

| Azione | DESCRIZIONE |
| --- | --- |
| HTTP Webhook |Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per attivare l'app per la logica in base alle esigenze. |

### <a name="trigger-details"></a>Dettagli del trigger

#### <a name="http-webhook"></a>HTTP Webhook

Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per attivare l'app per la logica in base alle esigenze.
L'asterisco (*) indica che il campo è obbligatorio.

| Nome visualizzato | Nome proprietà | DESCRIZIONE |
| --- | --- | --- |
| Subscribe Method* |statico |Metodo HTTP da usare per la richiesta di sottoscrizione |
| Subscribe URI* |Uri |URI HTTP da usare per la richiesta di sottoscrizione |
| Unsubscribe Method* |statico |Metodo HTTP da usare per annullare la richiesta di sottoscrizione |
| Unsubscribe URI* |Uri |URI HTTP da usare per annullare la richiesta di sottoscrizione |
| Subscribe Body |body |Request body HTTP per la sottoscrizione |
| Subscribe Headers |headers |Intestazioni della richiesta HTTP per la sottoscrizione |
| Subscribe Authentication |authentication |Autenticazione HTTP da usare per la sottoscrizione. Vedere [Connettore HTTP](connectors-native-http.md#authentication) per informazioni dettagliate |
| Unsubscribe Body |Corpo |Request body HTTP per annullare la sottoscrizione |
| Unsubscribe Headers |headers |Intestazioni della richiesta HTTP per annullare la sottoscrizione |
| Unsubscribe Authentication |authentication |Autenticazione HTTP da usare per annullare la sottoscrizione. Vedere [Connettore HTTP](connectors-native-http.md#authentication) per informazioni dettagliate |

**Dettagli dell'output**

Richiesta Webhook

| Nome proprietà | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| headers |object |Intestazioni della richiesta webhook |
| Corpo |object |Oggetto della richiesta webhook |
| Codice di stato |int |Codice di stato della richiesta webhook |

## <a name="webhook-actions"></a>Azioni webhook

| Azione | DESCRIZIONE |
| --- | --- |
| HTTP Webhook |Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per riprendere un passaggio del flusso di lavoro in base alle esigenze. |

### <a name="action-details"></a>Informazioni dettagliate sulle azioni

#### <a name="http-webhook"></a>HTTP Webhook

Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per riprendere un passaggio del flusso di lavoro in base alle esigenze.
L'asterisco (*) indica che il campo è obbligatorio.

| Nome visualizzato | Nome proprietà | DESCRIZIONE |
| --- | --- | --- |
| Subscribe Method* |statico |Metodo HTTP da usare per la richiesta di sottoscrizione |
| Subscribe URI* |Uri |URI HTTP da usare per la richiesta di sottoscrizione |
| Unsubscribe Method* |statico |Metodo HTTP da usare per annullare la richiesta di sottoscrizione |
| Unsubscribe URI* |Uri |URI HTTP da usare per annullare la richiesta di sottoscrizione |
| Subscribe Body |body |Request body HTTP per la sottoscrizione |
| Subscribe Headers |headers |Intestazioni della richiesta HTTP per la sottoscrizione |
| Subscribe Authentication |authentication |Autenticazione HTTP da usare per la sottoscrizione. Vedere [Connettore HTTP](connectors-native-http.md#authentication) per informazioni dettagliate |
| Unsubscribe Body |Corpo |Request body HTTP per annullare la sottoscrizione |
| Unsubscribe Headers |headers |Intestazioni della richiesta HTTP per annullare la sottoscrizione |
| Unsubscribe Authentication |authentication |Autenticazione HTTP da usare per annullare la sottoscrizione. Vedere [Connettore HTTP](connectors-native-http.md#authentication) per informazioni dettagliate |

**Dettagli dell'output**

Richiesta Webhook

| Nome proprietà | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| headers |object |Intestazioni della richiesta webhook |
| Corpo |object |Oggetto della richiesta webhook |
| Codice di stato |int |Codice di stato della richiesta webhook |

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Trovare altri connettori](apis-list.md)
