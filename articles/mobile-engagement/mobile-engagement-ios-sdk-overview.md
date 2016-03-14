<properties
	pageTitle="Panoramica di Azure Mobile Engagement iOS SDK"
	description="Ultimi aggiornamenti e procedure relativi a iOS SDK per Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="MehrdadMzfr" />

#iOS SDK per Azure Mobile Engagement

Da qui, è possibile visualizzare tutti i dettagli su come integrare Azure Mobile Engagement in un'app per iOS. Se si vuole fare prima una prova, eseguire l'[esercitazione di 15 minuti](mobile-engagement-ios-get-started.md).

Fare clic per visualizzare il [contenuto dell'SDK](mobile-engagement-ios-sdk-content.md).

##Procedure di integrazione
1. Iniziare da qui: [Come integrare Mobile Engagement in un'app per iOS](mobile-engagement-ios-integrate-engagement.md)

2. Per le notifiche: [Come integrare il servizio di copertura (di notifica) in un'app per iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implementazione del piano di tag: [Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app per iOS](mobile-engagement-ios-use-engagement-api.md)


##Note sulla versione

###3\.2.1 (12/11/2015)

-   Correzione del ritardo durante l’attivazione di una nuova istanza dell’app tramite notifica con collegamenti diretti 

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-ios-release-notes.md).

##Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere quindi le [procedure di aggiornamento](mobile-engagement-ios-upgrade-procedure.md) complete.

Per ogni nuova versione dell'SDK è necessario innanzitutto sostituire (rimuovere e importare nuovamente in Xcode) le cartelle EngagementSDK ed EngagementReach.

###Dalla versione 2.0.0 alla 3.0.0
Eliminazione del supporto per iOS 4.X. A partire da questa versione, la destinazione della distribuzione dell'applicazione deve essere almeno iOS 6.

Se si usa Reach nell'applicazione, è necessario aggiungere il valore `remote-notification` alla matrice `UIBackgroundModes` nel file Info.plist per ricevere notifiche remote.

Il metodo `application:didReceiveRemoteNotification:` deve essere sostituito da `application:didReceiveRemoteNotification:fetchCompletionHandler:` nel delegato dell'applicazione.

"AEPushDelegate.h" è un'interfaccia deprecata ed è necessario rimuovere tutti i riferimenti. Ciò include la rimozione di `[[EngagementAgent shared] setPushDelegate:self]` e dei metodi delegati dal delegato dell'applicazione:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

<!---HONumber=AcomDC_0302_2016-->