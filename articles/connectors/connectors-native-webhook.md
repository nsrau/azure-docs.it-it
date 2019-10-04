---
title: Creare le attività basate su eventi e flussi di lavoro nelle App per la logica di Azure
description: Attivare, sospendere e riprendere le attività automatizzate, processi e flussi di lavoro basati sugli eventi che si verificano in un endpoint con le App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541388"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Automatizzare le attività basate su eventi e flussi di lavoro usando i webhook HTTP nelle App per la logica di Azure

Con [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e il connettore HTTP Webhook incorporato, è possibile automatizzare i flussi di lavoro che attendere ed eseguire in base agli eventi specifici che si verificano in un endpoint HTTP o HTTPS mediante la compilazione di App per la logica. Ad esempio, è possibile creare un'app per la logica che monitora un endpoint del servizio in attesa di un evento specifico prima di attivare il flusso di lavoro e che esegue le azioni specificate, anziché controllare regolarmente o *polling* quell'endpoint.

Ecco alcuni esempio basato su eventi i flussi di lavoro:

* Attendere che un elemento in arrivo da un' [Hub eventi di Azure](https://github.com/logicappsio/EventHubAPI) prima di attivare un'esecuzione dell'app per la logica.
* Attendere l'approvazione prima di continuare un flusso di lavoro.

## <a name="how-do-webhooks-work"></a>Come funzionano i webhook?

Un trigger di webhook HTTP è basato su eventi, che non dipendono dal controllo o regolarmente il polling per i nuovi elementi. Quando si salva un'app per la logica che inizia con un trigger webhook o quando si modifica l'app per la logica da disabilitato ad abilitato, il trigger webhook *sottoscrive* a un servizio specifico o un endpoint registrando un *URL callback* con tale servizio o dell'endpoint. Attende quindi il trigger per tale servizio o dell'endpoint da chiamare all'URL, che viene avviata l'esecuzione dell'app per la logica. Simile al [trigger di richiesta](connectors-native-reqres.md), app per la logica viene attivata immediatamente quando si verifica l'evento specificato. Il trigger *Annulla la sottoscrizione* dal servizio o endpoint se si rimuove il trigger e salvare l'app per la logica, o quando si modifica l'app per la logica da abilitato a disabilitato.

È anche un'azione di webhook HTTP basato su eventi e *sottoscrive* a un servizio specifico o un endpoint tramite la registrazione di un *URL di callback* con tale servizio o dell'endpoint. L'azione webhook sospende del flusso di lavoro dell'app per la logica e attende fino a quando il servizio o dell'endpoint dell'URL prima che i curricula di app per la logica che esegue chiamate. L'app per la logica di azione *Annulla la sottoscrizione* dal servizio o endpoint in questi casi:

* Quando l'azione webhook è stata completata correttamente
* Se l'esecuzione dell'app per la logica è stata annullata durante l'attesa di una risposta
* Prima la logica app verifica il timeout

Ad esempio, Office 365 Outlook del connettore [ **inviare posta elettronica di approvazione** ](connectors-create-api-office365-outlook.md) azione è riportato un esempio di azione webhook che segue questo modello. È possibile estendere questo modello in qualsiasi servizio tramite l'azione webhook.

Per altre informazioni, vedere gli argomenti seguenti:

* [Parametri di trigger HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [I Webhook e le sottoscrizioni](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Creare API personalizzate che supportano un webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* API che supporta il webhook o l'URL per un endpoint già distribuito sottoscrivere e annullare la sottoscrizione di modello per [trigger di webhook nelle App per la logica](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) oppure [le azioni webhook nelle App per la logica](../logic-apps/logic-apps-create-api-app.md#webhook-actions) come appropriato

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

* L'app per la logica in cui si desidera attendere eventi specifici nell'endpoint di destinazione. Per il trigger HTTP Webhook, per iniziare [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare l'azione di HTTP Webhook, avviare l'app per la logica con un qualsiasi trigger desiderato. Questo esempio Usa il trigger HTTP come primo passaggio.

## <a name="add-an-http-webhook-trigger"></a>Aggiungere un trigger HTTP Webhook

Questo trigger predefinito registra un URL di callback con il servizio specificato e attende che invia una richiesta HTTP POST all'URL del servizio. Quando si verifica questo evento, il trigger viene attivato e viene eseguito immediatamente l'app per la logica.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in Progettazione App per la logica.

1. Nella finestra di progettazione, nella casella di ricerca immettere "webhook http" come filtro. Dal **trigger** elenco, selezionare la **HTTP Webhook** trigger.

   ![Selezionare il trigger HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Questo esempio mostra come rinominare il trigger "Trigger di HTTP Webhook" in modo che il passaggio ha un nome più descrittivo. Inoltre, nell'esempio viene successivamente viene aggiunta un'azione HTTP Webhook e che entrambi i nomi devono essere univoci.

1. Specificare i valori per il [parametri di trigger HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) che si desidera utilizzare per il "Subscribe" e "UNSUBSCRIBE" chiamate, ad esempio:

   ![Immettere i parametri di trigger HTTP Webhook](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Per aggiungere altri parametri disponibili, aprire il **Aggiungi nuovo parametro** elencare e selezionare i parametri desiderati.

   Per altre informazioni sui tipi di autenticazione disponibili per il HTTP Webhook, vedere [l'autenticazione HTTP trigger e azioni](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Quando hai finito, termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione, selezionare **salvare**.

   Salvataggio dell'app per la logica chiama l'endpoint "Subscribe" e registra l'URL di callback per l'attivazione dell'app per la logica.

1. A questo punto, ogni volta che il servizio di destinazione invia un `HTTP POST` richiesta all'URL callback, viene attivato l'app per la logica e include tutti i dati che viene passati tramite la richiesta.

## <a name="add-an-http-webhook-action"></a>Aggiungi un'azione HTTP Webhook

Questa azione integrata registra un URL di callback con il servizio specificato, viene sospeso del flusso di lavoro dell'app per la logica e in attesa di inviare una richiesta HTTP POST all'URL del servizio. Quando si verifica questo evento, l'azione viene ripresa l'esecuzione dell'app per la logica.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

   Questo esempio Usa il trigger HTTP Webhook come primo passaggio.

1. Sotto il passaggio in cui si desidera aggiungere l'azione di HTTP Webhook, selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) che viene visualizzata e quindi selezionare **Aggiungi un'azione**.

1. Nella finestra di progettazione, nella casella di ricerca immettere "webhook http" come filtro. Dal **azioni** elenco, selezionare la **HTTP Webhook** azione.

   ![Selezionare l'azione di HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   Questo esempio mostra come rinominare l'azione da "Azione di HTTP Webhook" in modo che il passaggio ha un nome più descrittivo.

1. Specificare i valori per il HTTP Webhook parametri dell'azione, che sono simili per il [parametri di trigger HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) che si desidera utilizzare per il "Subscribe" e "UNSUBSCRIBE" chiamate, ad esempio:

   ![Immettere i parametri di azione HTTP Webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Durante la fase di esecuzione, l'app per la logica chiama l'endpoint "Subscribe" quando si esegue questa azione. App per la logica, quindi sospende il flusso di lavoro e attende che il servizio di destinazione inviare un `HTTP POST` richiesta all'URL di callback. Se l'azione viene completata correttamente, l'azione Annulla la sottoscrizione dall'endpoint e riprende l'app per la logica che esegue il flusso di lavoro.

1. Per aggiungere altri parametri disponibili, aprire il **Aggiungi nuovo parametro** elencare e selezionare i parametri desiderati.

   Per altre informazioni sui tipi di autenticazione disponibili per il HTTP Webhook, vedere [l'autenticazione HTTP trigger e azioni](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione, selezionare **salvare**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per altre informazioni sui parametri di trigger e azione, che sono simili tra loro, vedere [i parametri del HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Dettagli dell'output

Di seguito sono riportate altre informazioni sugli output da un HTTP Webhook trigger o azione, che restituisce queste informazioni:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| headers | object | Le intestazioni della richiesta |
| body | object | Oggetto JSON | L'oggetto con il contenuto del corpo della richiesta |
| status code | int | Il codice di stato della richiesta |
|||

| status code | Descrizione |
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
