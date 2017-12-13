---
title: Creare cicli e ambiti o suddividere i dati nei flussi di lavoro - App per la logica di Azure | Documenti di Microsoft
description: "Creare cicli per eseguire iterazioni sui dati, raggruppare le azioni in ambiti o suddividere i dati per avviare più flussi di lavoro logica nelle app per la logica di Azure."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: a17de187f67c075147ea8ff7f69434014eea3fdb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Cicli, ambiti e debatching delle app per la logica
  
Le app per la logica offrono una serie di metodi per usare matrici, raccolte, batch e cicli all'interno di un flusso di lavoro.
  
## <a name="foreach-loop-and-arrays"></a>Matrici e cicli ForEach
  
App per la logica consente di eseguire un ciclo su un set di dati e di eseguire un'azione per ogni elemento.  Per queste operazioni viene usata l'azione `foreach` .  Nella finestra di progettazione è possibile specificare l'aggiunta di un loop ForEach.  Dopo aver selezionato la matrice su cui si vuole eseguire un'iterazione, è possibile iniziare ad aggiungere azioni.  È possibile aggiungere più azioni per ciclo foreach.  All'interno del loop è possibile iniziare a specificare cosa deve avvenire in corrispondenza di ogni valore della matrice.

Se si usa la visualizzazione del codice, è possibile specificare un loop ForEach come nell'illustrazione che segue.  In questo esempio di loop ForEach viene inviato un messaggio di posta elettronica per ogni indirizzo di posta elettronica che contiene "microsoft.com":

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  Un'azione `foreach` è in grado di eseguire l'iterazione su matrici con fino a 5.000 righe.  Per impostazione predefinita, ogni iterazione verrà eseguita in parallelo alle altre.  

### <a name="sequential-foreach-loops"></a>Cicli ForEach sequenziali

Per abilitare l'esecuzione sequenziale di un ciclo ForEach è necessario aggiungere l'opzione di operazione `Sequential`.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Ciclo Until
  
  È possibile eseguire un'azione o una serie di azioni finché non viene soddisfatta una condizione.  Lo scenario più comune è quello in cui si chiama un endpoint fino a ottenere la risposta desiderata.  Nella finestra di progettazione è possibile specificare l'aggiunta di un loop Until.  Dopo avere aggiunto le azioni nel loop, è possibile impostare la condizione di uscita, nonché i limiti del loop.  Si verifica un ritardo di 1 minuto tra i cicli del loop.
  
  Se si usa la visualizzazione del codice, è possibile specificare un loop Until come nell'illustrazione che segue.  Questo è un esempio di chiamata a un endpoint HTTP finché nel corpo della risposta è presente il valore "Completed".  Ciò avviene in presenza di una di queste condizioni: 
  
  * Lo stato della risposta HTTP è "Completed"
  * Sono stati fatti tentativi per 1 ora
  * Il ciclo è stato eseguito 100 volte
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
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
  
## <a name="spliton-and-debatching"></a>SplitOn e scomposizione batch

In alcuni casi un trigger può ricevere una matrice di elementi che si preferisce separare per avviare un flusso di lavoro per ogni elemento.  Questa operazione può essere eseguita con il comando `spliton` .  Per impostazione predefinita, se lo swagger del trigger specifica un payload che è una matrice, viene aggiunto `spliton` e viene avviata un'esecuzione per ogni elemento.  SplitOn può essere aggiunto solo a un trigger.  L'operazione può essere configurata manualmente o sottoposta a override nella visualizzazione del codice di definizione.  Attualmente SplitOn è in grado di suddividere matrici con al massimo 5.000 elementi.  Non è possibile usare `spliton` e implementare anche il modello di risposta sincrona.  Qualsiasi flusso di lavoro chiamato che ha un'azione `response` oltre a `spliton` verrà eseguito in modo asincrono e invierà immediatamente una risposta `202 Accepted`.  

SplitOn può essere specificato nella visualizzazione del codice, come nell'esempio seguente.  In questo caso viene ricevuta una matrice di elementi e la scomposizione dei batch avviene su ogni riga.

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

## <a name="scopes"></a>Ambiti

È possibile raggruppare una serie di azioni tra loro usando un ambito.  Ciò è particolarmente utile per implementare la gestione delle eccezioni.  Nella finestra di progettazione è possibile aggiungere un nuovo ambito e iniziare ad aggiungere tutte le azioni all'interno di esso.  È possibile definire gli ambiti nella visualizzazione del codice come nell'esempio che segue:


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
