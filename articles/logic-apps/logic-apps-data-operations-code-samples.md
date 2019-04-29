---
title: Esempi di operazioni sui dati - App per la logica di Azure | Microsoft Docs
description: Esempi di codice per le definizioni di azioni di operazioni sui dati in App per la logica di Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: v-yiso
manager: jeconnoc
ms.topic: reference
origin.date: 07/25/2018
ms.date: 12/10/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 8de70e8df50b79f27c19c74307d9321ff8cd1eb5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128550"
---
# <a name="data-operation-code-samples-for-azure-logic-apps"></a>Esempi di codice per operazioni sui dati per App per la logica di Azure

Di seguito sono riportati gli esempi di codice per le definizioni delle azioni di operazioni sui dati illustrate nell'articolo [Eseguire operazioni sui dati](../logic-apps/logic-apps-perform-data-operations.md). È possibile usare questi codici quando si vuole provare gli esempi forniti con le connessioni API, la sottoscrizione di Azure e la definizione del flusso di lavoro sottostante della propria app per la logica. È sufficiente copiare e incollare queste definizioni di azioni nell'editor della visualizzazione Codice per la definizione del flusso di lavoro dell'app per la logica e quindi modificare le definizioni per il flusso di lavoro specifico. 

In base agli standard JSON (JavaScript Object Notation), queste definizioni di azioni vengono visualizzate in ordine alfabetico. In Progettazione App per la logica, tuttavia, ogni definizione viene visualizzata nella sequenza corretta all'interno del flusso di lavoro, poiché ogni proprietà `runAfter` della definizione di azione specifica l'ordine di esecuzione. 

<a name="compose-action-example"></a>

## <a name="compose"></a>Composizione

Per provare l'[esempio dell'azione **Componi**](../logic-apps/logic-apps-perform-data-operations.md#compose-action), è possibile usare le definizioni di azioni seguenti:

```json
"actions": {
  "Compose": {
    "type": "Compose",
    "inputs": {
      "age": "@variables('ageVar')",
      "fullName": "@{variables('lastNameVar')}, @{variables('firstNameVar')}"
    },
    "runAfter": {
      "Initialize_variable_-_ageVar": [
          "Succeeded"
      ]
    }
  },
  "Initialize_variable_-_ageVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "ageVar",
          "type": "Integer",
          "value": 35
        }
      ]
    },
    "runAfter": {
      "Initialize_variable_-_lastNameVar": [
        "Succeeded"
      ]
    }
  },
  "Initialize_variable_-_firstNameVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "firstNameVar",
          "type": "String",
          "value": "Sophie "
        }
      ]
    },
    "runAfter": {}
  },
  "Initialize_variable_-_lastNameVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "lastNameVar",
          "type": "String",
          "value": "Owen"
        }
      ]
    },
    "runAfter": {
      "Initialize_variable_-_firstNameVar": [
        "Succeeded"
      ]
    }
  }
},
```

<a name="create-csv-table-action-example"></a>

## <a name="create-csv-table"></a>Crea tabella CSV

Per provare l'[esempio dell'azione **Crea tabella CSV**](../logic-apps/logic-apps-perform-data-operations.md#create-csv-table-action), è possibile usare le definizioni di azioni seguenti:

```json
"actions": {
   "Create_CSV_table": {
      "type": "Table",     
      "inputs": {
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      },
      "runAfter": {
         "Initialize_variable_-_JSON_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_JSON_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myJSONArray",
               "type": "Array",
                  "value": [
                     {
                        "Description": "Apples",
                        "Product_ID": 1
                     },
                     {
                        "Description": "Oranges",
                        "Product_ID": 2
                     }
                  ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="create-html-table-action-example"></a>

## <a name="create-html-table"></a>Crea tabella HTML

Per provare l'[esempio dell'azione **Crea tabella HTML**](../logic-apps/logic-apps-perform-data-operations.md#create-html-table-action), è possibile usare le definizioni di azioni seguenti:

```json
"actions": {
   "Create_HTML_table": {
      "type": "Table",     
      "inputs": {
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      },
      "runAfter": {
         "Initialize_variable_-_JSON_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_JSON_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myJSONArray",
               "type": "Array",
                  "value": [
                     {
                        "Description": "Apples",
                        "Product_ID": 1
                     },
                     {
                        "Description": "Oranges",
                        "Product_ID": 2
                     }
                  ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="filter-array-action-example"></a>

## <a name="filter-array"></a>Filtra matrice

Per provare l'[esempio dell'azione **Filtra matrice**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action), è possibile usare le definizioni di azioni seguenti:

```json
"actions": {
   "Filter_array": {
      "type": "Query",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "where": "@greater(item(), 1)"
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="join-action-example"></a>

## <a name="join"></a>Join

Per provare l'[esempio dell'azione **Aggiungi**](../logic-apps/logic-apps-perform-data-operations.md#join-action), è possibile usare le definizioni di azioni seguenti:

```json
"actions": {
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   },
   "Join": {
      "type": "Join",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "joinWith": ":"
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
             "Succeeded"
         ]
      }
   }
},
```

<a name="parse-json-action-example"></a>

## <a name="parse-json"></a>Analizzare JSON

Per provare l'[esempio dell'azione **Analizza JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action), è possibile usare le definizioni di azioni seguenti:

```json
"actions": {
   "Initialize_variable_-_JSON_object": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [
            {
               "name": "myJSONObject",
               "type": "Object",
               "value": {
                  "Member": {
                     "Email": "Sophie.Owen@contoso.com",
                     "FirstName": "Sophie",
                     "LastName": "Owen"
                  }
               }
            }
         ]
      },
      "runAfter": {}
   },
   "Parse_JSON": {
      "type": "ParseJson",
      "inputs": {
         "content": "@variables('myJSONObject')",
         "schema": {
            "type": "object",
            "properties": {
               "Member": {
                  "type": "object",
                  "properties": {
                     "Email": {
                        "type": "string"
                     },
                     "FirstName": {
                        "type": "string"
                     },
                     "LastName": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      },
      "runAfter": {
         "Initialize_variable_-_JSON_object": [
            "Succeeded"
         ]
      }
},
```

<a name="select-action-example"></a>

## <a name="select"></a>Select

Per provare l'[esempio dell'azione **Seleziona**](../logic-apps/logic-apps-perform-data-operations.md#select-action), è possibile usare le definizioni di azioni seguenti:

```json
"actions": {
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   },
   "Select": {
      "type": "Select",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "select": {
            "Product_ID": "@item()"
         }
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
           "Succeeded"
         ]
      }
   }
},
```

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire operazioni sui dati](../logic-apps/logic-apps-perform-data-operations.md)
