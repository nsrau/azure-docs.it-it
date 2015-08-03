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


#Come integrare ADM con Mobile Engagement

> [AZURE.IMPORTANT]Prima di usare questa guida, è necessario eseguire la procedura di integrazione descritta nel documento relativo all'integrazione di Engagement in Android.
>
> Questo documento è utile solo se è stato integrato il modulo Reach per il supporto continuo delle campagne. Per integrare le campagne Reach nell'applicazione, leggere prima l'articolo relativo all'integrazione del servizio Reach di Engagement in Android.

##Introduzione

L'integrazione di ADM consente il push dell'applicazione anche quando questa non è in esecuzione.

Nessun dato della campagna viene effettivamente inviato tramite ADM: è solo un segnale in background che indica all'applicazione di recuperare il push di Engagement. Se l'applicazione non è in esecuzione durante la ricezione di un push ADM, attiva una connessione ai server di Engagement per recuperare il push; la connessione di Engagement rimane attiva per circa un minuto nel caso in cui l'utente avvii l'applicazione nella risposta al push.

> [AZURE.IMPORTANT]Solo i dispositivi Amazon Kindle che eseguono Android 4.0.3 o versioni successive sono supportati da Amazon Device Messaging. È comunque possibile integrare questo codice in modo sicuro in altri dispositivi. Se l'applicazione non può essere riattivata da ADM, verrà ricevuta la notifica di Engagement al successivo avvio dell'applicazione.

##Iscriversi ad ADM

Se questa operazione non è già stata eseguita, è necessario abilitare ADM nel proprio account Amazon.

La procedura è descritta in dettaglio all'indirizzo: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Dopo aver completato la procedura, si ottiene quanto segue:

-   Le credenziali OAuth (ID e segreto client) che consentono a Engagement di eseguire il push dei dispositivi.
-   Una chiave API che deve essere integrata nell'applicazione.

##Integrazione dell'SDK

### Gestione delle registrazioni dei dispositivi

Ogni dispositivo deve inviare un comando di registrazione ai server ADM; in caso contrario non saranno raggiungibili.

Se si usa già la [libreria client ADM] e si è già [integrato ADM], è possibile passare direttamente ad android-sdk-adm-receive.

Se ADM non è stato ancora integrato, Engagement offre un modo più semplice per abilitarlo nell'applicazione:

Modificare il file `AndroidManifest.xml`:

-   Aggiungere lo spazio dei nomi Amazon. Il file deve iniziare nel modo seguente:

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   All'interno del tag `<application/>` aggiungere questa sezione:

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>
		
		<meta-data android:name="engagement:adm:register" android:value="true" />

-   Dopo aver aggiunto il tag di Amazon, può verificarsi un errore di compilazione se come destinazione di compilazione del progetto è impostata una versione precedente Android 2.1. È necessario usare una destinazione di compilazione **Android 2.1** o versione successiva, anche se è ancora possibile avere una proprietà `minSdkVersion` impostata su 4.
-   Integrare la chiave API di ADM come asset seguendo [questa procedura].

Seguire quindi le istruzioni riportate nelle sezioni successive.

### Comunicare l'ID di registrazione al servizio push di Engagement e ricevere le notifiche

Per comunicare l'ID di registrazione del dispositivo al servizio push di Engagement e ricevere le notifiche, aggiungere quanto segue al file `AndroidManifest.xml`, all'interno del tag `<application/>` (anche se si usa ADM senza Engagement):

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>
		
		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

Assicurarsi di avere le seguenti autorizzazioni nel file `AndroidManifest.xml` (prima del tag `</application>`).

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Concedere le credenziali OAuth di Engagement

Inviare le credenziali OAuth (ID e segreto client) a $/#application/YOUR_APPID/native-push.

È ora possibile selezionare "In qualsiasi momento" durante la creazione di annunci e sondaggi del servizio Reach.


[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[libreria client ADM]: https://developer.amazon.com/sdk/adm/setup.html
[integrato ADM]: https://developer.amazon.com/sdk/adm/integrating-app.html
[questa procedura]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
 

<!---HONumber=July15_HO4-->