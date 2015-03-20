<properties 
	pageTitle="Come utilizzare l'API Engagement in iOS" 
	description="SDK iOS più recente: come utilizzare l'API Engagement in iOS"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />


#Come utilizzare l'API Engagement in iOS

Il presente documento è complementare a quello dal titolo "Come integrare Engagement in iOS:" e fornisce dettagli su come utilizzare l'API Engagement per segnalare le statistiche relative all'applicazione.

Tenere presente che se si desidera impostare Engagement in modo che segnali solo le sessioni, le attività, gli arresti anomali e le informazioni tecniche dell'applicazione, il modo più semplice consiste nel far sì che tutti gli oggetti  `UIViewController` personalizzati ereditino dalla classe `EngagementViewController` corrispondente.

Se si desidera effettuare ulteriori operazioni, ad esempio, se si desidera segnalare eventi, errori e processi specifici dell'applicazione o se si desidera segnalare le attività dell'applicazione in un modo differente rispetto a quello implementato nelle classi  `EngagementViewController`, è necessario utilizzare l'API Engagement.

L'API Engagement viene fornita dalla classe `EngagementAgent`. Un'istanza di questa classe può essere recuperata chiamando il metodo statico`[EngagementAgent shared]` (tenere presente che l'oggetto `EngagementAgent` restituito è un singleton).

Prima di eseguire qualsiasi chiamata dell'API, è necessario inizializzare l'oggetto  `EngagementAgent` chiamando il metodo `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##Concetti di Engagement

Nelle sezioni seguenti vengono riportati i concetti comuni relativi a [Mobile Engagement](../mobile-engagement-concepts/) per la piattaforma iOS.

### `Sessione` e  `Attività`

Generalmente, un' *attività* è associata a una schermata dell'applicazione, vale a dire che l' *attività* si avvia quando la schermata viene visualizzata e si interrompe quando la schermata viene chiusa. Ciò si verifica quando l'SDK Engagement viene integrato utilizzando le classi `EngagementViewController`.

Tuttavia, le *attività* possono essere controllate anche manualmente utilizzando l'API Engagement. In questo modo, è possibile suddividere una schermata in più parti secondarie al fine di visualizzarne ulteriori dettagli sull'utilizzo (ad esempio, per scoprire la frequenza e la durata dell'utilizzo delle finestre di dialogo nella schermata).

##Segnalazione di attività

### L'utente inizia una nuova attività

			[[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

È necessario chiamare  `startActivity()` ogni volta che l'attività dell'utente cambia. La prima chiamata a questa funzione avvia una nuova sessione utente.

### L'utente termina la sua attività corrente

			[[EngagementAgent shared] endActivity];

> [AZURE.WARNING] Questa funzione non deve essere **MAI** chiamata dall'utente, a meno che non desideri dividere un utilizzo dell'applicazione in diverse sessioni. La chiamata di questa funzione interrompe immediatamente la sessione corrente, per cui una successiva chiamata di `startActivity()` avvierebbe una nuova sessione. Questa funzionalità viene chiamata automaticamente dall'SDK durante la chiusura dell'applicazione.

##Segnalazione di eventi

### Eventi di sessione

Gli eventi di sessione in genere vengono utilizzati per segnalare le azioni eseguite da un utente durante la sessione.

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

Gli errori di sessione in genere vengono utilizzati per segnalare gli errori che hanno un impatto sull'utente durante la sessione.

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

Si consideri una situazione nella quale l'utente desidera segnalare la durata del processo di accesso:

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

Gli errori possono essere correlati a un processo in esecuzione anziché alla sessione utente corrente.

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

Gli eventi possono riferirsi a un processo in corso, invece che alla sessione utente attuale.

**Esempio:**

si supponga di disporre di un social network e di utilizzare un processo per segnalare la durata totale di connessione dell'utente al server. È possibile che l'utente riceva messaggi dai suoi amici. In questo caso, si verifica un evento di processo.

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

I dati arbitrari possono essere collegati a eventi, errori, attività e processi.

Questi dati possono essere strutturati e utilizzano la classe NSDictionary di iOS.

Tenere presente che i parametri aggiuntivi possono includere `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` o altre istanze `NSDictionary`.

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

Ogni chiave in `NSDictionary` deve corrispondere alla seguente espressione regolare:

`^[a-zA-Z][a-zA-Z_0-9]*`

In altre parole, le chiavi devono iniziare con almeno una lettera seguita da lettere, cifre o caratteri di sottolineatura (\).

#### Dimensioni

I parametri aggiuntivi possono contenere al massimo **1024** caratteri per chiamata (dopo essere stati codificati dall'agente Engagement in JSON).

Nell'esempio precedente, il formato JSON inviato al server era composto da 58 caratteri:

			{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##Segnalazione di informazioni sull'applicazione

È possibile segnalare manualmente le informazioni di rilevamento (o qualsiasi altra informazione specifica delle applicazioni) utilizzando la funzione `sendAppInfo:`.

Tenere presente che queste informazioni possono essere inviate in modo incrementale: verrà mantenuto solo l'ultimo valore di una chiave per un dispositivo specifico.

In modo analogo agli eventi aggiuntivi, la classe `NSDictionary` viene utilizzata per estrarre le informazioni sull'applicazione. Tenere presente che i dizionari secondari e le matrici secondarie vengono considerati come stringhe piatte (mediante la serializzazione JSON).

**Esempio:**

			NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
			[appInfo setObject:@"female" forKey:@"gender"];
			[appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
			[[EngagementAgent shared] sendAppInfo:appInfo];

### Limiti

#### Chiavi

Ogni chiave in `NSDictionary` deve corrispondere alla seguente espressione regolare:

`^[a-zA-Z][a-zA-Z_0-9]*`

In altre parole, le chiavi devono iniziare con almeno una lettera seguita da lettere, cifre o caratteri di sottolineatura (\).

#### Dimensioni

Le informazioni sull'applicazione possono contenere al massimo **1024** caratteri per chiamata (dopo essere state codificate dall'agente Engagement in JSON).

Nell'esempio precedente, il JSON inviato al server è lungo 44 caratteri:

			{"birthdate":"1983-12-07","gender":"female"}


<!--HONumber=47-->
