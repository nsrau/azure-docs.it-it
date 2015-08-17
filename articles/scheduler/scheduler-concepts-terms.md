<properties
 pageTitle="Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article" 
 ms.date="08/04/2015"
 ms.author="krisragh"/>

# Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione

## Gerarchia di entità dell'Utilità di pianificazione

Nella tabella seguente vengono descritte le risorse principali esposte o usate dall'API dell'Utilità di pianificazione:

|Risorsa | Descrizione |
|---|---|
|**Servizio cloud**|Concettualmente, un servizio cloud rappresenta un'applicazione. Una sottoscrizione può includere diversi servizi cloud.|
|**Raccolta di processi**|Una raccolta di processi contiene un gruppo di processi e gestisce le impostazioni, le quote e le limitazioni condivise dai processi all'interno della raccolta. Le raccolte di processi vengono create dal proprietario della sottoscrizione e raggruppano i processi in base ai limiti di utilizzo o di applicazione. È vincolata a un'area. Consente inoltre di applicare quote per vincolare l'uso di tutti i processi in tale raccolta; le quote includono MaxJobs e MaxRecurrence.|
|**Processo**|Un processo definisce una singola azione ricorrente, con strategie semplici o complesse per l'esecuzione. Le azioni possono includere richieste HTTP o code di archiviazione.|
|**Cronologia processi**|Una cronologia processi rappresenta i dettagli per l'esecuzione di un processo. Contiene esito positivo o negativo, nonché i dettagli della risposta.|

## Gestione di entità dell'utilità di pianificazione

A un livello elevato, l'utilità di pianificazione e l'API di gestione servizio espongono le seguenti operazioni sulle risorse:

|Funzionalità|Descrizione e indirizzo URI|
|---|---|
|**Gestione dei servizi cloud**|Supporto di GET, PUT e DELETE per la creazione e modifica di servizi cloud <p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}`</p>|
|**Gestione delle raccolte di processi**|Supporto di GET, PUT e DELETE per la creazione e la modifica di raccolte di processi e i processi in essi contenuti. Una raccolta di processi è un contenitore per i processi, e mappa quote e impostazioni condivise. Esempi di quote, descritti più avanti, sono il numero massimo di processi e l'intervallo minimo delle ricorrenze <p>PUT e DELETE: `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/jobcollections/{jobCollectionName}`</p><p>GET: `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}`</p>
|**Gestione dei processi**|Supporto di GET, PUT, POST, PATCH e DELETE per la creazione e modifica di processi. Tutti i processi devono appartenere a una raccolta di processi che esiste già, e che non viene creata implicitamente <p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}`</p>|
|**Gestione della cronologia dei processi**|Supporto GET per il recupero di 60 giorni di cronologia di esecuzioni del processo, ad esempio il tempo di esecuzione del processo e i risultati dell'esecuzione del processo. Aggiunge il supporto del parametro della stringa di query per filtrare in base a stato e status <P>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}/history`</p>|

## Tipi di processo

Esistono due tipi di processi: processi HTTP (inclusi i processi HTTPS che supportano SSL) e i processi sulle code di archiviazione. I processi HTTP sono ideali se si dispone di un endpoint di un carico di lavoro o di un servizio esistente. I processi sulle code di archiviazione consentono di inviare messaggi a code di archiviazione, per cui sono processi ideali per i carichi di lavoro che usano le code di archiviazione.

## L'entità "processo" in dettaglio

A livello di base, un processo pianificato ha diverse parti:

1.  L'azione da eseguire quando viene si attiva il timer del processo  

2.  (Facoltativo) Il tempo necessario per eseguire il processo

3.  (Facoltativo) Quando e quanto spesso ripetere il processo

4.  (Facoltativo) Un'azione da attivare in caso di esito negativo dell'azione primaria

Internamente, un processo pianificato contiene inoltre dati forniti dal sistema, ad esempio il momento di esecuzione pianificata successivo.

Di seguito è riportato un esempio esaustivo di un'utilità di pianificazione. I dettagli vengono forniti nelle sezioni a seguire.

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

1.  Ora di inizio ("startTime")  

2.  Azione ("action"), che include l'azione in caso di errore ("errorAction")

3.  Ricorrenza ("recurrence")

4.  Stato (“state”)

5.  Status (“status”)

6.  Criterio di ripetizione (“retryPolicy”)

Esaminiamo ciascuna in modo dettagliato:

## startTime

"startTime" è l'ora di inizio e consente al chiamante di specificare una differenza di fuso orario in transito in [formato ISO-8601](http://en.wikipedia.org/wiki/ISO_8601).

## action ed errorAction

"action" è l'azione richiamata a ogni occorrenza, e descrive un tipo di chiamata di servizio. L'azione è ciò che verrà eseguito nella pianificazione specificata. L'utilità di pianificazione supporta attualmente azioni HTTP e sulla coda di archiviazione.

L'azione nell'esempio precedente è un'azione http. Di seguito è riportato un esempio di un'azione in coda di archiviazione:

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

"errorAction" è il gestore degli errori, l'azione viene richiamata quando l'azione principale ha esito negativo. È possibile usare questa variabile per chiamare un endpoint di gestione degli errori o per inviare una notifica all'utente. Ciò può essere usato per raggiungere un endpoint secondario in caso quello primario non sia disponibile (ad esempio, in caso di emergenza nel sito dell'endpoint) o può essere usato per notificare un endpoint che si occupi di gestire gli errori. Proprio come l'azione principale, l'azione di errore può essere semplice o a logica composita basata su altre azioni. Per informazione su come creare un token SAS, fare riferimento a [Creare e utilizzare una firma di accesso condiviso](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## ricorrenza

La ricorrenza ha diverse parti:

1.  Frequenza: Un valore tra minuto, ora, giorno, settimana, mese, anno  

2.  Intervallo: Intervallo alla frequenza specificata per la ricorrenza

3.  Pianificazione prescritta: Specifica minuti, ore, giorni della settimana, mesi e giorni del mese della ricorrenza

4.  Conteggio: Conteggio delle occorrenze

5.  Ora di fine: Nessun processo verrà eseguito dopo l'ora di fine specificata

Un processo è ricorrente se dispone di un oggetto ricorrente specificato nella sua definizione JSON. Se sono specificati sia conteggio che l'ora di fine, viene applicata la regola di completamento che si verifica per primo.

## state

Lo stato del processo è uno di questi quattro valori: abilitato, disabilitato, completato o in errore. È possibile eseguire azioni PUT o PATCH sui processi per aggiornarli allo stato abilitato o disabilitato. Se un processo è stato completato o in errore, si trova in uno stato finale che non può essere aggiornato (anche se è comunque possibile eliminare il processo). Un esempio della proprietà stato è come segue:


    	"state": "disabled", // enabled, disabled, completed, or faulted
I processi completati e con errori vengono eliminati dopo 60 giorni.

## status

Dopo l'avvio dell'Utilità di pianificazione, verranno restituite informazioni sullo stato corrente del processo. Questo oggetto non può essere impostato dall'utente – è impostato dal sistema. Tuttavia, è incluso nell'oggetto processo (anziché in una risorsa collegata separata) in modo da consentire di ottenere semplicemente lo stato di un processo.

Lo stato del processo include l'ora dell'esecuzione precedente (se presente), l'ora della successiva esecuzione pianificata (per i processi in corso) e il conteggio delle esecuzioni del processo.

## retryPolicy

Se un'utilità di pianificazione non riesce, è possibile specificare un criterio di ripetizione per determinare se e come l'azione verrà ripetuta. Ciò è determinato dall'oggetto **retryType** – è impostato su **nessuno** se non esiste alcun criterio di ripetizione, come illustrato in precedenza. Impostato su **fisso** se esiste un criterio di ripetizione.

Per impostare un criterio di ripetizione, è possibile specificare due impostazioni aggiuntive: un intervallo tra i tentativi (**retryInterval**) e il numero di tentativi (**retryCount**).

L'intervallo tra tentativi, specificato con l'oggetto **retryInterval**, è l'intervallo di tempo tra i tentativi. Il valore predefinito è di 1 minuto, il valore minimo è di 1 minuto, e il valore massimo è di 18 mesi. Viene definito nel formato ISO 8601. Analogamente, il valore del numero di tentativi è specificato con l'oggetto **retryCount** e specifica quanti tentativi verranno eseguiti. Il valore predefinito è 5 e il valore massimo è 20. Sia **retryInterval** che **retryCount** sono facoltativi. A questi oggetti vengono assegnati i valori predefiniti se **retryType** è impostato su **fixed** e non sono specificati valori in modo esplicito.

## Vedere anche

 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Introduzione all'uso dell'Utilità di pianificazione nel portale di gestione](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'Utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/dn528946)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione](scheduler-outbound-authentication.md)
 

<!---HONumber=August15_HO6-->