---
title: Aggiungere ed eseguire i frammenti di codice - App per la logica di Azure
description: Aggiungere ed eseguire i frammenti di codice con il codice inline nelle App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 9ef11eb2099ff617fb4da4b9a924dc3f0550f226
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160546"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Aggiungere ed eseguire i frammenti di codice utilizzando codice inline nelle App per la logica di Azure

Quando si desidera eseguire un frammento di codice all'interno dell'app per la logica, è possibile aggiungere l'elemento predefinito **codice Inline** azione come un passaggio nel flusso di lavoro dell'app per la logica. Questa operazione funziona meglio quando si desidera eseguire il codice che soddisfa questo scenario:

* Viene eseguito in JavaScript. Altre lingue sarà presto disponibile.
* Al termine dell'esecuzione in cinque secondi o meno.
* Gestisce i dati di dimensioni fino a 50 MB.
* Usa Node. js versione 8.11.1. Per altre informazioni, vedere [gli oggetti predefiniti Standard](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

Questa azione esegue il frammento di codice e restituisce l'output di tale frammento di codice come un token denominato **risultato**, che è possibile usare in azioni successive nelle app per la logica. Per altri scenari in cui si desidera creare una funzione per il codice, provare [creando e chiamando una funzione di Azure](../logic-apps/logic-apps-azure-functions.md) nell'app per la logica.

In questo articolo, i trigger di app per la logica di esempio quando un nuovo messaggio di posta elettronica arriva in un account di Office 365 Outlook. Il frammento di codice estrae e restituisce tutti gli indirizzi di posta elettronica che vengono visualizzati nel corpo del messaggio di posta elettronica.

![Cenni preliminari sull'esempio](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'app per la logica in cui si desidera aggiungere il frammento di codice, tra cui un trigger. Se non si dispone di un'app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   L'app per la logica di esempio in questo argomento Usa il trigger di Office 365 Outlook: **All'arrivo di un nuovo messaggio di posta elettronica**

* Un' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) che è stato collegato all'app per la logica

## <a name="add-inline-code"></a>Aggiungere il codice inline

1. Se già stato fatto, nelle [portale di Azure](https://portal.azure.com), aprire l'app per la logica in Progettazione App per la logica.

1. Nella finestra di progettazione, aggiungere il **codice Inline** azione nel percorso desiderato nel flusso di lavoro dell'app per la logica.

   * Per aggiungere l'azione alla fine del flusso di lavoro, scegliere **nuovo passaggio**.

   * Per aggiungere l'azione tra i passaggi esistenti, spostare il puntatore del mouse sulla freccia che collega tali passaggi. Scegliere il segno più (**+**) e selezionare **Aggiungi un'azione**.

   Questo esempio viene aggiunto il **codice Inline** azione sotto il trigger di Office 365 Outlook.

   ![Aggiungere nuovo passaggio](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Sotto **scegliere un'azione**, nella casella di ricerca immettere "codice inline" come filtro. Nell'elenco di azioni selezionare questa azione: **Eseguire il codice JavaScript**

   ![Selezionare "Esecuzione codice JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   L'azione viene visualizzata nella finestra di progettazione e contiene alcune codice di esempio predefinito, tra cui un'istruzione return.

   ![Azione di codice inline con codice di esempio predefinito](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Nel **codice** casella, eliminare il codice di esempio e immettere il codice che si desidera eseguire. Scrivere codice che si inseriscono all'interno di un metodo, ma senza definire la firma del metodo. 

   Quando si digita una parola chiave riconosciuta, viene visualizzato l'elenco di completamento automatico in modo che è possibile selezionare le parole chiave disponibili, ad esempio:

   ![Elenco di completamento automatico (parola chiave)](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Questo frammento di codice di esempio crea prima di tutto una variabile che archivia una *espressione regolare*, che consente di specificare un criterio di corrispondenza nel testo di input. Il codice crea quindi una variabile che archivia i dati del corpo di messaggio di posta elettronica dal trigger.

   ![Creare le variabili](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Per semplificare i risultati dai trigger e azioni precedenti per riferimento, viene visualizzato l'elenco di contenuto dinamico mentre il cursore si trova all'interno di **codice** casella. Per questo esempio, l'elenco Visualizza i risultati disponibili da trigger, ad esempio la **corpo** token, che è ora possibile selezionare.

   Dopo aver selezionato il **corpo** token, l'azione di codice inline risolve i token da un `workflowContext` oggetto cui fa riferimento il messaggio di posta elettronica `Body` valore proprietà:

   ![Risultati SELECT](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Nel **codice** casella, il frammento di codice è possibile usare la proprietà di sola lettura `workflowContext` dell'oggetto come input. Questo oggetto dispone di sottoproprietà che forniscono il codice di accesso ai risultati dai trigger e azioni precedenti nel flusso di lavoro.
   Per altre informazioni, vedere la sezione più avanti in questo argomento: [Fare riferimento a trigger e azione i risultati nel codice](#workflowcontext).

   > [!NOTE]
   >
   > Se il frammento di codice fa riferimento a nomi di azioni che usano l'operatore punto (.), è necessario aggiungere i nomi delle azioni per la [ **azioni** parametro](#add-parameters). Tali riferimenti anche necessario racchiudere i nomi delle azioni con le parentesi quadre ([]) e racchiusi tra virgolette, ad esempio:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   L'azione di codice inline non richiede un `return` istruzione, ma i risultati di una `return` istruzione sono disponibili per riferimenti nelle azioni successive tramite il **risultato** token. 
   Ad esempio, il frammento di codice restituisce il risultato chiamando la `match()` funzione, che trova soddisfa nel corpo del messaggio di posta elettronica con l'espressione regolare. Il **Componi** azione utilizza le **risultato** token per fare riferimento ai risultati dell'inline del codice azione e crea un singolo risultato.

   ![App per la logica completata](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Al termine, salvare l'app per la logica.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Risultati di trigger e azione riferimenti nel codice

Il `workflowContext` oggetto dispone di questa struttura, che include le `actions`, `trigger`, e `workflow` sottoproprietà:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Questa tabella contiene ulteriori informazioni su queste sottoproprietà:

| Proprietà | Type | DESCRIZIONE |
|----------|------|-------|
| `actions` | Raccolta di oggetti | Oggetti risultato dalle azioni eseguite prima che venga eseguito il frammento di codice. Ogni oggetto dispone di un *chiave-valore* coppia in cui la chiave è il nome di un'azione e il valore è equivalente alla chiamata il [funzione actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions) con `@actions('<action-name>')`. Nome dell'azione Usa lo stesso nome di azione che viene usato nella definizione del flusso di lavoro sottostante, che sostituisce gli spazi ("") nel nome dell'azione con caratteri di sottolineatura (_). Questo oggetto fornisce l'accesso ai valori delle proprietà azione dall'istanza corrente del flusso di lavoro eseguito. |
| `trigger` | Object | Oggetto risultato dal trigger ed equivalente alla chiamata di [trigger() funzione](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Questo oggetto fornisce l'accesso ai valori delle proprietà trigger dall'istanza corrente del flusso di lavoro eseguito. |
| `workflow` | Object | L'oggetto del flusso di lavoro e l'equivalente alla chiamata di [workflow () funzione](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Questo oggetto fornisce l'accesso ai valori delle proprietà del flusso di lavoro, ad esempio il nome del flusso di lavoro, ID di esecuzione e così via, dall'istanza corrente del flusso di lavoro eseguito. |
|||

Nell'esempio di questo argomento, il `workflowContext` oggetto presenta le seguenti proprietà che può accedere al codice:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Aggiungere parametri

In alcuni casi, potrebbe essere necessario richiedere in modo esplicito che il **codice Inline** azione include i risultati da trigger o azioni specifiche che fa riferimento il codice come dipendenze aggiungendo le **Trigger** o **Azioni** parametri. Questa opzione è utile per scenari in cui i risultati di cui viene fatto riferimento non vengono trovati in fase di esecuzione.

> [!TIP]
> Se si prevede di riutilizzare il codice, aggiungere i riferimenti a proprietà usando la **codice** casella in modo che il codice include i riferimenti del token risolti, anziché aggiungere il trigger o azioni come dipendenze esplicite.

Ad esempio, si supponga che si dispone di codice che fa riferimento il **SelectedOption** derivano dal **inviare posta elettronica di approvazione** azione per il connettore Office 365 Outlook. Al momento della creazione, il motore di App per la logica consente di analizzare il codice per determinare se si è fatto riferimento a qualsiasi trigger o azione risultante e include automaticamente i risultati. In fase di esecuzione, dovrebbe viene visualizzato un errore che il risultato di azione o trigger di cui viene fatto riferimento non è disponibile nell'oggetto specificato `workflowContext` dell'oggetto, è possibile aggiungere tale trigger o azione come una dipendenza esplicita. In questo esempio, si aggiunge il **azioni** parametro e specificare che le **codice Inline** azione includono in modo esplicito il risultato dal **inviare posta elettronica di approvazione** azione.

Per aggiungere questi parametri, aprire il **Aggiungi nuovo parametro** elencare e selezionare i parametri desiderati:

   ![Aggiungere parametri](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametro | DESCRIZIONE |
   |-----------|-------------|
   | **Actions** | Includere i risultati delle azioni precedenti. Visualizzare [includono i risultati dell'azione](#action-results). |
   | **Trigger** | Includere i risultati dal trigger. Visualizzare [Include i risultati di trigger](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Includere i risultati di trigger

Se si seleziona **trigger**, viene chiesto se per includere trigger risultati.

* Dal **Trigger** elenco, selezionare **Yes**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Includere risultati dell'azione

Se si seleziona **azioni**, richiesto per le azioni che si desidera aggiungere. Tuttavia, prima di avviare l'aggiunta di azioni, necessaria la versione del nome dell'azione che viene visualizzato nella definizione del flusso di lavoro sottostante dell'app per la logica.

* Questa funzionalità non supporta le variabili, cicli e gli indici di iterazione.

* I nomi nella definizione del flusso di lavoro dell'app per la logica usano un carattere di sottolineatura (_), non uno spazio.

* Per i nomi delle azioni che usano l'operatore punto (.), includere tali operatori, ad esempio:

  `My.Action.Name`

1. Sulla barra degli strumenti della finestra di progettazione, scegliere **visualizzazione codice**e di ricerca all'interno di `actions` attributo per il nome dell'azione.

   Ad esempio, `Send_approval_email_` è il nome JSON per il **inviare posta elettronica di approvazione** azione.

   ![Trovare il nome di azione in JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Per tornare alla visualizzazione di progettazione, sulla barra degli strumenti di visualizzazione del codice, scegliere **progettazione**.

1. Per aggiungere la prima azione, il **azioni elemento - 1** immettere nome JSON dell'azione.

   ![Immettere prima azione](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Per aggiungere un'altra azione, scegliere **Aggiungi nuovo elemento**.

## <a name="reference"></a>Riferimenti

Per altre informazioni sul **eseguire il codice JavaScript** struttura dell'azione e la sintassi nella definizione del flusso di lavoro sottostante dell'app per la logica usando il linguaggio di definizione del flusso di lavoro, vedere questa azione [sezione di riferimento ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [connettori per App per la logica di Azure](../connectors/apis-list.md)
