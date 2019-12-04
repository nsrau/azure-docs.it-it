---
title: Creare e gestire le variabili per archiviare i valori
description: Come archiviare e gestire i valori usando le variabili in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 9b3ba7ff20e581988c3e862cff3bbf6d5ee96bf4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793164"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Archiviare e gestire i valori usando le variabili in app per la logica di Azure

Questo articolo illustra come creare e usare le variabili usate per archiviare i valori nell'app per la logica. Le variabili, ad esempio, consentono di tenere traccia del numero di esecuzioni di un ciclo. Per eseguire l'iterazione di una matrice o controllare una matrice per un elemento specifico, è possibile usare una variabile per fare riferimento al numero di indice per ogni elemento della matrice.

È possibile creare variabili per tipi di dati quali integer, float, booleano, stringa, matrice e oggetto. Dopo aver creato una variabile è possibile eseguire altre attività, ad esempio:

* Ottenere o fare riferimento al valore della variabile.
* Aumentare o diminuire la variabile di un valore costante, noto anche come *incremento* e *decremento*.
* Assegnare un valore diverso alla variabile.
* Inserire o *aggiungere* il valore della variabile come ultima volta in una stringa o matrice.

Le variabili esistono e sono globali solo all'interno dell'istanza dell'app per la logica che le crea. Inoltre persistono in tutte le iterazioni di ciclo all'interno di un'istanza dell'app per la logica. Quando si fa riferimento a una variabile, usare il nome della variabile come token, non il nome dell'azione, che rappresenta il modo consueto per fare riferimento agli output di un'azione.

> [!IMPORTANT]
> Per impostazione predefinita, i cicli in un ciclo "for each" vengono eseguiti in parallelo. Quando si utilizzano variabili nei cicli, eseguire il ciclo in [sequenza](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) in modo che le variabili restituiscano risultati prevedibili.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, [iscriversi per ottenere un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica in cui si vuole creare la variabile

  Se non si ha familiarità con le app per la logica, vedere informazioni sulle [app per la](../logic-apps/logic-apps-overview.md) logica di Azure e [Guida introduttiva: creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) come primo passo nell'app per la logica

  Prima di poter aggiungere azioni per creare e gestire le variabili, l'app per la logica deve iniziare con un trigger.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inizializzare una variabile

È possibile creare una variabile e dichiararne il tipo e il valore iniziale, il tutto in un'unica azione nell'app per la logica. È possibile dichiarare solo variabili a livello globale, non all'interno di ambiti, condizioni e cicli.

1. Nell' [portale di Azure](https://portal.azure.com) o in Visual Studio aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

   In questo esempio si usa il portale di Azure e un'app per la logica con un trigger esistente.

1. Nell'app per la logica, sotto il punto in cui si desidera aggiungere una variabile, seguire uno di questi passaggi: 

   * Per aggiungere un'azione nel passaggio precedente, selezionare **nuovo passaggio**.

     ![Aggiungere un'azione](./media/logic-apps-create-variables-store-values/add-action.png)

   * Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia di connessione in modo che venga visualizzato il segno di addizione ( **+** ). Selezionare il segno più e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca di **Scegliere un'azione** immettere `variables` come filtro. Nell'elenco azioni selezionare **Inizializza variabile**.

   ![Seleziona azione](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Fornire queste informazioni sulla variabile come descritto di seguito:

   | Proprietà | Obbligatoria | Value |  Description |
   |----------|----------|-------|--------------|
   | **Nome** | SÌ | <*variable-name*> | Nome della variabile da incrementare |
   | **Tipo** | SÌ | <*variable-type*> | Tipo di dati per la variabile |
   | **Valore** | No | <*start-value*> | Valore iniziale della variabile <p><p>**Suggerimento**: anche se è facoltativo, impostare questo valore come procedura consigliata in modo da conoscere sempre il valore iniziale della variabile. |
   |||||

   ad esempio:

   ![Inizializzare una variabile](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Continuare quindi ad aggiungere le azioni desiderate. Al termine, fare clic su **Salva**nella barra degli strumenti della finestra di progettazione.

Se si passa dalla finestra di progettazione all'editor della visualizzazione codice, ecco il modo in cui viene visualizzata l'azione **Inizializza variabile** nella definizione dell'app per la logica, che è nel formato JavaScript Object Notation (JSON):

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

> [!NOTE]
> Sebbene l'azione **Inizializza variabile** includa una sezione di `variables` strutturata come matrice, l'azione può creare una sola variabile alla volta. Ogni nuova variabile richiede una singola azione di **inizializzazione della variabile** .

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

Per recuperare o fare riferimento al contenuto di una variabile è anche possibile usare la [funzione variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) in Logic App Designer (Progettazione app per la logica) e l'editor di visualizzazione del codice. Per fare riferimento a una variabile, usare il nome della variabile come token, non il nome dell'azione, che è il modo usuale per fare riferimento agli output di un'azione.

Questa espressione, ad esempio, ottiene gli elementi dalla variabile di matrice [creata in precedenza in questo articolo](#append-value) usando la funzione `variables()`. La funzione `string()` restituisce il contenuto della variabile in formato stringa: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Incrementare una variabile 

Per aumentare o *incrementare* una variabile con un valore costante, aggiungere l'azione **incrementa variabile** all'app per la logica. Questa operazione funziona solo con le variabili integer e float.

1. Nella finestra di progettazione dell'app per la logica, sotto il passaggio in cui si vuole aumentare una variabile esistente, selezionare **nuovo passaggio**. 

   Ad esempio, questa app per la logica ha già un trigger e un'azione che ha creato una variabile. In tal caso, aggiungere una nuova azione con questi passaggi:

   ![Aggiungere un'azione](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). Selezionare il segno più e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "increment variable" come filtro. Nell'elenco azioni selezionare **incrementa variabile**.

   ![Selezionare l'azione "Incrementare una variabile"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Fornire queste informazioni per incrementare la variabile:

   | Proprietà | Obbligatoria | Value |  Description |
   |----------|----------|-------|--------------|
   | **Nome** | SÌ | <*variable-name*> | Nome della variabile da incrementare |
   | **Valore** | No | <*increment-value*> | Valore usato per incrementare la variabile Il valore predefinito è uno. <p><p>**Suggerimento**: anche se è facoltativo, impostare questo valore come procedura consigliata in modo da conoscere sempre il valore specifico per incrementare la variabile. |
   ||||

   ad esempio:

   ![Esempio di un valore di incremento](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Al termine, fare clic su **Salva**nella barra degli strumenti della finestra di progettazione.

Se si passa dalla finestra di progettazione all'editor della visualizzazione codice, ecco il modo in cui viene visualizzata l'azione **incrementa variabile** all'interno della definizione dell'app per la logica, in formato JSON:

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

## <a name="example-create-loop-counter"></a>Esempio: Creazione del contatore di cicli

Le variabili vengono comunemente usate per contare il numero di volte in cui viene eseguito un ciclo. Questo esempio mostra come creare e usare le variabili per questa attività creando un ciclo che conta gli allegati in un'email.

1. Nel portale di Azure creare un'app per la logica vuota. Aggiungere un trigger che verifica la presenza di nuovo messaggio di posta elettronica e degli eventuali allegati.

   Questo esempio usa il trigger di Office 365 Outlook per **When a new email arrives** (Quando arriva un nuovo messaggio di posta elettronica). È possibile impostare l'attivazione del trigger solo quando il messaggio di posta elettronica contiene degli allegati. Tuttavia è possibile usare un connettore qualsiasi per verificare la presenza di nuovi messaggi di posta elettronica con allegati, ad esempio il connettore Outlook.com.

1. Nel trigger, per verificare la presenza di allegati e passare tali allegati al flusso di lavoro dell'app per la logica, selezionare **Sì** per queste proprietà:

   * **Con allegato**
   * **Includi allegati**

   ![Cercare e includere allegati](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Aggiungere l'azione [**Initialize variable** ](#create-variable) (Inizializzare una variabile). Creare una variabile di tipo integer denominata `Count` con un valore iniziale zero.

   ![Aggiungere un'azione per "Initialize variable" (Inizializzare una variabile)](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Per scorrere ogni allegato, aggiungere un ciclo *for each* .

   1. Nell'azione **Inizializza variabile** selezionare **nuovo passaggio**.

   1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `for each` come filtro di ricerca e selezionare **per ciascuna**.

      ![Aggiungere un ciclo "for each"](./media/logic-apps-create-variables-store-values/add-loop.png)

1. Nel ciclo fare clic nella casella **Select an output from previous steps** (Selezionare un output dai passaggi precedenti). Quando viene visualizzato l'elenco di contenuti dinamici, selezionare **Attachments** (Allegati).

   ![Selezionare "Allegati"](./media/logic-apps-create-variables-store-values/select-attachments.png)

   La proprietà **Attachments** passa una matrice, che contiene gli allegati di posta elettronica dall'output del trigger, al ciclo.

1. Nel ciclo **for each** selezionare **Aggiungi un'azione**.

   ![Selezionare "Add an action" (Aggiungi un'azione)](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. Nella casella di ricerca immettere "increment variable" come filtro. Nell'elenco azioni selezionare **incrementa variabile**.

   > [!NOTE]
   > Assicurarsi che l'azione di **incremento della variabile** venga visualizzata all'interno del ciclo. Se l'azione viene visualizzata all'esterno del ciclo, trascinare l'azione nel ciclo.

1. Nell'azione **Increment variable** (Incrementare una variabile) dall'elenco **Name** (Nome) selezionare la variabile **Count** (Conteggio).

   ![Selezionare la variabile "Count" (Conteggio)](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. Sotto il ciclo aggiungere qualsiasi azione che invii il numero di allegati. Nell'azione includere il valore della variabile **Count** (Conteggio), ad esempio:

   ![Aggiungere un'azione che invia i risultati](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Se l'app per la logica non è abilitata, scegliere **Panoramica**dal menu dell'app per la logica. Sulla barra degli strumenti selezionare **Abilita**.

1. Nella barra degli strumenti di progettazione app per la logica selezionare **Esegui**. Questo passaggio avvia manualmente l'app per la logica.

1. Inviare un messaggio di posta elettronica con uno o più allegati all'account di posta elettronica usato in questo esempio.

   Questo passaggio attiva il trigger dell'app per la logica, che crea ed esegue un'istanza per il flusso di lavoro dell'app per la logica. Di conseguenza, l'app per la logica invia un messaggio o email che riporta il numero di allegati nell'email inviata.

Se si passa dalla finestra di progettazione all'editor della visualizzazione codice, di seguito viene illustrato il modo in cui viene visualizzato il ciclo **for each** insieme all'azione di **incremento della variabile** all'interno della definizione dell'app per la logica, che è in formato JSON.

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

Per ridurre o *decrementare* una variabile in base a un valore costante, attenersi alla procedura per [aumentare una variabile](#increment-value) , ad eccezione del fatto che è possibile trovare e selezionare l'azione di **decremento della variabile** . Questa operazione funziona solo con le variabili integer e float.

Ecco le proprietà per l'azione **Decrementare una variabile**:

| Proprietà | Obbligatoria | Value |  Description |
|----------|----------|-------|--------------|
| **Nome** | SÌ | <*variable-name*> | Nome della variabile da decrementare | 
| **Valore** | No | <*increment-value*> | Valore per decrementare la variabile Il valore predefinito è uno. <p><p>**Suggerimento**: anche se è facoltativo, impostare questo valore come procedura consigliata in modo da conoscere sempre il valore specifico per decrementare la variabile. |
||||| 

Se si passa dalla finestra di progettazione all'editor della visualizzazione codice, di seguito viene illustrato il modo in cui l'azione della **variabile di decremento** viene visualizzata all'interno della definizione dell'app per la logica, in formato JSON.

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

1. Individuare e selezionare invece l'azione **Imposta variabile** .

1. Fornire il nome della variabile e il valore da assegnare. Sia il nuovo valore che la variabile devono avere lo stesso tipo di dati. Il valore è obbligatorio perché questa azione non ha un valore predefinito.

Ecco le proprietà per l'azione **Set variable** (Impostare una variabile):

| Proprietà | Obbligatoria | Value |  Description |
|----------|----------|-------|--------------|
| **Nome** | SÌ | <*variable-name*> | Nome della variabile da modificare |
| **Valore** | SÌ | <*new-value*> | Valore a cui si vuole assegnare la variabile. Entrambi devono avere lo stesso tipo di dati. |
||||| 

> [!NOTE]
> A meno che non si stiano incrementando o decrementando le variabili, la modifica delle variabili all'interno dei cicli *potrebbe* creare risultati imprevisti poiché i cicli vengono eseguiti in parallelo, o contemporaneamente, per impostazione predefinita. In questi casi, provare a impostare il ciclo in modo che venga eseguito in sequenza. Ad esempio, quando si desidera fare riferimento al valore della variabile all'interno del ciclo e si prevede lo stesso valore all'inizio e alla fine dell'istanza del ciclo, attenersi alla procedura seguente per modificare la modalità di esecuzione del ciclo: 
>
> 1. Nell'angolo in alto a destra del ciclo selezionare il pulsante con i puntini di sospensione ( **...** ) e quindi selezionare **Impostazioni**.
> 
> 2. Sotto **Concurrency Control** (Controllo della concorrenza) modificare l'impostazione **Override Default** (Ignora impostazione predefinita) su **On**.
>
> 3. Trascinare il dispositivo di scorrimento **Degree of Parallelism** (Grado di parallelismo) su **1**.

Se si passa dalla finestra di progettazione all'editor della visualizzazione codice, ecco il modo in cui viene visualizzata l'azione **Imposta variabile** all'interno della definizione dell'app per la logica, in formato JSON. Questo esempio Mostra come modificare il valore corrente della variabile `Count` in un altro valore.

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

   * **Accoda a variabile di stringa**
   * **Accoda a variabile di matrice** 

1. Fornire il valore da accodare come ultimo elemento nella stringa o nella matrice. Questo valore è obbligatorio.

Ecco le proprietà per le azioni **Append to...** (Accodare a...):

| Proprietà | Obbligatoria | Value |  Description |
|----------|----------|-------|--------------|
| **Nome** | SÌ | <*variable-name*> | Nome della variabile da modificare |
| **Valore** | SÌ | <*append-value*> | Valore da accodare, di qualsiasi tipo |
|||||

Se si passa dalla finestra di progettazione all'editor della visualizzazione codice, di seguito è illustrato il modo in cui viene visualizzata l'azione **Accoda a variabile di matrice** all'interno della definizione dell'app per la logica, in formato JSON. Questo esempio crea una variabile matrice e aggiunge un altro valore come ultimo elemento della matrice. Il risultato è una variabile aggiornata che contiene la matrice: `[1,2,3,"red"]`

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

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori di App per la logica](../connectors/apis-list.md)
