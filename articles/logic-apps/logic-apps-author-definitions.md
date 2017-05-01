---
title: 'Definire flussi di lavoro con JSON: App per la logica di Azure | Microsoft Docs'
description: Come scrivere definizioni flusso di lavoro in JSON per app per la logica
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: e94837bf79e42602e2f72cda747ea629eed45a20
ms.openlocfilehash: 920940d8ebe23d24216d3e886bd8ae58be12ce34
ms.lasthandoff: 03/01/2017


---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>Creare definizioni dei flussi di lavoro per le app per la logica usando JSON

È possibile creare definizioni flusso di lavoro per [App per la logica di Azure](logic-apps-what-are-logic-apps.md) con il semplice linguaggio dichiarativo JSON. Se non è già stato fatto, vedere prima [come creare la prima app per la logica con Progettazione app per la logica](logic-apps-create-a-logic-app.md). Vedere anche le [informazioni di riferimento complete per il linguaggio di definizione del flusso di lavoro](http://aka.ms/logicappsdocs).

## <a name="repeat-steps-over-a-list"></a>Ripetere i passaggi in un elenco

Per eseguire l'iterazione in una matrice con più di 10.000 elementi ed eseguire un'azione per ogni elemento, usare il [tipo foreach](logic-apps-loops-and-scopes.md).

## <a name="handle-failures-if-something-goes-wrong"></a>Gestire gli errori in caso di problemi

In genere, è opportuno includere una *procedura di correzione*, ovvero la logica che viene eseguita *se e solo se* una o più chiamate hanno esito negativo. Questo esempio recupera i dati da diverse posizioni, ma, se la chiamata ha esito negativo, è opportuno INSERIRE un messaggio in un punto qualsiasi, per poter ricostruire l'errore in un secondo momento:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

Per specificare che `postToErrorMessageQueue` viene eseguito solo quando `readData` è `Failed`, usare la proprietà `runAfter`, ad esempio, per specificare un elenco di possibili valori, in modo che `runAfter` possa essere `["Succeeded", "Failed"]`.

Infine, poiché questo esempio ora gestisce l'errore, l'esecuzione non viene più contrassegnata come `Failed`. Poiché è stato aggiunto il passaggio per gestire questo errore nell'esempio, l'esecuzione è `Succeeded` anche se un passaggio è `Failed`.

## <a name="execute-two-or-more-steps-in-parallel"></a>Eseguire due o più passaggi in parallelo

Per eseguire più azioni in parallelo, la proprietà `runAfter` deve essere equivalente in fase di esecuzione. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

In questo esempio sia `branch1` che `branch2` vengono impostati per l'esecuzione dopo `readData`. Di conseguenza, entrambi i rami vengono eseguiti in parallelo. Il timestamp è identico per entrambi i rami.

![Parallelo](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>Creare un join di due rami paralleli

È possibile creare un join di due azioni impostate per l'esecuzione in parallelo aggiungendo elementi alla proprietà `runAfter` come nell'esempio precedente.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![Parallelo](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>Eseguire il mapping di elementi elenco a una configurazione diversa

A questo punto, si vuole ottenere un contenuto diverso in base al valore di una proprietà. È possibile creare una mappa di valori alle relative destinazioni come parametro.  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

In questo caso, si ottiene prima un elenco di articoli. In base alla categoria definita come parametro, il secondo passaggio usa una mappa per cercare l'URL e ottenere così il contenuto.

Tenere presente quanto segue: 

*    La funzione [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) controlla se la categoria corrisponde a una delle categorie note definite.

*    Dopo avere ottenuto la categoria, è possibile effettuare il pull dell'elemento dalla mappa usando parentesi quadre: `parameters[...]`.

## <a name="process-strings"></a>Elaborare le stringhe

È possibile usare svariate funzioni per modificare le stringhe. Si supponga, ad esempio, di avere una stringa che si vuole passare a un sistema, ma non si è certi di quale sia la gestione appropriata per la codifica dei caratteri. Per questa stringa è possibile usare, ad esempio, la codifica Base64. Tuttavia, per evitare di eseguire l'escape in un URL, verranno sostituiti alcuni caratteri. 

Si vuole anche ottenere una sottostringa del nome dell'ordine, perché i primi cinque caratteri non vengono usati.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Passaggio dall'interno all'esterno:

1. Ottenere l'elemento [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) per il nome dell'autore dell'ordine per poter tornare al numero totale di caratteri.

2. Sottrarre 5 perché si vuole ottenere una stringa più breve.

3. Accettare di fatto l'elemento [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Inizia dall'indice `5` e procede alla parte rimanente della stringa.

4. Convertire la sottostringa in una stringa [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) sostituisce tutti i caratteri `+` con caratteri `-`.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) sostituisce tutti i caratteri `/` con caratteri `_`.

## <a name="work-with-date-times"></a>Uso di date e ore

Date e ore possono risultare utili, specialmente quando si prova a effettuare il pull dei dati da un'origine dati che non supporta *trigger* in modo naturale. È anche possibile usare date e ore per trovare la durata di diversi passaggi.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

In questo esempio viene estratto l'elemento `startTime` dal passaggio precedente. Si ottiene quindi l'ora corrente e si sottrae un secondo:

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

È possibile usare altre unità di tempo, ad esempio `minutes` o `hours`. Infine si potranno confrontare questi due valori. Se il primo valore è minore del secondo valore, è trascorso più di un secondo dal momento in cui è stato inserito l'ordine.

Per formattare le date, è possibile usare formattatori di stringa. Per ottenere la specifica RFC1123, ad esempio, si usa [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). Per informazioni sulla formattazione delle date, vedere [Workflow Definition Language](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) (Linguaggio di definizione del flusso di lavoro).

## <a name="deployment-parameters-for-different-environments"></a>Parametri di distribuzione per diversi ambienti

In genere, i cicli di vita di distribuzione prevedono un ambiente di sviluppo, un ambiente di staging e un ambiente di produzione. È possibile, ad esempio, usare la stessa definizione in tutti questi ambienti, ma usare database diversi. In modo analogo, è possibile usare la stessa definizione in aree diverse ai fini della disponibilità elevata, ma fare in modo che ogni istanza dell'app per la logica comunichi con il database di quell'area.
Questo scenario è diverso da quello in cui si accettano i parametri in *fase di esecuzione*, dove invece è consigliabile usare la funzione `trigger()` come nell'esempio precedente.

È possibile iniziare con una definizione di base, come questo esempio:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

Nella richiesta `PUT` effettiva per le app per la logica è possibile fornire il parametro `uri`. Poiché non esiste più un valore predefinito, il payload delle app per la logica richiede questo parametro:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

In ogni ambiente è possibile fornire un valore diverso per il parametro `connection`. 

Per tutte le opzioni disponibili per la creazione e la gestione di app per la logica, vedere [documentazione dell'API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx). 

