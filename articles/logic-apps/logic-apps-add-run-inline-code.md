---
title: Aggiungere ed eseguire frammenti di codice utilizzando codice inline
description: Informazioni su come creare ed eseguire frammenti di codice usando le azioni del codice inline per le attività automatizzate e i flussi di lavoro creati con le app per la logica di AzureLearn how to create and run code snippets by using inline code actions for automated tasks and workflows that you create with Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453504"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Aggiungere ed eseguire frammenti di codice usando il codice inline nelle app per la logica di AzureAdd and run code snippets by using inline code inazure Apps in Azure Logic Apps

Quando vuoi eseguire una parte di codice all'interno dell'app per la logica, puoi aggiungere l'azione **Codice inlinea** predefinita come passaggio nel flusso di lavoro dell'app per la logica. Questa azione funziona meglio quando si desidera eseguire codice che si adatta a questo scenario:This action works best when you want to run code that fits this scenario:

* Viene eseguito in JavaScript. Altre lingue in arrivo.
* Termina in esecuzione in cinque secondi o meno.
* Gestisce i dati di dimensioni fino a 50 MB.
* Non richiede l'utilizzo delle azioni [ **Variabili** ](../logic-apps/logic-apps-create-variables-store-values.md), che non sono ancora supportate.
* Utilizza Node.js versione 8.11.1. Per ulteriori informazioni, consultate [Oggetti incorporati standard.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects) 

  > [!NOTE]
  > La `require()` funzione non è supportata dall'azione **Codice inline** per l'esecuzione di JavaScript.

Questa azione esegue il frammento di codice e restituisce l'output di tale frammento come token denominato **Result**, che è possibile usare nelle azioni successive nell'app per la logica. Per altri scenari in cui si vuole creare una funzione per il codice, provare a [creare e chiamare](../logic-apps/logic-apps-azure-functions.md) una funzione di Azure nell'app per la logica.

In questo articolo, l'app per la logica di esempio viene attivata quando arriva un nuovo messaggio di posta elettronica in un account di Outlook di Office 365. Il frammento di codice estrae e restituisce tutti gli indirizzi di posta elettronica visualizzati nel corpo dell'e-mail.

![Panoramica di esempio](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica in cui si vuole aggiungere il frammento di codice, incluso un trigger. Se non si dispone di un'app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   L'app per la logica di esempio in questo argomento usa questo trigger di Office 365 Outlook: **all'arrivo di un nuovo messaggio** di posta elettronicaThe example logic app in this topic uses this Office 365 Outlook trigger: When a new email arrives

* Un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) collegato all'app per la logica

  > [!NOTE]
  > Assicurarsi di utilizzare un account di integrazione appropriato per il caso d'uso o lo scenario. Ad esempio, gli account di integrazione a [livello](../logic-apps/logic-apps-pricing.md#integration-accounts) gratuito sono pensati solo per scenari esplorativi e carichi di lavoro, non per scenari di produzione, sono limitati in termini di utilizzo e velocità effettiva e non sono supportati da un contratto di servizio. Altri livelli comportano costi, ma includono il supporto del contratti di servizio, offrono più velocità effettiva e hanno limiti più elevati. Ulteriori informazioni sui [livelli](../logic-apps/logic-apps-pricing.md#integration-accounts)di account di integrazione, [sui prezzi](https://azure.microsoft.com/pricing/details/logic-apps/)e sui [limiti](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="add-inline-code"></a>Aggiungere codice inlineAdd inline code

1. Se non è già stato fatto, nel portale di [Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Nella finestra di progettazione aggiungere l'azione **Codice inline** nel percorso desiderato nel flusso di lavoro dell'app per la logica.

   * Per aggiungere l'azione alla fine del flusso di lavoro, scegliere **Nuovo passaggio**.

   * Per aggiungere l'azione tra i passaggi esistenti, spostare il puntatore del mouse sulla freccia che connette tali passaggi. Scegliere il segno**+** più ( ) e selezionare **Aggiungi un'azione**.

   In questo esempio viene aggiunta l'azione **Codice in linea** sotto il trigger di Office 365 Outlook.

   ![Aggiungi nuovo passaggio](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. In **Scegliere un'azione**immettere "codice inline" come filtro nella casella di ricerca. Dall'elenco delle azioni, selezionare questa azione: **Esegui codice JavaScript**

   ![Seleziona "Esegui codice JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   L'azione viene visualizzata nella finestra di progettazione e contiene codice di esempio predefinito, inclusa un'istruzione return.

   ![Azione Codice inline con codice di esempio predefinitoInline Code action with default sample code](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Nella casella **Codice** eliminare il codice di esempio e immettere il codice che si desidera eseguire. Scrivere il codice da inserire all'interno di un metodo, ma senza definire la firma del metodo. 

   Quando si digita una parola chiave riconosciuta, viene visualizzato l'elenco di completamento automatico che consente di selezionare una delle parole chiave disponibili, ad esempio:

   ![Elenco di completamento automatico delle parole chiave](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Questo frammento di codice di esempio crea innanzitutto una variabile che archivia *un'espressione regolare*che specifica un modello di corrispondenza nel testo di input. Il codice crea quindi una variabile che archivia i dati del corpo del messaggio di posta elettronica dal trigger.

   ![Creare le variabili](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Per rendere più semplici i risultati del trigger e delle azioni precedenti, l'elenco del contenuto dinamico viene visualizzato mentre il cursore si trova all'interno della casella **Codice.** Per questo esempio, l'elenco mostra i risultati disponibili dal trigger, incluso il token **Body,** che è ora possibile selezionare.

   Dopo aver selezionato il token **Body,** l'azione del `workflowContext` codice inline risolve `Body` il token in un oggetto che fa riferimento al valore della proprietà del messaggio di posta elettronica:After you select the Body token, the inline code action resolves the token to a object that references the email's property value:

   ![Selezionare il risultato](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Nella casella **Codice,** il frammento di `workflowContext` codice può utilizzare l'oggetto di sola lettura come input. Questo oggetto dispone di sottoproprietà che concedono al codice l'accesso ai risultati del trigger e delle azioni precedenti nel flusso di lavoro.
   Per ulteriori informazioni, vedere questa sezione più avanti in questo argomento: [Trigger di riferimento e l'azione genera il codice.](#workflowcontext)

   > [!NOTE]
   >
   > Se il frammento di codice fa riferimento a nomi di azione che utilizzano l'operatore punto (.), è necessario aggiungere tali nomi di azione al [parametro **Actions** ](#add-parameters). Tali riferimenti devono inoltre racchiudere i nomi delle azioni tra parentesi quadre ([]) e tra virgolette, ad esempio:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   L'azione del codice inline `return` non richiede un'istruzione, ma i risultati di un'istruzione sono disponibili per riferimento nelle azioni successive tramite il token `return` **Result.The** inline code action doesn't require a statement, but the results from a statement are available for reference in later actions through the Result token. 
   Ad esempio, il frammento di `match()` codice restituisce il risultato chiamando la funzione, che trova le corrispondenze nel corpo del messaggio di posta elettronica rispetto all'espressione regolare. L'azione **Componi** usa il token **Result** per fare riferimento ai risultati dell'azione codice inline e crea un singolo risultato.

   ![App per la logica completata](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Al termine, salvare l'app per la logica.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Il trigger di riferimento e i risultati dell'azione nel codiceReference trigger and action results in your code

`workflowContext` L'oggetto dispone di questa `actions` `trigger`struttura, `workflow` che include le sottoproprietà , e :

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

Questa tabella contiene ulteriori informazioni su queste sottoproprietà:This table contains more information about these subproperties:

| Proprietà | Type | Descrizione |
|----------|------|-------|
| `actions` | Raccolta di oggetti | Oggetti dei risultati dalle azioni eseguite prima dell'esecuzione del frammento di codice. Ogni oggetto ha una coppia *chiave-valore* in cui la chiave è il nome di `@actions('<action-name>')`un'azione e il valore equivale a chiamare la [funzione actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions) con . Il nome dell'azione utilizza lo stesso nome di azione utilizzato nella definizione del flusso di lavoro sottostante, che sostituisce gli spazi (" ") nel nome dell'azione con caratteri di sottolineatura (_). Questo oggetto fornisce l'accesso ai valori delle proprietà dell'azione dall'esecuzione dell'istanza del flusso di lavoro corrente. |
| `trigger` | Oggetto | Oggetto risultato dal trigger ed equivalente alla chiamata della [funzione trigger().](../logic-apps/workflow-definition-language-functions-reference.md#trigger) Questo oggetto fornisce l'accesso per attivare i valori delle proprietà dall'esecuzione dell'istanza del flusso di lavoro corrente. |
| `workflow` | Oggetto | L'oggetto flusso di lavoro ed equivalente alla chiamata della [funzione workflow().](../logic-apps/workflow-definition-language-functions-reference.md#workflow) Questo oggetto fornisce l'accesso ai valori delle proprietà del flusso di lavoro, ad esempio il nome del flusso di lavoro, l'ID di esecuzione e così via, dall'esecuzione dell'istanza del flusso di lavoro corrente. |
|||

Nell'esempio di questo `workflowContext` argomento, l'oggetto dispone di queste proprietà a cui il codice può accedere:In this topic's example, the object has these properties that your code can access:

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

In alcuni casi, potrebbe essere necessario richiedere in modo esplicito che l'azione **Codice inline** includa i risultati del trigger o azioni specifiche a cui il codice fa riferimento come dipendenze aggiungendo i parametri **Trigger** o **Actions.** Questa opzione è utile per gli scenari in cui i risultati a cui si fa riferimento non vengono trovati in fase di esecuzione.

> [!TIP]
> Se si prevede di riutilizzare il codice, aggiungere riferimenti alle proprietà usando la casella **Codice** in modo che includa i riferimenti ai token risolti, anziché aggiungere il trigger o le azioni come dipendenze esplicite.

Si supponga, ad esempio, di disporre di codice che fa riferimento al risultato **SelectedOption** dall'azione Invia messaggio di posta elettronica di **approvazione** per il connettore di Outlook di Office 365. Al momento della creazione, il motore App per la logica analizza il codice per determinare se è stato fatto riferimento a qualsiasi trigger o risultati dell'azione e include tali risultati automaticamente. In fase di esecuzione, se si ottiene un errore che il trigger `workflowContext` di riferimento o il risultato dell'azione non è disponibile nell'oggetto specificato, è possibile aggiungere tale trigger o azione come dipendenza esplicita. In questo esempio si aggiunge il parametro **Actions** e si specifica che l'azione **Codice inline** include in modo esplicito il risultato dell'azione Invia messaggio di posta elettronica di **approvazione.**

Per aggiungere questi parametri, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati:

   ![Aggiungere parametri](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametro | Descrizione |
   |-----------|-------------|
   | **Azioni** | Includere i risultati delle azioni precedenti. Consultate [Includere i risultati dell'azione.](#action-results) |
   | **Grilletto** | Includere i risultati dal trigger. Consultate [Includere i risultati del trigger.](#trigger-results) |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Includi risultati trigger

Se si seleziona **Trigger**, viene richiesto se includere i risultati del trigger.

* Nell'elenco **Trigger** selezionare **Sì**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Includi risultati azione

Se si seleziona **Azioni**, vengono richieste le azioni che si desidera aggiungere. Tuttavia, prima di iniziare ad aggiungere azioni, è necessaria la versione del nome dell'azione visualizzata nella definizione del flusso di lavoro sottostante dell'app per la logica.

* Questa funzionalità non supporta variabili, cicli e indici di iterazione.

* I nomi nella definizione del flusso di lavoro dell'app per la logica usano un carattere di sottolineatura (_), non uno spazio.

* Per i nomi di azione che utilizzano l'operatore punto (.), includere tali operatori, ad esempio:

  `My.Action.Name`

1. Nella barra degli strumenti della finestra di `actions` progettazione scegliere Visualizzazione **Codice**ed eseguire la ricerca all'interno dell'attributo per il nome dell'azione.

   Ad esempio, `Send_approval_email_` è il nome JSON per l'azione Invia messaggio di posta elettronica di **approvazione.**

   ![Trovare il nome dell'azione in JSONFind action name in JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Per tornare alla visualizzazione Progettazione, sulla barra degli strumenti della visualizzazione codice scegliere **Finestra di progettazione**.

1. Per aggiungere la prima azione, nella casella **Elemento azioni - 1** immettere il nome JSON dell'azione.

   ![Inserisci la prima azione](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Per aggiungere un'altra azione, scegliere **Aggiungi nuovo elemento**.

## <a name="reference"></a>Riferimento

Per altre informazioni sulla struttura e la sintassi dell'azione **Esegui codice JavaScript** nella definizione del flusso di lavoro sottostante dell'app per la logica usando il linguaggio di definizione del flusso di lavoro, vedere la [sezione di riferimento](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)di questa azione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui connettori per le app per la logica di AzureLearn more about [Connectors for Azure Logic Apps](../connectors/apis-list.md)
