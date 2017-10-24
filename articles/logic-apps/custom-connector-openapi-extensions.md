---
title: Estensioni OpenAPI per i connettori personalizzati - App per la logica di Azure | Microsoft Docs
description: "Estendere la definizione OpenAPI del connettore personalizzato con funzionalità avanzate"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>Estendere la definizione OpenAPI del connettore personalizzato con funzionalità avanzate

Per creare connettori personalizzati per App per la logica di Azure, Microsoft Flow o Microsoft PowerApps, è necessario fornire un file di definizione OpenAPI, ovvero un documento leggibile dal computer e indipendente dal linguaggio che descrive le operazioni e i parametri dell'API. Oltre alle funzionalità predefinite di OpenAPI, è possibile includere anche queste estensioni OpenAPI quando si creano connettori personalizzati per App per la logica e Flow:

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

Di seguito sono disponibili ulteriori dettagli su queste estensioni:

<a name="summary"></a>

## <a name="summary"></a>summary

Specifica il titolo per l'azione (operazione). </br>
Si applica a: operazioni </br>
Consiglio: usare la *maiuscola a inizio frase* per `summary`. </br>
Esempio: "When an event is added to calendar" (Quando viene aggiunto un evento al calendario) o "Send an email" (Inviare un messaggio di posta elettronica)

!["summary" per ogni operazione](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-summary

Specifica il titolo per un'entità. </br>
Si applica a: parametri, schema di risposta </br>
Consiglio: usare l'*iniziale maiuscola* per `x-ms-summary`. </br>
Esempio: "Calendar ID" (ID calendario), "Subject" (Oggetto), "Event Description" (Descrizione evento) e così via

!["x-ms-summary" per ogni entità](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>description

Specifica una spiegazione dettagliata delle funzionalità dell'operazione oppure formato e funzione di un'entità. </br>
Si applica a: operazioni, parametri, schema di risposta </br>
Consiglio: usare la *maiuscola a inizio frase* per `description`. </br>
Esempio: "This operation triggers when a new event is added to the calendar" (Questa operazione viene attivata in seguito all'aggiunta di un nuovo evento al calendario), "Specify the subject of the mail" (Specificare l'oggetto del messaggio) e così via

!["description" per ogni operazione o entità](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibility

Specifica la visibilità agli utenti finali per un'entità. </br>
I possibili valori sono `important`, `advanced` e `internal`. </br>
Si applica a: operazioni, parametri, schemi

* Le operazioni e i parametri `important` vengono sempre visualizzati per primi all'utente.
* Le operazioni e i parametri `advanced` sono nascosti in un altro menu.
* Le operazioni e i parametri `internal` vengono nascosti all'utente.

> [!NOTE] 
> Per i parametri `internal` e `required`, si **devono** specificare valori predefiniti.

Esempio: le operazioni e i parametri `advanced` sono nascosti nel menu **See more** (Vedi altro) e nel menu **Show advanced options** (Mostra opzioni avanzate).

!["x-ms-visibility" per visualizzare o nascondere operazioni e parametri](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dynamic-values

Mostra un elenco popolato per l'utente, in modo che possa selezionare i parametri di input per un'operazione. </br>
Si applica a: parametri </br>
Uso: aggiungere l'oggetto `x-ms-dynamic-values` alla definizione del parametro. Ad esempio, vedere questo [esempio di OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

!["x-ms-dynamic-values" per la visualizzazione di elenchi](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>Proprietà per "x-ms-dynamic-values"

| Nome | Obbligatoria o facoltativa | Descrizione | 
| ---- | -------------------- | ----------- | 
| **operationID** | Obbligatorio | Operazione da chiamare per popolare l'elenco | 
| **value-path** | Obbligatorio | Stringa di percorso nell'oggetto all'interno di `value-collection` che fa riferimento al valore del parametro. </br>Se non si specifica `value-collection`, la risposta viene valutata come una matrice. | 
| **value-title** | Facoltativo | Stringa di percorso nell'oggetto all'interno di `value-collection` che fa riferimento alla descrizione del valore. </br>Se non si specifica `value-collection`, la risposta viene valutata come una matrice. | 
| **value-collection** | Facoltativo | Stringa di percorso che restituisce una matrice di oggetti nel payload della risposta | 
| **parameters** | Facoltativo | Oggetto le cui proprietà specificano i parametri di input necessari per richiamare un'operazione dynamic-values | 
|||| 

Di seguito è riportato un esempio che mostra le proprietà in `x-ms-dynamic-values`:

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>Esempio: tutte le estensioni OpenAPI fino a questo punto

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dynamic-schema

Indica che lo schema per il parametro o la risposta corrente è dinamico. Questo oggetto può richiamare un'operazione definita dal valore di questo campo, individuare lo schema in modo dinamico e visualizzare l'interfaccia utente appropriata per la raccolta di input dagli utenti oppure visualizzare i campi disponibili. 

Si applica a: parametri, risposta

Uso: aggiungere l'oggetto `x-ms-dynamic-schema` alla definizione di un parametro della richiesta o del corpo della risposta. Per un esempio, vedere questo [esempio di OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

Questo esempio mostra come cambia il modulo di input, in base all'elemento selezionato dall'utente nell'elenco a discesa:

!["x-ms-dynamic-schema" per i parametri dinamici](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

Questo esempio mostra invece come cambia l'output, in base all'elemento selezionato dall'utente nell'elenco a discesa. In questa versione, l'utente seleziona "Cars":

!["x-ms-dynamic-schema-response" per l'elemento selezionato "Cars"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

In questa versione, l'utente seleziona "Food":

!["x-ms-dynamic-schema-response" per l'elemento selezionato "Food"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>Proprietà per x-ms-dynamic-schema

| Nome | Obbligatoria o facoltativa | Descrizione | 
| ---- | -------------------- | ----------- | 
| **operationID** | Obbligatorio | Operazione da chiamare per recuperare lo schema | 
| **parameters** | Obbligatorio | Oggetto le cui proprietà specificano i parametri di input necessari per richiamare un'operazione dynamic-schema | 
| **value-path** |Facoltativo | Stringa di percorso che fa riferimento alla proprietà con lo schema. </br>Se non specificato, si presuppone che la risposta contenga lo schema nelle proprietà dell'oggetto radice. | 
|||| 

Di seguito è riportato un esempio per un parametro dinamico:

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

Di seguito è riportato un esempio per una risposta dinamica:

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Descrivere API e connettori personalizzati](../logic-apps/custom-connector-api-postman-collection.md)
* [App per la logica: Registrare il connettore](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: Registrare il connettore](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)