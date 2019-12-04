---
title: Creare, modificare o estendere le definizioni delle app per la logica
description: Come scrivere, modificare ed estendere le definizioni JSON delle app per la logica in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: bffbc29322a57d6bb9b8497299add5dbb0478d2c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792584"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Creare, modificare o estendere JSON per le definizioni di app per la logica - App per la logica di Azure

Quando si creano soluzioni di integrazione a livello aziendale con flussi di lavoro automatizzati in [App per la logica di Azure](../logic-apps/logic-apps-overview.md), le definizioni di app per la logica sottostanti usano il semplice linguaggio dichiarativo JSON (JavaScript Object Notation) insieme allo [schema del linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md) per la descrizione e la convalida. Questi formati rendono le definizioni di app per la logica più semplici da leggere e da comprendere, senza dover necessariamente avere familiarità con il codice. Quando si vuole automatizzare la creazione e la distribuzione di app per la logica, è possibile includere le definizioni di app per la logica come [risorse di Azure](../azure-resource-manager/resource-group-overview.md) all'interno di [modelli di Azure Resource Manager](../azure-resource-manager/template-deployment-overview.md). Per creare, gestire e distribuire app per la logica, è quindi possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), l'[interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) o le [API REST di App per la logica di Azure](https://docs.microsoft.com/rest/api/logic/).

Per usare le definizioni di app per la logica in JSON, aprire l'editor della visualizzazione Codice quando si lavora nel portale di Azure o in Visual Studio oppure copiare la definizione nell'editor che si preferisce. Se non si ha familiarità con le app per la logica, vedere [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Alcune funzionalità di App per la logica di Azure, ad esempio la definizione di parametri e di più trigger nelle definizioni di app per la logica, sono disponibili solo in JSON e non in Progettazione app per la logica.
> Pertanto, per queste attività è necessario lavorare nella visualizzazione Codice o in un altro editor.

## <a name="edit-json---azure-portal"></a>Modificare JSON - Portale di Azure

1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.

2. Dal menu a sinistra scegliere **Tutti i servizi**. Nella casella di ricerca cercare "app per la logica" e quindi selezionare l'app per la logica desiderata nei risultati.

3. Nel menu dell'app per la logica, in **Strumenti di sviluppo** selezionare **Visualizzazione codice app per la logica**.

   L'editor della visualizzazione Codice si apre e mostra la definizione dell'app per la logica in formato JSON.

## <a name="edit-json---visual-studio"></a>Modificare JSON - Visual Studio

Prima di usare la definizione dell'app per logica in Visual Studio, assicurarsi di avere [installato gli strumenti richiesti](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Per creare un'app per la logica con Visual Studio, vedere [Quickstart: Automate tasks and processes with Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) (Guida introduttiva: Automatizzare attività e processi con App per la logica di Azure - Visual Studio).

In Visual Studio è possibile aprire le app per la logica che sono state create e distribuite direttamente dal portale di Azure o come progetti di Azure Resource Manager da Visual Studio.

1. Aprire la soluzione di Visual Studio o il progetto del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) contenente l'app per la logica.

2. Individuare e aprire la definizione dell'app per la logica, che per impostazione predefinita viene visualizzata in un [modello di Resource Manager](../azure-resource-manager/template-deployment-overview.md), denominato **LogicApp.json**. È possibile usare e personalizzare questo modello per la distribuzione in ambienti diversi.

3. Aprire il menu di scelta rapida per la definizione e il modello dell'app per la logica. Selezionare **Open With Logic App Designer** (Apri con Progettazione app per la logica).

   ![Aprire un'app per la logica in una soluzione di Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

4. Nella parte inferiore della finestra di progettazione scegliere **Visualizzazione Codice**. 

   L'editor della visualizzazione Codice si apre e mostra la definizione dell'app per la logica in formato JSON.

5. Per tornare alla visualizzazione della finestra di progettazione, nella parte inferiore dell'editor della visualizzazione Codice, scegliere **Progettazione**.

## <a name="parameters"></a>parameters

Il ciclo di vita della distribuzione ha in genere ambienti diversi per lo sviluppo, il test, la gestione temporanea e la produzione. Quando si hanno valori che si vuole riusare nell'app per la logica senza hardcoded o che variano in base alle esigenze di distribuzione, è possibile creare un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) per la definizione del flusso di lavoro, in modo da poter automatizzare la distribuzione delle app per la logica. 

Attenersi alla procedura generale per *parametrizzare*o definire e utilizzare i parametri per tali valori. È quindi possibile fornire i valori in un file di parametri separato che passa tali valori al modello. In questo modo, è possibile modificare i valori più facilmente senza dover aggiornare e ridistribuire l'app per la logica. Per informazioni dettagliate, vedere [Panoramica: automatizzare la distribuzione per le app per la logica con i modelli Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. Nel modello definire i parametri del modello e i parametri di definizione del flusso di lavoro per accettare i valori da usare rispettivamente in fase di distribuzione e di Runtime.

   I parametri del modello sono definiti in una sezione dei parametri all'esterno della definizione del flusso di lavoro, mentre i parametri di definizione del flusso di lavoro sono definiti in una sezione dei parametri all'interno della definizione del flusso di lavoro.

1. Sostituire i valori hardcoded con espressioni che fanno riferimento a questi parametri. Nelle espressioni di modello viene utilizzata una sintassi diversa dalle espressioni di definizione del flusso di lavoro.

   Evitare di complicare il codice non usando le espressioni modello, che vengono valutate in fase di distribuzione, all'interno delle espressioni di definizione del flusso di lavoro, che vengono valutate in fase di esecuzione Usare solo espressioni di modello all'esterno della definizione del flusso di lavoro. Usare solo le espressioni di definizione del flusso di lavoro nella definizione del flusso di lavoro.

   Quando si specificano i valori per i parametri di definizione del flusso di lavoro, è possibile fare riferimento ai parametri del modello usando la sezione Parameters che non rientra nella definizione del flusso di lavoro ma ancora all'interno della definizione di risorsa per l'app per la logica. In questo modo, è possibile passare i valori dei parametri di modello nei parametri della definizione del flusso di lavoro.

1. Archiviare i valori per i parametri in un [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) separato e includere il file con la distribuzione.

## <a name="process-strings-with-functions"></a>Elaborare le stringhe con le funzioni

App per la logica ha diverse funzioni per l'uso delle stringhe. Si supponga, ad esempio, di voler passare un nome di società da un ordine a un altro sistema. Tuttavia, non si è certi di come gestire correttamente la codifica dei caratteri. Si potrebbe eseguire la codifica Base 64 su questa stringa, ma, per evitare caratteri di escape nell'URL, è invece possibile sostituire alcuni caratteri. È inoltre necessario ottenere solo una sottostringa del nome della società, perché i primi cinque caratteri non vengono usati.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

I passaggi seguenti descrivono come questa stringa viene elaborata nell'esempio, a partire dall'interno verso l'esterno:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Ottenere l'elemento [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) per il nome della società per poter ottenere il numero totale di caratteri.

2. Per ottenere una stringa più breve, sottrarre `5`.

3. Ottenere ora [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md). Iniziare dall'indice `5` e procedere alla parte rimanente della stringa.

4. Convertire la sottostringa in una stringa [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md).

5. [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) sostituisce ora tutti i caratteri `+` con caratteri `-`.

6. [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) sostituisce infine tutti i caratteri `/` con caratteri `_`.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Eseguire il mapping degli elementi di elenco ai valori delle proprietà, quindi usare le mappe come parametri

Per ottenere risultati diversi in base al valore di una proprietà, è possibile creare una mappa che associa ogni valore della proprietà a un risultato, quindi usare tale mappa come parametro.

Questo flusso di lavoro, ad esempio, definisce alcune categorie come parametri e una mappa che associa tali categorie a un URL specifico. Il flusso di lavoro ottiene prima di tutto un elenco degli articoli, quindi usa la mappa per trovare l'URL corrispondente alla categoria per ogni articolo.

*   La funzione [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) controlla se la categoria corrisponde a una categorie nota definita.

*   Dopo avere ottenuto una categoria corrispondente, l'esempio esegue il pull dell'elemento dalla mappa usando le parentesi quadre: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "science": "https://www.nasa.gov",
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
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
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

## <a name="get-data-with-date-functions"></a>Ottenere i dati con le funzioni di data

Per ottenere i dati da un'origine dati che non supporta i *trigger* in modo nativo, è possibile usare le funzioni di data per lavorare invece con ore e date. Questa espressione, ad esempio, trova quanto tempo richiedono i passaggi di questo flusso di lavoro, procedendo dall'interno verso l'esterno:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Dall'azione `order` estrarre `startTime`.
2. Ottenere l'ora corrente con `utcNow()`.
3. Sottrarre un secondo:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   È possibile usare altre unità di tempo, ad esempio `minutes` o `hours`.

3. È ora possibile confrontare questi due valori. 

   Se il primo valore è minore del secondo valore, è trascorso più di un secondo dal momento in cui è stato inserito l'ordine.

Per formattare le date, è possibile usare formattatori di stringa. Per ottenere la specifica RFC1123, ad esempio, usare [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md). Altre informazioni sulla [formattazione delle date](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
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
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
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
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
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

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire i passaggi in base a una condizione (istruzioni condizionali)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Eseguire i passaggi in base a valori diversi (istruzioni switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Eseguire e ripetere i passaggi (cicli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Eseguire o unire passaggi paralleli (rami)](../logic-apps/logic-apps-control-flow-branches.md)
* [Eseguire i passaggi in base allo stato delle azioni raggruppate (ambiti)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Altre informazioni sullo [schema del linguaggio di definizione del flusso di lavoro per App per la logica di Azure](../logic-apps/logic-apps-workflow-definition-language.md)
* Altre informazioni su [azioni e trigger del flusso di lavoro per App per la logica di Azure](../logic-apps/logic-apps-workflow-actions-triggers.md)
