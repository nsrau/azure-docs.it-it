<properties 
	pageTitle="Panoramica di Azure Mobile Engagement SDK per iOS" 
	description="Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per iOS"
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
	ms.date="05/04/2015" 
	ms.author="piyushjo" />

#Azure Mobile Engagement SDK per iOS

Da qui, è possibile visualizzare tutti i dettagli su come integrare Azure Mobile Engagement in un'app per iOS. Se si vuole fare prima una prova, eseguire l'[esercitazione di 15 minuti](mobile-engagement-ios-get-started.md).

Fare clic per visualizzare il [contenuto dell'SDK](mobile-engagement-ios-sdk-content.md).

##Procedure di integrazione
1. Iniziare da qui: [Come integrare Mobile Engagement in un'app per iOS](mobile-engagement-ios-integrate-engagement.md)

2. Per le notifiche: [Come integrare il servizio Reach (di notifica) in un'app per iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implementazione del piano di tag: [Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app per iOS](mobile-engagement-ios-use-engagement-api.md)


##Note sulla versione

###2.1.0 (24/04/2015)

-   È stata aggiunta la compatibilità per Swift.
-   Quando si fa clic su una notifica, l'URL dell'azione viene ora eseguito subito dopo l'apertura dell'applicazione.
-   È stato aggiunto il file di intestazione mancante nel pacchetto dell'SDK.
-   È stato risolto un problema relativo alla disabilitazione del reporter di arresto anomalo di Mobile Engagement.

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-ios-release-notes.md).

##Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere quindi le [procedure di aggiornamento](mobile-engagement-ios-upgrade-procedure.md) complete.

Per ogni nuova versione dell'SDK è necessario innanzitutto sostituire (rimuovere e importare nuovamente in Xcode) le cartelle EngagementSDK ed EngagementReach.

###Dalla versione 2.0.0 alla 2.1.0
Nessuna.

<!--HONumber=54--> 