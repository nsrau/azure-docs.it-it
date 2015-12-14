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
	ms.date="08/10/2015"
	ms.author="piyushjo" />


#Android SDK per Azure Mobile Engagement

In questo articolo sono disponibili informazioni dettagliate per integrare Azure Mobile Engagement in un'app per Android. Se si vuole fare prima una prova, eseguire l'[esercitazione di 15 minuti](mobile-engagement-android-get-started.md).

Fare clic per visualizzare il [contenuto dell'SDK](mobile-engagement-android-sdk-content.md).

##Procedure di integrazione
1. Iniziare da qui: [Come integrare Mobile Engagement in un'app per Android](mobile-engagement-android-integrate-engagement.md)

2. Per le notifiche: [Come integrare il servizio di copertura (di notifica) in un'app per Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [Come integrare GCM con Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Come integrare ADM con Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Implementazione del piano di tag: [Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app per Android](mobile-engagement-android-use-engagement-api.md)


##Note sulla versione

##4\.1.2 (11/25/2015)

- Miglioramenti della stabilità.

##4\.1.1 (11/04/2015)

- Miglioramenti della stabilità.

##4\.1.0 (08/25/2015)

- Gestione di un nuovo modello di autorizzazione per Android M.
- Ora è possibile configurare le funzionalità di indirizzo in fase di esecuzione anziché utilizzare `AndroidManifest.xml`.
- Correzione di un bug delle autorizzazioni: se si utilizza `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` non è più necessario.
- Miglioramenti della stabilità.

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-android-release-notes.md).

##Procedure di aggiornamento

Se è già stata integrata una versione precedente di Windows SDK nell'applicazione, consultare [Procedure di aggiornamento](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_1203_2015-->