<properties
	pageTitle="Associazioni di DocumentDB in Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come usare le associazioni Azure DocumentDB n Funzioni di Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server"/> 

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/> 

# Associazioni di DocumentDB in Funzioni di Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice di associazioni di Azure DocumentDB in Funzioni di Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="docdbinput"></a> Associazione di input di Azure DocumentDB

Le associazioni di input possono caricare un documento da una raccolta DocumentDB e passarlo direttamente all'associazione. L'ID documento può essere determinato in base al trigger che ha richiamato la funzione. In una funzione C# eventuali modifiche apportate al record verranno automaticamente inviate alla raccolta quando la funzione termina correttamente.

#### function.json per associazione di input di DocumentDB

Il file *function.json* specifica le proprietà seguenti:

- `name`: nome della variabile usato nel codice della funzione per il documento.
- `type`: deve essere impostato su "documentdb".
- `databaseName`: database che contiene il documento.
- `collectionName`: raccolta che contiene il documento.
- `id`: ID del documento da recuperare. Questa proprietà supporta associazioni simili a "{queueTrigger}", che useranno il valore della stringa del messaggio della coda come ID documento.
- `connection`: questa stringa deve essere un'impostazione dell'applicazione configurata sull'endpoint per l'account DocumentDB. Se si sceglie l'account dalla scheda Integra, verrà automaticamente creata una nuova impostazione dell'app con un nome nel formato yourAccount\_DOCUMENTDB. Se è necessario creare manualmente l'impostazione dell'app, la stringa di connessione effettiva deve avere il formato AccountEndpoint=<Endpoint dell'account>;AccountKey=<Chiave di accesso primaria>;.
- `direction: deve essere impostato su *"in"*.

Esempio di *function.json*:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Esempio di codice di input di Azure DocumentDB per un trigger della coda C#
 
Usando il file function.json di esempio precedente, l'associazione di input di DocumentDB recupererà il documento con l'ID corrispondente alla stringa del messaggio della coda e lo passerà al parametro "document". Se tale documento non viene trovato, il parametro "document" sarà null. Il documento viene quindi aggiornato con il nuovo valore di text quando la funzione termina.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Esempio di codice di input di Azure DocumentDB per un trigger della coda F#

Usando il file function.json di esempio precedente, l'associazione di input di DocumentDB recupererà il documento con l'ID corrispondente alla stringa del messaggio della coda e lo passerà al parametro "document". Se tale documento non viene trovato, il parametro "document" sarà null. Il documento viene quindi aggiornato con il nuovo valore di text quando la funzione termina.

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- "This has changed."

Sarà necessario un file `project.json` che usa NuGet per specificare i pacchetti `FSharp.Interop.Dynamic` e `Dynamitey` come dipendenze dei pacchetti come segue:

	{
	  "frameworks": {
	    "net46": {
	      "dependencies": {
	        "Dynamitey": "1.0.2",
	        "FSharp.Interop.Dynamic": "3.0.0"
	      }
	    }
	  }
	}

Verrà usato NuGet per recuperare le dipendenze e verrà creato un riferimento alle dipendenze nello script.

#### Esempio di codice di input di Azure DocumentDB per un trigger della coda di Node.js
 
Usando il file function.json di esempio precedente, l'associazione di input di DocumentDB recupererà il documento con l'ID corrispondente alla stringa del messaggio della coda e lo passerà alla proprietà dell'associazione `documentIn`. Nelle funzioni Node.js i documenti aggiornati non vengono inviati alla raccolta. È tuttavia possibile passare l'associazione di input direttamente a un'associazione di output di DocumentDB denominata `documentOut` per supportare gli aggiornamenti. Questo esempio di codice aggiorna la proprietà text del documento di input e la imposta come documento di output.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

## <a id="docdboutput"></a> Associazioni di output di Azure DocumentDB

Le funzioni possono scrivere documenti JSON in un database di Azure DocumentDB usando l'associazione di output **Documento di Azure DocumentDB**. Per altre informazioni su Azure DocumentDB, vedere [Introduzione a DocumentDB: un Database NoSQL JSON](../documentdb/documentdb-introduction.md) ed [Esercitazione su NoSQL: Compilare un'applicazione console C# di DocumentDB](../documentdb/documentdb-get-started.md).

#### function.json per associazione di output di DocumentDB

Il file function.json specifica le proprietà seguenti:

- `name`: nome della variabile usato nel codice della funzione per il nuovo documento.
- `type`: deve essere impostato su *"documentdb"*.
- `databaseName`: database contenente la raccolta in cui verrà creato il nuovo documento.
- `collectionName`: raccolta in cui verrà creato il nuovo documento.
- `createIfNotExists`: valore booleano che indica se la raccolta viene creata quando non esiste. Il valore predefinito è *false*. Il motivo è che le nuove raccolte vengono create con una velocità effettiva riservata, che ha implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: questa stringa deve essere un'**impostazione dell'applicazione** configurata sull'endpoint per l'account DocumentDB. Se si sceglie l'account dalla scheda **Integra**, verrà creata una nuova impostazione dell'app con un nome nel formato `yourAccount_DOCUMENTDB`. Se è necessario creare manualmente l'impostazione dell'app, la stringa di connessione effettiva deve avere il formato `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`.
- `direction`: deve essere impostato su *"out"*.
 
Function.json di esempio:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Esempio di codice di output di Azure DocumentDB per un trigger della coda di Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

Documento di output:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Esempio di codice di output di Azure DocumentDB per un trigger della coda F#

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### Esempio di codice di output di Azure DocumentDB per un trigger della coda C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Impostazione del nome file nell'esempio di codice di output di Azure DocumentDB

Per impostare il nome del documento nella funzione, è sufficiente impostare il valore `id`. Se, ad esempio, il contenuto JSON di un dipendente venisse rilasciato nella coda in modo simile al seguente:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

Sarebbe possibile usare il codice C# seguente in una funzione trigger della coda:
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

Oppure il codice F# equivalente:

	open FSharp.Interop.Dynamic
	open Newtonsoft.Json

	type Employee = {
	    id: string
	    name: string
	    employeeId: string
	    address: string
	}

	let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
	    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
	    let employee = JObject.Parse(myQueueItem)
	    employeeDocument <-
	        { id = sprintf "%s-%s" employee?name employee?employeeId
	          name = employee?name
	          employeeId = employee?id
	          address = employee?address }

Output di esempio:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Passaggi successivi

[AZURE.INCLUDE [Passaggi successivi](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->