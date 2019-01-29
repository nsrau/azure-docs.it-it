---
title: Creare variabili per salvare i valori - App per la logica di Azure | Microsoft Docs
description: Come salvare e gestire i valori creando le variabili nelle App per la logica di Azure
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: bb84c7d5e483b0a2abc3b7d1a37de8760513d203
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063217"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Creare variabili per salvare e gestire i valori nelle App per la logica di Azure

Questo articolo illustra come archiviare e gestire i valori con l'app per la logica mediante la creazione di variabili. Ad esempio, le variabili consentono di contare il numero di volte in cui un ciclo viene eseguito. Durante l'iterazione su una matrice o la ricerca di un elemento specifico in una matrice è possibile usare una variabile per fare riferimento al numero di indice di ogni elemento della matrice. 

È possibile creare variabili per tipi di dati quali integer, float, booleano, stringa, matrice e oggetto. Dopo aver creato una variabile è possibile eseguire altre attività, ad esempio:

* Ottenere o fare riferimento al valore della variabile.
* Aumentare o diminuire la variabile di un valore costante, noto anche come *incremento* e *decremento*.
* Assegnare un valore diverso alla variabile.
* Inserire o *aggiungere* il valore della variabile come ultima volta in una stringa o matrice.

Le variabili esistono e sono globali solo all'interno dell'istanza dell'app per la logica che le crea. Inoltre persistono in tutte le iterazioni di ciclo all'interno di un'istanza dell'app per la logica. Per fare riferimento a una variabile, usare il nome della variabile come token, non il nome dell'azione, che è il modo usuale per fare riferimento agli output di un'azione. 

> [!IMPORTANT]
> Per impostazione predefinita, in una versione di "Foreach" i cicli sono eseguiti in parallelo. Quando si usano le variabili nei cicli, eseguire il ciclo [in sequenza](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) in modo che le variabili restituiscano risultati prevedibili. 

Se non si dispone ancora di una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">registrarsi per creare un account Azure gratuito</a>. 

## <a name="prerequisites"></a>Prerequisiti

Per seguire questo articolo, ecco gli elementi necessari:

* App per la logica in cui si desidera creare una variabile 

  Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) come primo passo nell'app per la logica 

  Prima di poter aggiungere azioni per creare e gestire le variabili, l'app per la logica deve iniziare con un trigger.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inizializzare una variabile

È possibile creare una variabile e dichiararne il tipo e il valore iniziale, il tutto in un'unica azione nell'app per la logica. È possibile dichiarare solo variabili a livello globale, non all'interno di ambiti, condizioni e cicli. 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). 

   In questo esempio si usa il portale di Azure e un'app per la logica con un trigger esistente.

2. Nell'app per la logica, sotto il punto in cui si desidera aggiungere una variabile, seguire uno di questi passaggi: 

   * Per aggiungere un'azione sotto l'ultimo passaggio scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione).

     ![Aggiungere un'azione](./media/logic-apps-create-variables-store-values/add-action.png)

   * Per aggiungere un'azione tra due passaggi posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). 
   Fare clic sul segno più e quindi scegliere **Add an action** (Aggiungi un'azione).

3. Nella casella di ricerca immettere "variables" come filtro. Dall'elenco delle azioni selezionare **Variables - Initialize variable** (Variabili - Inizializzare una variabile).

   ![Seleziona azione](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Fornire queste informazioni per la variabile:

   | Proprietà | Obbligatoria | Value |  DESCRIZIONE |
   |----------|----------|-------|--------------|
   | Name | Yes | <*variable-name*> | Nome della variabile da incrementare | 
   | Type | Yes | <*variable-type*> | Tipo di dati per la variabile | 
   | Value | No  | <*start-value*> | Valore iniziale della variabile <p><p>**Suggerimento**: anche se è facoltativo, impostare questo valore come procedura consigliata per poter conoscere sempre il valore iniziale della variabile. | 
   ||||| 

   ![Inizializzare una variabile](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Continuare quindi ad aggiungere le azioni desiderate. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

Passando dalla finestra di progettazione all'editor di visualizzazione del codice, ecco come viene visualizzata l'azione **Inizializzare una variabile** all'interno della definizione dell'app per la logica, nel formato JSON (JavaScript Object Notation):

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

Ecco alcuni esempi per altri tipi di variabili:

*Variabile stringa*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Variabile booleana*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Matrice con numeri interi*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Matrice con stringhe*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Ottenere il valore della variabile

Per recuperare o fare riferimento al contenuto di una variabile è anche possibile usare la [funzione variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) in Logic App Designer (Progettazione app per la logica) e l'editor di visualizzazione del codice.
Per fare riferimento a una variabile, usare il nome della variabile come token, non il nome dell'azione, che è il modo usuale per fare riferimento agli output di un'azione. 

Ad esempio, questa espressione ottiene gli elementi dalla variabile della matrice [creata in precedenza in questo articolo](#append-value) usando la funzione **variables()**. La funzione **string()** restituisce il contenuto della variabile in formato stringa: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Incrementare una variabile 

Per aumentare o *incrementare* una variabile di un valore costante aggiungere l'azione **Variables - Increment variable** (Variabili - Incrementare una variabile) all'app per la logica. Questa operazione funziona solo con le variabili integer e float.

1. In Logic App Designer (Progettazione app per la logica) sotto il passaggio in cui si desidera aumentare una variabile esistente, scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione). 

   Ad esempio, questa app per la logica ha già un trigger e un'azione che ha creato una variabile. In tal caso, aggiungere una nuova azione con questi passaggi:

   ![Aggiungere un'azione](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). Fare clic sul segno più e quindi scegliere **Add an action** (Aggiungi un'azione).

2. Nella casella di ricerca immettere "increment variable" come filtro. Dall'elenco delle azioni selezionare **Variables - Increment variable** (Variabili - Incrementare una variabile).

   ![Selezionare l'azione "Incrementare una variabile"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Fornire queste informazioni per incrementare la variabile:

   | Proprietà | Obbligatoria | Value |  DESCRIZIONE |
   |----------|----------|-------|--------------|
   | Name | Yes | <*variable-name*> | Nome della variabile da incrementare | 
   | Value | No  | <*increment-value*> | Valore usato per incrementare la variabile Il valore predefinito è uno. <p><p>**Suggerimento**: anche se è facoltativo, impostare questo valore come procedura consigliata per poter conoscere sempre il valore specifico per incrementare la variabile. | 
   |||| 

   Ad esempio:  
   
   ![Esempio di un valore di incremento](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

Passando dalla finestra di progettazione all'editor di visualizzazione del codice, ecco come viene visualizzata l'azione **Incrementare una variabile** all'interno della definizione dell'app per la logica, nel formato JSON:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Esempio: Creare il contatore di cicli

Le variabili vengono comunemente usate per contare il numero di volte in cui viene eseguito un ciclo. Questo esempio mostra come creare e usare le variabili per questa attività creando un ciclo che conta gli allegati in un'email.

1. Nel portale di Azure creare un'app per la logica vuota. Aggiungere un trigger che verifica la presenza di nuovo messaggio di posta elettronica e degli eventuali allegati. 

   Questo esempio usa il trigger di Office 365 Outlook per **When a new email arrives** (Quando arriva un nuovo messaggio di posta elettronica). 
   È possibile impostare l'attivazione del trigger solo quando il messaggio di posta elettronica contiene degli allegati.
   Tuttavia è possibile usare un connettore qualsiasi per verificare la presenza di nuovi messaggi di posta elettronica con allegati, ad esempio il connettore Outlook.com.

2. Nel trigger scegliere **Show advanced options** (Mostra opzioni avanzate). Per fare in modo che il trigger controlli gli allegati e trasferisca tali allegati nel flusso di lavoro dell'app per la logica, selezionare **Yes** (Sì) per queste proprietà:
   
   * **Con allegato** 
   * **Includi allegati** 

   ![Cercare e includere allegati](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Aggiungere l'azione [**Initialize variable** ](#create-variable) (Inizializzare una variabile). Creare una variabile integer denominata **Count** (Conteggio) con un valore iniziale pari a zero.

   ![Aggiungere un'azione per "Initialize variable" (Inizializzare una variabile)](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Per scorrere ogni allegato, aggiungere un ciclo *for each* scegliendo **New step** > **More** > **Add a for each** (Nuovo passaggio > Altro > Aggiungi For each).

   ![Aggiungere un ciclo "for each"](./media/logic-apps-create-variables-store-values/add-loop.png)

5. Nel ciclo fare clic nella casella **Select an output from previous steps** (Selezionare un output dai passaggi precedenti). Quando viene visualizzato l'elenco di contenuti dinamici, selezionare **Attachments** (Allegati). 

   ![Selezionare "Allegati"](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Il campo **Attachments** (Allegati) passa una matrice contenente gli allegati di posta elettronica dall'output del trigger al ciclo.

6. Nel ciclo "for each" selezionare **Add an action** (Aggiungi un'azione). 

   ![Selezionare "Add an action" (Aggiungi un'azione)](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. Nella casella di ricerca immettere "increment variable" come filtro. Dall'elenco delle azioni selezionare **Variables - Increment variable** (Variabili - Incrementare una variabile).

   > [!NOTE]
   > Assicurarsi che l'azione **Increment variable** (Incrementare una variabile) sia visualizzata all'interno del ciclo. Se l'azione viene visualizzata all'esterno del ciclo, trascinare l'azione nel ciclo.

8. Nell'azione **Increment variable** (Incrementare una variabile) dall'elenco **Name** (Nome) selezionare la variabile **Count** (Conteggio). 

   ![Selezionare la variabile "Count" (Conteggio)](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. Sotto il ciclo aggiungere qualsiasi azione che invii il numero di allegati. Nell'azione includere il valore della variabile **Count** (Conteggio), ad esempio: 

   ![Aggiungere un'azione che invia i risultati](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Se l'app per la logica non è abilitata, nel menu dell'app per la logica selezionare **Overview** (Panoramica). Nella barra degli strumenti scegliere **Abilita**. 

2. Nella barra degli strumenti di Progettazione app per la logica selezionare **Esegui**. Questo passaggio avvia manualmente l'app per la logica.

3. Inviare un messaggio di posta elettronica con uno o più allegati all'account di posta elettronica usato in questo esempio.

   Questo passaggio attiva il trigger dell'app per la logica, che crea ed esegue un'istanza per il flusso di lavoro dell'app per la logica.
   Di conseguenza, l'app per la logica invia un messaggio o email che riporta il numero di allegati nell'email inviata.

Passando dalla finestra di progettazione all'editor di visualizzazione del codice, ecco come viene visualizzato il ciclo "for each" con l'azione **Incrementare una variabile** all'interno della definizione dell'app per la logica, nel formato JSON.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Decrementare una variabile

Per ridurre o *decrementare* una variabile di un valore costante, seguire gli stessi passaggi indicati per [aumentare una variabile](#increment-value) ma selezionare l'azione **Variables - Decrement variable** (Variabili - Decrementare una variabile). Questa operazione funziona solo con le variabili integer e float.

Ecco le proprietà per l'azione **Decrementare una variabile**:

| Proprietà | Obbligatoria | Value |  DESCRIZIONE |
|----------|----------|-------|--------------|
| Name | Yes | <*variable-name*> | Nome della variabile da decrementare | 
| Value | No  | <*increment-value*> | Valore per decrementare la variabile Il valore predefinito è uno. <p><p>**Suggerimento**: anche se è facoltativo, impostare questo valore come procedura consigliata per poter conoscere sempre il valore specifico per decrementare la variabile. | 
||||| 

Passando dalla finestra di progettazione all'editor di visualizzazione del codice, ecco come viene visualizzata l'azione **Decrementare una variabile** all'interno della definizione dell'app per la logica, nel formato JSON.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```


<a name="assign-value"></a>

## <a name="set-variable"></a>Impostare una variabile

Per assegnare un valore diverso a una variabile esistente, seguire gli passaggi indicati per [incrementare una variabile](#increment-value) eccetto per: 

1. Individuare e selezionare invece l'azione **Variables - Set variable** (Variabili - Impostare una variabile). 

2. Fornire il nome della variabile e il valore da assegnare. Sia il nuovo valore che la variabile devono avere lo stesso tipo di dati.
Il valore è obbligatorio perché questa azione non ha un valore predefinito. 

Ecco le proprietà per l'azione **Set variable** (Impostare una variabile):

| Proprietà | Obbligatoria | Value |  DESCRIZIONE | 
|----------|----------|-------|--------------| 
| Name | Yes | <*variable-name*> | Nome della variabile da modificare | 
| Value | Yes | <*new-value*> | Valore a cui si vuole assegnare la variabile. Entrambi devono avere lo stesso tipo di dati. | 
||||| 

> [!NOTE]
> A meno che non si stiano incrementando o decrementando le variabili, la modifica delle variabili all'interno dei cicli *potrebbe* creare risultati imprevisti poiché i cicli vengono eseguiti in parallelo, o contemporaneamente, per impostazione predefinita. In questi casi, provare a impostare il ciclo in modo che venga eseguito in sequenza. Ad esempio, quando si desidera fare riferimento al valore della variabile all'interno del ciclo e si prevede lo stesso valore all'inizio e alla fine dell'istanza del ciclo, attenersi alla procedura seguente per modificare la modalità di esecuzione del ciclo: 
>
> 1. Nell'angolo superiore destro del ciclo scegliere il pulsante con i puntini di sospensione (...), quindi scegliere **Impostazioni**.
> 
> 2. Sotto **Concurrency Control** (Controllo della concorrenza) modificare l'impostazione **Override Default** (Ignora impostazione predefinita) su **On**.
>
> 3. Trascinare il dispositivo di scorrimento **Degree of Parallelism** (Grado di parallelismo) su **1**.

Passando dalla finestra di progettazione all'editor di visualizzazione del codice, ecco come viene visualizzata l'azione **Impostazione di una variabile** all'interno della definizione dell'app per la logica, nel formato JSON. Questo esempio modifica il valore corrente della variabile "Count" in un altro valore. 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Accodare a una variabile

Per le variabili che memorizzano stringhe o matrici è possibile inserire o *accodare* il valore di una variabile come ultimo elemento di tali stringhe o matrici. È possibile seguire gli stessi passaggi indicati per [aumentare una variabile](#increment-value) ma: 

1. Individuare e selezionare una delle azioni seguenti a seconda che la variabile sia una stringa o una matrice: 

  * **Variables - Append to string variable** (Variabili - Accodare a una variabile stringa)
  * **Variables - Append to array variable** (Variabili - Accodare a una variabile matrice) 

2. Fornire il valore da accodare come ultimo elemento nella stringa o nella matrice. Questo valore è obbligatorio. 

Ecco le proprietà per le azioni **Append to...** (Accodare a...):

| Proprietà | Obbligatoria | Value |  DESCRIZIONE | 
|----------|----------|-------|--------------| 
| Name | Yes | <*variable-name*> | Nome della variabile da modificare | 
| Value | Yes | <*append-value*> | Valore da accodare, di qualsiasi tipo | 
|||||  

Passando dalla finestra di progettazione all'editor di visualizzazione del codice, ecco come viene visualizzata l'azione **Append to array variable** (Accodare a una variabile matrice) all'interno della definizione dell'app per la logica, nel formato JSON.
Questo esempio crea una variabile matrice e aggiunge un altro valore come ultimo elemento della matrice. Il risultato è una variabile aggiornata che contiene la matrice: `[1,2,3,"red"]` 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori di App per la logica](../connectors/apis-list.md)
