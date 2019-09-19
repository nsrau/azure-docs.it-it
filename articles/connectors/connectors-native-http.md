---
title: Chiamare endpoint HTTP e HTTPS-app per la logica di Azure
description: Inviare le richieste in uscita agli endpoint HTTP e HTTPS usando app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: df856e0d76dbd5903964bc80aa01b97b7461128a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122706"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Inviare chiamate in uscita agli endpoint HTTP o HTTPS usando app per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e l'azione o il trigger http incorporato è possibile creare attività e flussi di lavoro automatizzati che inviano regolarmente richieste a qualsiasi endpoint HTTP o HTTPS. Per ricevere e rispondere invece alle chiamate HTTP o HTTPS in ingresso, usare il [trigger di richiesta o l'azione di risposta](../connectors/connectors-native-reqres.md)predefinita.

Ad esempio, è possibile monitorare l'endpoint del servizio per il sito Web controllando l'endpoint in base a una pianificazione specificata. Quando si verifica un evento specifico in corrispondenza di tale endpoint, ad esempio quando il sito Web si arresta, l'evento attiva il flusso di lavoro dell'app per la logica ed esegue le azioni specificate.

Per controllare o eseguire il polling di un endpoint in base a una pianificazione regolare, è possibile usare il trigger http come primo passaggio del flusso di lavoro. In ogni controllo, il trigger invia una chiamata o una *richiesta* all'endpoint. La risposta dell'endpoint determina se il flusso di lavoro dell'app per la logica è in esecuzione. Il trigger passa da qualsiasi contenuto, dalla risposta alle azioni, nell'app per la logica.

È possibile usare l'azione HTTP come qualsiasi altro passaggio nel flusso di lavoro per chiamare l'endpoint quando si desidera. La risposta dell'endpoint determina l'esecuzione delle azioni rimanenti del flusso di lavoro.

In base alla funzionalità dell'endpoint di destinazione, il connettore HTTP supporta le versioni di Transport Layer Security (TLS) 1,0, 1,1 e 1,2. App per la logica negozia con l'endpoint usando la versione più elevata supportata possibile. Se, ad esempio, l'endpoint supporta 1,2, il connettore utilizza prima 1,2. In caso contrario, il connettore usa la versione successiva più elevata supportata.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* URL per l'endpoint di destinazione che si desidera chiamare

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

* App per la logica da cui si vuole chiamare l'endpoint di destinazione. Per iniziare con il trigger HTTP, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare l'azione HTTP, avviare l'app per la logica con tutti i trigger desiderati. Questo esempio usa il trigger HTTP come primo passaggio.

## <a name="add-an-http-trigger"></a>Aggiungere un trigger HTTP

Questo trigger incorporato esegue una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in progettazione app per la logica.

1. Nella casella di ricerca della finestra di progettazione immettere "http" come filtro. Dall'elenco **trigger** selezionare il trigger **http** .

   ![Selezionare HTTP Trigger](./media/connectors-native-http/select-http-trigger.png)

   Questo esempio rinomina il trigger in "trigger HTTP" in modo che il passaggio abbia un nome più descrittivo. Inoltre, nell'esempio in seguito viene aggiunta un'azione HTTP ed entrambi i nomi devono essere univoci.

1. Specificare i valori per i [parametri del trigger http](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) che si desidera includere nella chiamata all'endpoint di destinazione. Impostare la ricorrenza per la frequenza con cui si desidera che il trigger verifichi l'endpoint di destinazione.

   ![Immettere i parametri del trigger HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Per altre informazioni sui tipi di autenticazione disponibili per HTTP, vedere eseguire l'autenticazione di [trigger e azioni http](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="add-an-http-action"></a>Aggiungere un'azione HTTP

Questa azione predefinita esegue una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

   Questo esempio usa il trigger HTTP come primo passaggio.

1. Nel passaggio in cui si vuole aggiungere l'azione HTTP, selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca della finestra di progettazione immettere "http" come filtro. Nell'elenco **azioni** selezionare l'azione **http** .

   ![Selezionare l'azione HTTP](./media/connectors-native-http/select-http-action.png)

   Questo esempio rinomina l'azione in "azione HTTP" in modo che il passaggio abbia un nome più descrittivo.

1. Specificare i valori per i [parametri dell'azione http](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) che si desidera includere nella chiamata all'endpoint di destinazione.

   ![Immettere i parametri dell'azione HTTP](./media/connectors-native-http/http-action-parameters.png)

   Per altre informazioni sui tipi di autenticazione disponibili per HTTP, vedere eseguire l'autenticazione di [trigger e azioni http](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="content-with-multipartform-data-type"></a>Contenuto con tipo di dati multipart/form

Per gestire il contenuto `multipart/form-data` con tipo nelle richieste HTTP, è possibile aggiungere un oggetto JSON che `$content-type` includa gli `$multipart` attributi e al corpo della richiesta HTTP usando questo formato.

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

Si supponga, ad esempio, di avere un'app per la logica che invia una richiesta HTTP post per un file di Excel a un sito Web usando l'API del `multipart/form-data` sito, che supporta il tipo. Ecco come può sembrare questa azione:

![Dati in formato multipart](./media/connectors-native-http/http-action-multipart.png)

Di seguito è riportato lo stesso esempio che mostra la definizione JSON dell'azione HTTP nella definizione del flusso di lavoro sottostante:

```json
{
   "HTTP_action": {
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

Per ulteriori informazioni sui parametri trigger e Action, vedere le sezioni seguenti:

* [Parametri del trigger HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parametri azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Dettagli output

Di seguito sono riportate altre informazioni sugli output di un trigger o un'azione HTTP, che restituisce queste informazioni:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| headers | object | Intestazioni della richiesta |
| body | object | Oggetto JSON | Oggetto con il contenuto del corpo della richiesta |
| status code | int | Codice di stato della richiesta |
|||

| status code | Descrizione |
|-------------|-------------|
| 200 | OK |
| 202 | Accettato |
| 400 | Richiesta non valida |
| 401 | Non autorizzato |
| 403 | Non consentito |
| 404 | Non trovato |
| 500 | Errore interno del server. Si è verificato un errore sconosciuto. |
|||

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
