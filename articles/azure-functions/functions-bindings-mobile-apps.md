<properties
	pageTitle="Associazioni di app per dispositivi mobili in Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come usare le associazioni di app per dispositivi mobili in Funzioni di Azure"
	services="functions"
	documentationCenter="na"
	authors="ggailey777"
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
	ms.date="06/02/2016"
	ms.author="glenga"/>

# Associazioni di app per dispositivi mobili in Funzioni di Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice di associazioni di app per dispositivi mobili di Azure in Funzioni di Azure

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Le app per dispositivi mobili del servizio app di Azure consentono di esporre i dati degli endpoint tabella ai client per dispositivi mobili. Questi stessi dati tabulari possono essere usati con le associazioni sia di input che di output in Funzioni di Azure. Grazie al supporto dello schema dinamico, un'app per dispositivi mobili back-end Node.js è ideale per l'esposizione di dati tabulari da usare con le funzioni. Lo schema dinamico è abilitato per impostazione predefinita ed è consigliabile disabilitarlo in un'app per dispositivi mobili di produzione. Per altre informazioni sugli endpoint tabella in un back-end Node.js, vedere [Operazioni su tabella](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations). Nelle app per dispositivi mobili il back-end Node.js supporta l'esplorazione e la modifica di tabelle nel portale. Per altre informazioni, vedere la sezione relativa alla [modifica nel portale](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing) nell'argomento che illustra l'uso di Node.js SDK. Quando si usa un'app per dispositivi mobili del back-end .NET con le funzioni di Azure è necessario aggiornare manualmente il modello di dati come richiesto dalla funzione. Per altre informazioni sugli endpoint tabella in un'app per dispositivi mobili del back-end .NET, vedere [Procedura: Definire un controller tabelle](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) nell'argomento relativo all'SDK di un back-end .NET.

## Creare una variabile di ambiente per l'URL del back-end di app per dispositivi mobili

Le associazioni di app per dispositivi mobili richiedono attualmente la creazione di una variabile di ambiente che restituisca l'URL back-end della stessa app per dispositivi mobili. È possibile trovare l'URL nel [portale di Azure](https://portal.azure.com), individuando l'app per dispositivi mobili e aprendo il pannello.

![Pannello App per dispositivi mobili nel portale di Azure](./media/functions-bindings-mobile-apps/mobile-app-blade.png)

Per impostare questo URL come variabile di ambiente nell'app per le funzioni:

1. Nell'app per le funzioni nel [portale Funzioni di Azure](https://functions.azure.com/signin) fare clic su **Impostazioni dell'app per le funzioni** > **Passa a Impostazioni del servizio app**.

	![Pannello Impostazioni dell'app per le funzioni](./media/functions-bindings-mobile-apps/functions-app-service-settings.png)

2. Nell'app per le funzioni fare clic su **Tutte le impostazioni**, scorrere verso il basso fino a **Impostazioni dell'applicazione**, quindi in **Impostazioni app** digitare un nuovo **Nome** per la variabile di ambiente, incollare l'URL nel campo **Valore** assicurandosi di usare lo schema HTTPS, quindi fare clic su **Salva** e chiudere il pannello dell'app per le funzioni per tornare al portale Funzioni.

	![Aggiungere una variabile di ambiente come impostazione dell'app](./media/functions-bindings-mobile-apps/functions-app-add-app-setting.png)  

È ora possibile impostare la nuova variabile di ambiente come campo *connection* delle associazioni.

## <a id="mobiletablesapikey"></a> Usare una chiave API per proteggere l'accesso agli endpoint tabella delle app per dispositivi mobili.

In Funzioni di Azure le associazioni delle tabelle delle app per dispositivi mobili consentono di specificare una chiave API, ovvero un segreto condiviso che può essere usato per impedire l'accesso indesiderato ad app diverse dalle proprie funzioni. Le app per dispositivi mobili non hanno il supporto predefinito per l'autenticazione con la chiave API. È tuttavia possibile implementare una chiave API nell'app per dispositivi mobili del back-end Node.js seguendo gli esempi nell'articolo [Azure App Service Mobile Apps backend implementing an API key](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) (Back-end dell'app per dispositivi mobili del servizio app di Azure che implementa una chiave API). È possibile implementare analogamente una chiave API in un'[app per dispositivi mobili back-end .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key).

>[AZURE.IMPORTANT] Questa chiave API non deve essere distribuita con i client app per dispositivi mobili, ma è consigliabile distribuirla in modo sicuro solo ai client lato servizio, ad esempio Funzioni di Azure.

## <a id="mobiletablesinput"></a> Associazione di input di app per dispositivi mobili di Azure

Le associazioni di input possono caricare un record da un endpoint tabella per dispositivi mobili e passarlo direttamente all'associazione. L'ID record viene determinato in base al trigger che ha richiamato la funzione. In una funzione C# eventuali modifiche apportate al record vengono automaticamente inviate alla tabella quando la funzione termina correttamente.

#### function.json per associazione di input di app per dispositivi mobili

Il file *function.json* supporta le proprietà seguenti:

- `name`: nome della variabile usato nel codice della funzione per il nuovo record.
- `type`: il tipo di associazione deve essere impostato su *mobileTable*.
- `tableName`: tabella in cui verrà creato il nuovo record.
- `id`: ID del record da recuperare. Questa proprietà supporta associazioni simili a `{queueTrigger}` che useranno il valore stringa del messaggio della coda come ID record.
- `apiKey`: stringa corrispondente all'impostazione dell'applicazione che specifica la chiave API facoltativa per l'app per dispositivi mobili. È necessaria quando l'app per dispositivi mobili usa una chiave API per limitare l'accesso client.
- `connection`: stringa che rappresenta il nome della variabile di ambiente nelle impostazioni dell'applicazione che specifica l'URL del back-end dell'app per dispositivi mobili.
- `direction`: direzione dell'associazione, che deve essere impostata su *in*.

Esempio di file *function.json*:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Url",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Esempio di codice di app per dispositivi mobili di Azure per un trigger della coda C#

In base al file function.json di esempio precedente, l'associazione di input recupera da un endpoint tabella dell'app per dispositivi mobili il record con l'ID corrispondente alla stringa del messaggio della coda e lo passa al parametro *record*. Quando il record non viene trovato, il parametro è null. Il record viene quindi aggiornato con il nuovo valore *Text* quando la funzione termina.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Esempio di codice di app per dispositivi mobili di Azure per un trigger della coda Node.js

In base al file function.json di esempio precedente, l'associazione di input recupera da un endpoint tabella dell'app per dispositivi mobili il record con l'ID corrispondente alla stringa del messaggio della coda e lo passa al parametro *record*. Nelle funzioni Node.js i record aggiornati non vengono inviati alla tabella. Questo esempio di codice scrive il record recuperato nel log.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


## <a id="mobiletablesoutput"></a> Associazione di output di app per dispositivi mobili di Azure

La funzione può scrivere un record in un endpoint tabella delle app per dispositivi mobili usando un'associazione di output.

#### function.json per associazione di output di app per dispositivi mobili

Il file function.json supporta le proprietà seguenti:

- `name`: nome della variabile usato nel codice della funzione per il nuovo record.
- `type`: tipo di associazione che deve essere impostato su *mobileTable*.
- `tableName`: tabella in cui viene creato il nuovo record.
- `apiKey`: stringa corrispondente all'impostazione dell'applicazione che specifica la chiave API facoltativa per l'app per dispositivi mobili. È necessaria quando l'app per dispositivi mobili usa una chiave API per limitare l'accesso client.
- `connection`: stringa che rappresenta il nome della variabile di ambiente nelle impostazioni dell'applicazione che specifica l'URL del back-end dell'app per dispositivi mobili.
- `direction`: direzione dell'associazione, che deve essere impostata su *out*.

Function.json di esempio:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Url",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Esempio di codice di app per dispositivi mobili di Azure per un trigger della coda C#

Questo esempio di codice C# inserisce un nuovo record in un endpoint tabella dell'app per dispositivi mobili con una proprietà *Text* nella tabella specificata nell'associazione precedente.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Esempio di codice di app per dispositivi mobili di Azure per un trigger della coda Node.js

Questo esempio di codice Node.js inserisce un nuovo record in un endpoint tabella dell'app per dispositivi mobili con una proprietà *text* nella tabella specificata nell'associazione precedente.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Passaggi successivi

[AZURE.INCLUDE [Passaggi successivi](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->