<properties 
	pageTitle="API di tag dell'SDK Windows Store per Azure Mobile Engagement" 
	description="Ultimi aggiornamenti e procedure per l'SDK Windows Store per Azure Mobile Egagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Come utilizzare l'API Engagement in Windows 

Questo documento è un componente aggiuntivo al documento [Come integrare Engagement in Windows](mobile-engagement-windows-store-integrate-engagement.md): fornisce informazioni approfondite su come utilizzare l'API Engagement per segnalare le statistiche dell'applicazione.

Tenere presente che se si desidera che Engagement segnali solo sessioni, attività, arresti anomali e informazioni tecniche dell'applicazione, il modo più semplice consiste nel far sì che tutte le sottoclassi  `Page` ereditino dalla classe  `EngagementPage`.

Se si desidera effettuare ulteriori operazioni, ad esempio, se si desidera segnalare eventi, errori e processi specifici dell'applicazione o se si desidera segnalare le attività dell'applicazione in un modo differente rispetto a quello implementato nelle classi 
 `EngagementPage`, è necessario utilizzare l'API Engagement.

L'API Engagement viene fornita dalla classe  `EngagementAgent`. È possibile accedere a questi metodi tramite  `EngagementAgent.Instance`.

Anche se il modulo dell'agente non è stato inizializzato, ogni chiamata all'API viene posticipata e verrà eseguita nuovamente quando l'agente è disponibile.

##Concetti di Engagement

Le parti seguenti approfondiscono i [Concetti di Mobile Engagemet](mobile-engagement-concepts.md) per la piattaforma Windows.

### `Sessione` e  `Attività`

Un' *attività* è generalmente associata a una pagina dell'applicazione, vale a dire che l' *attività* inizia quando la pagina viene visualizzata e termina quando la pagina viene chiusa: è ciò che accade quando l'SDK Engagement viene integrato tramite la classe  `EngagementPage`.

Le  *attività*, però, possono essere controllate anche manualmente utilizzando l'API Engagement. Ciò consente di dividere una determinata pagina in più sottoparti per ottenere maggiori dettagli sull'utilizzo della pagina (ad esempio per conoscere la frequenza e la durata dell'uso delle finestre di dialogo all'interno della pagina).

##Segnalazione di attività

### L'utente inizia una nuova attività

#### Riferimenti

			void StartActivity(string name, Dictionary<object, object> extras = null)

È necessario chiamare  `StartActivity()` ogni volta che l'attività dell'utente cambia. La prima chiamata a questa funzione avvia una nuova sessione utente.

> [AZURE.TIP] Non è necessario chiamare  `EndActivity()` dopo ogni chiamata a  `StartActivity()`.

#### Esempio

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### L'utente termina la sua attività corrente

#### Riferimenti

			void EndActivity()

Vengono terminate l'attività e la sessione. Chiamare questo metodo solo se si è perfettamente consapevoli delle conseguenze.

#### Esempio

			EngagementAgent.Instance.EndActivity();

##Segnalazione di processi

### Avviare un processo

#### Riferimenti

			void StartJob(string name, Dictionary<object, object> extras = null)

È possibile utilizzare il processo per tenere traccia delle attività in un determinato periodo di tempo.

#### Esempio

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### Terminare un processo

#### Riferimenti

			void EndJob(string name)

Appena terminata un'attività di cui un processo tiene traccia, è necessario chiamare il metodo EndJob per questo processo, fornendo il nome del processo.

#### Esempio

			// In the previous section, we started an upload Rilevamento con un processo 
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Segnalazione di eventi

Esistono tre tipi di eventi:

- Eventi autonomi
- Eventi di sessione
- Eventi di processo

### Eventi autonomi

#### Riferimenti

			void SendEvent(string name, Dictionary<object, object> extras = null)

Gli eventi autonomi possono verificarsi all'esterno del contesto di una sessione.

#### Esempio

			EngagementAgent.Instance.SendEvent("event", extra);

### Eventi di sessione

#### Riferimenti

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Gli eventi di sessione in genere vengono utilizzati per segnalare le azioni eseguite da un utente durante la sessione.

#### Esempio

**Senza dati:**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Con i dati:**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### Eventi di processo

#### Riferimenti

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Gli eventi di processo in genere vengono utilizzati per segnalare le azioni eseguite da un utente durante un processo.

#### Esempio

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##Segnalazione di errori

Esistono tre tipi di errori:

- Errori autonomi
- Errori di sessione
- Errori di processo

### Errori autonomi

#### Riferimenti

			void SendError(string name, Dictionary<object, object> extras = null)

Contrariamente agli errori di sessione, gli errori autonomi possono verificarsi all'esterno del contesto di una sessione.

#### Esempio

			EngagementAgent.Instance.SendError("errorName", extras);

### Errori di sessione

#### Riferimenti

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Gli errori di sessione in genere vengono utilizzati per segnalare gli errori che hanno un impatto sull'utente durante la sessione.

#### Esempio

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Errori di processo

#### Riferimenti

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Gli errori possono essere correlati a un processo in esecuzione anziché alla sessione utente corrente.

#### Esempio

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Segnalazione di arresti anomali

L'agente fornisce due metodi per gestire gli arresti anomali.

### Inviare un'eccezione

#### Riferimenti

			void SendCrash(Exception e, bool terminateSession = false)

#### Esempio

È possibile inviare un'eccezione in qualsiasi momento chiamando:

			EngagementAgent.Instance.SendCrash(aCatchedException);

È inoltre possibile utilizzare un parametro facoltativo per terminare la sessione di Engagement contemporaneamente all'invio dell'arresto anomalo. A tale scopo, chiamare:

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

In questo caso la sessione e i processi verranno chiusi solo dopo l'invio dell'arresto anomalo.

### Inviare un'eccezione non gestita

#### Riferimenti

			void SendCrash(Exception e)

Engagement fornisce anche un metodo per inviare le eccezioni non gestite, se è stata **DISABILITATA** la segnalazione automatica degli **arresti anomali ** in Engagement. Questa possibilità è particolarmente utile se utilizzata all'interno del gestore eventi UnhandledException.

Questo metodo terminerà **SEMPRE** la sessione di Engagement e i processi dopo essere stato chiamato.

#### Esempio

Consente di implementare un proprio gestore UnhandledExceptionEventArgs. Ad esempio, aggiungere il metodo  `Current_UnhandledException` del file  `App.xaml.cs`:

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
			{
			 EngagementAgent.Instance.SendCrash(e.Exception,false);
			}

In App.xaml.cs in "Public App(){}" aggiungere:

			Application.Current.UnhandledException += Current_UnhandledException;

##Id dispositivo

			String EngagementAgent.Instance.GetDeviceId()

È possibile ottenere l'id del dispositivo Engagement chiamando questo metodo.

##Parametri supplementari

Dati arbitrari possono essere collegati a un evento, un errore, un'attività o un processo. Tali dati possono essere strutturati utilizzando un dizionario. Chiavi e valori possono essere di qualsiasi tipo.

I dati supplementari vengono serializzati, per cui se si desidera inserirvi il proprio tipo è necessario aggiungere un contratto dati per questo tipo.

### Esempio

Si crea una nuova classe "Person".

			using System.Runtime.Serialization;
			
			namespace Microsoft.Azure.Engagement
			{
			 [DataContract]
			 public class Person
			 {
			 public Person(string name, int age)
			 {
			 Age = age;
			 Name = name;
			 }
			
			 // Properties
			
			 [DataMember]
			 public int Age
			 {
			 get;
			 set;
			 }
			
			 [DataMember]
			 public string Name
			 {
			 get;
			 set; 
			 }
			 }
			}

Quindi, si aggiunge un'istanza di  `Person` per un dato supplementare.

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Se si inseriscono altri tipi di oggetti, assicurarsi che il relativo metodo ToString() venga implementato per restituire una stringa leggibile.

### Limiti

#### Chiavi

Ogni chiave dell'oggetto deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*$`

In altre parole, le chiavi devono iniziare con almeno una lettera seguita da lettere, cifre o caratteri di sottolineatura (\).

#### Dimensioni

I dati supplementari sono limitati a **1024** caratteri per ogni chiamata.

##Segnalazione di informazioni sull'applicazione

### Riferimenti

			void SendAppInfo(Dictionary<object, object> appInfos)

È possibile segnalare manualmente le informazioni di traccia o qualsiasi altra informazione specifica dell'applicazione mediante la funzione SendAppInfo().

Si noti che queste informazioni possono essere inviate in modo incrementale: verrà mantenuto solo l'ultimo valore per una determinata chiave per un determinato dispositivo. Come dati supplementari di eventi, utilizzare Dictionary\ <oggetto,oggetto\ > per allegare informazioni.

### Esempio

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			 {
			 {"birthdate", "1983-12-07"},
			 {"gender", "female"}
			 };
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### Limiti

#### Chiavi

Ogni chiave dell'oggetto deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*$`

In altre parole, le chiavi devono iniziare con almeno una lettera seguita da lettere, cifre o caratteri di sottolineatura (\).

#### Dimensioni

Le informazioni sull'applicazione sono limitate a **1024** caratteri per ogni chiamata.

Nell'esempio precedente, il JSON inviato al server è lungo 44 caratteri:

			{"birthdate":"1983-12-07","gender":"female"}

<!--HONumber=47-->
