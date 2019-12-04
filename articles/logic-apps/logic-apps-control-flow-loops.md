---
title: Aggiungere cicli per ripetere le azioni
description: Creare cicli che ripetono le azioni del flusso di lavoro o elaborano le matrici nelle App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791757"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Creare cicli che ripetono le azioni del flusso di lavoro o elaborano le matrici nelle App per la logica di Azure

Per elaborare una matrice nell'app per la logica, è possibile creare un [ciclo "Foreach"](#foreach-loop). Questo ciclo ripete una o più azioni su ogni elemento della matrice. Per i limiti al numero massimo di elementi di una matrice che i cicli "Foreach" possono elaborare, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md). 

Per ripetere le azioni fino a quando una condizione non viene soddisfatta o non viene modificato uno stato, è possibile creare un [ciclo "Until"](#until-loop). L'app per la logica esegue prima tutte le azioni all'interno del ciclo, quindi controlla la condizione o lo stato. Se la condizione viene soddisfatta, il ciclo si arresta. In caso contrario, il ciclo viene ripetuto. Per i limiti al numero massimo di cicli "Until" in un'esecuzione dell'app per la logica, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Se è presente un trigger che riceve una matrice e si vuole eseguire un flusso di lavoro per ogni elemento della matrice, è possibile *eseguire il debatch* di tale matrice con la [proprietà **SplitOn** per il trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Ciclo "Foreach"

Un ciclo "Foreach" ripete una o più azioni su ogni elemento della matrice e funziona solo nelle matrici. In un ciclo "Foreach" le iterazioni sono eseguite in parallelo. È tuttavia possibile eseguire un'iterazione per volta configurando un [ciclo "Foreach" sequenziale](#sequential-foreach-loop). 

Di seguito sono riportate alcune considerazioni in caso di uso dei cicli "Foreach":

* Nei cicli annidati le iterazioni vengono sempre eseguite in sequenza, non in parallelo. Per eseguire le operazioni in parallelo per gli elementi di un ciclo annidato, creare e [chiamare un'app per la logica figlio](../logic-apps/logic-apps-http-endpoint.md).

* Per ottenere risultati prevedibili dalle operazioni sulle variabili durante ogni iterazione del ciclo, eseguire i cicli in modo sequenziale. Quando ad esempio un ciclo in esecuzione simultanea termina, l'incremento, il decremento e l'aggiunta alle operazioni sulle variabili restituiscono risultati prevedibili. Tuttavia, durante ogni iterazione del ciclo in esecuzione simultanea, queste operazioni potrebbero restituire risultati imprevisti. 

* Le azioni in un ciclo "Foreach" usano l'espressione [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
per fare riferimento a ogni elemento nella matrice ed elaborarlo. Se si specificano dati non presenti in una matrice, il flusso di lavoro dell'app per la logica ha esito negativo. 

Questa app per la logica di esempio invia un riepilogo giornaliero per un feed RSS del sito Web. L'app usa un ciclo "Foreach" che invia un messaggio di posta elettronica per ogni nuovo elemento.

1. [Creare questa app per la logica di esempio](../logic-apps/quickstart-create-first-logic-app-workflow.md) con un account Outlook.com o Office 365 Outlook.

2. Tra il trigger RSS e l'azione di invio del messaggio di posta elettronica, aggiungere un ciclo "Foreach". 

   1. Per aggiungere un ciclo tra i passaggi, spostare il puntatore del mouse sulla freccia tra tali passaggi. 
   Scegliere il **segno più** ( **+** ) visualizzato, quindi selezionare **Aggiungi un'azione**.

      ![Selezionare "Add an action" (Aggiungi un'azione)](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Nella casella di ricerca scegliere **Tutti**. Nella casella di ricerca digitare "for each" come filtro. Nell'elenco azioni selezionare questa azione: **per ogni controllo**

      ![Aggiungere un ciclo "For each"](media/logic-apps-control-flow-loops/select-for-each.png)

3. A questo punto creare il ciclo. In **Selezionare un output dai passaggi precedenti**, quando viene visualizzato l'elenco **Aggiungi contenuto dinamico**, selezionare la matrice **Collegamenti ai feed**, che è l'output del trigger RSS. 

   ![Selezionare dall'elenco di contenuto dinamico](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > È possibile selezionare *solo* output della matrice dal passaggio precedente.

   La matrice selezionata ora viene visualizzata qui:

   ![Selezionare la matrice](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Per eseguire un'azione su ogni elemento della matrice, trascinare l'azione **Invia un messaggio di posta elettronica** nel ciclo. 

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
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Ciclo "Foreach": sequenziale

Per impostazione predefinita, in una versione di "Foreach" i cicli sono eseguiti in parallelo. Per eseguire ogni ciclo in sequenza, impostare l'opzione **Sequenziale** della versione. I cicli "Foreach" devono essere eseguiti in sequenza quando sono presenti cicli annidati o variabili all'interno dei cicli dove si attendono risultati prevedibili. 

1. Nell'angolo in alto a destra del ciclo scegliere i **puntini di sospensione** ( **...** ) > **Impostazioni**.

   ![Nel ciclo "Foreach" scegliere "..." > "Impostazioni"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. In **Controllo concorrenza** attivare l'opzione **Controllo concorrenza** impostandola su **Sì**. Spostare il dispositivo di scorrimento **Grado di parallelismo** sul numero **1** e scegliere **Fine**.

   ![Attivare il controllo della concorrenza](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Se si usa la definizione JSON dell'app per la logica, è possibile usare l'opzione `Sequential` aggiungendo il parametro `operationOptions`, ad esempio:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Ciclo "Until"
  
Per eseguire e ripetere le azioni fino a quando non viene soddisfatta una condizione o uno stato, inserire le azioni in un ciclo "until". L'app per la logica esegue prima tutte le azioni all'interno del ciclo, quindi controlla la condizione o lo stato. Se la condizione viene soddisfatta, il ciclo si arresta. In caso contrario, il ciclo viene ripetuto.

Ecco alcuni scenari comuni in cui è possibile usare un ciclo "Until":

* Chiamare un endpoint finché non si ottiene la risposta desiderata.

* Creare un record in un database. Attendere finché un campo specifico di tale record non viene approvato. Continuare l'elaborazione. 

A partire dalle 8:00 di ogni giorno, questa app per la logica di esempio incrementa una variabile finché il valore della variabile non è uguale a 10. L'app per la logica invia quindi un messaggio di posta elettronica che conferma il valore corrente. 

> [!NOTE]
> Questa procedura usa Office 365 Outlook, ma è possibile usare qualsiasi provider di posta elettronica supportato da App per la logica. 
> [Controllare l'elenco dei connettori qui](https://docs.microsoft.com/connectors/). Se si usa un altro account di posta elettronica, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa. 

1. Creare un'app per la logica vuota. In Progettazione app per la logica scegliere **Tutti** sotto la casella di ricerca. Cercare "ricorrenza". 
   Dall'elenco trigger selezionare questo trigger: **ricorrenza-pianificazione**

   ![Aggiungere il trigger "Ricorrenza - Pianificazione"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Specificare quando il trigger deve essere attivato impostando l'intervallo, la frequenza e l'ora del giorno. Per impostare l'ora, scegliere **Mostra opzioni avanzate**.

   ![Configurare la pianificazione della ricorrenza](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Proprietà | Value |
   | -------- | ----- |
   | **Interval** | 1 | 
   | **Frequenza** | Giorno |
   | **A queste ore** | 8 |
   ||| 

1. Nel trigger scegliere **Nuovo passaggio**. 
   Cercare "variabili" e selezionare questa azione: **Inizializza variabile-variabili**

   ![Aggiungere l'azione "Inizializza variabile - Variabili"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Configurare la variabile con questi valori:

   ![Impostare le proprietà della variabile](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Proprietà | Value | Description |
   | -------- | ----- | ----------- |
   | **Nome** | Limite | Nome della variabile | 
   | **Tipo** | Integer | Nome del tipo di dati della variabile | 
   | **Valore** | 0 | Valore iniziale della variabile | 
   |||| 

1. Sotto l'azione **Inizializza variabile** scegliere **Nuovo passaggio**. 

1. Nella casella di ricerca scegliere **Tutti**. Cercare "until" e selezionare questa azione: **until-Control**

   ![Aggiungere il ciclo "Until"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Creare la condizione di uscita dal ciclo selezionando la variabile **Limite** e l'operatore **è uguale a**. 
   Immettere **10** come valore di confronto.

   ![Creare la condizione di uscita per l'arresto di ciclo](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Nel ciclo scegliere **Aggiungi un'azione**. 

1. Nella casella di ricerca scegliere **Tutti**. Cercare "variabili" e selezionare questa azione: **incremento variabile-variabili**

   ![Aggiungere l'azione per incrementare la variabile](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. In **Nome** selezionare la variabile **Limite**. In **Valore** immettere "1". 

     ![Incrementare "Limite" di 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Esternamente e sotto il ciclo scegliere **Nuovo passaggio**. 

1. Nella casella di ricerca scegliere **Tutti**. 
     Trovare e aggiungere un'azione che invia posta elettronica, ad esempio: 

     ![Aggiungere l'azione per l'invio di posta elettronica](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Se richiesto, accedere all'account di posta elettronica.

1. Impostare le proprietà dell'azione di posta elettronica. Aggiungere la variabile **Limite** all'oggetto. In questo modo, è possibile verificare che il valore corrente della variabile soddisfi la condizione specificata, ad esempio:

      ![Configurare le proprietà del messaggio di posta elettronica](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Proprietà | Value | Description |
      | -------- | ----- | ----------- | 
      | **To** | *\<indirizzo di posta elettronica\@dominio >* | Indirizzo e-mail del destinatario. Per il test è possibile usare l'indirizzo di posta elettronica personale. | 
      | **Oggetto** | Il valore corrente per "Limite" è **Limite** | Specificare l'oggetto del messaggio di posta elettronica. Assicurarsi di includere in questo esempio la variabile **Limite**. | 
      | **Corpo** | <*contenuto del messaggio di posta elettronica*> | Specificare il contenuto del messaggio di posta elettronica da inviare. Per questo esempio, immettere il testo desiderato. | 
      |||| 

1. Salvare l'app per la logica. Per testare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione scegliere **Esegui**.

      Dopo l'avvio dell'esecuzione della logica, viene visualizzato un messaggio di posta elettronica con il contenuto specificato:

      ![Messaggio di posta elettronica ricevuto](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Evitare i cicli infiniti

Un ciclo "Until" ha limiti predefiniti che arrestano l'esecuzione se si verifica una di queste condizioni:

| Proprietà | Valore predefinito | Description | 
| -------- | ------------- | ----------- | 
| **Numero** | 60 | Numero massimo di cicli eseguiti prima della chiusura del ciclo. L'impostazione predefinita è 60 cicli. | 
| **Timeout** | PT1H | Intervallo di tempo massimo per l'esecuzione di un ciclo prima della chiusura del ciclo. L'impostazione predefinita è un'ora ed è specificata in formato ISO 8601. <p>Il valore di timeout viene valutato per ogni ciclo. Se un'azione del ciclo richiede più tempo di quello previsto dal limite di timeout, la sequenza corrente non viene arrestata. Il ciclo successivo non viene tuttavia avviato perché non viene soddisfatta la condizione limite. | 
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
      }
   }
}
```

Questo ciclo "Until" di esempio chiama un endpoint HTTP, che crea una risorsa. Il ciclo si arresta quando il corpo della risposta HTTP restituisce lo stato `Completed`. Per evitare i cicli infiniti, il ciclo viene arrestato anche se si verifica una di queste condizioni:

* Il ciclo è stato eseguito 10 volte, come specificato dall'attributo `count`. Il valore predefinito è 60 volte. 

* Il ciclo è stato eseguito per due ore, come specificato dall'attributo `timeout` in formato ISO 8601. Il valore predefinito è un'ora.
  
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
}
```

## <a name="get-support"></a>Ottenere supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare suggerimenti relativi alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire i passaggi in base a una condizione (istruzioni condizionali)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Eseguire i passaggi in base a valori diversi (istruzioni switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Eseguire o unire passaggi paralleli (rami)](../logic-apps/logic-apps-control-flow-branches.md)
* [Eseguire i passaggi in base allo stato delle azioni raggruppate (ambiti)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
