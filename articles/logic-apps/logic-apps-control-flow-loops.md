---
title: Cicli - Elaborare le matrici o ripetere le azioni - App per la logica di Azure | Microsoft Docs
description: "Elaborare le matrici con i cicli \"for each\" o ripetere le azioni finché non vengono soddisfatte condizioni specifiche nelle app per la logica"
services: logic-apps
keywords: cicli for each
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: f634b1004fef2eb65c6b8134088ceead47c91890
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="loops-process-arrays-or-repeat-actions-until-a-condition-is-met"></a>Cicli: elaborare le matrici o ripetere le azioni finché non viene soddisfatta una condizione

Per eseguire l'iterazione delle matrici nell'app per la logica, è possibile usare un [ciclo "Foreach"](#foreach-loop) o un [ciclo "Foreach" sequenziale](#sequential-foreach-loop). Nella versione standard di "Foreach" i cicli sono eseguiti in parallelo, mentre nella versione sequenziale sono eseguiti uno alla volta. Per il numero massimo di elementi di una matrice che i cicli "Foreach" possono elaborare in una singola esecuzione dell'app per la logica, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Se è presente un trigger che riceve una matrice e si vuole eseguire un flusso di lavoro per ogni elemento della matrice, è possibile *eseguire il debatch* di tale matrice con la [proprietà **SplitOn** per il trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Per ripetere azioni fino a quando non viene soddisfatta una condizione o è stato modificato uno stato, usare un [ciclo "Until"](#until-loop). L'app per la logica esegue prima tutte le azioni nel ciclo e quindi, come ultimo passaggio, controlla la condizione. Se la condizione viene soddisfatta, il ciclo si arresta. In caso contrario, il ciclo viene ripetuto. Per il numero massimo di elementi di cicli "Until" in una singola esecuzione dell'app per la logica, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Ciclo "Foreach"

Per ripetere le azioni per ogni elemento in una matrice, usare un ciclo "Foreach" nel flusso di lavoro dell'app per la logica. È possibile includere più azioni in un ciclo "Foreach" ed è possibile annidare i cicli "Foreach" all'interno di altri. Per impostazione predefinita, in una versione standard di "Foreach" i cicli sono eseguiti in parallelo. Per il numero massimo di cicli paralleli che "Foreach" può eseguire, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> Un ciclo "Foreach" funziona solo con una matrice e le azioni del ciclo usano il riferimento `@item()` per elaborare ogni elemento della matrice. Se si specificano dati non presenti in una matrice, il flusso di lavoro dell'app per la logica ha esito negativo. 

Questa app per la logica, ad esempio, invia un riepilogo giornaliero dal feed RSS di un sito Web. L'app usa un ciclo "Foreach" che invia un messaggio di posta elettronica per ogni nuovo elemento trovato.

1. [Creare questa app per la logica di esempio](../logic-apps/quickstart-create-first-logic-app-workflow.md) con un account Outlook.com o Office 365 Outlook.

2. Tra il trigger RSS e l'azione di invio del messaggio di posta elettronica, aggiungere un ciclo "Foreach". 

   Per aggiungere un ciclo tra un passaggio e l'altro, spostare il puntatore sulla freccia in cui si vuole aggiungere il ciclo. 
   Scegliere il **segno più** (**+**) visualizzato, quindi **Aggiungi For each**.

   ![Aggiungere un ciclo "Foreach" tra un passaggio e l'altro](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. A questo punto creare il ciclo. In **Selezionare un output dai passaggi precedenti**, quando viene visualizzato l'elenco **Aggiungi contenuto dinamico**, selezionare la matrice **Collegamenti ai feed**, che è l'output del trigger RSS. 

   ![Selezionare dall'elenco di contenuto dinamico](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > È possibile selezionare *solo* output della matrice dal passaggio precedente.

   La matrice selezionata ora viene visualizzata qui:

   ![Selezionare la matrice](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Per eseguire un'azione su ogni elemento della matrice, trascinare l'azione **Invia un messaggio di posta elettronica** nel ciclo **For each**. 

   L'app per la logica dovrebbe avere un aspetto simile all'esempio seguente:

   ![Aggiungere i passaggi al ciclo "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Salvare l'app per la logica. Per testare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione scegliere **Esegui**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definizione del ciclo "Foreach" (JSON)

Se si usa la visualizzazione Codice per l'app per la logica, è invece possibile definire il ciclo `Foreach` nella definizione JSON dell'app per la logica, ad esempio:

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {
                "type": "ApiConnection",
                "inputs": {
                    "body": {
                        "Body": "@{item()}",
                        "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                        "To": "me@contoso.com"
                    },
                    "host": {
                        "api": {
                            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                        },
                        "connection": {
                            "name": "@parameters('$connections')['office365']['connectionId']"
                        }
                    },
                    "method": "post",
                    "path": "/Mail"
                },
                "runAfter": {}
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Ciclo "Foreach": sequenziale

Per impostazione predefinita, ogni ciclo in "Foreach" viene eseguito in parallelo per ogni elemento della matrice. Per eseguire ogni ciclo in sequenza, impostare l'opzione **Sequenziale** in "Foreach".

1. Nell'angolo in alto a destra del ciclo scegliere i **puntini di sospensione** (**...**) > **Impostazioni**.

   ![Nel ciclo "Foreach" scegliere "..." > "Impostazioni"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Attivare l'impostazione **Sequenziale**, quindi scegliere **Fatto**.

   ![Attivare l'impostazione Sequenziale del ciclo "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

È anche possibile impostare il parametro **operationOptions** su `Sequential` nella definizione JSON dell'app per la logica. Ad esempio: 

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Ciclo "Until"
  
Per ripetere azioni fino a quando non viene soddisfatta una condizione o è stato modificato uno stato, usare un ciclo "Until" nel flusso di lavoro dell'app per la logica. Ecco alcuni casi d'uso comuni in cui è possibile usare un ciclo "Until":

* Chiamare un endpoint finché non si ottiene la risposta desiderata.
* Creare un record in un database, attendere finché un campo specifico in tale record non viene approvato e continuare l'elaborazione. 

> [!NOTE]
> I cicli "Until" non possono includere cicli "Foreach" o altri cicli "Until".

Ogni giorno alle 8:00, ad esempio, questa app per la logica incrementa una variabile finché il valore della variabile non è uguale a 10. L'app per la logica invia quindi un messaggio di posta elettronica che conferma il valore corrente. Anche se questo esempio usa Office 365 Outlook, è possibile usare qualsiasi provider di posta elettronica supportato da App per la logica. Vedere l'[elenco di connettori qui](https://docs.microsoft.com/connectors/). Se si usa un altro account di posta elettronica, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa. 

1. Creare un'app per la logica vuota. In Progettazione app per la logica cercare "ricorrenza" e selezionare questo trigger: **Pianificazione - Ricorrenza** 

   ![Aggiungere il trigger "Pianificazione - Ricorrenza"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Specificare quando il trigger deve essere attivato impostando l'intervallo, la frequenza e l'ora del giorno. Per impostare l'ora, scegliere **Mostra opzioni avanzate**.

   ![Aggiungere il trigger "Pianificazione - Ricorrenza"](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Proprietà | Valore |
   | -------- | ----- |
   | **Interval** | 1 | 
   | **Frequenza** | Giorno |
   | **A queste ore** | 8 |
   ||| 

3. Nel trigger scegliere **Nuovo passaggio** > **Add an action** (Aggiungi un'azione). Cercare "variabili" e quindi selezionare l'azione: **Variabili - Inizializza variabile**

   ![Aggiungere l'azione "Variabili - Inizializza variabile"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Configurare la variabile con questi valori:

   ![Impostare le proprietà della variabile](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Proprietà | Valore | DESCRIZIONE |
   | -------- | ----- | ----------- |
   | **Nome** | Limite | Nome della variabile | 
   | **Tipo** | Integer | Nome del tipo di dati della variabile | 
   | **Valore** | 0 | Valore iniziale della variabile | 
   |||| 

5. Sotto l'azione **Inizializza variabile** scegliere **Nuovo passaggio** > **Altro**. Selezionare questo ciclo: **Aggiungi Do until**

   ![Aggiungere il ciclo "do until"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Creare la condizione di uscita dal ciclo selezionando la variabile **Limite** e l'operatore **è uguale a**. Immettere **10** come valore di confronto.

   ![Creare la condizione di uscita per l'arresto di ciclo](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. Nel ciclo scegliere **Aggiungi un'azione**. Cercare "variabili" e quindi aggiungere l'azione: **Variabili - Incrementa variabile**

   ![Aggiungere l'azione per incrementare la variabile](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. In **Nome** selezionare la variabile **Limite**. In **Valore** immettere "1". 

   ![Incrementare "Limite" di 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. Sotto, ma al di fuori del ciclo aggiungere un'azione per l'invio di posta elettronica. Se richiesto, accedere all'account di posta elettronica.

   ![Aggiungere l'azione per l'invio di posta elettronica](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Impostare le proprietà del messaggio di posta elettronica. Aggiungere la variabile **Limite** all'oggetto. In questo modo, è possibile verificare che il valore corrente della variabile soddisfi la condizione specificata, ad esempio:

    ![Configurare le proprietà del messaggio di posta elettronica](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Proprietà | Valore | DESCRIZIONE |
    | -------- | ----- | ----------- | 
    | **To** | *<email-address@domain>* | Indirizzo e-mail del destinatario. Per il test è possibile usare l'indirizzo di posta elettronica personale. | 
    | **Oggetto** | Il valore corrente per "Limite" è **Limite** | Specificare l'oggetto del messaggio di posta elettronica. Assicurarsi di includere in questo esempio la variabile **Limite**. | 
    | **Corpo** | <*contenuto del messaggio di posta elettronica*> | Specificare il contenuto del messaggio di posta elettronica da inviare. Per questo esempio, immettere il testo desiderato. | 
    |||| 

11. Salvare l'app per la logica. Per testare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione scegliere **Esegui**.

    Dopo l'avvio dell'esecuzione della logica, viene visualizzato un messaggio di posta elettronica con il contenuto specificato:

    ![Messaggio di posta elettronica ricevuto](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Evitare i cicli infiniti

Un ciclo "Until" ha limiti predefiniti che arrestano l'esecuzione se si verifica una di queste condizioni:

| Proprietà | Valore predefinito | DESCRIZIONE | 
| -------- | ------------- | ----------- | 
| **Numero** | 60 | Numero massimo di cicli eseguiti prima della chiusura del ciclo. L'impostazione predefinita è 60 cicli. | 
| **Timeout** | PT1H | Intervallo di tempo massimo per l'esecuzione di un ciclo prima della chiusura del ciclo. L'impostazione predefinita è un'ora ed è specificata in formato ISO 8601. <p>Il valore di timeout viene valutato per ogni ciclo. Se un'azione richiede più tempo del limite di timeout, il ciclo corrente non viene arrestato, ma quello successivo non viene avviato perché la condizione del limite non è soddisfatta. | 
|||| 

Per modificare questi limiti predefiniti, scegliere **Mostra opzioni avanzate** nella forma dell'azione del ciclo.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definizione di "Until" (JSON)

Se si usa la visualizzazione Codice per l'app per la logica, è invece possibile definire un ciclo `Until` nella definizione JSON dell'app per la logica, ad esempio:

``` json
"actions": {
    "Initialize_variable": {
        // Definition for initialize variable action
    },
    "Send_an_email": {
        // Definition for send email action
    },
    "Until": {
        "type": "Until",
        "actions": {
            "Increment_variable": {
                "type": "IncrementVariable",
                "inputs": {
                    "name": "Limit",
                    "value": 1
                },
                "runAfter": {}
            }
        },
        "expression": "@equals(variables('Limit'), 10)",
        // To prevent endless loops, an "Until" loop 
        // includes these default limits that stop the loop. 
        "limit": { 
            "count": 60,
            "timeout": "PT1H"
        },
        "runAfter": {
            "Initialize_variable": [
                "Succeeded"
            ]
        },
    }
},
```

In un altro esempio questo ciclo "Until" chiama un endpoint HTTP che crea una risorsa e viene arrestato quando il corpo della risposta HTTP restituisce lo stato "Completed". Per evitare i cicli infiniti, il ciclo viene arrestato anche se si verifica una di queste condizioni:

* Il ciclo è stato eseguito 10 volte, come specificato dall'attributo `count`. Il valore predefinito è 60 volte. 
* Il ciclo ha provato l'esecuzione per due ore, come specificato dall'attributo `timeout` in formato ISO 8601. Il valore predefinito è un'ora.
  
``` json
"actions": {
    "myUntilLoopName": {
        "type": "Until",
        "actions": {
            "Create_new_resource": {
                "type": "Http",
                "inputs": {
                    "body": {
                        "resourceId": "@triggerBody()"
                    },
                    "url": "https://domain.com/provisionResource/create-resource",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                },
                "runAfter": {},
                "type": "ApiConnection"
            }
        },
        "expression": "@equals(triggerBody(), 'Completed')",
        "limit": {
            "count": 10,
            "timeout": "PT2H"
        },
        "runAfter": {}
    }
},
```

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare suggerimenti relativi alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire i passaggi in base a una condizione (istruzioni condizionali)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Eseguire i passaggi in base a valori diversi (istruzioni switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Eseguire o unire passaggi paralleli (rami)](../logic-apps/logic-apps-control-flow-branches.md)
* [Eseguire i passaggi in base allo stato delle azioni raggruppate (ambiti)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)