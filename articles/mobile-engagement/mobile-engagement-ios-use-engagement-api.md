<properties
	pageTitle="Come usare l'API di Engagement in iOS"
	description="iOS SDK più recente: come usare l'API di Engagement in iOS"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="piyushjo" />


#Come usare l'API di Engagement in iOS

Questo documento è complementare all'articolo relativo all'integrazione di Engagement in iOS e fornisce informazioni approfondite su come usare l'API di Engagement per segnalare le statistiche dell'applicazione.

Tenere presente che, se si vuole impostare Engagement in modo che segnali solo le sessioni, le attività, gli arresti anomali e i dati tecnici dell'applicazione, la soluzione più semplice consiste nel fare in modo che tutti gli oggetti `UIViewController` personalizzati ereditino dalla classe `EngagementViewController` corrispondente.

Se invece si hanno esigenze più complesse, ad esempio se è necessario segnalare eventi, errori e processi specifici dell'applicazione o presentare le attività dell'applicazione in modo diverso rispetto a quello implementato nelle classi `EngagementViewController`, è necessario usare l'API di Engagement.

L'API di Engagement viene fornita dalla classe `EngagementAgent`. Un'istanza di questa classe può essere recuperata chiamando il metodo statico `[EngagementAgent shared]` (tenere presente che l'oggetto `EngagementAgent` restituito è un singleton).

Prima di eseguire chiamate API, è necessario inizializzare l'oggetto `EngagementAgent` chiamando il metodo `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##Concetti relativi a Mobile Engagement

Le parti seguenti approfondiscono le informazioni contenute nell'articolo [Concetti relativi ad Azure Mobile Engagement](mobile-engagement-concepts.md) per la piattaforma iOS.

### `Session` e `Activity`

Un'*attività* è in genere associata a una schermata dell'applicazione, ovvero l'*attività* inizia quando la schermata viene visualizzata e si arresta quando la schermata viene chiusa. Questo avviene quando l'SDK di Engagement è integrato mediante le classi `EngagementViewController`.

Le *attività* possono tuttavia essere controllate anche manualmente usando l'API di Engagement. In questo modo, è possibile dividere una schermata specifica in diverse parti secondarie per ottenere maggiori dettagli sull'utilizzo della schermata, ad esempio per definire la frequenza e la durata in base alle quali le finestre di dialogo vengono usate all'interno della schermata.

##Segnalazione di attività

### L'utente inizia una nuova attività

			[[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

È necessario chiamare `startActivity()` ogni volta che l'attività dell'utente cambia. La prima chiamata a questa funzione avvia una nuova sessione utente.

### L'utente termina l'attività corrente

			[[EngagementAgent shared] endActivity];

> [AZURE.WARNING] Questa funzione non deve **MAI** essere chiamata autonomamente, a meno che non si desideri dividere un uso dell'applicazione in più sessioni. Una chiamata a questa funzione interromperebbe immediatamente la sessione corrente e quindi una successiva chiamata a `startActivity()` avvierebbe una nuova sessione. Questa funzionalità viene chiamata automaticamente dall'SDK durante la chiusura dell'applicazione.

##Segnalazione di eventi

### Eventi di sessione

Gli eventi di sessione vengono in genere usati per segnalare le azioni eseguite da un utente durante la sua sessione.

**Esempio senza dati aggiuntivi:**

	@implementation MyViewController {
	   [...]
	   - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
	   {
	    [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
	        ...
	    [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
	        ...
	   }
	   [...]
	}

**Esempio con dati aggiuntivi:**

	@implementation MyViewController {
	   [...]
	   - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
	   {
	    [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
	        ...
	    NSMutableDictionary* extras = [NSMutableDictionary dictionary];
	    [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
	    [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
	    [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
	        ...
	   }
	   [...]
	}

### Eventi autonomi

Contrariamente agli eventi della sessione, quelli autonomi possono essere utilizzati al di fuori del contesto di una sessione.

**Esempio:**

	[[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##Segnalazione di errori

### Errori di sessione

Gli errori di sessione vengono in genere usati per segnalare gli errori che hanno impatto sull'utente durante la sua sessione.

**Esempio:**

	/** The user has entered invalid data in a form */
	@implementation MyViewController {
	  [...]
	  -(void)onMyFormSubmitted:(MyForm*)form {
	    [...]
	    /* The user has entered an invalid email address */
	    [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
	    [...]
	  }
	  [...]
	}

### Errori autonomi

Contrariamente agli errori della sessione, quelli autonomi possono essere utilizzati al di fuori del contesto di una sessione.

**Esempio:**

	[[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##Segnalazione di processi

**Esempio:**

Si supponga di voler segnalare la durata del processo di accesso:

	[...]
	-(void)signIn
	{
	  /* Start job */
	  [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

	  [... sign in ...]

	  /* End job */
	  [[EngagementAgent shared] endJob:@"sign_in"];
	}
	[...]

### Segnalazione di errori durante un processo

Gli errori possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente.

**Esempio:**

Si consideri una situazione nella quale l'utente desidera segnalare un errore durante il processo di accesso:

	[...]
	-(void)signin
	{
	  /* Start job */
	  [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

	  BOOL success = NO;
	  while (!success) {
	    /* Try to sign in */
	    NSError* error = nil;
	    [self trySigin:&error];
	    success = error == nil;

	    /* If an error occured report it */
	    if(!success)
	    {
	      [[EngagementAgent shared] sendJobError:@"sign_in_error"
	                     jobName:@"sign_in"
	                      extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

	      /* Retry after a moment */
	      [NSThread sleepForTimeInterval:20];
	    }
	  }

	  /* End job */
	  [[EngagementAgent shared] endJob:@"sign_in"];
	};
	[...]

### Eventi durante un processo

Gli eventi possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente.

**Esempio:**

Si supponga di disporre di un social network e di usare un processo per segnalare il tempo totale durante il quale l'utente è connesso al server. Se l'utente riceve messaggi dagli amici, si tratta di un evento di processo.

	[...]
	- (void) signin
	{
	  [...Sign in code...]
	  [[EngagementAgent shared] startJob:@"connection" extras:nil];
	}
	[...]
	- (void) signout
	{
	  [...Sign out code...]
	  [[EngagementAgent shared] endJob:@"connection"];
	}
	[...]
	- (void) onMessageReceived
	{
	  [...Notify user...]
	  [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
	}
	[...]

##Parametri aggiuntivi

È possibile collegare dati arbitrari a eventi, errori, attività e processi.

Questi dati possono essere strutturati e utilizzano la classe NSDictionary di iOS.

Tenere presente che i dati aggiuntivi possono includere `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` o altre istanze di `NSDictionary`.

> [AZURE.NOTE] Il parametro aggiuntivo viene serializzato in JSON. Se si desidera passare oggetti diversi da quelli descritti in precedenza, è necessario implementare il metodo seguente nella classe:
>
			 -(NSString*)JSONRepresentation;
>
> Il metodo deve restituire una rappresentazione JSON dell'oggetto.

### Esempio

	NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
	[extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
	[extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
	[[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### Limiti

#### Chiavi

Ogni chiave in `NSDictionary` deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*`

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\\_).

#### Dimensione

I dati aggiuntivi sono limitati a **1024** caratteri per chiamata, una volta codificati in JSON dall'agente di Engagement.

Nell'esempio precedente il codice JSON inviato al server è lungo 58 caratteri:

	{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##Segnalazione di informazioni sull'applicazione

È possibile segnalare manualmente le informazioni di traccia o qualsiasi altra informazione specifica dell'applicazione mediante la funzione `sendAppInfo:`.

Queste informazioni possono essere inviate in modo incrementale: viene mantenuto solo l'ultimo valore per una determinata chiave per ogni dispositivo specifico.

Come per i dati aggiuntivi degli eventi, la classe `NSDictionary` viene usata per astrarre le informazioni sull'applicazione. Tenere presente che le matrici o i dizionari secondari vengono trattati come stringhe flat (usando la serializzazione JSON).

**Esempio:**

	NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
	[appInfo setObject:@"female" forKey:@"gender"];
	[appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
	[[EngagementAgent shared] sendAppInfo:appInfo];

### Limiti

#### Chiavi

Ogni chiave in `NSDictionary` deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*`

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\\_).

#### Dimensione

Le informazioni sull'applicazione sono limitate a **1024** caratteri per chiamata, una volta codificate in JSON dall'agente di Engagement.

Nell'esempio precedente il codice JSON inviato al server è lungo 44 caratteri:

	{"birthdate":"1983-12-07","gender":"female"}

<!---HONumber=AcomDC_0824_2016-->