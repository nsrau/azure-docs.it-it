<properties
   pageTitle="Test di Funzioni di Azure | Microsoft Azure"
   description="Testare Funzioni di Azure con Postman, cURL e Node.js."
   services="functions"
   documentationCenter="na"
   authors="wesmc7777"
   manager="erikre"
   editor=""
   tags=""
   keywords="funzioni di azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, test"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="wesmc"/>  

# Test di Funzioni di Azure

## Overview

In questa esercitazione si apprenderanno diversi approcci ai test delle funzioni. Verrà definita una funzione trigger HTTP che accetta l'input tramite un parametro della stringa di query o il corpo della richiesta. Il codice del modello predefinito di **funzione HttpTrigger Node.js** supporta il parametro della stringa di query `name`. Verrà anche aggiunto il codice per supportare tale parametro insieme a informazioni relative al parametro `address` per l'utente nel corpo della richiesta.

## Creare una funzione per i test

Per la maggior parte di questa esercitazione si userà una versione leggermente modificata del modello di **funzione HttpTrigger Nodejs** disponibile quando si crea una nuova funzione. Se occorrono suggerimenti per la creazione di una nuova funzione, è possibile vedere l'esercitazione [Creare la prima funzione di Azure](functions-create-first-azure-function.md). Scegliere semplicemente il modello di **funzione HttpTrigger Nodejs** quando si crea la funzione di test nel [portale di Azure].

Il modello di funzione predefinito è fondamentalmente una funzione Hello World che restituisce il nome dal parametro della stringa query o del corpo della richiesta, `name=<your name>`. Il codice verrà aggiornato per consentire all'utente di fornire anche il nome e un indirizzo come contenuto JSON nel corpo della richiesta. La funzione restituirà quindi queste informazioni al client, se disponibile.

Aggiornare la funzione con il codice seguente che verrà usato per il test:

	module.exports = function(context, req) {
	    context.log("Node.js HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
	    context.log("Request Headers = " + JSON.stringify(req.headers));    
	
	    if (req.query.name || (req.body && req.body.name)) {
	        if (typeof req.query.name != "undefined") {
	            context.log("Name was provided as a query string param..."); 
	            ProcessNewUserInformation(context, req.query.name);
	        }
	        else {
	            context.log("Processing user info from request body..."); 
	            ProcessNewUserInformation(context, req.body.name, req.body.address);
	        }
	    }
	    else {
	        context.res = {
	            status: 400,
	            body: "Please pass a name on the query string or in the request body"
	        };
	    }
	    context.done();
	};
	
	function ProcessNewUserInformation(context, name, address)
	{    
	    context.log("Processing User Information...");            
	    context.log("name = " + name);            
	    echoString = "Hello " + name;
	    
	    if (typeof address != "undefined")
	    {
	        echoString += "\n" + "The address you provided is " + address;
	        context.log("address = " + address);            
	    }
	    
	    context.res = {
	            // status: 200, /* Defaults to 200 */
	            body: echoString
	        };
	}


## Eseguire il test di una funzione con gli strumenti

### Eseguire il test con cURL

Spesso, quando si eseguono test del software, non è necessario eseguire ricerche particolari al di là della riga di comando per eseguire il debug dell'applicazione. L'approccio è analogo per le funzioni.

Per testare la funzione precedente, copiare l'**URL funzione** dal portale. Avrà il formato seguente:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
Questo è l'URL per attivare la funzione. È possibile testarlo con il comando cURL dalla riga di comando per eseguire una richiesta Get (`-G` o `--get`) sulla funzione:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
L'esempio particolare precedente richiede un parametro della stringa di query che può essere passato come dati (`-d`) nel comando cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>
    
Premere INVIO per visualizzare l'output della funzione nella riga di comando.

![](./media/functions-test-a-function/curl-test.png)

Nella finestra **Log** del portale viene registrato un output simile al seguente durante l'esecuzione della funzione:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### Eseguire il test con un browser

Le funzioni che non richiedono parametri o richiedono solo parametri della stringa di query possono essere testate con un browser.

Per testare la funzione definita sopra, copiare l'**URL funzione** dal portale. Avrà il formato seguente:

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Aggiungere il parametro della stringa di query `name` come indicato di seguito, usando un nome effettivo per il segnaposto `<Enter a name here>`.

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Incollare l'URL nel browser. Verrà restituita una risposta simile alla seguente.

![](./media/functions-test-a-function/browser-test.png)

Nella finestra **Log** del portale viene registrato un output simile al seguente durante l'esecuzione della funzione:

	2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
	2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
	2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Wes from a browser
	2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"it-IT"}
	2016-03-23T07:35:10.338 Name was provided as a query string param.
	2016-03-23T07:35:10.338 Processing User Information...
	2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### Eseguire il test con Postman

Postman è lo strumento consigliato per testare la maggior parte delle funzioni. Per installare Postman, vedere la pagina dalla quale è possibile [ottenere Postman](https://www.getpostman.com/). Postman consente di controllare molti più attributi di una richiesta HTTP.

> [AZURE.TIP] Usare il client REST preferito. Ecco alcune alternative a Postman:
> 
> * [Fiddler](http://www.telerik.com/fiddler)
> * [Paw](https://luckymarmot.com/paw)

Per testare la funzione con un corpo della richiesta in Postman:

1. Avviare Postman dal pulsante **App** nell'angolo in alto a sinistra di una finestra del browser Chrome.
2. Copiare l'**URL funzione** e incollarlo in Postman. Include il parametro della stringa di query del codice di accesso.
3. Modificare il metodo HTTP in **POST**.
4. Fare clic su **Body** > **raw** e aggiungere il corpo della richiesta JSON simile al seguente:

		{
		    "name" : "Wes testing with Postman",
		    "address" : "Seattle, W.A. 98101"
		}

5. Fare clic su **Send**.

L'immagine seguente mostra il test della semplice funzione echo di esempio in questa esercitazione.

![](./media/functions-test-a-function/postman-test.png)

Nella finestra **Log** del portale viene registrato un output simile al seguente durante l'esecuzione della funzione:

	2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
	2016-03-23T08:04:57.763 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
	2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"it-IT"}
	2016-03-23T08:04:57.763 Processing user info from request body...
	2016-03-23T08:04:57.763 Processing User Information...
	2016-03-23T08:04:57.763 name = Wes testing with Postman
	2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
	2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    
### Eseguire il test di un trigger del BLOB con Storage Explorer

È possibile testare una funzione trigger del BLOB con [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

1. Nel [portale di Azure] creare una nuova funzione trigger del BLOB in C# o Node per l'app Funzioni. Impostare il percorso da monitorare sul nome del contenitore BLOB. ad esempio:

		files

2. Fare clic sul pulsante **+** per selezionare o creare l'account di archiviazione da usare. Fare quindi clic su **Crea**.

3. Creare un file di testo con il contenuto seguente e salvarlo:

		A text file for blob trigger function testing.

4. Eseguire [Microsoft Azure Storage Explorer](http://storageexplorer.com/) e connettersi al contenitore BLOB nell'account di archiviazione monitorato.

5. Fare clic sul pulsante **Carica** e caricare il file di testo.

	![](./media/functions-test-a-function/azure-storage-explorer-test.png)


	Il codice della funzione trigger del BLOB predefinita segnalerà l'elaborazione del BLOB nei log:

		2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
		2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
		2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
		2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## Eseguire il test di una funzione all'interno di Funzioni

### Eseguire il test con il pulsante Esegui nel portale di Funzioni

Il portale fornisce un pulsante **Esegui** che consente di eseguire alcuni test limitati. È possibile specificare un corpo della richiesta con il pulsante Esegui, tuttavia non si possono specificare i parametri della stringa di query o aggiornare le intestazioni della richiesta.

Eseguire il test della funzione trigger HTTP creata in precedenza aggiungendo una stringa JSON simile alla seguente nel campo **Corpo della richiesta** e quindi fare clic sul pulsante **Esegui**.

	{
		"name" : "Wes testing Run button",
		"address" : "USA"
	} 

Nella finestra **Log** del portale viene registrato un output simile al seguente durante l'esecuzione della funzione:

	2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
	2016-03-23T08:03:18.697 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
	2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"it-IT"}
	2016-03-23T08:03:18.697 Processing user info from request body...
	2016-03-23T08:03:18.697 Processing User Information...
	2016-03-23T08:03:18.697 name = Wes testing Run button
	2016-03-23T08:03:18.697 address = USA
	2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### Eseguire il test con un trigger del timer

Alcune funzioni non possono essere realmente testate con gli strumenti citati in precedenza. Ad esempio, una funzione trigger della coda eseguita quando un messaggio viene inserito in [Archiviazione code di Azure](../storage/storage-dotnet-how-to-use-queues.md). È sempre possibile scrivere codice per inserire un messaggio nella coda. Di seguito ne viene fornito un esempio in un progetto console. Esiste tuttavia un altro approccio che può essere usato per eseguire il test direttamente con le funzioni.

È possibile usare un trigger del timer configurato con un'associazione di output della coda. Il codice del trigger del timer può quindi scrivere i messaggi di prova nella coda. Questa sezione illustra in dettaglio un esempio.

Per informazioni più dettagliate sull'uso di associazioni con Funzioni di Azure, vedere la [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md).


#### Creare trigger della coda per il test

Per illustrare questo approccio, si creerà prima di tutto una funzione trigger della coda che si vuole testare per una coda denominata `queue-newusers`. Questa funzione elaborerà le informazioni relative a nome e indirizzo per un nuovo utente inserito nell'archiviazione code di Azure.

> [AZURE.NOTE] Se si usa un nome di coda diverso, assicurarsi che il nome usato sia conforme alle regole descritte in [Denominazione di code e metadati](https://msdn.microsoft.com/library/dd179349.aspx). In caso contrario, si otterrà un codice di stato HTTP 400: Richiesta non valida.

1. Nel [portale di Azure] fare clic su **Nuova funzione** > **QueueTrigger - C#** per l'app Funzioni.
2. Immettere il nome della coda da monitorare tramite la funzione queue

		queue-newusers 

3. Fare clic sul pulsante **+** (aggiunta) per selezionare o creare l'account di archiviazione da usare. Fare quindi clic su **Crea**.
4. Lasciare aperta la finestra del browser nel portale per monitorare le voci di log per il codice del modello di funzione coda predefinito.


#### Creare un trigger del timer per inserire un messaggio nella coda

1. Aprire il [portale di Azure] in una nuova finestra del browser e passare all'app per le funzioni.
2. Fare clic su **Nuova funzione** > **TimerTrigger - C#**. Immettere un'espressione CRON per impostare la frequenza con cui il codice del timer eseguirà il test della funzione coda. Fare quindi clic su **Crea**. Se si vuole che il test venga eseguito ogni 30 secondi, è possibile usare l'[espressione CRON](https://wikipedia.org/wiki/Cron#CRON_expression) seguente:

		*/30 * * * * *


2. Fare clic sulla scheda **Integra** per il nuovo trigger del timer.
3. In **Output** fare clic sul pulsante **+ Nuovo output**. Fare clic su **coda** e quindi sul pulsante **Seleziona**.
4. Prendere nota del nome che si usa per l'**oggetto messaggio della coda** che verrà usato nel codice della funzione timer.

		myQueue

4. Immettere il nome della coda a cui verrà inviato il messaggio:

		queue-newusers 

3. Fare clic sul pulsante **+** (aggiunta) per selezionare l'account di archiviazione usato in precedenza con il trigger della coda. Fare quindi clic su **Salva**.
4. Fare clic sulla scheda **Sviluppo** per il trigger del timer.
5. È possibile usare il codice seguente per la funzione timer C#, a condizione di avere usato lo stesso nome di oggetto messaggio della coda indicato sopra. Fare quindi clic su **Salva**.

		using System;
		
		public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
		{
		    String newUser = 
		    "{"name":"User testing from C# timer function","address":"XYZ"}";
		
		    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
		    log.Verbose($"{newUser}");   
		    
		    myQueue = newUser;
		}

A questo punto, se è stata usata l'espressione CRON di esempio, la funzione timer C# verrà eseguita ogni 30 secondi. I log per la funzione timer segnaleranno ogni esecuzione:

	2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
	2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
	2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

Nella finestra del browser per la funzione coda viene visualizzato ogni messaggio in fase di elaborazione:

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	
## Eseguire il test di una funzione con il codice

### Eseguire il test di una funzione trigger HTTP con il codice Node.js

È possibile usare il codice Node.js per eseguire una richiesta HTTP per testare la funzione di Azure.

Assicurarsi di impostare:

- `host` nelle opzioni di richiesta per l'host dell'app per le funzioni.
- Il nome della funzione in `path`.
- Il codice di accesso (`<your code>`) in `path`.

Esempio di codice

	var http = require("http");
	
	var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";
	
	var nameBodyJSON = {
	    name : "Wes testing with Node.JS code",
	    address : "Dallas, T.X. 75201"
	};
	
	var bodyString = JSON.stringify(nameBodyJSON);
	
	var options = {
	  host: "functions841def78.azurewebsites.net",
	  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
	  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
	  method: "POST",
	  headers : {
	      "Content-Type":"application/json",
	      "Content-Length": Buffer.byteLength(bodyString)
	    }    
	};
	
	callback = function(response) {
	  var str = ""
	  response.on("data", function (chunk) {
	    str += chunk;
	  });
	
	  response.on("end", function () {
	    console.log(str);
	  });
	}
	
	var req = http.request(options, callback);
	console.log("*** Sending name and address in body ***");
	console.log(bodyString);
	req.end(bodyString);



Output:

	C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
	*** Sending name and address in body ***
	{"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
	Hello Wes testing with Node.JS code
	The address you provided is Dallas, T.X. 75201
		
Nella finestra **Log** del portale viene registrato un output simile al seguente durante l'esecuzione della funzione:

	2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
	2016-03-23T08:09:01.153 Node.js HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
	2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
	2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
	2016-03-23T08:09:01.153 Request Body Type = object
	2016-03-23T08:09:01.153 Request Body = [object Object]
	2016-03-23T08:09:01.153 Processing User Information...
	2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    

### Eseguire il test di una funzione trigger della coda con il codice C# #

Si è accennato in precedenza che è possibile testare un trigger della coda tramite codice per inserire un messaggio nella coda. L'esempio di codice seguente si basa sul codice C# illustrato nell'esercitazione [Introduzione all'archiviazione code di Azure con .NET](../storage/storage-dotnet-how-to-use-queues.md). Da questo collegamento è disponibile il codice anche per altri linguaggi.

Per testare il codice in un'app console è necessario:

- [Configurare la stringa di connessione di archiviazione](../storage/storage-dotnet-how-to-use-queues.md#setup-a-storage-connection-string).
- Questo codice accetta il nome e l'indirizzo per un nuovo utente come argomenti della riga di comando in fase di esecuzione. Passare `name` e `address` come parametri per l'app. Ad esempio: `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`


Codice C# di esempio:

    static void Main(string[] args)
    {
        string name = null;
        string address = null;
        string queueName = "queue-newusers";
        string JSON = null;

        if (args.Length > 0)
        {
            name = args[0];
        }
        if (args.Length > 1)
        {
            address = args[1];
        }

        // Retrieve storage account from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

        // Create the queue client
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

        // Retrieve a reference to a queue
        CloudQueue queue = queueClient.GetQueueReference(queueName);

        // Create the queue if it doesn't already exist
        queue.CreateIfNotExists();

        // Create a message and add it to the queue.
        if (name != null)
        {
            if (address != null)
                JSON = String.Format("{{"name":"{0}","address":"{1}"}}", name, address);
            else
                JSON = String.Format("{{"name":"{0}"}}", name);
        }

        Console.WriteLine("Adding message to " + queueName + "...");
        Console.WriteLine(JSON);

        CloudQueueMessage message = new CloudQueueMessage(JSON);
        queue.AddMessage(message);
    }

Nella finestra del browser per la funzione coda viene visualizzato ogni messaggio in fase di elaborazione:

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->  

[portale di Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0824_2016-->