<properties 
	pageTitle="Integrazione di Android SDK per Azure Mobile Engagement"
	description="Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per Android"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="piyushjo"/>

#Come integrare Engagement in Android

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Questa procedura descrive il modo più semplice per attivare le funzioni di analisi e monitoraggio di Engagement in un'applicazione per Android.

> [AZURE.IMPORTANT]L'API di Android SDK deve essere almeno di livello 10 o superiore (Android 2.3.3 o versioni successive).
 
I passaggi seguenti sono sufficienti per attivare la segnalazione dei log necessari per calcolare tutte le statistiche relative a utenti, sessioni, attività, arresti anomali del sistema e dati tecnici. La segnalazione dei log necessari per calcolare altre statistiche quali eventi, errori e processi deve essere eseguita manualmente mediante l'API di Engagement (vedere [Come usare l'API di Engagement in Android](mobile-engagement-android-use-engagement-api.md)) poiché queste statistiche dipendono dall'applicazione.

##Incorporare il servizio ed Engagement SDK nel progetto Android

Android SDK è disponibile [qui](http://go.microsoft.com/?linkid=9863935&clcid=0x409) per il download. Ottenere i file `mobile-engagement-VERSION.jar` e inserirli nella cartella `libs` del progetto Android (creare la cartella libs, se non esiste ancora).

> [AZURE.IMPORTANT]Se si compila il pacchetto dell'applicazione con ProGuard, è necessario mantenere alcune classi. È possibile usare il frammento di codice di configurazione seguente:
>
> 
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

Specificare la stringa di connessione a Engagement chiamando il metodo seguente nell'attività dell'utilità di avvio:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure.

-   Aggiungere le autorizzazioni Android seguenti, se mancanti, prima del tag `<application>`:

			<uses-permission android:name="android.permission.INTERNET"/>
			<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Aggiungere la sezione seguente tra i tag `<application>` e `</application>`:

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

-   Al posto di `<Your application name>` specificare il nome dell'applicazione.

> [AZURE.TIP]L'attributo `android:label` consente di scegliere il nome del servizio Engagement così come verrà presentato agli utenti finali nella schermata dei servizi in esecuzione sul telefono. È consigliabile impostare questo attributo su `"<Your application name>Service"`, ad esempio `"AcmeFunGameService"`.

Se si specifica l'attributo `android:process`, il servizio Engagement verrà eseguito nel relativo processo (l'esecuzione di Engagement nello stesso processo dell'applicazione può ridurre la reattività del thread principale o dell'interfaccia utente).

> [AZURE.NOTE]Il codice inserito in `Application.onCreate()` e altri callback dell'applicazione verrà eseguito per tutti i processi dell'applicazione, incluso il servizio Engagement. È possibile che si verifichino effetti collaterali indesiderati, ad esempio allocazioni di memoria e thread superflui nel processo di Engagement oppure ricevitori o servizi di trasmissione duplicati.

Se si esegue l'override di `Application.onCreate()`, è consigliabile aggiungere il frammento di codice seguente all'inizio della funzione `Application.onCreate()`:

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;
			
			   ... Your code...
			 }

È possibile eseguire la stessa operazione per `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

È anche possibile estendere `EngagementApplication` anziché `Application`: il callback esegue il controllo del processo `Application.onCreate()` e chiama `Application.onApplicationProcessCreate()` solo se il processo corrente non è quello che ospita il servizio Engagement. Per gli altri callback vengono applicate le stesse regole.

##Segnalazione di base

### Metodo consigliato: eseguire l'overload delle classi `Activity`

Per attivare la segnalazione di tutti i log richiesti da Engagement per il calcolo delle statistiche relative a utenti, sessioni, attività, arresti anomali del sistema e dati tecnici, è sufficiente fare in modo che tutte le sottoclassi `*Activity` ereditino dalle classi `Engagement*Activity` corrispondenti. Ad esempio, se l'attività legacy estende `ListActivity`, fare in modo che estenda `EngagementListActivity`.

**Senza Engagement:**

			package com.company.myapp;
			
			import android.app.Activity;
			import android.os.Bundle;
			
			public class MyApp extends Activity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

**Con Engagement:**

			package com.company.myapp;
			
			import com.microsoft.azure.engagement.activity.EngagementActivity;
			import android.os.Bundle;
			
			public class MyApp extends EngagementActivity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

> [AZURE.IMPORTANT]Quando si usa `EngagementListActivity` o `EngagementExpandableListActivity`, assicurarsi che tutte le chiamate a `requestWindowFeature(...);` vengano eseguite prima della chiamata a `super.onCreate(...);`. In caso contrario, si verificherà un arresto anomalo del sistema.

Le sottoclassi di `FragmentActivity` e `MapActivity` sono disponibili, ma per evitare problemi con le applicazioni che usano **ProGuard** non sono incluse in `engagement.jar`.

Queste classi sono incluse nella cartella `src` e possono essere copiate nel progetto. Sono reperibili anche in **JavaDoc**.

### Metodo alternativo: chiamare manualmente `startActivity()` e `endActivity()`

Se non si può o non si vuole eseguire l'overload delle classi `Activity`, è possibile avviare e terminare le attività chiamando direttamente i metodi di `EngagementAgent`.

> [AZURE.IMPORTANT]Android SDK non chiama mai il metodo `endActivity()`, neanche alla chiusura dell'applicazione (in Android le applicazioni in realtà non vengono mai chiuse). Per questo motivo, è *ALTAMENTE* consigliabile chiamare il metodo `startActivity()` nel callback `onResume` di *TUTTE* le attività e il metodo `endActivity()` nel callback `onPause()` di *TUTTE* le attività. È l'unico modo per evitare la perdita di sessioni. In caso di perdita di una sessione, il servizio Engagement non si disconnetterà mai dal back-end di Engagement, dato che il servizio rimane connesso fintanto che una sessione è in sospeso.

Di seguito è fornito un esempio:

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }
			
			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

Questo esempio è molto simile alla classe `EngagementActivity` e alle relative varianti, il cui codice di origine è disponibile nella cartella `src`.

##Test

Verificare ora l'integrazione, eseguendo l'app per dispositivi mobili in un emulatore o in un dispositivo e verificando che registri una sessione nella scheda Monitoraggio.

Le sezioni successive sono facoltative.

##Segnalazione della posizione

Per fare in modo che le posizioni vengano segnalate, è necessario aggiungere alcune righe di configurazione tra i tag `<application>` e `</application>`.

### Segnalazione differita della posizione

La segnalazione differita della posizione consente di segnalare il paese, l'area geografica e la località associati ai dispositivi. Questo tipo di segnalazione della posizione usa solo le posizioni di rete, sulla base dell'ID di cella o della connessione Wi-Fi. L'area del dispositivo viene segnalata al massimo una volta per sessione. Il GPS non viene mai usato, per cui l'impatto di questo tipo di segnalazione della posizione sulla batteria è minimo, se non addirittura nullo.

Le aree segnalate vengono usate per calcolare statistiche geografiche relative a utenti, sessioni, eventi ed errori. Possono essere usate anche come criteri nelle campagne Reach.

Per abilitare la segnalazione differita della posizione, è possibile utilizzare la configurazione descritta in precedenza in questa procedura:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

È necessario aggiungere anche le autorizzazioni seguenti, se mancanti:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Oppure è possibile continuare a utilizzare ``ACCESS_FINE_LOCATION`` se è già utilizzato nell'applicazione.

### Segnalazione della posizione in tempo reale

La segnalazione della posizione in tempo reale consente di segnalare la latitudine e la longitudine associate ai dispositivi. Per impostazione predefinita, la segnalazione differita della posizione usa solo posizioni di rete (in base all'ID di cella o alla connessione Wi-Fi) ed è attiva solo quando l'applicazione viene eseguita in primo piano, ad esempio durante una sessione.

Le posizioni in tempo reale *NON* sono usate per calcolare dati statistici. Il loro unico scopo è consentire l'uso del criterio di definizione del recinto virtuale in tempo reale <Reach-Audience-geofencing> nelle campagne Reach.

Per abilitare la segnalazione della posizione in tempo reale, è possibile utilizzare la configurazione descritta in precedenza in questa procedura:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

È necessario aggiungere anche le autorizzazioni seguenti, se mancanti:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Oppure è possibile continuare a utilizzare ``ACCESS_FINE_LOCATION`` se è già utilizzato nell'applicazione.

#### Segnalazione basata su GPS

Per impostazione predefinita, la segnalazione della posizione in tempo reale usa solo posizioni di rete. Per abilitare l'uso di posizioni basate su GPS (che sono molto più precise), utilizzare l’oggetto di configurazione:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

È necessario aggiungere anche le autorizzazioni seguenti, se mancanti:

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Segnalazione in background

Per impostazione predefinita, la segnalazione della posizione in tempo reale è attiva solo quando l'applicazione viene eseguita in primo piano, ad esempio durante una sessione. Per abilitare la segnalazione anche in background, utilizzare l’oggetto di configurazione:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE]Quando l'applicazione viene eseguita in background, vengono segnalate solo le posizioni basate sulla rete, anche se è abilitato il GPS.

La segnalazione della posizione in background verrà arrestata se l'utente riavvia il dispositivo. Per fare in modo che venga riavviata automaticamente al riavvio, aggiungere quanto segue:

			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>

È necessario aggiungere anche le autorizzazioni seguenti, se mancanti:

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### Autorizzazioni Android M

A partire da Android M, alcune autorizzazioni vengono gestite in fase di esecuzione e richiedono l'approvazione dell'utente.

Le autorizzazioni di runtime verranno disattivate per impostazione predefinita per le installazioni di nuove app se la destinazione è il livello 23 dell’API Android. In caso contrario verranno attivate per impostazione predefinita.

L'utente può abilitare o disabilitare le autorizzazioni dal menu delle impostazioni del dispositivo. La disattivazione delle autorizzazioni dal menu di sistema elimina i processi in background dell'applicazione; si tratta di un comportamento del sistema e non influisce sulla possibilità di ricevere push in background.

Nel contesto di Mobile Engagement, le autorizzazioni che richiedono l'approvazione al runtime sono:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE` (solo quando la destinazione è il livello 23 dell’API Android)

L’archiviazione esterna viene utilizzata solo per la funzionalità della panoramica generale Reach. Se si ritiene che richiedere agli utenti questa autorizzazione sia problematico, è possibile rimuoverla se è stata utilizzata solo per Mobile Engagement, ma comporta la disattivazione delle funzionalità della panoramica generale.

Per le funzionalità del percorso, è necessario richiedere le autorizzazioni all'utente tramite una finestra di dialogo di sistema standard. Se l'utente approva, è necessario specificare ``EngagementAgent`` per tenere in considerazione la modifica in tempo reale (in caso contrario la modifica verrà elaborata la volta successiva che l'utente avvia l'applicazione).

Ecco un esempio di codice da utilizzare in un'attività dell'applicazione per richiedere autorizzazioni e inoltrare il risultato, se positivo, a ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */
    
      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);
    
        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##Segnalazione avanzata

Facoltativamente, per segnalare eventi, errori e processi specifici dell'applicazione, è necessario usare l'API di Engagement mediante i metodi della classe `EngagementAgent`. Un oggetto di questa classe può essere recuperato chiamando il metodo statico `EngagementAgent.getInstance()`.

L'API di Engagement consente di usare tutte le funzionalità avanzate di Engagement ed è descritta in dettaglio nell'argomento dedicato all'uso dell'API in Android, oltre che nella documentazione tecnica relativa alla classe `EngagementAgent`.

##Configurazione avanzata (in AndroidManifest.xml)

Per assicurarsi che le statistiche vengano inviate in tempo reale quando si usa una connessione Wi-Fi o quando lo schermo è spento, aggiungere la seguente autorizzazione facoltativa:

			<uses-permission android:name="android.permission.WAKE_LOCK"/>

Per disabilitare la segnalazione di arresti anomali del sistema, aggiungere quanto segue tra i tag `<application>` e `</application>`:

			<meta-data android:name="engagement:reportCrash" android:value="false"/>

Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala i log molto spesso, è preferibile memorizzare i log nel buffer e segnalarli tutti insieme con cadenza regolare (la cosiddetta "modalità burst"). A tale scopo, aggiungere quanto segue tra i tag `<application>` e `</application>`:

			<meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitor di Engagement: la durata di tutte le sessioni e di tutti i processi verrà arrotondata alla soglia di burst (di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili). Si consiglia di usare una soglia di burst non maggiore di 30000 (30 secondi).

Per impostazione predefinita, il servizio Engagement stabilisce la connessione al server non appena la rete è disponibile. Per posticipare la connessione, aggiungere quanto segue tra i tag `<application>` e `</application>`:

			<meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

Per impostazione predefinita, una sessione viene terminata 10 secondi dopo la fine dell'ultima attività, che in genere si verifica premendo Home o Indietro, impostando l'inattività del telefono o passando a un'altra applicazione. Questo avviene per evitare una divisione di sessione ogni volta che l'utente esce e rientra nell'applicazione molto rapidamente, come può accadere quando preleva un'immagine, controlla una notifica e così via. Questo parametro può essere modificato. A tale scopo, aggiungere quanto segue tra i tag `<application>` e `</application>`:

			<meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

##Disabilitare la segnalazione di log

### Uso di una chiamata del metodo

Se si vuole che Engagement non invii più log, è possibile chiamare:

			EngagementAgent.getInstance(context).setEnabled(false);

Questa chiamata è persistente: usa un file di preferenze condivise.

Se Engagement è attivo quando si chiama questa funzione, l'arresto del servizio può richiedere 1 minuto. Al successivo avvio dell'applicazione, tuttavia, il servizio non verrà avviato.

È possibile abilitare di nuovo la segnalazione di log chiamando la stessa funzione con `true`.

### Integrazione nella propria classe `PreferenceActivity`

Invece di chiamare questa funzione, è anche possibile integrare questa impostazione direttamente nella classe `PreferenceActivity` esistente.

È possibile configurare Engagement in modo da usare il file di preferenze (con la modalità desiderata) nel file `AndroidManifest.xml` con `application meta-data`:

-   La chiave `engagement:agent:settings:name` viene usata per definire il nome del file di preferenze condivise.
-   La chiave `engagement:agent:settings:mode` viene usata per definire la modalità del file di preferenze condivise. È consigliabile adottare la stessa modalità usata per `PreferenceActivity`. La modalità deve essere passata come numero: se si usa una combinazione di flag di costanti nel codice, controllare il valore totale.

Engagement usa sempre la chiave booleana `engagement:key` all'interno del file di preferenze per la gestione di questa impostazione.

L'esempio seguente di `AndroidManifest.xml` mostra i valori predefiniti:

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

Sarà quindi possibile aggiungere un elemento `CheckBoxPreference` nel layout delle preferenze simile a quello riportato di seguito:

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
 

<!---HONumber=August15_HO9-->