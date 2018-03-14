---
title: Istruzioni condizionali - Eseguire i passaggi in base a una condizione - App per la logica di Azure | Microsoft Docs
description: Eseguire i passaggi nell'app per la logica solo dopo avere soddisfatto una condizione. Creare alberi delle decisioni che eseguono flussi di lavoro in base a condizioni specificate.
services: logic-apps
keywords: Istruzioni condizionali, alberi delle decisioni
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 486c1053f42ed3becc2c4b60accc993db7f24baa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="conditional-statements-run-steps-based-on-a-condition-in-logic-apps"></a>Istruzioni condizionali: eseguire i passaggi in base a una condizione nelle app per la logica

Per eseguire i passaggi solo dopo avere passato una condizione specificata, usare un'*istruzione condizionale*. Questa struttura confronta i dati nel flusso di lavoro con valori o campi specifici. È quindi possibile definire passaggi diversi da eseguire a seconda che i dati soddisfino o meno la condizione. È possibile annidare le condizioni all'interno di altre.

Si supponga, ad esempio, di avere un'app per la logica che invia troppi messaggi di posta elettronica quando vengono visualizzati nuovi elementi sul feed RSS di un sito Web. È possibile aggiungere un'istruzione condizionale per inviare posta elettronica solo quando il nuovo elemento include una stringa specifica. 

> [!TIP]
> Per eseguire passaggi diversi in base a valori specifici, usare invece un'[*istruzione switch*](../logic-apps/logic-apps-control-flow-switch-statement.md).

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Per seguire l'esempio di questo articolo, [creare questa app per la logica di esempio](../logic-apps/quickstart-create-first-logic-app-workflow.md) con un account Outlook.com o Office 365 Outlook.

## <a name="add-a-condition"></a>Aggiungere una condizione

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire l'app per la logica in Progettazione app per la logica.

2. Aggiungere una condizione nella posizione desiderata. 

   Per aggiungere una condizione tra un passaggio e l'altro, spostare il puntatore sulla freccia in cui si vuole aggiungere la condizione. Scegliere il **segno più** (**+**) visualizzato, quindi **Aggiungi una condizione**. Ad esempio: 

   ![Aggiungere la condizione tra un passaggio e l'altro](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Quando si vuole aggiungere una condizione alla fine del flusso di lavoro, nella parte inferiore dell'app per la logica scegliere **+ Nuovo passaggio** > **Aggiungi una condizione**.

3. Sotto **Condizione** creare la condizione. 

   1. Nella casella a sinistra specificare i dati o i campi che si vuole confrontare.

      Nell'elenco **Aggiungi contenuto dinamico** è possibile selezionare i campi esistenti dall'app per la logica.

   2. Nell'elenco centrale selezionare l'operazione da eseguire. 
   3. Nella casella a destra specificare un valore o un campo come criterio.

   Ad esempio: 

   ![Modificare la condizione nella modalità di base](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   Questa è la condizione completa:

   ![Condizione completa](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Per creare una condizione più avanzata o usare le espressioni, scegliere **Modifica in modalità avanzata**. È possibile usare le espressioni definite dal [linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md).
   > 
   > Ad esempio: 
   >
   > ![Modificare la condizione nel codice](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. In **SE SÌ** e **SE NO** aggiungere i passaggi da eseguire in base al fatto che la condizione sia soddisfatta o meno. Ad esempio: 

   ![Condizione con percorsi SÌ e NO](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > È possibile trascinare le azioni esistenti nei percorsi **SE SÌ** e **SE NO**.

6. Salvare l'app per la logica.

Questa app per la logica ora invia messaggi solo quando i nuovi elementi nel feed RSS soddisfano la condizione.

## <a name="json-definition"></a>Definizione JSON

Dopo aver creato un'app per la logica usando un'istruzione condizionale, esaminare la definizione del codice generale dietro l'istruzione condizionale.

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare suggerimenti relativi alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire i passaggi in base a valori diversi (istruzioni switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Eseguire e ripetere i passaggi (cicli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Eseguire o unire passaggi paralleli (rami)](../logic-apps/logic-apps-control-flow-branches.md)
* [Eseguire i passaggi in base allo stato delle azioni raggruppate (ambiti)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)