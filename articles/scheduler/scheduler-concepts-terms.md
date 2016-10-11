<properties
 pageTitle="Entità, termini e concetti dell'Utilità di pianificazione | Microsoft Azure"
 description="Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure, inclusi processi e raccolte di processi. Fornisce un esempio completo di un processo pianificato."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione

## Gerarchia di entità dell'Utilità di pianificazione

Nella tabella seguente vengono descritte le risorse principali esposte o usate dall'API dell'Utilità di pianificazione:

|Risorsa | Descrizione |
|---|---|
|**Raccolta di processi**|Una raccolta di processi contiene un gruppo di processi e gestisce le impostazioni, le quote e le limitazioni condivise dai processi all'interno della raccolta. Le raccolte di processi vengono create dal proprietario della sottoscrizione e raggruppano i processi in base ai limiti di utilizzo o dell'applicazione. È vincolata a un'area. Consente inoltre di applicare quote per vincolare l'uso di tutti i processi in tale raccolta. Le quote includono MaxJobs e MaxRecurrence.|
|**Processo**|Un processo definisce una singola azione ricorrente, con strategie semplici o complesse per l'esecuzione. Le azioni possono includere HTTP, coda di archiviazione, coda del bus di servizio o richieste di argomento del bus di servizio.|
|**Cronologia processi**|Una cronologia processi rappresenta i dettagli per l'esecuzione di un processo. Contiene esito positivo o negativo, nonché i dettagli della risposta.|

## Gestione di entità dell'utilità di pianificazione

A un livello elevato, l'utilità di pianificazione e l'API di gestione servizio espongono le seguenti operazioni sulle risorse:

|Funzionalità|Descrizione e indirizzo URI|
|---|---|
|**Gestione delle raccolte di processi**|Supporto di GET, PUT e DELETE per la creazione e la modifica di raccolte di processi e i processi in essi contenuti. Una raccolta di processi è un contenitore per i processi, e mappa quote e impostazioni condivise. Esempi di quote, descritti più avanti, sono il numero massimo di processi e l'intervallo minimo delle ricorrenze. <p>PUT e DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Gestione dei processi**|Supporto di GET, PUT, POST, PATCH e DELETE per la creazione e modifica di processi. Tutti i processi devono appartenere a una raccolta di processi che esiste già, e che non viene creata implicitamente. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Gestione della cronologia dei processi**|Supporto GET per il recupero di 60 giorni di cronologia di esecuzioni del processo, ad esempio il tempo di esecuzione del processo e i risultati dell'esecuzione del processo. Aggiunge il supporto del parametro della stringa di query per filtrare in base a stato e status. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## Tipi di processo

Sono disponibili più tipi di processi: processi HTTP, inclusi quelli che supportano SSL, processi della coda di archiviazione, processi della coda del bus di servizio e processi dell'argomento del bus di servizio. I processi HTTP sono ideali se si dispone di un endpoint di un carico di lavoro o di un servizio esistente. I processi sulle code di archiviazione consentono di inviare messaggi a code di archiviazione, per cui sono processi ideali per i carichi di lavoro che usano le code di archiviazione. Analogamente, i processi del bus di servizio sono ideali per carichi di lavoro che usano argomenti e code del bus di servizio.

## L'entità "processo" in dettaglio

A livello di base, un processo pianificato ha diverse parti:

- L'azione da eseguire quando viene si attiva il timer del processo

- (Facoltativo) Il tempo necessario per eseguire il processo

- (Facoltativo) Quando e quanto spesso ripetere il processo

- (Facoltativo) Un'azione da attivare in caso di esito negativo dell'azione primaria

Internamente, un processo pianificato contiene inoltre dati forniti dal sistema, ad esempio il momento di esecuzione pianificata successivo.

Il codice seguente fornisce un esempio completo di un processo pianificato. I dettagli vengono forniti nelle sezioni a seguire.

	{
		"startTime": "2012-08-04T00:00Z",               // optional
		"action":
		{
			"type": "http",
			"retryPolicy": { "retryType":"none" },
			"request":
			{
				"uri": "http://contoso.com/foo",        // required
				"method": "PUT",                        // required
				"body": "Posting from a timer",         // optional
				"headers":                              // optional

				{
					"Content-Type": "application/json"
				},
			},
		   "errorAction":
		   {
			   "type": "http",
			   "request":
			   {
				   "uri": "http://contoso.com/notifyError",
				   "method": "POST",
			   },
		   },
		},
		"recurrence":                                   // optional
		{
			"frequency": "week",                        // can be "year" "month" "day" "week" "minute"
			"interval": 1,                              // optional, how often to fire (default to 1)
			"schedule":                                 // optional (advanced scheduling specifics)
			{
				"weekDays": ["monday", "wednesday", "friday"],
				"hours": [10, 22]
			},
			"count": 10,                                 // optional (default to recur infinitely)
			"endTime": "2012-11-04",                     // optional (default to recur infinitely)
		},
		"state": "disabled",                           // enabled or disabled
		"status":                                       // controlled by Scheduler service
		{
			"lastExecutionTime": "2007-03-01T13:00:00Z",
			"nextExecutionTime": "2007-03-01T14:00:00Z ",
			"executionCount": 3,
											    "failureCount": 0,
												"faultedCount": 0
		},
	}

Come illustrato nell'utilità di pianificazione di esempio sopra riportato, la definizione di un processo ha diverse parti:

- Ora di inizio ("startTime")

- Azione ("action"), che include l'azione in caso di errore ("errorAction")

- Ricorrenza ("recurrence")

- Stato (“state”)

- Status (“status”)

- Criterio di ripetizione (“retryPolicy”)

Esaminiamo ciascuna in modo dettagliato:

## startTime

"startTime" è l'ora di inizio e consente al chiamante di specificare una differenza di fuso orario in transito in [formato ISO-8601](http://en.wikipedia.org/wiki/ISO_8601).

## action ed errorAction

"action" è l'azione richiamata a ogni occorrenza, e descrive un tipo di chiamata di servizio. L'azione è ciò che verrà eseguito nella pianificazione specificata. L'Utilità di pianificazione supporta azioni della coda del bus di servizio, dell'argomento del bus di servizio, della coda di archiviazione e HTTP.

L'azione nell'esempio precedente è un'azione HTTP. Di seguito è riportato un esempio di un'azione in coda di archiviazione:

	{
			"type": "storageQueue",
			"queueMessage":
			{
				"storageAccount": "myStorageAccount",  // required
				"queueName": "myqueue",                // required
				"sasToken": "TOKEN",                   // required
				"message":                             // required
					"My message body",
			},
	}

Di seguito è riportato un esempio di azione dell'argomento del bus di servizio.

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1", "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Di seguito è riportato un esempio di azione di coda del bus di servizio.


  "action": { "serviceBusQueueMessage": { "queueName": "q1", "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

"errorAction" è il gestore degli errori, l'azione viene richiamata quando l'azione principale ha esito negativo. È possibile usare questa variabile per chiamare un endpoint di gestione degli errori o per inviare una notifica all'utente. Ciò può essere usato per raggiungere un endpoint secondario in caso quello primario non sia disponibile (ad esempio, in caso di emergenza nel sito dell'endpoint) o può essere usato per notificare un endpoint che si occupi di gestire gli errori. Proprio come l'azione principale, l'azione di errore può essere semplice o a logica composita basata su altre azioni. Per informazione su come creare un token SAS, fare riferimento a [Creare e utilizzare una firma di accesso condiviso](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## ricorrenza

La ricorrenza ha diverse parti:

- Frequenza: Un valore tra minuto, ora, giorno, settimana, mese, anno

- Intervallo: Intervallo alla frequenza specificata per la ricorrenza

- Pianificazione prescritta: Specifica minuti, ore, giorni della settimana, mesi e giorni del mese della ricorrenza

- Conteggio: Conteggio delle occorrenze

- Ora di fine: Nessun processo verrà eseguito dopo l'ora di fine specificata

Un processo è ricorrente se dispone di un oggetto ricorrente specificato nella sua definizione JSON. Se sono specificati sia conteggio che l'ora di fine, viene applicata la regola di completamento che si verifica per primo.

## state

Lo stato del processo è uno di questi quattro valori: abilitato, disabilitato, completato o in errore. È possibile eseguire azioni PUT o PATCH sui processi per aggiornarli allo stato abilitato o disabilitato. Se un processo è stato completato o in errore, si trova in uno stato finale che non può essere aggiornato (anche se è comunque possibile eliminare il processo). Un esempio della proprietà stato è come segue:


    	"state": "disabled", // enabled, disabled, completed, or faulted
I processi completati e con errori vengono eliminati dopo 60 giorni.

## status

Dopo l'avvio dell'Utilità di pianificazione, verranno restituite informazioni sullo stato corrente del processo. Questo oggetto non può essere impostato dall'utente: è impostato dal sistema. Tuttavia, è incluso nell'oggetto processo (anziché in una risorsa collegata separata) in modo da consentire di ottenere semplicemente lo stato di un processo.

Lo stato del processo include l'ora dell'esecuzione precedente (se presente), l'ora della successiva esecuzione pianificata (per i processi in corso) e il conteggio delle esecuzioni del processo.

## retryPolicy

Se un'utilità di pianificazione non riesce, è possibile specificare un criterio di ripetizione per determinare se e come l'azione verrà ripetuta. Ciò è determinato dall'oggetto **retryType**: è impostato su **nessuno** se non esiste alcun criterio di ripetizione, come illustrato in precedenza. Impostato su **fisso** se esiste un criterio di ripetizione.

Per impostare un criterio di ripetizione, è possibile specificare due impostazioni aggiuntive: un intervallo tra i tentativi (**retryInterval**) e il numero di tentativi (**retryCount**).

L'intervallo tra tentativi, specificato con l'oggetto **retryInterval**, è l'intervallo di tempo tra i tentativi. Il valore predefinito è 30 secondi, il valore minimo configurabile è 15 secondi e il valore massimo è 18 mesi. I processi inclusi nelle raccolte di processi del livello Gratuito hanno un valore minimo configurabile di 1 ora. Viene definito nel formato ISO 8601. Analogamente, il valore del numero di tentativi è specificato con l'oggetto **retryCount** e specifica quanti tentativi verranno eseguiti. Il valore predefinito è 4 e il valore massimo è 20. Sia **retryInterval** che **retryCount** sono facoltativi. A questi oggetti vengono assegnati i valori predefiniti se **retryType** è impostato su **fixed** e non sono specificati valori in modo esplicito.

## Vedere anche

 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Introduzione all'uso dell'Utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_1005_2016-->