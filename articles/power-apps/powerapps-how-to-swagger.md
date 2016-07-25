<properties
	pageTitle="Come personalizzare la definizione Swagger per PowerApps e flussi logici | Microsoft Azure"
	description="Visualizzazione delle estensioni dello schema richieste da Swagger per lavorare con PowerApps e flussi logici"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>


# Personalizzazione della definizione Swagger per PowerApps e flussi logici

>[AZURE.IMPORTANT] Questo argomento è stato spostato nel sito powerapps.microsoft.com in [Personalizzazione della definizione Swagger per PowerApps e flussi logici](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/). Passare a PowerApps per visualizzare la versione più recente. Questo collegamento di Azure verrà archiviato.


## Creazione della definizione API Swagger 2.0 per l'API

Per informazioni su come aggiungere Swagger all'API Web, vedere [Swashbuckle][1].

## Estensioni dello schema
Oltre alle specifiche Swagger standard, sono disponibili alcune estensioni Swagger aggiuntive durante la creazione di un'API personalizzata per PowerApps e flussi logici. Questa sezione elenca e descrive queste estensioni.

##### x-ms-summary
Una stringa che descrive i nomi visualizzati per le entità che non hanno un campo `summary` definito nella specifica Swagger. **Nomi parametri** è un esempio.

##### x-ms-visibility
Questo valore descrive se l'entità viene visualizzata nella finestra di progettazione del flusso della logica. Sono disponibili i valori seguenti:

- "none" (impostazione predefinita)
- "advanced"
- "internal": la finestra di progettazione del flusso della logica non mostra queste operazioni

Se un'operazione è contrassegnata come "importante", il client del flusso della logica si occuperà di evidenziare queste operazioni.

##### x-ms-trigger
Indica se questa operazione può essere usata come trigger nella logica del flusso. Le opzioni includono:
	
- none (impostazione predefinita): l'operazione non può essere usata come trigger.
- single: questa operazione può essere usata anche come trigger.
- batched: questa operazione può essere usata come trigger. Inoltre, questa operazione risponde con una 'matrice' JSON di oggetti e il flusso della logica attiva un trigger per ogni elemento nella matrice.


##### x-ms-dynamic-values
Indica alla finestra di progettazione del flusso della logica che l'API fornisce un elenco di valori consentiti in modo dinamico per questo parametro. La finestra di progettazione del flusso della logica può richiamare un'operazione in base a quanto definito dal valore del campo ed estrarre i valori possibili dal risultato. La finestra di progettazione del flusso può quindi visualizzare tali valori come opzioni per l'utente finale.

Il valore è un oggetto che contiene le proprietà seguenti:
	
- `operationId`: stringa corrispondente all'operationId per l'operazione che viene richiamata
- `parameters`: oggetto le cui proprietà definiscono i parametri necessari per l'operazione
- `value-collection`: stringa di percorso che restituisce una matrice di oggetti nel payload della risposta
- `value-path`: stringa di percorso nell'oggetto all'interno di "value-collection" che fa riferimento al valore del parametro.
- `value-title`: stringa di percorso nell'oggetto all'interno di "value-collection" che fa riferimento a una descrizione per il valore.


Esempio:

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

In questo esempio, il file Swagger definisce l'operazione `TableData_CreateItem` che crea un nuovo oggetto in Salesforce.

Salesforce ha molti oggetti predefiniti. `x-ms-dynamic-values` viene usato per consentire alla finestra di progettazione di comprendere l'elenco degli oggetti di Salesforce predefiniti. L'elenco è ottenuto chiamando `TableMetadata_ListTables`.

##### x-ms-dynamic-schema
Indica alla finestra di progettazione del flusso della logica che lo schema per questo parametro (o risposta) è dinamico per natura. Può richiamare un'operazione in base a quanto definito dal valore del campo e individuare lo schema in modo dinamico. Può quindi visualizzare un'interfaccia utente appropriata per l'input dell'utente o visualizzare i campi disponibili.

Esempio:

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

Ciò è utile negli scenari in cui gli input per un'operazione sono dinamici. Ad esempio, si consideri il caso di SQL. Lo schema di ogni tabella è diverso. Quando un utente seleziona una determinata tabella, la progettazione del flusso della logica deve quindi comprendere la struttura della tabella in modo da potere visualizzare i nomi di colonna. In questo contesto, se la definizione Swagger ha `x-ms-dynamic-schema`, chiama l'operazione corrispondente per recuperare lo schema.


<!--Reference links in article-->
[1]: https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md

<!---HONumber=AcomDC_0713_2016-->