<properties
   pageTitle="Loop, ambiti e scomposizione batch delle app per la logica | Microsoft Azure"
   description="Concetti di loop, ambito e scomposizione batch delle app per la logica"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
  # Loop, ambiti e scomposizione batch delle app per la logica
  
  >[AZURE.NOTE] Questa versione dell'articolo si applica allo schema 2016-04-01-preview e versioni successive delle app per la logica. I concetti sono simili per gli schemi precedenti, ma gli ambiti sono disponibili solo per questo schema e le versioni successive.
  
  ## Loop e matrici ForEach
  
  App per la logica consente di eseguire un ciclo su un set di dati e di eseguire un'azione per ogni elemento. Per queste operazioni viene usata l'azione `foreach`. Nella finestra di progettazione è possibile specificare l'aggiunta di un loop ForEach. Dopo aver selezionato la matrice su cui si vuole eseguire un'iterazione, è possibile iniziare ad aggiungere azioni. Attualmente è possibile eseguire solo un'azione per ogni loop ForEach, ma questa limitazione verrà rimossa nelle prossime settimane. All'interno del loop è possibile iniziare a specificare cosa deve avvenire in corrispondenza di ogni valore della matrice.
  
  Se si usa la visualizzazione del codice, è possibile specificare un loop ForEach come nell'illustrazione che segue. In questo esempio di loop ForEach viene inviato un messaggio di posta elettronica per ogni indirizzo di posta elettronica che contiene "microsoft.com":
  
  ```
  {
      "forEach_email": {
          "type": "foreach",
          "foreach": "@triggerBody()['emails']",
          "expression": "@contains(item(), 'microsoft.com')",
          "actions": {
              "send_email": {
                  "type": "ApiConnection",
                  "inputs": {
                    "body": {
                        "to": "@item()",
                        "from": "me@contoso.com",
                        "message": "Hello, thank you for ordering"
                    }
                    "host": {
                        "connection": {
                            "id": "@parameters('$connections')['office365']['connection']['id']"
                        }
                    }
                  }
              }
          }
      }
  }
  ```
  
  Un'azione `foreach` è in grado di eseguire l'iterazione su matrici con fino a 5.000 righe. Ogni iterazione può essere eseguita in parallelo, quindi può essere necessario aggiungere messaggi a una coda se è richiesto il controllo di flusso.
  
  ## Loop Until
  
  È possibile eseguire un'azione o una serie di azioni finché non viene soddisfatta una condizione. Lo scenario più comune è quello in cui si chiama un endpoint fino a ottenere la risposta desiderata. Nella finestra di progettazione è possibile specificare l'aggiunta di un loop Until. Dopo avere aggiunto le azioni nel loop, è possibile impostare la condizione di uscita, nonché i limiti del loop. Si verifica un ritardo di 1 minuto tra i cicli del loop.
  
  Se si usa la visualizzazione del codice, è possibile specificare un loop Until come nell'illustrazione che segue. Questo è un esempio di chiamata a un endpoint HTTP finché nel corpo della risposta è presente il valore "Completed". Ciò avviene in presenza di una di queste condizioni:
  
  * Lo stato della risposta HTTP è "Completed"
  * Sono stati fatti tentativi per 1 ora
  * Il ciclo è stato eseguito 100 volte
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## SplitOn e scomposizione batch

In alcuni casi un trigger può ricevere una matrice di elementi che si preferisce separare per avviare un flusso di lavoro per ogni elemento. Questa operazione può essere eseguita con il comando `spliton`. Per impostazione predefinita, se lo swagger del trigger specifica un payload che è una matrice, viene aggiunto `spliton` e viene avviata un'esecuzione per ogni elemento. SplitOn può essere aggiunto solo a un trigger. L'operazione può essere configurata manualmente o sottoposta a override nella visualizzazione del codice di definizione. Attualmente SplitOn è in grado di suddividere matrici con al massimo 5.000 elementi. Non è possibile usare `spliton` e implementare anche il modello di risposta sincrona. Qualsiasi flusso di lavoro chiamato che ha un'azione `response` oltre a `spliton` verrà eseguito in modo asincrono e invierà immediatamente una risposta `202 Accepted`.

SplitOn può essere specificato nella visualizzazione del codice, come nell'esempio seguente. In questo caso viene ricevuta una matrice di elementi e la scomposizione dei batch avviene su ogni riga.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## Ambiti

È possibile raggruppare una serie di azioni tra loro usando un ambito. Ciò è particolarmente utile per implementare la gestione delle eccezioni. Nella finestra di progettazione è possibile aggiungere un nuovo ambito e iniziare ad aggiungere tutte le azioni all'interno di esso. È possibile definire gli ambiti nella visualizzazione del codice come nell'esempio che segue:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

<!---HONumber=AcomDC_0525_2016-->