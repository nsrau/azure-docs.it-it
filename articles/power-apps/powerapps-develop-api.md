<properties
	pageTitle="Sviluppo dell’API per PowerApps Enterprise | Microsoft Azure"
	description="Compilare o creare API personalizzate per PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="rajram"/>

# Sviluppare un'API per PowerApps

È possibile creare o sviluppare la propria API che può essere utilizzata all'interno di PowerApps. I passaggi includono:

- Compilare e distribuire l’API
- Creare la definizione API [Swagger 2.0](http://swagger.io/) per l'API

In questo argomento vengono riportati i passaggi seguenti.

## Passaggio 1: Compilare e distribuire l’API

La compilazione e la distribuzione di un'API per PowerApps equivalgono alla creazione di un'API. È possibile scegliere il linguaggio di programmazione e il framework preferito. È inoltre possibile scegliere di ospitare l'API in qualunque posizione. È consigliabile ospitarla nello stesso ambiente del servizio app di PowerApps come [app per le API](https://azure.microsoft.com/services/app-service/api/).

Gli articoli seguenti illustrano come compilare e distribuire un’API .Net, Java o Node.js nell'ambiente del servizio app:

- [Compilare e distribuire un'app .NET nel servizio app di Azure](../app-service-api-dotnet-get-started.md)
- [Compilare e distribuire un'app per le API Java nel servizio app di Azure](../app-service-api-java-api-app.md)
- [Compilare e distribuire un'app per le API Node.js in Servizio app di Azure](../app-service-api-nodejs-api-app.md)


## Passaggio 2: Creare la definizione API Swagger 2.0 per l'API

Se si segue uno degli articoli a viene fatto riferimento nel *Passaggio 1*, viene generata automaticamente una definizione API Swagger 2.0 standard per l'API. Per ottimizzarla per PowerApps, è possibile personalizzare facoltativamente la definizione API Swagger 2.0 generata mediante le seguenti estensioni dello schema.

Per informazioni su come personalizzare la definizione API Swagger 2.0 in generale, vedere [Personalizzare le definizioni API generate da Swashbuckle](../app-service-api-dotnet-swashbuckle-customize.md).

### Estensioni dello schema
Oltre allo swagger generato automaticamente da Swashbuckle, sono disponibili alcune estensioni swagger aggiuntive durante la creazione di un'API per PowerApps. In questa sezione vengono elencate e descritte tali estensioni.

##### x-ms-summary
Una stringa che descrive i nomi visualizzati per le entità che non dispongono di un campo di riepilogo definito nella specifica Swagger. **Nomi parametri** è un esempio.

##### x-ms-visibility
Questo valore descrive se l'entità viene visualizzata nella finestra di progettazione del flusso della logica. Sono disponibili i seguenti valori:

- "none" (impostazione predefinita)
- "advanced"
- "internal": la finestra di progettazione del flusso della logica non mostra queste operazioni

Se un'operazione è contrassegnata come "importante", il client del flusso della logica si occuperà di evidenziare queste operazioni.

##### x-ms-trigger
Definisce se questa operazione può essere utilizzata come trigger nella logica del flusso. Le opzioni includono:
	
- nessuno (predefinito): l'operazione non può essere utilizzata come trigger.
- singolo: questa operazione può essere utilizzata anche come trigger.
- modalità batch: questa operazione può essere utilizzata come trigger. Inoltre, questa operazione risponde con un 'array' JSON di oggetti e il flusso della logica attiva un trigger per ogni elemento nell’array.


##### x-ms-dynamic-values
Si tratta di un suggerimento alla progettazione del flusso della logica che l'API fornisce un elenco di valori consentiti in modo dinamico per questo parametro. La finestra di progettazione del flusso della logica può richiamare un'operazione come definita dal valore del campo ed estrarre i valori possibili dal risultato. La finestra di progettazione del flusso può quindi visualizzare tali valori come opzioni per l'utente finale.

Il valore è un oggetto che contiene le proprietà seguenti:
	
- operationId: una stringa corrispondente all’operationId per l'operazione che viene richiamata
- parameters: un oggetto le cui proprietà definiscono i parametri necessari per l'operazione
- value-collection: una stringa di percorso che valuta in una matrice di oggetti nel payload della risposta
- value-path: una stringa di percorso nell'oggetto all'interno di "value-collection" che fa riferimento al valore del parametro.
- value-title: una stringa di percorso nell'oggetto all'interno di "value-collection" che fa riferimento a una descrizione per il valore.


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

Nell'esempio precedente, il file swagger definisce un'operazione denominata _TableData\_CreateItem_ che crea un nuovo oggetto in Salesforce.

Salesforce dispone di molti oggetti integrati. _x-ms-dynamic-values_ viene utilizzato per consentire al progettazione di capire l'elenco degli oggetti di Salesforce integrati. Si ottiene chiamando _TableMetadata\_ListTables_.

##### x-ms-dynamic-schema
Si tratta di un suggerimento al progettazione del flusso della logica che lo schema per questo parametro (o risposta) è dinamico in natura. Può richiamare un'operazione come definita dal valore del campo e individuare lo schema in modo dinamico. Quindi può visualizzare un'interfaccia utente appropriata per l'input dell'utente o visualizzare i campi disponibili.

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

Ciò è utile in scenari in cui gli input per un'operazione sono dinamici. Ad esempio, si consideri il caso di SQL. Lo schema di ogni tabella è diverso. Quindi, quando un utente seleziona una determinata tabella, la progettazione del flusso della logica deve comprendere la struttura della tabella in modo che possa visualizzare i nomi di colonna. In questo contesto, se la definizione swagger ha _x-ms-dynamic-schema_, chiama l'operazione corrispondente per recuperare lo schema.

<!---HONumber=AcomDC_1203_2015-->