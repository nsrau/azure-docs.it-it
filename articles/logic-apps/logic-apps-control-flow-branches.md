---
title: Rami paralleli - App per la logica di Azure | Microsoft Docs
description: Creare rami paralleli o crearne un join nelle app per la logica
services: logic-apps
keywords: rami, elaborazione parallela
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
ms.openlocfilehash: 9a836b707a576b9a938f43397ef35c00aeb476bf
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="create-or-join-parallel-branches-in-your-logic-app"></a>Creare rami paralleli o crearne un join nell'app per la logica

Per impostazione predefinita, le azioni in un'app per la logica vengono eseguite in sequenza. Per eseguire simultaneamente azioni indipendenti, è possibile creare [rami paralleli](#parallel-branches) e quindi [creare un join di tali rami](#join-branches) più avanti nel flusso. 

> [!TIP] 
> Se è presente un trigger che riceve una matrice e si vuole eseguire un flusso di lavoro per ogni elemento della matrice, è possibile *eseguire il debatch* di tale matrice con la [proprietà **SplitOn** per il trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-a-parallel-branch"></a>Aggiungere un ramo parallelo

Per eseguire simultaneamente passaggi indipendenti, è possibile aggiungere rami paralleli accanto a un passaggio esistente. 

![Eseguire i passaggi in parallelo](media/logic-apps-control-flow-branches/parallel.png)

L'app per la logica attende il completamento di tutti i rami prima di continuare il flusso di lavoro.
I rami paralleli vengono eseguiti solo quando i valori della proprietà `runAfter` corrispondono allo stato del passaggio padre completato. Ad esempio, sia `branchAction1` che `branchAction2` sono impostati per essere eseguiti solo quando `parentAction` termina con lo stato `Succeded`.

> [!NOTE]
> Prima di iniziare, l'app per la logica deve avere già un passaggio in cui è possibile aggiungere rami paralleli.

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire l'app per la logica in Progettazione app per la logica.

2. Spostare il mouse sulla freccia sopra il passaggio in cui si vogliono aggiungere i rami paralleli.

3. Scegliere il segno **più** (**+**), scegliere **Aggiungi un ramo parallelo** e selezionare l'elemento che si vuole aggiungere.

   ![Aggiungere il ramo parallelo](media/logic-apps-control-flow-branches/add-parallel-branch.png)

   L'elemento selezionato viene ora visualizzato in un ramo parallelo.

4. Per ogni ramo parallelo, aggiungere i passaggi desiderati. Per aggiungere un'azione sequenziale a un ramo parallelo, spostare il mouse sotto l'azione in cui si vuole aggiungere l'azione sequenziale. Scegliere il segno **più** (**+**) e i passaggi che si vuole aggiungere.

   ![Aggiungere il passaggio sequenziale al ramo parallelo](media/logic-apps-control-flow-branches/add-sequential-action-parallel-branch.png)

5. Per unire di nuovo i rami, [creare un join dei rami paralleli](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definizione dei rami paralleli (JSON)

Se si usa la visualizzazione Codice, è invece possibile definire la struttura parallela nella definizione JSON dell'app per la logica, ad esempio:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Creare un join dei rami paralleli

Per unire i rami paralleli, è sufficiente aggiungere un passaggio in basso sotto tutti i rami. Questo passaggio viene eseguito al termine dell'esecuzione di tutti i rami paralleli.

![Creare un join dei rami paralleli](media/logic-apps-control-flow-branches/join.png)

1. Nel [portale di Azure](https://portal.azure.com) individuare e aprire l'app per la logica in Progettazione app per la logica. 

2. Sotto i rami paralleli di cui si vuole creare un join aggiungere il passaggio che si vuole eseguire.

   ![Aggiungere un passaggio che crea un join dei rami paralleli](media/logic-apps-control-flow-branches/join-steps.png)

   I rami paralleli ora risultano uniti.

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definizione del join (JSON)

Se si usa la visualizzazione Codice, è invece possibile definire la struttura del join nella definizione JSON dell'app per la logica, ad esempio:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare suggerimenti relativi alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire i passaggi in base a una condizione (istruzioni condizionali)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Eseguire i passaggi in base a valori diversi (istruzioni switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Eseguire e ripetere i passaggi (cicli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Eseguire i passaggi in base allo stato delle azioni raggruppate (ambiti)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)