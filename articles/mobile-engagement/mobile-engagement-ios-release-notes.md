<properties
	pageTitle="Note sulla versione di Azure Mobile Engagement iOS SDK | Microsoft Azure"
	description="Ultimi aggiornamenti e procedure relativi a iOS SDK per Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="piyushjo" />

#Note sulla versione di Azure Mobile Engagement iOS SDK

##3\.2.4 (30/06/2016)

-   Aggregazione fissa tra log tecnici e altri log.

##3\.2.3 (07/06/2016)

-   Correzione del bug che causava la mancata segnalazione del feedback di recapito con l'applicazione in background.
-   Ottimizzazione dell’invio di log tecnici.

##3\.2.2 (07/04/2016)

-   Correzione del bug sull'annullamento della richiesta HTTP che a volte provoca l'arresto anomalo del sistema.

##3\.2.1 (12/11/2015)

-   Correzione del ritardo durante l’attivazione di una nuova istanza dell’app tramite notifica con collegamenti diretti

##3\.2.0 (10/08/2015)

-   Abilitato Bitcode nel SDK per utilizzarlo con **Xcode 7**.
-   Bug corretti relativi a notifiche all'interno dell'applicazione.
-   Apportate le notifiche all'interno dell'app più affidabili in caso di batteria scarica e altri scenari.
-   Rimossi i registri aggiuntivi della console generati dalla libreria di terze parti 3.

##3\.1.0 (08/26/2015)

-   Correzione del bug di compatibilità di iOS 9 con una libreria di terze parti. Provocava arresti anomali durante l'invio del polling dei risultati, di informazioni sull'applicazione o di dati aggiuntivi.

##3\.0.0 (19/06/2015)

-   Mobile Enagement usa le notifiche push Silent.
-   Eliminazione del supporto per iOS 4.X. A partire da questa versione, la destinazione della distribuzione dell'applicazione deve essere almeno iOS 6.

##2\.2.0 (21/05/2015)

-   L'ID del dispositivo di Mobile Engagement per dispositivi < iOS 6 è ora basato su un GUID generato in fase di installazione.

##2\.1.0 (24/04/2015)

-   È stata aggiunta la compatibilità per Swift.
-   Quando si fa clic su una notifica, l'URL dell'azione viene ora eseguito subito dopo l'apertura dell'applicazione.
-   È stato aggiunto il file di intestazione mancante nel pacchetto dell'SDK.
-   È stato risolto un problema relativo alla disabilitazione del reporter di arresto anomalo di Mobile Engagement.

##2\.0.0 (17/02/2015)

-   Versione iniziale di Azure Mobile Engagement
-   La configurazione di appId/sdkKey viene sostituita da una configurazione della stringa di connessione.
-   L'API non invia e non riceve più messaggi XMPP da entità XMPP arbitrarie.
-   L'API non riceve e non invia più messaggi tra i dispositivi.
-   Sono stati introdotti miglioramenti per la sicurezza.
-   È stato eliminato il rilevamento di SmartAd.

<!---HONumber=AcomDC_0706_2016-->