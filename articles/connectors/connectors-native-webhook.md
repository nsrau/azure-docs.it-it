---
title: Connettori webhook per App per la logica di Azure | Microsoft Docs
description: Come usare azioni e trigger webhook per eseguire azioni come Filtra matrice da app per la logica
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: fbfef291334109c6dcfcde80741874549fb7929f
ms.contentlocale: it-it
ms.lasthandoff: 04/10/2017

---
# <a name="get-started-with-the-webhook-connector"></a>Introduzione al connettore webhook

Con l'azione e il trigger webhook è possibile attivare, sospendere e riprendere flussi per eseguire queste attività:

* Eseguire l'attivazione da un [Hub eventi di Azure](https://github.com/logicappsio/EventHubAPI) alla ricezione di un elemento
* Attendere l'approvazione prima di continuare un flusso di lavoro

Altre informazioni su [come creare API personalizzate che supportano un webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Usare il trigger webhook

Un [*trigger*](connectors-overview.md) è un evento che avvia un flusso di lavoro nell'app per la logica. Un trigger webhook è basato su eventi e non si basa sul polling per nuovi elementi. Come il [trigger di richiesta](connectors-native-reqres.md), l'app per la logica viene attivata nel momento in cui si verifica un evento. Il trigger webhook registra un *URL di callback* a un servizio e usa l'URL per attivare l'app per la logica in base alle esigenze.

Ecco un esempio che mostra come configurare un trigger HTTP in Progettazione app per la logica. I passaggi presuppongono che un'API sia già stata distribuita o che si stia accedendo all'API che segue il [modello di "subscribe" e "unsubscribe" del webhook nelle app per la logica](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). La chiamata "subscribe" viene eseguita ogni volta che un'app per la logica viene salvata con un nuovo webhook o che passa da uno stato disabilitato ad abilitato. La chiamata "unsubscribe" viene eseguita quando un trigger webhook dell'app per la logica viene rimosso e salvato o passa dallo stato abilitato a disabilitato.

**Per aggiungere il trigger webhook**

1. Aggiungere il trigger **HTTP Webhook** come primo passaggio in un'app per la logica.
2. Specificare i parametri per le chiamate "subscribe" e "unsubscribe" del webhook.

   Questo passaggio segue lo stesso modello del formato dell'[azione HTTP](connectors-native-http.md).

     ![Trigger HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Aggiungere almeno un'azione.
4. Fare clic su **Salva** per pubblicare l'app per la logica. Questo passaggio chiama l'endpoint "subscribe" con l'URL di callback necessario per attivare questa app per la logica.
5. Ogni volta che il servizio esegue un `HTTP POST` all'URL di callback, viene attivata l'app per la logica, inclusi i dati passati nella richiesta.

## <a name="use-the-webhook-action"></a>Usare l'azione webhook

Un'[*azione*](connectors-overview.md) è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. Un'azione webhook registra un *URL di callback* con un servizio e attende che l'URL venga chiamato prima di riprendere le operazioni. ["Send Approval Email"](connectors-create-api-office365-outlook.md) è un esempio di connettore che segue questo modello. È possibile estendere questo modello in qualsiasi servizio tramite l'azione webhook. 

Ecco un esempio che mostra come configurare un azione webhook in Progettazione app per la logica. I passaggi presuppongono che un'API sia già stata distribuita o che si stia accedendo a un'API che segue il [modello di "subscribe" e "unsubscribe" del webhook usato nelle app per la logica](../logic-apps/logic-apps-create-api-app.md#webhook-actions). Quando un'app per la logica esegue l'azione webhook, viene eseguita la chiamata "subscribe". Quando un'esecuzione viene annullata in attesa di una risposta o prima che si verifichi il timeout dell'app per la logica, viene eseguita la chiamata "unsubscribe".

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

| Azione | Descrizione |
| --- | --- |
| HTTP Webhook |Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per attivare l'app per la logica in base alle esigenze. |

### <a name="trigger-details"></a>Dettagli del trigger

#### <a name="http-webhook"></a>HTTP Webhook

Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per attivare l'app per la logica in base alle esigenze.
L'asterisco (*) indica che il campo è obbligatorio.

| Nome visualizzato | Nome proprietà | Descrizione |
| --- | --- | --- |
| Subscribe Method* |statico |Metodo HTTP da usare per la richiesta di sottoscrizione |
| Subscribe URI* |Uri |URI HTTP da usare per la richiesta di sottoscrizione |
| Unsubscribe Method* |statico |Metodo HTTP da usare per annullare la richiesta di sottoscrizione |
| Unsubscribe URI* |Uri |URI HTTP da usare per annullare la richiesta di sottoscrizione |
| Subscribe Body |body |Request body HTTP per la sottoscrizione |
| Subscribe Headers |headers |Intestazioni della richiesta HTTP per la sottoscrizione |
| Subscribe Authentication |authentication |Autenticazione HTTP da usare per la sottoscrizione. Vedere [Connettore HTTP](connectors-native-http.md#authentication) per informazioni dettagliate |
| Unsubscribe Body |body |Request body HTTP per annullare la sottoscrizione |
| Unsubscribe Headers |headers |Intestazioni della richiesta HTTP per annullare la sottoscrizione |
| Unsubscribe Authentication |authentication |Autenticazione HTTP da usare per annullare la sottoscrizione. Vedere [Connettore HTTP](connectors-native-http.md#authentication) per informazioni dettagliate |

**Dettagli dell'output**

Richiesta Webhook

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| headers |object |Intestazioni della richiesta webhook |
| body |object |Oggetto della richiesta webhook |
| Codice di stato |int |Codice di stato della richiesta webhook |

## <a name="webhook-actions"></a>Azioni webhook

| Azione | Descrizione |
| --- | --- |
| HTTP Webhook |Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per riprendere un passaggio del flusso di lavoro in base alle esigenze. |

### <a name="action-details"></a>Informazioni dettagliate sulle azioni

#### <a name="http-webhook"></a>HTTP Webhook

Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per riprendere un passaggio del flusso di lavoro in base alle esigenze.
L'asterisco (*) indica che il campo è obbligatorio.

| Nome visualizzato | Nome proprietà | Descrizione |
| --- | --- | --- |
| Subscribe Method* |statico |Metodo HTTP da usare per la richiesta di sottoscrizione |
| Subscribe URI* |Uri |URI HTTP da usare per la richiesta di sottoscrizione |
| Unsubscribe Method* |statico |Metodo HTTP da usare per annullare la richiesta di sottoscrizione |
| Unsubscribe URI* |Uri |URI HTTP da usare per annullare la richiesta di sottoscrizione |
| Subscribe Body |body |Request body HTTP per la sottoscrizione |
| Subscribe Headers |headers |Intestazioni della richiesta HTTP per la sottoscrizione |
| Subscribe Authentication |authentication |Autenticazione HTTP da usare per la sottoscrizione. Vedere [Connettore HTTP](connectors-native-http.md#authentication) per informazioni dettagliate |
| Unsubscribe Body |body |Request body HTTP per annullare la sottoscrizione |
| Unsubscribe Headers |headers |Intestazioni della richiesta HTTP per annullare la sottoscrizione |
| Unsubscribe Authentication |authentication |Autenticazione HTTP da usare per annullare la sottoscrizione. Vedere [Connettore HTTP](connectors-native-http.md#authentication) per informazioni dettagliate |

**Dettagli dell'output**

Richiesta Webhook

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| headers |object |Intestazioni della richiesta webhook |
| body |object |Oggetto della richiesta webhook |
| Codice di stato |int |Codice di stato della richiesta webhook |

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Trovare altri connettori](apis-list.md)
