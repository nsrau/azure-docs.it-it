---
title: Istruzioni switch - Eseguire i passaggi in base a valori specifici - App per la logica di Azure | Microsoft Docs
description: Eseguire passaggi diversi a seconda dei valori di oggetti, espressioni o token nelle app per la logica
services: logic-apps
keywords: Istruzione switch
author: ecfan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: e1f515189be8a5659af0f6c29b3fac0550abc9f9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="switch-statements-run-different-steps-based-on-specific-values-in-logic-apps"></a>Istruzioni switch: eseguire passaggi diversi a seconda dei valori specifici nelle app per la logica

Per eseguire i passaggi diversi a seconda dei valori di un oggetto, un'espressione o un token, usare un'istruzione *switch*. Questa struttura valuta l'oggetto, l'espressione o il token, sceglie il case corrispondente al risultato, ed esegue i passaggi solo per tale case. Quando viene eseguita l'istruzione switch, un solo caso deve corrispondere al risultato.

Si supponga, ad esempio, che sia necessaria un'app per la logica che esegue passaggi diversi in base a un'opzione selezionata nel messaggio di posta elettronica. In questo esempio l'app per la logica verifica la presenza di nuovo contenuto nel feed RSS di un sito Web. Quando un nuovo elemento viene visualizzato nel feed RSS, l'app per la logica invia il messaggio di posta elettronica a un responsabile approvazione. A seconda che il responsabile approvazione selezioni "Approve" o "Reject", l'app per la logica segue passaggi diversi.

> [!TIP]
> Come tutti i linguaggi di programmazione, le istruzioni switch supportano solo gli operatori di uguaglianza. Se sono necessari altri operatori relazionali, ad esempio "maggiore di", usare un'[istruzione condizionale](#conditions).
> Per garantire il comportamento di esecuzione deterministico, i case devono contenere un valore univoco e statico invece di token o espressioni dinamiche.

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Per seguire l'esempio di questo articolo, [creare questa app per la logica di esempio](../logic-apps/quickstart-create-first-logic-app-workflow.md) con un account Outlook.com o Office 365 Outlook.

  1. Quando si aggiunge l'azione per inviare posta elettronica, selezionare invece **Send an approval email** (Invia un messaggio di posta elettronica di approvazione).

     ![Selezionare "Send an approval email" (Invia un messaggio di posta elettronica di approvazione)](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. Compilare i campi obbligatori, ad esempio l'indirizzo di posta elettronica della persona che riceve il messaggio di posta elettronica di approvazione. 
  In **Opzioni utente**, inserire "Approve, Reject".

     ![Immettere i dettagli del messaggio di posta elettronica](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>Aggiungere un'istruzione switch

1. Alla fine del flusso di lavoro di esempio, scegliere **+ Nuovo passaggio** > **... Altro** > **Aggiungi istruzione switch case**. 

   ![Aggiungere un'istruzione switch](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Viene visualizzata un'istruzione switch con un case e un case predefinito. 
   Le istruzioni switch richiedono almeno un case oltre al case predefinito. 

   Quando si vuole aggiungere un'istruzione switch tra un passaggio e l'altro, spostare il puntatore sulla freccia in cui si vuole aggiungere l'istruzione switch. 
   Scegliere il **segno più** (**+**) visualizzato, quindi **Aggiungi istruzione switch-case**.

4. Nella casella **Sì** selezionare il campo **SelectedOption** il cui output determina l'azione da eseguire. 
   
   È possibile selezionare il campo nell'elenco **Aggiungi contenuto dinamico** visualizzato.

5. Per gestire i case in cui il responsabile approvazione seleziona `Approve` o `Reject`, aggiungere un altro case tra **Case** e **Predefinito**. 
   
6. Aggiungere queste azioni ai case corrispondenti:

   | N. case | **SelectedOption** | Azione |
   |:------ |:-------------------|:------ |
   | Case 1 | **Approve** | Aggiungere l'azione **Invia un messaggio di posta elettronica** di Outlook per inviare i dettagli dell'elemento RSS solo quando il responsabile approvazione ha selezionato **Approve**. |
   | Case 2 | **Reject** | Aggiungere l'azione **Invia un messaggio di posta elettronica** di Outlook per notificare agli altri responsabili approvazione che l'elemento RSS è stato rifiutato. |
   | Predefinito | \<nessuna\> | Nessuna azione necessaria. In questo esempio il case **predefinito** è vuoto perché **SelectedOption** ha solo due opzioni. |
   |         |          |

   ![Istruzioni switch](./media/logic-apps-control-flow-switch-statement/switch.png)

7. Salvare l'app per la logica. 

   Per testare manualmente questo esempio, scegliere **Esegui** finché l'app per la logica non trova un nuovo elemento RSS e invia un messaggio di posta elettronica di approvazione. 
   Selezionare **Approve** per osservare i risultati.

## <a name="json-definition"></a>Definizione JSON

Dopo aver creato un'app per la logica usando un'istruzione switch, esaminare la definizione del codice generale dietro l'istruzione switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
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

| Etichetta              | DESCRIZIONE |
| :----------------- | :---------- |
| `"Switch"`         | Nome dell'istruzione switch che può essere rinominata per migliorarne la leggibilità |
| `"type": "Switch"` | Specifica che l'azione è un'istruzione switch |
| `"expression"`     | In questo esempio specifica l'opzione selezionata dal responsabile approvazione che viene valutata in ogni case dichiarato successivamente nella definizione |
| `"cases"` | Definisce qualsiasi numero di case. Per ogni case, `"Case_*"` è il nome predefinito di tale case, che è possibile rinominare per migliorare la leggibilità |
| `"case"` | Specifica il valore del case, che deve essere un valore costante e univoco usato dall'istruzione switch per effettuare un confronto. Se nessun case corrisponde al risultato dell'espressione switch, vengono eseguite le azioni nella sezione `"default"`.
|           |         |

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare suggerimenti relativi alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire i passaggi in base a una condizione (istruzioni condizionali)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Eseguire e ripetere i passaggi (cicli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Eseguire o unire passaggi paralleli (rami)](../logic-apps/logic-apps-control-flow-branches.md)
* [Eseguire i passaggi in base allo stato delle azioni raggruppate (ambiti)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)