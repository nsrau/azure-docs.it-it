---
title: Chiamare gli endpoint di servizio tramite HTTP o HTTPS
description: Inviare richieste HTTP o HTTPS in uscita agli endpoint di servizio da app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/09/2020
tags: connectors
ms.openlocfilehash: 8c7a0ddb80ba28548fc1821cc2063e500af0fa66
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286632"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Chiamare gli endpoint di servizio su HTTP o HTTPS da App per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e l'azione o il trigger http incorporato è possibile creare attività e flussi di lavoro automatizzati che inviano richieste agli endpoint di servizio tramite http o HTTPS. Ad esempio, è possibile monitorare l'endpoint del servizio per il sito Web controllando l'endpoint in base a una pianificazione specifica. Quando l'evento specificato si verifica in corrispondenza di tale endpoint, ad esempio quando il sito Web si arresta, l'evento attiva il flusso di lavoro dell'app per la logica ed esegue le azioni nel flusso di lavoro. Se invece si desidera ricevere e rispondere alle chiamate HTTPS in ingresso, utilizzare il trigger di richiesta incorporato [o l'azione di risposta](../connectors/connectors-native-reqres.md).

* Per verificare o eseguire il *polling* di un endpoint in base a una pianificazione ricorrente, [aggiungere il trigger http](#http-trigger) come primo passaggio del flusso di lavoro. Ogni volta che il trigger controlla l'endpoint, il trigger chiama o invia una *richiesta* all'endpoint. La risposta dell'endpoint determina se il flusso di lavoro dell'app per la logica è in esecuzione. Il trigger passa il contenuto dalla risposta dell'endpoint alle azioni nell'app per la logica.

* Per chiamare un endpoint da qualsiasi altra parte del flusso di lavoro, [aggiungere l'azione http](#http-action). La risposta dell'endpoint determina l'esecuzione delle azioni rimanenti del flusso di lavoro.

Questo articolo illustra come aggiungere un trigger o un'azione HTTP al flusso di lavoro dell'app per la logica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* URL per l'endpoint di destinazione che si desidera chiamare

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, vedere informazioni sulle [app per la logica di Azure](../logic-apps/logic-apps-overview.md)

* App per la logica da cui si vuole chiamare l'endpoint di destinazione. Per iniziare con il trigger HTTP, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare l'azione HTTP, avviare l'app per la logica con tutti i trigger desiderati. Questo esempio usa il trigger HTTP come primo passaggio.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Aggiungere un trigger HTTP

Questo trigger incorporato esegue una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota nella finestra di progettazione di App per la logica.

1. Nella casella di ricerca della finestra di progettazione selezionare **predefinito**. Nella casella di ricerca immettere `http` come filtro. Dall'elenco **trigger** selezionare il trigger **http** .

   ![Selezionare HTTP Trigger](./media/connectors-native-http/select-http-trigger.png)

   Questo esempio rinomina il trigger in "trigger HTTP" in modo che il passaggio abbia un nome più descrittivo. Inoltre, nell'esempio in seguito viene aggiunta un'azione HTTP ed entrambi i nomi devono essere univoci.

1. Specificare i valori per i [parametri del trigger http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) che si desidera includere nella chiamata all'endpoint di destinazione. Impostare la ricorrenza per la frequenza con cui si desidera che il trigger verifichi l'endpoint di destinazione.

   ![Immettere i parametri del trigger HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Se si seleziona un tipo di autenticazione diverso da **None**, le impostazioni di autenticazione variano in base alla selezione effettuata. Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP, vedere gli argomenti seguenti:

   * [Aggiunta dell'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
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

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `http` come filtro. Nell'elenco **azioni** selezionare l'azione **http** .

   ![Selezionare l'azione HTTP](./media/connectors-native-http/select-http-action.png)

   Questo esempio rinomina l'azione in "azione HTTP" in modo che il passaggio abbia un nome più descrittivo.

1. Specificare i valori per i [parametri dell'azione http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) che si desidera includere nella chiamata all'endpoint di destinazione.

   ![Immettere i parametri dell'azione HTTP](./media/connectors-native-http/http-action-parameters.png)

   Se si seleziona un tipo di autenticazione diverso da **None**, le impostazioni di autenticazione variano in base alla selezione effettuata. Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP, vedere gli argomenti seguenti:

   * [Aggiunta dell'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticare l'accesso alle risorse con identità gestite](../logic-apps/create-managed-service-identity.md)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

In base alla funzionalità dell'endpoint di destinazione, le chiamate in uscita supportano Transport Layer Security (TLS), che in precedenza era Secure Sockets Layer (SSL), versioni 1,0, 1,1 e 1,2. App per la logica negozia con l'endpoint usando la versione più elevata supportata possibile.

Se, ad esempio, l'endpoint supporta 1,2, il connettore HTTP USA prima 1,2. In caso contrario, il connettore usa la versione successiva più elevata supportata.

<a name="self-signed"></a>

## <a name="self-signed-certificates"></a>Certificati autofirmati

* Per le app per la logica nell'ambiente Azure multi-tenant globale, il connettore HTTP non consente i certificati TLS/SSL autofirmati. Se l'app per la logica effettua una chiamata HTTP a un server e presenta un certificato autofirmato TLS/SSL, la chiamata HTTP ha esito negativo e viene `TrustFailure` visualizzato un errore.

* Per le app per la logica in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), il connettore HTTP consente i certificati autofirmati per gli handshake TLS/SSL. Tuttavia, è necessario innanzitutto [abilitare il supporto dei certificati autofirmati](../logic-apps/create-integration-service-environment-rest-api.md#request-body) per un ISE esistente o un nuovo ISE usando l'API REST di app per la logica e installare il certificato pubblico nel `TrustedRoot` percorso.

## <a name="content-with-multipartform-data-type"></a>Contenuto con tipo di dati multipart/form

Per gestire il contenuto con `multipart/form-data` tipo nelle richieste HTTP, è possibile aggiungere un oggetto JSON che includa `$content-type` gli `$multipart` attributi e al corpo della richiesta HTTP usando questo formato.

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

Si supponga, ad esempio, di avere un'app per la logica che invia una richiesta HTTP POST per un file di Excel a un sito Web usando l'API del sito, che supporta il `multipart/form-data` tipo. Ecco come può sembrare questa azione:

![Dati in formato multipart](./media/connectors-native-http/http-action-multipart.png)

Di seguito è riportato lo stesso esempio che mostra la definizione JSON dell'azione HTTP nella definizione del flusso di lavoro sottostante:

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

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Comportamento di richiesta-risposta asincrono

Per impostazione predefinita, tutte le azioni basate su HTTP nelle app per la logica di Azure seguono il [modello di operazione asincrona](/azure/architecture/patterns/async-request-reply)standard. Questo modello specifica che dopo un'azione HTTP chiama o invia una richiesta a un endpoint, un servizio, un sistema o un'API, il destinatario restituisce immediatamente una risposta ["202 accettata"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) . Questo codice conferma che il ricevitore ha accettato la richiesta ma non ha completato l'elaborazione. La risposta può includere un' `location` intestazione che specifica l'URL e un ID di aggiornamento che il chiamante può usare per eseguire il polling o controllare lo stato della richiesta asincrona finché il ricevitore non interrompe l'elaborazione e restituisce una risposta di esito positivo ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) o un'altra risposta non 202. Tuttavia, il chiamante non deve attendere che la richiesta completi l'elaborazione e possa continuare a eseguire l'azione successiva. Per altre informazioni, vedere l' [integrazione di microservizi asincroni impone l'autonomia dei microservizi](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* Nella finestra di progettazione dell'app per la logica, l'azione HTTP, ma non il trigger, ha un'impostazione del **modello asincrona** , abilitata per impostazione predefinita. Questa impostazione specifica che il chiamante non attende il completamento dell'elaborazione e può passare all'azione successiva, ma continua a controllare lo stato fino a quando l'elaborazione non viene arrestata. Se disabilitata, questa impostazione specifica che il chiamante attende il completamento dell'elaborazione prima di procedere all'azione successiva.

  Per trovare questa impostazione, attenersi alla seguente procedura:

  1. Sulla barra del titolo dell'azione HTTP, selezionare il pulsante con i puntini di sospensione (**...**) per aprire le impostazioni dell'azione.

  1. Trovare l'impostazione del **modello asincrono** .

     ![Impostazione "modello asincrono"](./media/connectors-native-http/asynchronous-pattern-setting.png)

* La definizione di JavaScript Object Notation (JSON) sottostante dell'azione HTTP segue in modo implicito il modello di operazione asincrona.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Disabilitare le operazioni asincrone

In alcuni casi, potrebbe essere necessario il comportamento asincrono dell'azione HTTP in scenari specifici, ad esempio quando si desidera:

* [Evitare i timeout HTTP per le attività a esecuzione prolungata](#avoid-http-timeouts)
* [Disabilita controllo intestazioni posizione](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>Disattiva impostazione del **modello asincrono**

1. Nella finestra di progettazione dell'app per la logica, sulla barra del titolo dell'azione HTTP, selezionare il pulsante con i puntini di sospensione (**...**) per aprire le impostazioni dell'azione.

1. Individuare l'impostazione del **modello asincrono** **, disattivare l'impostazione se** abilitata, quindi fare clic su **fine**.

   ![Disabilitare l'impostazione "modello asincrono"](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Disabilitare il modello asincrono nella definizione JSON dell'azione

Nella definizione JSON sottostante dell'azione HTTP [aggiungere l' `"DisableAsyncPattern"` opzione Operation](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) alla definizione dell'azione in modo che l'azione segua invece il modello di operazione sincrona. Per altre informazioni, vedere [eseguire azioni anche in un modello di operazione sincrona](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern).

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>Evitare i timeout HTTP per le attività a esecuzione prolungata

Le richieste HTTP hanno un [limite di timeout](../logic-apps/logic-apps-limits-and-config.md#http-limits). Se si verifica un timeout di un'azione HTTP a esecuzione prolungata a causa di questo limite, sono disponibili le opzioni seguenti:

* [Disabilitare il modello di operazione asincrona dell'azione http](#disable-asynchronous-operations) in modo che l'azione non esegua continuamente il polling o controlli lo stato della richiesta. Al contrario, l'azione attende che il ricevitore risponda con lo stato e i risultati al termine dell'elaborazione della richiesta.

* Sostituire l'azione HTTP con l' [azione http webhook](../connectors/connectors-native-webhook.md), che attende che il ricevitore risponda con lo stato e i risultati al termine dell'elaborazione della richiesta.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Disabilita controllo intestazioni posizione

Alcuni endpoint, servizi, sistemi o API restituiscono una risposta "202 ACCEPTed" senza `location` intestazione. Per evitare che un'azione HTTP verifichi continuamente lo stato della richiesta quando l' `location` intestazione non esiste, è possibile disporre di queste opzioni:

* [Disabilitare il modello di operazione asincrona dell'azione http](#disable-asynchronous-operations) in modo che l'azione non esegua continuamente il polling o controlli lo stato della richiesta. Al contrario, l'azione attende che il ricevitore risponda con lo stato e i risultati al termine dell'elaborazione della richiesta.

* Sostituire l'azione HTTP con l' [azione http webhook](../connectors/connectors-native-webhook.md), che attende che il ricevitore risponda con lo stato e i risultati al termine dell'elaborazione della richiesta.

## <a name="known-issues"></a>Problemi noti

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Intestazioni HTTP omesse

Se un trigger o un'azione HTTP include queste intestazioni, app per la logica rimuove le intestazioni dal messaggio di richiesta generato senza visualizzare alcun avviso o errore:

* `Accept-*`
* `Allow`
* `Content-*` con le eccezioni seguenti: `Content-Disposition`, `Content-Encoding` e `Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Sebbene le app per la logica non interrompano il salvataggio di app per la logica che usano un trigger o un'azione HTTP con queste intestazioni, le app per la logica ignorano queste intestazioni.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori informazioni sui parametri trigger e Action, vedere le sezioni seguenti:

* [Parametri del trigger HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametri azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Dettagli dell'output

Di seguito sono riportate altre informazioni sugli output di un trigger o un'azione HTTP, che restituisce queste informazioni:

| Proprietà | Type | Description |
|----------|------|-------------|
| `headers` | Oggetto JSON | Intestazioni della richiesta |
| `body` | Oggetto JSON | Oggetto con il contenuto del corpo della richiesta |
| `status code` | Integer | Codice di stato della risposta |
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

