<properties 
	pageTitle="Come usare l'API di Engagement in Android" 
	description="Android SDK più recente - Come usare l'API di Engagement in Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

#Come usare l'API di Engagement in Android

Questo documento è complementare all'articolo [Come integrare Engagement in Android](mobile-engagement-android-integrate-engagement.md) e fornisce informazioni approfondite su come usare l'API di Engagement per segnalare le statistiche dell'applicazione.

Tenere presente che, se si vuole impostare Engagement in modo che segnali solo le sessioni, le attività, gli arresti anomali e i dati tecnici dell'applicazione, la soluzione più semplice consiste nel fare in modo che tutte le sottoclassi `Activity` ereditino dalla classe `EngagementActivity` corrispondente.

Se invece si hanno esigenze più complesse, ad esempio se è necessario segnalare eventi, errori e processi specifici dell'applicazione o presentare le attività dell'applicazione in modo diverso rispetto a quello implementato nelle classi `EngagementActivity`, è necessario usare l'API di Engagement.

L'API di Engagement viene fornita dalla classe `EngagementAgent`. Un'istanza di questa classe può essere recuperata chiamando il metodo statico `EngagementAgent.getInstance(Context)` (tenere presente che l'oggetto `EngagementAgent` restituito è un singleton).

##Concetti relativi a Mobile Engagement

Le parti seguenti approfondiscono le informazioni contenute nell'articolo [Concetti relativi ad Azure Mobile Engagement](mobile-engagement-concepts.md) per la piattaforma Android.

### `Session` e `Activity`

Se l'utente resta inattivo per più di due secondi tra due *attività*, la sequenza di *attività* viene divisa in due *sessioni* distinte. Questi pochi secondi vengono chiamati "timeout della sessione".

Un'*attività* è in genere associata a una schermata dell'applicazione, ovvero l'*attività* inizia quando la schermata viene visualizzata e si arresta quando la schermata viene chiusa. Questo avviene quando l'SDK di Engagement è integrato mediante le classi `EngagementActivity`.

Le *attività* possono tuttavia essere controllate anche manualmente usando l'API di Engagement. In questo modo, è possibile dividere una schermata specifica in diverse parti secondarie per ottenere maggiori dettagli sull'utilizzo della schermata, ad esempio per definire la frequenza e la durata in base alle quali le finestre di dialogo vengono usate all'interno della schermata.

##Segnalazione di attività

> [AZURE.IMPORTANT]Non è necessario segnalare le attività nel modo indicato in questa sezione se si usa la classe `EngagementActivity` e le sue varianti in base alle istruzioni disponibili nell'articolo relativo all'integrazione di Engagement in Android.

### L'utente inizia una nuova attività

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

È necessario chiamare `startActivity()` ogni volta che l'attività dell'utente cambia. La prima chiamata a questa funzione avvia una nuova sessione utente.

Il punto migliore in cui chiamare questa funzione corrisponde a ogni callback `onResume` dell'attività.

### L'utente termina l'attività corrente

			EngagementAgent.getInstance(this).endActivity();

È necessario chiamare `endActivity()` almeno una volta quando l'utente termina la sua ultima attività. In questo modo, si indica all'SDK di Engagement che l'utente è attualmente inattivo e che la sessione utente deve essere chiusa allo scadere del timeout. Se si chiama `startActivity()` prima dello scadere del timeout, la sessione viene semplicemente ripresa.

Il punto migliore in cui chiamare questa funzione corrisponde a ogni callback `onPause` dell'attività.

##Segnalazione di eventi

### Eventi di sessione

Gli eventi di sessione vengono in genere usati per segnalare le azioni eseguite da un utente durante la sua sessione.

**Esempio senza dati aggiuntivi:**

			public MyActivity extends EngagementActivity {
			   [...]
			   @Override
			   public boolean onPrepareOptionsMenu(Menu menu) {
			      getEngagementAgent().sendSessionEvent("menu_shown", null);
			   }
			   [...]
			}

**Esempio con dati aggiuntivi:**

			public MyActivity extends EngagementActivity {
			  [...]
			  @Override
			  public boolean onMenuItemSelected(int featureId, MenuItem item) {
			    Bundle extras = new Bundle();
			    extras.putInt("id", item.getItemId());
			    getEngagementAgent().sendSessionEvent("menu_selected", extras);
			  }
			  [...]
			}

### Eventi autonomi

Diversamente dagli eventi di sessione, gli eventi autonomi possono verificarsi all'esterno del contesto di una sessione.

**Esempio:**

Si supponga di voler segnalare eventi verificatisi all'attivazione di un ricevitore di trasmissione:

			/** Triggered by Intent.ACTION_BATTERY_LOW */
			public BatteryLowReceiver extends BroadcastReceiver {
			  [...]
			  @Override
			  public void onReceive(Context context, Intent intent) {
			    EngagementAgent.getInstance(context).sendEvent("battery_low", null);
			  }
			  [...]
			}

##Segnalazione di errori

### Errori di sessione

Gli errori di sessione vengono in genere usati per segnalare gli errori che hanno impatto sull'utente durante la sua sessione.

**Esempio:**

			/** The user has entered invalid data in a form */
			public MyActivity extends EngagementActivity {
			  [...]
			  public void onMyFormSubmitted(MyForm form) {
			    [...]
			    /* The user has entered an invalid email address */
			    getEngagementAgent().sendSessionError("sign_up_email", null);
			    [...]
			  }
			  [...]
			}

### Errori autonomi

Diversamente dagli errori di sessione, gli errori autonomi possono verificarsi all'esterno del contesto di una sessione.

**Esempio:**

L'esempio seguente mostra come segnalare un errore ogni volta che la memoria nel telefono è insufficiente durante l'esecuzione del processo dell'applicazione.

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

##Segnalazione di processi

### Esempio

Si supponga di voler segnalare la durata del processo di accesso:
			
			[...]
			public void signIn(Context context, ...) {
			
			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has started */
			  engagementAgent.startJob("sign_in", null);
			
			  [... sign in ...]
			
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Segnalazione di errori durante un processo

Gli errori possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente.

**Esempio:**

Si supponga di voler segnalare un errore durante il processo di accesso:

[...] public void signIn(Context context, ...) {

			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has been started */
			  engagementAgent.startJob("sign_in", null);
			
			  /* Try to sign in */
			  while(true)
			    try {
			      trySignin();
			      break;
			    }
			    catch(Exception e) {
			      /* Report the error to Engagement */
			      engagementAgent.sendJobError("sign_in_error", "sign_in", null);
			
			      /* Retry after a moment */
			      sleep(2000);
			    }
			  [...]
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Segnalazione di eventi durante un processo

Gli eventi possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente.

**Esempio:**

Si supponga di disporre di un social network e di usare un processo per segnalare il tempo totale durante il quale l'utente è connesso al server. Poiché l'utente può restare connesso in background anche quando usa un'altra applicazione o quando il telefono è inattivo, potrebbe non esservi alcuna sessione.

Se l'utente riceve messaggi dagli amici, si tratta di un evento di processo.
			
			[...]
			public void signin(Context context, ...) {
			  [...Sign in code...]
			  EngagementAgent.getInstance(context).startJob("connection", null);
			}
			[...]
			public void signout(Context context) {
			  [...Sign out code...]
			  EngagementAgent.getInstance(context).endJob("connection");
			}
			[...]
			public void onMessageReceived(Context context) {
			  [...Notify in status bar...]
			  EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
			}
			[...]

##Parametri aggiuntivi

È possibile collegare dati arbitrari a eventi, errori, attività e processi.

Questi dati possono essere strutturati, usando la classe Bundle di Android (in realtà, funziona come i parametri aggiuntivi negli intenti di Android). Notare che una classe Bundle può contenere matrici o altre istanze Bundle.

> [AZURE.IMPORTANT]Se si inseriscono parametri parcellizzabili o serializzabili, assicurarsi che venga implementato il rispettivo metodo `toString()` per restituire una stringa leggibile. Le classi serializzabili che contengono campi non temporanei non serializzabili provocano l'arresto anomalo di Android quando si chiama `bundle.putSerializable("key",value);`

> [AZURE.WARNING]Le matrici di tipo sparse non sono supportate nei parametri aggiuntivi, ovvero non vengono serializzate come matrice. Prima di usarle nei parametri aggiuntivi, è consigliabile convertirle in matrici standard.

### Esempio

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### Limiti

#### Chiavi

Ogni chiave in `Bundle` deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*`

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (_).

#### Dimensione

I dati aggiuntivi sono limitati a **1024** caratteri per chiamata, una volta codificati in JSON dal servizio Engagement.

Nell'esempio precedente il codice JSON inviato al server è lungo 58 caratteri:

			{"ref_click":"http://foobar.com/blog","video_id":"123"}

##Segnalazione di informazioni sull'applicazione

È possibile segnalare manualmente le informazioni di traccia o qualsiasi altra informazione specifica dell'applicazione mediante la funzione `sendAppInfo()`.

Queste informazioni possono essere inviate in modo incrementale: viene mantenuto solo l'ultimo valore per una determinata chiave per ogni dispositivo specifico.

Come per i dati aggiuntivi degli eventi, la classe Bundle viene usata per astrarre le informazioni sull'applicazione. Tenere presente che le matrici o i bundle secondari vengono trattati come stringhe flat (usando la serializzazione JSON).

### Esempio

Ecco un esempio di codice per inviare il sesso e la data di nascita dell'utente:

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### Limiti

#### Chiavi

Ogni chiave in `Bundle` deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*`

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (_).

#### Dimensione

Le informazioni sull'applicazione sono limitate a **1024** caratteri per chiamata, una volta codificate in JSON dal servizio Engagement.

Nell'esempio precedente il codice JSON inviato al server è lungo 44 caratteri:

			{"expiration":"2016-12-07","status":"premium"}

<!--HONumber=54--> 