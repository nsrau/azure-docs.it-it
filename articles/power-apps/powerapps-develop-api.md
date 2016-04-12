<properties
	pageTitle="Sviluppo dell’API per PowerApps Enterprise | Microsoft Azure"
	description="Compilare o creare API personalizzate per PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="rajram"/>

# Sviluppare un'API per PowerApps

È possibile creare o sviluppare la propria API che può essere usata all'interno di PowerApps. I passaggi includono:

- Compilare e distribuire l'API
- Creare la definizione API [Swagger 2.0](http://swagger.io/) per l'API

Questi passaggi sono illustrati in questo argomento.

## Passaggio 1: Compilare e distribuire l'API

La compilazione e la distribuzione di un'API per PowerApps equivalgono alla creazione di un'API. È possibile scegliere il linguaggio di programmazione e il framework preferiti. È anche possibile scegliere di ospitare l'API in qualunque posizione. È consigliabile ospitarla nello stesso ambiente del servizio app di PowerApps come [app per le API](https://azure.microsoft.com/services/app-service/api/).

Gli articoli seguenti illustrano come compilare e distribuire un'API .Net, Java o Node.js nell'ambiente del servizio app:

- [Compilare e distribuire un'app .NET nel servizio app di Azure](../app-service-api/app-service-api-dotnet-get-started.md)
- [Compilare e distribuire un'app per le API Java nel servizio app di Azure](../app-service-api/app-service-api-java-api-app.md)
- [Compilare e distribuire un'app per le API Node.js in Servizio app di Azure](../app-service-api/app-service-api-nodejs-api-app.md)


## Passaggio 2: Creare la definizione API Swagger 2.0 per l'API

Se si seguono le istruzioni di uno degli articoli indicati nel *Passaggio 1*, viene generata automaticamente una definizione API Swagger 2.0 standard per l'API. Per ottimizzarla per PowerApps, è possibile personalizzare facoltativamente la definizione API Swagger 2.0 generata mediante le seguenti estensioni dello schema.

Per informazioni su come personalizzare la definizione API Swagger 2.0 in generale, vedere [Personalizzare le definizioni API generate da Swashbuckle](../app-service-api/app-service-api-dotnet-swashbuckle-customize.md).

### Estensioni dello schema
Oltre allo swagger generato automaticamente da Swashbuckle, sono disponibili alcune estensioni swagger aggiuntive durante la creazione di un'API per PowerApps. Questa sezione elenca e descrive tali estensioni.

##### x-ms-summary
Una stringa che descrive i nomi visualizzati per le entità che non hanno un campo di riepilogo definito nella specifica Swagger. **Nomi parametri** è un esempio.

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
	
- operationId: stringa corrispondente all'operationId per l'operazione che viene richiamata.
- parameters: oggetto le cui proprietà definiscono i parametri necessari per l'operazione.
- value-collection: stringa di percorso che restituisce una matrice di oggetti nel payload della risposta.
- value-path: stringa di percorso nell'oggetto all'interno di "value-collection" che fa riferimento al valore del parametro.
- value-title: stringa di percorso nell'oggetto all'interno di "value-collection" che fa riferimento a una descrizione per il valore.


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

Nell'esempio precedente il file swagger definisce un'operazione denominata _TableData\_CreateItem_ che crea un nuovo oggetto in Salesforce.

Salesforce ha molti oggetti predefiniti. _x-ms-dynamic-values_ viene usato per consentire alla finestra di progettazione di comprendere l'elenco degli oggetti di Salesforce predefiniti. L'elenco viene ottenuto chiamando _TableMetadata\_ListTables_.

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

Ciò è utile negli scenari in cui gli input per un'operazione sono dinamici. Ad esempio, si consideri il caso di SQL. Lo schema di ogni tabella è diverso. Quando un utente seleziona una determinata tabella, la progettazione del flusso della logica deve quindi comprendere la struttura della tabella in modo da potere visualizzare i nomi di colonna. In questo contesto, se la definizione swagger ha _x-ms-dynamic-schema_, chiama l'operazione corrispondente per recuperare lo schema.

<!---HONumber=AcomDC_0309_2016-->