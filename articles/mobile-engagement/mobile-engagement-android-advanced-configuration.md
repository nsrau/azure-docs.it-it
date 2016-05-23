<properties
	pageTitle="Configurazione avanzata con Mobile Engagement SDK per Android"
	description="Opzioni di configurazione avanzate per Android con Azure Mobile Engagement SDK"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="piyushjo;ricksal" />

# Configurazione avanzata con Mobile Engagement SDK per Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-logging.md)

Questa procedura descrive la definizione di diverse opzioni di configurazione avanzate per le app di engagement per Android.

## Prerequisiti

[AZURE.INCLUDE [Prerequisiti](../../includes/mobile-engagement-android-prereqs.md)]

## Requisiti di autorizzazione
Alcune opzioni richiedono autorizzazioni specifiche, ognuna delle quali viene elencata di seguito a titolo di riferimento nonché inline nella funzionalità specifica. Aggiungere queste autorizzazioni al file AndroidManifest.xml del progetto immediatamente prima o dopo il tag `<application>`.

Il codice di autorizzazione deve essere simile al seguente. L'autorizzazione appropriata viene compilata in base alla tabella seguente.

		<uses-permission android:name="android.permission.[specific permission]"/>



| Autorizzazione | Quando si usa |
| ---------- | --------- |
| INTERNET | Segnalazione di base |
| ACCESS\_NETWORK\_STATE" | Segnalazione di base |
| WRITE\_EXTERNAL\_STORAGE | Segnalazione di base |
| RECEIVE\_BOOT\_COMPLETED | Segnalazione di base |
| VIBRATE | Segnalazione di base |
| DOWNLOAD\_WITHOUT\_NOTIFICATION | Notifica generale |
| WAKE\_LOCK | Raccolta delle statistiche quando si usa il Wi-Fi o lo schermo è disattivato |
| RECEIVE\_BOOT\_COMPLETED | Abilitazione di report in background |
| ACCESS\_COARSE\_LOCATION | Report sulle posizioni in tempo reale |
| ACCESS\_FINE\_LOCATION | Report basati su GPS |



A partire da Android M, [alcune autorizzazioni vengono gestite in fase di esecuzione](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Se si usa già ``ACCESS_FINE_LOCATION`` non è necessario usare anche ``ACCESS_COARSE_LOCATION``.

## Opzioni di configurazione del file manifesto

### Segnalazione di arresto anomalo

Per disabilitare la segnalazione di arresti anomali del sistema, aggiungere quanto segue tra i tag `<application>` e `</application>`:

		<meta-data android:name="engagement:reportCrash" android:value="false"/>

### Soglia del burst

Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala i log molto spesso, è preferibile memorizzare i log nel buffer e segnalarli tutti insieme con cadenza regolare (la cosiddetta "modalità burst"). A tale scopo, aggiungere questo codice tra i tag `<application>` e `</application>`:

		<meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitor di Engagement: la durata di tutte le sessioni e di tutti i processi verrà arrotondata alla soglia di burst (di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili). Si consiglia di usare una soglia di burst non maggiore di 30000 (30 secondi).

### Timeout della sessione

Per impostazione predefinita, una sessione viene terminata 10 secondi dopo la fine dell'ultima attività, che in genere si verifica premendo Home o Indietro, impostando l'inattività del telefono o passando a un'altra applicazione. Questo avviene per evitare una divisione di sessione ogni volta che l'utente esce e rientra nell'applicazione molto rapidamente, come può accadere quando preleva un'immagine, controlla una notifica e così via. Questo parametro può essere modificato. A tale scopo, aggiungere quanto segue tra i tag `<application>` e `</application>`:

		<meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## Disabilitare la segnalazione di log

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

<!---HONumber=AcomDC_0511_2016-->