<properties 
	pageTitle="Integrazione di Azure Mobile Engagement SDK per Android" 
	description="Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Come integrare GCM con Mobile Engagement

> [AZURE.IMPORTANT]Prima di usare questa guida, è necessario eseguire la procedura di integrazione descritta nel documento relativo all'integrazione di Engagement in Android.
>
> Questo documento è utile solo se è stato integrato il modulo Reach per il supporto continuo delle campagne. Per integrare le campagne Reach nell'applicazione, leggere prima l'articolo relativo all'integrazione del servizio Reach di Engagement in Android.

##Introduzione

L'integrazione di GCM consente il push dell'applicazione anche quando questa non è in esecuzione.

Nessun dato della campagna viene effettivamente inviato tramite GCM: è solo un segnale in background che indica all'applicazione di recuperare il push di Engagement. Se l'applicazione non è in esecuzione durante la ricezione di un push GCM, attiva una connessione ai server di Engagement per recuperare il push; la connessione di Engagement rimane attiva per circa un minuto nel caso in cui l'utente avvii l'applicazione nella risposta al push.

A titolo informativo, Engagement usa solo messaggi [Send-to-Sync] con la chiave di compressione `engagement.tickle`.

> [AZURE.IMPORTANT]Solo i dispositivi che eseguono Android 2.2 o versioni successive, con Google Play installato e con la connessione a Google in background abilitata, possono essere riattivati da GCM. È comunque possibile integrare questo codice in modo sicuro in versioni precedenti di Android SDK e in dispositivi che non supportano GCM (usa solo elementi di tipo Intent). Se l'applicazione non può essere riattivata da GCM, la notifica di Engagement verrà ricevuta al successivo avvio dell'applicazione.


> [AZURE.WARNING]Se il proprio codice client gestisce gli identificatori di registrazione C2DM mentre l'SDK di Engagement è configurato per l'uso di GCM, si verifica un conflitto negli identificatori di registrazione. Usare GCM in Engagement solo se il proprio codice non usa C2DM.

##Iscriversi a GCM e abilitare il servizio GCM

Se non è già stato fatto, è necessario abilitare GCM nel proprio account Google.

Al momento della stesura di questo documento (5 febbraio 2014), è possibile seguire la procedura all'indirizzo [<http://developer.android.com/guide/google/gcm/gs.html>].

Seguire tale procedura solo per abilitare GCM per l'account. Quando si raggiunge la sezione che descrive come **ottenere una chiave API**, non leggerla e tornare a questa pagina anziché proseguire la procedura di Google.

Nella procedura viene spiegato che il **numero di progetto** viene usato come **ID mittente GCM**. Questa informazione sarà necessario più avanti nella procedura.

> [AZURE.IMPORTANT]Il **numero di progetto** non deve essere confuso con l'**ID progetto**. L'ID progetto può essere diverso (è un nome nei nuovi progetti). Nell'SDK di Engagement è necessario integrare il **numero di progetto**, che è visualizzato nel menu **Overview** di [Google Developers Console].

##Integrazione dell'SDK

### Gestione delle registrazioni dei dispositivi

Ogni dispositivo deve inviare un comando di registrazione ai server Google; in caso contrario non sarà raggiungibile.

Un dispositivo può anche annullare la registrazione alle notifiche GCM (la registrazione del dispositivo viene annullata automaticamente se l'applicazione viene disinstallata).

Se si usa la [libreria client GCM], è possibile leggere direttamente android-sdk-gcm-receive.

Se l'Intent di registrazione non viene inviato manualmente, è possibile fare in modo che Engagement registri automaticamente il dispositivo.

Per abilitare questa funzionalità, aggiungere il codice seguente nel file `AndroidManifest.xml`, all'interno del tag `<application/>`:

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### Comunicare l'ID di registrazione al servizio push di Engagement e ricevere le notifiche

Per comunicare l'ID di registrazione del dispositivo al servizio push di Engagement e ricevere le relative notifiche, aggiungere quanto segue al file `AndroidManifest.xml`, all'interno del tag `<application/>` (anche se si gestiscono autonomamente le registrazioni dei dispositivi):

			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
			    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

Assicurarsi di avere le seguenti autorizzazioni nel file `AndroidManifest.xml` (dopo il tag `</application>`).

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##Concedere a Engagement l'accesso a una chiave API server

Se non si è ancora fatto, creare una **chiave API server** in [Google Developers Console].

La chiave server **NON DEVE avere restrizioni di IP**.

Al momento della stesura di questo documento (5 febbraio 2014), la procedura è la seguente:

-   Aprire [Google Developers Console].
-   Selezionare lo stesso progetto usato in precedenza nella procedura (quello con il **numero di progetto** che è stato integrato in `AndroidManifest.xml`).
-   Passare a APIs & auth -\\> Credentials, fare clic su "CREATE NEW KEY" nella sezione "Public API access".
-   Selezionare "Server key".
-   Nella schermata successiva lasciare vuoto il campo **(no IP restriction)**, quindi fare clic su Create.
-   Copiare la **chiave API** generata.
-   Passare a $/#application/YOUR_ENGAGEMENT_APPID/native-push.
-   Nella sezione GCM impostare la chiave API su quella appena generata e copiata.

È ora possibile selezionare "In qualsiasi momento" durante la creazione di annunci e sondaggi del servizio Reach.

> [AZURE.IMPORTANT]Engagement richiede una **chiave server**. I server di Engagement non possono usare una chiave Android.

##Test

Per verificare l'integrazione, vedere l'argomento relativo al test dell'integrazione di Engagement in Android.


[Send-to-Sync]: http://developer.android.com/google/gcm/adv.html#collapsible
[<http://developer.android.com/guide/google/gcm/gs.html>]: http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]: https://cloud.google.com/console
[libreria client GCM]: http://developer.android.com/guide/google/gcm/gs.html#libs
[Google Developers Console]: https://cloud.google.com/console


<!--HONumber=54-->