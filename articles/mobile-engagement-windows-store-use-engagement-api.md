<properties 
	pageTitle="Come usare l'API di Engagement in un'app di Windows universale" 
	description="Come usare l'API di Engagement in un'app di Windows universale"			
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="piyushjo" />

#Come usare l'API di Engagement in un'app di Windows universale

Questo documento è complementare all'articolo [Come integrare Engagement in un'app di Windows universale](../mobile-engagement-windows-store-integrate-engagement/) e fornisce informazioni approfondite su come usare l'API di Engagement per segnalare le statistiche dell'applicazione.

Tenere presente che, se si vuole impostare Engagement in modo che segnali solo le sessioni, le attività, gli arresti anomali e i dati tecnici dell'applicazione, la soluzione più semplice consiste nel fare in modo che tutte le sottoclassi `Page` ereditino dalla classe `EngagementPage`.

Se invece si hanno esigenze più complesse, ad esempio se è necessario segnalare eventi, errori e processi specifici dell'applicazione o presentare le attività dell'applicazione in modo diverso rispetto a quello implementato nelle classi `EngagementPage`, è necessario usare l'API di Engagement.

L'API di Engagement viene fornita dalla classe `EngagementAgent`. È possibile accedere a questi metodi tramite `EngagementAgent.Instance`.

Anche se il modulo dell'agente non è stato inizializzato, ogni chiamata all'API viene posticipata e verrà eseguita nuovamente quando l'agente è disponibile.

##Concetti relativi a Mobile Engagement

Le parti seguenti approfondiscono le informazioni contenute nell'articolo [Concetti relativi ad Azure Mobile Engagement](../mobile-engagement-concepts/) per la piattaforma Windows universale.

### `Session` e `Activity`

Un'*attività* è in genere associata a una pagina dell'applicazione, ovvero l'*attività* inizia quando la pagina viene visualizzata e si arresta quando la pagina viene chiusa. Questo avviene quando l'SDK di Engagement è integrato mediante la classe `EngagementPage`.

Le *attività* possono tuttavia essere controllate anche manualmente usando l'API di Engagement. Ciò consente di dividere una determinata pagina in più sottoparti per ottenere maggiori dettagli sull'utilizzo della pagina (ad esempio per conoscere la frequenza e la durata dell'uso delle finestre di dialogo all'interno della pagina).

##Segnalazione di attività

### L'utente inizia una nuova attività

#### Riferimento

			void StartActivity(string name, Dictionary<object, object> extras = null)

È necessario chiamare `StartActivity()` ogni volta che l'attività dell'utente cambia. La prima chiamata a questa funzione avvia una nuova sessione utente.

> [AZURE.IMPORTANT]L'SDK chiama automaticamente il metodo EndActivity quando viene chiusa l'applicazione. Di conseguenza, è ALTAMENTE consigliato chiamare il metodo StartActivity ogni volta che l'attività dell'utente cambia e non chiamare MAI il metodo EndActivity poiché la chiamata di questo metodo forza la chiusura della sessione corrente.

#### Esempio

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### L'utente termina l'attività corrente

#### Riferimento

			void EndActivity()

Vengono terminate l'attività e la sessione. Chiamare questo metodo solo se si è perfettamente consapevoli delle conseguenze.

#### Esempio

			EngagementAgent.Instance.EndActivity();

##Segnalazione di processi

### Avviare un processo

#### Riferimento

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

#### Riferimento

			void EndJob(string name)

Appena terminata un'attività di cui un processo tiene traccia, è necessario chiamare il metodo EndJob per questo processo, fornendo il nome del processo.

#### Esempio

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Segnalazione di eventi

Esistono tre tipi di eventi:

-   Eventi autonomi
-   Eventi di sessione
-   Eventi di processo

### Eventi autonomi

#### Riferimento

			void SendEvent(string name, Dictionary<object, object> extras = null)

Gli eventi autonomi possono verificarsi all'esterno del contesto di una sessione.

#### Esempio

			EngagementAgent.Instance.SendEvent("event", extra);

### Eventi di sessione

#### Riferimento

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Gli eventi di sessione vengono in genere usati per segnalare le azioni eseguite da un utente durante la sua sessione.

#### Esempio

**Senza dati:**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Con dati:**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### Eventi di processo

#### Riferimento

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Gli eventi di processo in genere vengono utilizzati per segnalare le azioni eseguite da un utente durante un processo.

#### Esempio

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##Segnalazione di errori

Esistono tre tipi di errori:

-   Errori autonomi
-   Errori di sessione
-   Errori di processo

### Errori autonomi

#### Riferimento

			void SendError(string name, Dictionary<object, object> extras = null)

Diversamente dagli errori di sessione, gli errori autonomi possono verificarsi all'esterno del contesto di una sessione.

#### Esempio

			EngagementAgent.Instance.SendError("errorName", extras);

### Errori di sessione

#### Riferimento

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Gli errori di sessione vengono in genere usati per segnalare gli errori che hanno impatto sull'utente durante la sua sessione.

#### Esempio

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Errori di processo

#### Riferimento

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Gli errori possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente.

#### Esempio

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Segnalazione di arresti anomali

L'agente fornisce due metodi per gestire gli arresti anomali.

### Inviare un'eccezione

#### Riferimento

			void SendCrash(Exception e, bool terminateSession = false)

#### Esempio

È possibile inviare un'eccezione in qualsiasi momento chiamando:

			EngagementAgent.Instance.SendCrash(aCatchedException);

È inoltre possibile utilizzare un parametro facoltativo per terminare la sessione di Engagement contemporaneamente all'invio dell'arresto anomalo. A tale scopo, chiamare:

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

In questo caso la sessione e i processi verranno chiusi solo dopo l'invio dell'arresto anomalo.

### Inviare un'eccezione non gestita

#### Riferimento

			void SendCrash(Exception e)

Engagement fornisce anche un metodo per inviare le eccezioni non gestite, se è stata **DISABILITATA** la segnalazione automatica degli **arresti anomali** in Engagement. Questa possibilità è particolarmente utile se utilizzata all'interno del gestore eventi UnhandledException.

Questo metodo terminerà **SEMPRE** la sessione di Engagement e i processi dopo essere stato chiamato.

#### Esempio

Consente di implementare un proprio gestore UnhandledExceptionEventArgs. Ad esempio, aggiungere il metodo `Current_UnhandledException` del file `App.xaml.cs`:

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

##Parametri aggiuntivi

Dati arbitrari possono essere collegati a un evento, un errore, un'attività o un processo. Tali dati possono essere strutturati utilizzando un dizionario. Chiavi e valori possono essere di qualsiasi tipo.

I dati aggiuntivi vengono serializzati, per cui se si desidera inserirvi il proprio tipo è necessario aggiungere un contratto dati per questo tipo.

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

Quindi, si include un'istanza di `Person` per un dato aggiuntivo.

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING]Se si inseriscono altri tipi di oggetti, assicurarsi che il relativo metodo ToString() venga implementato per restituire una stringa leggibile.

### Limiti

#### Chiavi

Ogni chiave dell'oggetto deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\\_).

#### Dimensione

I dati aggiuntivi sono limitati a **1024** caratteri per chiamata.

##Segnalazione di informazioni sull'applicazione

### Riferimento

			void SendAppInfo(Dictionary<object, object> appInfos)

È possibile segnalare manualmente le informazioni di traccia o qualsiasi altra informazione specifica dell'applicazione mediante la funzione SendAppInfo().

Queste informazioni possono essere inviate in modo incrementale: viene mantenuto solo l'ultimo valore per una determinata chiave per ogni dispositivo specifico. Come per i dati aggiuntivi degli eventi, usare Dictionary<object, object> per allegare informazioni.

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

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\\_).

#### Dimensione

Le informazioni sull'applicazione sono limitate a **1024** caratteri per chiamata.

Nell'esempio precedente il codice JSON inviato al server è lungo 44 caratteri:

			{"birthdate":"1983-12-07","gender":"female"}

<!--HONumber=54-->