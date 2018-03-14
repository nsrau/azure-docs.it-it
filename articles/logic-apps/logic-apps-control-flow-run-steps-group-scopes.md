---
title: Eseguire i passaggi in base allo stato delle azioni raggruppate - App per la logica di Azure | Microsoft Docs
description: Raggruppare le azioni in ambiti ed eseguire i passaggi in base allo stato del gruppo
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
ms.openlocfilehash: 052af45962f442e96ca28f05ffaa1b9814b2588b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="scopes-run-steps-based-on-group-status-in-logic-apps"></a>Ambiti: eseguire i passaggi in base allo stato del gruppo nelle app per la logica

Per eseguire i passaggi solo dopo che un altro gruppo di azioni ha avuto esito positivo o negativo, inserire tale gruppo in un *ambito*. Questa struttura è utile quando si vuole organizzare le azioni come gruppo logico, valutare lo stato del gruppo ed eseguire le azioni in base allo stato dell'ambito. Al termine dell'esecuzione di tutte le azioni in un ambito, l'ambito ottiene anche il proprio stato. È ad esempio possibile usare gli ambiti quando si vuole implementare la [gestione degli errori e delle eccezioni](../logic-apps/logic-apps-exception-handling.md#scopes). 

Per controllare lo stato di un ambito, è possibile usare lo gli stessi criteri usati per determinare lo stato di esecuzione di un'app per la logica, ad esempio "Succeeded", "Failed", "Cancelled" e così via. Per impostazione predefinita, quando tutte le azioni dell'ambito hanno esito positivo lo stato dell'ambito viene contrassegnato come "Succeeded", ma, quando un'azione nell'ambito ha esito negativo o viene annullata, lo stato dell'ambito viene contrassegnato come "Failed". Per i limiti degli ambiti, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md). 

La seguente, ad esempio è un'app per la logica generale che usa un ambito per eseguire azioni specifiche e una condizione per controllare lo stato dell'ambito. Se un'azione nell'ambito ha esito negativo o termina in modo imprevisto, l'ambito viene rispettivamente contrassegnato come "Failed" o "Aborted" e l'app per la logica invia un messaggio "Scope failed". Se tutte le azioni nell'ambito hanno esito positivo, l'app per la logica invia un messaggio "Scope succeeded".

![Configurazione del trigger "Pianificazione - Ricorrenza"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>prerequisiti

Per eseguire l'esempio in questo articolo, sono necessari questi elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Un account di posta elettronica di un provider di posta elettronica supportato da App per la logica. In questo esempio viene usato Outlook.com. Se si usa un provider diverso, il flusso generale rimane invariato, ma l'interfaccia utente ha un aspetto diverso.

* Una chiave di Bing Maps. Per ottenere questa chiave, vedere <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Get a Bing Maps key</a> (Ottenere una chiave di Bing Maps).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Creare un'app per la logica di esempio

Creare prima di tutto questa app per la logica di esempio per poter aggiungere un ambito in un secondo momento:

![Creare un'app per la logica di esempio](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Un trigger **Pianificazione - Ricorrenza** che controlla il servizio Bing Maps in base all'intervallo specificato
* Un'azione **Bing Maps - Get route** (Bing Maps - Ottieni itinerario) che controlla il tempo di viaggio tra due località
* Un'istruzione condizionale che controlla se il tempo di viaggio supera il tempo di viaggio specificato
* Un'azione che invia un messaggio di posta elettronica per informare che il tempo di viaggio corrente supera il tempo specificato

È possibile salvare l'app per la logica in qualsiasi momento, quindi salvare spesso il lavoro.

1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>, se questa operazione non è già stata eseguita. Creare un'app per la logica vuota.

2. Aggiungere il trigger **Pianificazione - Ricorrenza** con queste impostazioni: **Intervallo** = "1" e **Frequenza** = "Minuto"

   ![Configurazione del trigger "Pianificazione - Ricorrenza"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Per semplificare la visualizzazione e nascondere i dettagli di ogni azione nella finestra di progettazione, comprimere la forma di ogni azione mentre si procede con i passaggi.

3. Aggiungere l'azione **Bing Maps - Get route** (Bing Maps - Ottieni itinerario). 

   1. Se non si ha già una connessione esistente a Bing Maps, viene chiesto di crearne una.

      | Impostazione | Valore | DESCRIZIONE |
      | ------- | ----- | ----------- |
      | **Connection Name** (Nome connessione) | BingMapsConnection | Specificare un nome per la connessione. | 
      | **Chiave API** | <*chiave-Bing-Maps*> | Immettere la chiave di Bing Maps ricevuta in precedenza. | 
      ||||  

   2. Configurare l'azione **Get route** (Ottieni itinerario), come illustrato nella tabella sotto questa immagine:

      ![Configurare l'azione "Bing Maps - Get route" (Bing Maps - Ottieni itinerario)](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Per altre informazioni su questi parametri, vedere [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calcolare un itinerario).

      | Impostazione | Valore | DESCRIZIONE |
      | ------- | ----- | ----------- |
      | **Punto di tragitto 1** | <*start*> | Immettere l'origine dell'itinerario. | 
      | **Punto di tragitto 2** | <*end*> | Immettere la destinazione dell'itinerario. | 
      | **Avoid** (Evita) | Nessuna | Immettere gli elementi da evitare lungo l'itinerario, ad esempio autostrade, strade a pedaggio e così via. Per i possibili valori, vedere [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calcolare un itinerario). | 
      | **Optimize** (Ottimizza) | timeWithTraffic | Selezionare un parametro per ottimizzare l'itinerario, ad esempio distanza, tempo con le informazioni sul traffico corrente e così via. Questo esempio usa il valore "timeWithTraffic" | 
      | **Unità distanza** | <*preferenza*> | Immettere l'unità di distanza per calcolare l'itinerario. Questo esempio usa il valore "Mile" | 
      | **Travel mode** (Modalità di viaggio) | Driving (Guida) | Immettere la modalità di viaggio per l'itinerario. Questo esempio usa il valore "Driving" | 
      | **Transit Date-Time** (Data e ora transito) | Nessuna | Si applica solo alla modalità di transito. | 
      | **Transit Date-Time Type** (Tipo di data e ora transito) | Nessuna | Si applica solo alla modalità di transito. | 
      ||||  

4. Aggiungere una condizione per verificare se il tempo di viaggio corrente con il traffico supera un tempo specificato. Per questo esempio, seguire i passaggi sotto questa immagine:

   ![Creare una condizione](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Rinominare la condizione con questa descrizione: **If traffic time more than specified time** (Se il tempo con il traffico è superiore al tempo specificato)

   2. Nell'elenco di parametri selezionare il campo **Travel Duration Traffic** (Durata viaggio con traffico), espresso in secondi. 

   3. Come operatore di confronto selezionare **è maggiore di**

   4. Come valore di confronto immettere **600**, espresso in secondi ed equivalente a 10 minuti.

5. Nel ramo **È true** della condizione aggiungere un'azione "invia messaggio di posta elettronica" per il provider di posta elettronica. Configurare questa azione con i dettagli indicati nella tabella sotto l'immagine:

   ![Aggiungere l'azione "Invia messaggio di posta elettronica" al ramo "È true"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Nel campo **A** immettere l'indirizzo di posta elettronica a scopo di test.

   2. Nel campo **Oggetto** immettere il testo seguente:

      ```Time to leave: Traffic more than 10 minutes```

   3. Nel campo **Corpo** immettere il testo seguente con uno spazio finale: 

      ```Travel time: ```

      Mentre il cursore è visualizzato nel campo **Corpo**, l'elenco di contenuto dinamico rimane aperto per consentire di selezionare i parametri disponibili.

   4. Nell'elenco di contenuto dinamico scegliere **Espressione**.

   5. Trovare e selezionare la funzione **div( )**.

   6. Mentre il cursore è tra le parentesi della funzione, scegliere **Contenuto dinamico** per poter aggiungere successivamente il parametro **Traffic Duration Traffic** (Durata viaggio con traffico).

   7. Sotto **Get route** (Ottieni itinerario) nell'elenco di parametri dinamici selezionare il campo **Traffic Duration Traffic** (Durata viaggio con traffico).

      ![Selezionare "Travel Duration Traffic" (Durata viaggio con traffico)](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. Dopo che il campo viene risolto nel formato JSON, aggiungere una **virgola** (```,```) seguita dal numero ```60``` per poter convertire il valore di **Traffic Duration Traffic** (Durata viaggio con traffico) da secondi a minuti. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      L'espressione dovrebbe avere ora un aspetto simile all'esempio seguente:

      ![Espressione finale](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. Al termine, fare clic su **OK**.

  10. Dopo la risoluzione dell'espressione, aggiungere il testo seguente con uno spazio iniziale: ``` minutes```
  
      Il campo **Corpo** dovrebbe avere ora un aspetto simile all'esempio seguente:

      ![Campo "Corpo" finale](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. Salvare l'app per la logica.

Aggiungere successivamente un ambito per poter raggruppare azioni specifiche e valutarne lo stato.

## <a name="add-a-scope"></a>Aggiungere un ambito

1. Se non è già stato fatto, aprire l'app per la logica in Progettazione app per la logica. 

2. Aggiungere un ambito al percorso del flusso di lavoro da usare. Ad esempio: 

   * Per aggiungere un ambito tra i passaggi esistenti nel flusso di lavoro dell'app per la logica, spostare il puntatore sulla freccia dove si vuole aggiungere l'ambito. 
   Scegliere il **segno più** (**+**) > **Aggiungi un ambito**.

     ![Aggiungere un ambito](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     Quando si vuole aggiungere un ambito alla fine del flusso di lavoro, nella parte inferiore dell'app per la logica scegliere **+ Nuovo passaggio** > **Altro** > **Aggiungi un ambito**.

3. Aggiungere ora i passaggi o trascinare i passaggi esistenti che si vuole eseguire nell'ambito. Per questo esempio, trascinare le azioni seguenti nell'ambito:
      
   * **Get route** (Ottieni itinerario)
   * **If traffic time more than specified time** (Se il tempo con il traffico è superiore al tempo specificato), che include entrambi i rami **true** e **false**

   L'app per la logica dovrebbe avere ora un aspetto simile all'esempio seguente:

   ![Ambito aggiunto](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. Sotto l'ambito aggiungere una condizione che controlla lo stato dell'ambito. Rinominare la condizione con questa descrizione: **If scope failed** (Se l'ambito ha avuto esito negativo)

   ![Aggiungere la condizione per controllare lo stato dell'ambito](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. Creare questa espressione che controlla se lo stato dell'ambito è uguale a `Failed` o `Aborted`.

   ![Aggiungere l'espressione che controlla lo stato dell'ambito](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   In alternativa, per immettere questa espressione come testo, scegliere **Modifica in modalità avanzata**.

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. Nei rami **È true** ed **È false** aggiungere le azioni che si vuole eseguire, ad esempio inviare posta elettronica o un messaggio.

   ![Aggiungere l'espressione che controlla lo stato dell'ambito](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. Salvare l'app per la logica.

L'app per la logica completata sarà ora simile a questo esempio con tutte le forme espanse:

![App per la logica completata con ambito](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testare il lavoro

Nella barra degli strumenti della finestra di progettazione scegliere **Esegui**. Se tutte le azioni nell'ambito hanno esito positivo, viene visualizzato un messaggio "Scope succeeded". Se nessuna azione nell'ambito hanno esito positivo, viene visualizzato un messaggio "Scope failed". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definizione JSON

Se si usa la visualizzazione Codice, è invece possibile definire una struttura di ambito nella definizione JSON dell'app per la logica. La seguente, ad esempio, è la definizione JSON per il trigger e le azioni dell'app per la logica precedente:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare suggerimenti relativi alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire i passaggi in base a una condizione (istruzioni condizionali)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Eseguire i passaggi in base a valori diversi (istruzioni switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Eseguire e ripetere i passaggi (cicli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Eseguire o unire passaggi paralleli (rami)](../logic-apps/logic-apps-control-flow-branches.md)