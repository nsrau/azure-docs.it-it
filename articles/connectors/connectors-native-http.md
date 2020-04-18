---
title: Chiamare gli endpoint del servizio tramite HTTP o HTTPSCall service endpoints by using HTTP or HTTPS
description: Inviare richieste HTTP o HTTPS in uscita agli endpoint del servizio dalle app per la logica di AzureSend outbound HTTP or HTTPS requests to service endpoints from Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 6c52f2df34faf441ab70b48b11bbc393ebcecb65
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617606"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Chiamare gli endpoint del servizio su HTTP o HTTPS da App per la logica di AzureCall service endpoints over HTTP or HTTPS from Azure Logic Apps

Con [le app per](../logic-apps/logic-apps-overview.md) la logica di Azure e l'azione o il trigger o l'azione HTTP predefinita, è possibile creare attività e flussi di lavoro automatizzati che inviano richieste agli endpoint del servizio tramite HTTP o HTTPS. Ad esempio, è possibile monitorare l'endpoint del servizio per il sito Web controllando l'endpoint in base a una pianificazione specifica. Quando l'evento specificato si verifica in tale endpoint, ad esempio il sito Web inattivo, l'evento attiva il flusso di lavoro dell'app per la logica ed esegue le azioni in tale flusso di lavoro. Se invece si desidera ricevere e rispondere alle chiamate HTTPS in ingresso, utilizzare il trigger di [richiesta incorporato o l'azione Risposta](../connectors/connectors-native-reqres.md).

> [!NOTE]
> In base alla funzionalità dell'endpoint di destinazione, il connettore HTTP supporta Transport Layer Security (TLS) versioni 1.0, 1.1 e 1.2. App per la logica negozia con l'endpoint usando la versione supportata più alta possibile. Ad esempio, se l'endpoint supporta 1.2, il connettore utilizza prima 1.2.Soexample, if the endpoint supports 1.2, the connector uses 1.2 first. In caso contrario, il connettore utilizza la versione più alta successiva supportata.

Per controllare o *eseguire* il polling di un endpoint in base a una pianificazione ricorrente, [aggiungere il trigger HTTP](#http-trigger) come primo passaggio del flusso di lavoro. Ogni volta che il trigger controlla l'endpoint, il trigger chiama o invia una *richiesta* all'endpoint. La risposta dell'endpoint determina se il flusso di lavoro dell'app per la logica è in esecuzione. Il trigger passa qualsiasi contenuto dalla risposta dell'endpoint alle azioni nell'app per la logica.

Per chiamare un endpoint da qualsiasi altro punto del flusso di lavoro, [aggiungere l'azione HTTP](#http-action). La risposta dell'endpoint determina l'esecuzione delle azioni rimanenti del flusso di lavoro.

> [!IMPORTANT]
> Se un trigger o un'azione HTTP include queste intestazioni, App per la logica rimuove queste intestazioni dal messaggio di richiesta generato senza visualizzare alcun avviso o errore:If an HTTP trigger or action includes these headers, Logic Apps removes these headers from the generated request message without showing any warning or error:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`con queste eccezioni: `Content-Disposition`, `Content-Encoding`, e`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Anche se app per la logica non ti impedirà di salvare le app per la logica che usano un trigger HTTP o un'azione con queste intestazioni, App per la logica ignora queste intestazioni.

Questo articolo illustra come aggiungere un trigger o un'azione HTTP al flusso di lavoro dell'app per la logica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Url dell'endpoint di destinazione che si desidera chiamare

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, vedere [Che cos'è](../logic-apps/logic-apps-overview.md)Azure Logic Apps?

* App per la logica da cui si vuole chiamare l'endpoint di destinazione. Per iniziare con il trigger HTTP, [creare un'app per la logica vuota.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Per usare l'azione HTTP, avviare l'app per la logica con qualsiasi trigger desiderato. In questo esempio viene utilizzato il trigger HTTP come primo passaggio.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Aggiungere un trigger HTTPAdd an HTTP trigger

Questo trigger predefinito effettua una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in Progettazione app per la logica.

1. Nella casella di ricerca della finestra di progettazione selezionare **Predefinito**. Nella casella di ricerca immettere `http` come filtro. Nell'elenco **Trigger** selezionare il trigger **HTTP.**

   ![Selezionare HTTP Trigger](./media/connectors-native-http/select-http-trigger.png)

   In questo esempio il trigger viene rinominato in "HTTP trigger" in modo che il passaggio abbia un nome più descrittivo. Inoltre, l'esempio aggiunge in seguito un'azione HTTP ed entrambi i nomi devono essere univoci.

1. Specificare i valori per i [parametri del trigger HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) che si desidera includere nella chiamata all'endpoint di destinazione. Impostare la ricorrenza per la frequenza con cui si desidera che il trigger controlli l'endpoint di destinazione.

   ![Immettere i parametri del trigger HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Se si seleziona un tipo di **autenticazione**diverso da Nessuno , le impostazioni di autenticazione variano in base alla selezione effettuata. Per altre informazioni sui tipi di autenticazione disponibili per HTTP, vedere gli argomenti seguenti:For more information about authentication types available for HTTP, see these topics:

   * [Aggiungere l'autenticazione alle chiamate in uscitaAdd authentication to outbound calls](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticare l'accesso alle risorse con identità gestiteAuthenticate access to resources with managed identities](../logic-apps/create-managed-service-identity.md)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Aggiungere un'azione HTTP

Questa azione predefinita effettua una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

   In questo esempio viene utilizzato il trigger HTTP come primo passaggio.

1. Nel passaggio in cui si desidera aggiungere l'azione HTTP selezionare **Nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `http` come filtro. Nell'elenco **Azioni** selezionare l'azione **HTTP.**

   ![Selezionare l'azione HTTP](./media/connectors-native-http/select-http-action.png)

   In questo esempio l'azione viene rinominata in "HTTP action" in modo che il passaggio abbia un nome più descrittivo.

1. Specificare i valori per i [parametri dell'azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) che si desidera includere nella chiamata all'endpoint di destinazione.

   ![Immettere i parametri dell'azione HTTP](./media/connectors-native-http/http-action-parameters.png)

   Se si seleziona un tipo di **autenticazione**diverso da Nessuno , le impostazioni di autenticazione variano in base alla selezione effettuata. Per altre informazioni sui tipi di autenticazione disponibili per HTTP, vedere gli argomenti seguenti:For more information about authentication types available for HTTP, see these topics:

   * [Aggiungere l'autenticazione alle chiamate in uscitaAdd authentication to outbound calls](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticare l'accesso alle risorse con identità gestiteAuthenticate access to resources with managed identities](../logic-apps/create-managed-service-identity.md)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="content-with-multipartform-data-type"></a>Contenuto con tipo multipart/form-data

Per gestire il `multipart/form-data` contenuto che include il tipo nelle richieste `$content-type` `$multipart` HTTP, è possibile aggiungere un oggetto JSON che include gli attributi e al corpo della richiesta HTTP utilizzando questo formato.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Si supponga, ad esempio, di disporre di un'app per la logica che invia una richiesta `multipart/form-data` HTTP POST per un file di Excel a un sito Web usando l'API del sito, che supporta il tipo. Ecco come potrebbe apparire questa azione:Here's how this action might look:

![Dati modulo multiparte](./media/connectors-native-http/http-action-multipart.png)

Ecco lo stesso esempio che mostra la definizione JSON dell'azione HTTP nella definizione del flusso di lavoro sottostante:Here is the same example that shows the HTTP action's JSON definition in the underlying workflow definition:

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per altre informazioni sui parametri di trigger e azione, vedere le sezioni seguenti:For more information about trigger and action parameters, see these sections:

* [Parametri trigger HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametri dell'azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Dettagli dell'output

Ecco altre informazioni sugli output da un trigger http o da un'azione, che restituisce queste informazioni:Here is more information about the outputs from an HTTP trigger or action, which returns this information:

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
