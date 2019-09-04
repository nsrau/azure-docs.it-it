---
title: Aggiungere ed eseguire frammenti di codice-app per la logica di Azure
description: Aggiungere ed eseguire frammenti di codice con codice inline in app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: 76b3807727f4b5c9ab0a2c2bc21c45af1f713b83
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242455"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Aggiungere ed eseguire frammenti di codice usando il codice inline in app per la logica di Azure

Quando si vuole eseguire una parte di codice all'interno dell'app per la logica, è possibile aggiungere l'azione di **codice inline** incorporata come passaggio nel flusso di lavoro dell'app per la logica. Questa azione funziona meglio quando si desidera eseguire codice che soddisfi questo scenario:

* Viene eseguito in JavaScript. Più lingue saranno presto disponibili.
* Termina l'esecuzione in meno di cinque secondi.
* Gestisce i dati con dimensioni massime di 50 MB.
* USA node. js versione 8.11.1. Per ulteriori informazioni, vedere [oggetti incorporati standard](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > La `require()` funzione non è supportata dall'azione di **codice inline** per l'esecuzione di JavaScript.

Questa azione esegue il frammento di codice e restituisce l'output del frammento come token denominato **result**, che è possibile usare nelle azioni successive nell'app per la logica. Per altri scenari in cui si vuole creare una funzione per il codice, provare a [creare e chiamare una funzione di Azure](../logic-apps/logic-apps-azure-functions.md) nell'app per la logica.

In questo articolo, l'app per la logica di esempio viene attivata quando arriva un nuovo messaggio di posta elettronica in un account Office 365 Outlook. Il frammento di codice estrae e restituisce tutti gli indirizzi di posta elettronica visualizzati nel corpo del messaggio di posta elettronica.

![Panoramica di esempio](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica in cui si vuole aggiungere il frammento di codice, incluso un trigger. Se non si dispone di un'app per la [logica, vedere Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   L'app per la logica di esempio in questo argomento usa il trigger Outlook per Office 365: **All'arrivo di un nuovo messaggio di posta elettronica**

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) collegato all'app per la logica

  > [!NOTE]
  > Assicurarsi di usare un account di integrazione appropriato per il caso d'uso o lo scenario. Ad esempio, gli account di integrazione a [livello gratuito](../logic-apps/logic-apps-pricing.md#integration-accounts) sono destinati solo a scenari esplorativi e carichi di lavoro, non a scenari di produzione, sono limitati all'utilizzo e alla velocità effettiva e non sono supportati da un contratto di servizio (SLA). Altri livelli comportano costi, ma includono il supporto del contratto di servizio, offrono maggiore velocità effettiva e hanno limiti più elevati. Altre informazioni sui [livelli](../logic-apps/logic-apps-pricing.md#integration-accounts), i [prezzi](https://azure.microsoft.com/pricing/details/logic-apps/)e i [limiti](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)dell'account di integrazione.

## <a name="add-inline-code"></a>Aggiungi codice inline

1. Se non è già stato fatto, nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

1. Nella finestra di progettazione aggiungere l'azione di **codice inline** nel percorso desiderato nel flusso di lavoro dell'app per la logica.

   * Per aggiungere l'azione alla fine del flusso di lavoro, scegliere **nuovo passaggio**.

   * Per aggiungere l'azione tra i passaggi esistenti, spostare il puntatore del mouse sulla freccia che connette tali passaggi. Scegliere il segno più ( **+** ) e selezionare **Aggiungi un'azione**.

   Questo esempio Mostra come aggiungere l'azione di **codice inline** nel trigger Office 365 Outlook.

   ![Aggiungi nuovo passaggio](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. In **scegliere un'azione**, nella casella di ricerca, immettere "codice inline" come filtro. Nell'elenco di azioni selezionare questa azione: **Esegui codice JavaScript**

   ![Selezionare "Esegui codice JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   L'azione viene visualizzata nella finestra di progettazione e contiene un codice di esempio predefinito, inclusa un'istruzione return.

   ![Azione di codice inline con codice di esempio predefinito](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Nella casella **codice** eliminare il codice di esempio e immettere il codice che si desidera eseguire. Scrivere codice da inserire all'interno di un metodo, ma senza definire la firma del metodo. 

   Quando si digita una parola chiave riconosciuta, viene visualizzato l'elenco di completamento automatico in modo che sia possibile selezionare le parole chiave disponibili, ad esempio:

   ![Elenco di completamento automatico delle parole chiave](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Questo frammento di codice di esempio crea innanzitutto una variabile che archivia un' *espressione regolare*, che specifica un criterio per la corrispondenza nel testo di input. Il codice crea quindi una variabile che archivia i dati del corpo del messaggio di posta elettronica dal trigger.

   ![Creare le variabili](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Per rendere più semplice il riferimento ai risultati del trigger e delle azioni precedenti, l'elenco di contenuto dinamico viene visualizzato quando il cursore si trova all'interno della casella di **codice** . Per questo esempio, l'elenco Mostra i risultati disponibili dal trigger, incluso il token del **corpo** , che è ora possibile selezionare.

   Dopo aver selezionato il token del **corpo** , l'azione di codice inline risolve il token in un `workflowContext` oggetto che `Body` fa riferimento al valore della proprietà del messaggio di posta elettronica:

   ![Seleziona risultato](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Nella casella **codice** , il frammento di codice può usare l' `workflowContext` oggetto di sola lettura come input. Questo oggetto dispone di sottoproprietà che consentono al codice di accedere ai risultati ottenuti dal trigger e dalle azioni precedenti del flusso di lavoro.
   Per ulteriori informazioni, vedere questa sezione più avanti in questo argomento: [Il trigger di riferimento e l'azione generano il codice](#workflowcontext).

   > [!NOTE]
   >
   > Se il frammento di codice fa riferimento a nomi di azione che usano l'operatore punto (.), è necessario aggiungere i nomi di azione al [parametro **Actions** ](#add-parameters). Tali riferimenti devono anche racchiudere i nomi delle azioni tra parentesi quadre ([]) e virgolette, ad esempio:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   L'azione di codice inline non richiede `return` un'istruzione, ma i risultati di `return` un'istruzione sono disponibili per il riferimento nelle azioni successive tramite il token di **risultato** . 
   Ad esempio, il frammento di codice restituisce il risultato chiamando `match()` la funzione, che trova le corrispondenze nel corpo del messaggio di posta elettronica rispetto all'espressione regolare. L'azione **compose** usa il token di **risultato** per fare riferimento ai risultati dell'azione di codice inline e crea un singolo risultato.

   ![App per la logica completata](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Al termine, salvare l'app per la logica.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Trigger di riferimento e risultati dell'azione nel codice

L' `workflowContext` oggetto ha questa struttura, che include le `actions`sottoproprietà, `workflow` `trigger`e:

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
| `actions` | Raccolta di oggetti | Oggetti risultato da azioni eseguite prima dell'esecuzione del frammento di codice. Ogni oggetto ha una coppia *chiave-valore* in cui la chiave è il nome di un'azione e il valore equivale a chiamare la [funzione Actions ()](../logic-apps/workflow-definition-language-functions-reference.md#actions) con `@actions('<action-name>')`. Il nome dell'azione usa lo stesso nome di azione usato nella definizione del flusso di lavoro sottostante, che sostituisce gli spazi ("") nel nome dell'azione con caratteri di sottolineatura (_). Questo oggetto fornisce l'accesso ai valori delle proprietà dell'azione dall'esecuzione dell'istanza del flusso di lavoro corrente. |
| `trigger` | Object | Oggetto risultato dal trigger e equivalente alla chiamata della [funzione trigger ()](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Questo oggetto fornisce l'accesso ai valori delle proprietà trigger dall'esecuzione dell'istanza del flusso di lavoro corrente. |
| `workflow` | Object | Oggetto del flusso di lavoro e equivalente alla chiamata della [funzione Workflow ()](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Questo oggetto fornisce l'accesso ai valori delle proprietà del flusso di lavoro, ad esempio il nome del flusso di lavoro, l'ID esecuzione e così via, dall'esecuzione dell'istanza del flusso di lavoro corrente. |
|||

Nell'esempio di questo argomento, l' `workflowContext` oggetto dispone di queste proprietà a cui il codice può accedere:

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

## <a name="add-parameters"></a>Aggiungi parametri

In alcuni casi, potrebbe essere necessario richiedere in modo esplicito che l'azione del **codice inline** includa i risultati del trigger o azioni specifiche a cui il codice fa riferimento come dipendenze mediante l'aggiunta dei parametri **trigger** o **Actions** . Questa opzione è utile per gli scenari in cui i risultati a cui viene fatto riferimento non vengono individuati in fase di esecuzione.

> [!TIP]
> Se si prevede di riutilizzare il codice, aggiungere i riferimenti alle proprietà utilizzando la casella di **codice** in modo che il codice includa i riferimenti al token risolto, anziché aggiungere il trigger o le azioni come dipendenze esplicite.

Si supponga, ad esempio, di avere il codice che fa riferimento al risultato **SelectedOption** dall'azione **Invia messaggio di posta elettronica di approvazione** per il connettore Office 365 Outlook. Al momento della creazione, il motore app per la logica analizza il codice per determinare se è stato fatto riferimento a un trigger o a risultati dell'azione e include automaticamente tali risultati. In fase di esecuzione, se si riceve un errore in cui il trigger o il risultato dell'azione a cui si `workflowContext` fa riferimento non è disponibile nell'oggetto specificato, è possibile aggiungere tale trigger o azione come dipendenza esplicita. In questo esempio si aggiunge il parametro **Actions** e si specifica che l'azione di **codice inline** includa in modo esplicito il risultato dall'azione **Invia messaggio di posta elettronica di approvazione** .

Per aggiungere questi parametri, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati:

   ![Aggiungi parametri](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametro | Descrizione |
   |-----------|-------------|
   | **Actions** | Includere i risultati delle azioni precedenti. Vedere [includere i risultati dell'azione](#action-results). |
   | **Trigger** | Includere i risultati del trigger. Vedere [includere i risultati del trigger](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Includi risultati trigger

Se si seleziona **trigger**, viene richiesto se includere i risultati del trigger.

* Nell'elenco **trigger** selezionare **Sì**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Includi risultati azione

Se si seleziona **azioni**, vengono richieste le azioni che si desidera aggiungere. Tuttavia, prima di iniziare ad aggiungere azioni, è necessaria la versione del nome dell'azione che viene visualizzata nella definizione del flusso di lavoro sottostante dell'app per la logica.

* Questa funzionalità non supporta variabili, cicli e indici di iterazione.

* I nomi nella definizione del flusso di lavoro dell'app per la logica usano un carattere di sottolineatura (_), non uno spazio.

* Per i nomi di azione che usano l'operatore punto (.), includere tali operatori, ad esempio:

  `My.Action.Name`

1. Sulla barra degli strumenti della finestra di progettazione scegliere **visualizzazione codice**e cercare `actions` all'interno dell'attributo il nome dell'azione.

   Ad esempio, `Send_approval_email_` è il nome JSON per l'azione **Invia messaggio di posta elettronica di approvazione** .

   ![Trovare il nome dell'azione in JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Per tornare alla visualizzazione di progettazione, sulla barra degli strumenti della visualizzazione codice scegliere **finestra**di progettazione.

1. Per aggiungere la prima azione, nella casella **azioni elemento-1** immettere il nome JSON dell'azione.

   ![Immettere la prima azione](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Per aggiungere un'altra azione, scegliere **Aggiungi nuovo elemento**.

## <a name="reference"></a>Riferimenti

Per altre informazioni sulla struttura e la sintassi dell'azione **Esegui codice JavaScript** nella definizione del flusso di lavoro sottostante dell'app per la logica usando il linguaggio di definizione del flusso di lavoro, vedere la [sezione di riferimento](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)di questa azione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [connettori per le app per la logica di Azure](../connectors/apis-list.md)
