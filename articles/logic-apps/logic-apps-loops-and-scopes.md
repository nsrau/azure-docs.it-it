---
title: Creare cicli e ambiti o suddividere i dati nei flussi di lavoro - App per la logica di Azure | Documenti di Microsoft
description: "Creare cicli per eseguire iterazioni sui dati, raggruppare le azioni in ambiti o suddividere i dati per avviare più flussi di lavoro logica nelle app per la logica di Azure."
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: estfan; LADocs
ms.openlocfilehash: 24a19ef7d93b4b0d006dfb0aed38f88d3d821d99
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Cicli, ambiti e debatching delle app per la logica
  
Le app per la logica offrono una serie di metodi per usare matrici, raccolte, batch e cicli all'interno di un flusso di lavoro.
  
## <a name="foreach-loop-and-arrays"></a>Matrici e cicli ForEach
  
App per la logica consente di eseguire un ciclo su un set di dati e di eseguire un'azione per ogni elemento.  L'esecuzione di un ciclo su una raccolta è possibile tramite l'azione `foreach`.  Nella finestra di progettazione è possibile aggiungere un ciclo foreach.  Dopo aver selezionato la matrice su cui si vuole eseguire un'iterazione, è possibile iniziare ad aggiungere azioni.  È possibile aggiungere più azioni per ciclo foreach.  All'interno del ciclo è possibile iniziare a specificare cosa deve avvenire in corrispondenza di ogni valore della matrice.

  Questo esempio invia un messaggio di posta elettronica per ogni indirizzo di posta elettronica che contiene "microsoft.com". Se si usa la visualizzazione codice, è possibile specificare un ciclo foreach come nell'esempio seguente:

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
  
  Un'azione `foreach` può eseguire l'iterazione su matrici con migliaia di entità.  Per impostazione predefinita, le iterazioni vengono eseguite in parallelo.  Per informazioni dettagliate sui limiti di concorrenza e per le matrici, vedere [Limiti e configurazione](logic-apps-limits-and-config.md).

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
  
  È possibile eseguire un'azione o una serie di azioni finché non viene soddisfatta una condizione.  Lo scenario più comune per l'uso di un ciclo until consiste nel chiamare un endpoint fino a ottenere la risposta desiderata.  Nella finestra di progettazione è possibile specificare l'aggiunta di un loop Until.  Dopo avere aggiunto le azioni nel loop, è possibile impostare la condizione di uscita, nonché i limiti del loop.
  
  Questo esempio chiama un endpoint HTTP finché nel corpo della risposta non è presente il valore "Completed".  Questa chiamata viene completata in presenza di una di queste condizioni: 
  
  * Lo stato della risposta HTTP è "Completed"
  * Sono stati eseguiti tentativi per un'ora
  * Il ciclo è stato eseguito 100 volte
  
  Se si usa la visualizzazione codice, è possibile specificare un ciclo until come nell'esempio seguente:
  
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

In alcuni casi un trigger può ricevere una matrice di elementi che si preferisce separare per avviare un flusso di lavoro per ogni elemento.  Il debatching può essere eseguito con il comando `spliton`.  Per impostazione predefinita, se lo swagger del trigger specifica un payload che è una matrice, viene aggiunto un comando `spliton`. Il comando `spliton` avvia un'esecuzione per ogni elemento nella matrice.  SplitOn può essere aggiunto solo a un trigger che possa essere configurato o sostituito manualmente. Non è possibile usare `spliton` e implementare anche il modello di risposta sincrona.  Qualsiasi flusso di lavoro chiamato che include un'azione `response` oltre a `spliton` viene eseguito in modo asincrono e invia una risposta `202 Accepted` immediata.  

  Questo esempio riceve una matrice di elementi ed esegue il debatch su ogni riga. SplitOn può essere specificato nella visualizzazione codice, come nell'esempio seguente:

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

È possibile raggruppare una serie di azioni tra loro usando un ambito.  Gli ambiti sono utili per l'implementazione della gestione delle eccezioni.  Nella finestra di progettazione è possibile aggiungere un nuovo ambito e iniziare ad aggiungere tutte le azioni all'interno di esso.  È possibile definire ambiti nella visualizzazione codice, come nell'esempio seguente:


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
