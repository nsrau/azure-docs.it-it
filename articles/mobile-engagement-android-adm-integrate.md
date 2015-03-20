<properties 
	pageTitle="Integrazione di Azure Mobile Engagement con Android SDK" 
	description="Ultimi aggiornamenti e procedure per Android SDK per Azure Mobile Engagement"
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

> [AZURE.IMPORTANT] Prima di usare questa guida, è necessario eseguire la procedura di integrazione illustrata nell'argomento relativo a come integrare Engagement con Android.
>
> Questo documento è utile solo se è stato integrato il modulo di copertura per il supporto di qualsiasi campagna. Per integrare le campagne di copertura nell'applicazione, leggere Come aggiornare il servizio di copertura di Engagement su Android.

##Introduzione

L'integrazione di ADM consente all'applicazione di essere inserita anche quando non è in esecuzione.

Nessun dato della campagna viene effettivamente inviato tramite ADM: è solo un segnale di background che istruisce l'applicazione a recuperare il push di Engagement. Se l'applicazione non è in esecuzione durante la ricezione di un push ADM, attiva una connessione ai server di Engagement per recuperare il push; la connessione di Engagement rimane attiva per circa un minuto nel caso in cui l'utente avvii l'applicazione nella risposta al push.

> [AZURE.IMPORTANT] Solo i dispositivi Kindle Amazon che eseguono Android 4.0.3 o versioni successive sono supportati dalla messaggistica dei dispositivi Amazon. Tuttavia, è possibile integrare questo codice in modo sicuro in altri dispositivi. Se l'applicazione non può essere riattivata da ADM, verrà ricevuta la notifica di Engagement al successivo avvio dell'applicazione.

##Iscriversi ad ADM

Se questa operazione non è già stata eseguita, è necessario abilitare ADM nel proprio account Amazon.

La procedura è descritta in dettaglio in: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Dopo aver completato la procedura, si ottiene quanto segue:

-   Credenziali OAuth (un ID Client e un segreto Client) che consentono a Engagement di eseguire il push dei dispositivi.
-   Una chiave API che deve essere integrata nell'applicazione.

##Integrazione SDK

### Gestione delle registrazioni dei dispositivi

Ogni dispositivo deve inviare un comando di registrazione ai server ADM; in caso contrario non saranno raggiungibili.

Se si usa già la [libreria client ADM] e si è già [integrato ADM] è possibile passare direttamente ad android-sdk-adm-receive.

Se ADM non è stato ancora integrato, Engagement offre un modo più semplice per abilitarlo nell'applicazione:

Modificare il file `AndroidManifest.xml`:

-   Aggiungere lo spazio dei nomi Amazon: il file deve iniziare come il seguente:

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   All'interno del tag `<application/>` aggiungere questa sezione:

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>
		
		<meta-data android:name="engagement:adm:register" android:value="true" />

-   Dopo aver aggiunto il tag di Amazon, potrebbe verificarsi un errore di compilazione se il Project Build Target è sotto Android 2.1. È necessario usare una destinazione di compilazione **Android 2.1+**. È comunque possibile impostare `minSdkVersion` su 4.
-   Integrare la chiave API di ADM come un asset seguendo [questa procedura].

Seguire quindi le istruzioni riportate nelle sezioni successive.

### Comunicare l'ID di registrazione al servizio push di Engagement e ricevere le notifiche

Per comunicare l'ID di registrazione del dispositivo al servizio Push di Engagement e ricevere le notifiche, aggiungere quanto segue al file  `AndroidManifest.xml`, all'interno del tag `<application/>` (anche se si usa ADM senza Engagement):

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

Assicurarsi di avere le seguenti autorizzazioni nel file  `AndroidManifest.xml` (prima del tag `</application>`).

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Concedere le credenziali OAuth di Engagement

Inviare le credenziali OAuth (ID e segreto client) a $/\#application/YOUR\_APPID/native-push.

È ora possibile selezionare "Any Time" durante la creazione di annunci e sondaggi del servizio di copertura.


[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[Libreria client ADM]:https://developer.amazon.com/sdk/adm/setup.html
[ADM integrato]:https://developer.amazon.com/sdk/adm/integrating-app.html
[questa procedura]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset

<!--HONumber=47-->
