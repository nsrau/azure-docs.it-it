---
title: Attendere e rispondere agli eventi
description: Automatizzare i flussi di lavoro che attivano, sospendono e riprendono in base agli eventi in un endpoint del servizio usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656289"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Creare ed eseguire flussi di lavoro automatizzati basati su eventi usando webhook HTTP in app per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il connettore http webhook incorporato è possibile automatizzare i flussi di lavoro in attesa ed esecuzione in base a eventi specifici che si verificano in un endpoint HTTP o HTTPS compilando app per la logica. Ad esempio, è possibile creare un'app per la logica che esegue il monitoraggio di un endpoint del servizio attendendo un evento specifico prima di attivare il flusso di lavoro ed eseguendo le azioni specificate, invece di eseguire regolarmente il controllo o il *polling* dell'endpoint.

Di seguito sono riportati alcuni flussi di lavoro basati su eventi di esempio:

* Attendere l'arrivo di un elemento da un [Hub eventi di Azure](https://github.com/logicappsio/EventHubAPI) prima di avviare un'esecuzione dell'app per la logica.
* Attendere un'approvazione prima di continuare con un flusso di lavoro.

## <a name="how-do-webhooks-work"></a>Come funzionano i webhook?

Un trigger webhook HTTP è basato su eventi, che non dipende dal controllo o dal polling regolarmente per i nuovi elementi. Quando si salva un'app per la logica che inizia con un trigger webhook o quando si modifica l'app per la logica da disabilitato ad abilitato, il trigger webhook *sottoscrive* un servizio o un endpoint specifico registrando un *URL di callback* con tale servizio o endpoint. Il trigger attende quindi il servizio o l'endpoint per chiamare l'URL, che avvia l'esecuzione dell'app per la logica. Analogamente al [trigger di richiesta](connectors-native-reqres.md), l'app per la logica viene attivata immediatamente quando si verifica l'evento specificato. Il trigger *Annulla la sottoscrizione* dal servizio o dall'endpoint se si rimuove il trigger e si salva l'app per la logica o quando si modifica l'app per la logica da abilitato a disabilitato.

Un'azione webhook HTTP è inoltre basata su eventi e *sottoscrive* un servizio o un endpoint specifico registrando un URL di *callback* con tale servizio o endpoint. L'azione webhook sospende il flusso di lavoro dell'app per la logica e attende fino a quando il servizio o l'endpoint chiama l'URL prima che l'app per la logica riprende l'esecuzione. In questi casi, l'app per la logica di azione *Annulla la sottoscrizione* del servizio o dell'endpoint:

* Quando l'azione del webhook viene completata correttamente
* Se l'esecuzione dell'app per la logica viene annullata durante l'attesa di una risposta
* Prima del timeout dell'app per la logica

Ad esempio, l'azione [**Invia messaggio di posta elettronica di approvazione**](connectors-create-api-office365-outlook.md) di Office 365 Outlook Connector è un esempio di azione webhook che segue questo modello. È possibile estendere questo modello in qualsiasi servizio usando l'azione webhook.

> [!NOTE]
> App per la logica impone Transport Layer Security (TLS) 1,2 quando riceve la chiamata al trigger o all'azione del webhook HTTP. Se vengono visualizzati errori di handshake TLS, assicurarsi di usare TLS 1,2. Per le chiamate in ingresso, di seguito sono riportati i pacchetti di crittografia supportati:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Per altre informazioni, vedere gli argomenti seguenti:

* [Parametri del trigger webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook e sottoscrizioni](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Creare API personalizzate che supportano un webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* URL per un endpoint o un'API già distribuita che supporta il modello di sottoscrizione e annullamento della sottoscrizione webhook per i [trigger di Webhook in app](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) per la logica o [azioni webhook nelle app](../logic-apps/logic-apps-create-api-app.md#webhook-actions) per la logica in base alle esigenze

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

* App per la logica in cui si vuole attendere eventi specifici nell'endpoint di destinazione. Per iniziare con il trigger HTTP webhook, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare l'azione HTTP webhook, avviare l'app per la logica con tutti i trigger desiderati. Questo esempio usa il trigger HTTP come primo passaggio.

## <a name="add-an-http-webhook-trigger"></a>Aggiungere un trigger HTTP webhook

Questo trigger predefinito chiama l'endpoint di sottoscrizione nel servizio di destinazione e registra un URL di callback con il servizio di destinazione. L'app per la logica attende quindi che il servizio di destinazione invii `HTTP POST` una richiesta all'URL di callback. Quando si verifica questo evento, il trigger viene attivato e passa tutti i dati nella richiesta insieme al flusso di lavoro.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in progettazione app per la logica.

1. Nella casella di ricerca della finestra di progettazione `http webhook` immettere come filtro. Dall'elenco **trigger** selezionare il trigger **http webhook** .

   ![Selezionare il trigger HTTP webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Questo esempio rinomina il trigger in in `HTTP Webhook trigger` modo che il passaggio abbia un nome più descrittivo. Inoltre, nell'esempio in seguito viene aggiunta un'azione webhook HTTP ed entrambi i nomi devono essere univoci.

1. Specificare i valori per i [parametri del trigger del webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) che si vuole usare per le chiamate di sottoscrizione e annullamento della sottoscrizione.

   In questo esempio, il trigger include i metodi, gli URI e i corpi dei messaggi da usare quando si eseguono le operazioni di sottoscrizione e annullamento della sottoscrizione.

   ![Immettere i parametri del trigger webhook HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Subscription-metodo** | Sì | Metodo da usare quando si sottoscrive l'endpoint di destinazione |
   | **Subscribe-URI** | Sì | URL da utilizzare per la sottoscrizione all'endpoint di destinazione |
   | **Sottoscrivi corpo** | No | Corpo del messaggio da includere nella richiesta di sottoscrizione. Questo esempio include l'URL di callback che identifica in modo univoco il Sottoscrittore, ovvero l'app per la `@listCallbackUrl()` logica, usando l'espressione per recuperare l'URL di callback dell'app per la logica. |
   | **Unsubscribe-metodo** | No | Metodo da utilizzare per l'annullamento della sottoscrizione dall'endpoint di destinazione |
   | **Annulla sottoscrizione-URI** | No | URL da utilizzare per l'annullamento della sottoscrizione dall'endpoint di destinazione |
   | **Unsubscribe-corpo** | No | Corpo del messaggio facoltativo da includere nella richiesta di annullamento della sottoscrizione <p><p>**Nota**: questa proprietà non supporta l'uso `listCallbackUrl()` della funzione. Tuttavia, il trigger include automaticamente e invia le intestazioni, `x-ms-client-tracking-id` e `x-ms-workflow-operation-name`, che il servizio di destinazione può utilizzare per identificare in modo univoco il Sottoscrittore. |
   ||||

1. Per aggiungere altre proprietà del trigger, aprire l'elenco **Aggiungi nuovo parametro** .

   ![Aggiungere altre proprietà del trigger](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Ad esempio, se è necessario usare l'autenticazione di, è possibile aggiungere le proprietà **Subscribe-Authentication** e **unsubscribe-Authentication** . Per altre informazioni sui tipi di autenticazione disponibili per il webhook HTTP, vedere [aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Il salvataggio dell'app per la logica chiama l'endpoint di sottoscrizione nel servizio di destinazione e registra l'URL di callback. L'app per la logica attende quindi che il servizio di destinazione invii `HTTP POST` una richiesta all'URL di callback. Quando si verifica questo evento, il trigger viene attivato e passa tutti i dati nella richiesta insieme al flusso di lavoro. Se questa operazione viene completata correttamente, il trigger Annulla la sottoscrizione dall'endpoint e l'app per la logica continua il flusso di lavoro rimanente.

## <a name="add-an-http-webhook-action"></a>Aggiungere un'azione HTTP webhook

Questa azione predefinita chiama l'endpoint di sottoscrizione nel servizio di destinazione e registra un URL di callback con il servizio di destinazione. L'app per la logica sospende quindi il servizio di destinazione per inviare una `HTTP POST` richiesta all'URL callback. Quando si verifica questo evento, l'azione passa tutti i dati nella richiesta insieme al flusso di lavoro. Se l'operazione viene completata correttamente, l'azione annulla la sottoscrizione dall'endpoint e l'app per la logica continua a eseguire il flusso di lavoro rimanente.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

   Questo esempio usa il trigger HTTP webhook come primo passaggio.

1. Nel passaggio in cui si vuole aggiungere l'azione HTTP webhook selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più (**+**) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca della finestra di progettazione `http webhook` immettere come filtro. Nell'elenco **azioni** selezionare l'azione **webhook http** .

   ![Selezionare l'azione HTTP webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   Questo esempio rinomina l'azione in "azione webhook HTTP" in modo che il passaggio abbia un nome più descrittivo.

1. Specificare i valori per i parametri dell'azione webhook HTTP, che sono simili ai [parametri del trigger webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)che si vuole usare per le chiamate di sottoscrizione e annullamento della sottoscrizione.

   In questo esempio, l'azione include i metodi, gli URI e i corpi dei messaggi da usare quando si eseguono le operazioni di sottoscrizione e annullamento della sottoscrizione.

   ![Immettere i parametri dell'azione webhook HTTP](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Subscription-metodo** | Sì | Metodo da usare quando si sottoscrive l'endpoint di destinazione |
   | **Subscribe-URI** | Sì | URL da utilizzare per la sottoscrizione all'endpoint di destinazione |
   | **Sottoscrivi corpo** | No | Corpo del messaggio da includere nella richiesta di sottoscrizione. Questo esempio include l'URL di callback che identifica in modo univoco il Sottoscrittore, ovvero l'app per la `@listCallbackUrl()` logica, usando l'espressione per recuperare l'URL di callback dell'app per la logica. |
   | **Unsubscribe-metodo** | No | Metodo da utilizzare per l'annullamento della sottoscrizione dall'endpoint di destinazione |
   | **Annulla sottoscrizione-URI** | No | URL da utilizzare per l'annullamento della sottoscrizione dall'endpoint di destinazione |
   | **Unsubscribe-corpo** | No | Corpo del messaggio facoltativo da includere nella richiesta di annullamento della sottoscrizione <p><p>**Nota**: questa proprietà non supporta l'uso `listCallbackUrl()` della funzione. Tuttavia, l'azione include automaticamente e invia le intestazioni, `x-ms-client-tracking-id` e `x-ms-workflow-operation-name`, che il servizio di destinazione può utilizzare per identificare in modo univoco il Sottoscrittore. |
   ||||

1. Per aggiungere altre proprietà dell'azione, aprire l'elenco **Aggiungi nuovo parametro** .

   ![Aggiungi altre proprietà azione](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Ad esempio, se è necessario usare l'autenticazione di, è possibile aggiungere le proprietà **Subscribe-Authentication** e **unsubscribe-Authentication** . Per altre informazioni sui tipi di autenticazione disponibili per il webhook HTTP, vedere [aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   A questo punto, quando viene eseguita questa azione, l'app per la logica chiama l'endpoint di sottoscrizione nel servizio di destinazione e registra l'URL di callback. L'app per la logica sospende quindi il flusso di lavoro e attende che il servizio di destinazione `HTTP POST` invii una richiesta all'URL callback. Quando si verifica questo evento, l'azione passa tutti i dati nella richiesta insieme al flusso di lavoro. Se l'operazione viene completata correttamente, l'azione annulla la sottoscrizione dall'endpoint e l'app per la logica continua a eseguire il flusso di lavoro rimanente.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per altre informazioni sui parametri trigger e Action, che sono simili tra loro, vedere [parametri del webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Dettagli dell'output

Di seguito sono riportate altre informazioni sugli output di un trigger o un'azione HTTP webhook che restituisce queste informazioni:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| headers | object | Intestazioni della richiesta |
| Corpo | object | Oggetto JSON | Oggetto con il contenuto del corpo della richiesta |
| Codice di stato | INT | Codice di stato della richiesta |
|||

| Codice di stato | Descrizione |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Richiesta non valida |
| 401 | Non autorizzata |
| 403 | Accesso negato |
| 404 | Non trovato |
| 500 | Errore interno del server. Si è verificato un errore sconosciuto. |
|||

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
