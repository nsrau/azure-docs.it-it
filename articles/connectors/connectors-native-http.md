---
title: Chiamare gli endpoint di servizio tramite HTTP o HTTPS
description: Inviare richieste HTTP o HTTPS in uscita agli endpoint di servizio da app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 8aefe851708c0b8d8780d03e4364e034e783bf4a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297203"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Chiamare gli endpoint di servizio su HTTP o HTTPS da app per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e l'azione o il trigger http incorporato è possibile creare attività e flussi di lavoro automatizzati che inviano richieste agli endpoint di servizio tramite http o HTTPS. Ad esempio, è possibile monitorare l'endpoint del servizio per il sito Web controllando l'endpoint in base a una pianificazione specifica. Quando l'evento specificato si verifica in corrispondenza di tale endpoint, ad esempio quando il sito Web si arresta, l'evento attiva il flusso di lavoro dell'app per la logica ed esegue le azioni nel flusso di lavoro. Se invece si desidera ricevere e rispondere alle chiamate HTTPS in ingresso, utilizzare il trigger di richiesta incorporato [o l'azione di risposta](../connectors/connectors-native-reqres.md).

> [!NOTE]
> In base alla funzionalità dell'endpoint di destinazione, il connettore HTTP supporta le versioni di Transport Layer Security (TLS) 1,0, 1,1 e 1,2. App per la logica negozia con l'endpoint usando la versione più elevata supportata possibile. Se, ad esempio, l'endpoint supporta 1,2, il connettore utilizza prima 1,2. In caso contrario, il connettore usa la versione successiva più elevata supportata.

Per verificare o eseguire il *polling* di un endpoint in base a una pianificazione ricorrente, [aggiungere il trigger http](#http-trigger) come primo passaggio del flusso di lavoro. Ogni volta che il trigger controlla l'endpoint, il trigger chiama o invia una *richiesta* all'endpoint. La risposta dell'endpoint determina se il flusso di lavoro dell'app per la logica è in esecuzione. Il trigger passa il contenuto dalla risposta dell'endpoint alle azioni nell'app per la logica.

Per chiamare un endpoint da qualsiasi altra parte del flusso di lavoro, [aggiungere l'azione http](#http-action). La risposta dell'endpoint determina l'esecuzione delle azioni rimanenti del flusso di lavoro.

> [!IMPORTANT]
> Se un trigger o un'azione HTTP include queste intestazioni, app per la logica rimuove le intestazioni dal messaggio di richiesta generato senza visualizzare alcun avviso o errore:
>
> * `Accept-*`
> * `Allow`
> * `Content-*` con le eccezioni seguenti: `Content-Disposition`, `Content-Encoding`e `Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Sebbene le app per la logica non interrompano il salvataggio di app per la logica che usano un trigger o un'azione HTTP con queste intestazioni, le app per la logica ignorano queste intestazioni.

Questo articolo illustra come aggiungere un trigger o un'azione HTTP al flusso di lavoro dell'app per la logica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* URL per l'endpoint di destinazione che si desidera chiamare

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, vedere informazioni sulle [app per la logica di Azure](../logic-apps/logic-apps-overview.md)

* App per la logica da cui si vuole chiamare l'endpoint di destinazione. Per iniziare con il trigger HTTP, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare l'azione HTTP, avviare l'app per la logica con tutti i trigger desiderati. Questo esempio usa il trigger HTTP come primo passaggio.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Aggiungere un trigger HTTP

Questo trigger incorporato esegue una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in progettazione app per la logica.

1. Nella casella di ricerca della finestra di progettazione selezionare **predefinito**. Nella casella di ricerca immettere `http` come filtro. Dall'elenco **trigger** selezionare il trigger **http** .

   ![Selezionare HTTP Trigger](./media/connectors-native-http/select-http-trigger.png)

   Questo esempio rinomina il trigger in "trigger HTTP" in modo che il passaggio abbia un nome più descrittivo. Inoltre, nell'esempio in seguito viene aggiunta un'azione HTTP ed entrambi i nomi devono essere univoci.

1. Specificare i valori per i [parametri del trigger http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) che si desidera includere nella chiamata all'endpoint di destinazione. Impostare la ricorrenza per la frequenza con cui si desidera che il trigger verifichi l'endpoint di destinazione.

   ![Immettere i parametri del trigger HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Se si seleziona un tipo di autenticazione diverso da **None**, le impostazioni di autenticazione variano in base alla selezione effettuata. Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP, vedere gli argomenti seguenti:

   * [Aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticare l'accesso alle risorse con identità gestite](../logic-apps/create-managed-service-identity.md)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Aggiungere un'azione HTTP

Questa azione predefinita esegue una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

   Questo esempio usa il trigger HTTP come primo passaggio.

1. Nel passaggio in cui si vuole aggiungere l'azione HTTP, selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `http` come filtro. Nell'elenco **azioni** selezionare l'azione **http** .

   ![Selezionare l'azione HTTP](./media/connectors-native-http/select-http-action.png)

   Questo esempio rinomina l'azione in "azione HTTP" in modo che il passaggio abbia un nome più descrittivo.

1. Specificare i valori per i [parametri dell'azione http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) che si desidera includere nella chiamata all'endpoint di destinazione.

   ![Immettere i parametri dell'azione HTTP](./media/connectors-native-http/http-action-parameters.png)

   Se si seleziona un tipo di autenticazione diverso da **None**, le impostazioni di autenticazione variano in base alla selezione effettuata. Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP, vedere gli argomenti seguenti:

   * [Aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticare l'accesso alle risorse con identità gestite](../logic-apps/create-managed-service-identity.md)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="content-with-multipartform-data-type"></a>Contenuto con tipo di dati multipart/form

Per gestire il contenuto con `multipart/form-data` tipo nelle richieste HTTP, è possibile aggiungere un oggetto JSON che includa gli attributi `$content-type` e `$multipart` al corpo della richiesta HTTP usando questo formato.

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

Si supponga, ad esempio, di avere un'app per la logica che invia una richiesta HTTP POST per un file di Excel a un sito Web usando l'API del sito, che supporta il tipo di `multipart/form-data`. Ecco come può sembrare questa azione:

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

* [Parametri del trigger HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametri azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Dettagli dell'output

Di seguito sono riportate altre informazioni sugli output di un trigger o un'azione HTTP, che restituisce queste informazioni:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| intestazioni | object | Intestazioni della richiesta |
| Corpo | object | oggetto JSON | Oggetto con il contenuto del corpo della richiesta |
| Codice di stato | int | Codice di stato della richiesta |
|||

| Codice di stato | Descrizione |
|-------------|-------------|
| 200 | OK |
| 202 | Accettato |
| 400 | Richiesta non valida |
| 401 | Non autorizzata |
| 403 | Non consentito |
| 404 | Non trovato |
| 500 | Errore interno del server. Si è verificato un errore sconosciuto. |
|||

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
