---
title: Aggiungere la istruzioni switch ai flussi di lavoro - App per la logica di Azure | Microsoft Docs
description: Come creare istruzioni switch che controllano le azioni del flusso di lavoro in base a valori specifici nella App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 2a3f8ee5cba3110d392555fad78c1cb2513b5d4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60683133"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Creare istruzioni switch che eseguono le azioni del flusso di lavoro in base a valori specifici nella App per la logica di Azure

Per eseguire azioni specifiche in base ai valori di oggetti, espressioni o token, aggiungere un'istruzione *switch*. Questa struttura valuta l'oggetto, l'espressione o il token, sceglie il case corrispondente al risultato, ed esegue azioni specifiche solo per tale case. Quando viene eseguita l'istruzione switch, un solo caso deve corrispondere al risultato.

Si supponga, ad esempio, che sia necessaria un'app per la logica che esegue passaggi diversi in base a un'opzione selezionata nel messaggio di posta elettronica. In questo esempio l'app per la logica verifica la presenza di nuovo contenuto nel feed RSS di un sito Web. Quando un nuovo elemento viene visualizzato nel feed RSS, l'app per la logica invia il messaggio di posta elettronica a un responsabile approvazione. A seconda che il responsabile approvazione selezioni "Approve" o "Reject", l'app per la logica segue passaggi diversi.

> [!TIP]
> Come tutti i linguaggi di programmazione, le istruzioni switch supportano solo gli operatori di uguaglianza. Se sono necessari altri operatori relazionali, ad esempio "maggiore di", usare un'[istruzione condizionale](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Per garantire il comportamento di esecuzione deterministico, i case devono contenere un valore univoco e statico invece di token o espressioni dinamiche.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Per seguire l'esempio di questo articolo, [creare questa app per la logica di esempio](../logic-apps/quickstart-create-first-logic-app-workflow.md) con un account Outlook.com o Office 365 Outlook.

  1. Quando si aggiunge l'azione per inviare posta elettronica, trovare e selezionare invece questa azione: **Inviare un'email di approvazione**

     ![Selezionare "Send an approval email" (Invia un messaggio di posta elettronica di approvazione)](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Compilare i campi obbligatori, ad esempio l'indirizzo di posta elettronica della persona che riceve il messaggio di posta elettronica di approvazione. 
  In **Opzioni utente**, inserire "Approve, Reject".

     ![Immettere i dettagli del messaggio di posta elettronica](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Aggiungere l'istruzione switch

1. Per questo esempio, aggiungere un'istruzione switch alla fine del flusso di lavoro di esempio. Dopo l'ultimo passaggio, scegliere **Nuovo passaggio**.

   Quando si vuole aggiungere un'istruzione switch tra un passaggio e l'altro, spostare il puntatore sulla freccia in cui si vuole aggiungere l'istruzione switch. Scegliere il **segno più** ( **+** ) visualizzato, quindi **Aggiungi un'azione**.

1. Nella casella di ricerca digitare "switch" come filtro. Selezionare questa azione: **Opzione - controllo**

   ![Aggiunta di switch](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Viene visualizzata un'istruzione switch con un case e un case predefinito. 
   Per impostazione predefinita, un'istruzione switch richiede almeno un case oltre a quello predefinito. 

   ![Istruzione switch predefinita vuota](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Fare clic all'interno della casella **On** (Su) per visualizzare l'elenco di contenuti dinamici. Nell'elenco selezionare il campo **SelectedOption** il cui output determina l'azione da eseguire. 

   ![Selezione di "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Per gestire i case in cui il responsabile approvazione seleziona `Approve` o `Reject`, aggiungere un altro case tra **Case** e **Predefinito**. 

   ![Aggiunta di un altro case](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Aggiungere queste azioni ai case corrispondenti:

   | N. case | **SelectedOption** | Azione |
   |--------|--------------------|--------|
   | Case 1 | **Approve** | Aggiungere l'azione **Invia un messaggio di posta elettronica** di Outlook per inviare i dettagli dell'elemento RSS solo quando il responsabile approvazione ha selezionato **Approve**. |
   | Case 2 | **Reject** | Aggiungere l'azione **Invia un messaggio di posta elettronica** di Outlook per notificare agli altri responsabili approvazione che l'elemento RSS è stato rifiutato. |
   | Predefinito | Nessuna | Nessuna azione necessaria. In questo esempio il case **predefinito** è vuoto perché **SelectedOption** ha solo due opzioni. |
   |||

   ![Istruzione switch completata](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Salvare l'app per la logica. 

   Per testare manualmente questo esempio, scegliere **Esegui** finché l'app per la logica non trova un nuovo elemento RSS e invia un messaggio di posta elettronica di approvazione. 
   Selezionare **Approve** per osservare i risultati.

## <a name="json-definition"></a>Definizione JSON

Dopo aver creato un'app per la logica usando un'istruzione switch, esaminare la definizione del codice generale dietro l'istruzione switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Etichetta | Descrizione |
|-------|-------------|
| `"Switch"`         | Nome dell'istruzione switch che può essere rinominata per migliorarne la leggibilità |
| `"type": "Switch"` | Specifica che l'azione è un'istruzione switch |
| `"expression"`     | In questo esempio specifica l'opzione selezionata dal responsabile approvazione che viene valutata in ogni case dichiarato successivamente nella definizione |
| `"cases"` | Definisce qualsiasi numero di case. Per ogni case, `"Case_*"` è il nome predefinito di tale case, che è possibile rinominare per migliorare la leggibilità |
| `"case"` | Specifica il valore del case, che deve essere un valore costante e univoco usato dall'istruzione switch per effettuare un confronto. Se nessun case corrisponde al risultato dell'espressione switch, vengono eseguite le azioni nella sezione `"default"`. | 
| | | 

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare suggerimenti relativi alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire i passaggi in base a una condizione (istruzioni condizionali)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Eseguire e ripetere i passaggi (cicli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Eseguire o unire passaggi paralleli (rami)](../logic-apps/logic-apps-control-flow-branches.md)
* [Eseguire i passaggi in base allo stato delle azioni raggruppate (ambiti)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
