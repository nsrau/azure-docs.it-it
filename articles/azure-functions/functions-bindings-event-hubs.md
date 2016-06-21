<properties
	pageTitle="Associazioni di Hub eventi di Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come usare le associazioni di Hub eventi di Azure in Funzioni di Azure."
	services="functions"
	documentationCenter="na"
	authors="wesmc7777"
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
	ms.date="05/27/2016"
	ms.author="wesmc"/>

# Associazioni di Hub eventi di Funzioni di Azure

Questo articolo illustra come configurare e scrivere il codice di associazioni di [Hub eventi di Azure](../event-hubs/event-hubs-overview.md) in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per Hub eventi di Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## Associazione del trigger di Hub eventi di Azure

Il trigger di Hub eventi di Azure può essere usato per rispondere a un evento inviato a un flusso di eventi di Hub eventi. Per configurare un'associazione del trigger è necessario avere accesso in lettura ad Hub eventi.

#### function.json per l'associazione del trigger di Hub eventi

Il file *function.json* per un trigger di Hub eventi di Azure specifica le proprietà seguenti:

- `type`: deve essere impostato su *eventHubTrigger*.
- `name`: nome della variabile usato nel codice della funzione per il messaggio di Hub eventi. 
- `direction`: deve essere impostato su *in*. 
- `path`: nome di Hub eventi.
- `connection` : nome di un'impostazione dell'applicazione che contiene la stringa di connessione nello spazio dei nomi in cui si trova Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo spazio dei nomi, non per lo stesso Hub eventi. Per attivare il trigger, questa stringa di connessione deve disporre almeno delle autorizzazioni Read.

		{
		  "bindings": [
		    {
		      "type": "eventHubTrigger",
		      "name": "myEventHubMessage",
		      "direction": "in",
		      "path": "MyEventHub",
		      "connection": "myEventHubReadConnectionString"
		    }
		  ],
		  "disabled": false
		}

#### Esempio C# del trigger di Hub eventi di Azure
 
Usando il file function.json di esempio riportato in alto, il corpo del messaggio dell'evento viene registrato usando il codice di funzione C# seguente:
 
	using System;
	
	public static void Run(string myEventHubMessage, TraceWriter log)
	{
	    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
	}

#### Esempio Node.js del trigger di Hub eventi di Azure
 
Usando il file function.json di esempio riportato in alto, il corpo del messaggio dell'evento viene registrato usando il codice di funzione Node.js seguente:
 
	module.exports = function (context, myEventHubMessage) {
	    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);	
	    context.done();
	};


## Associazione di output di Hub eventi di Azure

L'associazione di output di Hub eventi di Azure viene usata per scrivere eventi in un flusso di eventi di Hub eventi. Per scrivervi eventi, è necessario disporre dell'autorizzazione Send verso un Hub eventi.

#### function.json per l'associazione di output di Hub eventi

Il file *function.json* per un'associazione di output di Hub eventi di Azure specifica le proprietà seguenti:

- `type`: deve essere impostato su *eventHub*.
- `name`: nome della variabile usato nel codice della funzione per il messaggio di Hub eventi. 
- `path`: nome di Hub eventi.
- `connection` : nome di un'impostazione dell'applicazione che contiene la stringa di connessione nello spazio dei nomi in cui si trova Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo spazio dei nomi, non per lo stesso Hub eventi. Per inviare il messaggio al flusso di Hub eventi, questa stringa di connessione deve disporre di autorizzazioni Send.
- `direction`: deve essere impostato su *out*. 

	    {
	      "type": "eventHub",
	      "name": "outputEventHubMessage",
	      "path": "myeventhub",
	      "connection": "MyEventHubSend",
	      "direction": "out"
	    }


#### Esempio di codice C# di Hub eventi di Azure per l'associazione di output
 
Il codice di funzione C# di esempio seguente illustra la scrittura di un evento in un flusso di eventi di Hub eventi. Questo esempio rappresenta l'applicazione a un trigger del timer C# dell'associazione di output di Hub eventi illustrata in precedenza.
 
	using System;
	
	public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
	{
	    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
	
	    log.Verbose(msg);   
	    
	    outputEventHubMessage = msg;
	}

#### Esempio di codice Node.js di Hub eventi di Azure per l'associazione di output
 
Il codice di funzione Node.js di esempio seguente illustra la scrittura di un evento in un flusso di eventi di Hub eventi. Questo esempio rappresenta l'applicazione a un trigger del timer Node.js dell'associazione di output di Hub eventi illustrata in precedenza.
 
	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	    
	    if(myTimer.isPastDue)
	    {
	        context.log('TimerTriggerNodeJS1 is running late!');
	    }

	    context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
	    
	    context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
	
	    context.done();
	};

## Passaggi successivi

[AZURE.INCLUDE [Passaggi successivi](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0608_2016-->