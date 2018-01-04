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
ms.openlocfilehash: 9cdbe4a12a0b16341a1e52f176901045baf327b5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Cicli, ambiti e debatching delle app per la logica
  
Le app per la logica offrono una serie di metodi per usare matrici, raccolte, batch e cicli all'interno di un flusso di lavoro.
  
## <a name="foreach-loop-and-arrays"></a>Matrici e cicli ForEach
  
App per la logica consente di eseguire un ciclo su un set di dati e di eseguire un'azione per ogni elemento.  Ciclo su una raccolta è possibile tramite il `foreach` azione.  Nella finestra di progettazione, è possibile aggiungere un ciclo for each.  Dopo aver selezionato la matrice su cui si vuole eseguire un'iterazione, è possibile iniziare ad aggiungere azioni.  È possibile aggiungere più azioni per ciclo foreach.  Una volta all'interno del ciclo, è possibile iniziare a specificare cosa dovrebbe accadere in ogni valore della matrice.

  Questo esempio viene inviato un messaggio di posta elettronica per ogni indirizzo di posta elettronica che contiene 'microsoft.com'. In visualizzazione codice, è possibile specificare un ciclo for each analogo al seguente:

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
  
  Oggetto `foreach` azione possibile scorrere le matrici con migliaia di entità.  Per impostazione predefinita, le iterazioni eseguite in parallelo.  Vedere [limiti e configurazione](logic-apps-limits-and-config.md) per ulteriori informazioni sui limiti della matrice e la concorrenza.

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
  
  È possibile eseguire un'azione o una serie di azioni finché non viene soddisfatta una condizione.  Lo scenario più comune per l'utilizzo di un fino a quando non è chiamata di un endpoint ciclo fino a ottenere la risposta desiderata.  Nella finestra di progettazione è possibile specificare l'aggiunta di un loop Until.  Dopo avere aggiunto le azioni nel loop, è possibile impostare la condizione di uscita, nonché i limiti del loop.
  
  Questo esempio viene chiamato un endpoint HTTP finché il corpo della risposta è il valore 'Completed'.  Viene completato quando entrambi: 
  
  * Lo stato della risposta HTTP è "Completed"
  * È tentato per un'ora
  * Il ciclo è stato eseguito 100 volte
  
  In visualizzazione codice, è possibile specificare un fino al ciclo analogo al seguente:
  
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

In alcuni casi un trigger può ricevere una matrice di elementi che si preferisce separare per avviare un flusso di lavoro per ogni elemento.  La scomposizione dei batch può essere eseguita tramite il `spliton` comando.  Per impostazione predefinita, se il swagger trigger consente di specificare un payload che è una matrice, un `spliton` viene aggiunto. Il `spliton` comando avvia un'esecuzione per ogni elemento nella matrice.  SplitOn possono essere aggiunti solo a un trigger che può essere configurato manualmente o sottoposto a override. Non è possibile usare `spliton` e implementare anche il modello di risposta sincrona.  Qualsiasi flusso di lavoro denominato che ha un `response` azione oltre a `spliton` viene eseguito in modo asincrono e invia immediatamente `202 Accepted` risposta.  

  In questo esempio riceve una matrice di elementi e debatches in ogni riga. SplitOn può essere specificato nella visualizzazione codice come nell'esempio seguente:

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

È possibile raggruppare una serie di azioni tra loro usando un ambito.  Gli ambiti sono utili per l'implementazione di gestione delle eccezioni.  Nella finestra di progettazione è possibile aggiungere un nuovo ambito e iniziare ad aggiungere tutte le azioni all'interno di esso.  È possibile definire gli ambiti nella visualizzazione di codice simile al seguente:


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
