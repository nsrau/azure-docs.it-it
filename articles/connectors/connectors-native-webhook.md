---
title: Attendere e rispondere agli eventi
description: Automatizzare i flussi di lavoro che attivano, sospendeno e riprendono in base agli eventi in un endpoint del servizio usando app per la logica di AzureAutomate workflows that trigger, pause, and resume based on events at a service endpoint by using Azure Logic Apps
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
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Creare ed eseguire flussi di lavoro automatizzati basati su eventi usando webhook HTTP in App per la logica di AzureCreate and run automated event-based workflows by using HTTP webhooks in Azure Logic Apps

Con [le app per](../logic-apps/logic-apps-overview.md) la logica di Azure e il connettore HTTP Webhook incorporato, è possibile automatizzare i flussi di lavoro che attendono ed eseguiti in base a eventi specifici che si verificano in un endpoint HTTP o HTTPS mediante la creazione di app per la logica. Ad esempio, è possibile creare un'app per la logica che monitora un endpoint del servizio in attesa di un evento specifico prima di attivare il flusso di lavoro ed eseguire le azioni specificate, anziché controllare o *eseguire* regolarmente tale endpoint.

Di seguito sono riportati alcuni flussi di lavoro basati su eventi di esempio:Here are some example event-based workflows:

* Attendere l'arrivo di un elemento da un [hub eventi](https://github.com/logicappsio/EventHubAPI) di Azure prima di attivare l'esecuzione di un'app per la logica.
* Attendere un'approvazione prima di continuare un flusso di lavoro.

## <a name="how-do-webhooks-work"></a>Come funzionano i webhook?

Un trigger webhook HTTP è basato su eventi, che non dipende dal controllo o dal polling periodicamente della ricerca di nuovi elementi. Quando si salva un'app per la logica che inizia con un trigger webhook o quando si modifica l'app per la logica da disabilitata ad abilitata, il trigger webhook *sottoscrive* un servizio o un endpoint specifico registrando un URL di *callback* con tale servizio o endpoint. Il trigger attende quindi che il servizio o l'endpoint chiami l'URL, che avvia l'esecuzione dell'app per la logica. Simile al [trigger Di richiesta](connectors-native-reqres.md), l'app per la logica viene attivata immediatamente quando si verifica l'evento specificato. Il trigger annulla la *sottoscrizione* dal servizio o dall'endpoint se si rimuove il trigger e si salva l'app per la logica oppure quando si modifica l'app per la logica da abilitata a disabilitata.

Un'azione webhook HTTP è anche basata su eventi e *sottoscrive* un servizio o un endpoint specifico registrando un URL di *callback* con tale servizio o endpoint. L'azione webhook sospende il flusso di lavoro dell'app per la logica e attende che il servizio o l'endpoint chiami l'URL prima che l'app per la logica riprenda l'esecuzione. L'app per la logica delle azioni annulla la sottoscrizione dal servizio o dall'endpoint nei seguenti casi:The action logic app *unsubscribes* from the service or endpoint in these cases:

* Quando l'azione webhook termina correttamente
* Se l'esecuzione dell'app per la logica viene annullata durante l'attesa di una risposta
* Prima del timeout dell'app per la logica

Ad esempio, l'azione [**di posta elettronica invia approvazione**](connectors-create-api-office365-outlook.md) del connettore di Outlook di Office 365 è un esempio di azione webhook che segue questo modello. È possibile estendere questo modello in qualsiasi servizio usando l'azione webhook.You can extend this pattern into any service by using the webhook action.

> [!NOTE]
> App per la logica applica Transport Layer Security (TLS) 1.2 quando si riceve la chiamata al trigger webhook HTTP o all'azione. Se vengono visualizzati errori di handshake TLS, assicurarsi di utilizzare TLS 1.2. Per le chiamate in arrivo, ecco le suite di crittografia supportate:
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

* [Parametri trigger Webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook e sottoscrizioni](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Creare API personalizzate che supportano un webhookCreate custom APIs that support a webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* URL per un endpoint o un'API già distribuita che supporta il modello di sottoscrizione e annullamento della sottoscrizione del webhook per [i trigger di webhook nelle app per la logica](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) o le azioni [webhook nelle app per la logica](../logic-apps/logic-apps-create-api-app.md#webhook-actions) in base alle esigenze

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

* App per la logica in cui si vuole attendere eventi specifici nell'endpoint di destinazione. Per iniziare con il trigger HTTP Webhook, [creare un'app per la logica vuota.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Per usare l'azione HTTP Webhook, avviare l'app per la logica con il trigger desiderato. In questo esempio viene utilizzato il trigger HTTP come primo passaggio.

## <a name="add-an-http-webhook-trigger"></a>Aggiungere un trigger Webhook HTTP

Questo trigger predefinito chiama l'endpoint di sottoscrizione nel servizio di destinazione e registra un URL di callback con il servizio di destinazione. L'app per la logica attende `HTTP POST` quindi che il servizio di destinazione invii una richiesta all'URL di callback. Quando si verifica questo evento, il trigger viene attivato e passa tutti i dati nella richiesta al flusso di lavoro.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in Progettazione app per la logica.

1. Nella casella di ricerca della `http webhook` finestra di progettazione immettere il filtro. Nell'elenco **Trigger** selezionare il trigger **HTTP Webhook.**

   ![Selezionare il trigger Http Webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   In questo esempio il `HTTP Webhook trigger` trigger viene rinominato in modo che il passaggio abbia un nome più descrittivo. Inoltre, nell'esempio viene aggiunta un'azione Webhook HTTP ed entrambi i nomi devono essere univoci.

1. Specificare i valori per i [parametri del trigger HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) che si desidera utilizzare per le chiamate di sottoscrizione e annullamento della sottoscrizione.

   In questo esempio, il trigger include i metodi, gli URI e i corpi dei messaggi da utilizzare quando si eseguono le operazioni di sottoscrizione e annullamento della sottoscrizione.

   ![Immettere i parametri del trigger HTTP Webhook](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Abbonamento - Metodo** | Sì | Metodo da utilizzare per la sottoscrizione all'endpoint di destinazione |
   | **Sottoscrivi - URI** | Sì | URL da utilizzare per la sottoscrizione all'endpoint di destinazione |
   | **Iscriviti - Corpo** | No | Qualsiasi corpo del messaggio da includere nella richiesta di sottoscrizione. Questo esempio include l'URL di callback che identifica in modo `@listCallbackUrl()` univoco il sottoscrittore, ovvero l'app per la logica, usando l'espressione per recuperare l'URL di callback dell'app per la logica. |
   | **Annulla sottoscrizione - Metodo** | No | Metodo da utilizzare quando si annulla la sottoscrizione dall'endpoint di destinazione |
   | **Annullamento sottoscrizione - URI** | No | URL da utilizzare per l'annullamento della sottoscrizione dall'endpoint di destinazione |
   | **Annulla iscrizione - Corpo** | No | Corpo del messaggio facoltativo da includere nella richiesta di annullamento della sottoscrizioneAn optional message body to include in the unsubscribe request <p><p>**Nota:** questa proprietà non `listCallbackUrl()` supporta l'utilizzo della funzione. Tuttavia, il trigger include e `x-ms-client-tracking-id` `x-ms-workflow-operation-name`invia automaticamente le intestazioni e , che il servizio di destinazione può utilizzare per identificare in modo univoco il sottoscrittore. |
   ||||

1. Per aggiungere altre proprietà del trigger, aprire l'elenco **Aggiungi nuovo parametro.**

   ![Aggiungere altre proprietà del triggerAdd more trigger properties](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Ad esempio, se è necessario utilizzare l'autenticazione, è possibile aggiungere le proprietà **Subscribe - Authentication** e **Unsubscribe - Authentication.** Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP Webhook, vedere [Aggiungere l'autenticazione alle chiamate in uscita.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Il salvataggio dell'app per la logica chiama l'endpoint di sottoscrizione nel servizio di destinazione e registra l'URL di callback. L'app per la logica attende `HTTP POST` quindi che il servizio di destinazione invii una richiesta all'URL di callback. Quando si verifica questo evento, il trigger viene attivato e passa tutti i dati nella richiesta al flusso di lavoro. Se questa operazione viene completata correttamente, il trigger annulla la sottoscrizione dall'endpoint e l'app per la logica continua il flusso di lavoro rimanente.

## <a name="add-an-http-webhook-action"></a>Aggiungere un'azione Webhook HTTP

Questa azione predefinita chiama l'endpoint di sottoscrizione nel servizio di destinazione e registra un URL di callback con il servizio di destinazione. L'app per la logica quindi viene sospesa e attende che il servizio di destinazione invii una `HTTP POST` richiesta all'URL di callback. Quando si verifica questo evento, l'azione passa tutti i dati nella richiesta al flusso di lavoro. Se l'operazione viene completata correttamente, l'azione annulla la sottoscrizione all'endpoint e l'app per la logica continua a eseguire il flusso di lavoro rimanente.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

   In questo esempio viene utilizzato il trigger HTTP Webhook come primo passaggio.

1. Nel passaggio in cui si desidera aggiungere l'azione HTTP Webhook selezionare **Nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca della `http webhook` finestra di progettazione immettere il filtro. Nell'elenco **Azioni** selezionare l'azione **HTTP Webhook.**

   ![Selezionare l'azione Webhook HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   In questo esempio l'azione viene rinominata in "HTTP Webhook action" in modo che il passaggio abbia un nome più descrittivo.

1. Specificare i valori per i parametri dell'azione HTTP Webhook, simili ai parametri del [trigger HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger), che si desidera utilizzare per le chiamate di sottoscrizione e annullamento della sottoscrizione.

   In questo esempio, l'azione include i metodi, gli URI e i corpi dei messaggi da utilizzare quando si eseguono le operazioni di sottoscrizione e annullamento della sottoscrizione.

   ![Immettere i parametri dell'azione HTTP Webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Abbonamento - Metodo** | Sì | Metodo da utilizzare per la sottoscrizione all'endpoint di destinazione |
   | **Sottoscrivi - URI** | Sì | URL da utilizzare per la sottoscrizione all'endpoint di destinazione |
   | **Iscriviti - Corpo** | No | Qualsiasi corpo del messaggio da includere nella richiesta di sottoscrizione. Questo esempio include l'URL di callback che identifica in modo `@listCallbackUrl()` univoco il sottoscrittore, ovvero l'app per la logica, usando l'espressione per recuperare l'URL di callback dell'app per la logica. |
   | **Annulla sottoscrizione - Metodo** | No | Metodo da utilizzare quando si annulla la sottoscrizione dall'endpoint di destinazione |
   | **Annullamento sottoscrizione - URI** | No | URL da utilizzare per l'annullamento della sottoscrizione dall'endpoint di destinazione |
   | **Annulla iscrizione - Corpo** | No | Corpo del messaggio facoltativo da includere nella richiesta di annullamento della sottoscrizioneAn optional message body to include in the unsubscribe request <p><p>**Nota:** questa proprietà non `listCallbackUrl()` supporta l'utilizzo della funzione. Tuttavia, l'azione include e `x-ms-client-tracking-id` `x-ms-workflow-operation-name`invia automaticamente le intestazioni e , che il servizio di destinazione può utilizzare per identificare in modo univoco il sottoscrittore. |
   ||||

1. Per aggiungere altre proprietà di azione, aprire l'elenco **Aggiungi nuovo parametro.**

   ![Aggiungere altre proprietà dell'azioneAdd more action properties](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Ad esempio, se è necessario utilizzare l'autenticazione, è possibile aggiungere le proprietà **Subscribe - Authentication** e **Unsubscribe - Authentication.** Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP Webhook, vedere [Aggiungere l'autenticazione alle chiamate in uscita.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Ora, quando questa azione viene eseguita, l'app per la logica chiama l'endpoint di sottoscrizione nel servizio di destinazione e registra l'URL di callback. L'app per la logica sospende quindi il `HTTP POST` flusso di lavoro e attende che il servizio di destinazione invii una richiesta all'URL di richiamata. Quando si verifica questo evento, l'azione passa tutti i dati nella richiesta al flusso di lavoro. Se l'operazione viene completata correttamente, l'azione annulla la sottoscrizione all'endpoint e l'app per la logica continua a eseguire il flusso di lavoro rimanente.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori informazioni sui parametri trigger e action, simili tra loro, vedere [Parametri Webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Dettagli dell'output

Di seguito sono riportate ulteriori informazioni sugli output da un trigger o un'azione HTTP Webhook, che restituisce queste informazioni:Here is more information about the outputs from an HTTP Webhook trigger or action, which returns this information:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| headers | object | Le intestazioni della richiesta |
| Corpo | object | Oggetto JSON | Oggetto con il contenuto del corpo della richiesta |
| Codice di stato | INT | Il codice di stato della richiesta |
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
