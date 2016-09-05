<properties
	pageTitle="Integrazione di Android SDK per Azure Mobile Engagement"
	description="Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per Android"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="piyushjo" />  

#Come integrare GCM con Mobile Engagement

> [AZURE.IMPORTANT] Prima di usare questa guida, è necessario eseguire la procedura di integrazione descritta nel documento relativo all'integrazione di Engagement in Android.
>
> Il documento è utile solo se è già stato integrato il modulo di copertura e si intende eseguire il push dei dispositivi Google Play. Per integrare le campagne Reach nell'applicazione, leggere prima l'articolo relativo all'integrazione del servizio Reach di Engagement in Android.

##Introduzione

L'integrazione di GCM consente il push dell'applicazione.

I payload GCM di cui viene eseguito il push sull’SDK contengono sempre la chiave `azme` nell'oggetto dati. Pertanto se si usa GCM per uno scopo diverso nell'applicazione, è possibile filtrare le notifiche push in base a tale chiave.

> [AZURE.IMPORTANT] Solo i dispositivi che eseguono Android 2.2 o versioni successive, con Google Play installato e con la connessione a Google in background abilitata, possono essere riattivati da GCM. È comunque possibile integrare questo codice in modo sicuro in dispositivi non supportati (usa solo elementi di tipo Intent).

##Creare un progetto Google Cloud Messaging con chiave API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

> [AZURE.IMPORTANT] Il **numero di progetto** non deve essere confuso con l'**ID progetto**.

##Integrazione dell'SDK

### Gestione delle registrazioni dei dispositivi

Ogni dispositivo deve inviare un comando di registrazione ai server Google; in caso contrario non sarà raggiungibile.

Un dispositivo può anche annullare la registrazione alle notifiche GCM (la registrazione del dispositivo viene annullata automaticamente se l'applicazione viene disinstallata).

Se non si usa [Google Play SDK] oppure se l'Intent di registrazione non viene inviato manualmente, è possibile fare in modo che Engagement registri automaticamente il dispositivo.

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

##Concedere a Mobile Engagement l'accesso alla chiave API GCM

Seguire [questa guida](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) per concedere a Mobile Engagement l'accesso alla chiave API GCM.

[Google Play SDK]: https://developers.google.com/cloud-messaging/android/start

<!---HONumber=AcomDC_0824_2016-->